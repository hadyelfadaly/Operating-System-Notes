# Operating Systems Services

- There are services for users and others for the system

## User Services

1. User Interface like Command-Line interface, GUI, Batch interface (every thing works automatically)
2. Program Execution: The system must be able to load a program into memory and to run that program, end execution, either normally or abnormally (indicating error)
3. I/O Operations: A running program may require I/O, which may involve a file or an I/O device, the OS handles this operation, the program shouldn't be busy with it.
4. File-System Manipulation: The OS handles rename, read, write, ..etc operations and The OS also handles permissions. 
5. Communications: Processes may exchange information, on the same computer or on different computers over a network, The OS has protocols to allow that.
6. Error Detection: OS needs to be constantly aware of possible errors in CPU, I/O Devices, In user program, memory hardware, ..etc
     - Based on the Error OS should take the appropriate action to ensure correct and consistent computing

## System Services

1. Resource Allocation: When multiple users or multiple jobs running concurrently, resources must be allocated to each of them
     - CPU Cycles: how much each job will take from CPU time
     - Main memory, file storage and I/O Devices may have general request for resources and release code to notify resource is now available for use after I/O Device finishes.
2. Accounting: To keep track of which users use how much and what kinds of computer resources
3. Protection and Security
     - Protection involves ensuring that all access to system resources is controlled
     - Security of system from outsiders requires user authentication, extends to defending external I/O devices from invalid access attempts

![image](Imgs/Pasted%20image%2020251114003122.png)

# User Operating System Interface

## Command-Line Interface (CLI)

- CLI or command interpreter allows users to enter commands that performed by OS
- Sometimes implemented in kernel, sometimes by systems program (shells)
- Kernel implementation is faster but modification is more difficult than shells implementation
- Shells has multiple implementations
     - First Implementation: Every command has its own code that executes it -> built in execution
     - Second Implementation: Every Command is a program in itself, if a new command is added we will do new program not a new WHOLE shell (doesn’t require shell modification)

## GUI

- User-friendly desktop metaphor interface
- Instead of writing commands Various mouse buttons over objects in the interface cause various actions (provide information, options, execute function, open directory – known as a folder)
- Touchscreen devices require new interfaces:
     - Mouse not possible or not desired
     - Actions and selection based on motions
     - Virtual keyboard for text entry

# System Calls

- A way for any program to request any service from the OS (Programming interface to the services provided by the OS) Usually written in C or C++
- System Calls is not programmer's responsibility, It is API responsibility. As System Calls is So low level and differs from one OS to another for the programmer to handle.

## System Calls Implementation

- OS have for every System Call An ID or unique ID, System-call interface maintains a table indexed according to these numbers
- The system call interface invokes proposed system call in OS kernel and returns status of the system call and any return values
- The caller need know nothing about how the system call is implemented, Just needs to follow API and understand what OS will do as a result call

![Image](Imgs/Pasted%20image%2020251114005415.png)

- System Call Interface -> System Call Table -> Invoke System Call -> Get Work done -> Returns the result. 

## System Call Parameter Passing

- Often, more information is required than simply identity of desired system call
- Example: if you want to copy a file you must provide what file you want to copy and where do you want to copy it
- Three general methods used to pass parameters to the OS:
     1. Pass The parameters to the registers (CPU registers) directly. (Simplest Method but have a challenge if number of parameters is more than number of registers)
     2. Store parameters in a block and pass the block's address to the register. (Used by Linux)
     3. Parameters pushed into stack by a program and popped off by the OS
- Block and stack methods do not limit the number or length of parameters being passed

## Type of Calls

1. Process Control: To Create, Manage and terminate process
2. File Management: Create, delete, open, close, get and set file attributes, ....etc.
3. Device Management: To communicate with hardware devices (Request device , release device, read, write, get device attributes, ...etc)
4. Information Maintenance: get and set time or date, get or set system data, Generally makes data consistent
5. Communications: create, delete communication connection, send and receive messages, ..etc
6. Protection: Control access to resources, Get and set permissions.

# System Programs

- System programs provide a convenient environment for program development and execution.
## Types of System Programs

1. File management: Create, delete, copy, rename, print, dump, list, and generally manipulate files and directories
     - File Manipulation: Like renaming or moving from folder to folder
     - File Modification: Modify the file content itself.
2. Status information: Some ask the system for info - date, time, amount of available memory, disk space, number of users or provide detailed performance, logging, and debugging information
     - Registry: Its something like a database that stores everything about the system, downloaded programs, user preferences, ...etc (Configuration Information)
