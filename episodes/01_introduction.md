---
title: "Introduction"
teaching: 10
exercises: 0
---

:::::::::::::::::::::::::::::::::::::: questions 

- What exactly is job efficiency in the computing world?
- Why would I care about job efficiency and what are potential pitfalls?
- How can I start measuring how my program performs?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

After completing this episode, participants should be able to …

- Use timing commands provided by `time`, `date` and the Bash-shell.
- Understand the benefits of efficient jobs in terms of runtime and numerical accuracy.
- Have developed some awareness about the overall high energy consumption of HPC.

::::::::::::::::::::::::::::::::::::::::::::::::

<!--
Prerequisite: Bash https://swcarpentry.github.io/shell-novice/05-loop.html
-->

:::::::::::::::::::::::::: instructor
## Intention: Step into the narrative

Set up narrative:

- Important upcoming conference presentation
- Time is ticking, the deadline is approaching way too fast
- The talk is almost done, but, critically, we're missing a picture for the title slide
- It should contain three snowmen, and we've exhausted our credits for all generative AI models in previous chats with colleagues
- => Ray tracing a scene to the rescue!
- Issue: we need to try many different iterations of the scene to find the exact right picture. How can we maximise the number of raytraced snowman images before our conference deadline?
- Ray tracing is expensive, but luckily we have access to an HPC system


What we're doing here:

- Run workflow example for the first time
- Simple `time` measurement to get started
- Introduce different perspectives on efficiency
- Core-h and correlation to cost in energy/money
- Either set up the first Slurm job here or in the next episode

:::::::::::::::::::::::::::::::::::::

<!---the ratio of the useful work performed by a machine or in a process to the total energy expended or heat taken in
-->

## Background
Job efficieny, as defined by Oxford’s English Dictionaries, is *the ratio of the useful work performed by a machine [...] to the total energy expended or heat taken in*. In a high-performance-computing (HPC) context, the useful work
is the entirety of all calculations to be performed by our (heat-generating) computers. Doing this eficiently thus translates to maximizing the calculations completed in some limited time span while minimizing the heat output. In more extreme words, we want to avoid running big computers for *nothing but hot air*.

One may object that a single user's job may hardly have an effect on an HPC system's power usage since such systems are in power-on state 24/7 anyway.
The same may be argued about air travel. The plane will take off
anyway, whether I board the plane or not. However, we indeed have some leverage in contributing to efficiency, defined by fuel consumption in air travel: traveling lightly, i.e., avoiding excessive baggage will improve the airplane's ratio $\frac{useful\;work}{total\;energy\;expended}$. 
So let's get back to the ground and look at some inefficiencies in computing jobs, 
while we will continue to use the air-travel analogy.

### `time` to `sleep`
Let's look at the command`sleep`
```bash
sleep 2
```
This command triggers a "computer nap". It actually delays whatever would come next
for the specified time, here 2 seconds.
You can verify that nap time using a stopwatch, the latter given by the`time`command:
```bash
time sleep 2
```
which will report something like
```
real	0m2.002s
user	0m0.001s
sys	0m0.000s
```
The `time` command shall be our first performance-measuring tool. `time`has become a bit
of a *hello-world* equivalent in HPC contexts.
This command gives you a breakdown of how your program uses CPU (Central Processing Unit)
and wall-clock time. 
The standard output of `time` reports three fields, *real*, *user* and *sys*:

+------+-------------------------------------------------------------------------------+
| Time | Meaning                                                                       |
+------+-------------------------------------------------------------------------------+
| real | Wall-clock time = total runtime as seen on a stopwatch                        | 
+------+-------------------------------------------------------------------------------+
| user | Time spent in *user*-mode: actual computations like math, loops, logic        | 
+------+-------------------------------------------------------------------------------+
| sys  | Time spent in OS's *kernel*-mode (system calls): I/O = reading/writing files, |
|      | handling memory, talking to other devices                                     |
+------+-------------------------------------------------------------------------------+

The above`sleep`command abstains from any kind of math, I/O, or other work that
would show up in *user* or *sys* time, hence these entries show (almost) zero.

