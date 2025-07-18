---
title: "Introduction"
teaching: 10
exercises: 0
---

:::::::::::::::::::::::::::::::::::::: questions 

- Why should I care about my jobs performance?
- How is efficiency defined?
- How do I start measuring?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

After completing this episode, participants should be able to …

- Use the `time` command for a first measurement.
- Understand the benefits of efficient jobs.
- Roughly estimate a job energy consumption based on core-h.
- Identify which general areas of computer hardware may affect performance.

::::::::::::::::::::::::::::::::::::::::::::::::



:::::::::::::::::::::::::: instructor
## ToDo: Rewrite to start with the narrative from the very beginning

- Needs a specific example job.
- Gradual improvement would be great
- Start with baseline measurement at the very beginning?
   - `time` can raise questions of efficiency, "what's good?", etc.

:::::::::::::::::::::::::::::::::::::


## Setting the Baseline
Absolute performance is hard to determine:

- In comparison to current hardware (theoretical limits vs. real usage)
- Still important, if long way from theoretical limits
- Always limited by something (one optimization just shifts to the next saturated bottleneck)

During optimization, performance is often expressed in relative terms to a baseline measurement.
Define "baseline".
Comparison between before and after a change.

:::::::::::::::::::::::::: challenge
## Exercise: Baseline Measurement with `time`

