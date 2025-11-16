# Process Concept

- An operating system executes a variety of programs:
     - Batch system – jobs (groups similar jobs together and execute them automatically)
     - Time-shared systems – user programs or tasks (Divide resources on all users)
- Program is passive entity stored on disk (executable file), process is active, Program becomes process when executable file loaded into memory
- One program can be several processes
- Process execute instructions sequentially
- Process Includes multiple parts
     - The program code, also called text section
     - Current activity involving program counter (Have the address of the next instruction the CPU must execute), processor registers (Contains the data CPU needs in the mean time)
     - Stack containing temporary data (Function parameters, return addresses, local variables)
     - Data section containing global variables
     - Heap containing memory dynamically allocated during run time

# Process State

- As a process executes, it changes state
     - new: The process is being created
     - running: Instructions are being executed (Executing in CPU)
     - waiting: The process is waiting for some event to occur
     - ready: The process is waiting to be assigned to a processor
     - terminated: The process has finished execution

![Image](./Imgs/Pasted%20image%2020251115203944.png)
- Long Term scheduler or Job Scheduler is the one who admits a process from new to ready if the needed resources is available
- Scheduler dispatch gives it access to use CPU resources (running state)


# Process Control Block (PCB)

- Have all information we could need about a process
     - Process state – running, waiting, etc
     - Program counter – location of instruction to next execute
     - CPU registers – contents of all process-centric registers
     - CPU scheduling information – priorities, scheduling queue pointers (Decides which process executes next)
     - Memory-management information – memory allocated to the process
     - Accounting information – CPU used, clock time elapsed since start, time limits
     - I/O status information – I/O devices allocated to process, list of open files

# Process Scheduling

- Maximize CPU use, quickly switch processes onto CPU for time sharing
- Process scheduler selects among available processes for next execution on CPU
- Maintains scheduling queues of processes
    - Job queue – set of all processes in the system
    - Ready queue – set of all processes residing in main memory, ready and waiting to execute
    - Device queues – set of processes waiting for an I/O device
- Processes migrate among the various queues

![Pasted image 20251115220217.png](./Imgs/Pasted%20image%2020251115220217.png)

# Schedulers

## Long-Term Scheduler (Job Scheduler)

- Decides which process can be created and ready for execution and how many process can be running at once (the degree of multiprogramming)
- Works Slowly - Invoked every few seconds or minutes
- The long-term scheduler controls the degree of multiprogramming
- Controls the mix between I/O Bound and CPU-bound Processes
     - I/O-bound process – spends more time doing I/O than computations, many short CPU bursts
     - CPU-bound process – spends more time doing computations; few very long CPU bursts

## Short-Term Scheduler (CPU Scheduler)

- Choose which processes from the ready queue should run next
- Works fast - every milliseconds
- Make decisions based on scheduling algorithms
## Medium Term Scheduler (Swapper)

- Medium-term scheduler can be added if degree of multiple programming needs to decrease
- Called swapper because if there are too many processes in memory, it can suspend one (move it to disk), freeing memory.
- Used in systems that swap in and out of memory
- Later, it can resume the suspended process (bring it back from disk into the ready queue)

# Context Switch

- A **context switch** happens when the CPU switches from one process (or thread) to another **by saving the old process’s state and loading the next one’s state**.
- When CPU switches to another process, the system must save the state of the old process and load the saved state for the new process via a context switch
     - Context of a process represented in the PCB (The values in the CPU registers, The program counter (where it left off), Memory management info, etc.)
- Context-switch time is overhead; the system does no useful work while switching The more complex the OS and the PCB -> longer the context switch
- Time dependent on hardware support

# Operations On Processes

- To do that, it must have mechanisms (built-in procedures) for several key operations on processes like process creation and process termination.

## Process Creation

- A process can create another process making the creating process the parent and the created process the child
- Children can in turn create other processes, forming a tree of processes
- Each process has a unique process identifier (PID)

![Pasted image 20251115222649.png](./Imgs/Pasted%20image%2020251115222649.png)
### Resource Sharing Options:

When a parent creates a child, resources can be shared in different ways:

1. **All shared:** Parent and child share everything.
2. **Partially shared:** Only some resources are shared.
3. **None shared:** Each has its own resources.

### Execution Options:

When a new process is created:

