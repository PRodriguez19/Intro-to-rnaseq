---
Week: "6" 
Lesson: "Mapping with HISAT2"
Date: "Thursday, February 29, 2024"
---

# Mapping with HISAT2 

## Learning Objectives 
* Use the splice-aware mapper called HISAT2 to align RNA-Seq data 
* Generate the bash script required to align and generate the desired outputs using HISAT2 and SAMtools, respectively 
* Understand the output files 

***

**Class Exercise #1**  

1. Grab the data folder from this location 

	```
	/gpfs1/cl/mmg3320/course_materials/HISAT2_example 
	```

2. Submit the `hisat2_align.sh` script to SLURM 

3. Check to see that your job is running. If your terminal looks like below...

	```
             JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
          16545144  bluemoon align_CD pdrodrig  R      24:11      1 node329
	```

<p align="center">
<img src="../img/dog_good_job.jpg" width="300">
</p>

***


## Read Alignment using HISAT2 
The alignment process consists of choosing an appropriate reference genome to map our reads against, and performing the read alignment using one of several splice-aware alignment tools such as [STAR](https://github.com/alexdobin/STAR) or [HISAT2](https://ccb.jhu.edu/software/hisat2/index.shtml) (HISAT2 is a successor to both HISAT and TopHat2). The choice of aligner is a personal preference and also dependent on the computational resources that are available to you.
 
Today we will be using HISAT2 (hierarchical indexing for spliced alignment of transcripts 2), an aligner that is fast and sensitive used for mapping next-generation sequencing reads (whole-genome, whole-exome, transcriptome). 

+ [HISAT2 main page](http://daehwankimlab.github.io/hisat2/)
+ [HISAT2 user manual](http://daehwankimlab.github.io/hisat2/manual/)

***
## Class Exercise 2: Refresher on Loops

Everyone will be downloading a minimum of (6) FASTQ files from the GEO repository. To repeat the alignment and downstream processing steps for each sample precisely, its best to run the same line of code to each sample at the same time. For this we can turn to loops, a function of programming languages that allow us to write out a series of commands and perform them on all the files we want at one time. In addition to being a major time saver, this makes your code a lot easier to read and much more concise overall.

There are many types of loops, but the kind we are going to cover is called a for loop. 

### Variables

The variable portion of a for loop is the item that we are going to iterate over and will change with every “new” loop. **The action is typically being performed on each individual file.** In their simplest form variables can look like this:

```
DIRECTORY=PATH/TO/DIRECTORY

DBDIR=/users/p/d/pdrodrig/genome_index
```
> In the above example, this is showing a location to a specific directory 

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

Now, let's start filling this in for the purpose of submitting files for alignment using HISAT2 and creating BAM files. **BAM is a binary version of the SAM file, also known as Sequence Alignment Map format.** The SAM file is a tab-delimited text file that contains information for each individual read and its alignment to the genome. However, SAM files are never a desirable output because of their large size therefore, we will be opting to immediately create BAM files using the program SAMtools.

Our script will be "written" in sections. Please note, 
+ You have already been provided the script so no peeking! 
+ The point of this exercise is the understand how this script is composed. Yes, you are mostly just copying-and-pasting, but do take this time to read the descriptions provided. 
+ Open Jupyter Notebooks for this exercise. Call the script hisat2-practice.sh 

1. Provide the job submission parameters. Type in a job name. 

	```bash
	#!/bin/bash
	#SBATCH --partition=bluemoon
	#SBATCH --nodes=1
	#SBATCH --ntasks=2
	#SBATCH --mem=10G
	#SBATCH --time=3:00:00
	#SBATCH --job-name=
	# %x=job-name %j=jobid
	#SBATCH --output=%x_%j.out
	```

2. Next, copy-and-paste this section below. The importance of this section, is that it will allow you to process all fastq files *while* maintaining the file name for each file output.  

	```bash
	# Iterate through each fastq.gz file in the current directory
	for fastq_file in *fastq.gz; do
    
    # Extract sample name from the file name
    SAMPLE=$(echo ${fastq_file} | sed "s/.fastq.gz//")
    echo ${SAMPLE}.fastq.gz 
	```

    How does it maintain sample naming? 
	+ Remember, the hashtag symbol are used to comment. This is a really important habit to stress. Commenting allows you, the bioinformatician, to understand what you were thinking as you are developing your code.  
    + We’re setting the variable `SAMPLE` as the "sample name" using the `sed` command to find and replace every instance where it says `.fastq.gz` and replace with nothing. 
    	+ This let’s us have a variable that is essentially a list of every sample name.
    	+ Say for example, the sample name is called WT-REP1.fastq.gz. 
    		+ The `sed` command will find the `.fastq.gz` and replace with this with nothing.  
    		+ So upon returning the SAMPLE variable with echo, you will only end up with WT-REP1. 
    + echo the names of each sample to make sure it’s correct

3. Next, copy-and-paste this section below. In your terminal, see if the modules are loaded. If not, load them. 

	```bash
    # Load required modules
    module load hisat2-2.1.0-gcc-7.3.0-knvgwpc
    module load samtools-1.10-gcc-7.3.0-pdbkohx
    ```

4. Next, copy-and-paste this section below.

	```bash
    # Set database directory, genome, and processor count
    DBDIR="/gpfs1/cl/mmg3320/course_materials/genome_index/hisat2_index_mm10"
    GENOME="GRCm39"
    p=2
    ```
	
	> All of these are examples of VARIABLES. These variables are being set to either a path, a character name, or a number.  

5. Next, copy-and-paste this section below.

	```bash
	# Align reads to the reference genome
    hisat2 \
        -p ${p} \
        -x ${DBDIR}/${GENOME} \
        -U ${SAMPLE}.fastq.gz \
        -S ${SAMPLE}.sam &> ${SAMPLE}.log
    ```

6. Move your terminal tab. Do not close your script, we will return to it shortly. 

7. Run the following command on your terminal. 

	```bash
	hisat2 --help
	```
	
	+ What does `-U` mean? 
	+ What does `-S` mean? 
	+ What does `-x` mean? 

	**Its really important at this point, that you take some time to understand what these parameters are!**
	**This isn't a race, take your time.**
	
	**When you are ready, come up and show me the following:** 
	
	a) I would like you to point on your terminal screen and show me what each of the parameters above mean.  
	b) Rewrite the following block of code on your practice script, to accomodate **paired-end** reads. Let's pretend that the file names are WT-REP1-R1.fastq.qz and WT-REP1-R2.fastq.qz.  
	c) MULTIQC output of `hisat2_align.sh`. See #8 and 9 below.   
	d) Open the `SRR13423162.log` file. See #8 below.     
	
	```bash
	# Align reads to the reference genome
    hisat2 \
        -p ${p} \
        -x ${DBDIR}/${GENOME} \
        -U ${SAMPLE}.fastq.gz \
        -S ${SAMPLE}.sam &> ${SAMPLE}.log
    ```

