---
title: Intro
category: CAMP
order: 1
---

## Introduction to CAMP

CAMP is our high performance cluster at the Crick.
People have different attitudes, some run most of their analysis on personal laptop and use the cluster when necessary.
Others run most of their analysis on CAMP regardless.

- I'm stuck! - Post on [#hpc](https://app.slack.com/client/T0H1NAC7M/C2Z0C6Y1L/details/top) channel on Crick Computing Chat Slack workspace, you will often get a fast reply. People also post here if they think the cluster is running slowly or broken.

- I'm stuck but I'm embarassed - Sometimes we post on [#lus-hpc](https://app.slack.com/client/T0H1NAC7M/G4HDXGVC3/details/top) to ask questions just to other people in the lab if we feel like they might know or answer quicker.


### Getting access

There are two main parts to CAMP, CAMP compute and CAMP storage, and you will need access to both.
Access to CAMP storage lets you mount CAMP folders onto your laptop and read/write files.
Access to CAMP compute lets you access the cluster via ssh and send jobs .etc.

To request access email the IT helpdesk - its-helpdesk@crick.ac.uk, you will need to generate a public key to send to the HPC team. 

Here are some instructions on how to do this:

Open a terminal window. At the shell prompt, type the following command:

`ssh-keygen -t rsa`

The ssh-keygen program will prompt you for the location of the key file. Press Return to accept the defaults. You can optionally specify a passphrase to protect your key material. Press Return to omit the passphrase. The output of the program will look similar to this:

```
Enter file in which to save the key (/Users/tony/.ssh/id_rsa):
Created directory '/Users/tony/.ssh'.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /Users/tony/.ssh/id_rsa.
Your public key has been saved in /Users/tony/.ssh/id_rsa.pub.
```

### Once you have an account, how to log on

You can log on to CAMP via ssh, we have access to two login nodes - login000 and login001. If there is a problem with one, you might try the other:

`ssh -XY login000.camp.thecrick.org`

Once you logon you'll find yourself in a directory with very little space, where you will not want to save anything.
Most people will symbolically link their working directory to this directory so they can easily go there when they log on.

### Directory structure on CAMP

The base directory is /camp/. From there you will mostly be using /camp/lab/luscomben/home/. Other labs have their own folders in the "lab" directory. If you find yourself working with another lab a lot, you might consider asking for a project directory between the two labs, this should simplify sharing because both labs should then have access to the directory. e.g we have a shared directory with the Jernej Ule lab at /camp/project/proj-luscombe-ule.

From /camp/lab/luscomben/home/ there are two main directories: shared and users. Everyone has their own personal folder on users and this what I mean when I refer to the "working directory". The shared folder contains data that we all have access to and so we like to keep this tidy. We ask that whenever someone finds themselves downloading a resource file, such as a genome, an annotation .etc that they try and add this to the shared folder so that we can save duplicating large files in our personal directories.

The structure of the shared folder is:

```
/camp/lab/luscomben/home/shared
├── archive
|   └── folder structures that have been archived, so we can easily see in case we want to get things back
├── legacy
|   └── murky secrets from the lab's dark past, best left alone
├── projects
|   └── if you have within lab projects you might consider making a shared directory here, for multi-lab projects best to ask for a project folder
├── ref
    └── genomes
        └── human
        └── mouse
        └── yeast
        └── etc
```

### Doing analysis and running jobs

The "quickstart" version is that CAMP is divided into "partitions" with certain nodes dedicated for certain things.
Mostly you will submit batch jobs to the "cpu" partition and for testing code you will get an interactive node on the "int" partition. 
Never run anything substantial (ie. more than simple head, cd, mkdir .etc commands) on the login node (this is where you are when you first ssh into the cluster).

To get an interactive node with 4 cores you can do something like:
`srun --x11 -N 1 -c 4 --mem=16G --part=int --pty -t 30:00:00 /bin/bash`

The simplest way to run a batch job is to wrap your command like this:
```
sbatch --time=70:00:00 --partition=cpu --ntasks=1 --cpus-per-task=8 --wrap="\
slamdunk all -r /camp/lab/luscomben/home/shared/ref/genomes/mouse/gencode_GRCm38/GRCm38.releaseM25.primary_assembly.genome.fa.gz \
-b /camp/lab/luscomben/home/shared/ref/genomes/mouse/gencode_GRCm38/gencode.vM25.primary_assembly.3primeUTR.bed \
-o results \
-t 8 \
-i $i \
-5 12 -n 100 -m -rl 100 --skip-sam \
slamdunk_metadata.csv\
"
```

Note that "threads" are equivalent to --cpus-per-task and the max seems to be 32 on the cpu partition.
We can only have *one* interactive session running at a time. If you can't get one, it might be that you have one running that you forgot about.

To check what you've got running:
`squeue | grep *username*`

To cancel a job:
`scancel JOBID`

For all the details read HPC's wiki: https://wiki.thecrick.org/display/HPC/Analysis+%7C+Simulation+%7C+Processing

### Permissions on files and folders

Permissions on CAMP are frequently a problem when trying to share files around. HPC have set up defaults using ACLs. 

For changing permissions instructions for our lab from HPC team are: *Chown is fine, Chmod is likely to grant permissions to more people than you meant to! But should keep the ACL.*

We can also use `setfacl` to change permissions. A good resource for constructing ACL commands: https://www.computerhope.com/unix/usetfacl.htm.

If you can't sort the permissions out yourself it's pretty common to raise an IT ticket and/or post on Slack #hpc channel.
