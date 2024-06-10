# lecture 4

Created: February 4, 2024 1:42 AM

# processes

- the terms **process** and **job** are used almost interchangeably in most textbooks— however, a process includes:
    - value of program counter
    - value of registers and processor status word
    - stack for temporary variables
    - text for program code
    - data section for global variables
    - heap for dynamic storage of variables (those created using **malloc**)
- in an OS, there are multiple processes executing at the same time
    
    
    | some computer systems have multiple CPUs, but most smaller ones have only one CPU |
    | --- |
    | when the number of processes is more than number of CPUs, each CPU can only be allocated to a process for execution at each moment |
    | not all process can receive the CPU service at any moment |
    | process state: process would alternate between “served by a CPU” and “waiting” |
    | process control block: to keep track of the state of a process, and the information that should be maintained when the process switches between CPUs, a data structure is allocated for each process— this data structure is process control block used to store the information |

## process state

- as a process, it changes from one state to another, reflected by a **state transition diagram**
    
    
    | new | process is being created |
    | --- | --- |
    | running | process is running |
    | waiting | process is waiting for some event to occur |
    | ready | process is waiting to be assigned to a processor or CPU for execution  |
    | terminated | process has finished execution |

![Screenshot 2024-02-04 at 8.06.58 PM.png](lecture%204%2012848cbe65e84e84a542a453f47e8d76/Screenshot_2024-02-04_at_8.06.58_PM.png)

## process control block

- each process is represented by a **process control block**
    
    
    | process state | new, ready, waiting etc |
    | --- | --- |
    | program counter |  |
    | CPU registers | registers, stack pointer, PSW |
    | CPU scheduling information | process priority, pointer to scheduling queue |
    | memory-management information | limit of memory boundary |
    | accounting information | process id, CPU time used |
    | I/O status information | list of opened files |

## CPU switching

- an example that process $P_0$ passes the CPU to process $P_1$ when $P_0$ is interrupted or executes a system call

![Screenshot 2024-02-04 at 8.50.55 PM.png](lecture%204%2012848cbe65e84e84a542a453f47e8d76/Screenshot_2024-02-04_at_8.50.55_PM.png)

## process scheduling queues

- **job queue**:
    - set of all processes in the system

- **ready queue**:
    - set of all processes residing in main memory, ready and waiting to be executed

- **device queue**:
    - set of processes waiting for an I/O device
- processes migrate (i.e. move) among the various queues
    - a process on ready queue gets CPU and may later wait for I/O at the device queue
- a **process scheduler for each type of queues** determines who will get service next

## ready and I/O device queues

![Screenshot 2024-02-04 at 8.53.09 PM.png](lecture%204%2012848cbe65e84e84a542a453f47e8d76/Screenshot_2024-02-04_at_8.53.09_PM.png)

## process life cycle

![Screenshot 2024-02-04 at 8.53.50 PM.png](lecture%204%2012848cbe65e84e84a542a453f47e8d76/Screenshot_2024-02-04_at_8.53.50_PM.png)

# scheduler

- a process will move through different queues as between its **birth (creation)** and its **death (termination)**
- a **scheduler** selects the process to be served when it is waiting for different services

- **long-term scheduler**:
    - also called job scheduler
    - select which processes should be brought into the ready queue (only process in ready queue are eligible for CPU)
    - concerned with admission of jobs or processes for execution
        - decision to put a process in the ready queue occurs rarely, perhaps maybe once when the process is created
        - decision making should be a quite good one (it can afford a longer running time)

- **short-term scheduler**:
    - also called CPU scheduler
    - select which process should be executed next and be allocated the CPU
    - concerned with the allocation of CPU
        - decision to give a CPU to a process occurs many times every second
        - decision making step must be very fast (so it would be simple)

> **long-term scheduler controls the degree of multi-programming** (number of processes potentially competing for CPU)
> 

### I/O bound process

- a process that spends more time doing I/O than computations
- between long duration of waiting, there are many short periods of using CPU (many short CPU bursts)
    - example: interactive programs, e.g. editor

### CPU bound process

- a process that spends more time doing computations than I/O
- between occasional I/O, there are long periods of using CPU (few very long CPU bursts)
    - example: computation programs, e.g. finding next move in chess playing
- a process may be I/O-bound initially and then become CPU-bound or vice versa— intermediate process between I/O and CPU-bound, with moderate I/O

- a computer system will **not be effective** if all processes are I/O-bound
    - poor use of CPU and heavy competition on I/O devices
    - **CPU utilization**: the percentage of time that the CPU is used
    - **device contention**: the competition to access I/O devices

- a system will **not perform well** if all processes are CPU-bound
    - CPU is used to an extreme, but I/O devices are idle which leads to poor device utilization
    - processes need to wait for a long time to get the CPU and to complete their execution

> long-term scheduler makes decision to maintain a good mix of CPU-bound and I/O-bound processes
> 

## short-term scheduler

- makes decision on which process to get CPU
- simple schedulers just submit the processes in a first-come-first-serve manner to the CPU
- better schedulers allocate the CPU to improve system performance
    - waiting time for CPU
    - completion time of processes
    - responsiveness
