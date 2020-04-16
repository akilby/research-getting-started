# Onboarding guide for RAs

## Getting started with Northeastern's High Performance Computing cluster

Nearly everything we do will be on Northeastern's HPC (also called the Discovery cluster or the Holyoke Cluster). There are many advantages to the cluster - it allows for terabytes of storage, has a large number of fast machines that you can run in parallel, and has all the software (and licenses) we use. It also has some disadvantages: it can be confusing, buggy, and can get overloaded in periods of high demand. There is also a pretty significant learning curve.

### Get an account

You need to first sign up for an account [here using ServiceNow](https://northeastern.service-now.com/research?id=nurc_category). Click `Research Computing Access Request` and fill out the form, indicating me as your sponsor. (Answer *no* to `Do you need access to the following licensed software?`)  

When you've done this, let me know as I will need to initiate approvals on my end, and have you added to my resources.

### Familiarizing yourself with the cluster

There is [extensive documentation about how the cluster works](https://rc-docs.northeastern.edu/en/latest/) and there are [sometimes training sessions](https://rc.northeastern.edu/support/training/) that you can sign up for. You should at least have a look at the documentation, though it contains an overwhelming amount of detail for newbies. I'll try to cover the basics of what you need here.

### Logging in

You'll need to be able to SSH into the cluster. On a Mac, you simply open the Terminal application. On a PC \~thar be dragons\~, i.e., you'll need to figure out an SSH client on your own. Once you have Terminal or equivalent open, type:

```bash
ssh YOUR_USER_NAME@login.discovery.neu.edu
```

You'll be prompted for a password. Enter your NU password, and you should then be logged into the cluster.

*Note: If you find it gets annoying to type your password every time you log in to the HPC, [it's possible to set up SSH keys](http://sshmenu.sourceforge.net/articles/key-setup.html) that automate login. Don't do this right when you're first getting going, but do come back to this once you're all set up if you are finding it annoying.*

### What is this

What you're in here is a basic Linux machine. If you've never interacted with a Linux computer at the command line, you should try to sign up for an HPC tutorial, or read some online guides. 

*(Two workhorse commands you'll use over and over are* `cd`*, or change directory, and* `ls`*, or list contents of the directory. Once you've been added to my resources, try* `cd /work/akilby/` *and then* `ls` *to see what's inside.)*

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

If you use one of my reservations, that's fine, but I only have 11 of them and an increasing number of RAs. That means you shouldn't use more than one or two at a time, and you should always `exit` out of the node when you are done working.

**The most important thing you need to remember is that you may never do any serious work (in Python, R, or Stata) on a login node: you must provision a real machine using srun or sbatch.** If you do work in a login node, you may receive a nasty email from someone annoyed at you for taking down the login server.

### module add
