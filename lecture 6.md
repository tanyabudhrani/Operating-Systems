# lecture 6

# scheduling

- **cpu scheduling**: controls the decision of which process should get the CPU when the CPU is not in use
    - the CPU scheduler needs to maximize CPU utilization in the presence of multi-programming
- a careful observation on CPU and I/O burst and **distribution via histogram** of CPU bursts suggests the real need of CPU scheduling
    - **CPU burst**: the consecutive amount of CPU time used by a process— there are usually many relatively short CPU bursts

- **CPU scheduling decisions** may take place when a process:
    - needs to wait for I/O or special events
    - finishes execution
    - gives up the CPU willingly or non-willingly
    - completes its I/O or its special events happen

- the first two situations imply that current process can continue to hold CPU if it needs to (**non-preemptive**, i.e. no forced taken of CPU)

- the last two situations imply that there is a choice of taking CPU to give to a chosen process (**preemptive**, i.e. possibly forced taken of CPU)

## non-preemptive

- a process just uses the CPU until it wants to give it up when
    - terminated
    - waiting for I/O
    - execute a yield statement (in Java)
- no special hardware for timer interrupt is needed
- used in windows 3.1 and old systems

## preemptive

- a process will be deprived of the CPU when its allocated time slice is used up or some other event happens
- a form of **timer interrupt** must be used so that OS can take control when it is time
- used in most OS

> a **scheduling algorithm** is used by scheduler to determine which eligible process will be the next to get the CPU

### after making the decision

- OS needs to:
    - perform context switching
    - switch to user mode
    - jump to the proper location in the user program to resume that program
- the time taken by the OS to stop the current process and start another process for running is called **dispatch latency**
    - dispatch latency is a form of overhead, since CPU time is spent without doing real work

## scheduling consideration

| cpu utilization | percentage of CPU time used for real work (non-idle) | want high CPU utilization |
| --- | --- | --- |
| throughput | number of processes completing execution per time unit | want high throughput |
| turnaround time | amount of time to complete process execution since arrival | want short turnaround time |
| waiting time | amount of time a process spends waiting in ready queue | want short waiting time |
| response time | amount of time from the moment a request was submitted until the first response is produced (may not be output) | want short response time |

## scheduling algorithms

- first come first serve (FCFS)
- shortest job First (SJF)
- shortest remaining time (SRT)
- priority (PR)
- round robin (RR)
- multi-level queue
- multi-level feedback queue

# FCFS scheduling

- to dry-run scheduling algorithms, we need information about the burst time (execution time) and the arrival time of processes
    
    > turnaround time = completion time - arrival time 
    waiting time = turnaround time - burst time
    response time = the time in which a process gets the CPU the first time - arrival time
    > 

1.  in FCFS, processes are served in their **arrival order**
    
    
    | process | burst time | arrival |
    | --- | --- | --- |
    | P1 | 24 | 0 |
    | P2 | 3 | 1 |
    | P3 | 3 | 2 |
    - the gantt chart for the schedule is:
        - waiting time for P1 = 0, for P2 = 23, for P3 = 25
            - p1 = 0, p2 = 24-AT, p3 = 27-AT
        - average waiting time = $(0 + 23 + 25)/3 = 16$
        
2. assume that process P1 arrives later than P2 and P3
    
    
    | process | burst time | arrival |
    | --- | --- | --- |
    | P1 | 24 | 2 |
    | P2 | 3 | 0 |
    | P3 | 3 | 1 |
    - waiting time for P1 = 4, for P2 = 0, for P3 = 2
        - P2 is executed first for 3 seconds, then P3 is executed for 3 seconds (6), then P1 will be executed for 24 seconds (30)
        - turnaround time: P2 = 3 (3-0), P3 = 5 (6-1), P1 = 28 (30-2)
        - waiting time: P2 = 0 (3-3), P3 = 2 (5-2), P1 = 4 (28-4)
    - average waiting time = $(4 + 0 + 2)/3 = 2$