:::::::::::::::::::::::::: callout
The`time`commmand is both a keyword directly built into the Bash shell
as well as an executable file, usually residing under`/usr/bin/time`. While very similar, they are
not exactly the same. Shell/Bash keywords take
precedence, so preceding a command with`time`invokes the shell keyword. Therefore, if you want
to force the usage of`/usr/bin/time`, you would do
```bash
/usr/bin/time sleep 2
```
Further note, that shell keyword documentation is invoked via`help <KEYWORD>`, for example
`help time`, while most executables have manual pages, e.g.,`man time`.
At last, you can prefix the shell keyword with a backslash in order to stop Bash from evaluating it,
so`\time sleep 2`will revert to`/usr/bin/time`.
::::::::::::::::::::::::::

### Give me a second: `SECONDS`
Bash offers another stopwatch. Try this:
```bash
SECONDS=0 && sleep 2 && echo $SECONDS
```
Here, we initiate the Bash-internal variable`SECONDS`to zero, then take a 2-second nap, and
finally print out the number of elapsed seconds. Note that`&&`in between the commands is
a way of creating a command sequence.

:::::::::::::::::::::::::: callout
While useful,`SECONDS`is a stopwatch without the ability of counting tenths of seconds,
that is,
```bash
SECONDS=0 && sleep 1.6 && echo $SECONDS
```
will also report 2 seconds. Therefore, this method is less useful when a more
accurate timing is required.
::::::::::::::::::::::::::

### Let's have a `date`
The`date`command, as its manpage (`man date`) says, prints or sets the system date and time.
In fact, this gives us a super accurate stopwatch when used like this:
```bash
date +%s.%N
```
reports a point in time as a number of seconds elapsed since a fixed reference point.
Such a referenced time point is also referred to as Epoch time, where,
according to the manpage, the (default) reference point is the
beginning of the year 1970 (*1970-01-01 00:00 UTC*).

While`%s`invokes Epoch time output, the additional specifier`%N`enforces an 
accuracy down to nanoseconds. Give it a try and you will see a large number (of seconds)
followed by 9 digits after the decimal point.

::::::::::::: challenge
### An accurate stopwatch: `date`
You can use the construct`date +%s.%N`on the command line or in a Bash script 
to save some starting time point as a variable:
```bash
start=$(date +%s.%N)
```
This gives you a stopwatch by setting a start time, running some **command(s)**, and
then storing the end (Epoch) time after **command(s)** into a second variable.
Differencing the two Epoch times produces the elapsed time. 
Give this a try with the`sleep`command in between.

:::: hint
Differencing two numbers (as produced above via`date`) can be done, among other ways, like this:
```bash
echo "$end - $start" | bc -l
```
which uses the`bc`calculator tool.
::::

:::: solution
```bash
start=$(date +%s.%N) && sleep 2 && end=$(date +%s.%N) && echo "$end - $start" | bc -l
```
::::

:::::::::::::

::::::::::::: keypoints
- Different methods exist for timing our compute jobs or portions thereof.
- `time`is useful if only one command, which could of course be given as a lenghty script,
is to be timed from the command line: `time ./mylongscript.bash`.
- Using the builtin Bash variable`SECONDS` is useful for timing command
sequences within Bash scripts, when an accuracy of seconds suffices. Note that
`SECONDS=0` resets this internal timer, in case you want to time different
parts of a longer script.
- Differencing two Epoch-time measurements set via`date +%s.%N` provides
another accurate timing method.
:::::::::::::

## Part 1: Example for an inefficient job
After warming up with some timing methods, let's analyze the efficiency of a
small script that makes our computer sweat a bit more than the`sleep`command.
Have a look at the following Bash shell 7-liner.
```bash
#!/bin/bash
sum=0
for i in $(seq 1 1000); do
  val=`echo "e(1.5 * l(${i}))" | bc -l`
  sum=$(echo "$sum + $val" | bc -l)
done
echo Sum=$sum runtime=$SECONDS seconds
```
Copy-paste this to a file, say`sum.bash`, and make it executable via
```bash
chmod u+x sum.bash
```
The main part of this shell script consists of a`for`statement which
runs a loop over 1000 iterations; note that`seq 1 1000`creates the number 
sequence ($i=1,2,3,...,1000$). Inside the `for`loop the`bc`calculator tool is employed.
The first statement inside the loop (`val=...`) prints the expression
`e(1.5 * l(${i}))`, which is `bc`-talk for the expression
$i^{1.5}$ because of the relation $i^x=e^{x\cdot \ln(i)}$, for example
$e^{1.5\cdot\ln(3)}=3^{1.5}$, where ln is the natural logarithm.
The second statement inside the loop (`sum=...`) accumulates the expressions `val=`$i^{1.5}$ into`sum`,
so the output of the final`echo`line is the total, $\sum_{i=1}^{1000}i^{1.5}$.

