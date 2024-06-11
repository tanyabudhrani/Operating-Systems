# lecture 10

### producer/consumer

- a very common view point of cooperating processes is the model of a producer and a consumer
    - **producer** process produces information (called **item**) that is consumed by a **consumer** process
    - information (item) produced by producer must be stored up for consumer usage later (since consumer may not be running at the same speed as producer)
- producer-consumer problem is related to a problem to coordinate producer(s) and consumer(s), especially when the buffer to hold the intermediate data is not unlimited

- producer could store data into a shared array or shared queue and consumer takes it out there

- producer may send a message containing the data to consumer for consumer to read

# synchronization

- concurrent access to shared data may make data inconsistent
- synchronization ensures the orderly execution of cooperating processes
    - synchronization requests processes to wait for the signal to go ahead, to avoid the race condition (e.g. include sleeping barber problem, society room problem)

## race condition

- suppose we have a car park and want to monitor the number of cars inside the car park
    - define an integer variable count, initially zero

```c
when a car enters the entrance, count <- count + 1
when a car leaves the exit, count <- count - 1
```

```nasm
register1 = count; register1 = register1 + 1; count = register1;
register2 = count; register2 = register2 – 1; count = register2
```

- when car1 is entering the car park (assume currently with 5 cars) and car2 is leaving the car park at around the same time
    
    ```c
    car1: execute register1 = count {register1 = 5}
    car1: execute register1 = register1 + 1 {register1 = 6}
    car2: execute register2 = count {register2 = 5}
    car2: execute register2 = register2 – 1 {register2 = 4}
    car1: execute count = register1 {count = 6} //car 1 entered so we add 1
    car2: execute count = register2 {count = 4} //car 2 left so we subtract 1 
    ```
    

## a solution to the PC problem

- a solution to the producer/consumer problem is to use a few shared buffers
    - a **shared buffer** can be viewed as a drawer to store money that a parent is passing to a child
    
    | parent puts money into an empty drawer |
    | --- |
    | child takes money out of a filled drawer |

- **livelock**: a simple solution is to have parent put money into a random drawer and child take money randomly out of the drawers

- **circular queue implementation**: another solution is to have parent put money starting from the first drawer and child take money from the first drawer
    - that should continue to bottom drawer and restart from top

```c
//producer
while (true) do {
	// produce an item and put in //
	nextProduced
	while (count == NUM_BUF) do { };
	// wait for an unused buffer
	buffer[in] = nextProduced;
	in = (in + 1) % NUM_BUF;
	count = count + 1;
}
```

```c
//consumer
while (true) do {
	while (count == 0) do { };
	// wait for an item
	nextConsumed = buffer[out];
	out = (out + 1) % NUM_BUF;
	count = count – 1;
	// consume item stored in //
	nextConsumed
}
```

- is this solution correct?
    - race condition occurs when the count is increased or decreased— can you show a sequence of CPU execution events that make the solution incorrect?

- is this solution correct?
    - what would happen if there are two parents putting money or two children taking money at around the same moment? we need a solution that will work in all conditions
- now assume that we use a high quality CPU that provides an increment instruction and a decrement instruction so that count = count + 1 and count = count – 1 can be implemented in one single instruction
    - that is a better arrangement— race condition in the car park example will not happen
- the key problem is that the count, in and out variables are used by more than one processes at any moment— this is similar to the problem of concurrent insertion of items into a linked list

# railways in the andes

- high in the andes mountains (inca empire), there are two circular railway lines
    1. one line is in peru; the other is in bolivia— they share a common section of track where the lines cross a mountain pass on international border near Lake titicaca
    2. the trouble is that the drivers of the peruvian and bolivian trains are both blind and deaf, so they can neither see nor hear each other
    3. unfortunately, the two trains collide when they simultaneously enter the common section of track (mountain pass)

### solution one

- the drivers agreed on the following method to prevent collision
    1. they set up a large bowl at the entrance to the pass
    2. before entering the pass, a driver must stop his train, walk over to the bowl, and check that it contains a rock
        - if the bowl is empty, the driver finds a rock and drops the rock in the bowl, indicating that his train is entering the pass
        - once his train has exited the pass, he walks back to the bowl and removes his rock, indicating that the pass is no longer being used
        - finally, he walks back to the train and continues
    3. if a driver arriving at the pass finds a rock in the bowl, he takes a nap and rechecks the bowl randomly until he finds it empty
        - if the bowl is found empty, he drops a rock in the bowl and drives his train into the pass

- the problem with this however, is that a train may be blocked forever
    
    ```c
    driver P checks the bowl, which is empty, then drops a rock
    driver B checks the bowl, which is not empty, then takes a nap
    driver P drives and exits, remove rock from the bowl and continues
    driver P is efficient to come back the entrance in the next round
    driver P checks the bowl, which is empty, then drops a rock
    driver B wakes up and rechecks the bowl, which is not empty, takes a nap
    driver P drives and exits, remove rock from the bowl and continues
    driver P comes back again and put a rock, driver B wakes up and takes a nap again
    ```
    

