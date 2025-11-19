---
title: "Scaling Study"
teaching: 30
exercises: 40
---

:::::::::::::::::::::::::::::::::::::: questions 

- How many resources should we request for a given job?
- How does our application behave at different scales?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

After completing this episode, participants should be able to …

- Perform a simple scaling study for a given application.
- Identify good working points for the job configuration.

::::::::::::::::::::::::::::::::::::::::::::::::


:::::::::::::::::::::::::: instructor
## Intention: Introduce/Recollect concept of Speedup and do a simple scaling study

Narrative:

- We panic, maybe we need more resources to meet the deadline with our title picture!
- Requesting resources with bigger systems requires a project proposal with an estimate of the resource demand


What we're doing here:

- Vary number of cores
- Which metrics are most useful?
- Define speedup
- Visualize results

ToDo:

- Define example payload
   - Long enough to be significant
   - Short enough to be feasible for a quick study
- Identify dimension for scaling study, e.g.
   - number of processes (on a single node)
   - number of processes (across nodes)
   - number of nodes involved (network-communication boundary)
   - size of workload
   - Decide on number of processes across node, fixed workload size
- Choose limits (e.g. 1, 2, 4, ... cores), within reasonable size for given Cluster
- Beyond nodes? Set to one node?


:::::::::::::::::::::::::::::::::::::

Panic!
The deadline is approaching way too fast and we may not finish our project in time.
Maybe requesting more resources from our clusters scheduler does the trick?
How could we know if it helps and by how much?

## What is Scaling?
:::::::::::::::::::::::::: instructor
## Todo: show, don't tell

:::::::::::::::::::::::::::::::::::::

Answer: how much does it help to add more CPU cores, if I keep the problem sized fixed? (Translates to: samples per pixel, number of pixels, same scene)
Vary the number of CPU cores and look at behaviour with `time`, `seff`, `sacct`

- Prepare script to use `${SLURM_NTASKS}` in `mpirun -n ` and `time`.
- Live coding, executing on single node (-N 1) and motivate $2^N$:
   - Run with 1 core as baseline (can we reuse value from before?)
   - Run with 2 cores
   - Run with 4 cores
   - Run with 8 cores
   - Run with 16 cores
- Compare wall times, and some `seff`, `sacct` metrics
- Use grep to quickly compare executions qualitatively
- See need to compare it in a clearer way
- List times
- Introduce "strong scaling"
- Introduce Speedup
- Calculate Speedup for examples above
- Exercise to extend executions and gather all speedups
- Draw speedup vs. $N$
- Observe overhead \& compare to perfect scaling
- Introduce Amdahls Law

Answer: Why go in steps of $2^n$?
Often aligns well to available resources (number of cores etc.), also a bit of tradition

List times in csv file:

```csv
time,ntasks,size
909.241,2,1600
462.018,4,1600
245.603,8,1600
124.328,16,1600
```

**Strong scaling** is limited by parallelizable fraction.
At some point adding more cores does not help. => Amdahls law.
picture of serial and parallel fractions w.r.t Amdahls law.

The *Speedup* metric is often used to compare the execution with different amounts of resources.
Speedup answers the question

> How much faster is the application with $N$ parallel processes/threads, compared to the serial execution with $1$ process/threads?

It's defined through the comparison of wall times $T(p)$ of the application with $p$ parallel processes: $$S = \frac{T(1)}{T(N)}$$
Here, $T(1)$ is the wall time for a sequential execution, and $T(N)$ is the execution with $N$ parallel processes.

Plot results (Black board, prepared python script, piece of paper?)

Additionally, adding more CPU cores can actively slow down the process.
Each process introduces some communication overhead.
Between processes, and if it is distributed among multiple servers/worker nodes, also between processes through the network.


:::::::::::::::::::::::::: challenge
## Exercise: Continue scaling study

- Continue with 32, 64, and 128 cores
- Go beyond single node, use all cores of two nodes
- Take `time` measurements (maybe: ideally multiple and with `--exclusive`)
- Prepare a list of results in a csv with the format (what's ntasks, what's size?):

Questions:

- Can we "see" the network between both nodes?
- Is there a slow down already?
- What does it tell about the parallelization of the application?

Some points may be:

- How serial portion of the code effects the scaling?
- If we have a infinite number of workers or processes doing a highly parallel code which is 99% is parallelized but 1% is serial execution. The speedup will be 100. What is the ideal limit to speedup?
- How the communication affects the scaling?

::::::::::::::::::::::::::::::::::::


## Speedup and Efficiency
Different representation of the info above.
Answers: how much does it help to add more cores?

Define efficiency w.r.t speedup.

:::::::::::::::::::::::::: challenge
## Exercise: Speedup and Efficiency

Plot efficiency and make a guess when adding more cores is not worth it.

::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::: discussion
## Discussion: When should we stop adding CPU cores?

Discuss the previous (potentially subjective) results and decide on a good approach in the group.

Depends on X, Y, and Z.
Could be around #N cores.
It's an optimization problem!
::::::::::::::::::::::::::::


## If scaling is limited, why are there larger HPC systems?
What if the project benefits from increasing the problem size:

- More detail/resolution may lead to more accurate result.
- More repetitions may improve statistics

Scaling up the problem size, while also scaling up the compute resources (CPU cores) is called weak scaling.

=> Gustafsons Law


:::::::::::::::::::::::::: challenge
## Exercise: Weak scaling

- Run with 1, 2, 4, 8, 16, 32, 64 on single node
- Increase problem size at the same time by same fraction (e.g. 800x800 pixels on 1 core, 1131x1131 on 2 cores, 1600x1600 on 4, $\sqrt{640000 \times N}$ on $N$ cores)
- Take `time` measurements (maybe: ideally multiple and with `--exclusive`)

How does it scale now?
Would we benefit from increasing the problem size in this case?

::::: solution

Maybe not, if picture is good enough

::::::::::::::

::::::::::::::::::::::::::::::::::::


## Summary
What is scaling?
What's a good working point for our example (at a given workload)?

What's a good working point? When is a overhead setting in?
Not the same for all kinds of applications!

Requests for computational time (project proposals) may require scaling study.

Recollect scaling study: compared change of wall time with number of cores and computational workload.
We can look at any parameter this way and study the impact on walltime, CPU utilization, FLOPS, memory utilization, communication, output size on disk with respect to any parameter of our job or our application.

[Reproducible HPC workflows using JUBE](https://carpentries-incubator.github.io/hpc-workflows-jube/)

Leading question: `time` and scheduler tools still don't provide a complete picture, what other ways are there? -> Introduce third party tools to get a good performance overview

:::::::::::::::::::::::::::::::::::::: keypoints

- Jobs behave differently with varying resources and workloads
- Scaling study is necessary to check behavior of the application
- Good working points defined by sections where more cores still provide sufficient speedup, but no costs due to overhead etc. occurs
- Amdahl's law: speedup is limited by serial fraction of the program
- Gustafson's law: more resources still help, if larger workloads can be used

::::::::::::::::::::::::::::::::::::::::::::::::
