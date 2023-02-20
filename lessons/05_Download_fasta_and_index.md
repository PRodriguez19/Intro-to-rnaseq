---
Week: "6" 
Lesson: "Download fasta & index"
Date: "Tuesday, February 21, 2023"
---

## Download fasta & index

For this tutorial, we will be using an **Organism-specific biological database:** called Cryptodb. These are often updated more frequently then Ensembl, so these tend to be more comprehensive. 

The steps we will perform include: 
1. Make a directory called indexed_genomes
2. Grab the fasta file from the Cryptodb website 
3. Loading the hisat2 program using `module` 
4. Build the genome index 

What is the purpose of building a genome index? 
Prior to mapping, most aligners require you to construct and index the genome, so that the aligner can quickly and efficiently retrieve reference sequence information. 

Indexing in general is widely used in bioinformatics to improve performance. This step can take a long time depending on how large the genome is (i.e. hours) so it is a good idea to generate a script. 

For this example, we will be indexing the genome ofthe parasite *Cryptosporidium parvum*, which is very small in size in comparison. 

### Step 1: Make a directory called indexed_genomes

### Step 2: Grab the fasta file from the Cryptodb website 

```
wget https://cryptodb.org/common/downloads/Current_Release/CparvumIOWA-ATCC/fasta/data/CryptoDB-61_CparvumIOWA-ATCC_Genome.fasta
```

Check the file with `head`

### Step 3: Load the hisat2 module

```
module load hisat2-2.1.0-gcc-7.3.0-knvgwpc 
```

Check that the module loaded with `module list`

### Step 4: Build the genome index 
The `hisat2-build` can index reference genomes of any size. 

```
hisat2-build

Usage: hisat2-build [options]* <reference_in> <ht2_index_base>
    reference_in            comma-separated list of files with ref sequences
    hisat2_index_base       write ht2 data to files with this dir/basename
Options:
    -c                      reference sequences given on cmd line (as
                            <reference_in>)
    --large-index           force generated index to be 'large', even if ref
                            has fewer than 4 billion nucleotides
    -a/--noauto             disable automatic -p/--bmax/--dcv memory-fitting
    -p                      number of threads
    --bmax <int>            max bucket sz for blockwise suffix-array builder
    --bmaxdivn <int>        max bucket sz as divisor of ref len (default: 4)
    --dcv <int>             diff-cover period for blockwise (default: 1024)
    --nodc                  disable diff-cover (algorithm becomes quadratic)
    -r/--noref              don't build .3/.4.ht2 (packed reference) portion
    -3/--justref            just build .3/.4.ht2 (packed reference) portion
    -o/--offrate <int>      SA is sampled every 2^offRate BWT chars (default: 5)
    -t/--ftabchars <int>    # of chars consumed in initial lookup (default: 10)
    --localoffrate <int>    SA (local) is sampled every 2^offRate BWT chars (default: 3)
    --localftabchars <int>  # of chars consumed in initial lookup in a local index (default: 6)
    --snp <path>            SNP file name
    --haplotype <path>      haplotype file name
    --ss <path>             Splice site file name
    --exon <path>           Exon file name
    --seed <int>            seed for random number generator
    -q/--quiet              verbose output (for debugging)
    -h/--help               print detailed description of tool and its options
    --usage                 print this usage message
    --version               print version information and quit

```

```
hisat2-build CryptoDB-61_CparvumIOWA-ATCC_Genome.fasta CparvumIOWA_ATCC_v61
```

### Ouput

Small indexes are stored in files with the .ht2 extension. These files together constitute the index: they are all that is needed to align reads to that reference. The original sequence FASTA files are no longer used by HISAT2 once the index is built.

The contents within the directory should look like below: 
```
CparvumIOWA_ATCC_v61.1.ht2  CparvumIOWA_ATCC_v61.6.ht2
CparvumIOWA_ATCC_v61.2.ht2  CparvumIOWA_ATCC_v61.7.ht2
CparvumIOWA_ATCC_v61.3.ht2  CparvumIOWA_ATCC_v61.8.ht2
CparvumIOWA_ATCC_v61.4.ht2  CryptoDB-61_CparvumIOWA-ATCC_Genome.fasta
CparvumIOWA_ATCC_v61.5.ht2
```