8. In the beginning of class you ran `hisat2_align.sh`. If successful, the outputs will look like this:

        ```
        align_CD8_7422840.out  SRR13423162.log             SRR13423165.fastq.gz
        hisat2_align.sh        SRR13423162_sorted.bam      SRR13423165.log
        hisat2.log             SRR13423162_sorted.bam.bai  SRR13423165_sorted.bam
        SRR13423162.bam        SRR13423162.txt             SRR13423165_sorted.bam.bai
        SRR13423162.fastq.gz   SRR13423165.bam             SRR13423165.txt
        ```

9. Run MUlTIQC 

10. The entire `hisat2_align.sh` script is shown below:

```bash
#!/bin/bash
#SBATCH --partition=bluemoon
#SBATCH --nodes=1
#SBATCH --ntasks=2
#SBATCH --mem=10G
#SBATCH --time=3:00:00
#SBATCH --job-name=align_CD8
# %x=job-name %j=jobid
#SBATCH --output=%x_%j.out

# Iterate through each fastq.gz file in the current directory
for fastq_file in *fastq.gz; do
    
    # Extract sample name from the file name
    SAMPLE=$(echo ${fastq_file} | sed "s/.fastq.gz//")
    echo ${SAMPLE}.fastq.gz 

    # Set database directory, genome, and processor count
    DBDIR="/gpfs1/cl/mmg3320/course_materials/genome_index/hisat2_index_mm10"
    GENOME="GRCm39"
    p=2

    # Load required modules
    module load hisat2-2.1.0-gcc-7.3.0-knvgwpc
    module load samtools-1.10-gcc-7.3.0-pdbkohx

    # Align reads to the reference genome
    hisat2 \
        -p ${p} \
        -x ${DBDIR}/${GENOME} \
        -U ${SAMPLE}.fastq.gz \
        -S ${SAMPLE}.sam &> ${SAMPLE}.log

    # Convert SAM to BAM
    samtools view ${SAMPLE}.sam \
        --threads 2 \
        -b \
        -o ${SAMPLE}.bam

    # Remove SAM file
    rm ${SAMPLE}.sam

    # Generate alignment statistics
    samtools flagstat ${SAMPLE}.bam > ${SAMPLE}.txt

    # Sort the BAM file by coordinates
    samtools sort ${SAMPLE}.bam -o ${SAMPLE}_sorted.bam

    # Index the sorted BAM file
    samtools index ${SAMPLE}_sorted.bam

    echo "Sample ${SAMPLE} processing complete."
done
```

