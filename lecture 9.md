# lecture 9

Created: March 19, 2024 1:58 AM

# deadlock

- recall that OS is a resource manager
    - the **presence of a user** in the OS an be represented by a process, that will need to use resources— a process will consume CPU time, use up memory to execute, read inputs from a file, and send outputs to a printer

### example

1. consider two processes P1 and P2 communicating via pipes

- **P1**: read(fd2to1[0], b1, 80); write(fd1to2[1], b1, strlen(b1));

- **P2**: read(fd1to2[0], b2, 80); write(fd2to1[1], b2, strlen(b2));
- both processes will wait to read from the pipe before each goes ahead to write into the pipe— the result is a deadlock!
1. both P1 and P2 want to print a file on disk to printer— P1 gets hold of printer and wants to get file on disk, but P2 has already got the file on disk and wants to get printer— the result is a deadlock!

## resource model

- a **resource** is an object that is capable of being used
    
    
    | a process uses a file by opening the file and getting the file pointer or file descriptor |
    | --- |
    | a process gets a block of memory with malloc() |
    | a process uses I/O device via a system call to the device driver |
    | CPU is consumed by processes as arranged by the scheduler  |
- steps to use a resource:
    1. request for the resource (acquire or open)
    2. wait until the resource is allocated or granted
    3. use the resource (read or write)
    4. release the resource (return or close)

- a resource can only be used by a process after it issues a request to the resource manager and the manager approves its usage (grant the resource)

- a process releases the resource when done to the manager so that the next process waiting in line could use it
- we assume that all processes follow this rule
- resources could be grouped into types
    - there are m types of resources: R1, R2,…, Rm— each resource type $R_i$ has $W_i$ copies (or $W_i$ instances)
    - for example, there may be 10 shared buffers, 80 physical memory frames, and 4 worker threads in the thread pool
- a process utilizes a resource through the **request/use/release** cycle
- a **deadlock** is a set of blocked processes, each holding a resource and waiting to acquire a resource held by another process in the set
    - a process is blocked if it is waiting for a resource currently held by another process— we call this **resource deadlock**
- another type of deadlock is called **communication deadlock**— a set of processes are waiting for a message to be sent from processes (e.g. data to be written into a pipe) within the set, or an event to be triggered by the set (e.g. go ahead signal)

## deadlock characterization

- deadlock can only occur if all four necessary conditions are true
    
    
    | mutual exclusion  | only one process at a time can use a resource |
    | --- | --- |
    | hold and wait  | a process holding at least one resource is waiting to acquire additional resources held by other processes |
    | no preemption  | a resource can be released only voluntarily by the process holding it, after that process completes its task |
    | circular wait  | there is a chain of waiting processes {P1, P2,…,Pn,P1} such that P1 is waiting for a resource that is held
    by P2, P2 is waiting for a resource that is held by P3, …, Pn-1 is waiting for a resource that is held by Pn, and Pn is waiting for a resource that is held by P1 |

# resource allocation

- to study deadlock, we use **resource allocation graph** as a tool— a resource allocation graph has a set of vertices V and a set of edges E

- there are two types of vertices V:
    - P = {P1,P2, …, Pn} is the set of all processes
    - R = {R1, R2, …, Rm} is the set of all resource types

- there are two types of edges E:
    - **request edge** is a directed edge from P to R— Pi → Rj means that Pi is requesting for resource Rj
    - **assignment edge** is a directed edge from R to P— Rj → Pi means that Rj
    is allocated/assigned to Pi, which holds the resource
- if a request can be fulfilled by the resource manager, **a request edge will become an assignment edge and the direction of the edge is reversed**

## graph notation

![Screenshot 2024-03-19 at 5.22.09 PM.png](lecture%209%20096ccdf8fc6144a4bc795f1cfc49d94c/Screenshot_2024-03-19_at_5.22.09_PM.png)

![Screenshot 2024-03-19 at 5.22.26 PM.png](lecture%209%20096ccdf8fc6144a4bc795f1cfc49d94c/Screenshot_2024-03-19_at_5.22.26_PM.png)

