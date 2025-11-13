# Operating System Definition

- An operating system is a program or software that manages a computer’s hardware. It also provides a basis for application programs and acts as an intermediary between the computer user and the computer hardware.
- some operating systems are designed to be convenient, others to be efficient, and others to be some combination of the two.
- The one program running at all times on the computer is the kernel, Everything else is either a system program (ships with the operating system but not necessarily part of the kernel) or an application program which includes all programs not associated with the operation of the system.

# What Operating Systems Do
## Computer System Structure

### The Hardware

- The hardware—the central processing unit (CPU), the memory (RAM), and the input/output (I/O) devices—provides the basic computing resources for the system.

### The Application Programs

- The application programs—such as word processors, spreadsheets, compilers, and Web browsers—define the ways in which these resources are used to solve users’ computing problems.

### The Operating System

- The operating system controls the hardware and coordinates its use among the various application programs for the various users.
- An operating system is similar to a government. Like a government, it performs no useful function by itself. It simply provides an environment within which other programs can do useful work.

### Users

- People, Machines, other computers

#### User View

- A single user convenience, ease of use and no attention paid for resource utilization
- A multiple Users who are connected to mainframe or a minicomputer, These users share resources and may exchange information. The operating system in such cases is designed to maximize resource utilization— to assure that all available CPU time, memory, and I/O are used efficiently and that no individual user takes more than her fair share.
- Users who sit at workstations connected to networks of other workstations and servers, they  share resources such as networking and servers, including file, compute, and print servers. Their operating system is designed to compromise between individual usability and resource utilization.
- Some computers have little or no user view, like embedded computers in home devices and automobiles.

#### System View

- From the computer’s point of view, the operating system is the program most intimately involved with the hardware. In this context, we can view an operating system as a **resource allocator** of: CPU time, memory space, file-storage space, I/O devices, and so on. The operating system acts as the manager of these resources deciding between conflicting requests for efficient and fair resource use
- An operating system is a **control program**. A control program manages the execution of user programs to prevent errors and improper use of the computer. It is especially concerned with the operation and control of I/O devices. So , It Controls execution of programs to prevent errors and improper use of the computer.

![image](Imgs/Pasted%20image%2020251112142543.png)

# Computer-System Organization

## Computer-System Operation

- The initial program, or bootstrap program, tends to be simple. Typically, it is stored within the computer hardware in (ROM) or (EEPROM), known by the general term **firmware**. It initializes all aspects of the system, from CPU registers to device controllers to memory contents. The bootstrap program must know how to load the operating system and how to start executing that system. The bootstrap program must locate the operating-system kernel and load it into memory.
- One or more CPUs, device controllers connect through common bus providing access to shared memory
- CPUs and device controllers can execute in parallel, competing for memory cycles
- Each device controller is in charge of a particular device type ex: (Disk controller, USB controller, ...etc)
- Each device controller has a local buffer
- CPU moves data from/to main memory to/from local buffers
- I/O devices and the CPU can execute concurrently
- I/O is from the device to local buffer of controller
- Device controller informs CPU that it has finished its operation by causing an interrupt

### Interrupts

- Modern operating systems are **interrupt driven**. An event's occurrence is signaled by an interrupt or a trap.
- **Interrupts** are typically hardware-generated signals (e.g., an I/O device controller informing the CPU that it has finished its operation)
- Software may trigger an interrupt by executing a special operation called a system call  (also called a monitor call)
- A **trap (or exception)** is a software-generated interrupt caused either by an error (like division by zero or Other process problems include infinite loop, processes modifying each other or the operating system) or by a specific **user request for an OS service (a system call)**
- **When an Interrupt occurs:**
  1- The CPU **transfers control to the appropriate interrupt service routine** via the **interrupt vector** (which contains addresses of all service routines)
  2- The operating system **preserves the state of the CPU** by storing registers and the program counter
  3- The CPU then resumes the interrupted computation once the service routine is complete


# Storage Structure

- **Word** is a given computer architecture's native unit of data.
- Word can be 32 bit or 64 bit depends on word size.

## Main Memory (RAM)

- Volatile and Random Access
- Only Large storage media that the CPU can access directly

## Secondary Storage

- Extension of the main memory that provides large nonvolatile storage capacity

### Hard Disk (Magnetic Disk)

- Disk surface is logically divided into tracks, which are subdivided into sectors, The disk controller determines the logical interaction between the device and the computer.
- Usually Slow

### Solid-State disks

