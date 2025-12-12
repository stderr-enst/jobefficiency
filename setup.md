---
title: Setup
---

## Learning Objectives
After attending this training, participants will be able to:

- Explain efficiency in the context of *High Performance Computing* (*HPC*) systems
- Use batch system tools and third party tools to measure job efficiency
- Discern between worse and better performing jobs
- Describe common concepts and terms related to performance on HPC systems
- Identify hardware components involved in performance considerations
- Achieve first results in performance optimization of their application
- Recall next steps to take towards learning performance optimization


## Prerequisites
::: prereq

- Access to an HPC system
- Example workload setup
- Basic knowledge of HPC systems (batch systems, parallel file systems, modules) -- being able to submit a simple job and understand what happens in broad terms
- Knowledge of tools to work with HPC systems:
   - Bash shell & scripting
   - ssh & scp
   - Simple slurm jobscripts and commands like `srun`, `sbatch`, `squeue`, `scancel`
   - git

::::::::::


::: instructor
## ToDo: Improve prerequisites
Link to external resources in prerequisites:

- HPC Intro
- HPC Shell
- HPC.NRW
- Maybe Python for plotting performance data?
- Amount of knowledge about MPI, OpenMPI, CUDA, etc.?
  - Don't require in-depth MPI knowledge, but some basic understanding might be necessary?

Maybe make sure required definitions / concepts are available in the hpc-wiki and link to those? But this course should be somewhat self-contained.
"Jargon buster" similar to HPC intro?

