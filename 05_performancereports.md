---
title: "Performance Overview"
teaching: 10
exercises: 0
---

:::::::::::::::::::::::::::::::::::::: questions 

- Is it enough to look at a jobs walltime?
- What steps can I take to evaluate a jobs performance?
- What popular types of reports exist? (e.g. Roofline)

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

After completing this episode, participants should be able to …

- Explain different approaches to performance measurements.
- Understand common terms and concepts in performance analyses.
- Create a performance report through a third-party tool.
- Describe what a performance report is meant for (establish baseline, documentation of issues and improvements through optimization, publication of results, finding the next thread to pull in a quest for optimization)
- Measure the performance of central components of underlying hardware (CPU, Memory, I/O, ...) (split episode?)
- Identify which general areas of computer hardware may affect performance.

::::::::::::::::::::::::::::::::::::::::::::::::


:::::::::::::::::::::::::: instructor
## Intention: Introduce third party tools for performance reports

Narrative:

- Scaling study, scheduler tools, project proposal is written and handed in
- Maybe I can squeeze out more from my current system by trying to understand better how it behaves
- Another colleague told us about performance measurement tools
- We are learning more about our application
- Aha, there IS room to optimize! Compile with vectorization


What we're doing here:

- Get a complete picture
- Introduce missing metrics / definitions
- Relate to hardware on the same level of detail

:::::::::::::::::::::::::::::::::::::


## Workflow

- Previously checked scaling behavior by looking at walltime
- what if we would count other things while our job is running? Could be
   - CPU utilization
   - FLOPS
   - Memory uitilization
   - ...
- Two possible ways to look at this data with respect to time:
   1. *tracing*: over time
   2. *sampling*: accumulated results at the end
- Third-party tools to measure these things - you can use them with your jobs

:::::::::::::::::::::::::: instructor
## Pick a main tool

We go with three alternatives here, pick one an stick to it throughout your course, but highlight that there are alternatives and learners may not have access to certain tools on any cluster.

:::::::::::::::::::::::::::::::::::::


::: callout

Here you can choose between three alternative perspectives on our job:

1. [*ClusterCockpit*](https://clustercockpit.org/): A job monitoring service available on many of our clusters. Needs to be centrally maintained by your HPC administration team.
1. [*Linaro Forge Performance Reports*](https://docs.linaroforge.com/25.0.4/html/forge/performance_reports/index.html): A commercial application providing a single page performance overview of your job. Your cluster may have licenses available.
1. *TBD*: A free, open source tool/set of tools, to get a general performance overview of your job.

Performance counters and permissions, may require `--exclusive`, depends on system! Look at documentation / talk to your administrators / support.
```
cap_perfmon,cap_sys_ptrace,cap_syslog=ep
kernel.perf_event_paranoid
```

:::

Live coding:

- Set up the main tool. How do I access it? How can I use it with my job?
- Run snowman with 8 cores

::: group-tab

### ClusterCockpit

1. Setup: webpage & login. An conditions on when it is enabled in your particular cluster?
2. If always enabled: figure out jobid of previous 8-core job from Episode 4

### Performance Reports

1. (Check for licenses?)
2. Setup: load software modules
3. Submit job with `perf-report`

### TBD

N/A

:::

## General report

::: group-tab

### ClusterCockpit

1. Go to webpage
2. Navigate to the job
3. Discuss overall info on a broad level
   - Job meta data
   - Footprint
   - Roofline plot
   - Detailed plots
   - Tabled statistics

### Performance Reports

1. Identify result files (txt, html)
2. Look at txt with editor
3. Copy html to local computer & open it with browser
4. Discuss report on a broad level
   - Computing
   - Memory
   - Communication
   - ...

### TBD

N/A

:::


## How Does Performance Relate to Hardware?
:::::::::::::::::::::::::: instructor
## ToDo: Connect Hardware to Performance Measurements

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

- General reports show direction in which to continue
   - Specialized tools may be necessary to move on

:::::::::::::::::::::::::: challenge
## Exercise:
::::::::::::::::::::::::::::::::::::

Leading question: Connection to hardware is quite deep, why does it matter? -> Drill deeper, e.g. on NUMA & pinning

:::::::::::::::::::::::::::::::::::::: keypoints
- First things first, second things second, ...
- Profiling, tracing
- Sampling, summation
- Different HPC centers may provide different approaches to this workflow
- Performance reports offer more insight into the job and application behavior
::::::::::::::::::::::::::::::::::::::::::::::::