::::::::::::: challenge
### Identify the inefficient pieces
In the above Bash script, the`for`loop invokes the `bc`calculator twice during
every loop iteration. Compared to another method to be investigated below, this 
method is rather slow. Any idea why that is the case?

:::: hint
Each statement`echo … | bc -l`spawns a new`bc`process via a subshell.
::::

:::: solution
The statement`echo … | bc -l`spawns a new`bc`process via a subshell. Here, each loop
iteration invokes two of those. Each subshell
is essentially a separate process and involves a certain startup cost, parsing overhead, and OS-internal inter‑process communication. Such overhead will account for most of the
total runtime of`sum.bash`.
::::

:::::::::::::

The overhead in this shell script is dominated by process creation and context switching,
that is, calling the`bc`tool so many times.
Going back to our air-travel analogy, the summation of 1000 numbers shall be
equivalent to having a total of 1000 passengers board a large plane.
When total boarding time counts, an inefficient boarding procedure would involve
every passenger loading two carryon pieces. Many of you may have experienced,
how stuffing an excessive number of baggage pieces into the overhead compartments
can slow things down in the plane's aisles,
similar to the overhead due to the 2000 (two for each loop iteration)`bc`sub-processes
that hinder the data stream inside the CPU's "aisles".

::::::::::::: challenge
### Let's pull out our stopwatches
You may have noticed the runtime output using`SECONDS`in the last line of`sum.bash`,
providing a rather crude runtime measurement. By the way, an initialization
`SECONDS=0` is not needed as it happens (once) internally at script invocation.
Using one of the other timers introduced above, can you get a more accurate runtime
measurement?

:::: hint
You can precede any command with`time`. If you want to use`date`,
remember that`start=$(date +%s.%N)`lets you store the (Epoch) time and`&&`lets 
you join commands together.
::::

:::: solution
A straightforward way is
```bash
time ./sum.bash
```
Alternatively,`date`and`&&`can be combined to a wrapper in order to time`sum.bash`externally,
```bash
start=$(date +%s.%N) && ./sum.bash && end=$(date +%s.%N) && echo "$end - $start" | bc -l
```
Another option is to place`date`inside the script`sum.bash`,
```bash
#!/bin/bash
start=$(date +%s.%N) # set start time
sum=0
for i in $(seq 1 1000); do
  val=`echo "e(1.5 * l(${i}))" | bc -l`
  sum=$(echo "$sum + $val" | bc -l)
done
end=$(date +%s.%N) # set end time
echo Sum=$sum runtime1=$SECONDS runtime2=`echo "$end - $start" | bc -l`
```
::::
:::::::::::::

### Speeding things up
A remedy to the inefficiencies we found inside the`for`loop of`sum.bash`is to avoid the
spawning of many sub-processes that are caused by repetitively calling`bc`.
In other words, ideally, the many sub-processes conflate into one.
In terms of the airplane analogy, we want people to store all their carryon
pieces in a big container, where its subsequent loading onto the plane is a single process,
as opposed to every passenger running a proprietary sub-process.
Unifying the`for`loop's individual sub-processes can be achieved using 
yet another tool,`awk`,
```bash
seq 1 1000 | awk '{s+=$1^1.5} END {printf("Sum=%.6f\n",s)}'
```
In this method, the loop, arithmetic, and accumulation all happen inside a 
single`awk`process. Consequently, the math is done natively in memory, almost
completely free of overhead.

In case you feel somewhat awkward with the`awk`syntax, no worries. No need to understand`awk`at this point. Just note that it is a powerful tool which implicitly treats line input, here given by`seq 1 1000`, like a loop associated with a single process. The example shall be a placeholder for a common scenario, where potentially large efficiency gains can be achieved by replacing 
inefficient math implementations by numerically optimized software *libraries*.

::::::::::::: challenge
### Evaluate the runtime improvement
Compare the runtimes of the summation script`sum.bash`versus the one-liner which
uses`awk`.

:::: hint
The Bash keyword`time`is sufficient to see the runtime difference.
::::

:::: solution
You can use`time`for both summation methods,
```bash
time ./sum.bash
time seq 1 1000 | awk '{s+=$1^1.5} END {printf("Sum=%.6f\n",s)}'
```
::::
:::::::::::::

