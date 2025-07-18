---
title: "Pinning"
teaching: 10
exercises: 0
---

:::::::::::::::::::::::::::::::::::::: questions 

- Why what how?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

After completing this episode, participants should be able to …

- A

::::::::::::::::::::::::::::::::::::::::::::::::


:::::::::::::::::::::::::: instructor
## Intention: Go deeper in performance and hardware relationship

- Introduce pinning and slurm hint options
- Relate to hardware effects
- Use third party performance tools to observe effects!

:::::::::::::::::::::::::::::::::::::


:::::::::::::::::::::::::: instructor
## ToDo: Extract episode about pinning

Stick to simple options here.
Put more complex options for pinning / hints, etc. into its own episode somewhere later in the course

Pinning is an important part of job optimization, but requires some knowledge, e.g. about the hardware hierarchies in a cluster, NUMA, etc.
So it should be done after we've introduced different performance reports and their perspective on hardware

Maybe point to [JSC pinning simulator](https://apps.fz-juelich.de/jsc/llview/pinning) and have similar diagrams as an independent "offline" version in this course

:::::::::::::::::::::::::::::::::::::

Binding / pinning:

- `--mem-bind=[{quiet|verbose},]<type>`
- `-m, --distribution={*|block|cyclic|arbitrary|plane=<size>}[:{*|block|cyclic|fcyclic}[:{*|block|cyclic|fcyclic}]][,{Pack|NoPack}]`
- `--hint=`: Hints for CPU- (`compute_bound`) and memory-bound (`memory_bound`), but also `multithread`, `nomultithread`
- `--cpu-bind=[{quiet|verbose},]<type>` (`srun`)
- Mapping of application <-> job resources


## Why what how?
B
<!-- EPISODE CONTENT HERE -->


## Summary

:::::::::::::::::::::::::: challenge
## Exercise:
::::::::::::::::::::::::::::::::::::

Leading question: Pinning is very specific, but was it really limiting the performance of out application? How can I identify the biggest issue?

:::::::::::::::::::::::::::::::::::::: keypoints
- C
::::::::::::::::::::::::::::::::::::::::::::::::