- Faster than magnetic disks 
- Have various technologies and becoming more popular

## Storage Systems Hierarchy

![image](Imgs/Pasted%20image%2020251112150450.png)

- Registers very fast but very expensive, so usually (small sizes) of it is put with processors so it can be fast with it to store its essential items.
- Cache copying information into faster storage system ,exists with processor and motherboard
- Main memory can be viewed as a cache for secondary storage
- Optical disk like CD, DVD
- Magnetic tapes like شرايط الفيديو
- First 3 is most volatile, the rest is non-volatile

### Caching

- Important principle, performed at many levels in a computer (in H/W, OS, S/W)
- Information in use copied from slower to faster storage temporarily
- Faster storage (cache) checked first to determine if information is there.
- Cache smaller than storage being cached.


# I/O Structure

- When a program needs to read or write data, it asks the operating system to do that through a system call (like read() or write())
- The OS then handles communication with the **I/O device** (like disk or printer)

## Two I/O Methods

### Synchronous I/O (Blocking I/O)

- After the program starts the I/O operation, it **waits** until the operation finishes.
- The CPU might:
  - Be idle (doing nothing) until the device finishes, or
  - Continuously check (a “wait loop”) to see if the operation is done.
- Only **one I/O request** is processed at a time.
- The program **blocks** (waits).
- Example:  
You send a document to the printer — your program **pauses** until printing is complete.

### Asynchronous I/O (Non-blocking I/O)

- After starting the I/O, **control returns immediately** to the program **without waiting** for completion.
- The OS will **notify the program later** (usually via an **interrupt**) when the I/O finishes.
- The program **keeps running** while I/O happens in the background.
- The OS uses **interrupts** to signal I/O completion
- **More efficient** because the CPU can do other work.
- Example:  
You start downloading a file and **continue browsing** — the OS notifies the app when the download is done.

**Device-Status Table** → the OS keeps a table that records information about each I/O device (like whether it’s busy, what operation it’s doing, etc.).

![image](Imgs/Pasted%20image%2020251112152934.png)

# How Modern Computer Works

## Direct Memory Access (DMA)

- For high-speed I/O devices, a specialized controller called the **Direct Memory Access (DMA) controller** is used
- The DMA controller moves **blocks of data** directly between the device's buffer storage and main memory **without intervention from the CPU**
- This is highly efficient because **only one interrupt is generated per block transferred**, instead of one interrupt per byte

![image](Imgs/Pasted%20image%2020251112153435.png)


# Computer System Architecture

- Most systems use a single general-purpose processor (PDAs through mainframes)

## Multiprocessors (Parallel Systems or Multicore Systems)

- Multiprocessors systems growing in use and importance.
- Advantages:
  1- Increased Throughput: by increasing the number of processors, we expect to get more work done in less time.
  2- Economy of scale: Multiprocessor systems can cost less than equivalent multiple single-processor systems, because they can share peripherals, mass storage and power supplies, ex: A car with 5 motors cheaper and better performance than 5 cars each with 1 motor.
  3- Increased reliability: fault tolerance.
- Two Types:
  1- Asymmetric Multiprocessing: Each processor is assigned to a specific task.
  2- symmetric Multiprocessing: Each processor performs all tasks.

## Dual-Core Design

- A **dual-core processor** is a single physical chip that contains **two CPU cores** — think of it as **two brains in one chip**.
- Each core has its own registers and its own cache.

### UMA (Uniform Memory Access)

- Every CPU core takes **the same time** to access any memory location.
- Memory access speed is **uniform** for all cores.
- **Think of it like:**  
Two people sharing one table — both can reach any item equally fast.

### NUMA (Non-Uniform Memory Access)

- Different parts of memory take **different amounts of time** to access depending on which core is accessing it.
- Each core (or group of cores) has its **own local memory**, but can still access others’ memory (slower)
- Common in **large multiprocessor servers**.
- **Think of it like:**  
Each person has their own desk (local memory) — reaching for something on another’s desk takes longer.

### Multiple Chips vs. Multiple Cores

- **Multiple chips with single cores:** old systems, each CPU chip had one core.
- **Multiple cores on one chip:** modern CPUs — multiple cores inside the same physical chip (faster communication, less power use).
- Example:
 - Old: 2 chips × 1 core each → two separate processors.
 - Now: 1 chip × 2 cores → dual-core processor.


### Blade Servers

