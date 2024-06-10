# lecture 5

Created: February 15, 2024 12:54 AM

# interprocess communication

- (IPC) mechanisms allow processes to communicate and to synchronize
    - data transfer and sharing
    - event notification
    - resource sharing
    - synchronization
    - process control
- **message passing** is more common in practice
    - it is hard to share memory content across different computers over the network, and even sharing memory within the same computer with several CPU’s is not always straightforward
- workable for a shared bus or cross-bar switch to multiple memory banks, but not easy for individual memory modules with CPU

## direct communication

- processes must name each other explicitly
    - **send(P, message)**: send a message to process P
    - **receive(Q, message)**: receive a message from process Q
- properties of communication link
    
    
    | links are established automatically |
    | --- |
    | a link is associated with exactly one pair of communicating processes |
    | between each pair there exists exactly one link |
    | the link may be unidirectional, but is usually bi-directional |

## indirect communication

- messages are directed and received **from mailboxes** (also called **ports**)
    - each mailbox has a unique identifier
    - processes can communicate only if they share a mailbox
- properties of communication link
    
    
    | link is established only if processes share a common mailbox |
    | --- |
    | a link may be associated with many processes |
    | each pair of processes may share several links |
    | link may be unidirectional or bi-directional |
- operations:
    - create a new mailbox
    - send and receive messages through mailbox
    - destroy a mailbox

## synchronization

- message passing may be either blocking or non-blocking

- **blocking** is considered synchronous (need to wait)
    - **blocking send** ensures that the sender is blocked until the message is received by the receiver
    - **blocking receive** ensures that the receiver is blocked until a message is available from the sender

- **non-blocking** is considered asynchronous (no need to wait)
    - **non-blocking send** allows the sender to send a message and can always continue
    - **non-blocking receive** allows the receiver to receive a valid message or receive null if message is not available
    

|  | blocking | non-blocking |
| --- | --- | --- |
| blocking | blocking send / blocking receive | blocking send / non-blocking receive |
| non-blocking | non-blocking send / blocking receive | non-blocking send / non-blocking receive |

## buffering

- sender may have sent several messages and receiver has not read them
- a queue of messages is attached to the link to store (buffer) those outstanding messages
- three different implementations for the message queue:
    
    
    | zero capacity | no message could be buffered
    
    sender must wait for receiver and vice versa |
    | --- | --- |
    | bounded capacity | queue can only store up to n messages
    
    sender must wait if the queue is full |
    | unbounded capacity | queue can hold infinite number of messages
    
    sender never needs to wait |

## providing IPC

- **IPC package** is used to establish interprocess communication
- the package sets up a data structure in **kernel space**
- the data structure is usually persistent, i.e. it will normally continue to exist until being deleted
- appropriate system calls are provided for a programmer to develop a program using IPC mechanism — creating, writing, reading, deletion

# unix/linux IPC

![Screenshot 2024-02-15 at 5.11.45 PM.png](lecture%205%20af3a690ed6644f4da7082c3cde36e96a/Screenshot_2024-02-15_at_5.11.45_PM.png)

