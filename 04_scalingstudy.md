---
title: "Scaling Study"
teaching: 30
exercises: 40
---

:::::::::::::::::::::::::::::::::::::: questions 

- How many resources should be requested for a given job?
- How does our application behave at different scales?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

After completing this episode, participants should be able to …

- Perform a scaling study for a given application.
- Notice different perspectives on scaling parameters.
- Identify good working points for the job configuration.

::::::::::::::::::::::::::::::::::::::::::::::::


:::::::::::::::::::::::::: instructor
# Intention: Introduce/Recollect concept of Speedup and do a simple scaling study

Narrative:

- We panic, maybe we need more resources to meet the deadline with our title picture!
- Requesting resources with bigger systems requires a project proposal with an estimate of the resource demand

:::::::::::::::::::::::::::::::::::::


The deadline is approaching way too fast and we may not finish our project in time.
Maybe requesting more resources from our clusters scheduler does the trick?
How could we know if it helps and by how much?

## What is Scaling?
The execution time of parallel applications changes with the number of parallel processes or threads.
In a *scaling study* we measure how much the execution time changes by scanning a reasonable range of number of processes.
In a common phrasing, this approach answers how the execution time *scales* with the number of parallel processors.

Starting from the job script `render_snowman.sbatch`:

```bash
#!/usr/bin/bash
#SBATCH --time=01:00:00
#SBATCH --nodes=1
#SBATCH --mem=2GB

module load 2025 GCC/13.2.0 OpenMPI/4.1.6 buildenv/default Boost/1.83.0 CMake/3.27.6 libpng/1.6.40
time mpirun -n ${SLURM_NTASKS} ./build/raytracer -width=1024 -height=1024 -spp=256 -png "$(date +%Y-%m-%d_%H%M%S).png"
```

we can manually run such a scaling study by submitting multiple jobs.
Here we use the environment variable `${SLURM_NTASKS}`, which is set by Slurm to the number of tasks during job submission.
Let's start some measurements:

```output
$ sbatch --ntasks 1 render_snowman.sbatch
Submitted batch job 15462593
$ sbatch --ntasks 2 render_snowman.sbatch
Submitted batch job 15462596
$ sbatch --ntasks 4 render_snowman.sbatch
Submitted batch job 15462597
$ sbatch --ntasks 8 render_snowman.sbatch
Submitted batch job 15462598
```

Now we have to wait until all four jobs are finished.


::: callout
# Regular update of `squeue`
You can use `squeue --me -i 30` to get an update of all of your jobs every 30 seconds.

If you don't need a more regular update, it is good practice to keep the interval on the order of 30s to a couple of minutes.
This will ease the load on the Slurm servers in situations where potentially hundreds of users access the information for many thousand jobs at the same time.
:::


Once the jobs are finished, we can use `grep` to get the wall clock time of all four jobs:

```output
$ grep "real" slurm-1546259*.out
slurm-15462593.out:real	6m52.719s
slurm-15462596.out:real	3m24.359s
slurm-15462597.out:real	1m41.754s
slurm-15462598.out:real	0m51.534s
```

The real-, wallclock-, time is decreasing significantly each time we double the number of Slurm tasks.
From this, we feel that doubling the number of CPU cores really is a winning strategy!

::: instructor
## Slurm Reservation and specific Hardware?

You may need to reserve a set of resources for the course, such that enough resources for the following exercises are available.
This is especially important for `--exclusive` access.

In that case, show how to use `--reservation=reservationname` to submit jobs.

It may be a good idea to point out the particular hardware of your cluster / partition to emphasize how many cores are available on a single node and when the scaling study goes beyond a single node.
:::

:::::::::::::::::::::::::: challenge
# Exercise: Continue scaling study to larger values

Run the same scaling study and continue it for even larger number of `--ntasks`, e.g. 16, 32, 64, 128.
So far, we have been using `--nodes=1` to stay on a single node. At which point are your MPI processes distributed across more than one node? Use Slurm commandline tools to find out the how many CPU cores -- in our case equivalent to the number of one-core MPI processes -- are available on a single node. You may have to increase the number of nodes `--nodes`, if you want to go beyond that limitation.

