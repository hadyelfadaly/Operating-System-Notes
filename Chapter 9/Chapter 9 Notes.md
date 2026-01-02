# Background

- Code needs to be in memory to execute, but entire program rarely used, Entire program code not needed at same time, we may only need parts or specific functionalities from it, Loading everything into RAM is wasteful
- The ability to execute partially-loaded program (dynamic loading) load only the parts that are actually needed not the whole program at once:
    - Program no longer constrained by limits of physical memory
    - Each program takes less memory while running so More programs can be in memory at the same time This increases multiprogramming
    - Increased CPU utilization and throughput with no increase in response time or turnaround time, CPU is less likely to be idle
    - Less I/O needed to load or swap programs into memory
- **Virtual memory** – separation of user logical memory from physical memory
    - Only part of the program needs to be in memory for execution
    - Logical address space can therefore be much larger than physical address space
    - Allows address spaces to be shared by several processes
    - Allows for more efficient process creation
    - More programs running concurrently (as i load only needed pages and parts i will have more space for several programs at the same time)
    - Less I/O needed to load or swap processes
- **Virtual address space** – logical view of how process is stored in memory
    -  Usually start at address 0, contiguous addresses until end of space
    - Meanwhile, physical memory organized in page frames
    - MMU must map logical to physical
- Virtual memory can be implemented via:
    -  Demand paging
    - Demand segmentation
![Pasted image 20260102072414.png](./Imgs/Pasted%20image%2020260102072414.png)

- Instead from copying programs from backing store (disk) to the RAM, we now go first to the virtual memory and select the pages needed not all the program and then goes to memory map to get it into the frames we want. 

# Demand Paging

- bring a page into memory only when it is needed
    -  Less I/O needed, no unnecessary I/O
    - Less memory needed
    - Faster response (as i will load only 5 pages needed instead of 100) 
    - More users can access RAM, achieves concurrency
- Similar to paging system with swapping
- Page is needed – reference to it
    - invalid reference -> abort (not related to my process)
    - valid but not-in-memory -> bring to memory (swap in) by lazy swapper