While it depends a bit on the employed hardware, one will notice that
the`awk`process runs roughly 1000 times faster than`sum.bash`.
Of course, we can live with this inefficiency when our summation is just needed
once in a while and the script's overall runtime amounts to just a few seconds.
However, imagine some large-scale computing job that is supposed to finish within an 
hour on a supercomputer for which one has to pay a usage fee on a per-hour basis.
If implemented poorly, an already small overhead increase, say by a factor of 2, 
would render this computing job expensive, both in terms of time and money.

### CPU-bound versus memory-bound
The above runtime comparisons merely look at calculation speed, which depends on
CPU processing speed. Such a task is thus called *CPU-bound*. 
On the other hand, the peformance of a *memory-bound* process is limited by the speed of memory access. This happens when the CPU spends most of its time waiting for data to be fetched from memory (RAM), cache, or storage, causing its execution pipeline to stall. Optimization of memory-bound tasks addresses performance bottlenecks due
to data transfer speeds rather than calculation speeds.
Finally, when data transfer involves a high percentage of disk or network access, 
disk speed or networking speed becomes a limiting factor, rendering a process *I/O-bound*.

::::::::::::: keypoints
- Efficiency for calculation-heavy jobs boils down to staying as CPU-bound as possible, that is,
  avoiding as much overhead as possible.
- Overhead essentially distracts a CPU (or GPU) from its main job, that is, the
  ratio $\frac{useful\;work}{total\;energy\;expended}$ decreases.
- Too many external system calls can cause time-consuming overhead in an application. 
  Examples for CPU-cycle wasting overhead are

  - repetitive I/O operations, 
  - slow memory access due to non-optimal addressing,
  - process contention owing to too many sub-processes spawned at once.
- For memory-bound and I/O-bound jobs, efficiency revolves around fine-tuning of those parameters that
  dictate data-transfer speed rather than calculation speed.
- Which hardware piece (CPU, memory, disk, network, etc.) poses the limiting factor, 
  depends on the nature of a particular application.
- Whether in scripted or compiled programs, runtime measurements can help isolate
  inefficient program components. Debuggers also offer such tools. 
:::::::::::::

### A different animal: numerical (in)efficiency
Inefficient computing is not only limited to being unneccessarily slow.
It can also entail avoidable numerical inaccuracies as well as excessive accuracies.
Our summation implementation using Bash +`bc`exemplifies a case
for an inaccuracy.

The internal accuracy of`bc`is defined by an adjustable parameter`scale`which
defines how some operations use digits after the decimal point. The default value of`scale`is 0.
During each`bc`call within the summation loop of`sum.bash`, the intermediate result is rounded 
according to the current setting of`scale`. An insufficiently low precision setting
leads to an accumulation of rounding errors over many loop iterations,
rendering the final result (like a sum or product) erroneous.

::::::::::::: challenge
### Compare numerical results
When running the two summation methods in the previous challenge, have a look at the actual
summation results. Which of the two end results do you think is more accurate and why?
Is the erroneous result smaller or larger and why?

:::: hint
Think of another airplane example. Which scenario is more prone to things getting
lost or forgotten? 1) Passengers bring and take their own baggage pieces to the cabin, 
or 2) Baggage pieces are stored and retrieved collectively.
::::

:::: solution
The Bash-`bc`method and the`awk`process return the final sums, respectively,
```
12664913.748614 # bc
12664925.956336 # awk
```
which may vary on your machine.
The second (`awk`) result is more accurate as it is not affected by propagating
rounding errors. While the Bash-`bc`method repeatedly sums values through external calls,
small rounding truncations accumulate at every step. Hence, the final sum drifts downward
compared to the “true” value.
::::
:::::::::::::

::::::::::::: keypoints
- Numerical (in)accuracy is another form of computational (in)efficiency.
- An insufficient accuracy can render computations useless; an excessive accuracy can lead to
  unneccessary runtime increases.
- Finding the optimal accuracy in terms of compute speed and requested precision for
  numerical applications usually involves fine-tuning of corresponding parameters in
  your scripts or compiled programs.
- Rounding errors can accumulate within loop iterations; they can also propagate to
  other program components via passing affected sums, products, etc.
- Errors owing to round-off are oftentimes a good first guess when some iterative
  calculation's numerical output initially looks good but then appears to deviate from expected 
  reference results.
