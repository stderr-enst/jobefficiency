---
title: "Resource Requirements"
teaching: 10
exercises: 0
---

:::::::::::::::::::::::::::::::::::::: questions 

- How many resources should it request initially?
- What options does the scheduler give to request resources?
- How do I know if they are used well?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

After completing this episode, participants should be able to …

- Request the right amount of resources from the scheduler.
- Identify the size of their jobs in relation to the HPC system.
- Change the parameters if the applications' resource requirements change.
- Perform a simple scaling study for a given application.

::::::::::::::::::::::::::::::::::::::::::::::::

## Starting Somewhere

Didactic path: I have no idea how many resources to ask for -> just guess and start with some combinations.
Next identify slower, or failed (OOM, timelimit) and choose the best
What does that say about efficiency?


## Requesting Resources

Slurm provides:
- Number of nodes
- Number of tasks/processes
- Number of CPUs per task/process
- Number of Threads per CPU
- Number of GPUs per task/process

Hints for CPU- and memory-bound

Mapping of application <-> job resources

Maybe discuss:

- Minimizing/maximizing involved number of nodes
- Different wait times for certain configurations
- What is a task / process
- Requesting memory, more than mem/core -> idle cores


## Compared to the HPC System

- Is my job large or small?
- How many resources are currently free?
- How long do I have to wait? (looking up scheduler estimate + apply common sense)


## Changing requirements

- How to change requested resources if application should run differently? (e.g. more processes)
- Considerations & estimates for
   - changing compute-time (more/less workload)
   - changing memory requirements (smaller/larger model)
   - changing number of processes / nodes
   - changing I/O -> more/less or larger/smaller files


## Scaling study

- Define example payload
   - Long enough to be significant
   - Short enough to be feasible for a quick study
- Identify dimension for scaling study, e.g.
   - number of processes (on a single node)
   - number of processes (across nodes)
   - number of nodes involved (network-communication boundary)
   - size of workload
   - Decide on number of processes across node, fixed workload size
- Take measurements
   - Use `time` and repeating measurements (something like 3 or 10)
   - Vary scaling parameter
- Analyze scaling behavior
   - Goal: best working point for job production
   - Plot data and see how it scales
   - Identify sweet-spots and points where behavior changes
- Conclude on best configuration for given setup


## Summary

:::::::::::::::::::::::::: challenge
## Exercise: Recollection

::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::: keypoints

- Estimate resource requirements and request them in terms the scheduler understands
- Don't over-/underuse resources
- Optimal time-to-solution by minimizing batch queue times and maximizing parallelism

::::::::::::::::::::::::::::::::::::::::::::::::
