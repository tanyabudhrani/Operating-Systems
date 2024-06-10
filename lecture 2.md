# lecture 2

Created: January 14, 2024 9:28 PM

## operating system mechanism

- since the OS is a program itself and a process is a program in execution, the OS is essentially one or more processes— when there are multiple processes executing, how can the OS control and manage them?
    - the OS relies on a special alarm system, called the **interrupt processing mechanism** which allows the OS to grasp the CPU and make a decision to control the system

# I/O processing

- one can perform I/O when CPU is doing useful work
- when I/O is completed, OS needs to put aside its current work and look at I/O device for the next instruction
    - the event that I/O completed causes an interrupt
    - the suspension of current work to handle the event is called **interrupt processing**

 

![Screenshot 2024-01-15 at 1.31.59 PM.png](lecture%202%20ce1535ee14d641afbbd19ec4b863c9a9/Screenshot_2024-01-15_at_1.31.59_PM.png)

## synchronous processing

- after I/O starts, user program will wait until I/O completion and does nothing or until interrupt is received
- at most one I/O request is outstanding at a time, no simultaneous I/O processing

## asynchronous processing

- after I/O starts, user program continues to execute without waiting for I/O to complete, only after does it receive an interrupt
- there could be several I/O requests working together

![Screenshot 2024-01-15 at 1.34.16 PM.png](lecture%202%20ce1535ee14d641afbbd19ec4b863c9a9/Screenshot_2024-01-15_at_1.34.16_PM.png)

### device status table

- you can use a **device-status table** to store the status for each I/O device with its type and state

![Screenshot 2024-01-15 at 1.35.11 PM.png](lecture%202%20ce1535ee14d641afbbd19ec4b863c9a9/Screenshot_2024-01-15_at_1.35.11_PM.png)

# interrupt processing

- an interrupt is a signal to the CPU to tell it about the occurrence of a major event
    - e.g. an error in calculation, completion of an I/O, hardware alarm, or user-generated event (often called a **trap** or **signal**)
- interrupts are the mechanism the OS uses so that it can turn attention to other activities and manage resources, because the interrupt will seize the CPU

### maskable interrupt

- interrupt that may be ignored or handled later due to its low priority

### non-maskable interrupt

- an interrupt that cannot be ignored and must be handled immediately
- a non-maskable interrupt may be “over” interrupted by another non-maskable higher priority interrupt

### example

![Screenshot 2024-01-24 at 11.29.08 AM.png](lecture%202%20ce1535ee14d641afbbd19ec4b863c9a9/Screenshot_2024-01-24_at_11.29.08_AM.png)

- depending on the cause of interrupts, we can classify them into 3 categories
    
    
    | program | caused by conditions within the CPU that requires attention (e.g. illegal instruction, overflow, division by zero, memory access violation, special instruction) |
    | --- | --- |
    | I/O | caused by I/O related events (e.g. I/O completion or device errors)  |
    | timer | caused by the hardware timer of the system to handle time-related events  |

# interrupt handling

- after a non maskable interrupt occurs, CPU will put aside the process it is executing, by saving the **program counter and register values on a stack**; then, it looks up an interrupt table, using the **interrupt number as an index**, for a procedure for execution
    - we need a stack to store the PC and register because it is possible that when an interrupt is being serviced, another one may arrived— if the new interrupt has a lower priority, it will wait till the first one completes, otherwise it will seize the CPU from the current interrupt handler
    

### low priority

### high priority

![Screenshot 2024-01-15 at 1.42.49 PM.png](lecture%202%20ce1535ee14d641afbbd19ec4b863c9a9/Screenshot_2024-01-15_at_1.42.49_PM.png)

- the procedure is called the **interrupt handler**, or **interrupt service routine (ISR)** and is executed in response to the interrupt
- after the interrupt is serviced, the CPU resumes the suspended program and returns to the next instruction pointed to by the saved PC

## operating system operation

- almost all OS are interrupt-driven; without interrupts, there is no way for the OS to get back the CPU from a program falling into an infinite loop
    - in PC, you could break a program with `<Ctrl><Alt><Del>` and kill the task manager since this instruction triggers a hardware interrupt
    - in unix, you could break a program with `<Ctrl><C>` to send a signal (user trap) to the program— if there is no signal handler in the program, it will be terminated, otherwise, some interesting behavior is resulted called **signal processing/system programming**
- interrupts allow the OS to gain control of the system when necessary
- basic interrupts are driven by hardware (often I/O devices or special interrupt pin to the microprocessor)
    - a **trap** is a software-generated interrupt caused either by an error or a user request— **software errors generate exceptions or traps**
- **the timer mechanism**: a simple way to prevent infinite loop and resources hogging by processes by setting an interrupt to occur after a specific time period
    - **watchdog timer**: operating system decrements counter upon timer interrupt— when counter reaches zero, something wrong happens, so it kills the process or takes back resources (e.g. unlocking a file) via the timer interrupt mechanism

