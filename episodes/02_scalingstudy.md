---
title: "Scaling Study"
teaching: 10
exercises: 0
---

:::::::::::::::::::::::::::::::::::::: questions 

- A?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

After completing this episode, participants should be able to …

- Perform a simple scaling study for a given application.
- B?

::::::::::::::::::::::::::::::::::::::::::::::::

## Scaling study

:::::::::::::::::::::::::: instructor
## ToDo
Simple scaling study could become its own episode right after this one:

- Pick dimension (e.g. number of cores) (Amdahl's vs. Gustavson's Law?)
- Pick limits & stepsize
- Run scaling study
- Make a nice plot
- Discuss ideal scaling, difference to reality, overhead, what's optimal, time-to-solution with queue-wait-times

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
## Exercise:
::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::: keypoints

- A

::::::::::::::::::::::::::::::::::::::::::::::::