Maybe add some form of self test, e.g. like [PC2 HPC and Linux self test?](https://pc2.uni-paderborn.de/teaching-old/trainings/hpc-user-trainings/selftests/selftest-hpc)
Or as an exercise in the setup / prerequisites sections?

Selftest should help to answer "Is the course for me?", i.e. prerequisites should be mostly green, course material should be mostly red

::::::::::::::


### HPC Access
::: instructor
## Tell learners how to get access to an HPC System

- Do they need to apply somewhere?
- Are they eligible to request access to another system?
- Are they expected to already have an account?
- Could they try to log in in advance?
- Is there maybe some test cluster in the cloud?

::::::::::::::

You will need access to an HPC cluster to run the examples in this lesson.
Discuss how to find out where to apply for access as a researcher (in general, in EU, in Germany, in NRW?).
Refer to the [HPC Introduction lessons](https://nesi.github.io/hpc-intro/) to learn how to access and use a compute cluster of that scale.

- Executive summary of typical HPC workflow? Or refer to other HPCC courses that cover this
- "HPC etiquette"
   - E.g. don't run benchmarks and other computationally heavy workloads on login node. Emphasise their purpose
   - Don't disturb jobs on shared nodes (<-- this phrasing is hard to grasp for newcomers and should be avoided. It will block them from trying things if they are afraid to break anything. Maybe this is more the responsibility of admins and users should just be aware that they may affect other users?)
- Setup of belows workflow example (next section)


::: discussion 

### Common Software on HPC Systems
Working on an HPC system commonly involves a

- *batch system* to schedule *jobs* (e.g. Slurm, PBS Pro, HTCondor, ...), a
- *module* system to load certain versions of centrally provided software and a
- way to log in to a *login node* of the cluster.

::::::::::::::

To login via `ssh`, you can use on (remove this since it's discussed in HPC introduction?)

::: spoiler

### Windows

- PuTTY
- `ssh` in PowerShell

:::::::::::


::: spoiler

### MacOS

- `ssh` in Terminal.app

:::::::::::


::: spoiler

### Linux

- `ssh` in Terminal

:::::::::::


### Example Workload: Snowman Raytracer
<!--
FIXME: place any data you want learners to use in `episodes/data` and then use
       a relative link ( [data zip file](data/lesson-data.zip) ) to provide a
       link to it, replacing the example.com link.

Download the [data zip file](https://example.com/FIXME) and unzip it to your Desktop
-->

::: instructor

## TODO: Episodes are tied together with a narrative around the example job

- Needs a specific example job.
- Gradual improvement throughout the course
- Introduce only topics that are directly observed/experienced with the example
- Point to additional information/overview in hpc-wiki where useful
- Maybe close every episode with the same metric? (snowman pictures / hour at a given energy?)
  - Could start with "?" when we didn't learn yet how to do it in the first episodes
  - Motivates the discovery of certain metrics, tools, etc.

::::::::::::::

Throughout the course, we will use an example application to learn workflows and tools for evaluation of job performance.
The example is a raytracer, rendering a prepared scene.
It provides different means of parallelization, i.e. multiple processes (MPI), multithreading, or on a GPU (CUDA).
MPI and multithreading can be combined.
The GPU accelerated version is utilizing MPI processes just to manage processes and all calculation is done on, potentially multiple, GPUs.

We do not have to study and understand the example code in detail.
After compilation, all necessary options are exposed as different binaries or through command line arguments.

We do, however, have to prepare a build environment with all necessary libraries, and build the code with `CMake`.
This is a common occurrence in scientific software as well.
Researchers are dependent on existing software and their first contact is in a situation like this, where they have to build and prepare the unknown code.
Their first interest typically is: is this project useful for my research?

The example application should be prepared on a central location, e.g. your HPC clusters parallel file system, such that it is accessible for multiple runs on various worker nodes of your cluster.

Let's get started by cloning the repository:

```bash
# Log in to your cluster via ssh first
mkdir jobefficiencyguide && cd jobefficiencyguide
git clone --recursive https://codeberg.org/HPC-NRW/SnowmanRaytracer.git
cd SnowmanRaytracer
```

::: callout

# Do not forget `--recursive`

Our example project depends on another project, implementing the basic raytracing methods.
This dependency is introduced as a `git submodule`, so recursive cloning is necessary, otherwise we cannot build the project.

:::::::::::


#### CPU Build
The example application can perform calculations on CPUs, potentially across multiple nodes using MPI for communication, and/or in multiple threads.
To prepare the out-of-source build:

```bash
# Assuming you are still in the SnowmanRaytracer source directory
cd ..
mkdir build && cd build
```

##### Dependencies
To build the example, you need to provide the following dependencies:

- Compiler, e.g. GCC
- MPI, e.g. OpenMPI
- CMake
- Boost
- libpng

::: instructor

Show learners how to prepare their environment on your particular HPC system.
This also serves as a reminder on how to work with software modules in general.

::::::::::::::


In HPC systems this often happens through loading software modules, centrally provided by your administrators.
How exactly the modules are named and what has to be loaded can very much depend on the specific configuration of your cluster.
In one particular case it may look like this:

```bash
# Only one example, consult your cluster documentation or ask the instructor or your HPC support
module load 2025 GCC/13.2.0 OpenMPI/4.1.6 Boost/1.83.0 CMake/3.27.6 libpng/1.6.40 buildenv/default
```

::: callout

# Software management differs widely on HPC systems
The details of how you load different versions of compilers and libraries very much depend on your particular HPC system.
Follow the instructor or consult your sites documentation or support staff in case of questions!

:::::::::::


##### Building the Software

::: instructor

# Show the preferred build process for your cluster
Do you recommend building the software on your login nodes, since they have enough resources and share the same hardware architecture with the worker nodes?
Or do you recommend building on the target architecture directly?

::::::::::::::

::: instructor

# TODO: Is the description here compatible / identical to the repo readme?

Also discuss output of the application & `scp` to copy the output png

::::::::::::::


Typically, it is recommended to build the software on exactly the same hardware architecture, where it is also intended to run later.
For HPC systems, you have to ask yourself, if the login nodes have enough resources for software compilation and if they share exactly the same hardware with your worker nodes.
Check your cluster documentation for any recommendations!

Here, we will build and test the software in a first Slurm job script, `build_snowman.sbatch`:

```bash
#!/usr/bin/env bash
#SBATCH --job-name=bulid-and-test-Snowman
#SBATCH --nodes=1
#SBATCH --ntasks=4

# Prepare your environment with the dependencies
# This will likely look different in your case!
module load 2025 GCC/13.2.0 OpenMPI/4.1.6 Boost/1.83.0 CMake/3.27.6 libpng/1.6.40 buildenv/default

# Assuming you are submitting from the "build" directory
cmake -DCMAKE_BUILD_TYPE=Release -DENABLE_CUDA=OFF ../SnowmanRaytracer

# Building the software in parallel
cmake --build . --parallel

# First test run with 4 MPI processes
mpirun -n 4 ./raytracer -width=800 -height=800 -spp=128 -threads=1 -png=snowman.png
```

::: instructor

# TODO: Is the script general, useful, correct?

How about `srun cmake`, vs. multiple processes vs. multiple cpus per process?

::::::::::::::

##### Running the Raytracer
The `mpirun` command from our first test run, above, is

- starting the `raytracer` binary, with the prepared scene,
- calculating the raytraced picture with $N = 4$ MPI processes, each using a single thread (`-threads=1`),
- calculating $128 / N = 32$ samples per pixel (`-spp=128`) in each MPI process,
- setting `height` and `width` of the resulting picture to $512$ pixel, and finally
- storing the picture as `snowman.png`.

A raytracer is calculating the interaction of straight "light-rays" with objects placed in a 3D scene.
Each object can have different material properties, resulting in different optical effects, e.g. matte or (partially) translucent surfaces.
Light rays that reach the "camera" are contributing to the final picture, by accumulating their effects across all pixels.

Computationally, all operations can be reduced to matrix-matrix and matrix-vector calculations, which can be individually performed for each ray of light.
This results in different parallelization schemes.
You could divide the pixels of the final picture into regions, where each parallel process calculates one region.
Another strategy, which is applied here, is dividing the number of *samples per pixel* (`spp`) across all parallel processes.
For each pixel, `spp` number of light rays contribute to the final pixel.
For example, with `-spp=128` and $4$ MPI processes, each MPI process is responsible for $\frac{128}{4}=32$ samples for all pixels of the resulting picture.

Instead of, but also additionally, the parallelization can be achieved through $T$ threads with the `-threads=T` parameter.
Threads can share the same memory and therefore may have a lower memory footprint than multiple processes.


#### CUDA Build
The example application can also utilize Nvidia GPUs via CUDA.
In this case, the raytracing calculations are performed on the GPUs directory, which is an ideal environment for this type of calculations, provided, that the resolution and complexity is large enough.

CUDA support requires a separate build, which we will also run its own Slurm job.
In this case, it may be especially important to build on the target hardware, since your login nodes may not contain the accelerators we intend to use.

Let's prepare the build directory again on our login node:
```bash
# Assuming you are still in the SnowmanRaytracer source directory or the CPU build directory
cd ..
mkdir build_gpu && cd build_gpu
```

Additionally to the above dependencies, this build relies on CUDA and you may have to load the corresponding modules for your HPC system.
The application still runs with MPI, but mostly to manage multiple processes, e.g. one process for each GPU, if multiple are used.

Our build script (`build_snowman_cuda.sbatch`) may look like this:
```bash
#!/usr/bin/env bash
#SBATCH --job-name=bulid-and-test-Snowman
#SBATCH --nodes=1
#SBATCH --ntasks=2
#SBATCH --partition=gpus
#SBATCH --gpus=2

# Prepare your environment with the dependencies
# This will likely look different in your case!
module load 2025 GCC/13.2.0 OpenMPI/4.1.6 Boost/1.83.0 CMake/3.27.6 libpng/1.6.40 buildenv/default

# Assuming you are submitting from the "build_gpu" directory
cmake -DCMAKE_BUILD_TYPE=Release -DENABLE_CUDA=ON ../SnowmanRaytracer

# Building the software in parallel
cmake --build . --parallel

# First test run with 4 MPI processes
export CUDA_VISIBLE_DEVICES=0,1
mpirun -n 2 ./build/raytracer -width=800 -height=800 -spp=128 -threads=1 -png=snowman_gpu.png
```

::: instructor

# TODO: CUDA Modules missing?

::::::::::::::

## We are all set to learn about job efficiency!
With the example application in place, we are all set to learn about many factors affecting job performance.
We will repeatedly use this application in different configurations, so make sure to keep it in a central location that stays accessible throughout the course.

## Acknowledgements

Course created in context of [HPC.NRW](https://hpc.dh.nrw/).
