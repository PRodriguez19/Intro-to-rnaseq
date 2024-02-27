---
Week: "6" 
Lesson: "Mapping with HISAT2"
Date: "Thursday, February 23, 2023"
---

# Mapping with HISAT2 

## Learning Objectives 
* Use the splice-aware mapper called HISAT2 to align RNA-Seq data 
* Generate the bash script required to align and generate the desired outputs using HISAT2 and SAMtools, respectively 
* Understand the output files 


## Setting up 

To get started with this lesson, we will be aligning (2) FASTQ files using the HISAT2 aligner: 

```
cp -r /gpfs1/cl/mmg232/course_materials/HISAT2_example .
```

## Read Alignment using HISAT2 
The alignment process consists of choosing an appropriate reference genome to map our reads against, and performing the read alignment using one of several splice-aware alignment tools such as [STAR](https://github.com/alexdobin/STAR) or [HISAT2](https://ccb.jhu.edu/software/hisat2/index.shtml) (HISAT2 is a successor to both HISAT and TopHat2). The choice of aligner is a personal preference and also dependent on the computational resources that are available to you.
 
For this first part, we will be using HISAT2 (hierarchical indexing for spliced alignment of transcripts 2), an aligner that is fast and sensitive used for mapping next-generation sequencing reads (whole-genome, whole-exome, transcriptome). 


## Introduction to Loops

Everyone will be downloading a minimum of (6) FASTQ files from the GEO repository. To repeat the alignment and downstream processing steps for each sample precisely, its best to run the same line of code to each sample at the same time. For this we can turn to loops, a function of programming languages that allow us to write out a series of commands and perform them on all the files we want at one time. In addition to being a major time saver, this makes your code a lot easier to read and much more concise overall.

There are many types of loops, but the kind we are going to cover is called a for loop. 

### Variables

The variable portion of a for loop is the item that we are going to iterate over and will change with every “new” loop. **This is typically each individual sample or file that the action is being performed on.** In their simplest form they will look something like this:

```
DIRECTORY=PATH/TO/DIRECTORY

DBDIR=/users/p/d/pdrodrig/genome_index
```
> In this case, this is showing a location to a specific directory 

As a reminder, if we want to make sure this assignment worked, use the `echo` command. This is a command that prints out whatever is provided to it, which can be really useful to test commands or report information back to yourself during a loop.

```
echo $DBDIR 
```

### For loops

For loops are constructed with 4 basic words: 

| Words |  What it does  |  
|:-----------:|:----------|   
|for | set the loop variable name| 
|in | specify whatever it is we are looping over| 
|do | specify what we want to do with each item | 
|done | tell the computer we are done | 

putting these together, for loop syntax is as follows: 

```bash
for VARIABLE in file1 file2 file3
do
  command1 on $VARIABLE
  command2 
done
```

A basic loop looks something like this when it’s written within a job script:

```bash
for i in A B C
do
  echo $i
done
```

> The VARIABLE could be any letter or word that is meaningful, but is commonly represented by a single letter like "i" for ease. 
> Often the `in` portion will be a directory instead of single files (A, B, C) - i.e. a directory that contains the FASTQ files you want to work with. 

Now, let's start filling this in for the purpose of submitting files for alignment using HISAT2 and creating BAM files. **BAM is a binary version of the SAM file, also known as Sequence Alignment Map format.** The SAM file is a tab-delimited text file that contains information for each individual read and its alignment to the genome. However, SAM files are never a desirable output because of their large size therefore, we will be opting to immediately create BAM files using the program SAMtools. We will talk more about these file formats in the next class. 

Our script will be written in sections: 

1. Will provide the job submission parameters

        ```
        #!/bin/bash
        #SBATCH --partition=bluemoon
        #SBATCH --nodes=1
        #SBATCH --ntasks=2
        #SBATCH --mem=40G
        #SBATCH --time=24:00:00
        #SBATCH --job-name=align_CD8
        # %x=job-name %j=jobid
        #SBATCH --output=%x_%j.out
        ```

2. Next, this section will allow us to run the script on all files *while* maintaining the file name for each file output 

        ```bash
        for i in reads/*.fastq
        do
          SAMPLE=$(echo ${i} | sed "s/.fastq//") 
          echo ${SAMPLE}.fastq
        ```

    Dissecting each line we see that:

    + We’re setting the variable SAMPLE to be the characters that make up each sample name using sed to find and replace for the “word” .fastq with nothing. This let’s us have a variable that is essentially a list of every sample name.
    + echo the names of each sample to make sure it’s correct

3. Load the modules required to run the commands 

        ```
        module load hisat2-2.1.0-gcc-7.3.0-knvgwpc
        module load samtools-1.10-gcc-7.3.0-pdbkohx
        ```

4. Followed by the commands to be executed by script. Below the entire script hisat2_align.sh is provided:  

        ```
        #!/bin/bash
        #SBATCH --partition=bluemoon
        #SBATCH --nodes=1
        #SBATCH --ntasks=2
        #SBATCH --mem=10G
        #SBATCH --time=3:00:00
        #SBATCH --job-name=align_CD8
        # %x=job-name %j=jobid
        #SBATCH --output=%x_%j.out

        for i in *fastq.gz; do
        SAMPLE=$(echo ${i} | sed "s/.fastq.gz//")
        echo ${SAMPLE}.fastq.gz 

        DBDIR=/gpfs1/cl/mmg232/course_materials/hisat2_index
        GENOME="GRCm39"
        p=2

        module load hisat2-2.1.0-gcc-7.3.0-knvgwpc
        module load samtools-1.10-gcc-7.3.0-pdbkohx

        #align to GRCm39
        hisat2 \
          -p ${p} \
          -x ${DBDIR}/${GENOME} \
          -U ${SAMPLE}.fastq.gz \
          -S ${SAMPLE}.sam &> ${SAMPLE}.log

        #create bam file
        samtools view ${SAMPLE}.sam \
          --threads 2 \
          -b \
          -o ${SAMPLE}.bam \
  
        #remove sam files once bam file is created
        rm ${SAMPLE}.sam

        #output stats
        samtools flagstat ${SAMPLE}.bam > ${SAMPLE}.txt

        # sort the bam file based on coordinates
        samtools sort ${SAMPLE}.bam -o ${SAMPLE}_sorted.bam

        # index bam file
        samtools index ${SAMPLE}_sorted.bam

        done &> hisat2.log
        ```

  After running the hisat2_align.sh script with the FASTQ provided, the outputs will look like this:

        ```
        align_CD8_7422840.out  SRR13423162.log             SRR13423165.fastq.gz
        hisat2_align.sh        SRR13423162_sorted.bam      SRR13423165.log
        hisat2.log             SRR13423162_sorted.bam.bai  SRR13423165_sorted.bam
        SRR13423162.bam        SRR13423162.txt             SRR13423165_sorted.bam.bai
        SRR13423162.fastq.gz   SRR13423165.bam             SRR13423165.txt
        ```

5. Run MUlTIQC 

Activate conda first

```
conda activate multiqc
```

then run the command
```
multiqc .
```