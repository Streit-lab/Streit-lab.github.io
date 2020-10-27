---
title: Introduction
category: Reproducibility
order: 1
---

## The reproducibility crisis

To combat the growing crisis in reproducing scientific study results, Luslab has a set of standards for bioinformatic work which should be adhered to. This will maximise the reproducibility of your work whilst keeping software quality consistant within the lab.

More resources on the reproducibility crisis can be found here:

- [Nature 2016](https://www.nature.com/news/1-500-scientists-lift-the-lid-on-reproducibility-1.19970)
- [Wiki](https://en.wikipedia.org/wiki/Replication_crisis)
- [Nextflow presentation](https://www.youtube.com/watch?v=jsxTC8pNPUc&feature=emb_logo)

## Luslab data analysis and software

![reproducibility_workflow](../../images/reproducibility_workflow.jpg)

The diagram above shows the three tennets of reproducibility for data pipelines and software in luslab: Reproducibility, Portability and Robustness. Lab members should have this structure in their mind when designing both data analysis pipelines and software to ensure maximum quality and chance of someone else replicating your work.

### Reproducibility and Portability

To maximise the chance of someone else being able to replicate your bioinformatic work, it should be both easily obtainable, reproducible and portable to any machine.

To make your source code easily available we use github. Github is an online store of git version control repositories. We have a luslab [organisation](https://github.com/luslab) that stores repository's of code from different projects for our lab. More information on git can be found [here](https://luslab.github.io/reproducibility/git/) and more information on the luslab github can be found [here](https://luslab.github.io/reproducibility/luslab-github/).

Assessable code gives a user the means to run your pipeline or software; however, without the supporting libraries, operating system or installed packages it will not run properly. Rather than providing a list of required dependencies or even prodiving a configuration file for a package manager such as conda, container technology can be used to build a complete image of the operating system, binaries and other dependencies for the software to run on top of.

We use [Docker](https://www.docker.com/) as our container technology of choice while the CAMP cluster uses [Singularity](https://sylabs.io/docs/). Built containers can be shared to [Docker hub](https://hub.docker.com/) for download alongside code from github to form a reproducible process. More information on how we use docker can be found [here](https://luslab.github.io/reproducibility/docker/).

### Robustness

To ensure reproducibility of results, software and data analysis pipelines should be rigorously tested everytime something is changed. We use [Github Actions](https://github.com/features/actions) to test our code and automate deployments such as pushing tested Docker images to Docker hub. More resources on testing code can be found here:

- [Unit testing 1](http://softwaretestingfundamentals.com/unit-testing/#:~:text=UNIT%20TESTING%20is%20a%20level,and%20usually%20a%20single%20output.)
- [Unit testing 2](https://en.wikipedia.org/wiki/Unit_testing)
- [Continuous integration](https://martinfowler.com/articles/continuousIntegration.html)

### Run framework

After code and containers have been obtained, the code must be executed on the container environment using supplied data to obtain the expected results. While this can be done manually, for data pipelines we use the Nextflow framework which has built-in functionality for integrating to both github and Docker hub. More information on nextflow can be found [here](https://luslab.github.io/nextflow/intro/).