- **Concurrent execution:** Parent and child run at the same time.
- **Sequential execution:** Parent waits until child finishes.

### Address Space

- The private virtual memory of any process
- When a new process (child) is created:
    - It **duplicates** the parent’s memory space.
    - Or, it can have a **new program** loaded into it. (When the child process starts, it initially has a copy of everything the parent had (same code, variables, etc.), but it can later replace that with a new program.)

## Process Termination

- A process can end in two main ways: 
     1. The process finishes its job and calls exit(). (Collected by parent using wait()), Process’ resources are deallocated by the operating system
     2. abort() for this reasons:
         - Child has exceeded allocated resources
         - Task assigned to child is no longer required
         - Parent itself is terminating → cascading termination (all children also stop).

## Interporocess Communication

- Processes within a system may be independent or cooperating
     - **Independent:** Works alone.
     - **Cooperating:** Works with others — can share data or signals.
- Cooperating process can affect or be affected by other processes, including sharing data
 - Why Processes Cooperate:
     - To share information.
     - To speed up computation.
    - To structure a program modularly.
    - For convenience.
-  Two Main IPC Models:
    1. **Message Passing:**
        - Processes send and receive messages via the OS.
        - Used when processes don’t share memory.
    2. **Shared Memory:**
        - Processes share a portion of memory.
        - Faster, but requires synchronization (to avoid conflicts).
        - It must be on the same device

### Message Passing

- Generally recommended over shared memory
- Mechanism for processes to communicate and to synchronize their actions
- Message system – processes communicate with each other without resorting to shared variables (more secure)
- IPC facility provides at least two operations:
    - send(message) – message size fixed or variable
    - receive(message)
- if two processes wish to communicate they need to establish communication link between them to exchange messages via send and receive

#### Implementation Questions

| **Question**                                                     | **Explanation**                                                                                              |
| ---------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------ |
| **How are links established?**                                   | Links can be established either **dynamically** (when processes communicate) or **at process creation**.     |
| **Can a link be associated with more than two processes?**       | Yes, a link can be shared by multiple processes (in the case of indirect communication, for example).        |
| **How many links can there be between every pair of processes?** | There can be **one** or **multiple** links between processes, depending on the system design.                |
| **What is the capacity of a link?**                              | The **capacity** refers to how many messages the link can hold at once (it can be limited or unlimited).     |
| **Is the size of a message fixed or variable?**                  | The message size can be **fixed** (e.g., 256 bytes) or **variable** (dynamic size depending on the message). |
| **Is a link unidirectional or bi-directional?**                  | Links can be **unidirectional** (one-way) or **bidirectional** (both send and receive).                      |

#### Direct Communication

- Processes must name each other explicitly who is sender and who is receiver
- Properties of communication link:
     - Links are established automatically
     - A link is associated with exactly one pair of communicating processes
     - Between each pair there exists exactly one link (only 2 processes)
     - The link may be unidirectional, but is usually bi-directional
#### Indirect Communication

- Messages are directed and received from mailboxes (also referred to as ports)
     - Each mailbox has a unique id
     - Processes can communicate only if they share a mailbox
- Properties of communication link:
     - Link established only if processes share a common mailbox
     - A link may be associated with many processes
     - Each pair of processes may share several communication links)
     - The link may be unidirectional, but is usually bi-directional
- A mailbox may be created (and then owned) either by a process or by the operating system
- Only owner process can receive messages through its mailbox
- Mailbox destroyed by the owner

##### Mailbox sharing

- P1, P2, and P3 share mailbox A owned by OS, P1 sends; P2 and P3 receive, Who gets the message?
- Solutions?
     - Allow a link to be associated with at most two processes (link between p1 and p2, link between p1 and p3, link between p2 and p3)
     - Allow only one process at a time to execute a receive operation
     - Allow the system to select arbitrarily the receiver. Sender is notified who the receiver was
# Producer-Consumer Problem (The Bounded Buffer Problem)

- A producer process produces information that is consumed by a consumer process
- The problem is how to make both processes work together safely without:
     - Overwriting data (producer adds when buffer is full)
    - Reading garbage (consumer takes when buffer is empty)
    - Corrupting data (two processes modifying the buffer at the same time).
