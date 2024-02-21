---
Week: "5" 
Lesson: "Install Conda"
Date: "Thursday, February 22nd, 2024"
---

# Installing Conda 
To run both `kraken2` and `bracken` we will need to install these packages into our VACC account/environment. To install both programs, the easiest way forward is by using a package manager system called `Conda`. 

Conda is an open-source package management system and environment management system that runs on Windows, macOS, and Linux. Conda quickly installs, runs, and updates packages and their dependencies. Conda easily creates, saves, loads, and switches between environments on your local computer. It was created for Python programs but it can package and distribute software for any language.

To begin the install, create a folder called software 

```
mkdir software
```

Navigate into software folder 

```
cd software/
```

Download the installer:

```bash
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
```

And then run the bash command in your terminal: 

```bash
bash Miniconda3-latest-Linux-x86_64.sh
```

A few interactive things will happen during this portion:

+ You will be asked to review the license agreement. To skip to the end of the licenses agreement hit `q`. 

```
Do you accept the license terms? [yes|no]
>>> yes
```

+ At the end you will need to press enter yes to accept:  

```
In order to continue the installation process, please review the license
agreement.
Please, press ENTER to continue
>>>
```
+ It will ask if the installation location is ok. Hit enter to accept. 

```
Do you wish to update your shell profile to automatically initialize conda?
This will activate conda on startup and change the command prompt when activated.
If you'd prefer that conda's base environment not be activated on startup,
   run the following command when conda is activated:

conda config --set auto_activate_base false

You can undo this by running `conda init --reverse $SHELL`? [yes|no]
[no] >>> 
```

```
==> For changes to take effect, close and re-open your current shell. <==
```



+ Finally, it will ask if you want to initialize Miniconda3. Say yes to this as well.

```
Do you wish the installer to initialize Miniconda3
by running conda init? [yes|no]
[no] >>> yes
```

Now that it’s installed we need to reload the current shell session with:

```
source ~/.bashrc
```

**Test your installation.** In your terminal, run the command `conda list`. A list of installed packages appears if it has been installed correctly. If not hang tight! 


Test to see if conda works now: 

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
Conda allows you to create separate environments containing files, packages, and their dependencies that will not interact with other environments. When you begin using conda, you already have a default environment name `base`. The base environment is your "home base", and packages installed here will always be accessible to you regardless of your location within the server. However, you do not want to install all programs into your `base` environment as this can lead to some software conflicts. Instead, you will need to create a separate environment and install a package here. 

We will now create an environment called `kraken2` and install the `kraken2` and `bracken` package here.  

Type the following:

```
conda create -n kraken2 -c conda-forge -c bioconda -c defaults kraken2 bracken
```

Conda checks to see what additional packages ("dependencies") if it does ask type 'y'

```
Proceed ([y]/n)? y 
```

Once the installation is complete you will see:

```
Preparing transaction: done
Verifying transaction: done
Executing transaction: done
```

## Managing environments
Conda allows you to create separate environments containing files, packages, and their dependencies that will not interact with other environments. When you begin using conda, you already have a default environment name `base`. The base environment is your "home base", and packages installed here will always be accessible to you regardless of your location within the server. However, you do not want to install all programs into your `base` environment as this can lead to some software conflicts. Instead, create a separate environment and install a package here. 


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

Then you see your (base) switch to (multiqc). Now install multiqc program: 

```
conda install -c bioconda multiqc
```

Conda will check to see what additional packages ("dependencies") are required, type 'y'. 

```
Proceed ([y]/n)? y
```

This may take a few minutes. Once it is done you will see the following:

```
Preparing transaction: done                                                     
Verifying transaction: done                                                     
Executing transaction: done
```

To check that `multiqc` is working type: 

```
multiqc --help
```

If so, navigate to raw_fastq directory and type the following to generate a multiqc .html file. 

```
multiqc .
```

Multiqc will provide an aggregate result from bioinformatic analyses across many samples in a single report! 

The final output is an .html report that should look like this: 

If you start your screen and see this:

```
AttributeError: module 'collections' has no attribute 'MutableMapping'
```

+ this is just because conda is active in your base envionment. To turn off this you can: 

conda config --set auto_activate_base false 