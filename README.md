# Onboarding guide for RAs

### Table of Contents
1. [Getting Started with the HPC](#getting-started-with-northeasterns-high-performance-computing-cluster)
2. [Running Python in a virtual environment](#running-python-in-a-virtual-environment)
3. [Getting files on and off the HPC](#getting-files-on-and-off-the-hpc)
4. [Installing and using Kilby group specific packages](#installing-and-using-kilby-group-specific-packages)
5. [Using GitHub repos to manage code](#using-github-repos-to-manage-code)
6. [Jupyter notebooks with Open OnDemand](#jupyter-notebooks-with-open-ondemand)
7. [Text editors](#text-editors)
8. [Tips and tricks](#tips-and-tricks)

## Getting started with Northeastern's High Performance Computing cluster

Nearly everything we do will be on Northeastern's HPC (also called the Discovery cluster or the Holyoke Cluster). There are many advantages to the cluster - it allows for terabytes of storage, has a large number of fast machines that you can run in parallel, and has all the software (and licenses) we use. It also has some disadvantages: it can be confusing, buggy, and can get overloaded in periods of high demand. There is also a pretty significant learning curve.

### Get an account

You need to first sign up for an account [here using ServiceNow](https://northeastern.service-now.com/research?id=nurc_category). Click `Research Computing Access Request` and fill out the form, indicating me as your sponsor. (Answer *no* to `Do you need access to the following licensed software?`)  

When you've done this, let me know as I will need to initiate approvals on my end, and have you added to my resources.

### Familiarizing yourself with the cluster

There is [extensive documentation about how the cluster works](https://rc-docs.northeastern.edu/en/latest/) and there are [sometimes training sessions](https://rc.northeastern.edu/support/training/) that you can sign up for. You should at least have a look at the documentation, though it contains an overwhelming amount of detail for newbies. I'll try to cover the basics of what you need here.

### Logging in

You'll need to be able to SSH into the cluster. On a Mac, you simply open the Terminal application. On a PC \~thar be dragons\~, i.e., you'll need to figure out an SSH client on your own (OpenSSH may come installed; some recommend PowerShell). Once you have Terminal or equivalent open, type:

```bash
ssh YOUR_USER_NAME@login.discovery.neu.edu
```

You'll be prompted for a password. Enter your NU password, and you should then be logged into the cluster.

*Note: If you find it gets annoying to type your password every time you log in to the HPC, [it's possible to set up SSH keys](http://sshmenu.sourceforge.net/articles/key-setup.html) that automate login. Don't do this right when you're first getting going, but do come back to this once you're all set up if you are finding it annoying.*

### What is this

What you're in here is a basic Linux machine. If you've never interacted with a Linux computer at the command line, you should try to sign up for an HPC tutorial, or read some online guides. Here's a [reasonable one.](https://www.techspot.com/guides/835-linux-command-line-basics/)

*(Three workhorse commands you'll use over and over are* `cd`*, or change directory,* `ls`*, or list contents of the directory, and* `mkdir`*, or make a new directory. Once you've been added to my resources, try* `cd /work/akilby/` *and then* `ls` *to see what's inside.)*

### How to start a machine to do work

When you log in, you're in what's called a *login node.* To do any substantial work, you need to start up a real machine using one of two commands, `srun` or  `sbatch`. You will mostly use `srun` to start. To log into a machine, try:

```bash
srun --partition=short --time=0-04:00:00  --pty /bin/bash
```

If this doesn't work, talk to me, as you may not be properly configured. 

Next, type `exit` to get out of this node and go back to a login node. 

The basic machines on Discovery are not usually good enough to do the kinds of work we do. To get a machine with enough juice, you can specify more options, like so:

```bash
srun --partition=short --mem=0 --exclusive --time=1-00:00:00 --cpus-per-task=28 --pty /bin/bash
```

I also have reservations that are well-resourced. You should be able to access them using:

```bash
srun --partition=reservation --reservation=kilby --exclusive --mem=0 --time=1-00:00:00 --pty /bin/bash
```

If you use one of my reservations, that's fine, but I only have 11 of them and an increasing number of RAs. That means you shouldn't use more than one or two at a time, and you should always `exit` out of the node when you are done working, to release the machine for others to use.

**The most important thing you need to remember is that you may never do any serious work (in Python, R, or Stata) on a login node: you must provision a real machine using srun or sbatch.** If you do work in a login node, you may receive a nasty email from someone annoyed at you for taking down the login server.

### How to use Python

In order to use software on the HPC, you have to add the module first. To see modules available on the HPC, type

```bash
module avail
```
And in order to add one such as Python for use, type

```bash
module add python/3.7.3-base
``` 

It may become annoying to type `module add python/3.7.3-base` every time you log in. To do something automatically at login, you need to modify the file found at `~/.bashrc`. 

*(The "easiest" way to do this is with the world's most difficult-to-use text editor, called vi. Type* `vi ~/.bashrc` *then press the down arrow to get to the bottom of the file. Press* `i` *for insert, then type* `module add python/3.7.3-base`. *Then hit, in order,* `esc`, *then* `:`, *then* `w`, *then* `q`, *then* `enter`. 

*A great, short tutorial is [here](https://www.howtogeek.com/102468/a-beginners-guide-to-editing-text-files-with-vi/). More [here](https://en.wikipedia.org/wiki/Editor_war).)*

### For more

[Other notes on the HPC, Unix, and Slurm](https://github.com/akilby/research-getting-started/tree/master/hpc#other-notes-on-the-hpc-unix-and-slurm)

## Running Python in a virtual environment

Much of our work will be in Python. However, the Python installed on the cluster is only Python with its standard packages. In order to add other packages (which we will need to do), you'll need to work inside a *virtual environment,* which allows you to install packages and modify your environment without modifying the base Python installation that's on the cluster.  

The below should work as long as the package that manages virtual environments has already been installed system-wide, which I believe it should already be on the cluster. (If not, try `pip install virtualenv`.)

To create a venv, make sure you've already `module add`ed Python per the above instructions. `mkdir` a directory where you will store venvs, such as `/home/YOUR_USER_NAME/venvs/`, and `cd` into it. Type (using a virtual environment name like *general*):

```bash
python -m venv VENVNAME 
```

To activate the venv (after first logging into a node using `srun`):
```bash
source VENVNAME/bin/activate 
```
You are now in a virtual environment that you can use to install custom packages either from our group or from the open source community. Packages are managed using `pip`. First, type 		

```bash		
pip list		
```		

To see what's installed so far. You will then need to type:

```bash
pip install -U pip wheel setuptools
```

You will probably also see a warning that pip itself is out of date. Do as advised and type: 

```bash
pip install --upgrade pip
```

to update. You can now install any packages you need using `pip`. For instance, you will need the popular data management package Pandas, which you can install like so:

```bash
pip install pandas
```

To check your installation worked, type `python` then `import pandas` -- this should work without raising an error.

Every time you log in to a node to work, you'll need to activate this venv:

```bash
source /home/YOUR_USER_NAME/venvs/VENVNAME/bin/activate 
```

To exit the venv when finished, you can type:
```bash
deactivate 
```

(or you can just log out without deactivating; it won't do any harm.)

## Getting files on and off the HPC

1. Using `xfer` and `scp`

    The simple command line option to move a file from the HPC to your computer:
    ```bash
    scp YOUR_USER_NAME@xfer.discovery.neu.edu:/PATH/TO/FILE /PATH/TO/DESTINATION
    ```
    or, from your computer to the HPC:
    ```bash
    scp /PATH/TO/FILE YOUR_USER_NAME@xfer.discovery.neu.edu:/PATH/TO/DESTINATION
    ```
    More detail in the [RC Docs](https://rc-docs.northeastern.edu/en/latest/using-discovery/transferringdata.html#), especially regarding how to do this on a PC.
    
2. Using Discovery's Open OnDemand

    Open OnDemand is a graphical interface for Discovery that we will use more later in this guide. You can log on by navigating to https://ood.discovery.neu.edu/ and entering your username and password.
    
    At the top is a menu. Click on `Files`, then `Home Directory`. This will take you to a graphical interface where you can upload and download files from the HPC.
    
3. Using GitHib, for code and scripts

    You will not use typical file transfer to get your scripts and code onto the HPC. Instead we will manage code via GitHub. To get going with this requires some work. See the [next section](#using-github-repos-to-manage-code).


## Installing and using Kilby group specific packages

In your virtual environment, install three internal packages as follows:

```bash
pip install -e /work/akilby/Packages/npi/
pip install -e /work/akilby/Packages/utility_data/
pip install -e /work/akilby/Packages/cache/
```

## Using GitHub repos to manage code

If you haven't already, sign up for a free [GitHub Education](https://education.github.com/students) account. I will add you as a contributor to the relevant repos for your project.

Once you have an account, you will need to get set up so you can push and pull from GitHub to your local computer. I do this using SSH keys, which you will have already worked with if you followed the note above under *Getting Started with the HPC*, [Logging in](#logging-in). 

GitHub has a [full guide here](https://docs.github.com/en/github/authenticating-to-github/connecting-to-github-with-ssh) on the steps you need to accomplish this. Specifically, you should start with [Checking for existing SSH keys](https://docs.github.com/en/github/authenticating-to-github/checking-for-existing-ssh-keys), which you will have if you installed them while following *Getting Started with the HPC*. Otherwise, you will likely have to create new SSH keys per the instructions in [Generating a new SSH key and adding it to the ssh-agent](https://docs.github.com/en/github/authenticating-to-github/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent). Finally, follow their instructions to [add the keys to your GitHub account](https://docs.github.com/en/github/authenticating-to-github/adding-a-new-ssh-key-to-your-github-account).



## Jupyter notebooks with Open OnDemand

Jupyter notebooks are an easy way to interact with Python and generate visual, easy-to-view presentations of results. 

The easiest way to use a Jupyter notebook is to navigate NU's Open OnDemand: 

https://ood.discovery.neu.edu/

And click on "Interactive Apps" -> Jupyter Notebook - Kilby Group.

(Or just "Interactive Apps" -> Jupyter Notebook if you don't want to use one of our reservations)

You can generally start a notebook with 24 hours and 250GB memory unless you know you need less.

You will then click on "Connect to Jupyter" and will see a listing of all the files in your home directory. You'll need a folder for virtual environments.  You can make one here if you like, by clicking "New" in the top right corner, and clicking "Folder." It will make an Untitled Folder, so you should title it something like "Notebooks." 

You will also want to be able to run your Jupyter notebook *inside* the virtual environment you created above. To do so requires running Jupyter from a *custom kernel* using your venv.

Switch back to the ssh interface you've chosen (Terminal on a mac) and make sure you're logged into Discovery. Source into the virtual environment you created as you normally do, per the above. Make sure that Jupyter is installed, as well as ipykernel:

```bash
pip install jupyter
```
```bash
pip install ipykernel
```

Then simply type:

```bash
ipython kernel install --user --name=NAME_OF_VENV
```

Now, when you create a new notebook in the Jupyter graphical interface in Open OnDemand, you'll see your venv (named whatever you called it above) as an option when clicking "New" in the upper right hand corner. When you select it, a new Jupyter notebook will open with NAME_OF_VENV as a kernel, and all your user packages will be avialable to you.


## Text editors

It is good to use a full-featured text editor to edit code. [This article](https://kinsta.com/blog/best-text-editors/) has a good overview of the best options; I use [Sublime Text](https://www.sublimetext.com/). 

### Sublime Text customization

Sublime Text has a wide variety of packages written by the open-source community to make writing code a better experience. We will work mostly in Python, so it is a very good idea to install a [*code linter*](https://en.wikipedia.org/wiki/Lint_(software)) for Python that will assist you in debugging and writing code with better style. To set up Sublime Text to lint Python, you should:

*Install Package Control*

1. Get Package Control (used to install Sublime Text packages) by typing Command-Shift-P and then typing “Install Package Control” and pressing "Enter."


*Code Linting*

1. In Terminal or at the command line you use to access Python on your home computer, use ```pip``` to install flake8 by typing: ```pip install flake8```. Do **not** do this inside a virtual environment.
2. In Sublime Text, use Package Control to install SublimeLinter. Type 

    ```command-shift-p -> Install Packages -> SublimeLinter```

3. One SublimeLinter is installed, you have to install the linting packages for Python. I use several. Type:

    * ```command-shift-p -> Install Packages -> SublimeLinter-pycodestyle```

    * ```command-shift-p -> Install Packages -> SublimeLinter-flake8```

    * ```command-shift-p -> Install Packages -> SublimeLinter-pyflakes```

[Here](https://janikarhunen.fi/three-steps-to-lint-python-3-6-in-sublime-text) is a useful article going over these steps in a bit more detail.

## Tips and tricks

[Here's](https://github.com/akilby/research-getting-started/tree/master/tips) a collection of useful tips and tricks I've accumulated over the years.
