---
title: "Introduction"
teaching: 10
exercises: 0
---

:::::::::::::::::::::::::::::::::::::: questions 

- Why should I care about my jobs performance?
- How is efficiency defined?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

After completing this episode, participants should be able to …

- Understand the benefits of efficient jobs.
- Use the `time` command for a first assessment.

::::::::::::::::::::::::::::::::::::::::::::::::

<!-- EPISODE CONTENT HERE -->

## Why Care About Performance?
Reasons from the perspective of learners (see profiles)

- Faster output, shorter iteration time
   - More research per time
- Potentially less wasted energy
   - Production of hardware and its operation costs energy (even when idle)
   - => Buy as little hardware as possible and use it as much as you can, if you have meaningful computations
- Applying for HPC resources in a larger center


## What is Efficient?

::::::::::::: challenge

Write down your current definition or understanding of efficiency with respect to HPC jobs. (Shared document?)

:::: hint
E.g. shortest time from submission to job completion.
:::::::::

:::: solution
Definitions of efficiency (to be ordered and discussed):

1. Minimal wall-/human-time of the job
2. Minimal compute-time
3. Minimal time-to-solution (like 1, including queue wait times)
4. Minimal cost in terms of energy/someones money
5. With regards to opportunity costs. Amount of research per job (including waiting times, computation time, slowdown through larger iteration cycles (turn around times))
:::::::::::::

:::::::::::::::::::::::


::::: discussion

Which definition of efficiency is most useful for us?

::::::::::::::::

## How Does Performance Relate to Hardware?
(Following this structure throughout the course, trying to understand the performance in these terms)

Basic/broad dimensions of performance:

- CPU (Front- and Backend, FLOPS)
- Memory (data hierarchy)
- I/O (broader data hierarchy: disk, network)
- Parallel timeline (synchronization, etc.)

## Setting the Baseline
Often relative improvements

:::::::::::::::::::::::::::::::::::::: keypoints
- Efficiency is efficient
   - Definitions: wall/human-time, compute-time, time-to-solution, energy (costs / environment), Money, opportunity cost (less research output)
- `time` and `time` again. Maybe also https://github.com/sharkdp/hyperfine ?
::::::::::::::::::::::::::::::::::::::::::::::::