- **Lazy swapper** – never swaps a page into memory unless page will be needed, Swapper that deals with pages is a **pager**
- With swapping, pager guesses which pages will be used before swapping out again, pager brings in only those pages into memory
- How to determine that set of pages? - Need new MMU functionality to implement demand paging
- If pages needed are already memory resident No difference from non demand-paging
- If page needed and not memory resident, Need to detect and load the page into memory from storage Without changing program behavior (for example if i am listening to a song i don't want it disturbed or cut) and Without programmer needing to change code (OS do this operation)

## Valid-Invalid Bit

- With each page table entry a valid–invalid bit is associated
    - v  -> in-memory – memory resident
    - i -> not-in-memory
- Initially valid–invalid bit is set to i on all entries, During MMU address translation, if valid–invalid bit in page table entry is i -> page fault

### Page Fault

- If there is a reference to a page, first reference to that page will trap to operating system: page fault
    1. Operating system looks at the process’s internal table to decide:
        -  Invalid reference -> abort (doesn't exist or belong to another process)
        - Just not in memory
    2. Find free frame
    3. Swap page into frame via scheduled disk operation
    4. Reset tables to indicate page now in memory (Set validation bit = v )
    5. Restart the instruction that caused the page fault (now it will find it valid so it will continue execution)
![Pasted image 20260102080052.png](./Imgs/Pasted%20image%2020260102080052.png)

- Always happens in demand paging as we start with no pages (pure demand paging)

## Aspects of Demand Paging

- Extreme case – start process with no pages in memory 
    - OS sets instruction pointer to first instruction of process, non-memory-resident -> page fault
    - And for every other process pages on first access (Pure demand paging)
- Actually, some instructions could access multiple new pages -> multiple page faults per instruction
    - Consider fetch and decode of instruction which adds 2 numbers from memory and stores result back to memory
    - Pain can be decreased using locality of reference
    - Locality of reference is the property that programs tend to access the same or nearby memory locations repeatedly within a short time interval. 
- Hardware support needed for demand paging:
    - Page table with valid / invalid bit
    - Secondary memory (swap device with swap space)
    - Ability to restart any instruction after a page fault

## Instruction Restart

- Must be able to restart the instruction in exactly the same place and state, Except that the desired page is now in memory and is accessible
- Consider an instruction that could access several different locations as Some instructions don’t just read/write one memory address.
    - **block move**: A block move instruction copies a whole block of memory, This is one instruction, but internally:
        - Reads from A[0], A[1], A[2], …
        - Writes to B[0], B[1], B[2], …
        - If a page fault happens in the middle, the instruction is half-done.
    - **auto increment/decrement location**: Some instructions automatically update pointers, If a page fault happens Did pointer already increment Or not yet? The CPU must know exactly
    - **source and destination overlap**: Example: copy array[0..9] → array[2..11], Source and destination share memory so Order of copying matters f the instruction partially executes and then restarts incorrectly Data can be corrupted so the CPU must ensure Either the instruction fully completes, Or it leaves no visible partial effects
- That's why we need to restart whole operation, From the exact same state Without duplicating or skipping work. This is hard if the instruction already modified registers, memory or pointers.

## Performance of Demand Paging

Stages in Demand Paging (worse case)

1. Trap to the operating system
2. Save the user registers and process state
3. Determine that the interrupt was a page fault
4. Check that the page reference was legal and determine the location of the page on the disk
5. Issue a read from the disk to a free frame:
    1. Wait in a queue for this device until the read request is serviced
    2. Wait for the device seek and/or latency time
    3. Begin the transfer of the page to a free frame
6. While waiting, allocate the CPU to some other user (process)
7. Receive an interrupt from the disk I/O subsystem (I/O completed)
8. Save the registers and process state for the other process
9. Determine that the interrupt was from the disk
10. Correct the page table and other tables to show page is now in memory
11. Wait for the CPU to be allocated to this process again
12. Restore the user registers, process state, and new page table, and then

In these steps there are 3 major activities:

1. Service the interrupt - careful coding means just several hundred instructions needed
2. Read the page – lots of time
3. Restart the process – again just a small amount of time

- Page Fault Rate (Probability) 0 <= p <= 1
    -  if p = 0 no page faults
    -  if p = 1, every reference is a fault
- Effective Access Time (EAT) = (1-p) x memory access + p (page fault overhead + swap page out + swap page in)
    - (1-p) the probability of finding the page  
    - p (page fault overhead + swap page out + swap page in) -> page not in memory

## Demand Paging Optimizations

- Swap space I/O faster than file system I/O even if on the same disk
    - Swap space is raw disk space managed by the OS, allocated in large, contiguous blocks, less management needed than file system -> faster reads/writes
    - File system I/O has extra overhead (directories, permissions, metadata), So even if both are on the same disk, accessing swap is faster.
- Copy entire process image (code, data, stack) to swap space at process load time for faster paging and simpler management (Used in older BSD Unix)
- Demand pages for program binary files as they are never modified (Program binary pages (code pages) is read-only and never changes)
    - If such a page is replaced then no need to write it to disk we will just discard it as it never changed from its version on disk and we can Reload it later from the executable file. (Used in Solaris and current BSD)
    - Still need to use swap space for pages not associated with a file (anonymous memory) as these pages doesn't exist in any file and may be modified so They must be written to swap before replacement.

## Copy-on-Write (COW)

Copy-on-Write allows parent and child processes to initially share the same memory pages. A page is copied only when one of the processes attempts to modify it, reducing memory usage and improving process creation efficiency. Free frames used for copying are taken from a pool of zero-fill-on-demand pages to ensure fast page-fault handling.

- Allow both parent and child processes to initially share the same pages in memory
    -  if either process writes to a shared page, a copy of the shared page is created
    - Use virtual `vfork()` variation on `fork()` system call
- COW allows more efficient process creation as only modified pages are copied
- OS keeps a pool of free frames that are filled with zeros, clean and safe (zero-fill-on-demand pages). When a copy is needed OS takes a frame from this pool and uses it immediately
- Pool should always have free frames for fast demand page execution as OS should not waste time freeing frames -> faster page fault handling
- Zero-fill-on-demand pages have been zeroed out before being allocated to guarantee no old data leaks and security + correctness.

## What Happens if There is no Free Frame?

- **Page replacement** – find some page in memory, but not really in use, page it out
- Prevent over-allocation of memory by modifying page-fault service routine to include page replacement
- Use modify (dirty) bit to reduce overhead of page transfers Only modified pages are written to disk and if the bit = 0 (no modification) we can drop it directly.
- Same page may be brought into memory several times
- Page replacement completes separation between logical memory and physical memory and Large virtual memory can be provided on a smaller physical memory
![Pasted image 20260102090105.png](./Imgs/Pasted%20image%2020260102090105.png)

- Here user 2 cant find free frame to load B
# Page Replacement

Basic Page Replacement

1. Find the location of the desired page on disk
2. Find a free frame:
    -  If there is a free frame, use it
    - If there is no free frame, use a page replacement algorithm to select a victim frame and Write victim frame to disk if dirty.
3. Bring the desired page into the (newly) free frame; update the page and frame tables.
4. Continue the process by restarting the instruction that caused the trap 

Note: if no frames are free, two page transfers are required for page fault – increasing EAT)

![Pasted image 20260102090504.png](./Imgs/Pasted%20image%2020260102090504.png)

## Page and Frame Replacement Algorithms

- **Frame-allocation algorithm** determines How many frames to give each process
- **Page-replacement algorithm** 
    - Which frames to replace
    - Want lowest page-fault rate on both first access and re-access
- Evaluate each algorithm by running it on a particular string of memory references (reference string) and computing the number of page faults on that string
    - Repeated access to the same page does not cause a page fault
    - Results depend on number of frames available
    - String is just page numbers, not full addresses
- Designing appropriate algorithms to solve these problems is an important task, because disk I/O is so expensive, Even slight improvements in demand-paging methods yield large gains in system performance.
- When number of frames increase number of page fault decreases

## First-In-First-Out (FIFO) Algorithm

- track ages of pages using FIFO Algorithm
- First in process is the first one out when we need to replace
- Adding more frames can cause more page faults -> **Belady’s Anomaly** 

## Optimal Algorithm

- Replace page that will not be used for longest period of time
- Not realistic because we cant read the future
- Used for measuring how well your algorithm performs

## Least Recently Used (LRU) Algorithm

- Use past knowledge rather than future
- Replace page that has not been used in the most amount of time, Associate time of last use with each page
- Number of faults better than FIFO but worse than OPT
- Generally good algorithm and frequently used
- LRU needs special hardware and still slow

### Counter implementation

- Every page-table entry has a time-of-use field; whenever a reference to a page is made, the contents of the clock are copied to this field its entry.
- Replace the page with the smallest time value, Require a search of the entire page table.

### Stack Implementation

- Keep a stack of page numbers, whenever a page is referenced, it is removed and put on top of the stack
- Least recently used page is at bottom of the stack
- This approach uses a doubly linked list with a head and a tail pointers
- Each update is a more expensive, but there is no search for a replacement and requires 6 pointers to be changed

### LRU Approximation Algorithms

- **Reference bit** 
    - With each page associate a bit, initially = 0
    - When page is referenced bit set to 1
    - Replace any with reference bit = 0 (if one exists), we don't know the order so any one with bit = 0
- **Second-chance algorithm** 
    - Generally FIFO plus hardware-provided reference bit
    - Clock replacement
    - If page to be replaced has:
        - Reference bit = 0 -> replace it
        - Reference bit = 1 then set reference bit 0, leave page in memory, replace next page, subject to same rules
![Pasted image 20260102102140.png](./Imgs/Pasted%20image%2020260102102140.png)

- here at bits = 1 we set them to 0 and took the next 0 bit as the victim
- **Enhanced Second-Chance Algorithm** Improve algorithm by using reference bit and modify bit (if available) in concert
    1. (0, 0) neither recently used not modified – best page to replace
    2. (0, 1) not recently used but modified – not quite as good, must write out before replacement
    3. (1, 0) recently used but clean – probably will be used again soon
    4. (1, 1) recently used and modified – probably will be used again soon and need to write out before replacement
- Enhanced Second-Chance classifies pages using (reference, modify) bits and replaces the lowest-priority class, possibly requiring multiple scans of the clock queue.

## Counting Algorithms

- Keep a counter of the number of references that have been made to each page (not common)
- **Lease Frequently Used (LFU) Algorithm**: replaces the page with smallest count
    - Based on an actively used page should have a large reference count
    - A problem when a page is used heavily during initial phase of a process but then is never used again (it will have a large count but wont be used again)
- **Most Frequently Used (MFU) Algorithm**: replaces the page with largest count
    - Based on the argument that a page with the smallest count was probably just brought in and has yet to be used 

## Page-Buffering Algorithms

**Page-Buffering Algorithms**, are optimizations added on top of page-replacement algorithms to reduce the cost of page faults.

- Page buffering does not replace FIFO, LRU, Clock, etc. It works alongside them to make page replacement faster.
- OS Always Keep a pool of free frames ready So when a page fault happens → a free frame is immediately available. Page faults become faster, because we don’t delay execution to search for a victim.
- Instead of page fault → find victim → evict → load page we do:
    1. Read the needed page into a free frame
    2. Later, select a victim page
    3. Remove the victim and add it to the free-frame pool
- Victim selection is **decoupled** from the fault itself.
- Keep a list of modified (dirty) pages If the backing store (disk) is idle, write dirty pages in advance and mark them as clean. When such a page is later chosen as a victim No need to write it to disk and Replacement becomes faster.
- Keep free-frame contents undamaged and note what is in them. Just mark it as free. If the page is referenced again before reuse No disk access needed and Page fault is avoided (soft replacement). Useful to reduce penalty if wrong victim frame selected.

# Virtual Memory Allocation Schemes

- Each process needs minimum number of frames
- When Instruction is restarted after page fault, we must have enough frames to hold all different pages that any single instruction can reference.
- Maximum of course is total frames in the system

## Fixed Allocation

- **Equal Allocation** Divide number of frames on number of Processes so all processes get the same number of frames equally, but we Keep some as free frame buffer pool
- **Proportional allocation** Allocate according to the size of process, Dynamic as degree of multiprogramming, process sizes change
    - formula = $\frac{s_i}{S} \times m$ , $s_i$ = size of process $p_i$ , S = sum of all processes sizes, m = total number of frames.

## Priority Allocation

Use a proportional scheme wherein ratio of frames depends on priorities of processes rather than on relative sizes of processes or on a combination of size and priority

- If a process  generates a page fault:
    - select for replacement one of its frames
    - select for replacement a frame from a process with lower priority number

## Global vs. Local Allocation

 - **Global replacement** – a process selects a replacement frame from the set of all frames; one process can take a frame from another
    - process execution time can vary greatly
    - greater throughput so more common
- **Local replacement** – each process selects from only its own set of allocated frames
    - More consistent per-process performance
    - But possibly underutilized memory

# Thrashing

**Thrashing** = a process is busy swapping pages in and out

- If a process does not have “enough” pages, the page-fault rate is very high
    - Page fault to get page and Replace existing frame But quickly need replaced frame back This lead to: 
        - Low CPU utilization
        - Operating system thinking that it needs to increase the degree of multiprogramming
        - Another process added to the system
- A process is Thrashing if it is spending more time paging than executing
![Pasted image 20260102110151.png](./Imgs/Pasted%20image%2020260102110151.png)

- To prevent thrashing, we must provide a process with as many frames as it needs. But how do we know how many frames it “needs”?
-  **Locality model** of process execution looks at how many frames a process is actually using
    - Locality is a set of pages that are actively used together
    - As a process executes, it moves from locality to another
- Why does thrashing occur?
    -   Do not allocate enough frames to accommodate the size of current locality of each process, sum of size of processes locality > memory size
- we can limit effects Limit effects by using local or priority page replacement

# Working-Set Model

The **working set model** is a memory management model that defines the set of pages a process has referenced during the most recent fixed window of time, and assumes that this set represents the pages the process needs to execute efficiently.

- $\Delta$  = working-set window = a fixed number of page references
- This working set is an approximation of the program’s locality
    - If a page is in active use, it will be in the working set
    - If it is no longer being used, it will drop from the working set $\Delta$  time units after its last reference
- Accuracy of the working set depends on the selection of $\Delta$:
    -  if $\Delta$ too small will not encompass entire locality
    -  if $\Delta$ too large will not encompass several localities
    -  if $\Delta$ = $\infty$  will encompass entire program
- We compute the working-set size, $WSS_i$ , for each process in the system 
- D = sum of all $WSS_i$ = total demand frames
- if D > m -> Thrashing, then suspend or swap out one of the processes

## Keeping Track of Working Set

- Can approximate the working-set model with a fixed-interval timer interrupt and a reference bit
- Timer Interrupts every fixed time units and we keep in memory 2 bits for page
- Whenever a timer interrupts copy and sets the values of all reference bits to 0
- If one of the bits in memory = 1 -> page in working set
- Why is this arrangement not entirely accurate?
    - Because we cannot tell where a reference occurred
    - Improvement: 10 bits and interrupt every 1000 references

# Page-Fault Frequency

- More direct approach than WSS
- Establish “acceptable” page-fault frequency (PFF) (upper threshold and lower threshold) rate and use local replacement policy
    - If actual rate too low, process loses frame 
    - If actual rate too high, process gains frame
![Pasted image 20260102112436.png](./Imgs/Pasted%20image%2020260102112436.png)

- There is a direct relationship between working set of a process and its page-fault rate
    - Working set changes over time as references to data and code sections move from one locality to another  
    - Page-fault rate of the process will transition between peaks and valleys over time
![Pasted image 20260102112744.png](./Imgs/Pasted%20image%2020260102112744.png)

- When execution enters a new locality:
    - New pages are needed
    - Page faults spike (peak)
- After pages are loaded:
    - Working set is fully in memory
    - Page faults drop (valley)