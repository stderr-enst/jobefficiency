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

A scheduler performs important tasks such as accepting and scheduling jobs,
monitoring job status, starting user applications, cleaning up jobs that
have finished or exceeded their allocated time. The scheduler also keeps
a history of jobs that have been run and how they behaved. The information
that is collected can be queried by the job owner to learn about how
the job utilized the resources it was given.

The `seff` command can be used to learn about how efficiently your job
has run. The `seff` command takes the job identifier as an argument
to select which job it displays information about. That means we need
to run a job first to get a job identifier we can query SLURM about.
Then we can ask about the efficiency of the job.

```bash
cat > render_snowman.sbatch << _EOF
#!/usr/bin/bash
#SBATCH --time=01:00:00
#SBATCH --nodes=1
#SBATCH --tasks-per-node=4
mpirun -np 4 snowman 800 3
_EOF
jobid=$(sbatch --parsable render_snowman.sbatch)
seff $jobid
```

```output
Job ID: 309489
Cluster: bigiron
User/Group: usr123/grp123
State: COMPLETED (exit code 0)
Nodes: 1
Cores per node: 4
CPU Utilized: 00:07:43
CPU Efficiency: 98.93% of 00:07:48 core-walltime
Job Wall-clock time: 00:01:57
Memory Utilized: 35.75 MB
Memory Efficiency: 0.20% of 17.58 GB (4.39 GB/core)
```

The job script we created asks for 4 CPUs for an hour. After submitting
the job script we need to wait until the job has finished as `seff` can
only report sensible statistics after the job is completed. The report
from `seff` shows basic statistics about the job, such as

- The resources the job was given
  * the number of nodes
  * the number of cores per node
  * the amount of memory per core
- The amount of resources used
  * `CPU Utilized` the aggregate CPU time (the time the job took times the number of CPUs allocated)
  * `CPU Efficiency` the actual CPU usage as a percentage of the total available CPU capacity
  * `Job Wall-clock time` the time the job took from start to finish
  * `Memory Utilized` the aggregate memory usage
  * `Memory Efficiency` the actual memory usage as a percentage of the total avaialable memory

Clearly this job took a lot less time
than the one hour we asked for. This can be problematic as the scheduler looks
for time windows when it can fit a job in. Long running jobs cannot be squeezed
in as easily as short running jobs. As a result, jobs that request a long time
to complete typically have to wait longer before they can be started. Therefore
asking for more than 10 times as much time as the job really needs, simply
means that you will have to wait longer for the job to start. On the other hand
you do not want to ask for too little time. Few things are more annoying than
waiting for a long running calculation to finish, just to see the job being
killed right before the end because it would have needed a couple of minutes more
than you asked for. So the best approach is to ask for more time than
the job needs, but not go overboard here. As the job elapse time depends on
many machine conditions, including congestion in the data communication, disk
access, operating system jitter, and so on, you might want to ask for a
substantial buffer. Nevertheless, asking for more than twice as much time as
job is expected to need is clearly senseless.

Another thing is that SLURM by default reserves a certain amount of memory per
core. In this case the actual memory usage is just a fraction of that amount.
We could reduce the memory allocation by explicitly asking for less.

```bash
cat > render_snowman.sbatch << _EOF
#!/usr/bin/bash
#SBATCH --time=01:00:00
#SBATCH --nodes=1
#SBATCH --tasks-per-node=4
#SBATCH --mem=100MB
mpirun -np 4 snowman 800 3
_EOF
jobid=$(sbatch --parsable render_snowman.sbatch)
seff $jobid
```

```output
Job ID: 310002
Cluster: bigiron
User/Group: usr123/grp123
State: COMPLETED (exit code 0)
Nodes: 1
Cores per node: 4
CPU Utilized: 00:07:43
CPU Efficiency: 98.09% of 00:07:52 core-walltime
Job Wall-clock time: 00:01:58
Memory Utilized: 50.35 MB
Memory Efficiency: 50.35% of 100.00 MB (100.00 MB/node)
```