- another possibility is that it never happened
    
    ```c
    driver P is efficient to come back the entrance in the next round
    driver P checks the bowl, which is empty, then drops a rock
    driver B wakes up and rechecks the bowl, which is not empty, takes a nap
    
    this can only happen if driver B goes to sleep just before driver P exits the pass, 
    and continues to sleep while P completes a long cycle and drops a rock but wakes up just after P drops a rock!
    ```
    

- the third possibility is that the trains crashed
    
    ```c
    driver P checks the bowl, which is empty, then goes to find a rock
    driver B checks the bowl, which is empty, then goes to find a rock
    driver P drops the rock and starts driving
    driver B drops the rock and starts driving
    driver P is in the pass
    driver B is in the pass
    crash!
    ```
    

### after the crash

- the bowl was used in the wrong way

- the bolivian driver must wait at the entry to the pass until the bowl is empty, drive through the pass and walk back to put a rock in the bowl

- the peruvian driver must wait at the entry until the bowl contains a rock, drive through the pass and walk back to remove the rock from the bowl
- before this arrangement, the peruvian train ran twice a day (33M) and the bolivian train ran once a day (12M)
    
    ```c
    day 1: driver P comes, finds a rock, drives through, removes the rock
    day 1: driver B comes, finds bowl empty, drives through, drops a rock
    day 1: driver P comes, finds a rock, drives through, removes the rock
    day 2: driver P comes but finds no rock, so has to wait
    day 2: driver B comes, finds bowl empty, drives through, drops a rock
    day 2: driver P finds a rock and drives through, removes the rock for the first train on day 2
    day 2: driver P comes for the second train, but finds no rock, so has to wait until the next day for driver B to drop a rock!
    every day, only one train from each side
    ```
    

### solution two

- use two bowls, one for each driver
    1. when a driver reaches the entry, he first drops a rock in his bowl, then checks the other bowl to see if it is empty
    2. if so, he drives his train through the pass, stops and walks back to remove his rock
    3. but if he finds a rock in the other bowl, he goes back to his bowl and removes his rock
    4. then he takes a nap, drops a rock in his bowl and re-checks the other bowl, until he finds the other bowl empty
- this method worked fine until judgement day, where the two trains were simultaneously blocked at the entry when the drivers take naps
    
    ```c
    driver P: drops rock P, and is about to check bowl B
    driver B: drops rock B, and is about to check bowl P
    driver P: finds a rock in bowl B, and is about to remove rock P
    driver B: finds a rock in bowl P, and is about to remove rock B
    driver P: removes rock P, and takes a nap
    driver B: removes rock B, and takes a nap
    driver P: wakes up, drops rock P, and is about to check bowl B
    driver B: wakes up, drops rock B, and is about to check bowl P
    you can see that this could go on forever
    ```
    

### solution three

- use three bowls, one for each driver, plus a shared bowl to control who goes first
    1. when a driver reaches the entry, he first drops a rock in his bowl
        - if he is from **bolivia**, he drops a rock in the shared bowl
        - if he is from **peru**, he empties the shared bowl
    2. he checks the other bowl to see if it is empty and also the shared bowl
        - if he is from **bolivia**, he ensures that the shared bowl is empty
        - if he is from **peru**, he ensures that the shared bowl contains a rock
    3. if either checking step succeeds, he drives his train through the pass, stops and walks back to remove the rock in his bowl
    4. but if both checking steps fail, he takes a nap, and re-checks the other bowl and the shared bowl, until the checking succeeds

# critical section problem

- in the car park problem, producer/consumer problem and the andes train problem, multiple processes or users are accessing a shared resource simultaneously
    - the car park entrance/exit, the shared buffer and the common rail section are shared resources
- this shared resource can only be shared in such a way that at any moment, only one can use it and it cannot be used by another one before its current usage is completed
    - resources are used through the **request / use / release cycle**
- the shared critical section is used through a similar cycle of **request-to-enter-critical-section / inside-critical-section / exit-from-critical-section**

```c
//producer
while (true) do {
	// produce an item and put in //
	nextProduced
	while (count == NUM_BUF) do { };
	// wait for an unused buffer
	buffer[in] = nextProduced;
	in = (in + 1) % NUM_BUF;
	count = count + 1;
} 
```

```c
//consumer
while (true) do {
	while (count == 0) do { };
	// wait for an item
	nextConsumed = buffer[out];
	out = (out + 1) % NUM_BUF;
	count = count – 1;
	// consume item stored in //
	nextConsumed
}
```

> group the **buffer handling code** into a critical section
> 
- a solution to the critical section problem must avoid the problems in the andes trains— the solution is made up of two separate sections of code around the critical section: **entry section and exit section**
    
    ```c
    Process P
    	while (true) do {
    	// entry section: part of the solution
    	Critical section of the process
    	// exit section: part of the solution
    	Remainder section of the process
    }
    ```
    
    - we can only make two very simple assumptions:
        
        
        | each process executes at a non-zero speed |
        | --- |
        | there is no limitation on the relative speed of the processes |