3. processes may arrive in around the same time
    - often the process pid could **reflect the real arrival order**, since earlier process would normally receive a smaller pid
    - this is the case in unix and linux, but not for windows
    
    | process | burst time | arrival |
    | --- | --- | --- |
    | P1 | 24 | 0 |
    | P2 | 3 | 0 |
    | P3 | 3 | 0 |
    - the gantt chart for the schedule is:
        - waiting time for P1 = 0, P2 = 24, P3 = 27
        - average waiting time = (0+24+27)/3 = 17
        
    - consider the example again, where time is in ms
        
        
        | turnaround time | P1 = 24, for P2 = 27, for P3 = 30 |
        | --- | --- |
        | average turnaround time | $(24 + 27 + 30)/3 = 27$ |
        | throughput | 3 process/0.03 s = 100 processes per second |
        | response time | P1 = 0, for P2 = 24, for P3 = 27 |
        | average response time | $(0 + 24 + 27)/3 = 17$ |

## convoy effect

- convoy effect happens when a single long process is blocking a number of processes
    - it will cause the waiting time to become very large
- to reduce the convoy effect, it is a good idea to let smaller jobs execute first — to an extreme, smallest jobs will always be executed first
    - this is called the **(SJF) scheduling algorithm**
- it could be proved that SJF produces the smallest average waiting time and smallest average turnaround time for a given set of processes
- we say that SJF is optimal

# SJF scheduling

