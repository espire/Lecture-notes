>Eli Spiro's lecture notes  
>University of Waterloo  
>CS 341: Algorithms

OS Philosophy
-------------
Some OSs are "small-government" style — a microkernel that provides only very basic system calls such as multithreading and hardware access. BSD has a very thin kernel.
Other OSs are "European socialist", providing lots of social services but also restrictions and performance taxes. Solaris has a lot of application control.

OS Aspects
----------
* Concurrency: keeping things consistent. If two apps use the system concurrently, make sure that one app won't overwrite memory used by the other. If two jobs are sent to the printer, queue the jobs nicely.
* Memory management: Modern OSs use virtual memory addresses. The OS protects applications from each other.
* Interprocess communication: Pipes, message passing, signals, sockets

What is an OS?
==============
Three views of an OS:  

* Application View: providing services for applications to run safely. Open your browser, and it uses the resource of a network adapter. OS gives you services to use the resource, and protects each application from the other one using the same resource. Two files opened for writing, how do we protect the file?
* System View: what problems does the OS solve?
* Implementation View: how is it built? How do I implement a data structure so that two applications can consistently access a location in memory concurrently? How do we ensure atomic execution (all at the same time, with no breaks for multitasking) of code blocks?

Application View
----------------
* The OS provides an execution environment for running programs.
* See course notes
* Execution environment isolates running programs from each other and prevents undesirable interactions among them.

System View
-----------
* Resource management
* An application requests to use a certain amount of memory, and the system provides it if possible
* Principle of locality: the information that you used frequently before will likely be used frequently again. How do we manage that?

Implementation View
-------------------
* OS is a concurrent, real-time program
* OS does not terminate. It can't crash!
* Example:
	* Prog1 obtains the network adapter, obtains memory, does some stuff, then releases the network adapter, then releases the network adapter.
	* Prog2 obtains memory, obtains the network adapter, does some stuff, then releases memory, then releases the network adapter.
	* Deadlock! The two programs are waiting for each other!
	
The OS and the Kernel
=====================
Some terminology:

* kernel: The kernel is the part that responds to system calls, interrupts and exceptions.
	* It's an abstraction layer on the hardware provided to applications.
* Operating system: Includes the kernel, and possibly other related programs that provide services.
	* example: terminal to interpret commands

Use Diagram
-----------
* Resources get commands and data from kernel, provide results
* Kernel gets system calls and data from applications, provides results

Threads
=======
* Within one program, there can be multiple entities that execute concurrently. Each entity is called a thread.
* Processes cannot access each other's address spaces. Threads in most cases are two functions of the same program. They have the same address space.

Synchronization
---------------
* Sometimes, two processes concurrently try to access the same location in memory
* They should not run in an interleaved fashion
* These blocks of code must be executed atomically; no other instructions should be interleaved

OS Abstractions
---------------
* Execution environment includes abstract entities that running processes can manipulate. Examples:
	* Files and filesystems
	* Address spaces: abstract view of secondary storage
	* Processes, threads: abstract view of program execution
	* Sockets, pipes: abstract view of network or other message channels
* This course will cover:
	* why theses abstractions are designed the way they are
	* how they are manipulated by applications
	* how they are implemented by the OS
	
Course Outline
--------------
* Introduction
* Threads and concurrency
* Synchronization
* Processes and the Kernel
* Virtual memory
* Scheduling
* Devices and device management
* File systems
* Interprocess communication and networking (time permitting)

Review: Program Execution
-------------------------
* An operating system is architecture-dependent, since it must be able to access hardware.
* Registers, including the program counter and stack pointer
* Memory
	* Program code
	* Program data
	* Program stack containing procedure activation records

Review: MIPS Register Usage
---------------------------
* See kern/arch/mips/include/asmdefs.h
* See slides

What is a Thread?
-----------------
* When the OS changes threads, it must save the state of that thread. Instruction counter, registers, the stack, the heap
* These together are called the context of a process. When we change processes, we perform _context switching._
* Why do we multithread?
	* Concurrency, sharing CPU time among threads. We suspend one thread and switch to another.
	* Blocks. Sometimes a process will be waiting on I/O devices, so we might as well pull the CPU to do something else.