- different types of processes would need different treatment

## medium-term scheduler

- when there are **too many processes** competing for CPU, some should be temporarily removed from ready queue
- when there are **few processes** using CPU, some removed processes should be returned to ready queue
- control multi-programming degree after process admission— if not done carefully, this could lead to **thrashing**

![Screenshot 2024-02-04 at 9.39.50 PM.png](lecture%204%2012848cbe65e84e84a542a453f47e8d76/Screenshot_2024-02-04_at_9.39.50_PM.png)

# context switching

- **short-term scheduler** controls which process gets CPU next
    - the **currently running process** will give away CPU to another process— e.g. initiating I/O (making an I/O system call), time allocation used up (implied by a clock interrupt), I/O completion interrupt (others)
- **context switching** refers to a sequence of events to bring CPU from an executing process to another
    - when CPU switches to another process, the system must save the state of old process and load the previously saved state for new process— **the** **state of old process will be put on stack when the “time-up” clock interrupt occurs** (it also puts the scheduler in-charge)
    - after deciding on successor of CPU, scheduler changes the PC and returns from interrupt/system call to the new process
- context switching time is a kind of overhead since system does no useful work while switching from one process to another
    - time cost is dependent on hardware support

![Screenshot 2024-02-04 at 9.41.53 PM.png](lecture%204%2012848cbe65e84e84a542a453f47e8d76/Screenshot_2024-02-04_at_9.41.53_PM.png)

- process $P_0$ is switched to process $P_1$— assume that the interrupt is a time-expire interrupt or a system call to I/O

## process creation

- a process is created when a program is run
- when you type `a.out` in linux, the linux shell (CLI) creates a new process for `a.out`, loads the code into the process and lets it run
    - since there is no long-term scheduler, the new process is automatically admitted and put in the ready queue
- processes are normally identified by an integer, called **process identifier or pid**
- there is a relationship between a process that creates another process
    - the creating process is called the **parent process**
    - the created process is called the **child process**
    - a child process could be the parent of another process, and a tree or hierarchy of processes could be formed

### resource sharing

- parent and children share all resources
- children share subset of parent’s resources
- parent and children share no resources

### execution

- parent and children execute concurrently
- parent waits until all children terminate

### address space

- each child duplicates that of parent
- each child has an independent program loaded into it

```bash
-H
ps -HlF 
ps -Helf
```

- in linux, full hierarchy could be obtained by `pstree` command
    
    ![Screenshot 2024-02-04 at 9.46.10 PM.png](lecture%204%2012848cbe65e84e84a542a453f47e8d76/Screenshot_2024-02-04_at_9.46.10_PM.png)
    
- use `fork` system call to create new process
- to replace process memory with a new program, use `exec` and its family of system calls
- parent uses `wait` to collect child and then continues

![Screenshot 2024-02-04 at 9.47.12 PM.png](lecture%204%2012848cbe65e84e84a542a453f47e8d76/Screenshot_2024-02-04_at_9.47.12_PM.png)

## process termination

- after a process executes its last statement, it asks the OS to terminate it by calling exit
    - it passes return data back to parent process via **wait**
    - process resources are de-allocated when terminated
- parent may terminate the execution of children processes by calling **abort** if child has exceeded usage of resources beyond its allocation, task assigned to child is no longer required, or parent itself is exiting
    - some OS do not allow a child process to continue if its parent terminates and the child will be terminated as well
    - some OS allow a special arrangement of child to continue after parent terminates

- **zombie process**: a completed child process that is not collected or picked up by its parent

- **orphan process**: a process without a parent

## process creation in unix/linux

```c
#include <stdio.h>
#include <stdlib.h>
int main() {
	int childid;
	childid = fork(); 

	if (childid < 0) { /* error occurred */
		printf("Fork Failed\n");
		exit(1);
	} else if (childid == 0) {
		execl("/bin/ls", "ls", NULL); #executes a program "ls" at pathname "/bin/ls", last argument is NULL
	} else {
		wait(NULL);
		printf("Child Complete\n");
		exit(0);
	}
}
```

- **resource sharing**:
    - parent and child share no resources

- **execution**:
    - parent and child execute concurrently

- **address**:
    - child duplicates that of parent
    - child may have an independent program loaded into it, with special exec system calls

> parent is informed about the completion of a child— parent should wait for a child to collect it
> 

- the **exec** family of a system calls allow a unix/linux child process to execute another program instead of the parent program
    - include the file header `unistd.h`

