---
title: "Resource Requirements"
teaching: 10
exercises: 0
---

:::::::::::::::::::::::::::::::::::::: questions 

- How many resources should it request initially?
- What options does the scheduler give to request resources?
- How do I know if they are used well?
- How large is my HPC cluster?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

After completing this episode, participants should be able to …

- Identify the size of their jobs in relation to the HPC system.
- Request a good amount of resources from the scheduler.
- Change the parameters to see how the execution time changes.

::::::::::::::::::::::::::::::::::::::::::::::::


:::::::::::::::::::::::::: instructor
## Objective: Vary one parameter and compare to baseline

- Learn about Slurm job parameters
- Develop intuition about job size with respect to the cluster
- First impression of whats a "good" amount of resources to request for a job

:::::::::::::::::::::::::::::::::::::

## Starting Somewhere

Didactic path: I have no idea how many resources to ask for -> just guess and start with some combinations.
Next identify slower, or failed (OOM, timelimit) and choose the best
What does that say about efficiency?

:::::::::::::::::::::::::: challenge
## Exercise: Starting Somewhere

- Run job with a timelimit of 1 minute -> Trigger timelimit. What's a good timelimit for our task?
- Run job with few cores, but too much memory/core -> Trigger OOM. What's a good memory limit for our task?
- Run job with requesting way too many cores -> Endless waiting or not accepted due to account limits. What's a good CPU limit for our task?
- `squeue` to learn about scheduling issues / reasons

::::::::::::::::::::::::::::::::::::

Summarize dimensions in which a job has to be sized correctly (time, cores, memory, gpus, ...).


## Compared to the HPC System

- What's the relationship between your job and existing hardware of the system?
   - What hardware does your HPC system offer?
   - Documentation and Slurm commands
- Is my job large or small?
   - What's considered large, medium, small? Maybe as percentage of whole system?
   - Issues of large jobs: long waiting times
   - Issues of many (thousands) small jobs: 
- How many resources are currently free?
- How long do I have to wait? (looking up scheduler estimate + apply common sense)

:::::::::::::::::::::::::: challenge
## Exercise: Comparing to the system

- `sinfo` to learn about partitions and free resources
- `scontrol` to learn about nodes in those partitions
- `lscpu` and `cat /proc/cpuinfo`
- Submit a job with a reasonable number of resources and use `squeue` and/or `scontrol show job` to learn about Slurms estimated start time

Answer questions about number and type of CPUs, HT/SMT, memory/core, timelimits.

Summarize with a well sized job that's a good start for the example.

::::::::::::::::::::::::::::::::::::


## Requesting Resources
:::::::::::::::::::::::::: instructor
## ToDo
This section is just an info dump, how do we make it useful and approachable?
What's a useful exercise?
Maybe put info here in other sections?
:::::::::::::::::::::::::::::::::::::

More detail about what [Slurm provides](https://slurm.schedmd.com/sbatch.html) (among others):

- `-t, --time=<time>`: Time limit of the job
- `-N, --nodes`: Number of nodes
- `-n, --ntasks`: Number of tasks/processes
- `-c, --cpus-per-task`: Number of CPUs per task/process
- `--threads-per-core=<threads>`: Select nodes with at least the number threads per CPU
- `--mem=<size>[units]`: Memory, but can also be as `--mem-per-cpu`, ...
- `-G, --gpus`: Number of GPUs
- `--exclusive`


Maybe discuss:

- Minimizing/maximizing involved number of nodes
   - Shared nodes: longer waiting times until a whole node is empty
   - Min/max number of nodes min/maximizes communication
- Different wait times for certain configurations
   - Few tasks on many shared nodes might schedule faster than many tasks on few exclusive nodes.
- What is a task / process -- Difference?
- Requesting memory, more than mem/core -> idle cores


## Changing requirements

- Motivate why requirements might change (resolution in simulation, more data, more complex model, ...)
- How to change requested resources if application should run differently? (e.g. more processes)
- Considerations & estimates for
   - changing compute-time (more/less workload)
   - changing memory requirements (smaller/larger model)
   - changing number of processes / nodes
   - changing I/O -> more/less or larger/smaller files

:::::::::::::::::::::::::: challenge
## Exercise: Changing requirements

- Walk through how to estimate increase in CPU cores / memory, etc.
- Run previous job with larger workload
- Check if and how it behaves differently than the smaller job

::::::::::::::::::::::::::::::::::::


## Summary

:::::::::::::::::::::::::: discussion
## Discussion: Recollection

Circle back to efficiency.
What's considered good/efficient in context of job requirements and parameters?

:::::::::::::::::::::::::::::::::::::

Leading question: `time` doesn't give much information, is there an easy way to get more? -> See what Slurm tools can tell about our previous jobs

:::::::::::::::::::::::::::::::::::::: keypoints

- Estimate resource requirements and request them in terms the scheduler understands
- Be aware of your job in relation to the whole system (available hardware, size)
- Aim for a good match between requested and utilized resources
- Optimal time-to-solution by minimizing batch queue times and maximizing parallelism

::::::::::::::::::::::::::::::::::::::::::::::::
