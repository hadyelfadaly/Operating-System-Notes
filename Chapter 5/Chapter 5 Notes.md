
# Background – The Problem

- Processes can execute concurrently May be interrupted at any time, partially executed
- Concurrent access to shared data may result in data inconsistency
- Maintaining data consistency requires mechanisms to ensure orderly execution of cooperating processes

# Producer-Consumer Problem with Counter

- we want to provide a solution to the consumer-producer problem that fills all the buffers.
- we can use a **counter** to keep track of how many items are in the buffer:
    - **Initially, the counter is set to 0** (the buffer is empty).
    - The **producer** increments the counter each time it adds an item.
    - The **consumer** decrements the counter each time it consumes an item.
- Producer code:
```C
while(true) 
{

    /* produce an item in next_produced */
    while (counter == BUFFER_SIZE) ; // do nothing if buffer is full
    
    buffer[in] = next_produced; // Add item to the buffer
    in = (in + 1) % BUFFER_SIZE; // Move to the next position in the buffer
    counter++; // Increment the counter
    
}
```
- Consumer code:
```C
while(true) 
{

    while (counter == 0) ; // Do nothing if buffer is empty
    
    next_consumed = buffer[out]; // Take the item from the buffer
    out = (out + 1) % BUFFER_SIZE; // Move to the next item in the buffer
    counter--; // Decrement the counter
    /* consume the item in next_consumed */
    
}
```

# Race Condition

- The counter ++ or counter-- is not one atomic step in real CPUs, It _looks_ like one line in code, but the CPU actually executes it as **three separate instructions**:
     - **Load** counter into a register (register1 = counter)
     - **Modify** the register (add 1 or subtract 1) (register1 = register1 + 1 or register2 = register2 - 1)
     - **Store** the register value back into counter (counter = register2)
- The CPU executes the three low-level operations separately, and **a context switch can happen at any moment**, causing interleaving.
- If counter = 5 and two threads run:
    - Producer: counter++
    - Consumer: counter--
    - Because the steps interleave, the final result can become **4**, **6**, **5**, or something incorrect — depending on timing

![Pasted image 20251116004015.png](./Imgs/Pasted%20image%2020251116004015.png)

## Critical Section

- A **critical section** is a part of the program where a **shared variable** or shared resource is accessed.
- So counter is shared variable because multiple threads use it, Whenever two processes/threads access shared data at the same time → **danger**.

## Critical Section Problem

- It’s the problem of ensuring that only ONE process/thread executes inside its critical section at any time. Why?
     - Race conditions
     - Inconsistent data
     - Corrupted states
- So the OS must enforce rules so threads don't break each other’s operations.
- Each process must ask permission to enter critical section in entry section, may follow critical section with exit section, then remainder section

## Solution to Critical-Section Problem

- The Critical Section Problem asks us to design a solution that satisfies 3 requirements:
     1. Mutual Exclusion: Only **one** process can be in its critical section at a time.
     2. Progress: If no thread is in its critical section, then any thread _wanting_ to enter should eventually be able to — **no infinite waiting**.
     3. Bounded Waiting: A process must not wait forever. There must be a bound on how many times other processes can enter before it gets its turn.

## Peterson’s Solution

- Peterson’s algorithm is a **software-only** solution for the **Critical Section Problem** for **two processes** (Pi and Pj). No hardware instructions like test-and-set or locks.
- **Shared variables used:**
    1. `flag[i]`  Meaning: “Process _i_ wants to enter the critical section.”
        - `flag[i] = true` → Pi is _ready_ and trying to enter.

    2. `turn` Decides whose turn it is to enter.
        - If `turn = j`, then Pj gets priority if both want to enter.
- Both processes show interest using their `flag[]`.
- **But only one gets priority** using the `turn` variable.
- Algorithm:
```C
do 
{

//Start of Entry Section
flag[i] = true;
turn = j;

while (flag[j] && turn == j);
//End of Entry Section

//critical section

flag[i] = false; //Exit Section

//remainder section

} while(true);
```

# Synchronization Hardware

- Many systems provide hardware support for critical section code.
- All solutions below based on idea of **locking** -> Protecting critical regions via locks.
- Before multi-core CPUs existed, a simple way to avoid race conditions was: Disable interrupts. Meaning:
     - The CPU cannot be interrupted
     - No context switch happens
     - No other process can run
     - The current process finishes its critical section safely
    So the code inside the critical section becomes **atomic** because nothing else can run.
