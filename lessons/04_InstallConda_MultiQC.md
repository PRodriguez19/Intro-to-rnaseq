---
Week: "5" 
Lesson: "Install Conda"
Date: "Thursday, February 22nd, 2024"
---

# Installing Conda 

Conda is an open-source package management system and environment management system that runs on Windows, macOS, and Linux. Conda quickly installs, runs, and updates packages and their dependencies. Conda also easily creates, saves, loads, and switches between environments on your local computer. Conda was created for Python programs but it can package and distribute software for any language.

More information can be found [here](https://docs.anaconda.com/free/miniconda/index.html). 


1. To begin the install, navigate into your home directory: 

	```
	cd ~
	```

2. Grab the miniconda installer:

	```bash
	wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
	```

3. Use the `bash` command to run the installer: 

	```bash
	bash Miniconda3-latest-Linux-x86_64.sh
	```

	+ A few interactive things will happen during this portion:

		+ You will be asked to review the license agreement. To skip to the end of the document hit `q` 

	```
	Do you accept the license terms? [yes|no]
	>>> yes
	```

	+ Type yes to accept.  
	
	```
	In order to continue the installation process, please review the license
	agreement.
	Please, press ENTER to continue
	>>>
	```

	+ It will also ask at some point if the installation location is ok. Since you are installing in your home directory, this is okay, so hit enter to accept. 

	```
	Do you wish to update your shell profile to automatically initialize conda?
	This will activate conda on startup and change the command prompt when activated.
	If you'd prefer that conda's base environment not be activated on startup,
   run the following command when conda is activated:

	conda config --set auto_activate_base false

	You can undo this by running `conda init --reverse $SHELL`? [yes|no]
	[no] >>> 
	```

	+ At this point type "no" 


	```
	==> For changes to take effect, close and re-open your current shell. <==
	```

	+ Go ahead and close your terminal, and sign-in, to continue with the next steps. 
	
	+ If you see the following "error" upon signing in: 
	
	```
	AttributeError: module 'collections' has no attribute 'MutableMapping'
	```

		+ This is only because conda is active in your base envionment. To turn off conda in your base, type this in your terminal: 
	
		```
		conda config --set auto_activate_base false 
    	```

4. **Test your installation.** 

In your terminal, run the command `conda list`. A list of installed packages appears if it has been installed correctly.


```
conda list
```

Your output should look similar to below:

```
(base) [mmg23200@vacc-user1 mmg23200]$ conda list
# packages in environment at /users/m/m/mmg23200/miniconda3:
#
# Name                    Version                   Build  Channel
_libgcc_mutex             0.1                        main  
_openmp_mutex             5.1                       1_gnu  
archspec                  0.2.1              pyhd3eb1b0_0  
boltons                   23.0.0          py311h06a4308_0  
brotli-python             1.0.9           py311h6a678d5_7  
bzip2                     1.0.8                h7b6447c_0  
c-ares                    1.19.1               h5eee18b_0  
ca-certificates           2023.12.12           h06a4308_0  
certifi                   2023.11.17      py311h06a4308_0  
cffi                      1.16.0          py311h5eee18b_0  
charset-normalizer        2.0.4              pyhd3eb1b0_0  
conda                     23.11.0         py311h06a4308_0  
conda-content-trust       0.2.0           py311h06a4308_0  
conda-libmamba-solver     23.12.0            pyhd3eb1b0_1  
conda-package-handling    2.2.0           py311h06a4308_0  
conda-package-streaming   0.9.0           py311h06a4308_0  
cryptography              41.0.7          py311hdda0065_0  
distro                    1.8.0           py311h06a4308_0  
fmt                       9.1.0                hdb19cb5_0  
```

***

### Managing environments
Conda allows you to create separate environments containing files, packages, and their dependencies that will not interact with other environments. You already have a default environment called `base`. The base environment is your "home base", and packages installed here will always be accessible to you regardless of your location within the server. However, you do not want to install all programs into your `base` environment as this can lead to some software conflicts. Instead, you will need to create a separate environment and install a package there. 

We will now create an environment called `multiqc` and install the `multiqc` package. 

Type the following:

```
conda create --name multiqc
```

Conda checks to see what additional packages ("dependencies") if it does ask type 'y'

```
Proceed ([y]/n)? y 
```

To use or activate the environment, type the following: 

```
conda activate multiqc
```

Then you will see your (base) switch to (multiqc). Now install multiqc program: 

```
conda install -c bioconda multiqc
```

Conda will check to see what additional packages ("dependencies") are required, type 'y'. 

```
Proceed ([y]/n)? y
```

**This will take a few minutes! Be patient.** Once it is done you will see the following:

```
Preparing transaction: done                                                     
Verifying transaction: done                                                     
Executing transaction: done
```

To check that `multiqc` is working type: 

```
multiqc --help
```

## MULTIQC 

<p align="center">
<img src="../img/multiqc.jpg" width="500">
</p>

Now lets test out multiqc!  

1. Download data from this location 

	```
	cp -r /gpfs1/cl/mmg3320/course_materials/multiqc_example . 
	```

2. Once downloaded, you will see that this folder, contains another folder called "data". 

3. Generate a multiqc report for all data found inside. 
	
	```
	multiqc -d data/
	```

	+ This is also a great time for you take a look at all the files contained the data folder. 
	+ Notice, that `Multiqc` will provide an aggregate result from many bioinformatic outputs across many samples in a single report! 
	+ This is also a great time for you to understand why the `-d` parameter was added! 

4. Use Filezilla to transfer the multiqc report from the VACC to your local desktop! 

	+ View the report. I don't expect you to understand everything contained within this report, but doesn't hurt to know where we are heading! 

5. Be proud of your efforts! 

*** 

## Additional information on Conda

** This section is just for you to read! Not to try! Do not get confused with all the bioinformatic programs listed in this section.** 

### Creating environment + install tools

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

You can activate your new environment using:

```
conda activate new-env
```

Instead of (base), your command prompt should now say (new-env)

You can exit or “close” an environment with: 

```
conda deactivate
```

Creating an environment with a specific python version

Many bioinformatics tools still rely on python2 or may have legacy dependencies that do. We can create environments for these like so:

```
conda create -n python-v2.7 python=2.7
```

You may also find over time that a particular environment or tool is no longer useful to you. To remove, you must use a conda-specific command: 

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

Always check your installation with --help or -h: 

```
fastANI -h
```

### Create + installing packages in one step

You can also create a new env + install a package all in one step. Below is an example: 

```
conda create -n kraken2 -c bioconda kraken2
```

### Updating

Say it’s been a few months since we last used fastQC, we could update it with:

```
conda update fastqc
```

Conda itself needs to be updated, which is done with: 

```
conda update conda
```




