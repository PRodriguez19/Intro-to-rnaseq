---
Week: "6" 
Lesson: "Download fasta & index"
Date: "Tuesday, February 26, 2024"
---

## Download fasta & index

For this tutorial, we will be using an **Organism-specific biological database:** called Cryptodb. These are often updated more frequently then Ensembl, so these tend to be more comprehensive. 

The steps we will perform include: 
1. Make a directory called indexed_genomes_example
2. Grab the fasta file from the Cryptodb website 
3. Load the `hisat2` program using `module load` - We will learn more about the HISAT2 program on Thursday! 
4. Build the genome index 

What is the purpose of building a genome index? 
Prior to mapping, most aligners require you to construct and index the genome, so that the aligner can quickly and efficiently retrieve reference sequence information. 

Indexing in general is widely used in bioinformatics to improve performance. This step can take a long time depending on how large the genome is (i.e. hours) so it is a good idea to generate a script. 

For this example, we will be indexing the genome of the parasite *Cryptosporidium parvum*, which is very small in size in comparison. 

### Step 1: Make a directory called `indexed_genomes_example`

### Step 2: Grab a fasta file from the Cryptodb website and place inside of `indexed_genomes_example`.  

```
https://cryptodb.org/common/downloads/Current_Release/CparvumIOWA-ATCC/fasta/data/
```

+ Which file will you select? Think about the **TYPE** of aligner we are using. 
+ Once you make a decision, right-click, select copy-link 

Use the `wget` command 

```
wget path-you-copied
```

You should see the following: 

```
Connecting to cryptodb.org (cryptodb.org)|128.192.21.13|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 9275167 (8.8M) [application/x-fasta]
Saving to: ‘CryptoDB-67_CparvumIOWA-ATCC_Genome.fasta’

100%[=================================================================>] 9,275,167   5.93MB/s   in 1.5s   

2024-02-26 20:27:32 (5.93 MB/s) - ‘CryptoDB-67_CparvumIOWA-ATCC_Genome.fasta’ saved [9275167/9275167]
```

Check the file with `head`. You should see the following: 

```
>CP044422 | organism=Cryptosporidium_parvum_IOWA-ATCC | version=2022-10-21 | length=920510 | SO=chromosome
AAACCCCTAAACCTAAACCTAAACCTAAACCTAAACCCTAAACCTAAACCTAAACCTAAA
CCTAAAACCTAAACCTAAAACCTAAACCTAAAACCTAAAACCTAAACCTAAACCTAAACC
CCTAAACCTAAACCTAAACCTAAAAACCTAAACCCTAAACCTAAACCTAAAAAACCTAAA
CCTAAACCTAAACCTAAACCTAAACCTAAACCTAAACCTAAAAAACCTAAAACCTAAACC
TAAACCTAAACCTAAACCTAAAACCTAAAAACCTAAACCTAAAAACCTAAAAAACCTAAA
CCTAAAAAACCTAAACCTAAACCTAAAACCTAAAACCTAAACCTAAAACCTAAAACCTAA
ACCTAAACTAAACCTAAAAAACCTAAAAACCTAAACCCCTAAACCTAAACCTAAAACCTA
AACCTAAACCTAAACCTAAACCTAAAACCTAAACCTAAACCTAAAACCTAAACCTAAACC
TAAACCTAAACCTCCTAAAAACCTAAACCTAAACCTAAACCTAAACCTAAAAAACCTAAA
```

### Step 3: Load the hisat2 module

```
module load hisat2-2.1.0-gcc-7.3.0-knvgwpc 
```

Check that the module loaded with `module list`

### Step 4: Build the genome index using the `hisat2-build` argument. The `hisat2-build` can index reference genomes of any size. 

```
hisat2-build
```

Output: 
```
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

The basic usage will be: 

```
hisat2-build fasta-file CparvumIOWA_ATCC_v67
```

### Final Ouput

Small indexes are stored in files with the .ht2 extension. These files together constitute the index: they are all that is needed to align reads to that reference. The original sequence FASTA files are no longer used by HISAT2 once the index is built.

The contents within the directory should look like below: 
```
CparvumIOWA_ATCC_v67.1.ht2  CparvumIOWA_ATCC_v67.6.ht2
CparvumIOWA_ATCC_v67.2.ht2  CparvumIOWA_ATCC_v67.7.ht2
CparvumIOWA_ATCC_v67.3.ht2  CparvumIOWA_ATCC_v67.8.ht2
CparvumIOWA_ATCC_v67.4.ht2  CryptoDB-61_CparvumIOWA-ATCC_Genome.fasta
CparvumIOWA_ATCC_v67.5.ht2
```