Now we see that a much larger fraction of the allocated memory has been
used. Normally you would not worry too much about the memory request. Lately
HPC clusters are used more for machine learning work loads which tend to require
a lot of memory. Their memory requirements per core might actually be so large
that they cannot use all the cores in a node. So there may be spare cores
available for jobs that need little memory. In such a scenario tightening the
memory allocation up could allow the scheduler to start your job early. How
much milage you might get from this depends on the job mix at the HPC site where
you run your calculations.

Note that the CPU utilization is reported as almost 100%, but this just means
that the CPU was busy with your job 100% of the time. It does not mean that this
time was well spent. For example, every parallel program has some serial parts
to the code.
Typically those parts are executed redundantly on all cores, which is wasteful
but not reflected in the CPU efficiency. Also, this number does not reflect
how well the capabilities of the CPU are used. If your CPU offers vector
instructions, for example, but your code does not use them then your code will
just run slow. The CPU efficiency will still show that the CPU was busy 100% of
the time even though the program is just running at a fraction of the speed
it could achieve if it fully exploited the hardware capabilities. It is worth
keeping these limitations of `seff` in mind.

The `seff` command cannot give you any information about the I/O performance of
your job. You have to use the `sacct` command for that.

The `sacct` command shows data stored in the job accounting database. You can
query the data of any of your previously run jobs. Just like with `seff` you
will need to provide the job ID to query the accounting database. Rather than
keeping track of all your jobs yourself you can ask `sacct` to provide you
with an overview of the jobs you have run.

```bash
sacct
```

```output
JobID           JobName  Partition    Account  AllocCPUS      State ExitCode
------------ ---------- ---------- ---------- ---------- ---------- --------
309902       render_sn+  STD-s-96h  project_a          4  COMPLETED      0:0
309902.batch      batch             project_a          4  COMPLETED      0:0
309902.exte+     extern             project_a          4  COMPLETED      0:0
309903       render_sn+  STD-s-96h  project_a          4  COMPLETED      0:0
309903.batch      batch             project_a          4  COMPLETED      0:0
309903.exte+     extern             project_a          4  COMPLETED      0:0
310002       render_sn+  STD-s-96h  project_a          4  COMPLETED      0:0
310002.batch      batch             project_a          4  COMPLETED      0:0
310002.exte+     extern             project_a          4  COMPLETED      0:0
```

Note that by default `sacct` only lists the jobs that have been run today. You
can use the `--starttime` option to list all jobs that have been run since
the given start date. For example, try running

```bash
sacct --starttime=2025-09-25
```

```output
JobID           JobName  Partition    Account  AllocCPUS      State ExitCode
------------ ---------- ---------- ---------- ---------- ---------- --------
308755       snowman.s+  STD-s-96h  project_a         16  COMPLETED      0:0
308755.batch      batch             project_a         16  COMPLETED      0:0
308755.exte+     extern             project_a         16  COMPLETED      0:0
308756       snowman.s+  STD-s-96h  project_a          4  COMPLETED      0:0
308756.batch      batch             project_a          4  COMPLETED      0:0
308756.exte+     extern             project_a          4  COMPLETED      0:0
309486       interacti+  STD-s-96h  project_a          4     FAILED      1:0
309486.exte+     extern             project_a          4  COMPLETED      0:0
309486.0          prted             project_a          4  COMPLETED      0:0
309489       render_sn+  STD-s-96h  project_a          4  COMPLETED      0:0
309489.batch      batch             project_a          4  COMPLETED      0:0
309489.exte+     extern             project_a          4  COMPLETED      0:0
309902       render_sn+  STD-s-96h  project_a          4  COMPLETED      0:0
309902.batch      batch             project_a          4  COMPLETED      0:0
309902.exte+     extern             project_a          4  COMPLETED      0:0
309903       render_sn+  STD-s-96h  project_a          4  COMPLETED      0:0
309903.batch      batch             project_a          4  COMPLETED      0:0
309903.exte+     extern             project_a          4  COMPLETED      0:0
310002       render_sn+  STD-s-96h  project_a          4  COMPLETED      0:0
310002.batch      batch             project_a          4  COMPLETED      0:0
310002.exte+     extern             project_a          4  COMPLETED      0:0
```

