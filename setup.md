---
title: Setup
---

## Learning Objectives
After attending this training, participants will be able to:

- Explain efficiency in the context of *HPC* systems
- Use batch system tools and third party tools to measure job efficiency
- Describe common concepts and terms related to performance on HPC systems
- Identify hardware components involved in performance considerations
- Achieve first results in performance optimization of their application
- Remember next steps to take towards learning performance optimization

:::::::::::::::::::::::::::::::::::::::::: prereq

- Access to a HPC system
- Example workload setup
- Basic knowledge of HPC systems (batch systems, parallel file systems) -- being able to submit a simple job and understand what happens in broad terms

::::::::::::::::::::::::::::::::::::::::::::::::::



## Example Workload & Setup
<!--
FIXME: place any data you want learners to use in `episodes/data` and then use
       a relative link ( [data zip file](data/lesson-data.zip) ) to provide a
       link to it, replacing the example.com link.

Download the [data zip file](https://example.com/FIXME) and unzip it to your Desktop
-->

Example workload that:

- Has some instructive performance issues that can be discovered, e.g.
   - Mismatch between requested resources in job script and used resources
   - Memory leak or unnecessary allocation with a quick fix? Either triggers OOM or just wasting resources, dependent on side and default memory/core
   - No vectorization?
   - Parallelism issues?
- Software that can run on CPU and GPU, to discuss both with the example


You will need access to an HPC cluster to run the examples in this lesson.
Discuss how to find out where to apply for access as a researcher (in general, in EU, in Germany, in NRW?).
Refer to the [HPC Introduction lessons](https://nesi.github.io/hpc-intro/) to learn how to access and use a compute cluster of that scale.

- Executive summary of typical HPC workflow? Or refer to other HPCC courses that cover this
- "HPC etiquette"
   - E.g. don't run benchmarks on login node
   - Don't disturb jobs on shared nodes
- Setup of example for performance studies


:::::::::::::::::::::::::::::::::::::::::: discussion 

### Common Software on HPC Systems
Working on an HPC system commonly involves a

- *batch system* to schedule *jobs* (e.g. Slurm, PBS Pro, HTCondor, ...), a
- *module* system to load certain versions of centrally provided software and a
- way to log in to a *login node* of the cluster.

::::::::::::::::::::::::::::::::::::::::::::::::::::::

To login via `ssh`, you can use on (remove this since it's discussed in HPC introduction?)

:::::::::::::::: spoiler

### Windows

- PuTTY
- `ssh` in PowerShell

::::::::::::::::::::::::

:::::::::::::::: spoiler

### MacOS

- `ssh` in Terminal.app

::::::::::::::::::::::::


:::::::::::::::: spoiler

### Linux

- `ssh` in Terminal

::::::::::::::::::::::::
