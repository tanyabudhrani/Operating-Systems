# lecture 1

Created: January 4, 2024 8:24 PM

# operating system

- at the foundation of all system software, it performs basic tasks such as controlling and allocating memory, prioritizing system requests, controlling input and output devices, facilitating networks, and managing files
- the manager of the whole computer system
    - executes user programs to solve user problems
    - makes the computer system convenient to use
    - use the computer hardware in an efficient manner

## computer system

| hardware | provide basic computing resources (e.g. CPU, memory, I/O) |
| --- | --- |
| operating system  | controls and coordinates use of hardware among various applications and users (e.g. unix, linux) |
| application programs | define the ways that system resources are used to solve computing problems  |
| users | people, devices, other computers |

## computer system operation

- at the hardware level, one or more CPUs and device controllers are connected through common bus to access shared memory
- concurrent execution of CPU and devices are competing for access to memory (memory cycles)

![Screenshot 2024-01-05 at 12.29.10 PM.png](lecture%201%206e838d0cb0814baabd2abf251226a168/Screenshot_2024-01-05_at_12.29.10_PM.png)

> a manager should allocate and manage company resources effectively, and control working of subordinates so that they deliver performance
> 

### resources manager

- manage the allocation of computer resources
- decide between conflicting requests for efficient and fair use of resources

### control program

- controls execution of programs to prevent errors and improper use of computers

## OS boundary

- everything a vendor ships when you buy an OS
- the one program that is running at all times on a computer
    - often called the **kernel** (central part or core) — everything other than the kernel is either the system program (shipped with the OS) or an application program (written by other programmers)

## kernel vs OS

- an operating system is a software which contains different programs in it, and a kernel is one of them
    - the kernel is a program which is close to the hardware of the system and so, it performs all the tasks of the operating systems which involve a link between both the hardware and the user level applications
- the kernel thus functions at the lowest level of the operating system, performing tasks such as file management, process management, memory management, etc

![Screenshot 2024-01-17 at 8.10.47 PM.png](lecture%201%206e838d0cb0814baabd2abf251226a168/Screenshot_2024-01-17_at_8.10.47_PM.png)

### kernel vs shell

| kernel  | shell |
| --- | --- |
| a computer program which acts as the core of the computer’s operating system and has the control over everything in the system  | a computer program which works as the interface to access the services provided by the operating system  |
| core of the system that controls all the tasks of the system  | interface between the kernel and user  |
| does not have types | has types such as bourne shell, c shell, korn shell, etc |

## four important components within OS

| processor manager | handle jobs within system to be executed by CPU  |
| --- | --- |
| memory manager | allocate the memory to be used by jobs  |
| file manager | provide systematic storage for permanent data or programs  |
| device manager | control the different devices, mostly physical I/O devices |
- these components interact with one another and are controlled by the users via the user command interface

![Screenshot 2024-01-05 at 12.38.29 PM.png](lecture%201%206e838d0cb0814baabd2abf251226a168/Screenshot_2024-01-05_at_12.38.29_PM.png)

### resource manager

- OS must manage the use of resources (e.g. processor or CPU, memory, file, devices) by users
- the presence of a user in the OS could be represented by a task or process which will need to use resources
    - a process will consume CPU time, use up memory to execute, read inputs from a file, and send outputs to a printer

### control program

- OS should control to avoid bad things, or it should try to recover

## process management

- a process is a **program in execution**

- programs are passive entities — you write a program to be executed

- processes are active entities — you run a program to create a process
- a process needs resources like CPU, memory, files to perform its functions, and it executes its instructions sequentially, one at a time, until completion — a completed process will return resources that it has taken (or forced taken back)
- a typical computer system has many processes running concurrently using **multiprogramming**
    - when a process is using the CPU, another process may be waiting for the user to input some data
- OS is responsible for several process management activities
    
    
    | create and terminate processes |
    | --- |
    | suspend and resume processes  |
    | allow for process communication |
    | allow for process synchronization  |
    | handle deadlocks |

## memory management