### quick rules of thumb

- if the graph contains no cycle, then there is definitely no deadlock
- if the graph contains a cycle, is there a deadlock?
    - if there is only one instance per resource type, then a **cycle means that there is a deadlock**
    - if there are several instances per resource type, then a cycle only means that there is a **possibility of deadlock**
- thus, cycle is only a necessary condition of a deadlock, but it is not a sufficient condition for a deadlock

### algorithm

- before a resource is allocated, OS checks whether the allocation would result in a cycle— do not allocate the resource if a cycle is produced
    - the **request to resource** can be granted only if converting the request edge to an assignment edge does not result in a cycle in the resource allocation graph

## deadlock in databases

- recall that in resource allocation graph
    
    
    | if the graph contains no cycle, then there is no deadlock |
    | --- |
    | if the graph contains a cycle, but if there is only one instance per resource type, then a cycle means that there is a deadlock |
    | so in a one-instance resource situation, a cycle is a necessary and sufficient condition for a deadlock |
- in a database, the shared resource is the data item (or data record)
    - **locking a data item or record** implies exclusive access to it without being interfered by other users / transactions
    - there is just **one copy per data item or record**, unless a replicated database is used
    - database systems will execute the cycle detection algorithm periodically on the simplified resource allocation graph (called wait-for graph) to detect deadlocks, and that is very common under two-phase locking

# deadlock handling

- four types of methods to deal with a deadlock
    
    
    | ostrich approach  | ignore the deadlock problem and pretend that deadlocks never occur in the system |
    | --- | --- |
    | deadlock prevention  | ensure that the system will never enter a deadlock state |
    | deadlock avoidance  | allocate the resources very carefully so that system will not enter a deadlock state |
    | deadlock detection | allow the system to enter a deadlock state, detect it and then recover from it |

## deadlock prevention

- there are four necessary conditions— attack any one to ensure that it is not satisfied

### mutual exclusion

- for sharable resources, mutual exclusion is not true and there is no deadlock
- **problem**: some resources are inherently not sharable

### hold and wait

- ensure that whenever a process requests a resource, it does not hold any other resources
- this means that process should request and be allocated all its resources before it begins execution, or allow a process to request resources only when it has none
- **problem**: low resource utilization, starvation

### no preemption

- allow resources to be preempted from a process
- if a process that is holding some resources requests another resource that cannot be allocated to it, then all resources currently being held are released (preempted)
- preempted resources are added to the list of resources for which the process is waiting
- process will be restarted only when it can regain its old resources, as well as the new ones that it is requesting
- **problem**: not all resources can be preempted, starvation is possible

### circular wait

- order all resource types with possibly a number
- each process can only request resources in an increasing order of resource number
- then no one can wait in a cycle, because everyone can only wait for a higher number

## prevention in databases

- to make use of deadlock prevention technique, look at those four conditions:
    
    
    | mutual exclusion | data items cannot be shared for updating |
    | --- | --- |
    | hold and wait | you may request data transactions to lock all data items at the beginning, but they could be held for too long, and sometimes hard to predict future need |
    | non-preemption | allow high priority transactions to take over data items— this is adopted in some database systems, in the wound-wait protocol |
    | circular wait | do not allow transactions to wait in a cycle- this is also adopted in some database systems to enforce the waiting order, by ordering the data items, or in the wait-die protocol |

# deadlock avoidance

- use additional information about how resources are requested
- carefully control the allocation of resources
    - the simplest and most useful model is to ask each process to declare the maximum number of resources of each type that it may need
- OS can check in advance when allocating resource to ensure that system remains in a safe state that will not enter a deadlock state

- **deadlock avoidance algorithm** dynamically examines the resource allocation state to ensure that there can never be a circular-wait condition

- **resource allocation state** is defined by the number of available and allocated resources and the maximum demands of the processes

![Screenshot 2024-03-19 at 5.40.50 PM.png](lecture%209%20096ccdf8fc6144a4bc795f1cfc49d94c/Screenshot_2024-03-19_at_5.40.50_PM.png)