- In multiprocessor (multi-core) systems: Each core runs independently, Disabling interrupts on **one** core does NOT stop the other cores they can still access the shared variable -> race conditions will still happen.
- What do modern systems use instead?
     - Special hardware instructions called _atomic instructions_ 
- These are **machine-level instructions** that:
     - Perform multiple operations in **one atomic, uninterruptible step**
     - Work correctly even on multiple cores
     - Guaranteed by hardware, not software
- Examples: Test-and-Set and Swap contents of two memory words
- Test-and-Set Code:
```C
boolean test_and_set (boolean *target)
{

boolean rv = *target;
*target = TRUE;

return rv:

}
```
- Critical Problem Solution using Test-and-Set (Not satisfy bounded-waiting requirement)
```C
do 
{

while(test_and_set(&lock)); /* do nothing */

/* critical section */

lock = false;

/* remainder section */

} while(true);
```

- `target` is used as a **lock variable**:
     - `target == FALSE` → lock is free
     - `target == TRUE` → lock is taken
- Case 1: lock was FALSE (free)
```C
rv = false;
lock = true;

return false; //This means: "I got the lock."
```
- lock was TRUE (someone already holds it)
```C
rv = true;
lock = true;

return true; //This means: "The lock is busy — I must wait."
```
- Compare and Swap Code:
```C
int compare_and_swap(int *value, int expected, int new_value)
{

int temp = *value;

if (*value == expected) *value = new_value;

return temp;

}
```
-  `value` is used as a **lock variable**:
     - `value == 0` → lock is free
     - `value == 1` → lock is taken
    And it does same logic as Test and Set

## Mutex Locks

- Previous solutions are complicated and generally inaccessible to application programmers (Too low-level, Not scalable)
- OS designers build software tools to solve critical section problem
- Simplest tool is *mutex* lock
-  think of it like a **door lock**:
    - Before entering a room → you lock it (acquire)
    - After leaving → you unlock it (release)
    Only one thread can hold the lock at a time → protects the critical section.
- A mutex lock is built using a simple Boolean variable (often called `available` or `lock`):
    - `available = true` → no one is inside critical section
    - `available = false` → the lock is taken
- To use the lock, you call:
    - **acquire()** → take the lock
    - **release()** → release the lock
    These operations **must be atomic**, Usually implemented via hardware atomic instructions.
- acquire() code:
```C
acquire(boolean *availabe)
{

while(!available); /* busy wait */

available = false;

}
```
- release() code:
```C
release(boolean *availabe) 
{available = true;}
```
- Mutex:
```C
do
{
//acquire lock

//critical section

//release lock

//remainder section

} while (true);
```
- It consumes CPU time → **busy waiting**.
- The thread does not sleep — it actively checks the lock. Hence the name: **spinlock**
- Why is busy waiting bad?
    - Because the CPU wastes time doing nothing:
        - 100% CPU usage for no real work
        - Worse on multiprocessors
        - Causes performance degradation
- Good for very short critical sections

## Semaphore

- Synchronization tool that does not require busy waiting and controls access to shared resources using an **integer value**.
- Semaphore S – integer variable and Two standard operations modify S:wait() and signal() Originally called P() and V()
- Less complicated
- Can only be accessed via two indivisible (atomic) operations:
- wait:
```C
wait(S) 
{
 
while (S <= 0); // busy wait

S--;

}
```       
 - signal:
```C
signal (S) 
{S++;}
```

- Counting semaphore – integer value can range over an unrestricted domain
- Binary semaphore – integer value can range only between 0 and 1 (same as mutex lock)

### Implementation

- The implementation of a semaphore (wait & signal) must itself be protected from race conditions.
- The semaphore’s internal variable (like its counter) is a **shared variable**, so it must be updated **atomically**, or else race conditions will happen.
- Semaphore Could now have busy waiting in critical section implementation Yes — but this busy waiting is:
    - **very short**
    - only a few CPU instructions
    - happens only when two processes call wait() or signal() simultaneously
