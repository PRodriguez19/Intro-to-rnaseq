---
Week: "6" 
Lesson: "For loops"
Date: "Thursday, February 23, 2023"
---

## Introduction to Loops

While wild cards can be used to perform simple actions on large numbers of files at once, they are limited in utility for more complex commands. In these instances we can turn to loops, a function of programming languages that allow us to write out a command or series of commands and perform them on all the files we want in one time. In addition to being a major time saver, this makes your code a lot easier to read and much more concise overall.

There are many types of loops, but the kind we are going to cover is called a for loop. 

### Variables

The variable portion of a loop is the item that we are going to iterate over and will change with every “new” loop. **This is typically each individual sample or file that the action is performed on.** In their simplest form they will look something like this:

```
var1=/long/path/to/DB
```
> In this case, a location to a specific file is being shown 

As a reminder, if we want to make sure this assignment worked, use the `echo` command. This is a command that prints out whatever is provided to it, which can be really useful to test commands or report information back to yourself during a loop.

```
echo $var1 
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

Now, let's start filling this in for the purpose of submitting files for alignment using HISAT2 and creating BAM files. The SAM files are never a desirable output because of their size

Our script will be written in parts: 

1. 

```bash
for i in reads/*.fastq
do
  SAMPLE=$(echo ${i} | sed "s/.fastq//") 
  echo ${SAMPLE}.fastq
  
## command for alignment (i.e. hisat2 or STAR) 
## command for generation of SAM to BAM (samtools)
  
done
```

Dissecting each line we see that:

+ We’re setting the variable SAMPLE to be the characters that make up each sample name using sed to find and replace for the “word” .fastq with nothing. This let’s us have a variable that is essentially a list of every sample name.
+ echo the names of each sample to make sure it’s correct
+ then perform the command itself.  



What you should see, is that for each FASTQ file you have **5 output files** and a single tmp directory. Briefly, these files are described below:

* `Log.final.out` - a summary of mapping statistics for the sample
* `Aligned.sortedByCoord.out.bam` - the aligned reads, sorted by coordinate, in BAM format
* `Log.out` - a running log from STAR, with information about the run 
* `Log.progress.out` -  job progress with the number of processed reads, % of mapped reads etc., updated every ~1 minute
* `SJ.out.tab` - high confidence collapsed splice junctions in tab-delimited format. Only junctions supported by uniquely mapping reads are reported