---
title: How Nextflow works
category: Nextflow
order: 6
---


Nextflow is a programming language that is based on Groovy. As well as the language itself, we are aiming to create pipelines using a similar structure that NF-core use. This structure includes many small 'building blocks' that can be reused for lots of different purposes. 


## Pipeline overview: Steps and Channels

A pipeline is made up of **steps**. Each step has an input and an output.

Input data --> Step 1 --> Step 2 --> Output

The way the data flows between these steps is determined by **channels**. Channels can be adapted to change the shape of the pipeline, for example they can split data into two parts to go into two different processes. They can also allow different bits of data (e.g. different samples) to go into a step in parallel or if they need to go in one at a time. 

<!-- replace with image here, and show alternative architecture -->

Example pipeline for RNA-seq data:

Raw data from sequencer --> Alignment process --> Downstream process


## Inside a step: processes

Steps are big chunk of a pipeline. For example, a 'Downstream analysis' step might include quality control steps, finding differentially expressed genes and making plots. 

Downstream step: 
Quality control -> find DEGs -> make plots
<!-- replace with image here -->

These smaller steps are called **processes**. Again, the data flow between processes is controlled by channels, meaning that these processes can be connected in lots of different ways. 


## Inside a process: modules

Let's look at some examples of processes. A 'Quality Control Process' might run one R script which has all the code to make some QC plots of the data and then filter the data to clean it up. A 'Find DEGs Process' would run a different R script, this time to find differentially expressed genes. As both processes are doing essentially the same thing (running an R script), they can make use of a common piece of code called a **module**. 

Modules (also called tools) are the smallest building block of the Nextflow pipeline. They are reusable for lots of different processes. We either use exisiting modules from shared locations or, if we want to make our own module, we would put it somewhere other people can access it. We don't have to write every single process from scratch because we can use these modules, and this saves a lot of time. 

For example, we have a module called R_analysis. This module takes the name of a R script as its input and then runs the R script. This module is used by a processes that run an R script, all that changes between the process that does the QC or the one that finds DEGs is which R script it is running. 