Gather your `real` time results and place them in a `.csv` file. Here is an example for our previous measurements:

```csv
ntasks,time
1,412.719
2,204.359
4,101.754
8,51.534
...
```

How much does each doubling of the CPU resources help with running the parallel raytracer?
You can also use `seff` to answer that question!

COMPARE `real` TIME WITH JOB WALLCLOCK TIMES! DECIDE WHAT TO GO WITH!

::: hint

You can use `sinfo` to find out the node names of your particular Slurm partition.
Then use `scontrol` to show all details about a single node from that partition.
It will show you the number of CPU (cores) available on that node.

:::

::: solution

PUT RESULTING TIMES HERE!

Adding more resources does not help indefinitely.
At some point the overhead of managing the calculation in separate tasks outweighs the benefit of parallel calculation.
There is too little to do in each tasks and the overhead starts to dominate.

With `seff` and `grep` we can see a lower CPU efficiency with each doubling of the number of tasks:

```output
$ for jobid in 15462593 15462596 15462597 15462598; do seff $jobid&& echo "" ; done | grep "CPU Efficiency"
CPU Efficiency: 97.85% of 00:06:59 core-walltime
CPU Efficiency: 95.73% of 00:07:02 core-walltime
CPU Efficiency: 91.90% of 00:07:12 core-walltime
CPU Efficiency: 84.27% of 00:07:44 core-walltime
```

So at some point adding more CPU cores will not help us.

:::

::::::::::::::::::::::::::::::::::::

::: instructor
## Todo: show, don't tell

info dump below in this section 

Maybe be more specific about which overheads and how we can see them?

:::


Adding more CPU cores can actively slow down the calculation after a certain point.
The optimal point is different for each application and each configuration.
It depends on the ratio between calculations, communications and various management overheads in the whole process of running everything.


::: callout
# Overheads and Reliable Measurements

Many overheads and when they show also depend on the underlying hardware.
So the sweet spot may very well be different for different clusters, even if the application and configuration stays the same!

Another common issue lies within our measurements themself.
We perform a single time measurement on a worker node that is possibly shared with other jobs at the same time.
What if another user runs an application that hogs shared resources like the local disk or network interface card?
In this case our measurements become somewhat non-deterministic.
Running the same measurement twice may result in significantly different values.
If you need reliable results, e.g. for a publication, requesting exclusive access to Slurms resources through the `sbatch` flag `--exclusive` is the best approach.
As a drawback, this typically results in longer waiting times, since whole nodes have to be reserved for the measurement jobs, even if not all resources are used.

Even on exclusive resources, the measurements cannot be 100% reliable.
For example, the scheduling behavior of the Linux kernel, or access to remote resources like the parallel file system or data from the web, are still affecting your measurements in unpredictable ways.
Therefore, the best results are achieved by taking the mean and standard deviations of repeated measurements for the same configuration.
The measured minimum also has strong informative value, since it proofs the best observed behavior.
:::


A scaling study can in fact be done with respect to different application parameters and circumstances.
For example, what is the execution time when we change the *workload*, e.g. a larger number of pixels, samples per pixels, or a more complex scene?
How much does a communication overhead change, if we change the number of involved nodes while keeping the workload and number of tasks fixed, i.e. changing the *network communication surface*?
Different scaling studies like these can help to identify pressure points that affect the applications performance.

Scaling studies typically occur in a *preparation phase* where the application is evaluated with a representative example workload.
Once a good configuration is found, we know the application is running close to an optimal performance and larger number of calculations can start, often called the *production phase*.

In a similar vein, scaling studies can be a formal requirement for compute time applications on larger HPC systems.
On these systems and for larger calculation campaigns it is more crucial to run efficient calculations, since the resources are typically more contested and the potential energy- and carbon footprint becomes much larger.


## Speedup, Efficiency, and Strong Scaling
To quantitatively and empirically study the scaling behavior of a given application,
it is common to look at the *speedup* and *efficiency*
*Speedup* is a metric to compare the execution times with different amounts of resources.
It answers the question

> How much faster is the application with $N$ parallel processes (threads), compared to the serial execution with $1$ process (threads)?

