---
Week: "5" 
Lesson: "Install Conda"
Date: "Tuesday, February 14th, 2023"
---

# Install Conda

The first thing we need to do is download miniconda:

```bash
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
```

And then run the bash command to install it:

```bash
bash Miniconda3-latest-Linux-x86_64.sh
```

A few interactive things will happen during this portion:

+ You will be asked to review the license agreement, hit return/enter to view it and the space bar to advance. At the end you will need to type + enter yes to accept.

+ It will ask if the installation location is ok. Since we are in our home directory, hit enter to accept. If you are not in your home directory, hit ctrl+c, navigate there, and start over with the bash command.

+ Finally, it will ask if you want to initialize Miniconda3. Say yes to this as well.

Now that it’s installed we need to reload the current shell session with:

```
source ~/.bashrc
```

You should now see a (base) at the start of your command prompt, indicating you are in the “base” conda environment.

## Install tools in your Base environment

Your base environment is your “home base”, things installed here will always be accessible to you as a user regardless of your location within the server. However, you want to avoid installing everything this way, as it is bound to lead to version or software conflicts. Instead, this can be a good place to install smaller, simple programs that get used very frequently.

One example of this kind of tool is FastQC, a lightweight tool for generating summary reports of sequencing read files. Try googling conda install FastQC. The first page should be the anaconda.org page for the tool, and searching any tool this way is a quick and easy to find the installation “recipe”.

```
conda install -c bioconda fastqc
```

Now try to execute the command (try tab complete too!)

```
fastqc --help
```

The manual page should now show up on your screen if the installation was successful.

## Create environment + install tools

Instead of base installations, more complicated installations (either large or with many dependencies) can be installed by creating a new environment.

The basic structure of this is

```
conda create -n new-env
```

This will simply create an empty environment called new-env, with python3 as the default version based on the installation of Miniconda that we chose.

As you start creating many environments you might forget the names of a few, which you can look up using:

```
conda env list
```

You can activate your new environment using

```
conda activate new-env
```

Instead of (base), your command prompt should now say (new-env)

Exiting or “closing” an environment is done with

```
conda deactivate
```

Creating an environment with a specific python version

As mentioned in the lecture, many bioinformatics tools still rely on python2 or may have legacy dependencies that do. We can create environments for these like so

```
conda create -n python-v2.7 python=2.7
```

And confirm the versions by activating the environment

### Check your base version of python

```
python --version
```

```
conda activate python-v2.7
python --version 
```
Remember to deactivate!
Removing an env

You may find over time that a particular environment or tool is no longer useful to you. You can’t just rm these like other types of files and instead must use a conda-specific command

```
conda env remove -n python-v2.7
conda env remove -n new-env
```

### Installing within an environment

Once an environment is created + activated, installation can be as simple as executing the conda install command for each package inside that environment.
```
conda create -n program
```
And then install the tool itself

```
conda  activate fastANI
conda install -c bioconda fastani
```

Then check your installation with

```
fastANI -h
```

### Create + install in one step

You can also create a new env + install a package all in one step. Let’s do that for kraken2, a metagenomics classifier we will use later this semester.

```
conda create -n kraken2 -c bioconda kraken2
```

### Updating

As time passes, software gets improved + updated into various versions. We want to make sure our installations are up to date, which we can do using `conda update`.

Say it’s been a few months since we last used fastQC, we could update it with

```
conda update fastqc
```
Conda itself needs to be updated, which is done with
```
conda update conda
```

Homework

Before next class, create conda environments and/or install the following tools. - trimmomatic (can be base) - multiqc (can be base) - nanoplot (needs an env)
