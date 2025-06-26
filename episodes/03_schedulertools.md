---
title: "Scheduler Tools"
teaching: 10
exercises: 0
---

:::::::::::::::::::::::::::::::::::::: questions 

- What information can the scheduler provide about my jobs performance?
- What's the meaning of the collected metrics?
- How large is my HPC cluster?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

After completing this episode, participants should be able to …

- Explain basic performance metrics.
- Use tools provided by the scheduler to collect basic performance metrics of their jobs.

::::::::::::::::::::::::::::::::::::::::::::::::

## Scheduler Tools

<!-- EPISODE CONTENT HERE -->

## Shortcomings
- Not enough info about e.g. I/O, no timeline of metrics during job execution, ...
- => trying other tools! (motivation for subsequent episodes)

:::::::::::::::::::::::::::::::::::::: keypoints
- `sacct` and `seff` for first results
- Small scaling study, maximum of X% overhead is "still good" (larger resource req. vs. speedup)
- Getting a feel for scale of the HPC system, e.g. "is 64 cores a lot?", how large is my job in comparison?
- CPU and Memory Utilization
- Core-h and relationship to power efficiency
::::::::::::::::::::::::::::::::::::::::::::::::