- if a system is in a safe state, there is no deadlock

- if a system is in an unsafe state, there is a possibility of deadlock
    - deadlock occurs only if a bad sequence of events happen— there may not be deadlock if there is some luck
- **deadlock avoidance**: ensure that a system will never enter an unsafe state, with care

## safe state

- a **state is safe** if the system can allocate the remaining resources in a certain order so as to avoid a deadlock
    - that **allocation order** is indicated by a safe sequence
- safe sequence <Pi1, Pi2, …, Pin> is a sequence with the property: for each Pj, the resources that Pj can still request can be satisfied by currently available resources plus resources currently held by all preceding processes Pk with k < j
- based on a safe sequence, if the resources needed by Pj are not immediately available, then Pj can wait until all Pk have finished

- when Pk finishes, Pj can obtain its needed resources, execute, return its allocated resources, and terminate

- when Pj finishes, Pj+1 can obtain its needed resources, and so on

### safe sequence example

![Screenshot 2024-03-19 at 5.45.28 PM.png](lecture%209%20096ccdf8fc6144a4bc795f1cfc49d94c/Screenshot_2024-03-19_at_5.45.28_PM.png)

![Screenshot 2024-03-19 at 5.45.42 PM.png](lecture%209%20096ccdf8fc6144a4bc795f1cfc49d94c/Screenshot_2024-03-19_at_5.45.42_PM.png)

- for each Pj, the resources that Pj can still request can be satisfied by currently available resources plus resources currently held by all preceding processes Pk with k < j

## banker’s algorithm

- n: number of processes (P1 to Pn)

- m: number of resources types (R1 to Rm)

| available[m]: array of m | number of instances of resource type $R_j$ available |
| --- | --- |
| max$_i$[m]: array of m | maximum number of instances of resource type $R_j$ that process $P_i$ may request |
| allocation$_i$[m]: array of m | number of instances of $R_j$ currently allocated to $P_i$ |
| Need$_i$[m]: array of m | additional number of instances of $R_j$ that $P_i$ may need to complete its task

$max_i
[j] – allocation_i
[j]$ |

- safety algorithm
    - define temporary arrays work[m] and finish[n]
        
        ```python
        work ← available
        finish ← false
        while not done do
        	find an i such that finish[i] = false and needi ≤ work
        	if no such i exists, break from the loop
        	work <- work + allocationi
        	finish[i] <- true
        ```
        

- request algorithm
    - define request$_i$[m] to be requests by process $P_i$
        
        ```python
        if not (Requesti ≤ Needi) then raise error (Pi has exceeded its maximum claim) and stop
        if not (Requesti ≤ Available) then Pi must wait, since resources are not available
        
        else pretend to allocate requested resources to Pi by modifying the allocation state
        	available = Available – Requesti
        	allocationi = Allocationi + Requesti
        	needi = Needi – Request
        ```
        
    - run safety algorithm on this pretended state
        - if safe, then resources are allocated to $P_i$, else $P_i$ must wait and the old resource allocation state is restored

### example

- 5 processes, P0 to P4, 3 resource types A, B, C, with 10, 5 and 7 instances
- after some allocation exercises, there are only 3, 3 and 2 instances of A, B, C available— the current allocation state is shown
    
    ![Screenshot 2024-03-19 at 5.58.12 PM.png](lecture%209%20096ccdf8fc6144a4bc795f1cfc49d94c/Screenshot_2024-03-19_at_5.58.12_PM.png)
    
- we can find a safe sequence <P1, P3, P0, P2, P4> and system is in a safe state

![Screenshot 2024-03-19 at 6.00.36 PM.png](lecture%209%20096ccdf8fc6144a4bc795f1cfc49d94c/Screenshot_2024-03-19_at_6.00.36_PM.png)

![Screenshot 2024-03-19 at 6.05.11 PM.png](lecture%209%20096ccdf8fc6144a4bc795f1cfc49d94c/Screenshot_2024-03-19_at_6.05.11_PM.png)