- Two Types of buffer:
     1. unbounded-buffer places no practical limit on the size of the buffer (Not realistic)
     2. bounded-buffer assumes that there is a fixed buffer size
- Shared data:
```C
#define BUFFER_SIZE 10
typedef struct 
{
    .. // Item structure (can be any type of data)

} item;

item buffer[BUFFER_SIZE]; //is the shared memory area (10 slots).
int in = 0; //points to where the next item will be produced.
int out = 0; //points to where the next item will be consumed.
```
- Producer Code:
```C
item next_produced;

while (true)
{
 
/* produce an item in next_produced */
while (((in + 1) % BUFFER_SIZE) == out); /* buffer is full, do nothing -- no free buffers */

buffer[in] = next_produced;
in = (in + 1) % BUFFER_SIZE;

}
```
- Consumer Code:
```C
item next_consumed;

while (true) 
{

while (in == out); /* the buffer is empty, do nothing – empty buffers */

next_consumed = buffer[out];
out = (out + 1) % BUFFER_SIZE;

/* consume the item in next_consumed */

}
```
- Solution is correct, but can only use BUFFER_SIZE-1 elements

## Why this simple version isn’t perfect 

- Although this solution “works” in theory, it has two issues:
     1. It wastes CPU time in the while loops — processes are busy waiting (spinning) instead of sleeping.
     2. It doesn’t handle synchronization properly in real systems — if both access in and out simultaneously, race conditions can occur.
- So in practice, we add **synchronization mechanisms** like:
    - **Semaphores**
    - **Mutex locks and condition variables**
- to protect the shared data and block processes properly instead of spinning.

# Synchronization

- Message passing may be either blocking or non-blocking
- Blocking is considered synchronous
     - Blocking send has the sender block until the message is received
     - Blocking receive has the receiver block until a message is available
- Non-blocking is considered asynchronous
     - Non-blocking send has the sender send the message and continue
     - Non-blocking receive has the receiver receive a valid message or null
- If both send and receive are blocking, we have a rendezvous, Producer-consumer problem becomes trivial both will wait for each other forever

# Buffering

- Queue of messages attached to the link; implemented in one of three ways:
     1. Zero capacity – 0 messages Sender must wait for receiver (rendezvous)
     2. Bounded capacity – finite length of n messages Sender must wait if link full
     3. Unbounded capacity – infinite length Sender never waits

# Communications in Client-Server Systems

- Shared memory and message passing strategies can be used for communication in client–server systems as well

## Sockets

- A socket is IP address + port number
- It is the place where server listens to client requests
- The server waits for incoming client requests by listening to a specified **port**
- All ports below 1024 are well known, used for standard services
- To allow a client and server on the same host to communicate, a special IP address 127.0.0.1 (loopback) is used to refer to itself
- Three types of sockets:
     - Connection-oriented (TCP) Socket class in java
     - Connectionless (UDP) datagram Socket class in java
     - MulticastSocket class – data can be sent to multiple recipients

## Remote Procedure Calls (RPCs)

- A Remote Procedure Call (RPC) is a way for a program to execute a procedure (or function) on a remote machine as if it were a local procedure. RPC abstracts the complexity of network communication and makes it look like the client is calling a local function, even though the function is being executed on a different machine.
- Stubs:
     - The client-side stub locates the server and marshalls (make it in a format that can be sent on the net)  the parameters
     - The server-side stub receives this message, unpacks the marshalled parameters, and performs the procedure on the server

## Pipes

- Acts as a channel allowing two processes to communicate
- Simpler ways for communication but have some limitations
- Issues:
     - Is communication unidirectional or bidirectional?
     - In the case of two-way communication, is it half (only goes one way) or full-duplex (can come and go both ways)?
     - Must there exist a relationship (as parent-child) between the communicating processes?
     - Can be used over a network or must reside on the same machine?
- There are 2 types of pipes who answer those issues

### Ordinary Pipe

- Ordinary Pipes allow communication in standard producer-consumer style
     - Producer writes to one end (the write-end of the pipe)
     - Consumer reads from the other end (the read-end of the pipe)
     - So, they allow only unidirectional communication
- Require parent-child relationship between communicating processes, A parent process creates a pipe and uses it to communicate with its child process
- Processes Must be on the same machine

### Named Pipe

