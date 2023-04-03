---
Week: "12" 
Lesson: "ssvQC"
Date: "Tuesday, April 4, 2023"
---

## Getting Started 

Copy this folder into your home directory:

```bash 
/gpfs1/cl/mmg232/course_materials/R_tutorials/ssvQC
```

Installing ssvQC can be difficult if the user is not using the most-up-to-date BiocManager, devtools, R, R/Studio. Below are the versions tested for this tutorial:

+ BiocManager 1.30.20
+ devtools 2.4.5
+ R version 4.2.2 (2022-10-31) – “Innocent and Trusting”
+ RStudio 2022.12.0+353 “Elsbeth Geranium” Release (7d165dcfc1b6d300eb247738db2c7076234f6ef0, 2022-12-03) for Ubuntu Jammy Mozilla/5.0 (Macintosh; Intel Mac OS X 10.13; rv:109.0) Gecko/20100101 Firefox/111.0

To install: 

```
BiocManager::install("seqsetvis") #this takes a few minutes to compile 
#Update all/some/none? [a/s/n]: a
```

```
devtools::install_github("FrietzeLabUVM/ssvQC")
```

***

### Citation 

Boyd J, Rodriguez P, Schjerven H, Frietze S. ssvQC: an integrated CUT&RUN quality control workflow for histone modifications and transcription factors. BMC Res Notes. 2021 Sep 20;14(1):366. doi: 10.1186/s13104-021-05781-8. PMID: 34544495; PMCID: PMC8454122.