- A **blade server** is like a box (chassis) that holds **many independent multiprocessor systems**.
- Each “blade” is its own computer (with CPU, memory, etc.), and all blades share **power, cooling, and sometimes networking**.
- **Think of it like:**  
A bookshelf full of mini-computers (each blade = one computer) all working together.


![image](Imgs/Pasted%20image%2020251112155330.png)

## Clustered Systems

- Clustered Systems are like **multiprocessor systems**, but instead of multiple CPUs in one computer, you have **multiple computers (nodes) working together** (connected through a fast network) as a single system.
-  Each node runs its own operating system.
- They **share storage** and sometimes memory through a **Storage Area Network (SAN)**.
  - A SAN is a **high-speed network** that connects all storage devices (disks) so all nodes can access the same data.
- If one node fails, another can **take over its work** — this makes the system **reliable and always available**.
  1- Asymmetric Clustering: One node active and other as standby (waiting to take over if the active one fails). Common in systems where uptime is critical (like banking servers). If one fails, others continue its work.
  2- Symmetric Clustering: **All nodes are active**, running applications **and monitoring each other**. This method is **more efficient** because no computer is idle.
  3- High-Performance Computing (HPC) Clusters: Some clusters are built to **boost performance**, not just reliability. They split a big task into smaller parts so **many nodes work in parallel** (this is called **parallelization**).
- In short:
> A cluster = several computers acting as one big computer.

### Distributed Lock Manager (DLM)

- When many computers share the same data, they might try to **change it at the same time** — this can cause conflicts. A **Distributed Lock Manager** prevents that by **coordinating access** to shared resources.
- Example:  
If one node is writing to a file, the DLM “locks” it so other nodes can’t modify it simultaneously.


![image](Imgs/Pasted%20image%2020251112160304.png)

![image](Imgs/Pasted%20image%2020251112160320.png)

# Operating System Structure

## Multiprogramming

- Multiprogramming needed for efficiency
- Multiprogramming organizes jobs (code and data) so CPU always has one to execute
- A jobs set is selected from a pool via job scheduling
- A subset of total jobs in the system is kept in memory
- When a job has to wait (for I/O for example), OS switches to another job

## Timesharing (Multitasking)

- Timesharing (multitasking) is a logical extension to multiprogramming
- The CPU switches jobs so frequently that users can interact with each job while it is running, creating interactive computing
- Each user has at least one program executing in memory -> process
- If several jobs ready to run at the same time -> CPU scheduling
- If processes don’t fit in memory, swapping moves them in and out to run
- Virtual memory allows execution of processes not completely in memory


# Operating Systems Operations

## Dual-mode

- Allows OS to protect itself and other system components
- To ensure the integrity of the system and prevent user programs from causing errors or improper use, computer systems rely on hardware support for **dual-mode operation**
  1-  User Mode: To deal with software
  2- Kernel Mode (supervisor mode, system mode, or privileged mode): To deal with hardware
- A bit, called the mode bit, is added to he hardware of the computer to indicate the current mode: kernel(0) or user(1)
  - Provides ability to distinguish when system is running user code or kernel code.
  - Some instructions designated as privileged, only executable in kernel mode
  - System call changes mode to kernel, return from call resets it to user

![image](Imgs/Pasted%20image%2020251112162245.png)

## Timer

- A **timer** (also called a _clock interrupt_ or _hardware timer_) is a **hardware mechanism** that lets the operating system **regain control of the CPU** after a specific amount of time.
- The OS must prevent user programs from **running forever** or **monopolizing CPU time**
- Without a timer, a user program could:
  - Enter an **infinite loop**
  - Or **hog the CPU** (use it indefinitely), preventing the OS from switching to another process.
- The timer ensures **fairness and control** by periodically giving the OS a chance to take the CPU back.

### How it works

1- Set interrupt after specific period
2- OS initializes and decrements counter
3- When counter reaches zero → interrupt generated
4- OS regains control / terminates overrun processes

## Process Management

