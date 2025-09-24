---
title: "Scheduler Tools"
teaching: 10
exercises: 0
---

:::::::::::::::::::::::::::::::::::::: questions 

- What can the scheduler tell about job performance?
- What's the meaning of collected metrics?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

After completing this episode, participants should be able to …

- Explain basic performance metrics.
- Use tools provided by the scheduler to collect basic performance metrics of their jobs.

::::::::::::::::::::::::::::::::::::::::::::::::


:::::::::::::::::::::::::: instructor
## Intention: Introduce more basic performance metrics

Narrative:
- Okay, so first couple of jobs ran, but were they "quick enough"?
- How many renders could I generate per minute/hour/day according to the current utilization
- Our cluster uses certain hardware, maybe we didn't use it as much as we could have?
- But I couldn't see all metrics (may be cluster dependent) (Energy, Disk I/O, Network I/O?)

What we're doing here:
- What `seff` and `sacct` have to offer
- Introduce simple relation to hardware, what does RSS, CPU, Disk read/write and their utilization mean?
- Point out what's missing from a complete picture

:::::::::::::::::::::::::::::::::::::


## Scheduler Tools

- `sacct`
   - `MaxRSS`, `AvgRSS`
   - `MaxPages`, `AvgPages`
   - `AvgCPU`, `AllocCPUS`
   - `ElapsedI
   - `MaxDiskRead`, AvgDiskRead`,
   - `MaxDiskWrite`, `AvgDiskWrite`
   - `energy`
- `seff`
   - Utilization of time allocation
   - Utilization of allocated CPUs (is 100% <=> efficient? Not if calculations are redundant etc.!)
   - Utilization of allocated memory



## Shortcomings
- Not enough info about e.g. I/O, no timeline of metrics during job execution, ...
   - I/O may be available, but likely only for local disks
   - => no parallel FS
   - => no network
- Energy demand may be missing or wrong
   - Depends on available features
   - Doesn't estimate energy for network switches, cooling, etc.
- => trying other tools! (motivation for subsequent episodes)


:::::::::::::::::::::::::: instructor
## ToDo
Can / should we cover I/O and energy metrics at this point?

E.g. use something like `beegfs-ctl` to get a rough estimate of parallel FS performance.
Use pidstat etc. to get numbers on node-local I/O (and much more)
:::::::::::::::::::::::::::::::::::::


## Summary

:::::::::::::::::::::::::: challenge
## Exercise:
::::::::::::::::::::::::::::::::::::

Leading question: Is there a systematic approach to study a jobs performance at different scales? -> Scaling study

:::::::::::::::::::::::::::::::::::::: keypoints
- `sacct` and `seff` for first results
- Small scaling study, maximum of X% overhead is "still good" (larger resource req. vs. speedup)
- Getting a feel for scale of the HPC system, e.g. "is 64 cores a lot?", how large is my job in comparison?
- CPU and Memory Utilization
- Core-h and relationship to power efficiency
::::::::::::::::::::::::::::::::::::::::::::::::