- More powerful tool than ordinary pipes
- Communication can be bidirectional
- Half-duplex
- No parent–child relationship is required.
- Several processes can use it for communication
- Doesn't require processes to be on the same machine
- Windows provides a richer mechanism for communication, allowing full-duplex communication (both processes can send and receive simultaneously). Named pipes can also be used to communicate between different machines.

### Remote Method Invocation

- Remote Method Invocation (RMI) is a Java mechanism similar to RPCs.
- RMI allows a Java program on one machine to invoke a method on a remote object.
![Image](Pasted%20image%2020251115203944.png)
- Long Term scheduler or Job Scheduler is the one who admits a process from new to ready if the needed resources is available
=======
![Image](Imgs/Pasted%20image%2020251115203944.png)
- Long Term scheduler or Job Scheduler is the one who admits a process from new to ready if the needed resources is available
- Scheduler dispatch gives it access to use CPU resources (running state)


# Process Control Block (PCB)

- Have all information we could need about a process
     - Process state – running, waiting, etc
     - Program counter – location of instruction to next execute
     - CPU registers – contents of all process-centric registers
     - CPU scheduling information – priorities, scheduling queue pointers (Decides which process executes next)
     - Memory-management information – memory allocated to the process
     - Accounting information – CPU used, clock time elapsed since start, time limits
     - I/O status information – I/O devices allocated to process, list of open files

# Process Scheduling

- Maximize CPU use, quickly switch processes onto CPU for time sharing
- Process scheduler selects among available processes for next execution on CPU
- Maintains scheduling queues of processes
    - Job queue – set of all processes in the system
    - Ready queue – set of all processes residing in main memory, ready and waiting to execute
    - Device queues – set of processes waiting for an I/O device
- Processes migrate among the various queues

![[Pasted image 20251115220217.png]]

# Schedulers

## Long-Term Scheduler (Job Scheduler)

- Decides which process can be created and ready for execution and how many process can be running at once (the degree of multiprogramming)
- Works Slowly - Invoked every few seconds or minutes
- The long-term scheduler controls the degree of multiprogramming
- Controls the mix between I/O Bound and CPU-bound Processes
     - I/O-bound process – spends more time doing I/O than computations, many short CPU bursts
     - CPU-bound process – spends more time doing computations; few very long CPU bursts

## Short-Term Scheduler (CPU Scheduler)

- Choose which processes from the ready queue should run next
- Works fast - every milliseconds
- Make decisions based on scheduling algorithms
## Medium Term Scheduler (Swapper)

- Medium-term scheduler can be added if degree of multiple programming needs to decrease
- Called swapper because if there are too many processes in memory, it can suspend one (move it to disk), freeing memory.
- Used in systems that swap in and out of memory
- Later, it can resume the suspended process (bring it back from disk into the ready queue)

# Context Switch

- A **context switch** happens when the CPU switches from one process (or thread) to another **by saving the old process’s state and loading the next one’s state**.
- When CPU switches to another process, the system must save the state of the old process and load the saved state for the new process via a context switch
     - Context of a process represented in the PCB (The values in the CPU registers, The program counter (where it left off), Memory management info, etc.)
- Context-switch time is overhead; the system does no useful work while switching The more complex the OS and the PCB -> longer the context switch
- Time dependent on hardware support

# Operations On Processes

- To do that, it must have mechanisms (built-in procedures) for several key operations on processes like process creation and process termination.

## Process Creation

- A process can create another process making the creating process the parent and the created process the child
- Children can in turn create other processes, forming a tree of processes
- Each process has a unique process identifier (PID)

![[Pasted image 20251115222649.png]]
### Resource Sharing Options:

When a parent creates a child, resources can be shared in different ways:

1. **All shared:** Parent and child share everything.
2. **Partially shared:** Only some resources are shared.
3. **None shared:** Each has its own resources.

### Execution Options:

When a new process is created:

- **Concurrent execution:** Parent and child run at the same time.
- **Sequential execution:** Parent waits until child finishes.

### Address Space

- The private virtual memory of any process
- When a new process (child) is created:
    - It **duplicates** the parent’s memory space.
    - Or, it can have a **new program** loaded into it. (When the child process starts, it initially has a copy of everything the parent had (same code, variables, etc.), but it can later replace that with a new program.)

## Process Termination

