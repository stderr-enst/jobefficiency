---
title: "Scheduler Tools"
teaching: 10
exercises: 0
---

:::::::::::::::::::::::::::::::::::::: questions 

- What information can the scheduler provide about my jobs performance?
- What's the meaning of the collected metrics?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

After completing this episode, participants should be able to …

- Explain basic performance metrics.
- Use tools provided by the scheduler to collect basic performance metrics of their jobs.

::::::::::::::::::::::::::::::::::::::::::::::::

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


## Summary

:::::::::::::::::::::::::: challenge
## Exercise:
::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::: keypoints
- `sacct` and `seff` for first results
- Small scaling study, maximum of X% overhead is "still good" (larger resource req. vs. speedup)
- Getting a feel for scale of the HPC system, e.g. "is 64 cores a lot?", how large is my job in comparison?
- CPU and Memory Utilization
- Core-h and relationship to power efficiency
::::::::::::::::::::::::::::::::::::::::::::::::