:::::::::::::

## Part 2: A hungry animal - HPC power consumption
The *HP* (high performace) in HPC refers to the fact that the employed computer hardware
is able to do a lot of multitasking, also called parallel computing.
Parallel programming essentially exploits the CPU's multitasking ability.
Therefore, a lot of HPC-efficiency aspects revolve
around keeping everyone in a CPU's multitasking team equally busy.
We will look at some of those aspects in the course of later episodes.

### The more the merrier: CPU/GPU cores
Common parallel-computing jobs employ multiple cores of a CPU, or even multiple CPUs, simultaneously. 
A core is a processing unit within a CPU that independently executes instructions. These days (as of 2025), typical CPUs are quad-core (4 cores), octa-core (8 cores), and so on. High-end gaming CPUs often have 16+ cores, HPC cluster nodes feature multiple CPUs,
oftentimes with 64+ cores each; and all these numbers keep going up.

Nowadays, almost all HPC centers are also equipped with GPU (Graphics Processing Unit)
hardware. The number of GPU cores varies greatly depending on the model, ranging from a few hundred in low-end GPU cards to over 16,000 in high-end ones.

### Measuring parallel runtime: core hours
Owing to the inherent parallelism in the HPC world, people came up with some
measure which takes the *granularity* into account when allocating not only 
runtime but also the number of requested cores.
The unit **core hour** (**core-h**) represents the usage of one CPU core for one hour and
scales with core count.
For example, assume you have a monthly allocation of 500 core-h, with
a fee incurred when exceeding that quota.
So with 500 core-h, you could run a one-hour parallel job utilizing 500 CPU cores for free.
Or, in the other extreme, if your program does not or cannot multitask, 
you could run a single-core job for 500 hours, provided you won't forget at the end
what this job was about.

:::::::::::::::::::::::::: callout
So far, the focus has been on core number and hours for
HPC resource allocation. Keep in mind, however, that the HPC resource portfolio involves
other hardware components as well:

- Memory: There are (whether parallel or not) jobs, that request a large amount of memory (RAM).
For example, some mathematical solution methods for large equation systems do not allow
the compartmentalization of the total required memory across CPU cores, that is, 
many-core processes need to know each other's memory chunks. HPC centers usually have large-memory nodes assigned for such applications.
- Storage: Other applications process huge amounts of data, think of genomics or climate
modelling, which can involve terabytes or even petabytes of data to be stored and analyzed.

::::::::::::::::::::::::::

### A typical HPC computing job
Like in the automotive world, high performance
means high power, which in turn involves a high energy demand. 
Let's consider a typical parallel scientific-computing job to be run in some HPC center.
Our example job shall be deemed too large for one CPU, so it employs multiple CPUs, which in turn are distributed across nodes.
Node power usage is measured in W=Watt, which is the SI unit of power and corresponds to the rate of consumption of energy in an electric circuit.
One compute node with a 64-core CPU can consume between 300 W in idle state, and 900 W (maximum load) for air-cooled systems, whereas this range is roughly 250-850 W for the slightly more efficient liquid-cooled systems. For comparison, an average coffee maker consumes between 800 W (drip coffee maker) and 1500 W (espresso machine). 
Our computing job shall then use these resources:

- 12 nodes are crunching numbers in parallel
- 64 cores/node (e.g., Intel® Xeon® 6774P, or AMD® EPYC® 9534) 
- 12 hours of full load (realistic for many scientific simulations)
- Power per node: (idle vs. full load):
  * Idle: ~300 W
  * Full load: ~900 W
  * Extra power per node: 600 W
- Total extra power: 12 nodes × 600 W × 12 hours = 86,400 Wh = 86.4 kWh

::::::::::::: challenge
### How many core hours does this job involve?
HPC centers have different job *queues* for different kinds of computing jobs.
For example, a queue named *big-jobs* may be reserved for jobs exceeding a total
of 1024 parallel processes = *tasks*. Another queue named *big-mem* may accomodate tasks
with high memory demands by giving access to high-memory nodes
(e.g., 512 GB, 1 TB, or more RAM per compute node).

Let's assume, you have three job queues available, all with identical memory layout:

- `small-jobs`: Total task count of up to 511.
- `medium-jobs`: Total task count 512-1023.
- `big-jobs`: Total task count of 1024 or more.

When submitting the above computing job, in which queue would it end up?