| system call | feature |
| --- | --- |
| int execl(const char *path, const char *arg0, ...); | execute a program at pathname path, last argument must be NULL |
| int execlp(const char *file, const char *arg0, ...); | execute a program named by file, last argument must be NULL |
| int execle(const char *path, const char *arg0, ..., char *const envp[]); | same as execl, but access environment variables via *envp[] |
| int execv(const char *path, char *const argv[]); | Same as execl, but arguments are stored in *argv[] instead |
| int execvp(const char *file, char *const argv[]); | same as execlp, but arguments are stored in *argv[] instead |
| int execve(const char *path, char *const argv[], char *const envp[]); | same as execv, but access environment variables via *envp[] |
- a **non-zero return value** indicates an error from system call and the list of arguments in `execl`, `execlp` and `execle` are terminated by a NULL pointer
    - the **path** is a string containing the file name, including the full path, to be executed
    - the **second argument `arg0`** is the name of the program for execution, e.g. `“ls”`
- for `execlp` and `execvp`, the **first argument is a file name instead of the path name**
    - if the string contains a "/", it is considered as a path name— otherwise, it is a file name and the system will search for the file according to the directories in environment variable PATH

- for `execv`, `execvp` and `execve`, arguments for the program are packed within `*argv[]`

- for `execle` and `execve`, there is a final argument `*envp[]` storing the environment variables and values for the program

> if parent does not wait for a child to complete, and if parent completes before child completes, the **child will become an orphan** (without parent), and on the other hand, a completed child process that is not collected or picked up by its parent is called a **zombie** (alive dead)— so **wait** is necessary
> 

### orphan process

- an orphan process will be adopted by a new parent process— the special process (**init** process) willing to become new parent for all orphan processes has pid 1

- process with pid 0 is the **swapper for paging**, and process with pid 1 is the **first process running for starting up and shutting down unix**

![Screenshot 2024-02-04 at 9.59.07 PM.png](lecture%204%2012848cbe65e84e84a542a453f47e8d76/Screenshot_2024-02-04_at_9.59.07_PM.png)

- in the example above, a process has its parent process 1
    - a student process is doing something and somehow never finishes (still running), but the original shell process for the session has terminated (logged out) the process becomes an orphan and is adopted by process 1 as the new parent

> note that process 1 on apollo (CentOS Linux) is called `systemd` (i.e. **system daemon**) instead of `init`
process 1 on MacOS is called `launchd` (i.e. **launch daemon**) instead of `init`
> 

![Screenshot 2024-02-04 at 10.02.32 PM.png](lecture%204%2012848cbe65e84e84a542a453f47e8d76/Screenshot_2024-02-04_at_10.02.32_PM.png)

## unix process state transition

![Screenshot 2024-02-04 at 10.03.55 PM.png](lecture%204%2012848cbe65e84e84a542a453f47e8d76/Screenshot_2024-02-04_at_10.03.55_PM.png)

## cooperating processes

- an **independent process** cannot affect or be affected by the execution of another process

- a **cooperating process** can affect or be affected by the execution of another process
- most larger systems have a collection of processes cooperating with one another
- **web server** and **web browser (client)** pair is a form of cooperating processes, residing over the network at different computers
- advantages of process cooperation:
    
    
    | information sharing | concurrent access to data |
    | --- | --- |
    | computation speed-up | break into subtasks for processes |
    | modularity | better structuring of functionality |
    | convenience | model a user in concurrent working mode |
- a very common view point of cooperating processes is the model of a **producer and a consumer**

- producer process produces information (called item) that is consumed by a consumer process

- information (item) produced by producer must be stored up for consumer usage later (since consumer may not be running at the same speed as producer)
- **producer-consumer problem** is related to a problem to coordinate producer(s) and consumer(s), especially when the buffer to hold the intermediate data is not unlimited
    - producer could store data into a shared array or shared queue and consumer takes it out there
        - **shared array** is like the board and everyone can see and draw
    - producer may send a message containing the data to consumer for consumer to read
        - **message** is like an SMS and only the pair knows

# interprocess communication

- mechanism for cooperating processes to communicate and to synchronize their actions
    - **shared memory system**: processes communicate by reading/writing to shared variables
    - **message passing system**: processes communicate with each other by passing information without using shared variables
- **interprocess communication (IPC)** normally refers to message passing approach with two operations: **send (message), receive (message)**
    - if process P and Q wish to communicate, they need to establish a communication link between them and exchange messages via send/receive
    - communication link could be **physical** (e.g., shared memory, hardware bus) or **logical** (e.g., channel or socket)

## synchronization

![Screenshot 2024-02-04 at 10.08.01 PM.png](lecture%204%2012848cbe65e84e84a542a453f47e8d76/Screenshot_2024-02-04_at_10.08.01_PM.png)

- concurrent access to shared data by cooperating processes (or threads) may make data inconsistent
    - in producer-consumer problem, what happens if a producer is producing an item when the consumer is attempting to take it at the same time? what would happen if multiple consumers are taking out the same item?
- **synchronization** ensures the orderly execution of cooperating processes that share a logical address space to guarantee data consistency
    - synchronization requests processes to wait for the signal to go ahead, to **avoid the race condition** (e.g. sleeping barber problem, reader-writer problem, society room problem)

### a simple race situation

![Screenshot 2024-02-04 at 10.09.06 PM.png](lecture%204%2012848cbe65e84e84a542a453f47e8d76/Screenshot_2024-02-04_at_10.09.06_PM.png)