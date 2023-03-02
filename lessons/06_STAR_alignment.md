---
Week: "6" 
Lesson: "Mapping with STAR"
Date: "Thursday, March 2, 2023"
---

# Read Alignment

<img src="../img/RNAseqWorkflow.png" width="400">

Now that we have explored the quality of our raw reads, we can move on to read alignment. We perform read alignment or mapping to determine where in the genome the reads originated from. The alignment process consists of choosing an appropriate reference genome to map our reads against and performing the read alignment using one of several splice-aware alignment tools such as [STAR](http://bioinformatics.oxfordjournals.org/content/early/2012/10/25/bioinformatics.bts635) or [HISAT2](http://ccb.jhu.edu/software/hisat2/index.shtml). The choice of aligner is often a personal preference and also dependent on the computational resources that are available to you.

## STAR Aligner

To determine where on the human genome our reads originated from, we will align our reads to the reference genome using [STAR](http://www.ncbi.nlm.nih.gov/pmc/articles/PMC3530905/) (Spliced Transcripts Alignment to a Reference). STAR is an aligner designed to specifically address many of the challenges of RNA-seq data mapping using a strategy to account for spliced alignments. 

### STAR Alignment Strategy

STAR is shown to have high accuracy and outperforms other aligners by more than a factor of 50 in mapping speed, but it is memory intensive. The algorithm achieves this highly efficient mapping by performing a two-step process:

1. Seed searching
2. Clustering, stitching, and scoring

#### Seed searching

For every read that STAR aligns, STAR will search for the longest sequence that exactly matches one or more locations on the reference genome. These longest matching sequences are called the Maximal Mappable Prefixes (MMPs):


![STAR_step1](../img/alignment_STAR_step1.png)
	
The different parts of the read that are mapped separately are called 'seeds'. So the first MMP that is mapped to the genome is called *seed1*.

STAR will then search again for only the unmapped portion of the read to find the next longest sequence that exactly matches the reference genome, or the next MMP, which will be *seed2*. 

![STAR_step2](../img/alignment_STAR_step2.png)

This sequential searching of only the unmapped portions of reads underlies the efficiency of the STAR algorithm. STAR uses an uncompressed suffix array (SA) to efficiently search for the MMPs, this allows for quick searching against even the largest reference genomes. Other slower aligners use algorithms that often search for the entire read sequence before splitting reads and performing iterative rounds of mapping.

**If STAR does not find an exact matching sequence** for each part of the read due to mismatches or indels, the previous MMPs will be extended.

![STAR_step3](../img/alignment_STAR_step3.png)

**If extension does not give a good alignment**, then the poor quality or adapter sequence (or other contaminating sequence) will be soft clipped.

![STAR_step4](../img/alignment_STAR_step4.png)


#### Clustering, stitching, and scoring

The separate seeds are stitched together to create a complete read by first clustering the seeds together based on proximity to a set of 'anchor' seeds, or seeds that are not multi-mapping.

Then the seeds are stitched together based on the best alignment for the read (scoring based on mismatches, indels, gaps, etc.). 

![STAR_step5](../img/alignment_STAR_step5.png)

## Running STAR

### Set-up

To get started, please download: 

```bash
cp -r /gpfs1/cl/mmg232/course_materials/STAR_alignment . 
```

Aligning reads using STAR is a two step process:   

1. Create a genome index 
2. Map reads to the genome

The indexed genomes for mm10, hg38 for STAR aligner can be found here: 

```
/gpfs1/cl/mmg232/course_materials/genome_index/
```

### Creating a genome index

For today's lesson, we are using reads that originate from a small subsection of chromosome 1 (~300,000 reads) and so we are using only chr1 as the reference genome. 

The basic options to **generate genome indices** using STAR are as follows:

* `--runThreadN`: number of threads
* `--runMode`: genomeGenerate mode
* `--genomeDir`: /path/to/store/genome_indices
* `--genomeFastaFiles`: /path/to/FASTA_file 
* `--sjdbGTFfile`: /path/to/GTF_file
* `--sjdbOverhang`: readlength -1

Now let's create a job submission script to generate the genome index:

```bash
#!/bin/bash
#BATCH --partition=bluemoon
#SBATCH --nodes=1
#SBATCH --ntasks=4
#SBATCH --mem=20G
#SBATCH --time=30:00:00
#SBATCH --job-name=star_index_chr1
# %x=job-name %j=jobid
#SBATCH --output=%x_%j.out

module load star-2.7.9a-gcc-7.3.0-eno6pnp

STAR --runThreadN 4 \
--runMode genomeGenerate \
--genomeDir /gpfs1/cl/mmg232/course_materials/genome_index/star_index_chr1_hg19 \
--genomeFastaFiles /gpfs1/cl/mmg232/course_materials/genome_index/star_index_chr1_hg19/chr1.fa \
--sjdbGTFfile /gpfs1/cl/mmg232/course_materials/genome_index/star_index_chr1_hg19/chr1-hg19_genes.gtf \
--sjdbOverhang 99 \
--outFileNamePrefix chr1_hg19
```

### Aligning reads

After the genome indices are generated, you can perform the read alignment. 

For now, we're going to work on two samples. We will use the first replicate in the Mov10 over-expression group, `Mov10_oe_1.subset.fq` and one replicate of the knock-down, `Irrel_kd_1.subset.fq`. 

More details on STAR and its functionality can be found in the [user manual](https://github.com/alexdobin/STAR/blob/master/doc/STARmanual.pdf); I encourage you to peruse through to get familiar with all available options.

The basic options for aligning reads to the genome using STAR are:

* `--runThreadN`: number of threads / cores
* `--readFilesIn`: /path/to/FASTQ_file
* `--genomeDir`: /path/to/genome_indices_directory
* `--outFileNamePrefix`: prefix for all output files

Listed below are additional parameters that we will use in our command:

* `--outSAMtype`: output filetype (SAM default)
* `--outSAMunmapped`: what to do with unmapped reads

Some honorable mentions:
* `--readFilesCommand zcat`: 
* `--quantMode GeneCounts`:  


```bash
#!/bin/bash
#BATCH --partition=bluemoon
#SBATCH --nodes=1
#SBATCH --ntasks=4
#SBATCH --mem=20G
#SBATCH --time=3:00:00
#SBATCH --job-name=star_align2
# %x=job-name %j=jobid
#SBATCH --output=%x_%j.out

for i in *fq; do
SAMPLE=$(echo ${i} | sed "s/.subset.fq//")
echo ${SAMPLE}.subset.fq

module load star-2.7.9a-gcc-7.3.0-eno6pnp

STAR --genomeDir /gpfs1/cl/mmg232/course_materials/genome_index/star_index_chr1_hg19/ \
--runThreadN 4 \
--readFilesIn ${SAMPLE}.subset.fq \
--outFileNamePrefix ${SAMPLE}_ \
--outSAMtype BAM SortedByCoordinate \
--outSAMunmapped Within \
--outSAMattributes Standard

done

```

---
*This lesson has been developed by members of the teaching team at the [Harvard Chan Bioinformatics Core (HBC)](http://bioinformatics.sph.harvard.edu/). These are open access materials distributed under the terms of the [Creative Commons Attribution license](https://creativecommons.org/licenses/by/4.0/) (CC BY 4.0), which permits unrestricted use, distribution, and reproduction in any medium, provided the original author and source are credited.*
