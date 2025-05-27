---
title: Setup
---

## Example Workload
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


## Software Setup
You will need access to an *HPC* cluster to run the examples in this lesson.
Refer to the HPC Introduction lessons to learn how to access and use a compute cluster of that scale.

- Executive summary of typical HPC workflow? Or refer to other HPCC courses that cover this
- "HPC Etiquette"
   - E.g. don't run benchmarks on login node
   - Don't disturb jobs on shared nodes
- Setup of example for performance studies

::::::::::::::::::::::::::::::::::::::: discussion

### Common Software on HPC Systems
Working on an HPC system commonly involves a

- *batch system* to schedule *jobs* (e.g. Slurm, PBS Pro, HTCondor, ...), a
- *module* system to load certain versions of centrally provided software and a
- way to log in to a *login node* of the cluster.

:::::::::::::::::::::::::::::::::::::::::::::::::::

To login via `ssh`, you can use on

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

