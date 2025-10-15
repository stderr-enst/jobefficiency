---
title: "Scaling Study"
teaching: 60
exercises: 3
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
- Colleague told us that this can be answered with a scaling study
- What is it? How could we do one?


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

Answer: how much does it help to add more CPU cores, if I keep the problem sized fixed. (Translates to: samples per pixel, number of pixels, same scene)
Vary the number of CPU cores and look at behaviour with `time`, `seff`, `sacct`

=> Strong scaling

Strong scaling is limited by parallelizable fraction.
At some point adding more cores does not help. => Amdahls Law.

Additionally, adding more CPU cores can actively slow down the process.
Each process introduces some communication overhead.
Between processes, and if it is distributed among multiple servers/worker nodes, also between processes through the network.

Answer: Why go in steps of $2^n$?

Live coding, executing on single node (-N 1):

- Run with 1 core as baseline (can we reuse value from before?)
- Run with 2 cores
- Run with 4 cores
- Run with 8 cores

Plot results (Black board, prepared python script, piece of paper?)

:::::::::::::::::::::::::: challenge
## Exercise: Continue scaling study

- Run with 16 cores and increase in steps of $2^N$
- Go beyond single node, use all cores of two nodes
- Take `time` measurements (maybe: ideally multiple and with `--exclusive`)

=> Can we "see" the network between both nodes?
=> Is there a slow down already?
=> What does it tell about the parallelization of the application?

Some points may be:
- How serial portion of the code effects the scaling? (May be a numerical would help)
- If we have a infinite number of workers or processes doing a highly parallel code which is 99% is parallelized but 1% is serial execution. The speedup will be 100. What is a ideal limit to the speedup.
- How the communication effects the scaling?

::::::::::::::::::::::::::::::::::::


## Speedup and Efficiency
Different representation of the info above.
Answers: how much does it help to add more cores?

Define speedup & efficiency.
Plot the same thing

:::::::::::::::::::::::::: challenge
## Exercise: Speedup and Efficiency
Text

::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::: discussion
## Discussion: When should we stop adding CPU cores?

::::: solution

Depends on XYZ.
Could be around X cores

::::::::::::::
::::::::::::::::::::::::::::


## If scaling is limited, why are there large HPC Systems at all?
What if the project benefits from increasing the problem size:

- More detail/resolution may lead to more accurate result.
- More repetitions may improve statistics

Scaling up the problem size, while also scaling up the compute resources (CPU cores) is called weak scaling.

=> Gustavsons Law


:::::::::::::::::::::::::: challenge
## Exercise: Weak scaling

- Run with 1, 2, 4, 8, 16, 32, 64 on single node
- Increase problem size at the same time by same fraction
- Take `time` measurements (maybe: ideally multiple and with `--exclusive`)

=> How does it scale now?
=> Would we benefit from increasing the problem size in this case?

::::: solution

Maybe not, if picture is good enough

::::::::::::::

::::::::::::::::::::::::::::::::::::


## Parameter Scans
We can look at different parameters this way.

How does the memory consumption scale vs. CPU cores or problem size?
How does the disk usage scale vs. CPU cores or problem size


## Summary
What is scaling?
What's a good working point for our example (at a given workload)?

What's a good working point? When is a overhead setting in?
Not the same for all kinds of applications!

Project proposals require scaling study.
:::::::::::::::::::::::::: instructor
## ToDo
Note on compute time application that need estimate of required compute resources and touch on scaling behavior here?
Could be important for one type of learner, if this is given in a context like HPC.NRW.
Optional for many others, but maybe interesting.
:::::::::::::::::::::::::::::::::::::

Leading question: `time` and scheduler tools still don't provide a complete picture, what other ways are there? -> Introduce third party tools to get a good performance overview

:::::::::::::::::::::::::::::::::::::: keypoints

- Jobs behave differently with varying resources and workloads
- Scaling study is necessary to check behavior of the application
- Good working points defined by sections where more cores still provide sufficient speedup, but no costs due to overhead etc. occurs

::::::::::::::::::::::::::::::::::::::::::::::::
