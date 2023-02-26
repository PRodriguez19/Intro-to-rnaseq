---
Week: "7" 
Lesson: "Learning the SAM/BAM Format"
Date: "Tuesday, February 28, 2023"
---

# Learning the SAM/BAM format

## Introduction 
SAMtools as we seen provides a way for generating and manipulating the SAM/BAM format. Below we will discuss the SAM (Sequence Alignment/Map) and the BAM (binary form of SAM) format. When working with high-throughput sequencing datasets, you will have to deal with SAM/BAM files, so taking the time to familarize yourself can be useful in the long run. 

## Running SAMtools 
Lucky for us SAMtools is offered as a module on the VACC that can be loaded easily using: 

```
module load samtools-1.10-gcc-7.3.0-pdbkohx
```

If you run `samtools` on the terminal with `--help`, you will see the following:

```
Usage:   samtools <command> [options]

Commands:
  -- Indexing
     dict           create a sequence dictionary file
     faidx          index/extract FASTA
     fqidx          index/extract FASTQ
     index          index alignment

  -- Editing
     calmd          recalculate MD/NM tags and '=' bases
     fixmate        fix mate information
     reheader       replace BAM header
     targetcut      cut fosmid regions (for fosmid pool only)
     addreplacerg   adds or replaces RG tags
     markdup        mark duplicates
     ampliconclip   clip oligos from the end of reads

  -- File operations
     collate        shuffle and group alignments by name
     cat            concatenate BAMs
     merge          merge sorted alignments
     mpileup        multi-way pileup
     sort           sort alignment file
     split          splits a file by read group
     quickcheck     quickly check if SAM/BAM/CRAM file appears intact
     fastq          converts a BAM to a FASTQ
     fasta          converts a BAM to a FASTA

  -- Statistics
     bedcov         read depth per BED region
     coverage       alignment depth and percent coverage
     depth          compute the depth
     flagstat       simple stats
     idxstats       BAM index stats
     phase          phase heterozygotes
     stats          generate stats (former bamcheck)
     ampliconstats  generate amplicon specific stats

  -- Viewing
     flags          explain BAM flags
     tview          text alignment viewer
     view           SAM<->BAM<->CRAM conversion
     depad          convert padded BAM to unpadded BAM
```

## SAM file format 
The SAM file is a tab-delimited text file that contains information for each individual read and its alignment to the genome. These are human-readable files that consists of two sections: A *header* section and an *alignment* section. 

The header section is optional and lines in this section start1 with '@` symbol. It also contains information about the sample name, length, and alignment method. 

The alignment section contains multiple rows, and each row contains information about each individual read and its alignment to the genome. 

 **Each alignment line has 11 mandatory fields** for essential mapping information and a variable number of fields for aligner specific information.

These fields are described briefly below, but for more detailed information the paper by [Heng Li et al](http://bioinformatics.oxfordjournals.org/content/25/16/2078.full) is a good start.

![SAM](../img/sam_bam.png)

![SAM](../img/sam_bam3.png)

11 fields of SAM file: 

1. QNAME: read name 
2. FLAG: features about the read alignment and pairmates
3. RNAME: Reference sequence name 
4. POS: 1-based leftmost mapping POSition
5. MAPQ: Mapping Quality 
6. CIGAR: Described by operators used to understand which parts of the reads mapped to the reference 
7. RNEXT: Ref name of the mate/next read
8. PNEXT: Position of the mate/next read
9. TLEN: observed template length
10. SEQ: segment sequence 
11. String: ASCII of Phred-scaled base Quality 


Let's take a look at a SAM file. To do this let's use the BAM file we made last week. We will need to convert the BAM file into SAM format using samtools and then pipe it to the `less -S` command. This will allows us to look at the contents without having to write it to file since we don't need a SAM file for downstream analyses.

```bash
samtools view -h SRR13423162_sorted.bam | less -S
```

## BAM file format 
A BAM file (*.bam) is a SAM file but stored in binary format; you should always convert your SAM files into BAM format since they are smaller in size and are faster to manipulate. These files are not human-readable so they can not be viewed using `less` or `head`. 


## SAMtools commands 

SAMtools provides a variety of commands, each can be invoked using the following format: 

```
samtools command_name

samtools view
```

SAMtools allows you to manipulate *.bam files for a variety of reasons but in order it looks like this: 

1. SAM files are converted into BAM files (samtools view)
2. BAM files are sorted by reference coordinates (samtools sort)
3. Sorted BAM files are indexed (samtools index)

### view

```bash
samtools view -b input.sam > input.bam
```

The `view` command filters SAM or BAM formatted data. Input for this command is usually a SAM file, but BAM file can also be used (as shown in the example above). Uses for `view` include extracting a subset of data into a new file, converting between BAM and SAM formats, and just looking at the raw file!  

### sorting 

When you align FASTQ files with all current sequence aligners, the alignments produced are in random order with respect to their position in the reference genome. In other words, the BAM file is in the order that the sequences occurred in the input FASTQ files. Therefore, the file must be sorted so that the reads occur in "genomic order". That is, the reads are ordered based on their alignment coordinates rather than the order in which they were sequenced. 

```bash
samtools sort sample.bam -o sample.sorted.bam 
```

Many downstream tools require sorted BAM files so this is very "good practice" in terms of moving forward in the bioinformatic pipeline.  

### indexing 

The index command creates a new index file that allows fast look-up of the data in a sorted-BAM or sorted-SAM. This command will create a *.bam.bai file that allows programs that can read this format to work more efficiently, such as Integrative Genome Viewer (IGV). 

```bash
samtools index sorted.bam
``` 
### filtering BAM files


# Citation 

*Work cited from Guri Giaever, "Learning the BAM format", https://bookdown.org/content/24942ad6-9ed7-44e9-b214-1ea8ba9f0224/learning-the-bam-format.html and Aaron Quinlan, "Converting SAM to BAM", http://quinlanlab.org/tutorials/samtools/samtools.html#converting-sam-to-bam-with-samtools-view*