- the full list
    
    ![Screenshot 2024-03-19 at 6.05.31 PM.png](lecture%209%20096ccdf8fc6144a4bc795f1cfc49d94c/Screenshot_2024-03-19_at_6.05.31_PM.png)
    

### example

- given the following system state, compute the need information— after some allocation exercises, there is 1 instance each for A, B, and C available (e.g. 1 1 1)

![Screenshot 2024-03-19 at 6.06.44 PM.png](lecture%209%20096ccdf8fc6144a4bc795f1cfc49d94c/Screenshot_2024-03-19_at_6.06.44_PM.png)

![Screenshot 2024-03-19 at 6.06.47 PM.png](lecture%209%20096ccdf8fc6144a4bc795f1cfc49d94c/Screenshot_2024-03-19_at_6.06.47_PM.png)

# deadlock detection

- allow system to enter a deadlock state
- detect deadlock by deadlock detection algorithm
    - executed periodically or when system suspects a deadlock
- execute a **deadlock recovery scheme** to recover from deadlock
    - process termination
    - kill all deadlocked processes
    - kill one victim process at a time until the deadlock cycle is eliminated
    - consideration: priority of the process, progress of process, resources used/needed by process, number of processes killed
- **process rollback**
    - return to a safe state and restart process from that state
- **possibility of starvation**: the same process may always be picked as victim

## wait-for-graph

- wait-for graph checking is the most common deadlock detection algorithm,
useful when each resource has only one instance
- a **wait-for graph** for processes is a condensed form of a resource allocation graph
    - nodes of the graph are processes
        - resources are eliminated
    - if $P_i$ → $R_k$ and $R_k$ → $P_j$ are in resource allocation graph, then we have $P_i$ → $P_j$ in wait-for graph
        - $P_i$ is waiting for a resource Rk allocated to $P_j$, so $P_i$ is waiting for $P_j$ to return the resource
- there is a deadlock if and only if there is a cycle in the wait-for graph
- periodically, check for cycles in the wait-for graph to detect deadlock

![Screenshot 2024-03-19 at 6.47.45 PM.png](lecture%209%20096ccdf8fc6144a4bc795f1cfc49d94c/Screenshot_2024-03-19_at_6.47.45_PM.png)

## detection algorithm

- for resources with multiple instances, the deadlock detection algorithm is very similar to banker’s algorithm, by finding a completion sequence

- n: number of processes (P1 to Pn)

- m: number of resources types (R1 to Rm)

| available[m]: array of m | number of instances of resource type Rj available |
| --- | --- |
| allocation$_i$[m]: array of m | number of instances of Rj currently allocated to Pi |
| request$_i$[m]: array of m | number of instances of Rj currently requested by Pi |

```python
Work <- Available
for all i
	if Allocationi ≠ 0 then Finish[i]  false else Finish[i]  true
while not done do
	find an i such that Finish[i] = false and Requesti ≤ Work
	if no such i exists, break from the loop
	Work <- Work + Allocationi
	Finish[i] <- true
```

- if for all i, finish[i] = true, then no deadlock

- if for some i, finish[i] = false, then deadlock
- the set of processes j with finish[j] = false are involved in the deadlock

### example

![Screenshot 2024-03-19 at 10.53.20 PM.png](lecture%209%20096ccdf8fc6144a4bc795f1cfc49d94c/Screenshot_2024-03-19_at_10.53.20_PM.png)

- 5 processes, P0 to P4, 3 resource types A, B, C, with 4, 2 and 4 instances— after some allocation exercises, there is just one C available; the current allocation state is as shown (available = (0 0 1))

![Screenshot 2024-03-19 at 10.54.03 PM.png](lecture%209%20096ccdf8fc6144a4bc795f1cfc49d94c/Screenshot_2024-03-19_at_10.54.03_PM.png)

- all possible completion sequences
    
    ![Screenshot 2024-03-19 at 10.54.32 PM.png](lecture%209%20096ccdf8fc6144a4bc795f1cfc49d94c/Screenshot_2024-03-19_at_10.54.32_PM.png)