# Basic Concepts

- Maximum CPU utilization obtained with multiprogramming
- CPU Burst Cycle: a way of calculating operations on CPU (CPU Execution)
- I/O Burst Cycle: a way of calculating time of I/O Operations (CPU Wait)
- CPU burst followed by I/O burst 
    - Because there must a process in execution first to check if it need any I/O Operations
- CPU burst distribution is of main concern
     - Because all scheduling algorithms depends on CPU burst distribution
- CPU Burst usually takes little time, while I/O Burst could take little or many time.

# CPU Scheduler

- Short-term scheduler selects from among the processes in ready queue, and allocates the CPU to one of them
     - Ready queue may be ordered in various ways (like the various algorithms we going to take)
- CPU scheduling decisions may take place when a process:
     1. Switches from running to waiting state (non-preemptive because process interrupts itself for I/O)
     2. Switches from running to ready state (Preemptive because interrupted by OS)
     3. Switches from waiting to ready (preemptive because OS can change processes due to higher priority)
     4. Terminates (non-preemptive because process terminates by itself when it finished what it needed to finish on the CPU)
- Job Scheduler (Long-term Scheduler) is the one who loads processes into main memory for the CPU Scheduler to start doing its job like stated above.
- **non-preemptive**: a process that cannot be interrupted by the OS, But it can be interrupted by itself
- **preemptive**: Can be interrupted by the OS, The highest priority process should always be the process that is currently running.
     - should be dealt with cautiously, and be a controlled interrupt due to access to shared data, while in kernel mode, and interrupts occurring during critical OS activities

# Dispatcher

- It takes processes from ready to running state
- Dispatcher module gives control of the CPU to the process selected by the short-term scheduler this involves:
     - switching context
     - switching to user mode
     - jumping to the proper location in the user program to restart that program
- **Dispatch latency**: time it takes for the dispatcher to stop one process and start another running.

# Scheduling Criteria

- Criteria to judge algorithms and compare between them.
- **CPU utilization** – keep the CPU as busy as possible
- **Throughput** – Number of processes that complete their execution per time unit
     - an algorithm who finishes 3 processes in 5 seconds better than a one who finishes only 2 in those 5 seconds (higher throughput)
- **Turnaround time** (TAT) – amount of time to execute a particular process (Completion time - Arrival time)
- **Waiting time** (WT)– amount of time a process has been waiting in the ready queue (Turnaround time - burst time = Execution Start time - arrival time)
- **Response time** – amount of time it takes from when a request was submitted until the first response is produced, not output (for timesharing environment)
- **Scheduling Algorithm Optimization Criteria** - We want to minimize Turnaround time, waiting time and response time and maximize CPU utilization and throughput 

# Scheduling Techniques

## First-Come, First-Served (FCFS) Scheduling

- We take processes one by one depending on who arrived first
- Non-preemptive algorithm where processes gets served fully without interruption depending on who arrived first
- In cases of processes with shortest jobs arrive first we found that WT becomes much better because they don't have to wait for long jobs (Convoy effect) and that introduces SJF algorithm
- **Convoy effect** - short process behind long process (Long processes block short process)
- Long jobs is CPU bound and short jobs is I/O bound
- Could be poor in performance as average wait time is high.

## Shortest-Job-First (SJF) Scheduling

- Associate with each process the length of its next CPU burst and use these lengths to schedule the process with the shortest time
- SJF is optimal – gives minimum average waiting time for a given set of processes
- The difficulty is knowing the length of the next CPU request but could ask the user
- We Can only estimate the length – should be similar to the previous one (If the previous one was 5 then the next can be estimated 7), Then pick process with shortest predicted next CPU burst
- Can be done by using the length of previous CPU bursts, using exponential averaging
- $$
\tau_{n+1} = \alpha t_n + (1 - \alpha)\tau_n
$$

- $t_n$ = actual length of nth CPU burst ,$\tau_{n+1}$ = predicted value for the next CPU burst, $\tau_n$ = estimation of nth process (previous prediction), we do for 0 <= $\alpha$ <= 1, commonly set to 1/2 to give both sides (actual and estimation the same possibility).
- when $\alpha$ = 0, $\tau_{n+1}$ =  $\tau_n$ , Recent History does not count (ignore the actual last CPU burst)
- when $\alpha$ = 1, $\tau_{n+1}$ =  $t_n$ , Only the actual last CPU burst counts
- The more recent the burst, the more influence it has, Old behavior still matters, but **less and less**
- Two schemes:
    - Non-preemptive – once CPU given to the process it cannot be preempted until completes its CPU burst.
    - Preemptive – if a new process arrives with CPU burst length less than remaining time of current executing process, preempt. This scheme is know as the **Shortest-Remaining-Time-First** (SRTF).
- SJF -> Non-preemptive
- SRTF -> Preemptive

## Priority Scheduling

- A priority number (integer) is associated with each process
- CPU is allocated to the process with the highest priority (smallest integer = highest priority)
- Priority can be decided based on memory requirements, time requirements or any other resource requirement.
- Problem = Starvation – low priority processes may never execute.
    - Solution = Aging – as time progresses increase the priority of the process.
- SJF is priority scheduling where priority is the inverse of predicted next CPU burst time (burst time increases, priority decreases)

