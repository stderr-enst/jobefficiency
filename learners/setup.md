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
- Basic knowledge of HPC systems (batch systems, parallel file systems, modules) -- being able to submit a simple job and understand what happens in broad terms
- Knowledge of tools to work with HPC systems:
   - Bash shell & scripting
   - ssh & scp
   - Simple slurm jobscripts and commands like `srun`, `sbatch`, `squeue`, `scancel`
   - git

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


### Example Workload: Snowman Raytracer
<!--
FIXME: place any data you want learners to use in `episodes/data` and then use
       a relative link ( [data zip file](data/lesson-data.zip) ) to provide a
       link to it, replacing the example.com link.

Download the [data zip file](https://example.com/FIXME) and unzip it to your Desktop
-->

Get the code:

```bash
git clone --recursive git@github.com:HellmannM/raytracer-vectorization-example.git
cd raytracer-vectorization-example.git
git checkout CUDA_snowman
```

#### CPU Build
Prepare the out-of-source build:

```bash
cd ..
mkdir build && cd build
cmake -DCMAKE_BUILD_TYPE=Release -DENABLE_CUDA=OFF ../raytracer-vectorization-example.git
```

To build the example, you need to provide the following dependencies:

- Compiler, e.g. GCC
- MPI, e.g. OpenMPI
- CMake
- Boost
- libpng

In HPC systems this often happens through loading software modules.
How exactly the modules are named and what has to be loaded can very much depend on the specific configuration of your cluster.
In this case it looks like this:

```bash
module load 2025 GCC/13.2.0 OpenMPI/4.1.6 buildenv/default Boost/1.83.0 CMake/3.27.6 libpng/1.6.40
```

Finally build and run the code

```bash
cmake --build . --parallel
mpirun -n 4 ./build/raytracer -width=512 -height=512 -spp=128 -threads=1 -png=snowman.png
```

This is

- starting the raytracer, with a prepared scene,
- calculating the raytraced picture with $N = 4$ MPI processes, each using a single thread (`-threads=1`),
- calculating $128 / N = 32$ samples per pixel (`-spp=128`) in each MPI process,
- setting `height` and `width` of the resulting picture to $512$ pixel, and finally
- storing the picture as `snowman.png`.

More on what a raytracer is and how it works.
How does it parallelize?

#### CUDA Build
Prepare the out-of-source build:

```bash
cd ..
mkdir build_gpu && cd build_gpu
cmake -DCMAKE_BUILD_TYPE=Release -DENABLE_CUDA=ON ../raytracer-vectorization-example.git
```

Additionally to above dependencies, this relies on CUDA and corresponding modules of your site.
The application is still run with MPI, but mostly to manage multiple processes, e.g. one per GPU:

```bash
cmake --build . --parallel
export CUDA_VISIBLE_DEVICES=0,1,2,3
mpirun -n 4 ./build/raytracer -width=512 -height=512 -spp=128 -threads=1 -png=snowman.png
```


## Acknowledgements

Course created in context of [HPC.NRW](https://hpc.dh.nrw/).
