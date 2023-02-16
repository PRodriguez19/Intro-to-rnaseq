---
Week: "5" 
Lesson: "Install Conda"
Date: "Tuesday, February 16th, 2023"
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

Add path to bash profile 

```
cd ~
nano .bash_profile
export PATH=$PATH:/users/m/m/mmg232in/miniconda3/bin

source .bash_profile
```

Navigate to raw_fastq directory 

```
multiqc .
```