![Screenshot 2024-01-24 at 11.34.53 AM.png](lecture%202%20ce1535ee14d641afbbd19ec4b863c9a9/Screenshot_2024-01-24_at_11.34.53_AM.png)

- since interrupt processing is important, special care must be taken in writing interrupt handlers, such as allowing or disabling interrupts
    - if a user program can disable interrupts, there is no way for the OS to get back the CPU and do some remedial action, so the result is a **system hang-up**
- the hardware should thus provide help to guard against an ordinary user from executing special interrupt related-commands
    - in terms of computer architecture, these commands are called **privileged commands**, and the common solution is to provide dual operation mode in CPU hardware

## user and kernel mode

- **dual-mode operation**: allows the OS to protect itself and other system components

- **user mode**: can only execute normal instructions, but not privileged instructions

- **kernel mode**: can execute all instructions (also called system, privileged, or supervisor mode)
- hardware provides a **mode bit** which provides the ability to distinguish when the system is running user programs or system programs
    - user mode on = user process | user mode off = kernel/system process
- if a user process executes in **user mode**, it can perform I/O that needs to execute privileged instructions through system calls since system calls are entry ports into the OS
    - a **system call** is actually a trap that changes from user mode to kernel mode, executes the privileged I/O command, returns from system call and reverts back to user mode to continue execution

![Screenshot 2024-01-15 at 1.57.12 PM.png](lecture%202%20ce1535ee14d641afbbd19ec4b863c9a9/Screenshot_2024-01-15_at_1.57.12_PM.png)

# system call

- a programming interface to the services provided by the OS whose nature is similar to that of a procedure call
    - often written in a high level language like C/C++ and provides the interface to execute these functions by user programs via an application program interface (API)

### example

![Screenshot 2024-01-24 at 12.04.06 PM.png](lecture%202%20ce1535ee14d641afbbd19ec4b863c9a9/Screenshot_2024-01-24_at_12.04.06_PM.png)

- inside the OS, a number would be associated with each system call

- system-call interface maintains a table indexed according to these numbers— such a table looks like a table of interrupt handlers

![Screenshot 2024-01-24 at 12.04.32 PM.png](lecture%202%20ce1535ee14d641afbbd19ec4b863c9a9/Screenshot_2024-01-24_at_12.04.32_PM.png)

- when the user process executes the system call, the system call interface generates a software trap, then the CPU switches to kernel mode and the system call routine is executed (like interrupt) where the status of the system call is then returned to the caller, together with any return values
- the caller needs to know nothing about how the system call is implemented

![Screenshot 2024-01-15 at 2.07.24 PM.png](lecture%202%20ce1535ee14d641afbbd19ec4b863c9a9/Screenshot_2024-01-15_at_2.07.24_PM.png)

### example

- example of printf() I/O library routine, which calls the write() system call in unix or linux

![Screenshot 2024-01-15 at 2.08.08 PM.png](lecture%202%20ce1535ee14d641afbbd19ec4b863c9a9/Screenshot_2024-01-15_at_2.08.08_PM.png)

## types of system calls

| process control  | load, execute, create, terminate, abort, get/set attributes, memory allocation |
| --- | --- |
| file management | create, delete, open, close, read, write, reposition, get/set attributes  |
| device management  | request, release, read, write, reposition, get/set attributes, attach/detach device |
| information maintenance  | get/set time/date, get/set system data, get/set various attributes  |
| communications | create connection, delete connection, send message, receive message, transfer status  |

## system call parameter passing

- system calls are like procedure calls, so there is a need to pass in parameters (or arguments); common procedure called **parameter passing** mechanisms are pass-by-value, pass-by-reference, and pass-by-result
- to pass parameters to OS via system call we face some limitations as system calls are of lower level than normal procedures, therefore it is hard to use complicated mechanisms
- three general methods to pass parameters to OS:
    
    
    | register | put parameters in registers and system call routine reads them from registers
    
    simple and fast, but it fails if the number of parameters is more than the number of registers |
    | --- | --- |
    | table  | put parameters in a block or a table in memory 
    
    pass the address of the block as a parameter in a register 
    
    system call routine reads the parameter using the address found at the register |
    | stack | push parameters onto the stack of the program
    
    system call routine pops parameters off the stack for use |

### table approach example

- effectively a pass-by-reference mechanism
    
    ![Screenshot 2024-01-15 at 2.40.41 PM.png](lecture%202%20ce1535ee14d641afbbd19ec4b863c9a9/Screenshot_2024-01-15_at_2.40.41_PM.png)
    

![Screenshot 2024-01-24 at 12.16.03 PM.png](lecture%202%20ce1535ee14d641afbbd19ec4b863c9a9/Screenshot_2024-01-24_at_12.16.03_PM.png)

