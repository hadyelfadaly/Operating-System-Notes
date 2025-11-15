---
share: true
---
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

![Image](Imgs/Pasted%20image%2020251115203944.png)
- Long Term scheduler or Job Scheduler is the one who admits a process from new to ready if the needed resources is available

Upload single current active note