- To avoid busy waiting:
     1. Every semaphore has a waiting queue (This is a _linked list_ of processes that are waiting for the semaphore.) Each node in the list stores:
         1. The process (PCB pointer)
         2. A pointer to the next node (linked list)
     2. Two OS operations: `block()` and `wakeup()`
         - `block()` called inside `wait()`, The OS **puts the process to sleep** -> The process stops running completely -> It no longer uses CPU
         - `wakeup()` Called inside `signal()`, Wakes up **one** process from the semaphore’s waiting queue, Moves it to the **ready queue**, Scheduler will eventually run it
- `wait()`
```C
wait(semaphore S) {
    S.value--;             // attempt to take the resource
    if (S.value < 0) {     // no resource available?
        add this process to S.list;  // join waiting queue
        block();            // go to sleep (NO busy waiting)
    }
}
```
-  `signal()`
```C
signal(semaphore S) {
    S.value++;             // release the resource
    if (S.value <= 0) {    // are processes waiting?
        remove a process P from S.list;  // get next waiting process
        wakeup(P);         // wake it up
    }
}
```

# Deadlock and Starvation

- Deadlock – two or more processes are waiting indefinitely for an event that can be caused by only one of the waiting processes
- Starvation – indefinite blocking -> A process may never be removed from the semaphore queue in which it is suspended
- Priority Inversion: Scheduling problem when lower-priority process holds a lock needed by higher-priority process

# Classical Problems of synchronization

## Bounded-Buffer Problem

- In the solution we use 3 semaphores `mutex`, `full` and `empty`
-  `mutex` = 1, A **binary semaphore** → used like a **lock** to make sure only **one** process (producer or consumer) accesses the buffer at a time.
- `full` A **counting semaphore** Represents how many **full slots** are in the buffer. `full = 0` → buffer is empty, Producer increments it, Consumer decrements it
- `empty` A **counting semaphore** Represents how many **empty slots** are available. Initially buffer is empty → `empty = n` Producer decrements it, Consumer increments it.
- Producer Code:
```C
do 
{

/* produce an item in next_produced */

wait(empty);
wait(mutex);

/* add next_produced to the buffer */

signal(mutex);
signal(full);

} while (true);
```
- Consumer Code:
```C
do
{

wait(full);
wait(mutex);
...
/* remove item from buffer to next_consumed */
...
signal(mutex);
signal(empty);
...
/* consume the item in next consumed */

} while (true);
```

## Readers-Writers Problem

- A data set is shared among a number of concurrent processes
- Readers – only read data set and do not perform any updates
- Writers – can both read and write
- Problem:
     - Allow multiple readers to read at the same time
     - Only one single writer can access the shared data at the same time
- Writer code:
```C
do
{
wait(rw_mutex);
...
/* writing is performed */
...
signal(rw_mutex);

} while (true);
```
- Readers code:
```C
do
{
wait(mutex);
read_count++;
if (read_count == 1)
wait(rw_mutex);
signal(mutex);
...
/* reading is performed */
...
wait(mutex);
read_count--;
if (read_count == 0)
signal(rw_mutex);
signal(mutex);
} while (true);
```
- 1st reader → blocks writers (wait(rw_mutex))
- other readers → just read
- last reader → unblocks writers (signal(mutex))

## Dining-Philosophers Problem

- Philosophers spend their lives thinking and eating, To eat, each philosopher needs **two chopsticks**, Each chopstick is shared by **two neighbors**
- each philosopher tries to:
    1. Pick up the **left** chopstick
    2. Then pick up the **right** chopstick
- Each chopstick is represented by a **semaphore**: Semaphore chopstick [5] initialized to 1
- The structure of Philosopher:
```C
do 
{
wait(chopstick[i] ); //picks left
wait(chopStick[ (i + 1) % 5]); //picks right
// eat
signal(chopstick[i] ); //puts left
signal(chopstick[ (i + 1) % 5]); //puts right
// think
} while(true);
```
- Since all chopsticks start at **1**, all 5 philosophers successfully pick up their **left** chopstick. Now EVERY philosopher is holding ONE chopstick. All left chopsticks are taken and All right chopsticks are ALSO taken  (because they are someone else's left chopstick), Each philosopher waits forever for the right chopstick. (DEADLOCK)

# Problems with Semaphores

- Incorrect use of semaphore operations:
     - signal (mutex) …. wait (mutex)
     - wait (mutex) … wait (mutex)
     - Omitting of wait (mutex) or signal (mutex) (or both)
- Deadlock and starvation