- A process can end in two main ways: 
     1. The process finishes its job and calls exit(). (Collected by parent using wait()), Process’ resources are deallocated by the operating system
     2. abort() for this reasons:
         - Child has exceeded allocated resources
         - Task assigned to child is no longer required
         - Parent itself is terminating → cascading termination (all children also stop).

## Interporocess Communication

- Processes within a system may be independent or cooperating
     - **Independent:** Works alone.
     - **Cooperating:** Works with others — can share data or signals.
- Cooperating process can affect or be affected by other processes, including sharing data
 - Why Processes Cooperate:
     - To share information.
     - To speed up computation.
    - To structure a program modularly.
    - For convenience.
-  Two Main IPC Models:
    1. **Message Passing:**
        - Processes send and receive messages via the OS.
        - Used when processes don’t share memory.
    2. **Shared Memory:**
        - Processes share a portion of memory.
        - Faster, but requires synchronization (to avoid conflicts).
        - It must be on the same device

### Message Passing

- Generally recommended over shared memory
- Mechanism for processes to communicate and to synchronize their actions
- Message system – processes communicate with each other without resorting to shared variables (more secure)
- IPC facility provides at least two operations:
    - send(message) – message size fixed or variable
    - receive(message)
- if two processes wish to communicate they need to establish communication link between them to exchange messages via send and receive

#### Implementation Questions

| **Question**                                                     | **Explanation**                                                                                              |
| ---------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------ |
| **How are links established?**                                   | Links can be established either **dynamically** (when processes communicate) or **at process creation**.     |
| **Can a link be associated with more than two processes?**       | Yes, a link can be shared by multiple processes (in the case of indirect communication, for example).        |
| **How many links can there be between every pair of processes?** | There can be **one** or **multiple** links between processes, depending on the system design.                |
| **What is the capacity of a link?**                              | The **capacity** refers to how many messages the link can hold at once (it can be limited or unlimited).     |
| **Is the size of a message fixed or variable?**                  | The message size can be **fixed** (e.g., 256 bytes) or **variable** (dynamic size depending on the message). |
| **Is a link unidirectional or bi-directional?**                  | Links can be **unidirectional** (one-way) or **bidirectional** (both send and receive).                      |

#### Direct Communication

- Processes must name each other explicitly who is sender and who is receiver
- Properties of communication link:
     - Links are established automatically
     - A link is associated with exactly one pair of communicating processes
     - Between each pair there exists exactly one link (only 2 processes)
     - The link may be unidirectional, but is usually bi-directional
#### Indirect Communication

- Messages are directed and received from mailboxes (also referred to as ports)
     - Each mailbox has a unique id
     - Processes can communicate only if they share a mailbox
- Properties of communication link:
     - Link established only if processes share a common mailbox
     - A link may be associated with many processes
     - Each pair of processes may share several communication links)
     - The link may be unidirectional, but is usually bi-directional
- A mailbox may be created (and then owned) either by a process or by the operating system
- Only owner process can receive messages through its mailbox
- Mailbox destroyed by the owner

##### Mailbox sharing

- P1, P2, and P3 share mailbox A owned by OS, P1 sends; P2 and P3 receive, Who gets the message?
- Solutions?
     - Allow a link to be associated with at most two processes (link between p1 and p2, link between p1 and p3, link between p2 and p3)
     - Allow only one process at a time to execute a receive operation
     - Allow the system to select arbitrarily the receiver. Sender is notified who the receiver was
# Producer-Consumer Problem (The Bounded Buffer Problem)

- A producer process produces information that is consumed by a consumer process
- The problem is how to make both processes work together safely without:
     - Overwriting data (producer adds when buffer is full)
    - Reading garbage (consumer takes when buffer is empty)
    - Corrupting data (two processes modifying the buffer at the same time).
- Two Types of buffer:
     1. unbounded-buffer places no practical limit on the size of the buffer (Not realistic)
     2. bounded-buffer assumes that there is a fixed buffer size
