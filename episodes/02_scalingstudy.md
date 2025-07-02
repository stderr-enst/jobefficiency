---
title: "Scaling Study"
teaching: 10
exercises: 0
---

:::::::::::::::::::::::::::::::::::::: questions 

- How can I decide the amount of resources I should request for my job?
- How do I know how my application behaves at different scales?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

After completing this episode, participants should be able to …

- Perform a simple scaling study for a given application.
- Identify good working points for the job configuration.

::::::::::::::::::::::::::::::::::::::::::::::::


## What do we look at?

- Amdahl's vs. Gustavsons's law / strong and weak scaling
- Walltime, Speedup, efficiency

:::::::::::::::::::::::::: discussion
## Discussion: What dimensions can we look at?

::::: solution

- CPUs
- Nodes
- Workload/problem size

::::::::::::::

:::::::::::::::::::::::::::::::::::::

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


## Parameter Scan

- Take measurements
   - Use `time` and repeating measurements (something like 3 or 10)
   - Vary scaling parameter

:::::::::::::::::::::::::: challenge
## Exercise: Run the Example with different -n

- 1, 2, 4, 8, 16, 32, ... cores and same workload
- Take `time` measurements (ideally multiple and with `--exclusive`)

::::::::::::::::::::::::::::::::::::


## Analyzing results

:::::::::::::::::::::::::: challenge
## Exercise: Plot the scaling

- Plot it against `time`
- Calculate speedup with respect to baseline with 1 core

::::::::::::::::::::::::::::::::::::

- What's a good working point? How 
- Overhead
- Efficiency: not wasting cores if adding them doesn't do much


## Summary

What's a good working point for our example (at a given workload)?

:::::::::::::::::::::::::: challenge
## Exercise:
::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::: keypoints

- Jobs behave differently with varying resources and workloads
- Scaling study is necessary to proof a certain behavior of the application
- Good working points defined by sections where more cores still provide sufficient speedup, but no costs due to overhead etc. occurs

::::::::::::::::::::::::::::::::::::::::::::::::