* Process states:
	* Running
	* Suspended
	* Waiting
	* Ready

Concurrent Threads
------------------
* On a multiprocessor CPU, some threads can be scheduled to one processor, and some on the other

Implementing Threads
--------------------
* A thread library creates threads and implements context switching.

Context Switch
--------------
* The scheduler will
	* Save the current context
	* Choose the next thread
	* Restore its context (dispatch the new thread)
* The stack grows down.

Thread Library Interface
------------------------
* If the OS forces threads to release CPU, they are preemptive.
* A thread can release the CPU voluntarily with Yield().
* Some OSs use both mechanisms.
* The thread library can also
	* Create threads
	* End threads
	* Block threads (what's that?)

Scheduling
----------
* Deciding what to run next
* Can be preemptive or non-preemptive
* Easiest scheduling: FIFO
	* All threads that are ready are put in a queue
	* Top thread is executed, then pushed to the back of the queue
* More policies discussed later

Round-Robin Scheduling
----------------------
* Preemptive
* A thread runs for a few microseconds, and is then pulled to the ready queue
* Problem: some processes should have higher priority than others
* CPU-bound processes require more CPU than I/O
* I/O-bound processes require more I/O than CPU
* We would rather give more CPU time to CPU-bound processes, but Round-Robin scheduling cannot do that.

Interrupts
----------
* An interrupt event will set the CPU's interrupt bit
* Execution jumps to the Interrupt Service Routine, then returns to the thread

Implementing Preemptive Scheduling
----------------------------------
* Each process is given a time quantum, then the time expires and control jumps to the next process in the ready queue
* We put the application stack frame on the kernel stack.
* The trap frame captures the value of all registers (important for preemptive switching)
* Interrupt handling: the ISR which may have several functions
* The ISR is attached to the current thread. Then the ISR calls Yield() on behalf of the current thread.

OS/161 Stack
------------
* Slide 24: This is _not_ the OS/161 stack. It is the kernel stack. The kernel stack is used for context switches.
* Application stack frames: contain the heap, stack, and instruction pointer
* thread|\_yield() stacK: since thread\_yield() is also a function.
* The main difference between voluntary and preemptive switching: preemptive might not preserve temporary registers

Concurrency
===========
* We want to run several threads simultaneously.
* On multiprocessors, several threads really do execute simultaneously.
* On uniprocessors, one thread executes at a time, but due to preemption and timesharing, they seem to run concurrently.
* Extremely important computers such as car and reactor computers are not concurrent, because concurrency is non-deterministic.

Thread Synchronization
----------------------
* Concurrent threads can interact with each other
	* Shared access to hardware through OS
	* Share access to program data (global variables)
* A common problem: enforcing mutual exclusion, that only one thread use a resource at one time
* The part of the program in which a shared object is accessed is called a critical section.

Critical Section Example
------------------------
* A code block that removes the first element of a LinkedList
	* The first will be removed by one thread, and the second thread tries to delete something that is already gone
* A code block that appends an item to a list
	* If both threads think the list is empty, the second append will overwrite the first
	
Enforcing Mutual Exclusion
--------------------------
* Ensure that only one thread executes at a time in a critical section
* Several techniques
	* Exploit special hardware-specific machine instructions that are intended for this
	* Use algorithms such as Peterson's Algorithm that ensure atomic execution
	* Disable all interrupts that trigger preemptions

Disabling Hardware Interrupts
-----------------------------
* advantages
	* Does not require hardware-specific instructions
	* Works for any number of threads
* disadvantages
	* Indiscriminate: prevents all preemption, not just dangerous preemption
	* Ignoring timer interrupts makes the kernel unaware of the passage of time. Critical sections must be short.
	* Does not enforce mutual exclusion on multiprocessors, since multiple threads are running at the same time. They need not wait for interrupts.

Peterson's Mutual Exclusion Algorithm
-------------------------------------

<pre>
void main() {
	tally = 0;
	total() || total();
	write(tally);
}
const int n = 50;
int tally;
void total() {
	int count;
	for(count = 1; count <= n; count++)
		tally++;
}
</pre>

What is the value of tally?

Hardware-Specific Synchronization Instructions
----------------------------------------------

###Test and Set
* a test-ad-set instruction atomically sets the value of a memory location and either
	* places that old value into a register or
	* checks a condition against the old value and records the result in a register
* for presentation purposes, we abstract it as a function TestAndSet(address, value)
	* it returns true if the old value is the same as the new value
* test-and-set can enforce mutual exclusion
*for each critical section, define a "lock" variable: boolean lock;
* use that to keep track of whether we're in the CS, then lock will be true
* before entering a CS, do this: while (TestAndSet(&lock, true)) {} // busy-wait
* when the thread leaves the CS, do lock = false;

<pre>
Thread1() {
	while(TestAndSet(&lock,true)) {}
	// CS
	lock = false;
}
</pre>

### Semaphores
* data structure: synchronization primitive that can enforce mutual exclusion
	* can solve other synchronization issues.
* object with integer value, supporting two operations:
	* P: if the semaphore value is greater than 0, decrement the value. otherwise wait until 0 and decrement.
	* V: increment the value of the semaphore
* two kinds of semaphores
	* counting semaphore: any non-negative value
	* binary semaphore: 0 or 1. V when 1 has no effect.
	
<pre>
struct semaphore {
	int count;
	queueType queue;
}

void wait(semaphore s) {
	s.count--;
	if (count < 0) {
		// place thread in s.queue
		// block the thread
	}
}

void signal(semaphore s) {
	s.count++;
	if (s.count <= 0) {
		// remove a thread from s.queue
		// place the thread in the ready queue
	}
}
</pre>

* if multiple threads run wait and multiple threads run signal, the semaphore will break
	* semaphore is a shared data structure, so we can't have race conditions!
	* we enforce the mutual exclusion of the semaphore (OS instructinos) with test-and-set (hardware)
	
### OS/161 Semaphores
<pre>
struct semaphore {
	char *name;
	volatile int count;
};

struct semaphore *sem.create(const char *name, int initial_count);
void P(struct semaphore *);
void V(struct semaphore *);
void sem.destroy(struct semaphore *);
</pre>

* see kern/include/synch.h, kern/include/sync.c

### Mutual Exclusion Using Semaphores
<pre>
struct semaphore *s;
s = sem create("MySem1", 1); /* initial value is 1 */
P(s); /* do this before entering critical section */
critical section /* e.g., call to list remove front */
V(s); /* do this after leaving critical section */
</pre>

### Producer/Consumer Synchronization
* we have threads that add items to a list (producers) and threads that remove items (ocnsumers)
* we want to prevent consumption from an empty list
* this requires producer/consumer synchronization, which semaphores can provide:

### Producer/Consumer Synchronization Using Semaphores
<pre>
struct semaphore *s;
s = sem create("Items", 0); /* initial value is 0 */
/* Producer’s Pseudo-code: */
add item to the list (call list append())
V(s);
/* Consumer’s Pseudo-code: */
P(s);
remove item from the list (call list remove front())
</pre>

* suppose the list's length cannot exceed N
* producers should wait if the list is full
* use an additional semaphore
	* Full is used to prevent overfilling: how many spaces are filled?
	* Empty is used to prevent overemptying: how many spaces are free?
	* The producer needs an empty spot, and the consumer needs a full spot.

<pre>
struct semaphore *full;
struct semaphore *empty;
full = sem_create("Full", 0); /* initial value = 0 */
empty = sem_create("Empty", N); /* initial value = N */

Producer’s Pseudo-code:
P(empty);
add item to the list (call list append())
V(full);
Consumer’s Pseudo-code:
P(full);
remove item from the list (call list remove front())
V(empty);
</pre>