- A **process is a program in execution** 
- A program is a **passive entity** (like a file on disk), while a process is an **active entity** that requires resources (CPU, memory, files, I/O devices) to accomplish its task
- Process termination requires reclaim of any reusable resources
- Single-threaded process has only one sequence of execution therefor, it has one program counter specifying location of next instruction to execute
- Process executes instructions sequentially, one at a time, until completion
- _Example:_  A simple calculator program that executes one operation at a time.
- Multi-threaded process has one program counter per thread
- Threads within the same process share: The same memory space and The same code and data sections, But each thread has its own: Program Counter, Registers and Stack.
- _Example:_ A web browser — one thread loads a webpage, another plays music, another handles user input.
- A computer typically runs **many processes** at once, The OS uses **concurrency** — meaning it gives each process a _slice_ of CPU time, switching between them rapidly so they _appear_ to run simultaneously.
- On single-core CPUs: Only **one process runs at a time**, but the OS **switches quickly** between them (called **multiprogramming** or **context switching**).
- On multi-core CPUs:  **Multiple processes or threads** can truly execute **in parallel**.
- The OS is responsible for activities like:
  -  **Scheduling** processes and threads on the CPUs.
  - Creating and deleting processes.
  - Providing mechanisms for **process synchronization** and **process communication**.
  - Providing mechanisms for **deadlock handling**
  - Suspending and resuming processes

## Memory Management

- Main memory is a large array of bytes that holds instructions and data for execution
- The OS performs memory management to keep several programs in memory simultaneously, improving CPU utilization and response speed
- Memory Management Activities:
  - Keeping track of which parts of memory are being used and by whom.
  - Deciding which processes (or parts thereof) and data move into and out of memory.
  - Allocating and deallocating memory space.

## Storage Management

- The OS abstracts the physical properties of storage devices into a logical unit called a **file**. Files are organized into **directories**
- Access control on most systems to determine who can access what
- OS activities include:
  - Creating and deleting files and directories
  - Supporting primitives to manipulate files and directories
  - Mapping files onto secondary storage
  - Backup files onto stable (non-volatile) storage media

## Mass-Storage Management

- **mass storage** refers to **non-volatile storage devices** like **magnetic disks**, **solid-state drives**, and **optical disks** that store data **long-term** — even when power is off.
- Entire speed of computer operation hinges on disk subsystem and its algorithms
- Usually disks used to store data that does not fit in main memory or data that must be kept for a “long” period of time
- Proper management is of central importance
- OS activities include:
  - Free-space management: It maintains a **free-space list** so new files can be allocated efficiently.
  - Storage allocation: Determines **where** and **how** files are stored on disk
  - Disk scheduling: When many I/O requests compete for the disk, the OS must decide **which request to serve first**.
  - Tertiary Storage: Some storage doesn’t need to be fast but is used for **backup and archival** purposes. Examples: **optical disks** (CDs, DVDs), **magnetic tapes**.
    - The OS (or an application) still must **manage it** — decide what to store, when to retrieve, and how to organize data
    - **Two main types**:
    1. **WORM (Write Once, Read Many)** – data can be written only once (like a CD-R).
    2. **RW (Read/Write)** – can be rewritten many times (like a hard drive or DVD-RW).

## Cache Management

- Movement of information between various levels of storage hierarchy can be explicit or implicit
- Careful selection of cache size and replacement policy can result in greatly increased performance

### Migration of Integer A from Disk to Register

![image](Imgs/Pasted%20image%2020251112170647.png)

- In a **multitasking system**, several processes or threads might try to read or modify the same variable `A`. So the operating system (and hardware) must make sure that:

> No matter where `A` is stored — in disk, memory, cache, or register — every part of the system uses the **most recent value**.

Otherwise, one process might read an **old version** of `A` while another has already updated it in a different memory level.

- In a system with **multiple CPUs**, each processor has its **own cache** do the value of A might be different from CPU to another so the fix?
- Cache Coherency:

> It means keeping all CPU caches synchronized so that every processor always sees the most up-to-date version of any shared data.

- Think of it like:

> Everyone sharing a Google Doc — if one person edits a line, everyone else’s version updates instantly.

- In **distributed systems**, data may be copied across **different computers** (not just CPUs).
- Here, maintaining consistency is much harder because:
    - Data may exist in **multiple copies across networks**
    - Some nodes may be **temporarily offline**
    - Delays cause some nodes to use **old data**

- These systems use **distributed consistency protocols** (like replication and locking algorithms) to ensure correctness — but it’s much slower than local cache coherency.


## I/O Subsystem

- **I/O subsystem** is described as the part of the operating system that **manages input and output operations** and provides a **uniform interface** between hardware devices and user programs.

- Purpose:
  > To hide the _individuality_ (differences) of hardware devices from users and applications.
  