It is defined by the comparison of wall times $T(N)$ of the application with $N$ parallel processes: $$S(N) = \frac{T(1)}{T(N)}$$
Here, $T(1)$ is the wall time for a sequential execution, and $T(N)$ is the execution with $N$ parallel processes.

*Efficiency* in this context is defined as $$\eta(N) = \frac{S(N)}{N}$$ with speedup $S(N)$ and describes by how much additional parallel processes, $N$, deviate from the theoretical linear optimum.


::: challenge
## Exercise: Calculate Speedup and Efficency

RUN WITH WALL-CLOCK TIMES & CPU EFFICIENCY INSTEAD!

```csv
ntasks,time,speedup,efficiency
1,412.719,1.00,1.00
2,204.359,2.02,1.01
4,101.754,4.06,1.015
8,51.534,8.01,1.00125
...
```
:::::::::::::


So far, we kept the workload size fixed to $1024 \times 1024$ pixels and $256$ samples per pixel for the same scene with three snowmen.
The diminishing returns for adding more and more parallel processors leads to a famous observation.
The speedup of a program through parallelization is limited by the execution time of the serial fraction that is not parallelizable.
No application is 100% parallelizable, so adding an arbitrary amount of parallel processors can only affect the parallelizable section.
In the best case, the execution time gets reduced to the serial fraction of the application.

An application is said to *scale strongly*, if adding more cores significantly reduces the execution time.

INSERT PICTURES OF AMDAHLS FRACTIONS HERE


::: callout
# Amdahls Law^[G. M. Amdahl, ‘Validity of the single processor approach to achieving large scale computing capabilities’, in Proceedings of the April 18-20, 1967, spring joint computer conference, in AFIPS ’67 (Spring). New York, NY, USA: Association for Computing Machinery, Apr. 1967, pp. 483–485. doi: 10.1145/1465482.1465560.]
The speedup of a program through parallelization is limited by the execution time of the serial fraction that is not parallelizable.
Speedup $S$, with $N$ processors, $s$ the time for the serial fraction, and $p$, the time for parallel fraction:
$$S(N) = \frac{s+p}{s+\frac{p}{N}} = \frac{1}{s + \frac{p}{N}} \Rightarrow \lim_{N\rightarrow \infty}  S(N) = \frac{1}{s}$$
:::::::::::


:::::::::::::::::::::::::: challenge
# Exercise: Speedup and Efficiency

Plot results (Black board, prepared python script, piece of paper?)
Draw speedup & efficiency vs. $N$
Plot efficiency and make a guess when adding more cores is not worth it.

::::::::::::::::::::::::::::::::::::


:::::::::::::::::::::::::: discussion
# Discussion: When should we stop adding CPU cores?

Discuss the previous (potentially subjective) results and decide on a good approach in the group.

Depends on X, Y, and Z.
Could be around #N cores.
It's an optimization problem!
::::::::::::::::::::::::::::


## If scaling is limited, why are there larger HPC systems?
We observed that for a fixed problem size adding more parallel processors can only help up to a certain point.
But what if the project benefits from increasing the workload size?
Does a higher resolution, more accuracy, or more statistics, etc., improve our insights and results?
If that is the case, the perspective on the issue changes and adding more parallel processors can become more feasible as well.
For our raytracer example, increasing the workload corresponds to more pixels, more samples per pixel, and/or a more complex scene.

*Weak scaling* refers to the scaling behavior of an application for a fixed workload per parallel processing unit, e.g. increasing the number of pixels by the same amount as the number of parallel processors $N$.

::: callout
# Gustafsons Law^[J. L. Gustafson, ‘Reevaluating Amdahl’s law’, Commun. ACM, vol. 31, no. 5, pp. 532–533, May 1988, doi: 10.1145/42411.42415.]
A program scales on $N$ parallel processors, if the problem size also scales with the number of processors.
The speedup $S$ becomes
$$\text{S(N)} = \frac{s+pN}{s+p} = s+pN = N+s(1-N)$$
with $N$ processors, $s$ the time for the serial fraction, and $p$, the time for parallel fraction:
:::::::::::


To scale the workload of the snowmen raytracer, we can multiply the number of parallel MPI processes, `${SLURM_NTASKS}`, with the intended samples per pixel (`-spp=256`).
For a single process, the whole $1024 \times 1024$ pixel picture is calculated in a single MPI process with 256 per pixel.
Running with two MPI processes, both have to calculate half the number of pixels, but twice the amount of samples per pixel.

