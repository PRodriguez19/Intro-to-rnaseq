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

The header section is optional and lines in this section start1 with '@` symbol.

The alignment section contains multiple rows, and each row contains information about each individual read and its alignment to the genome. 

 **Each alignment line has 11 mandatory fields** for essential mapping information and a variable number of fields for aligner specific information.

These fields are described briefly below, but for more detailed information the paper by [Heng Li et al](http://bioinformatics.oxfordjournals.org/content/25/16/2078.full) is a good start.

![SAM](../img/sam_bam.png)

![SAM](../img/sam_bam3.png)

SAM Alignment section
QNAME: read name 
FLAG: features about the read alignment, pairs are mapped 
RNAME: Reference sequence name 
POS:
MAPQ: Mapping Quality 
CIGAR: Operators have been used to understand which parts of the reads mapped to the reference 

 **Each alignment line has 11 mandatory fields** for essential mapping information and a variable number of fields for aligner specific information.

These fields are described briefly below, but for more detailed information the paper by [Heng Li et al](http://bioinformatics.oxfordjournals.org/content/25/16/2078.full) is a good start.

![SAM](../img/sam_bam.png)

![SAM](../img/sam_bam3.png)




Let's take a quick look at our alignment. To do so we first convert our BAM file into SAM format using samtools and then pipe it to the `less` command. This allows us to look at the contents without having to write it to file (since we don't need a SAM file for downstream analyses).

```bash
$ samtools view -h results/STAR/Mov10_oe_1_Aligned.sortedByCoord.out.bam | less
```


## BAM file format 
These are not human-readable files. If you opened these files, it would look like this: 




## How to view these files? 

Sequence quality is visualized using FASTQC & MULTIQC 

Check files yourself use IGV! 