- data should be in memory when a process is executing, and instructions of the program for a process should be in memory for execution
- memory management controls when and what data or program code should be in memory — this is important if the memory is not sufficient to hold all useful data and program code
- OS must keep track of the usage of memory
    - data for two different users should not be put in the same memory location

## storage management

- storage (disk or tape) is controlled by its device
- devices have different device features (e.g. speed, capacity, data-transfer rate, access method)
    - e.g. a tape must go from the beginning to the end, and a disk can begin anywhere in the middle
- storage that a user could use is often generalized into a file
- a user of a file does not need to concern with the speed or storage method
    - e.g. a file on a tape and a file stored on the disk can be accessed in a similar fashion, except for the time needed

### file system

- files are often collected and managed under a file system, where files are organized into **directories**
    - each directory is a collection of related files

## I/O subsystem

- devices are typically I/O devices (keyboard, mouse, monitor, disk), mass-storage devices (tape, disk), or communication devices (network card)
    - OS should hide variations and details of hardware devices from the user
- memory management concerning devices
    - **buffering**: storing data temporarily while it is being transferred
    - **caching**: storing parts of data in faster storage for better performance
    - **spooling**: overlapping output of one job with input of another
- provides a general device-driver interface and provides drivers for specific hardware devices

## mass-storage management

- mass-storage devices provide backup for data that does not fit in main memory or data that must be kept for a long period of time
- in OS, the system performance depends a lot on an efficient disk or secondary storage subsystem
- OS activities on mass-storage
    - **free-space management**: used and free disk clusters
    - **storage allocation**: which part to be used to store a file
    - **disk scheduling**: movement of the disk head and arm

## user command interface

- to connect a user to OS, there should be an interface, and through the interface, the user must be able to issue commands to the OS which accepts the commands and performs tasks according to user need

- **batch interface**: commands are stored in a file beforehand

- **interactive interface**: commands are issued on the fly by users (GUI and CLI)

### command line interface

- it is a program that loops to receive commands from the user
    - **built-in command**: one that is implemented in the kernel (dir in DOS or cd in unix)
    - **non-built-in command**: name of the system program (format.com or lpr — unix command for print)
- several implementation with similar functionalities may exist for user choice (e.g. unix vs linux shells)

### graphical user interface

- a desktop interface involving mouse, keyboard, and monitor to support WYSIWYG (what you see is what you get) using icons to represent files, programs, and actions

## protection

- a mechanism for controlling access of processes or users to resources defined by the system
- provide ways for users to specify the control and to enforce it

## security

- the defense of the computer system against internal and external attacks
    - e.g. worms, viruses, denial of service, identity theft, theft or service
- OS can only protect against some of the attacks, others should be managed by humans
- systems generally distinguish a user from another user — depending on who the user is, different privileges on data or resources are given
    - users are associated with unique user IDs which is then associated with all files and processes of that user to determine access control
    - **group IDs** allow sets of users to be defined and control shared access to processes or files

# network management

- **network operating system**
    - add the networking capability to an operating system
    - unix allows you to write programs to communicate with another program on another computer

- **distributed operating system**
    - users do not see the existence of the network
    - services and data are just transparent with respect to location and access

## operating system services

| program execution | the system must be able to load a program into memory and to run that program, and to complete the execution  |
| --- | --- |
| I/O operations | a process may require I/O and the system should support it  |
| file manipulation | a process may need to read and write files and directories, create and delete them, search them, etc |
| communication | processes may need to exchange information on the same computer or between computers over a network — this is called interprocess communication |
| error detection | OS needs to be constantly aware of possible errors, which occur in CPU and memory, in I/O devices, in user program  |
- operating system also provides these functions to improve system efficiency
    
    
    | resource allocation  | when multiple users or processes are running concurrently, resources must be allocated to each of them |
    | --- | --- |
    | accounting  | to keep track of which user uses how much and what kinds of computer resources |
    | protection and security | the owners of information stored in a multi-user computer system would want to control the use of their information
    
    protection ensures that access to system resources is controlled
    
    security requires user authentication and defense of external I/O devices from invalid access attempts |