```bash
#!/usr/bin/bash
#SBATCH --time=01:00:00
#SBATCH --nodes=1
#SBATCH --mem=2GB

module load 2025 GCC/13.2.0 OpenMPI/4.1.6 buildenv/default Boost/1.83.0 CMake/3.27.6 libpng/1.6.40

SPP="$[${SLURM_NTASKS}*256]"

time mpirun -n ${SLURM_NTASKS} ./build/raytracer -width=1024 -height=1024 -spp=${SPP} -png "$(date +%Y-%m-%d_%H%M%S).png"
```

Increasing the samples per pixels can be a qualitative improvement to the final result, since more effort is put into the calculation of the raytraced picture.
In practice, there is a cutoff, beyond which no reasonable improvement is to be expected.
This is a question about accuracy, error margins, and overall quality, which can only be answered in the specific context of each research project.
If there is no real improvement by increasing the workload, running a weakly scaling application is really just wasting valuable computational time and energy.


:::::::::::::::::::::::::: challenge
# Exercise: Weak scaling

Repeat the previous scaling study and increase the samples per pixel accordingly to study the raytracers weak scaling behavior.

- Run with 1, 2, 4, 8, 16, 32, 64 MPI processes on single node
- Increase the samples per pixel by the same fraction
- Take `time` measurements and consider running with `--exclusive` to ensure more reliable results.
- Create a `.csv` file and run the plotting script 

UPDATE CSV AND PLOTTING

```csv
ntasks,spp,time,speedup,efficiency
1,256,
2,512,
4,1024,
...
```

How well does the application scale with an increasing workload size?
Do you see a qualitative difference in the resulting `.png` files and is the increased sample-per-pixel size worth the computational costs?

::: solution
INCLUDE CSV AND PLOTTED PICTURE
COMPARE SPP PNGS HERE, IS IT WORTH IT?
:::
::::::::::::::::::::::::::::::::::::


## Summary
In this episode, we have seen that we can study the *scaling* behavior of our application with respect to different metrics, while varying its configuration.
Most commonly, we study the execution time of an application with an increasing number of parallel processors.
In such a scaling study, we collect comparable walltime measurements for an increasing number of Slurm tasks of a parallelizable and representative job.
If a good working point is found, larger scale "production" jobs can be submitted to the HPC system.

If the application has good *strong scaling* behavior, adding more cores leads to an effective improvement in execution time.
We observe diminishing returns of adding more cores to a fixed-size problem, so there is an optimal number of parallel processors for a given application configuration. (Amdahls Law)

If increasing the workload size leads to better results, because of improved accuracy and quality, that can be used in the project at hand, we can study the *weak scaling* behavior and increase the workload size by the same factor of increasing parallel processors.

A good working point depends on the availability of resources, specifics of the underlying hardware, the particular application, and a particular configuration for the application.
For that reason, scaling studies are a common requirement for formal compute time applications to prove an efficient execution of a given application.

We can study the impact of any parameter on metrics like, for example, walltime, CPU utilization, FLOPS, memory utilization, communication, output size on disk, and so on.

::: spoiler
# Automating Scaling Studies
If you find yourself repeating similar measurements over and over again, you may be interested in an automation approach. This can be done by creating [reproducible HPC workflows using JUBE](https://carpentries-incubator.github.io/hpc-workflows-jube/), among other things.
:::

Up to now, we were still working with basic metrics like the wall-clock time.
In the next episode, we start with more in-depth measurements of many other aspects of our job and application.

:::::::::::::::::::::::::::::::::::::: keypoints

- Jobs behave differently with increasing parallel resources and fixed or scaling workloads
- Scaling studies can help to quantitatively grasp this changing behavior 
- Good working points are defined by configurations where more cores still provide sufficient speedup or improve quality through increasing workloads
- Amdahl's law: speedup is limited by the serial fraction of a program
- Gustafson's law: more resources for parallel processing still help, if larger workloads can meaningfully contribute to project results

::::::::::::::::::::::::::::::::::::::::::::::::
