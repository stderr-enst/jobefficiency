---
title: Setup
---

## Learning Objectives
After attending this training, participants will be able to:

- Explain efficiency in the context of *HPC* systems
- Use batch system tools and third party tools to measure job efficiency
- Discern between worse and better performing jobs
- Describe common concepts and terms related to performance on HPC systems
- Identify hardware components involved in performance considerations
- Achieve first results in performance optimization of their application
- Recall next steps to take towards learning performance optimization


## Prerequisites
:::::::::::::::::::::::::::::::::::::::::: prereq

- Access to an HPC system
- Example workload setup
- Basic knowledge of HPC systems (batch systems, parallel file systems) -- being able to submit a simple job and understand what happens in broad terms
- Knowledge of tools to work with HPC systems:
   - Bash shell & scripting
   - ssh & scp
   - Simple slurm jobscripts and commands like `srun`, `sbatch`, `squeue`, `scancel`

::::::::::::::::::::::::::::::::::::::::::::::::::


:::::::::::::::::::::::::: instructor
## ToDo: Improve prerequisites
Link to external resources in prerequisites:

- HPC Intro
- HPC Shell
- HPC.NRW
- Amount of knowledge about MPI, OpenMPI, CUDA, etc.?
  - Don't require in-depth MPI knowledge, but some basic understanding might be necessary?

Maybe make sure required definitions / concepts are available in the hpc-wiki and link to those? But this course should be somewhat self-contained.
"Jargon buster" similar to HPC intro?

Maybe add some form of self test, e.g. like [PC2 HPC and Linux self test?](https://pc2.uni-paderborn.de/teaching-old/trainings/hpc-user-trainings/selftests/selftest-hpc)
Or as an exercise in the setup / prerequisites sections?

Selftest should help to answer "Is the course for me?", i.e. prerequisites should be mostly green, course material should be mostly red

:::::::::::::::::::::::::::::::::::::



### Example Workload & Setup
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
   - Uncover several performance issues in layers, one after the other?
- Reasonable execution time
  - Not too long to not slow the pacing of the course (e.g. 10 minutes)
  - Long enough to show real workflow and performance issues
  - E.g. 1 minute with 1 core doesn't leave enough headroom for scaling studies
  - We should avoid to switch problem size with each exercise to keep things consistent and go through an experience of improvement if things get faster
  - Changing workload might still be necessary at certain points. We have to be intentional and clear about it in the course and limit these!
- Software that can run on CPU and GPU, to discuss both with the example
- Should be easy to download, compile, run, and be understood (readability)
  - Provide example application in a container as a generic fallback solution?
- Meaningful workflow for batch processing
- Using commonly used programming languages / libraries in HPC
- Will likely not show all performance issues that could exist, only used as a vehicle to follow a narrative with particular performance issues


We are still looking, but are considering:

- Kuwahara filter
- Raytracer
- Simple agent-based simulation game
- (Benchmarks are portable, but they don't really show performance issues and are often complex)


### HPC Access
:::::::::::::::::::::::::: instructor
## Tell learners how to get access to an HPC System

- Do they need to apply somewhere?
- Are they eligible to request access to another system?
- Are they expected to already have an account?
- Could they try to log in in advance?
- Is there maybe some test cluster in the cloud?

:::::::::::::::::::::::::::::::::::::

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


## Acknowledgements

Course created in context of [HPC.NRW](https://hpc.dh.nrw/).