:::: hint
The total number of tasks results from the product *cores-per-node* $\times$ *nodes*. 
::::

:::: solution
The total number of tasks is *cores-per-node* $\times$ *nodes* = $64\times 12 = 768$, which
would put the job into the`medium-jobs`queue.
::::
:::::::::::::

### What are Watt hours?
The unit Wh (Watt-hours) measures energy, so 86,400 Wh is the energy that a 86,400 W (or 86.4 kW, k=kilo) powerful machine consumes in one hour.
Back to coffee, brewing one cup needs 50-100 Wh, depending on preparation time and method.
So, running your 12-node HPC job for 12 hours is equivalent to brewing between 864 and 1,728 cups of coffee.
For those of us who don't drink coffee, assuming 100% conversion efficiency from our compute job's heat to mechanical energy, which is unrealistic,
we could lift an average African elephant (~6 tons) about 5,285 meters straight up, not quite to the top but in sight of Mount Kilimanjaro’s (5,895 m) summit.

### Power-consuming hardware pieces
Note that the focus is on **extra** power, that is, beyond the CPU's idle state. Attributing our job's extra power only to CPU usage underestimates its footprint. 
In practice, the actual delta from idle to full load will vary based on the load posed on other hardware components.
Therefore, it is interesting to shed some light onto those other hardware components that start gearing up after hitting that Enter key which submits the above kind of HPC job. 

- CPUs consume power through two main processes:
  1. Dynamic power consumption: It is caused by the constant switching of transistors and is influenced by the CPU's clock frequency and voltage.
  2. Static power consumption: It is caused by small leakage currents even when the CPU is idle. This is a function of the total number of transistors.  

  Both processes convert electrical energy into heat, which makes CPU cooling so important. 

- Memory (DRAM) consumes power primarily through its refresh cycles. These are required to counteract the charge leakage in the data-storing capacitors. Periodic refreshing is necessary to maintain data integrity, which is the main reason why DRAM draws power even when idle. Other power consumption factors include the static power drawn by the memory's circuitry and the active power used during read/write operations.

- Network interface cards (NICs) consume power by converting digital data into electrical signals for transmission and reception. Power draw increases with data throughput, physical-media complexity, like fiber optics, and also depends on the specific interconnect technology used. 

- Storage components: Hard drives (HDDs) require constant energy due to moving mechanical parts, like the disc-spinning motors. SSDs store data electronically via flash memory chips and are thus more power-efficient, especially when idle. However, when performing heavy read/write tasks, SSD power consumption can also be significant, though they complete these tasks faster than HDDs
and return to their idle state sooner.

- Cooling is one of the biggest contributors to total energy use in HPC:

   - Idle: Cooling uses ~10–20% of total system power.
   - Max load: Cooling can consume ~50–70% of total power (depends on liquid- or air-cooled systems).

   Cooling is essential because all electrical circuits generate heat during operation. Under heavy computational loads, insufficiently cooled CPUs and GPUs exceed their safe temperature limits. 

These considerations hopefully highlight why there is benefit in identifying potential efficiency bottlenecks
before submitting an energy-intense HPC job. If all passengers care about efficient job design, i.e., the total baggage load, more can simultaneously jump onto the HPC workhorse.

:::::::::::::::::::::::::::::::::::::: keypoints
- Large-scaling computing is power hungry, so we want to use the energy wisely.
- Computing job efficiency goes beyond individual gain in runtime as shared resources are used more effectively, that is, the ratio $\frac{useful\;work}{total\;energy\;expended}\sim\frac{number\;of\;users}{total\;energy\;expended}$ goes up.
- While CPUs, GPUs and their cooling consume most power, other hardware components add to the overall energy footprint.
- As shown in the next episodes, you have more *power* than it may be expected over controlling job efficiency and thus overall energy footprint.
::::::::::::::::::::::::::::::::::::::

## So what's next?
The following episodes will put a number of these introductory thoughts 
into concrete action by looking at some efficiency aspects around 
a compute-intense graphical program.
While it is not directly an action-loaded video game, it does contain essential
pieces thereof, because it uses the technique of ray tracing.

Ray tracing is a technique that simulates how light travels in a 3D scene to create 
realistic images. It simulates the behaviour of light in terms of optical effects like
reflection, refraction, shadows, absorption, etc. 
The underlying calculations involve real-world physics, 
which makes them computationally expensive. So are you ready for running a ray tracer 
on HPC hardware?