- what would happen in MS-DOS
    - case 1: treat a.out as [a.com](http://a.com)
    - case 2: treat a.out as a.exe
- a good OS will provide proper protection mechanism against system crash
    - at most only the user program should crash, but not others
- with protection, a process cannot (and should not) access the memory space of other processes, nor the system (kernel) memory space — **no sharing can be done to the data part**

### shared memory mechanism

- special system calls are provided to create shared memory segments within kernel memory space
    - user processes make system calls to create / remove / write to / read from the shared kernel memory space
    - this is not flexible and is seldom used
- **unix pthreads library** provides more well-defined and more comprehensive mechanisms to support shared memory, since threads within the same process naturally share the memory space
    - linux also has pthreads library available

### message passing mechanism

- achieved via pipes and sockets
- two major types of pipes:
    - **unnamed pipes**
    - **named pipes**
- conceptually, a socket looks like a named pipe — a unix/linux pipe is a unidirectional, FIFO, unstructured data stream:
    
    
    | a buffer allocated within the kernel |
    | --- |
    | operates in one direction only |
    | some plumbing is required to use a pipe |
    | fixed maximum size |
    | no data type being transferred |
    | very simple flow control |
    | cannot support broadcast |

## IPC for user (UDP)

- at user level, IPC can be achieved by using the system program `write`
    - it allows sending one-way messages to another user by “writing” a message directly to it — note that write is also a system call for I/O
    - you can view this system program write (at user level) as an easy way for system call write (at program level)
- **usage**: `write user`
    - you can then type messages, which will appear on the screen of user
    - terminate your sequence of messages with `<Ctrl>-D` or break with `<Ctrl>-C`

### example

![Screenshot 2024-02-15 at 5.17.51 PM.png](lecture%205%20af3a690ed6644f4da7082c3cde36e96a/Screenshot_2024-02-15_at_5.17.51_PM.png)

## IPC for user (TCP)

- at user level, a more advanced utility program called `talk` exists in unix and many linux
    - it is a two-way, screen-oriented communication program across machines, the ancestor of other IM, IRC and chat programs — it is available on MacOS, but it is not installed on apollo
- **usage**: `talk user@machine [or user@IPaddress]`
    - a message will appear on the screen of user
        - talk: connection requested by your_address
        - talk: respond with: talk your_address
    - invited user can accept by executing talk your_address and the two users can then exchange texts

## IPC in program

- program-based IPC makes use of the pipe
    - the most common approach is to use the **unnamed pipe**
- an unnamed pipe is created via system call `pipe()`
    - a kernel buffer is allocated
    - two file descriptors (fd) forming a 2-element array are created to identify the read-end and write-end of the pipe
    - consistent with the meaning of `stdin` and `stdout` for file I/O, **fd[0] is for reading** and **fd[1] is for writing**
    - a process will call `read()` to read data from the pipe via fd[0] and call `write()` to write data to the pipe via fd[1]

![Screenshot 2024-02-15 at 5.20.32 PM.png](lecture%205%20af3a690ed6644f4da7082c3cde36e96a/Screenshot_2024-02-15_at_5.20.32_PM.png)

# unnamed pipe

![Screenshot 2024-02-15 at 5.20.58 PM.png](lecture%205%20af3a690ed6644f4da7082c3cde36e96a/Screenshot_2024-02-15_at_5.20.58_PM.png)

- a pipe should connect two processes, not to connect a process with itself
- child processes need to be created via system call `fork()` like before, but after the pipe is created
    - recall that fork() will make **the child an exact copy of the parent**

![Screenshot 2024-02-15 at 5.21.31 PM.png](lecture%205%20af3a690ed6644f4da7082c3cde36e96a/Screenshot_2024-02-15_at_5.21.31_PM.png)

- now both child and parent can write to fd[1] and read from fd[0]
    - there is only ONE pipe, so **both will write to the SAME pipe and read from the SAME pipe**
- to avoid confusion, the unused ends must be closed
    - **closing the read end of parent** and **write end of child** via system call `close()`, parent can write to child and child can read from parent, and not the other way round

![Screenshot 2024-02-15 at 5.25.23 PM.png](lecture%205%20af3a690ed6644f4da7082c3cde36e96a/Screenshot_2024-02-15_at_5.25.23_PM.png)

- example
    
    ![Screenshot 2024-02-15 at 5.25.44 PM.png](lecture%205%20af3a690ed6644f4da7082c3cde36e96a/Screenshot_2024-02-15_at_5.25.44_PM.png)
    
- child often needs to send data to parent — this can also be achieved by creating another pipe by closing the unused ends for this usage
    - **closing the read end of child** and **write end of parent** via system call close(), child can write to parent and parent can read from child, and not the other way round
- how can a parent talk to child and get data back from child?
    - keep both pairs of pipes, **one for parent to write to child as before, and one for child to write to parent as here**
    - communication can thus go in both direction, i.e. **bi-directional communication**
- programming guidelines to reduce bugs:
    - draw a diagram for all the pipes between parent and children
    - indicate the direction of data flow on each pipe
    - create array variable(s) to store fd[] for all the pipes
    - since parent will see all pipes, and children can see part or all of them, each process needs to close all unused ends in the diagram based on the fd[] variables
- shell programs implement the **command level pipe** (e.g. `ls | wc`) via a combination of fork() / pipe() / exec*()
    
    
    | fork() | is used to create child processes to hold and execute the relevant commands, e.g. ls and wc |
    | --- | --- |
    | pipe() | is used to create the pipe(s) to be shared between the processes, e.g. ls and wc |
    | exec*() | is used to replace the child processes with the required programs, e.g. ls and wc |
    - note that three child processes and two pipes will be created by the shell to execute a chained command like `prog1 | prog2 | prog3`

## unnamed pipe limit

- what is the maximum number of unnamed pipes that can be created?
    - remember that each unnamed pipe will occupy two file descriptors in unix
    - on apollo and apollo2, **maximum number of fd = 1024**, but fd 0, 1 and 2 are already reserved for `stdin`, `stdout`, and `stderr`

# communication topology

| star | parent or coordinator talks to each individual child process

simple to do, but bottleneck at parent

very few pipes

at most 2 rounds of messages | 2(n-1) for both directions

bottleneck/single-point-of-failure at parent with 2(n-1) pipes |
| --- | --- | --- |
| ring | processes connected in a ring

very few pipes

quite many rounds of messages | 2n

no particular bottleneck |
| linear | processes connected in a single line or chain

very few pipes

many rounds of messages | 2(n-1)

node failure will separate the communication |
| fully connected | parent or child processes directly talk to one another

need many pipes

fast communication | n(n-1)

no bottleneck: everybody with 2(n-1) |
| tree  | parent at each level of tree talks to its child process

very few pipes

more rounds of messages | 2(n-1)

bottleneck spread around |
| mesh | links follow some structure, often grid-like

fewer pipes than fully connected

could be harder to program and maintain

need routing for non-neighboring processes

flexible | between 2(n-1) and n(n-1)

each node is connected to around 4 neighbors |
| hypercube  | links follow a cube-structure

fewer pipes than fully connected

well-established routing mechanism for non-neighboring processes

flexible | between 2(n-1) and n(n-1)

each node is connected to log2 n neighbors |
| bus  | not considered with pipes (like ethernet)  |  |

## linear communication

![Screenshot 2024-02-15 at 5.35.26 PM.png](lecture%205%20af3a690ed6644f4da7082c3cde36e96a/Screenshot_2024-02-15_at_5.35.26_PM.png)

## star communication

![Screenshot 2024-02-15 at 5.36.39 PM.png](lecture%205%20af3a690ed6644f4da7082c3cde36e96a/Screenshot_2024-02-15_at_5.36.39_PM.png)

# named file

- a **named pipe is actually a file** and is created by creating a special file, via the system call `mkfifo()`
- after a named pipe is created, any two processes knowing the name of the file will first open() the pipe like a file
- they can then communicate by writing to and reading from it, using `write()` and `read()` accordingly like a file
- the pipe remains even after both processes have completed
    - it is a good programming practice to create the named pipe and then delete the named pipe inside the program at the end, via `unlink()` system call

![Screenshot 2024-02-15 at 5.37.46 PM.png](lecture%205%20af3a690ed6644f4da7082c3cde36e96a/Screenshot_2024-02-15_at_5.37.46_PM.png)

```
char pipename[] = "/tmp/mypipe";
```

![Screenshot 2024-02-15 at 5.38.20 PM.png](lecture%205%20af3a690ed6644f4da7082c3cde36e96a/Screenshot_2024-02-15_at_5.38.20_PM.png)

- sometimes it is clumsy to develop two separate programs for communication
    - note that there is no distinction between parent and child
- it is also tedious for the user to physically run the two programs (and to make sure to run the two programs in proper order, i.e. run the program creating the named pipe first)
    - this can be done by executing them on two different windows or by executing one program and put it to background (**using &**) and then run the second program
- we could make use of the fork() mechanism for the parent to create the child and run the two programs in one single shot or we could also combine the two programs into one

> advantage: pipe is always created before use
> 

![Screenshot 2024-02-15 at 6.15.30 PM.png](lecture%205%20af3a690ed6644f4da7082c3cde36e96a/Screenshot_2024-02-15_at_6.15.30_PM.png)

![Screenshot 2024-02-15 at 6.15.42 PM.png](lecture%205%20af3a690ed6644f4da7082c3cde36e96a/Screenshot_2024-02-15_at_6.15.42_PM.png)

- among the three different approaches in using named pipes, which one do you prefer?
    - option 1: two separate programs run separately
    - option 2: two programs, with one program executing the other
    - option 3: one single program
- option 1 is like **socket programming in a network**
    - the special file is replaced by an IP address (or a domain name) and a port number
    - socket programming is improved as a new process would be created by P1 with a new communication path talking to incoming process P2, i.e. the server listens to the port and forks a child upon accepting a connection to handle the communication needs
- named pipes could be structured based on different topologies
    - even with the potentially large naming space (on the names of pipes), it is still less common to involve many processes, since a name should be given to each named pipe (no name is needed for unnamed pipes)
    - managing named pipes with many processes is tedious and error-prone, especially explicit creation and deletion are required — it is not uncommon to see some left-behind named pipes under /tmp directory of apollo / apollo2
- sockets are more common and more flexible — advantage of named pipes is often related to the support of heterogeneous programs, perhaps from different users

> unnamed pipes are used more commonly than named pipes
> 

## pipes in IPC

- pipes in programming level and user (shell) level
    - user level:
        - prog1 | prog2 | prog3
        - effect like `prog1 > tempfile1; prog2 < tempfile1 > tempfile2; prog3 < tempfile2`

- programming level with **unnamed pipe**:
    - prog1 creates unnamed pipe1 and closes unused ends
    - prog1 forks a child and uses exec to load and run prog2
    - prog2 creates unnamed pipe2 and closes unused ends
    - prog2 forks a child and uses exec to load and run prog3

- programming level with **named pipe**:
    - based on option 1, create named pipe1 between prog1 and prog2
    - create named pipe2 between prog2 and prog3
    - effect like prog1 > pipe1; prog2 < pipe1 > pipe2; prog3 < pipe2
- however, the three programs are running concurrently with named pipes, instead of sequentially with files