- a solution should satisfy three properties
    
    
    | mutual exclusion  | if process is executing in its critical section, then no other processes can be executing in their critical sections— that is, at most one process could be in CS |
    | --- | --- |
    | progress | if no process is executing in its critical section and there exist some processes that wish to enter their critical sections, then some of the waiting processes will eventually enter the critical section—that is, at least one process could enter CS |
    | bounded waiting | a bound must exist on the number of times that other processes are allowed to enter their critical sections after a process has made a request to enter its critical section and before that request is granted— that is, there is no starvation |

# peterson’s algorithm

```c
//process P0
while (true) do {
	flag[0] = true;
	turn = 1;
	while (flag[1] and turn == 1) do { };
	< critical section >
	flag[0] = false;
	< remainder section >
}
```

```c
//process P1
while (true) do {
	flag[1] = true;
	turn = 0;
	while (flag[0] and turn == 0) do { };
	< critical section >
	flag[1] = false;
	< remainder section >
}
```

- example
    
## busy waiting solution

- problems with common solutions to the critical section problem, including peterson’s algorithm:
    - there are while-loops for processes to wait until they can enter the critical section
- **busy waiting**: a process is busy in waiting and this uses up and wastes the precious CPU cycle
    - if you are the CPU scheduler, you will not know whether a process is waiting to enter the critical section (CPU is wasted) or just running healthily in the remainder section (CPU is used)
- we should provide better solutions so that any process waiting to enter critical section should release the CPU to other processes because the waiting process is waiting for something to happen, but that something will not happen if it continues to hold the CPU and not allow other processes to use the CPU to make the event that it is waiting for happen

# semaphores

- we want a synchronization tool that does not require busy waiting— instead, the OS could suspend the process waiting to enter the critical section
- a **semaphore** S is an integer variable that provides two standard operations: **P(S)** and **V(S)**
    - originally, semaphore means flag signal (for andes trains?)

- **P(S)** means that the process will wait on S until S becomes positive
    
    ```c
    while (S <= 0) do { }; // wait until S becomes positive
    	S = S – 1 ;
    ```
    

- **V(S)** means that S will be increased by one and the process waiting on S could proceed
    
    ```c
    S = S + 1;
    
    ```
    

### counting semaphore

- integer value can be any positive number

### binary semaphore

- integer value can only be 0 or 1
- they are also called **mutex locks** (they look like locks and they help to ensure mutual exclusion or mutex)— providing critical section with semaphore S initialized to 1
    
    ```c
    while (true) do {
    	P(S); // entry section
    	< critical section >
    	V(S); // exit section
    	< remainder section >
    }
    ```
    

## semaphore implementation

- the implementation of semaphores must guarantee that no two processes can execute P() and V() on the same semaphore at the same time
    - to ensure no two processes executing P() and V() on the same semaphore at the same time, we could implement P() and V() inside a critical section— this implementation itself becomes the critical section problem if P() and V() implementation codes are placed inside critical sections
- in a simple way, **we could allow busy waiting in the critical section implementation for P() and V()**
    - implementation code is short and very little busy waiting
- to avoid busy waiting, OS associates an integer value and a list of processes waiting with each semaphore
    - **the integer value is the semaphore value**— a negative value indicates the number of processes waiting on the list
- two operations on the waiting list:
    
    
    | block() | place the process on the waiting list |
    | --- | --- |
    | wakeup() | remove an appropriate process from the waiting list and move it to the ready queue |

```c
//implementation of P(S)
Disable interrupt;
S.value = S.value – 1;
if (S.value < 0)
	block(S.queue);
Enable interrupt;
```

```c
//implementation of V(S)
Disable interrupt;
S.value = S.value + 1;
if (S.value <= 0)
	wakeup(S.queue);
Enable interrupt;
```

## use of semaphores

- semaphores are used to solve different types of synchronization problems
- consider the sequencing problem for three children
    - assume that parent creates three children, P1, P2, and P3— P1 is designed to read data, P2 to process data, and P3 to write results and they must be executed in that order but parent has no way to enforce that ordering
- a typical way of synchronization is to **use two semaphores**

- `semaphore canProcess = 0;`

- `semaphore canWrite = 0;`

| p1 | p2 | p3 |
| --- | --- | --- |
| readData();
V(canProcess); | P(canProcess);
processData();
V(canWrite); | P(canWrite);
writeData(); |

### using semaphores to solve PC problem

- define a semaphore to implement critical section— semaphore mutex = 1

```c
//producer

while (true) do {
	// produce an item
	P(mutex);
	// add the item to buffer
	V(mutex);
}
```

```c
//consumer

while (true) do {
	P(mutex);
	// remove item from buffer
	V(mutex);
	// consume removed item
}
```