1. assume the following processes
    
    
    | process | burst time | arrival |
    | --- | --- | --- |
    | P1` | 6 | 0 |
    | P2 | 8 | 0 |
    | P3 | 7 | 0 |
    | P4 | 3 | 0 |
    - the gantt chart for the schedule is:
        
    | turnaround time | P1 = 9, P2 = 24, P3 = 16, P4 = 3 |
    | --- | --- |
    | average turnaround time | $(9+24+16+3)/4 = 13$ |
    | waiting time | P1 = 3, P2 = 16, P3 = 9, P4 = 0 |
    | average waiting time | $(3 + 16 + 9 + 0)/4 = 7$ |
    - P4 executes first then P1 then P3 then P2
        - P4 executes for 3 seconds, then P1 executes for 6 seconds (6+3), then P3 executes for 7 seconds (9+7), then P2 for 8 seconds (8+16)
        - turnaround time is the same as completion time since arrival time is all 0
2. it won’t be so lucky that all processes arrive at 0 
    
    
    | process | burst time | arrival |
    | --- | --- | --- |
    | P1 | 8 | 0 |
    | P2 | 4 | 1 |
    | P3 | 9 | 2 |
    | P4 | 5 | 3 |
    - the gantt chart for the schedule is:
        
    | turnaround time | P1 = 8, P2 = 11, P3 = 24, P4 = 14 |
    | --- | --- |
    | average turnaround time | $(8+11+24+14)/4 = 14.25$ |
    | waiting time | P1 = 0, P2 = 7, P3 = 15, P4 = 9 |
    | average waiting time | $(0 + 7 + 15 + 9)/4 = 7.75$ |
    - P1 arrives first and runs for 8 seconds, then P2 for 4 seconds (4+8), then P4 for 5 seconds (12 + 5), then P3 for 9 seconds (17+9)
        - turnaround: P1 = 8 (8-0), P2 = 11 (12-1), P4 = 14 (17-3), P3 = 24 (26-2)
        - waiting: P1 = 0 (8-8), P2 = 7 (11-4), P4 = 9 (14-5), P3 = 15 (24 - 9)
        
        > even if this is SJF, P1 is the only process available at 0 so the CPU has no choice — then after P1 is executed, all other processes are placed into the queue, so then they are executed based on burst times
   
        

# SRT scheduling

- to beat the possible convoy effect in SJF when an early arriving long job is holding up the CPU, we could allow preemption of jobs in SJF
    - the preemptive version of SJF is called (SRT)
- in SRT, whenever a new process arrives, the remaining executing time of all existing processes are considered, and the one with the smallest remaining time (including the newly arriving one) will be executed
    - if the one to be executed is different from the currently executing one, **preemption has occurred**
- it could be proved that SRT always produces the smallest average waiting time and smallest average turnaround time for a given set of processes
- we say that **SRT is optimal for preemptive scheduling** while **SJF is optimal for non-preemptive scheduling**

1. in this example, we allow P2 to preempt P1
    
    
    | process | burst time | arrival |
    | --- | --- | --- |
    | P1 | 8  | 0 |
    | P2 | 4  | 1 |
    | P3 | 9 | 2 |
    | P4 | 5 | 3 |
    - the gantt chart for the schedule is:
        
        - explanation of gantt chart:
            - P1 gets executed first since it is first to arrive — it executes for 1 second
            - then since P2 has a smaller burst time it gets executed once (completion time 2)
            - then P2 gets executed again (completion time 3)
            - P2 gets executed until its over (completion time 5)
            - then P4 gets executed until its over (completion time 10)
            - then P1 gets executed until its over (completion time 17 since 7+10)
            - then finally P3 gets executed until its over (completion time 26)
    
    | turnaround time | P1 = 17, P2 = 4, P3 = 24, P4 = 7 |
    | --- | --- |
    | average turnaround time | $(17+4+24+7)/4 = 13$ |
    | waiting time | P1 = 9, P2 = 0, P3 = 15, P4 = 2 |
    | average waiting time | $(9 + 0 + 15 + 2)/4 = 6.5$ |
    - turnaround time: P1 = 17 (17 - 0), P2 = 2 (5 - 1), P3 = 24 (26 - 2), P4 = 7 (10 - 3)
    - waiting time: P1 = 9 (17-8), P2 = 0 (2-4), P3 = 15 (24-9), P4 = 2 (7-5)

### example

- try this on both SJF and SRT
    
    
    | process | burst time | arrival |
    | --- | --- | --- |
    | P1 | 8 — 7  | 0 |
    | P2 | 4 (small) — 23 | 1 |
    | P3 | 9 | 2 |
    | P4 | 5 | 3 |
    - the gantt charts are:
    
    | process | burst | arrival | SJF-W | SJF-TR | SRT-W | SRT-TR |
    | --- | --- | --- | --- | --- | --- | --- |
    | P1 | 7 | 0 | 0 | 7 | 9 | 16 |
    | P2  | 4 | 2 | 6 | 10 | 1 | 5 |
    | P3 | 1 | 4 | 3 | 4 | 0 | 1 |
    | P4 | 4 | 5 | 7 | 11 | 2 | 6 |
    | average |  |  | 4 | 8 | 3 | 7 |

# priority scheduling

- a **priority number** is associated with each process
- CPU is allocated to the process with the highest priority
    - in some systems, largest value in priority means highest priority (windows), and in some others, smallest value means highest priority (unix and linux)
    - again, priority scheduling could be either non-preemptive or preemptive, but the preemptive version is more commonly in use

- SJF is a form of priority scheduling where the priority is CPU burst time (with **smaller value** meaning **higher priority**)

- FCFS is a form of priority scheduling where the priority is process arrival time (with **smaller value** meaning **higher priority**)
1. assume the following processes — NON-PREEMPTIVE
    
    
    | process | burst time | priority | arrival time |
    | --- | --- | --- | --- |
    | P1 | 10 | 3 | 0 |
    | P2 | 1 | 1 (highest) | 0 |
    | P3 | 2 | 4 | 0 |
    | P4 | 1 | 5 (lowest) | 0 |
    | P5 | 5 | 2 | 0 |
    - the gantt chart for the schedule is:
        
        - explanation of gantt chart:
            - P2 runs first since it has the highest priority and it runs for 1 second
            - then P5 runs for 5 seconds (completion time 6)
            - then P1 runs for 10 seconds (completion time 16)
            - then P3 runs for 2 seconds (completion time 18)
            - then P4 runs for 1 second (completion time 19)
    
    | turnaround time | P1 = 16, P2 = 1, P3 = 18, P4 = 19, P5 = 6 |
    | --- | --- |
    | average turnaround time | $(16 + 1 + 18 + 19 + 6)/5 = 12$ |
    | waiting time | P1 = 6, P2 = 0, P3 = 16, P4 = 18, P5 = 1 |
    | average waiting time | $(9 + 0 + 15 + 2)/4 = 6.5$ |
    - turnaround time will be same as completion time since arrival time is 0
    - waiting time: P1 = 6 (16-10), P2 = 0 (1-1), P3 = 16 (18-2), P4 = 18 (19-1), P5 = 1 (6-5)
        - non-preemptive
            
2. assume the following processes — PREEMPTIVE
    
    
    | process | burst time | priority | arrival time |
    | --- | --- | --- | --- |
    | P1 | 10 | 3 | 0 |
    | P2 | 1 | 1 (highest) | 1 |
    | P3 | 2 | 4 | 2 |
    | P4 | 1 | 5 (lowest) | 3 |
    | P5 | 5 | 2 | 4 |
    - the gantt chart for the schedule is:
        
        - P1 runs first since it arrives first and it runs for 1 second
        - then P2 runs since it has a higher priority and it runs for 1 second and finishes (completion time 2)
        - P1 runs again since the next process has a higher arrival time until 4th second
        - then P5 has a higher priority so it runs until it finishes (completion time 9)
        - then P1 continues running until it finishes (completion time 16)
        - then P3 runs until it finishes (completion time 18)
        - then P4 runs until it finishes (completion time 19)
        
    
    | turnaround time | P1 = 16, P2 = 1, P3 = 16, P4 = 16, P5 = 5 |
    | --- | --- |
    | average turnaround time | $(16 + 1 + 16 + 16 + 5)/5 = 10.8$ |
    | waiting time | P1 = 6, P2 = 0, P3 = 14, P4 = 15, P5 = 0 |
    | average waiting time | $(9 + 0 + 15 + 2)/4 = 6.5$ |
    - preemptive
        
- the problem with priority scheduling is that a low priority process may get no chance of execution, if there are higher priority processes that keep coming
- this problem is called **starvation**
    - the low priority process starves for the CPU that is repeatedly consumed by high priority processes
- SJF and SRT are special cases of priority scheduling, so they also suffer from the same starvation problem
    - a long process would not get a chance of execution under SJF and SRT
- **solutions**:
    - upgrade the priority of processes that wait for too long
    - make a fairer usage of the CPU

# RR scheduling

- for FCFS, SJF, SRT, PR, there is good performance for the process at the head, but it is very poor for those at the tail
    - we want a fair share of the CPU to everyone
- this is achieved in **round robin (RR) scheduling**
    - each process gets a small unit of CPU time in turn
    - after this time has elapsed, the process is preempted and put back to the end of the ready queue
    - this specific small unit of CPU time is called a time **quantum**, typically 10-100 milliseconds
- if there are n processes in the ready queue and the time quantum is q, then each process gets 1/n of the CPU time in blocks of at most q time units at once— no process has to wait for more than the time it takes for each other process to use the CPU once, i.e. no more than $q  (n − 1)$ time units
1. when time quantum of a process is up, it will be put back to the end of the ready queue— assume q=4
    
    
    | process | burst time | arrival |
    | --- | --- | --- |
    | P1 | 18 | 0 |
    | P2 | 9 | 0 |
    | P3 | 3 | 0 |
    - the gantt chart for the schedule is:
    
    | turnaround time | P1 = 30, P2 = 24, P3 = 11 |
    | --- | --- |
    | average turnaround time | $(30+24+11)/3 = 21.67$ |
    | waiting time | P1 = 12, P2 = 15, P3 = 8 |
    | average waiting time | $ (12 + 15 + 8)/3 = 11.67$ |
2. when time quantum size changes, the performance could also change — assume now q=2
    
    
    | process | burst time | arrival |
    | --- | --- | --- |
    | P1 | 18 | 0 |
    | P2 | 9 | 0 |
    | P3 | 3 | 0 |
    - the gantt chart for the schedule is:
    
    | turnaround time | P1 = 30, P2 = 22, P3 = 11 |
    | --- | --- |
    | average turnaround time | $(30+22+11)/3 = 21$ |
    | waiting time | P1 = 12, P2 = 13, P3 = 8 |
    | average waiting time | $(12 + 13 + 8)/3 = 11$ |
3. when arrival times are not all zero, the arrival order would affect the ready queue — assume q=4
    
    
    | process | burst time | arrival |
    | --- | --- | --- |
    | P1 | 18 | 0 |
    | P2 | 9 | 3 |
    | P3 | 3 | 6 |
    - the gantt chart for the schedule is:
        
    
    | turnaround time | P1 = 30, P2 = 21, P3 = 9 |
    | --- | --- |
    | average turnaround time | $(30+21+9)/3 = 20$ |
    | waiting time | P1 = 12, P2 = 12, P3 = 6 |
    | average waiting time | $(12 + 12 + 6)/3 = 10$ |

### example

- try this on RR with q = 40 and q = 20
    
    
    | process | burst time | arrival |
    | --- | --- | --- |
    | P1 | 53 | 0 |
    | P2 | 17 | 0 |
    | P3 | 68 | 0 |
    | P4 | 24 | 0 |
    - the gantt charts for the schedules are:
        
    
    | process | burst | arrival | 40-W | 40-TR | 20-W | 20-TR |
    | --- | --- | --- | --- | --- | --- | --- |
    | P1 | 53 | 0 | 81 | 137 | 81 | 134 |
    | P2  | 17 | 0 | 40 | 57 | 20 | 37 |
    | P3 | 68 | 0 | 94 | 162 | 94 | 162 |
    | P4 | 24 | 0 | 97 | 121 | 97 | 121 |
    | average |  |  | 78.0 | 118.5 | 73.0 | 113.5 |
- try this again on RR with q = 40 and q = 20
    
    
    | process | burst time | arrival |
    | --- | --- | --- |
    | P1 | 53 | 0 |
    | P2 | 17 | 3 |
    | P3 | 68 | 6 |
    | P4 | 24 | 9 |
    - the gantt charts for the schedules are:
    
    | process | burst | arrival | 40-W | 40-TR | 20-W | 20-TR |
    | --- | --- | --- | --- | --- | --- | --- |
    | P1 | 53 | 0 | 81 | 134 | 81 | 134 |
    | P2  | 17 | 3 | 37 | 54 | 17 | 34 |
    | P3 | 68 | 6 | 88 | 156 | 88 | 156 |
    | P4 | 24 | 9 | 88 | 112 | 88 | 112 |
    | average |  |  | 73.5 | 114.0 | 68.5 | 109.0 |

## performance of RR scheduling

- higher turnaround time, but better response time
- q large then becomes close to FCFS
- q small then context switching overhead will be very high

# multi-level queue scheduling

- some jobs want fast turnaround time but do not care about response time (batch jobs) and some others want fast response time (interactive jobs)
    - **SJF/SRT is good for batch jobs**, and **RR is good for interactive jobs**
- in multi-level queue scheduling, the ready queue is partitioned into separate queues
    - system queue, interactive queue, batch queue, etc
- each queue has its own scheduling algorithm
    - system (priority), interactive (RR), batch (FCFS/SRT)
- scheduling must also be done **between the queues**

## fixed priority

- each queue has a priority and high priority queues will be served before low priority queue
- possibility of starvation

## time slicing

- each queue gets a certain amount of CPU time for scheduling its own processes
- example
    - allocate 50% to system jobs
    - allocate 25% to interactive jobs
    - allocate 25% to batch/other jobs

- in multi-level queue scheduling, a process may enter a wrong queue and get stuck
- in MLF queue scheduling, a process is allowed to move between the queues when there is a need
- a typical multi-level feedback queue scheduler is defined by the following parameters:
    - number of queues
    - scheduling algorithms for each queue
    - method used to determine when to upgrade a process to a higher priority queue
    - method used to determine when to downgrade a process to a lower priority queue
    - method used to determine which queue a process should enter when that process needs CPU

### example

- we consider an example with three queues
    - Q0 uses RR with time quantum 8 and max quantum 8
    - Q1 uses RR with time quantum 16 and max quantum 16
    - Q2 uses FCFS
