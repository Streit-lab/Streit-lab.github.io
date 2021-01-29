---
title: Why we are using Nextflow
category: Nextflow
order: 5
---


We use Nextflow in order to:

 - Work on analysis in a way that is collaborative
 - Make our computational projects reproducible


Analysis of a dataset, e.g. an RNA-seq dataset, requires a pipeline to analyse it. The pipeline is made of different steps e.g. alignment of the FASTA sequencing, quality control steps, making plots, looking for differentially expressed genes, etc. 

Each of these steps depend on different programs and might be written in different programming languages. This makes it very difficult to keep track of how we did every single step in an organised manner, and almost impossible to recreate earlier steps at a later time or on a different computer. Even a small difference, like having a newer version of one program, could completely change the results of a pipeline.

Nextflow is a programming language that connects multiple steps into one uniform pipeline. This pipeline can be run from anywhere - any computer or HPC - and give exactly the same result. 


Additional benefits of Nextflow:
- easy parallelisation ~ a way to make things run faster by splitting them up
- can break up a very complicated project e.g. if you have many different datasets to analyse, into easy to use pipelines
- it is becoming more widely used so we can be part of the nf community and make use of already written processes in our pipelines
- Nextflow is very modular, we can make bits of code that can be reused for other purposes and shared, maximising efficiency