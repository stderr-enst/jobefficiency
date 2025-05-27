---
title: "Introduction"
teaching: 10
exercises: 0
---

:::::::::::::::::::::::::::::::::::::: questions 

- Why should I care about my jobs performance?
- How is efficiency defined?
- How many resources should it request initially?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- Understand the benefits of efficient jobs.
- Use the `time` command for a first assessment.

::::::::::::::::::::::::::::::::::::::::::::::::

<!-- EPISODE CONTENT HERE -->

## Motivation

## Efficiency
Definitions of efficiency (to be ordered and discussed):

1. Minimal wall-/human-time of the job
2. Minimal compute-time
3. Minimal time-to-solution (like 1, including queue wait times)
4. Minimal cost in terms of energy/someones money
5. With regards to opportunity costs. Amount of research per job (including waiting times, computation time, slowdown through larger iteration cycles (turn around times))

## Performance in Relation to Hardware
(Following this structure throughout the course, trying to understand the performance in these terms)

Basic/broad dimensions of performance:
- CPU (Front- and Backend, FLOPS)
- Memory (data hierarchy)
- I/O (broader data hierarchy: disk, network)
- Parallel timeline (synchronization, etc.)

## Baseline

:::::::::::::::::::::::::::::::::::::: keypoints
- Efficiency is efficient
   - Definitions: wall/human-time, compute-time, time-to-solution, energy (costs / environment), Money, opportunity cost (less research output)
- `time` and `time` again. Maybe also https://github.com/sharkdp/hyperfine ?
::::::::::::::::::::::::::::::::::::::::::::::::