- Shared data:
```C
#define BUFFER_SIZE 10
typedef struct 
{
    .. // Item structure (can be any type of data)

} item;

item buffer[BUFFER_SIZE]; //is the shared memory area (10 slots).
int in = 0; //points to where the next item will be produced.
int out = 0; //points to where the next item will be consumed.
```
- Producer Code:
```C
item next_produced;

while (true)
{
 
/* produce an item in next_produced */
while (((in + 1) % BUFFER_SIZE) == out); /* buffer is full, do nothing -- no free buffers */

buffer[in] = next_produced;
in = (in + 1) % BUFFER_SIZE;

}
```
- Consumer Code:
```C
item next_consumed;

while (true) 
{

while (in == out); /* the buffer is empty, do nothing – empty buffers */

next_consumed = buffer[out];
out = (out + 1) % BUFFER_SIZE;

/* consume the item in next_consumed */

}
```
- Solution is correct, but can only use BUFFER_SIZE-1 elements

## Why this simple version isn’t perfect 

- Although this solution “works” in theory, it has two issues:
     1. It wastes CPU time in the while loops — processes are busy waiting (spinning) instead of sleeping.
     2. It doesn’t handle synchronization properly in real systems — if both access in and out simultaneously, race conditions can occur.
- So in practice, we add **synchronization mechanisms** like:
    - **Semaphores**
    - **Mutex locks and condition variables**
- to protect the shared data and block processes properly instead of spinning.

# Synchronization

- Message passing may be either blocking or non-blocking
- Blocking is considered synchronous
     - Blocking send has the sender block until the message is received
     - Blocking receive has the receiver block until a message is available
- Non-blocking is considered asynchronous
     - Non-blocking send has the sender send the message and continue
     - Non-blocking receive has the receiver receive a valid message or null
- If both send and receive are blocking, we have a rendezvous, Producer-consumer problem becomes trivial both will wait for each other forever

# Buffering

- Queue of messages attached to the link; implemented in one of three ways:
     1. Zero capacity – 0 messages Sender must wait for receiver (rendezvous)
     2. Bounded capacity – finite length of n messages Sender must wait if link full
     3. Unbounded capacity – infinite length Sender never waits

# Communications in Client-Server Systems

- Shared memory and message passing strategies can be used for communication in client–server systems as well

## Sockets

- A socket is IP address + port number
- It is the place where server listens to client requests
- The server waits for incoming client requests by listening to a specified **port**
- All ports below 1024 are well known, used for standard services
- To allow a client and server on the same host to communicate, a special IP address 127.0.0.1 (loopback) is used to refer to itself
- Three types of sockets:
     - Connection-oriented (TCP) Socket class in java
     - Connectionless (UDP) datagram Socket class in java
     - MulticastSocket class – data can be sent to multiple recipients

## Remote Procedure Calls (RPCs)

- A Remote Procedure Call (RPC) is a way for a program to execute a procedure (or function) on a remote machine as if it were a local procedure. RPC abstracts the complexity of network communication and makes it look like the client is calling a local function, even though the function is being executed on a different machine.
- Stubs:
     - The client-side stub locates the server and marshalls (make it in a format that can be sent on the net)  the parameters
     - The server-side stub receives this message, unpacks the marshalled parameters, and performs the procedure on the server

## Pipes

- Acts as a channel allowing two processes to communicate
- Simpler ways for communication but have some limitations
- Issues:
     - Is communication unidirectional or bidirectional?
     - In the case of two-way communication, is it half (only goes one way) or full-duplex (can come and go both ways)?
     - Must there exist a relationship (as parent-child) between the communicating processes?
     - Can be used over a network or must reside on the same machine?
- There are 2 types of pipes who answer those issues

### Ordinary Pipe

- Ordinary Pipes allow communication in standard producer-consumer style
     - Producer writes to one end (the write-end of the pipe)
     - Consumer reads from the other end (the read-end of the pipe)
     - So, they allow only unidirectional communication
- Require parent-child relationship between communicating processes, A parent process creates a pipe and uses it to communicate with its child process
- Processes Must be on the same machine

### Named Pipe

- More powerful tool than ordinary pipes
- Communication can be bidirectional
- Half-duplex
- No parent–child relationship is required.
- Several processes can use it for communication
- Doesn't require processes to be on the same machine
- Windows provides a richer mechanism for communication, allowing full-duplex communication (both processes can send and receive simultaneously). Named pipes can also be used to communicate between different machines.

### Remote Method Invocation

- Remote Method Invocation (RMI) is a Java mechanism similar to RPCs.
- RMI allows a Java program on one machine to invoke a method on a remote object.