## Round Robin

- Each process gets a small unit of CPU time (time quantum q), usually 10-100 milliseconds. After this time has elapsed, the process is preempted and added to the end of the ready queue. Context switching is used to save states of pre-empted processes.
- If there are n processes in the ready queue and the time quantum is q, then:
     - Each process gets 1/n of the CPU time in chunks of at most q time units at once.
     - No process waits more than (n-1)q time units.
     - Timer interrupts every q to schedule next process
 - Performance:
    - q large -> FIFO -> FCFS
    - q small -> q must be large with respect to context switch, otherwise overhead is too high. (small q, many context switches and interrupts)
- 80% of CPU bursts should be shorter than q, to minimize turnaround time
    - very few processes get preempted
    - fewer context switches
    - lower waiting time
    - better performance

# Multilevel Queue

- Ready queue is partitioned into separate queues, eg:
    - foreground (interactive) (Like apps we use usually)
    - background (batch) (like anti virus for example)
- Process permanently in a given queue, either foreground or background
- Each queue has its own scheduling algorithm:
    - foreground – RR
    - background – FCFS
- Scheduling must be done between the queues:
     1. Fixed priority scheduling; (i.e., serve all from foreground then from background). Possibility of starvation.
     2. Time slice – each queue gets a certain amount of CPU time which it can schedule amongst its processes; i.e., 80% to foreground in RR, 20% to background in FCFS

- Example:
![Pasted image 20251230204812.png](./Imgs/Pasted%20image%2020251230204812.png)

- The first 4 is system processes and the last one can be called other user processes

# Multilevel Feedback Queue

- A process can move between the various queues; aging can be implemented this way.
- Multilevel-feedback-queue scheduler defined by the following parameters:
    - number of queues
    - scheduling algorithms for each queue (FCFS, RR, ...)
    - method used to determine when to upgrade a process
    - method used to determine when to demote a process
    - method used to determine which queue a process will enter when that process needs service
- Example:
![Pasted image 20251230205837.png](./Imgs/Pasted%20image%2020251230205837.png)

# Multiple-Processor Scheduling

- With multiple CPUs, load sharing becomes possible but CPU scheduling more complex
## Types of Processors

- **Homogeneous processors** - Can use any processor to run any process in the ready queue
- **Asymmetric multiprocessing** - Only one master processor accesses system data structures and other processors execute only user code
- **Symmetric multiprocessing (SMP)** - Each processor is self-scheduling, all processes in common ready queue, or each has its own private ready queue, Currently, most common
- **Processor affinity** – the OS tries to keep a process running on the same CPU (cache-friendly)
     - soft affinity or hard affinity
     - **Processor sets** = a group (subset) of CPUs that a process or group of processes is allowed to run on.
     - Variations including processor sets

## Load Balancing

- On SMP, need to keep workload balanced among all CPUs to fully utilize benefits of multiprocessors
- **Load balancing** attempts to keep workload evenly distributed across all processors
    - Necessary only when each processor has its own private queue of ready processes
- **Push migration** – a task periodically checks load on each processor, and if found imbalance pushes task from overloaded CPU to other CPUs (OS Pushes)
- **Pull migration** – idle processors pulls waiting task from busy processor (CPU Pulls)

# Algorithm Evaluation

## Deterministic Evaluation

- Type of analytic evaluation
- Takes a particular predetermined workload and defines the performance of each algorithm for that workload
- Gives specific Numbers and answers
- Example: For each algorithm, calculate minimum average waiting time, so we can choose.
- Simple and fast, but requires exact numbers for input, and its answers apply only to those cases

## Queueing Models

- Normally, there is no static set of processes to use for deterministic modeling.
- However, the distribution of CPU and I/O bursts can be measured and then simply estimated
- Describes the arrival of processes, and CPU and I/O bursts **probabilistically**
    - Commonly exponential, and described by mean
    - Computes Average throughput, utilization, waiting time
- Computer system described as network of servers, each with queue of waiting processes
- Knowing arrival rates and service rates is a must
- Little's Formula: $n = \lambda$ X $W$      
- $n$ = average queue length, $W$ = average waiting time in queue, $\lambda$ = average arrival rate into queue
- Valid for any scheduling algorithm and arrival distribution

## Simulations

- Written program that simulates a real OS environment 
- Queueing analysis is useful but has limitations
     - Mathematics of complex algorithms and distributions can be difficult to work with
- Simulations more accurate:
    -  Programmed model of computer system
    - Data structures represent components of the system (clock is a variable)
    - Gather statistics indicating algorithm performance
    - Data to drive simulation gathered via
         - Random number generator according to probabilities
         - Distributions defined mathematically or empirically
         - Trace tapes record sequences of real events in real systems 
![Pasted image 20251230214702.png](./Imgs/Pasted%20image%2020251230214702.png)
## Implementation

- Simulation can be expensive and limited accuracy, A more detailed simulation provides more accurate results, but it also takes more computer time.
- Only completely accurate way to implement new scheduler and test in real systems, but some risks:
    - Cost of coding the algorithm and risk of users reaction
    - Changing the environment in which algorithm is used
- Most flexible scheduling algorithms that can be altered by system managers and tuned for a specific set of applications
    - Use APIs that can modify priority of a process or thread But again environments vary