# system programs

- provide a convenient environment for program development and execution
    - some of them are just user interfaces to system calls while others are much more complex

### types of system programs

| file management | create, delete, copy, rename, print, dump, list and files/directory manipulation |
| --- | --- |
| status information | some ask the system for information: date, time, amount of available memory, disk space, number of users

others provide detailed information for performance, logging and debugging

these programs format and print the output

some systems implement a registry to store and retrieve configuration information |
| file modification | text editors to create and modify files

special commands to search contents of files or performance transformations of the text |
| programming language support | compilers, assemblers, debuggers, and interpreters |
| program loading and execution | absolute loaders (.com), relocatable loaders (.exe), linkage editors and overlay editors (system programming stuffs) 

debugging systems for programs |
| communications | provide the mechanism for creating virtual connections among processes, users, and computer systems 

allow users to send messages to one another’s screens, browse web pages, send email messages, remote login, file transfer from one machine to another |
| utilities | additional useful programs

database system, word processor, spreadsheet, additional compilers |

# types of os

| batch processing | a job is a sequence of programs represented by an input data sets (often a deck of cards)

one job is executed one at a time and programs within each job are executed sequentially |
| --- | --- |
| multiprogramming | programs are executed in interleaved manner by the CPU

CPU is given to another program when the current program is waiting for I/O |
| time-sharing | processes are executed in interleaved manner by the CPU

same as multiprogramming, but system is interactive, so each process must get CPU without waiting for too long |
| real-time | a time-sharing system where processes have completion deadlines that must be met

hard real-time systems have firm deadlines—missing a deadline makes a process useless, e.g. examination

soft real-time systems have soft deadlines— missing a deadline reduces value of a process, e.g. assignment

important in high-valued and mission critical applications (e.g. banking transactions, airline reservation, space shuttle control) |
| distributed | operated upon a collection of computers scattered across the network

advanced from network operating systems

need to coordinate the share of resource and execution of processes to achieve a common goal |

## simple OS

- example: MS-DOS
- written to provide most functionality in least space
- not divided into modules (non-modular design)
- interfaces and levels of functionality are not well separated

## layered OS

- OS is divided into several layers, where each layer is built on top of lower layers
- bottom layer (layer 0) is the hardware while highest layer (layer N) is the user interface
- each layer is a **module**
    - it is selected such that each layer only uses services of lower-level layers

# *nix operating system

- unix and linux are based on common concepts and are referred to as the *nix family
- advantages:
    
    
    | reliability | seldom crash, ported to many systems and tested by many people |
    | --- | --- |
    | security | better security, with simpler kernel design |
    | speed | programs run faster with lower OS overhead  |
    | cost | linux is free and many unix systems are free |
    | open source | source codes available to modify  |
- *nix are the most influential operating systems and form the largest installation base behind windows— in fact, C was invented for unix
- two major flavors for unix:
    - BSD unix for academic use | solaris unix developed for unix installation
    - mac OS designed to be POSIX-compliant
- linux forms the basis of LAMP development platform for many commercial applications

## unix structure

- not structured in a complex way, but follows a bit of the layered approach

### kernel

- consist of everything below the system-call interface and
above the physical hardware
- provide file system, CPU scheduling, memory management and other OS functions
- not a good design, with many functions within one level

### system program

- programs that supplement the functions provided by kernel
- **shell** (command line interpreter), **compiler**, **editor**
- e.g. SSH, cc, gcc, pico, nano

![Screenshot 2024-01-15 at 3.03.11 PM.png](lecture%202%20ce1535ee14d641afbbd19ec4b863c9a9/Screenshot_2024-01-15_at_3.03.11_PM.png)

## linux structure

- similar 2-layered structure as unix
    
    ![Screenshot 2024-01-15 at 3.04.36 PM.png](lecture%202%20ce1535ee14d641afbbd19ec4b863c9a9/Screenshot_2024-01-15_at_3.04.36_PM.png)
    

## mac OS X structure

- based on the **darwin kernel**, a hybrid of mach kernel and freeBDS distribution
- quartx, openGL, and quicktime are used for graphics
- classic, carbon, and cocoa form the development framework

## microkernal system

- in unix and linux, there are many programs in kernel
    - not all programs need to be executed in kernel mode (i.e. need to execute privileged instructions or set interrupts)
- **microkernal approach**: move as many as possible of them from the “kernel” into “user” space to become user programs— let these programs communicate by sending/receiving

- **advantages**: easier to extend a microkernel, easier to port OS to new architecture/hardware, more reliable (less code running in critical kernel mode), more secure

- **disadvantages**: performance overhead of user space to kernel space communication

## modular OS

- most modern OS implement kernel modules
    - object-oriented approach
    - each core component is separated
    - each talks to others over known interfaces
    - each is loadable as needed within the kernel
- similar to layers but more flexible