---
Week: "4" 
Lesson: "Submit a job using Slurm"
Date: "Thursday, February 9, 2023"
---

# Learning Objectives:

* Understand the type of data that is accessible from Gene Expression Omnibus (GEO)
* Use the command-line interface to copy over data from GEO

# Submit a job on Bluemoon

Submitting a job to an HPC machine (such as Bluemoon or DeepGreen) is done using the batch system. The batch system allows users to submit jobs requesting the resources (nodes, processors, memory, GPUs) that they need. The jobs are queued and then run as resources become available.

## Batch Computing 
To run a batch job, you put the commands into a text file instead of typing them at the prompt. You submit this file to the batch system, called **SLURM**, which will run it as soon as resources become available. The output you would normally see on your display goes into a log file. You can check the status of your job interactively and/or receive emails when it begins and ends execution. The basic steps include: 

1. Log in to VACC
2. Write job script 
3. Submit Job 
4. Monitor job and wait for it to run! 
5. Retrieve your output 

We have completed step #1. Now we will proceed to writing a job script. This job script can be created using any text editor - such as Nano or Vim - or any GUI editor you may want to download! 
To use the Slurm job scheduler, it requires Slurm directives. To learn more you can do: 

```
man sbatch
```

## Slurm Directives 
At the top of the job script will always be several lines that start with #SBATCH. The Slurm directives provide the job setup information used by Slurm, including resources to request. This information is then followed by the commands to be executed in the script. 

### Partition
First, we will specify a partition. A partition refers to a group of nodes which are characterized by their hardware. Specifying a partition is optional and if not specified the default partition is bluemoon. As practice we will specify bluemoon anyways using the following line: 

```
#SBATCH --partition=bluemoon
```
| Partition|  Intended Use  | Max Runtime |  
|:-----------:|:----------:|:----------:| 
|bluemoon | General computing – default partition |30 hours| 
|short | General computing with short runtime | 3 hours| 
|week | General computing with longer runtime | 7 days | 
|bigmem| Large memory requirements computing| 30 hours| 
|bigmemwk | Large memory requirements with longer runtime | 7 days | 

### Walltime 
Walltime is the maximum amount of time your job will run. It’s the runtime of your job.

Your job may run for less time than you request (and you will only be charged for that amount of time), but it will not run for more time than you request.

Walltime is requested with #SBATCH --time=<dd-hh:mm:ss>, where “dd” refers to day(s), “hh” to hour(s), “mm” to minute(s), and “ss” to second(s). You will replace each of these units with a two-digit numeral. Acceptable formats are: mm, mm:ss, hh:mm:ss, dd-hh, dd-hh:mm, dd-hh:mm:ss.

```
# requesting 30 hours of walltime (hh:mm:ss)
#SBATCH --time=30:00:00
```