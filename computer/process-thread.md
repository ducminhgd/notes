---
type: System
keywords: process, thread
---
# Process, Thread

## Process

Processes are basically the programs that are dispatched from the ready state and are scheduled in the CPU for execution. It can be defined as an execution unit where a program runs. A [[process]] can create other processes which are known as Child Processes. The process takes more time to terminate and it is isolated means it does not share the memory with any other process.

The process can have the following states new, ready, running, waiting, terminated, and suspended. 

Here are the important properties of the process:
- Creation of each process requires separate system calls for each process.
- It is an isolated execution entity and does not share data and information.
- Processes use the IPC(Inter-Process Communication) mechanism for communication that significantly increases the number of system calls.
- Process management takes more system calls.
- A process has its stack, heap memory with memory, and data map.

Processes are able to run [[concurrent]]ly but not [[parallel]] with a CPU core.

## Thread

[[Thread]] is the segment of a process which means a process can have multiple threads and these multiple threads are contained within a process. A thread has three states: Running, Ready, and Blocked.

The thread takes less time to terminate as compared to the process but unlike the process, threads do not isolate. 

Threads are able to run in parallel.

Thread properties
- Single system call can create more than one thread
- Threads share data and information.
- Threads shares instruction, global, and heap regions. However, it has its register and stack.
- Thread management consumes very few, or no system calls because of communication between threads that can be achieved using shared memory.

Multiple threads share information like data, code, files, etc. We can implement threads in three different ways:
- Kernel-level threads
  
    The kernel-level threads are handled by the Operating system and managed by its [[kernel]]. These threads are slower than user-level threads because context information is managed by the kernel. To create and implement a kernel-level thread, we need to make a system call. Kernel knows and manages the threads. Instead of thread table in each process, the kernel itself has thread table (a master one) that keeps track of all the threads in the system. In addition kernel also maintains the traditional process table to keep track of the processes. OS kernel provides system call to create and manage threads.
  - Advantages:
    - Since kernel has full knowledge about the threads in the system, scheduler may decide to give more time to processes having large number of threads.
    - Good for applications that frequently block.
  - Limitations:
    - Slow and inefficient.
    - It requires thread control block so it is an overhead.

- User-level threads
  
  - As the name suggests, the user-level threads are only managed by users, and the kernel does not have its information.
  - The kernel takes all these threads as a single process and handles them as one process only.
  - The user-level threads are implemented by user-level libraries, not by the system calls.
  - Advantages:
    - These are faster, easy to create and manage.
    - Can be implemented on an OS that doesn’t support multithreading.
    - Simple representation since thread has only program counter, register set, stack space.
    - Simple to create since no intervention of kernel.
    - Thread switching is fast since no OS calls need to be made. 
  - Limitations:
    - No or less co-ordination among the threads and Kernel.
    - If one thread causes a page fault, the entire process blocks.

- Hybrid threads

## Difference between Process and Thread

|        Parameter        |                                      Process                                       |                                                                                    Thread                                                                                    |
| ----------------------- | ---------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Definition              | Process means any program is in execution.                                         | Thread means a segment of a process.                                                                                                                                         |
| Termination time        | The process takes more time to terminate.                                          | The thread takes less time to terminate.                                                                                                                                     |
| Creation time           | It takes more time for creation.                                                   | It takes less time for creation.                                                                                                                                             |
| Context switching time  | It also takes more time for context switching.                                     | It takes less time for context switching.                                                                                                                                    |
| Communication           | The process is less efficient in terms of communication.                           | Thread is more efficient in terms of communication.                                                                                                                          |
| Multi-programming       | Multiprogramming holds the concepts of multi-process.                              | We don’t need multi programs in action for multiple threads because a single process consists of multiple threads.                                                           |
| Sharing memory          | The process is **mostly** isolated.                                                | Threads share memory.                                                                                                                                                        |
| Lightweight             | The process is called the heavyweight process.                                     | A Thread is lightweight as each thread in a process shares code, data, and resources.                                                                                        |
| Context switching by OS | Process switching uses an interface in an operating system.                        | Thread switching does not require calling an operating system and causes an interrupt to the kernel.                                                                         |
| Blocking                | If one process is blocked then it will not affect the execution of other processes | If a user-level thread is blocked, then all other user-level threads are blocked.                                                                                            |
| Process Control Block   | The process has its own Process Control Block, Stack, and Address Space.           | Thread has Parents’ PCB, its own Thread Control Block, and Stack and common Address space.                                                                                   |
| Changing effect         | Changes to the parent process do not affect child processes.                       | Since all threads of the same process share address space and other resources so any changes to the main thread may affect the behavior of the other threads of the process. |
| System call             | A system call is involved in it.                                                   | No system call is involved, it is created using APIs.                                                                                                                        |
| Sharing data            | The process does not share data with each other.                                   | Threads share data with each other.                                                                                                                                          |

## Read more

1. https://www.geeksforgeeks.org/process-table-and-process-control-block-pcb/