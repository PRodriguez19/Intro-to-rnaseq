---
Week: "7" 
Lesson: "RSeQC"
Date: "Thursday, March 8, 2023"
---

# Introduction to RSeQC

## Recap from last week 
We performed alignment on fastq files using STAR last week. Let's examine the outputs: 

```
Irrel_kd_1_Aligned.sortedByCoord.out.bam  Mov10_oe_1_Log.out
Irrel_kd_1_Log.out                        Mov10_oe_1_Log.progress.out
Irrel_kd_1_Log.progress.out               Mov10_oe_1__STARtmp
Irrel_kd_1__STARtmp                       Mov10_oe_1.subset.fq
Irrel_kd_1.subset.fq                      star_align2_8032184.out
Mov10_oe_1_Aligned.sortedByCoord.out.bam  star_align.sh
```

STAR produces multiple output files: 
* `sortedByCoord.out.bam`: sorted bam file, similar to samtools sort command 
* `Log.out`: main log file with a lot of detailed information about the run. This file is most useful for troubleshooting and debugging. 
* `Log.progress.out`: reports job progress statistics, such as the number of processed reads, % of mapped reads, etc. 
* `STARtmp`: temp directory 

**When the time comes, Multiqc will use the .out files to provide you with an aggregated report!**

## RSeQC
Now that you have aligned reads in the form of a .bam file, you can proceed to perform additional QC on the BAM files. The major reason we are using this package in class is to utiltize the `infer_experiment.py` module. However, the RSeQC package offers a number of modules including evaluating sequencing saturation, mapped reads distribution, coverage uniformity, strand specificity, etc. You can read more about the package at the [RSeQC website](https://pythonhosted.org/RSeQC/).

Using `infer_experiment.py` will allow us with a way, if need be, to determine "strandedness of reads". Since everyone is reanalyzing published datasets, the authors may have provided this information to you already in the Methods section (or the GEO summary). But this doesn't always happen and its important to know this prior to counting readings with the `HTseq-count` program. 

Files needed to use RSeQC: 
* Aligned bam files 
* Index file for each bam file 
* A transcript bed file (in bed12 format)

```
BED12: A BED file where chrom, start, end, name, score, thickStart, thickEnd, itemRgb, blockCount, blockSizes, and blockStarts are listed in twelve columns.

For example: chr1 11873 14409 uc001aaa.3 0 + 11873 11873 0 3 354,109,1189, 0,739,1347,
```
more information about bed12 format can be found [here](https://bedtools.readthedocs.io/en/latest/content/general-usage.html)

### To install RSeQC: 

```
conda create -n rseqc
```
Then type: 
```
source activate rseqc
```
Now you will see that you have moved from (base) to (rseqc). Now run: 
```
conda install -c bioconda bedops
```
This will take a minute or two, press 'y' when prompted. Then you will know the install is completed when you see 'done'. 

```
Proceed ([y]/n)? y 

Preparing transaction: done                                                     
Verifying transaction: done                                                     
Executing transaction: done
```

Now, you can install RSeQC - this will take 5 -10 minutes. 
```
pip install --user RSeQC
``` 

### Using RSeQC

Download datset for today's lesson: 



**Remember, once you log off you will need to use the command `conda activate rseqc` to use this program in the future!**