With the job ID you can ask `sacct` for information about a specific job
as in

```bash
sacct --jobs=310002
```

```output
JobID           JobName  Partition    Account  AllocCPUS      State ExitCode
------------ ---------- ---------- ---------- ---------- ---------- --------
310002       render_sn+  STD-s-96h  project_a          4  COMPLETED      0:0
310002.batch      batch             project_a          4  COMPLETED      0:0
310002.exte+     extern             project_a          4  COMPLETED      0:0
```

Clearly this does not provide any information beyond what we already could
extract. To get more specific data we need to explicitly ask for the information
we want. As SLURM collects a broad range of data about every job it is worth
to evaluate what the most relevant items are.

- `MaxRSS`, `AveRSS`, or the Maximum or Average Resident Size Set (RSS). The
  RSS is the memory allocated by a program that is actually resident in the
  main memory of the computer. If the computer gets low on memory then the
  virtual memory manager can extend the apparently available memory by moving
  some of the data from memory to disk. This is done entirely transparently to the
  application, but the data that has been moved to disk is no longer resident
  in main memory. As a result accessing it will be slower because it needs to
  retrieved from disk first. Therefore if the RSS is small compared to the
  total amount of memory the program uses this might affect the performance
  of the program.
- `MaxPages`, `AvePages`, or the Maximum or Average number of Page Faults.
  These quantities are related to the Resident Size Sets. When the program
  tries to access data that is not resident in main memory this triggers a
  page fault.
  The virtual memory manager responds to a page fault by retrieving the accessed
  data from disk (and potentially migrating other data to disk to make
  space). These operations are typically costly. Therefore high numbers of
  page faults typically correspond to a significant reduction in the program's
  performance. For example, the CPU utilization might drop from as high as 98%
  to as low as 2% due to page faults. For that reason some HPC machines are
  configured to kill your job if the application generates a high rate of
  page faults.
- `AllocCPUS` is the number of CPUs allocated for the job.
- `Elapsed` is the amount of wall clock time it took to complete the job. I.e.
  the amount of time that passed between the start and finish of the job.
- `MaxDiskRead`, the Maximum amount of data read from disk.
- `MaxDiskWrite`, the Maximum amount of data written to disk.
- `ConsumedEnergy`, the amount of energy consumed by the job if that information  was collected. If that data is not collected the energy consumption will be
  reported as 0.
- `AveCPUFreq`, the average CPU frequency of all tasks in a job. In general the
  higher the clock frequency of the processor the faster the calculation runs.
  The exception is if the application is memory bandwidth limited and the data
  cannot be moved to processor fast enough to keep it busy. In that case
  modern hardware might throttle the frequency. This saves energy as the power
  consumption scales linearly with the clock frequency.

We can explicitly select the data elements that we are interested in. To select
all of the above variables run

```bash
sacct --jobs=310002 --format=MaxRSS,AveRSS,MaxPages,AvePages,AllocCPUS,Elapsed,MaxDiskRead,MaxDiskWrite,ConsumedEnergy,AveCPUFreq
```

```output
    MaxRSS     AveRSS MaxPages   AvePages  AllocCPUS    Elapsed  MaxDiskRead MaxDiskWrite ConsumedEnergy AveCPUFreq
---------- ---------- -------- ---------- ---------- ---------- ------------ ------------ -------------- ----------
                                                   4   00:01:58                                        0
    51556K     51556K      132        132          4   00:01:58        6.91M        0.72M              0         3M
         0          0        0          0          4   00:01:58        0.01M        0.00M              0         3M
```

While `sacct` provides a lot of information it is clearly incomplete. For
example, the information is for the entire calculation. Variations in the
metrics as a function of time throughout the job are not available.
Communication between different MPI processes is not recorded. The collection
of the energy consumption depends on the hardware and system configuration
at the HPC center and might not be available. So while we might be able to
glean some indications for different types of performance problems, for a
proper analysis more detailed information is needed.


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
