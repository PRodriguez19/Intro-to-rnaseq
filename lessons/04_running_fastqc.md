---
Week: "5" 
Lesson: "QC with FASTQC"
Date: "Thrusday, February 16th, 2023"
---

# Learning Objectives:

* Describe the contents and format of a FASTQ file
* Create a quality report using FASTQC
* Understand and use the environmental module system provided by the VACC 
* Create and run a SLURM job submission script to automate quality assessment
 
# Quality Control of FASTQ files


The first step in the RNA-Seq workflow is to take the FASTQ files received from the sequencing facility and assess the quality of the sequence reads. 

<p align="center">
<img src="../img/rnaseq_salmon_workflow.png" width="400">
</p>

## Unmapped read data (FASTQ)

The [FASTQ](https://en.wikipedia.org/wiki/FASTQ_format) file format is the defacto file format for sequence reads generated from next-generation sequencing technologies. This file format evolved from FASTA in that it contains sequence data, but also contains quality information. Similar to FASTA, the FASTQ file begins with a header line. The difference is that the FASTQ header is denoted by a `@` character. For a single record (sequence read), there are four lines, each of which are described below:

|Line|Description|
|----|-----------|
|1|Always begins with '@', followed by information about the read|
|2|The actual DNA sequence|
|3|Always begins with a '+', and sometimes the same info as in line 1|
|4|Has a string of characters representing the quality scores; must have same number of characters as line 2|

Let's use the following read as an example:

```
@HWI-ST330:304:H045HADXX:1:1101:1111:61397
CACTTGTAAGGGCAGGCCCCCTTCACCCTCCCGCTCCTGGGGGANNNNNNNNNNANNNCGAGGCCCTGGGGTAGAGGGNNNNNNNNNNNNNNGATCTTGG
+
@?@DDDDDDHHH?GH:?FCBGGB@C?DBEGIIIIAEF;FCGGI#########################################################
```

The line 4 has characters encoding the quality of each nucleotide in the read. The legend below provides the mapping of quality scores (Phred-33) to the quality encoding characters. *Different quality encoding scales exist (differing by offset in the ASCII table), but note the most commonly used one is fastqsanger, which is the scale output by Illumina since mid-2011.* 
 ```
 Quality encoding: !"#$%&'()*+,-./0123456789:;<=>?@ABCDEFGHI
                   |         |         |         |         |
    Quality score: 0........10........20........30........40                                
```
 
Using the quality encoding character legend, the first nucelotide in the read (C) is called with a quality score of 31 (corresponding to encoding character `@`), and our Ns are called with a score of 2 (corresponding to encoding character `#`). **As you can tell by now, this is a bad read.** 

Each quality score represents the probability that the corresponding nucleotide call is incorrect. This quality score is logarithmically based and is calculated as:

	Q = -10 x log10(P), where P is the probability that a base call is erroneous

These probabaility values are the results from the base calling algorithm and dependent on how much signal was captured for the base incorporation. The score values can be interpreted as follows:

|Phred Quality Score |Probability of incorrect base call |Base call accuracy|
|:-------------------:|:---------------------------------:|:-----------------:|
|10	|1 in 10 |	90%|
|20	|1 in 100|	99%|
|30	|1 in 1000|	99.9%|
|40	|1 in 10,000|	99.99%|

Therefore, for the first nucleotide in the read (C), there is less than a 1 in 1000 chance that the base was called incorrectly. Whereas, for the the end of the read there is greater than 50% probabaility that the base is called incorrectly.

## Assessing quality with FastQC

Now that we understand what information is stored in a FASTQ file, the next step is to examine quality metrics for our data.

[FastQC](http://www.bioinformatics.babraham.ac.uk/projects/fastqc/) provides a simple way to do some quality checks on raw sequence data coming from high throughput sequencing pipelines. It provides a modular set of analyses, which you can use to obtain an impression of whether your data has any problems that you should be aware of before moving on to the next analysis.

FastQC does the following:
* accepts FASTQ files (or BAM files) as input
* generates summary graphs and tables to help assess your data
* generates an easy-to-view HTML-based report with the graphs and tables

# Run FastQC  

## Environmental Module System 
We would like to run the FastQC tool on four files. However, if we were to run the following `fastqc` command now we would retrieve the following error:

```
fastqc --help

-bash: fastqc: command not found
```

This is due to the fact that this program is not available in your current environment. However, a great work-around to downloading and configuraing programs is to first check if they are available as library packages through the VACC environmental module system. 

Environmental Modules provide a convenient way for VACC users to load and unload packages. These packages are maintained and updated by the VACC. The following commands are necessary to work with modules: 

| Module commands | description |
|:---------:|:---------:|
| `module avail` | List all available software modules|
| `module load` | Loads the named software module|
| `module list` | Lists all the currently loaded modules | 
| `module unload` | Unload a specific module |
| `module purge` | Unload all loaded modules |
| `module help` | Displays general help/information about modules |

Change directories to `raw_data`.

```bash
cd raw_data
```  

Before we start using software, we have to load the module for each tool. 

If we check which modules we currently have loaded, we should not see FastQC.

```bash
module list
```

Let's load fastqc 

```bash
module load fastqc-0.11.7-gcc-7.3.0-vcaesw7
```

Once a module for a tool is loaded, you have essentially made it directly available to you like any other basic shell command.

```bash
module list
```

Now, let's create a directory to store the output of FastQC:

```bash
mkdir fastqc
```

We will need to specify this directory in the command to run FastQC. How do we know which argument to use?

```bash
fastqc --help
```

> **NOTE:** From the help manual, we know that `-o` (or `--outdir`) will create all output files in the specified output directory.

FastQC will accept multiple file names as input, so we can use the `*.fq` wildcard.

```bash
$ fastqc -o fastqc/ *.fq
```


### Performing quality assessment using job submission scripts
So far in our FASTQC analysis, we have been directly submitting commands to O2 using an interactive session (ie. `srun --pty -c 6 -p interactive -t 0-12:00 --mem 6G --reservation=HBC /bin/bash`). However, there are many [more partitions available on O2](https://wiki.rc.hms.harvard.edu/display/O2/Using+Slurm+Basic#UsingSlurmBasic-Partitions(akaQueues)) than just the interactive partition. We can submit a command or series of commands to these partitions using job submission scripts. 

**Job submission scripts** for O2 are just regular shell scripts, but contain the Slurm **options/directives** for our job submission. These directives define the various resources we are requesting for our job (i.e *number of cores, name of partition, runtime limit* )

Submission of the script using the `sbatch` command allows Slurm to run your job when its your turn. Let's create a job submission script to automate what we have done in [previous lesson](05_qc_running_fastqc_interactively.md).

Our script will do the following:

1. Change directories to where the FASTQ files are located
2. Load the FastQC module
3. Run FastQC on all of our FASTQ files

Let's first change the directory to `~/rnaseq/scripts`, and create a script named `mov10_fastqc.run` using `vim`.

```bash
$ cd ~/rnaseq/scripts

$ vim mov10_fastqc.run
```

Once in the vim editor, click `i` to enter INSERT mode. The first thing we need in our script is the **shebang line**:

```bash
#!/bin/bash
```

Following the shebang line are the Slurm directives. For the script to run, we need to include options for **queue/partition (-p) and runtime limit (-t)**. To specify our options, we precede the option with `#SBATCH`. Some key resources to specify are:

|Resource|Flag|Description|
|:----:|:----:|:----:|
|partition|-p|partition name|
|time|-t|hours:minutes run limit, after which the job will be killed|
|core|-c|number of cores requested -- this needs to be greater than or equal to the number of cores you plan to use to run your job|
|memory|--mem|memory limit per compute node for the job|

Let's specify those options as follows:

```bash
#SBATCH -p short 		# partition name
#SBATCH -t 0-2:00 		# time limit
#SBATCH -c 6 		# number of cores
#SBATCH --mem 6G   # requested memory
#SBATCH --job-name rnaseq_mov10_fastqc 		# Job name
#SBATCH -o %j.out			# File to which standard output will be written
#SBATCH -e %j.err 		# File to which standard error will be written
```

Now in the body of the script, we can include any commands we want to run. In this case, it will be the following:

```bash
## Change directories to where the fastq files are located
cd ~/rnaseq/raw_data

## Load modules required for script commands
module load fastqc/0.11.3

## Run FASTQC
fastqc -o ~/rnaseq/results/fastqc/ -t 6 *.fq
```

> **NOTE:** These are the same commands we used when running FASTQC in the interactive session. Since we are writing them in a script, the `tab` completion function will **not work**, so please make sure you don't have any typos when writing the script!

Once done with your script, click `esc` to exit the INSERT mode. Then save and quit the script by typing `:wq`. You may double check your script by typing `less mov10_fastqc.run`. If everything looks good submit the job!

```bash
$ sbatch mov10_fastqc.run
```

You should immediately see a prompt saying `Submitted batch job JobID`. Your job is assigned with that unique identifier `JobID`. You can check on the status of your job with:

```bash
$ O2sacct
```

Look for the row that corresponds to your `JobID`. The third column indicates the state of your job. Possible states include `PENDING`, `RUNNING`, `COMPLETED`. Once your job state is `RUNNING`, you should expect it to finish in less than two minutes. When the state is `COMPLETED`, that means your job is finished.

> **NOTE:** Other helpful options for checking/managing jobs are available as a [cheatsheet](https://wiki.rc.hms.harvard.edu/display/O2/O2+Command+CheatSheet) from HMS-RC.

Check out the output files in your directory:
```bash
$ ls -lh ../results/fastqc/
```
There should also be one standard error (`.err`) and one standard out (`.out`) files from the job listed in `~/rnaseq/scripts`. You can move these over to your `logs` directory and give them more intuitive names:

```bash
$ mv *.err ../logs/fastqc.err
$ mv *.out ../logs/fastqc.out
```
> **NOTE:** The `.err` and `.out` files store log information during the script running. They are helpful resources, especially when your script does not run as expected and you need to troubleshoot the script.

***
**Exercise**
1. Take a look at what's inside the `.err` and `.out` files. What do you observe? Do you remember where you see those information when using the interactive session?

---
*This lesson has been developed by members of the teaching team at the [Harvard Chan Bioinformatics Core (HBC)](http://bioinformatics.sph.harvard.edu/). These are open access materials distributed under the terms of the [Creative Commons Attribution license](https://creativecommons.org/licenses/by/4.0/) (CC BY 4.0), which permits unrestricted use, distribution, and reproduction in any medium, provided the original author and source are credited.*

* *The materials used in this lesson was derived from work that is Copyright © Data Carpentry (http://datacarpentry.org/). 
All Data Carpentry instructional material is made available under the [Creative Commons Attribution license](https://creativecommons.org/licenses/by/4.0/) (CC BY 4.0).*
