---
title: "Pinning"
teaching: 10
exercises: 0
---

:::::::::::::::::::::::::::::::::::::: questions 

- What is "pinning" of job resources?
- How can pinning improve the performance?
- How can I see, if pinning resources would help?
- What requirement hints can I give to the scheduler?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

After completing this episode, participants should be able to …

- Define the concept of "pinning" and how it can affect job performance.
- Name Slurms options for memory- and cpu- binding.
- Use hints to tell Slurm how to optimize their job allocation.

::::::::::::::::::::::::::::::::::::::::::::::::


:::::::::::::::::::::::::: instructor
## Intention: Go deeper in performance and hardware relationship

Narrative:

- We get the feeling, that hardware has a lot to offer, but the rabbit hole is deep!
- What are the "dimensions" in which we can optimize the throughput of snowman pictures per hour?
- Can we improve how the work maps to certain CPUs / Memory regions?


What we're doing here:

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


## Motivation
:::::::::::::::::::::::::: challenge
## Exercise 
Case 1: 1 thread per rank
`mpirun -n 8 ./raytracer -width=512 -height=512 -spp=128 -threads=1 -alloc_mode=3 -png=snowman.png`

Case 2: 2 thread per rank
`mpirun -n 8 ./raytracer -width=512 -height=512 -spp=128 -threads=2 -alloc_mode=3 -png=snowman.png`

Questions:
- Do you notice any difference in runtime between the two cases?
- Is the increase in threads providing a speedup as expected?

::::: solution
- Observation: The computation times are almost the same.
- Expected behavior: Increasing threads should ideally reduce runtime.
- Hypothesis: Additional threads do not contribute.
::::::::::::::
::::::::::::::::::::::::::::::::::::

## How to investigate?

You can verify the actual core usage in two ways:
1. Use `--report-bindings` with `mpirun`
2. Use `htop`command on the compute node

:::::::::::::::::::::::::: instructor
## Note: Login to the compute job

This is cluster specific. It can possibly be  done in two ways:
1. `srun --pty --overlap --jobid=<jobid> /bin/bash`
2. Check on which node job runs and login to the node via SSH
:::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::: challenge
## Exercise
Follow any one of the option above and run for 2 threads per rank
`mpirun -n 8 ./raytracer -width=512 -height=512 -spp=128 -threads=2 -alloc_mode=3 -png=snowman.png`

Questions:
- Did you find any justification for the hypothesis we made?

::::: solution
Only 8 cores are active instead of 16
::::::::::::::
::::::::::::::::::::::::::::::::::::

Explanation:

- Eventhough we requested 2 threads per MPI rank, both threads are pinned to the same core.
- The second thread waits for the first thread to finish, so no actual thread-level parallelization is achieved.

:::::::::::::::::::::::::: instructor
## TODO: Show an animation
- current behavior with overlapping threads on the same core.
- Expected behavior when threads are pinned to separate cores.
:::::::::::::::::::::::::::::::::::::

## How to achieve?
## Exercise: Understanding Process and Thread Binding

In this exercise we will explore how MPI process and thread binding works. We will try binding to **core**, **socket**, and **numa**, and observe timings and bindings.

:::::::::::::::::::::::::: instructor
## Note
- This exercise assumes the following hardware setup:  
  - Dual-socket system (2 sockets, 48 cores per socket, 8 NUMA regions, 96 cores total).  
  - Each MPI process can use multiple threads (`-threads`) for parallel execution.
- The idea is to **demonstrate oversubscription** by giving more MPI processes than available sockets or NUMA regions, or by over-allocating threads per domain.  
- You are free to adjust `-n` and `-threads` based on your cluster.
:::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::: challenge
## Exercise
Case 1: `--bind-to numa`
`mpirun -n 8 --bind-to numa ./raytracer -width=512 -height=512 -spp=128 -threads=12 -alloc_mode=3 -png=snowman.png`

Case 2: `--bind-to socket`
`mpirun -n 4 --bind to socket /raytracer -width=512 -height=512 -spp=128 -threads=48 -alloc_mode=3 -png=snowman.png`

Questions:
- What is difference between Case 1 and Case 2. Any difference in performance? How many workers?
- How could you adjust process/thread counts to better utilize the hardware in Case 2?

::::: solution
- MPI and thread pinning is hardware-aware.
- If the number of processes matches the number of domains (socket or NUMA), then the number of threads should equal the cores per domain to fully utilize the node.
- No speedup in Case 2: Oversubscription occurs because we requested 4 processes on a system with only 2 sockets.
- Threads compete for the same cores → OpenMPI queues threads and waits until other processes finish.
::::::::::::::
::::::::::::::::::::::::::::::::::::

#Best Practices for MPI Process and Thread Binding

We will use `--bind-to core` (the smallest hardware unit) and `--map-by` to distribute MPI processes across sockets or NUMA or node regions efficiently.

- Best practice: Always bind processes to the smallest unit (core) and spread processes evenly across the available hardware using `--map-by`.
- Example options:
  - `--bind-to core` → binds each process to a dedicated core (avoids oversubscription).  
  - `--map-by socket:PE=<threads>` → spreads given number of threads as a processing element across the socket
  - `--map-by numa:PE=<threads>` → spreads processes across NUMA domains, assigning `<threads>` cores per process.
  - similarly `--map-by numa:PE=<threads>`

:::::::::::::::::::::::::: challenge
## Exercise
Use the given best practices above for  `-n 8` and `-threads=4`  and answer following questions

Questions:
- Did you get more workers than you requested?

::::: solution
No.
::::::::::::::
::::::::::::::::::::::::::::::::::::  


## Summary

:::::::::::::::::::::::::::::::::::::: key Takeaways
- **Always check how pinning works**  
  Use verbose reporting (e.g., `--report-bindings`) to see how MPI processes and threads are mapped to cores and sockets.  

- **Documentation is your friend**  
  For OpenMPI with `mpirun`, consult the manual: [https://www.open-mpi.org/doc/v4.1/man1/mpirun.1.php](https://www.open-mpi.org/doc/v4.1/man1/mpirun.1.php)  

- **Know your hardware**  
  Understanding the number of sockets, cores per socket, and NUMA regions on your cluster helps you make effective binding decisions.  

- **Avoid oversubscription**  
  Assigning more threads or processes than available cores hurts performance — it causes contention and idle waits.  

- **Recommended practice for OpenMPI**  
  Use `--bind-to core` along with `--map-by` to control placement and threads per process to maximize throughput.
::::::::::::::::::::::::::::::::::::::::::::::::