Simple measurement with `time` of example application.
Maybe also with [hyperfine](https://github.com/sharkdp/hyperfine) ?

Observe system, user, and wall time.

Repeat measurements somewhere 3-10 times to reduce noise

- Average time
- Minimum (observed best case)

Maybe make a simple/obvious change to compare change to baseline.
How much relative improvement?

::::: solution
Example of how to run it and what the result looks like
::::::::::::::
::::::::::::::::::::::::::::::::::::

Discuss meaning of system, user, wall-time.
Relate to efficiencies (minimal wall-time vs. minimal compute-time)

## Why Care About Performance?

Reasons from the perspective of learners (see profiles)

- Faster output, shorter iteration-/turn-around-time
   - More research per time
   - Opportunity costs when "accepting" worse performance
   - Trade of between time spent on optimizing vs. doing actual research
- Potentially less wasted energy
   - Core-h / device-h directly correlate to wattage
   - Production of hardware and its operation costs energy (even when idle)
   - => Buy as little hardware as possible and use it as much as you can, if you have meaningful computations
- Applying for HPC resources in a larger center
   - Need estimate for expected resources
   - Jobs need to be sufficiently efficient
   - Is provided hardware a good fit for the applied computational workload?

:::::::::::::::::::::::::: challenge
## Exercise: Why care about performance?

maybe true-false statements as warmup exercise? E.g. something like

- Better performance allows for more research
- Application performance matters less on new computer hardware
- Computations directory correlate to energy consumption
- Good performance does not matter on my own hardware

**All statements should be connected to the example job & narrative!**

:::::: solution
- True, shorter turn around times, more results per time, more nobel prices per second!
- False, new hardware might make performance issues less pressing, but it is still important (opportunity costs, wasted energy, shared resources)
- True, device-hours consume energy (variable depending on utilized features, amount of communication, etc.), but there is a direct correlation to W
- False, performance is especially important  on shared systems, but energy and opportunity costs also affect researchers on their own hardware and exclusive allocations.
:::::::::::::::
::::::::::::::::::::::::::::::::::::


## Core-h and Energy

Define core-h.
Device usage for X seconds correlates to estimated power draw.
Real power usage depends on:

- Utilized features of the device (some more power-hungry than others)
- Amount of data movement through memory, data, network
- Cooling (rule of thumb factor $\times 2$)

Looking at energy is one perspective on "efficiency".

:::::::::::::::::::::::::: challenge
## Exercise: Core-h and Energy consumption

- Figure out your current hardware (docu, cpuinfo, websearch, LLM)
- Calculate core-h for above test (either including or excluding repetitions)
- Estimate power usage with TDP
- Keep it simple, back of the envelope calculations

::::: solution
Example for an existing cluster.
Stick to CPU TDP, maybe rough number for whole node from somewhere, multiply factor 2 for cooling, mention not-covered network and storage infrastructure
::::::::::::::
::::::::::::::::::::::::::::::::::::



## What is Efficient?

::::::::::::: challenge
## Challenge: Many perspectives on Efficiency
Write down your current definition or understanding of efficiency with respect to HPC jobs. (Shared document?)

(Exercise as think, pair, share?)

:::: hint
E.g. shortest time from submission to job completion.
:::::::::

:::: solution
Many definitions of efficiency (see below)
:::::::::::::
:::::::::::::::::::::::

::::: discussion
## Discussion: Which definition should we take?

Are these perspectives equally useful?
Is one particularly suited to our discussion?
::::::::::::::::

Many definitions of efficiency (to be ordered and discussed):

1. Minimal wall-/human-time of the job
2. Minimal compute-time
3. Minimal time-to-solution (like 1, including queue wait times, potentially multiple jobs for combined results)
4. Minimal cost in terms of energy/someones money
5. With regards to opportunity costs. Amount of research per job (including waiting times, computation time, slowdown through larger iteration cycles (turn around times))

Assuming only "useful" computations, no redundancies.

Which definition do refer to by default in the following episodes? (Do we need a default?)


## How Does Performance Relate to Hardware?
:::::::::::::::::::::::::: instructor
## ToDo: Find good place for hardware discussion

Introduce hardware on the same level of detail and with the same terms as the performance reports by ClusterCockpit, LinaroForge, etc., as soon as they appear.
Only introduce what we need, to avoid info dump.
But point to additional information that gives a complete overview -> hpc-wiki!

:::::::::::::::::::::::::::::::::::::

(Following this structure throughout the course, trying to understand the performance in these terms)

Broad dimensions of performance:

- CPU (Front- and Backend, FLOPS)
   - Frontend: decoding instructions, branch prediction, pipeline
   - Backend: getting data from memory, cache hierarchy & alignment
   - Raw calculations
   - Vectorization
   - Out-of-order execution
- Accelerators (e.g. GPUs)
   - More calculations
   - Offloading
   - Memory & communication models
- Memory (data hierarchy)
   - Working memory, reading data from/to disk
   - Bandwidth of data
- I/O (broader data hierarchy: disk, network)
   - Stored data
   - Local disk (caching)
   - Parallel fs (cluster-wide)
   - MPI-Communiction
- Parallel timeline (synchronization, etc.)
   - Application logic


::::: instructor
## ToDo: Clarify relation to hardware in this course
Maybe we should either focus on components (CPUs, memory, disk, accelerators, network cards) or functional entities (compute, data hierarchy, bandwidth, latency, parallel timelines)

We shouldn't go into too much detail here.
Define broad categories where performance can be good or bad. (calculations, data transfers, application logic, research objective (is the calculation meaningful?))

Reuse categories in the same order and fashion throughout the course, i.e. point out in what area a discovered inefficiency occurs.

Introduce detail about hardware later where it is needed, e.g. NUMA for pinning and hints.
::::::::::::::::

![Hardware](fig/JobEfficiency.drawio.png)

:::::::::::::::::::::::::: challenge
## Exercise: Match application behavior to hardware

Which part of the computer hardware may become an issue for the following application patterns:

1. Calculating matrix multiplications
2. Reading data from processes on other computers
3. Calling many different functions from many equally likely if/else branches
4. Writing very large files (TB)
5. Comparing many different strings if they match
6. Constructing a large simulation model
7. Reading thousands of small files for each iteration

Maybe not the best questions, also missing something for accelerators.

:::: solution
1. CPU (FLOPS) and/or Parallel timeline
2. I/O (network)
3. CPU (Front-End)
4. I/O (disk)
5. (?) CPU-Backend, getting strings through the cache?
6. Memory (size)
7. I/O (disk)
:::::::::::::
::::::::::::::::::::::::::::::::::::


## Summary

:::::::::::::::::::::::::: challenge
## Exercise: Recollecting efficiency
Exercise to raise the question if example workload is efficient or not.
Do we know yet? -> No, we can only tell how long it takes, estimate how much time/resources it consumes, and if there is a relative improvement on a change
::::::::::::::::::::::::::::::::::::


:::::::::::::::::::::::::::::::::::::: keypoints

- Absolute vs. relative performance measurements
   - `time` to establish a baseline
   - Estimating energy consumption
- Job performance affects you as a user
- Core-h and very rough energy estimate
- Different perspectives on efficiency
   - Definitions: wall/human-time, compute-time, time-to-solution, energy (costs / environment), Money, opportunity cost (less research output)
- Relationship between performance and computer hardware

::::::::::::::::::::::::::::::::::::::::::::::::