3. File modification: Text editors to create and modify files, Special commands to search contents of files or perform transformations of the text
4. Programming-language support: Like IDEs, Compilers, assemblers, debuggers and interpreters sometimes provided
5. Program loading and execution
     - Absolute Loader: Loads program to fixed address that its always loads in (if it doesnt find its address it wont load)
     - Relocatable Loader: Loads program anywhere in memory (opposite to Absolute loader)
     - Linkage Editor: Combines modules and resolves references (combines program files so the program can load)
     - Overlay Loader: Loads program parts as needed (like watching a movie on netflix it doesnt load the whole movie but part by part)
     - Debugging System: Finds and fixes program errors
6. Background service: Always-running helper program (programs that run in the background when the OS is launched) Runs in user mode not kernel mode so it can be more secure, it provide facilities like disk checking, process scheduling, error logging, printing
7. Application Programs

# Operating System Design and Implementation

- No universal design or template to follow, depends on hardware and what do you want from your OS to do.
- But some approaches have proven successful.

## Goals

- User goals – operating system should be convenient to use, easy to learn, reliable, safe, and fast
- System goals – operating system should be easy to design, implement, and maintain, as well as flexible, reliable, error-free, and efficient

- Important principle to separate
    - Policy: What will be done? (Faculty bylaw(Can be changed))
    - Mechanism: How to do it? (The way to calculate GPA (same way always))

- Specifying and designing OS is highly creative task of software engineering

## Implementation

- Early OSs in assembly language, Now C, C++, Actually usually a mix of languages
     - Lowest levels in assembly
     - Main body in C
     - Systems programs in C, C++, scripting languages like PERL, Python, shell scripts
- More high-level language easier to port to other hardware but slower
- Emulation can allow an OS to run on non-native hardware

# Operating System Structure

- General-purpose OS is very large program, so divided into structures to be easier in implementation and management

## Simple Structure 

- It is simple because it is not divided well (Not divided into modules)
- written to provide the most functionality in the least space

## Layered Approach

- The operating system is divided into a number of layers (levels), each built on top of lower layers.
- The bottom layer (layer 0), is the hardware; the highest (layer N) is the user interface.
- With modularity, layers are selected such that each uses functions (operations) and services of only lower-level layers
- Example: UNIX - Beyond Simple but not fully layered

![Image](Imgs/Pasted%20image%2020251114014120.png)

## Microkernel System Structure

- Have the kernel as small as possible
- Moves as much from the kernel into user space
- Communication takes place between user modules using message passing
- Benefits: 
     1. Easier to extend a microkernel
     2. Easier to port the operating system to new architectures
     3. More reliable (less code is running in kernel mode)
     4. More secure
- Disadvantages: 
     1. Performance overhead of user space to kernel space communication
- Example: Mac OS
![Images](Imgs/Pasted%20image%2020251114014628.png)
## Modules

- Most modern operating systems implement loadable kernel modules
- OS is divides into various models, Uses object-oriented approach
- Each core component is separate
- Each talks to the others over known interfaces
- Each is loadable as needed within the kernel
- Overall, similar to layers but with more flexible (Every class can communicate with another class while in layer approach if one layer want to communicate with another layer it has to pass by all the layers between them)
- Example: Linux, Solaris
![Images](Imgs/Pasted%20image%2020251114015021.png)

## Hybrid Systems

- Most modern operating systems actually not one pure model, Hybrid combines multiple approaches to address performance, security, usability needs
- Example: Mac OS (Layered and Microkernel)

## IOS

- Structured on Mac OS X, added functionality
- Does not run OS X applications natively though

## Android

- Based on Linux kernel but modified

# Operating-System Debugging

- Debugging is finding and fixing errors, or bugs. OSs generate log files containing error information.
- Failure of an application can generate **core dump file** capturing memory of the process
- Operating system failure can generate **crash dump file** containing kernel memory
- Profiling is periodic sampling of instruction pointer to look for statistical trends ex: what applications takes most of the resources


## Performance Tuning

- Improve performance by removing bottlenecks
- OS must provide means of computing and displaying measures of system behavior
- Example: Task Manager

# Operating System Generation

- Operating systems are designed to run on any of a class of machines; the system must be configured for each specific computer site
- SYSGEN program obtains information concerning the specific configuration of the hardware system, Used to build system-specific compiled kernel or system-tuned
- Can general more efficient code than one general kernel

# System Boot

- When power initialized on system, execution starts at a fixed memory location that held in firmware ROM.
- This is a step before the bootstrap step
- Firmware -> boot code -> bootstrap -> kernel -> OS
- Common bootstrap loader, GRUB, allows selection of kernel from multiple disks, versions, kernel options
- Kernel loads and system is then running
