
1. Please **study the contents** and **work through all the code** within the following lessons:

 * [Experimental design considerations](../lessons/02_experimental_planning_considerations.md)
 * [Quality Control of Sequence Data: Running FASTQC on multiple samples](../lessons/06_qc_running_fastqc_sbatch.md)
 * [Quality Control of Sequence Data: Evaluating FASTQC reports](../lessons/07_qc_fastqc_assessment.md)

    > **NOTE:** To run through the code above, you will need to be **logged into O2** and **working on a compute node** (i.e. your command prompt should have the word `compute` in it).
    > 1. Log in using `ssh rc_trainingXX@o2.hms.harvard.edu` and enter your password (replace the "XX" in the username with the number you were [assigned in class](https://docs.google.com/spreadsheets/d/1kBlYowhjjHJC9ZovmbBULmbqozKpprM17vZ2wPlhNg0/edit#gid=0)). 
    > 2. Once you are on the login node, use `srun --pty -p interactive -t 0-2:30 --mem 1G /bin/bash` to get on a compute node or as specified in the lesson.
    > 3. Proceed only once your command prompt has the word `compute` in it.
    > 4. If you log out between lessons (using the `exit` command twice), please follow points 1. and 2. above to log back in and get on a compute node when you restart with the self learning.

2. **Complete the exercises**:
   * Each lesson above contain exercises; please go through each of them.
   * **Copy over** your code from the exercises into a text file. 
   * **Upload the saved text file** to [Dropbox](https://www.dropbox.com/request/RLnrDop8oJxUUP5UKT7K) the **day before the next class**.
   
### Questions?
* ***If you get stuck due to an error*** while runnning code in the lesson, [email us](mailto:hbctraining@hsph.harvard.edu) 
* Post any **conceptual questions** that you would like to have **reviewed in class** [here](https://PollEv.com/hbctraining945).

***

## Day 2
Data Management
[Best Practices in Research Data Management (RDM)](../lessons/04a_data_organization.md)
[Project Organization (using Data Management best practices)](../lessons/04b_data_organization.md)
| Time |  Topic  | Instructor |
|:-----------:|:----------:|:--------:|
| 09:30 - 10:30 | Self-learning lessons review | All |
| 10:30 - 11:10 | [Expression quantification: Theory and Tools](../lectures/expression_quantification.pdf) | Radhika |
| 11:10 - 11:50 | [Quantifying expression using alignment-free methods (Salmon)](../lessons/08_quasi_alignment_salmon.md) | Jihe |
| 11:50 - 12:00 | [Review of workflow](../lectures/workflow_overview.pdf) | Radhika |

### Before the next class:

1. Please **study the contents** and **work through all the code** within the following lessons:

 * [Quantifying expression using alignment-free methods (Salmon on multiple samples)](../lessons/09_quasi_alignment_salmon_sbatch.md)
      <details>
       <summary><i>Click here for a preview of this lesson</i></summary>
         <br>Now that we know how to run the quantification of one sample with Salmon, this lesson will guide you to run multiple samples by creating a job submission script<br><br>
       </details>
 * [QC with Alignment Data](../lessons/10_QC_Qualimap.md)
      <details>
       <summary><i>Click here for a preview of this lesson</i></summary>
         <br>Besides transcript-level quantification, we also want to understand the quality of the mapping, which is not provided in Salmon output. <br><br>This lesson will cover:<br>
             - Aligning the reads with an aligner, STAR<br>
             - Assessing QC metrics among samples<br><br>
       </details>
 * [Documenting Steps in the Workflow with MultiQC](../lessons/11_multiQC.md)
      <details>
       <summary><i>Click here for a preview of this lesson</i></summary>
         <br>It would be great to have a summary document of all QC results from the previous analysis. <br><br>This lesson will cover:<br>
             - Generating such a summary report with multiQC<br>
             - Generating alignment metric with Qualimap<br><br>
       </details>

     > **NOTE:** To run through the code above, you will need to be **logged into O2** and **working on a compute node** (i.e. your command prompt should have the word `compute` in it).
     > 1. Log in using `ssh rc_trainingXX@o2.hms.harvard.edu` and enter your password (replace the "XX" in the username with the number you were assigned in class). 
     > 2. Once you are on the login node, use `srun --pty -p interactive -t 0-2:30 --mem 8G /bin/bash` to get on a compute node or as specified in the lesson.
     > 3. Proceed only once your command prompt has the word `compute` in it.
     > 4. If you log out between lessons (using the `exit` command twice), please follow points 1. and 2. above to log back in and get on a compute node when you restart with the self learning.

2. **Complete the exercises**:
   * Each lesson above contain exercises; please go through each of them.
   * **Copy over** your code from the exercises into a text file. 
   * **Upload the saved text file** to [Dropbox](https://www.dropbox.com/request/cJ3kkGLqkI6sTUrpgIvN) the **day before the next class**.
   
### Questions?
* ***If you get stuck due to an error*** while runnning code in the lesson, [email us](mailto:hbctraining@hsph.harvard.edu) 
* Post any **conceptual questions** that you would like to have **reviewed in class** [here](https://PollEv.com/hbctraining945).

***

## Day 3

| Time |  Topic  | Instructor |
|:-----------:|:----------:|:--------:|
| 09:30 - 10:10 | Self-learning lessons review | All |
| 10:10 - 11:10 | [Automating the RNA-seq workflow](../lessons/12_automating_workflow.md) | Radhika |
| 11:10 - 11:45 | [Troubleshooting RNA-seq Data Analysis](../lectures/RNA-seq_troubleshooting.pdf)| Meeta |
| 11:45 - 12:00 | [Wrap up](../lectures/workshop_wrapup.pdf) | Will |

***

* Downloadable Answer Keys (Day 2 exercises): 
  * [Experimental design (one possible solution)](https://www.dropbox.com/s/524mevuyba34l5b/exp_design_table.xlsx?dl=1)
  * [sbatch script](https://www.dropbox.com/s/9wdyhfqpic05l6p/mov10_fastqc.run?dl=1)
  * [.out file](https://www.dropbox.com/s/l7puf8oahtbwmpk/22914006.out?dl=1)
  * [.err file](https://www.dropbox.com/s/8a1g6o9t2kxit30/22914006.err?dl=1).

* Downloadable Answer Keys (Day 3 exercises): 
  * [sbatch script to run salmon for all samples](../answer_key/salmon_all_samples.sbatch)

* [Automation Script](../scripts/rnaseq_analysis_on_input_file.sh)