- So, when you read a file, print a document, or send data over the network, you don’t need to know **how** the hardware actually works — the OS handles that complexity through the I/O subsystem.
- I/O subsystem responsible for:
    -  Memory management of I/O including:
         - buffering - storing data temporarily while it is being transferred
         - caching - storing frequently used data in faster storage for performance
         - spooling - Allows **overlapping of I/O operations** — one process’s output can be used as another’s input later.
     - General Device-Driver Interface: The OS provides a **common interface** for all I/O devices, This means programs use the same commands for different hardware types (e.g., “read” or “write”), and the OS handles the specifics.
     - Drivers for Specific Hardware Devices: Each device has its own **device driver**, which is software that directly communicates with the hardware, The driver translates OS commands into hardware-specific instructions.


![image](Imgs/Pasted%20image%2020251112204252.png)

# Protection and Security

- Protection – any mechanism for controlling access of processes or users to resources defined by the OS
- Security – defense of the system against internal and external attacks (Huge range, including denial-of-service, worms, viruses, identity theft, theft of service)

## Access Control

- Before applying protection, the OS must know **who the user is** and what they’re allowed to do.
- Each user has a **unique identifier** (name + numeric ID).
- A **group ID** represents a collection of users with shared permissions.
- _Privilege escalation_ allows changing the **effective user ID** to gain higher privileges.

# Computing Environments

- Traditional:
     - Stand-alone general purpose machines
     - Indistinct as most systems interconnect with others (i.e. the Internet)
     - Portals provide web access to internal systems (A company has its own portal)
     - Mobile computers interconnect via wireless networks
     - Networking becoming ubiquitous - even home systems use firewalls to protect home computers from Internet attacks
     - Network computers (thin clients) are like Web terminals - more security or easier maintenance
 - Mobile:
     - Handheld smart phones, tablet computers, etc
     - More OS features (GPS, gyroscope, taking photos, and recording videos)
     - Allows new types of apps
- Distributed:
     - Collection of separate, possibly heterogeneous, systems networked together
     - Network is a communications path, TCP/IP most common (LAN, WAN, MAN, PAN, ...etc)
     - Network Operating System provides features (as file sharing) between systems across network:
         - Communication scheme allows systems to exchange messages, Impression of a single system.
- Client-Server:
     - Many systems now servers, responding to requests generated by clients
     - Compute-server system provides an interface to client to request services (i.e., database)
     - File-server system provides interface for clients to store and retrieve files
    ![image](Imgs/Pasted%20image%2020251112210720.png)
- Peer-to-Peer:
     - Another model of distributed system
     - P2P does not distinguish clients and servers, Instead all nodes are considered peers. May each act as client, server or both.
     - Registers its service with central lookup service on network, or Broadcast request for service and respond to requests for service via discovery protocol
     ![image](Imgs/Pasted%20image%2020251112211002.png)
 - Virtualization:
     - Allows operating systems to run as applications within other OSs.
     - Emulation used when source CPU type different from target CPU type (Generally slowest method).
     - When computer language not compiled to native code –> Interpretation
     - Virtualization –> OS natively compiled for CPU, running guest OSs also natively compiled
    ![image](Imgs/Pasted%20image%2020251112211154.png)
- Cloud Computing:
     - Delivers computing, storage, even apps as a service across a network
     - Logical extension of virtualization as based on virtualization
     - Cloud compute environments composed of usual OSs plus VMMs and cloud management tools
         - Internet connectivity requires security like firewalls
         - Load balancers spread traffic across multiple applications
     ![image](Imgs/Pasted%20image%2020251112211529.png)
     - Many types:
         - Public cloud – available via Internet to anyone willing to pay
         - Private cloud – run by a company for the company’s own use
         - Hybrid cloud – includes both public and private cloud components
         - Software as a Service (SaaS) – one or more applications available via the Internet (i.e. word processor)
         - Platform as a Service (PaaS) – software stack ready for application use via the Internet (i.e a database server)
         - Infrastructure as a Service (IaaS) – servers or storage available over Internet (i.e. storage available for backup)
- Real-Time Embedded Systems
     - Vary considerable, special purpose, limited purpose OS, real-time OS
     - Usually have little or no user interface
     - Some have OSs, some perform tasks without an OS
     - Real-time OS has well-defined fixed time constraints:
         - Processing must be done within constraint
         - Correct operation only if constraints met


# Open-Source Operating Systems

- Operating systems made available in source-code format rather than just binary closed-source
- Counter to the copy protection and Digital Rights Management (DRM) movement
- Started by Free Software Foundation (FSF), which has “copyleft” GNU Public License (GPL)
- Examples include GNU/Linux and BSD UNIX (including core of Mac OS X), and many more