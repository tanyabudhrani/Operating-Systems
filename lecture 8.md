# lecture 8

Created: March 12, 2024 2:32 AM

# memory management

- a program should be in main memory to be executed
    - loaded into memory in **contiguous storage**
    - broken down into pieces and stored in non-contiguous memory
- **memory management unit (MMU)** translates the logical address into physical address— OS often implements paging and segmentation algorithms for non-contiguous memory allocation

- do we really need the whole program in memory in order to execute it? could we execute a program of size 100K (with big data or big program) on a 64K apple II computer?

- **overlay**: break a program into stages and load the next stage when the current stage finishes

- **virtual memory**: store the process on disk in a way similar to memory and load the needed part only when that part is executed

## swapping

- when there are too many processes in memory, everybody will not have sufficient memory to run, so some of them should be moved out of the memory to free up the space for others— this is called **swapping**
    - a process can be swapped out of memory to backing store, and then brought back into memory for continued execution
    - **backing store** is normally a fast disk large enough to hold copies of memory images for users and provide direct access to these memory images
- processes can be swapped out in full (**whole process**) or in part (**segment or page**)
    - since I/O is relatively slow, it will take quite some time to swap a full process out and then swap it in later
- ability to swap part of a process forms the basis for overlay and virtual memory management

![Screenshot 2024-03-12 at 5.37.11 PM.png](lecture%208%20e6a0886e5a444f66b775b140443b4178/Screenshot_2024-03-12_at_5.37.11_PM.png)

## overlay

- swapping is done to different phases of program execution

![Screenshot 2024-03-12 at 5.38.05 PM.png](lecture%208%20e6a0886e5a444f66b775b140443b4178/Screenshot_2024-03-12_at_5.38.05_PM.png)

## memory need

- how much memory is needed?

![Screenshot 2024-03-12 at 5.38.33 PM.png](lecture%208%20e6a0886e5a444f66b775b140443b4178/Screenshot_2024-03-12_at_5.38.33_PM.png)

- actual memory needed:
    
    ![Screenshot 2024-03-12 at 5.38.59 PM.png](lecture%208%20e6a0886e5a444f66b775b140443b4178/Screenshot_2024-03-12_at_5.38.59_PM.png)
    
    - size of process = 8264K (size for VSZ is in KB)
    • RSS refers to size of partial process in memory
    • 348K is in main memory when this program is executed— this is much smaller than the process size of 8264K
    - change size of A to 100 and run again
    • VSZ = 4168 and RSS = 344.
    • only the needed part (344K) is in main memory
- it is not uncommon for large data need
    
    ![Screenshot 2024-03-12 at 5.40.26 PM.png](lecture%208%20e6a0886e5a444f66b775b140443b4178/Screenshot_2024-03-12_at_5.40.26_PM.png)
    

# virtual memory

- virtual memory separates user logical memory (not just logical address) from physical main memory
    
    
    | only the currently executing part of the process needs to be in memory for execution |
    | --- |
    | view the disk to be extension of physical memory (RAM) |
    | logical address space can therefore be much larger than physical address space |
    | consider the logical address space to be stored on the disk, as an extension of the real physical memory— this technique is called virtual address eXtension  |
    | allow address space to be shared by several processes |
    | less I/O since unused part of process/data is never loaded |
- possible implementations:
    - **paging**: break into pages
    - **segmentation**: break into segments

![Screenshot 2024-03-12 at 5.42.33 PM.png](lecture%208%20e6a0886e5a444f66b775b140443b4178/Screenshot_2024-03-12_at_5.42.33_PM.png)

- virtual memory is mapped to physical memory

- some parts are stored in real memory

- some parts are stored on disk only
- move in **needed part** to memory (swap in) and move out **unneeded part** from memory (swap out)
- **memory map** in virtual memory means the page table in paging

![Screenshot 2024-03-12 at 5.43.36 PM.png](lecture%208%20e6a0886e5a444f66b775b140443b4178/Screenshot_2024-03-12_at_5.43.36_PM.png)

- physical memory is smaller than virtual memory
    - we could not store all needed pages in memory
    - a page should be in memory when its contents are needed (containing the **current instruction or data to be accessed**)

### demand paging

- bring a page into memory only when it is needed
    - avoid loading pages that are not used (effective use of I/O resource)
    - to load a needed page, process must wait for I/O to complete

### anticipatory paging

- predict what pages a process would need in near future and to bring them in before hand
    - no need to wait for a page to be loaded, faster response time
    - prediction of future could be wrong and I/O would be wasted

> most systems implement demand paging, since it is simple and effective
> 

# demand paging

- a page is needed when there is a reference to it— a **reference** is a need indicated by **PC or MAR**

- if the **reference is invalid**, process is accessing outside its space, then generate a trap

- if the **page is not in physical memory**, load it from disk into memory
- demand paging is implemented by a lazy process swapper, meaning that it never swaps a part of a process into memory unless the part is needed
    - a swapper that swaps pages is called a **pager**
- in paging, a virtual or logical address is divided into page number and page offset
    - **page number** is used as an index into the page table— a frame number is found
- for virtual memory, not all pages need to be stored in memory

### advantages

| less I/O needed |
| --- |
| less physical memory needed |
| relatively good response |
| can support more user |

## valid-invalid bit

- used to indicate whether a page is in memory

- a **valid entry** means that the page is in memory and the frame number could be used

- an **invalid entry** means that the page is not in memory
- hardware will detect the invalid bit and trigger a trap, called the **page fault**
    - the **page fault handler** (a kind of interrupt handler) will ask the pager to help swapping the page into memory
- initially, valid-invalid bit is set to invalid for all entries

### example

![Screenshot 2024-03-12 at 5.51.05 PM.png](lecture%208%20e6a0886e5a444f66b775b140443b4178/Screenshot_2024-03-12_at_5.51.05_PM.png)

- a process is divided into 8 pages
    - all 8 pages can be found on disk but only 3 pages (A,C,F) are currently in main memory
- valid-invalid bits indicate which are in memory
- only pages that are in memory could be accessed by the CPU— memory pages are called **frames**

# page fault

- if there is an access to the address within a page, this is called a **reference to the page**
- if the reference is to an invalid page, it will generate a trap to OS— this trap is similar to an access beyond segment limit of a process
    - this is the **page fault** (a fault or an error on a page access)
- the page fault handler in OS checks with the address limit stored in PCB of the process for the nature of the trap
    - if the **reference is beyond the process logical address**, print out segmentation fault error and terminate the program
    - if the **page is just not in memory**, continue with page fault handling to bring it into the memory
- servicing the page fault:
    
    
    | get an empty frame from the free frame list |
    | --- |
    | schedule I/O to load the page on disk into the free frame |
    | update the page table to point to the loaded frame |
    | set the valid-invalid bit to be valid |
    | restart the instruction that caused the page fault |

![Screenshot 2024-03-12 at 5.53.29 PM.png](lecture%208%20e6a0886e5a444f66b775b140443b4178/Screenshot_2024-03-12_at_5.53.29_PM.png)

## demand paging performance

- let the page fault rate be f

- if f = 0, there is no page fault

- if f = 1, every reference generates a page fault
- note that 0 ≤ f ≤ 1

## effective access time

- the expected time to access to memory, in the presence of virtual memory
    - **EAT = (1 – f) x memory access time + f x page fault service time**
    - page fault service time = page fault overhead + time to swap the page in + restart overhead

### example

- memory access time = 0.2µs
- linux context switching time on 2GHz Xeon = 4µs
- average disk access time = 8 ms (5 seek+3 rotation)
- average page fault service time ～ 8000s
- EAT = (1 – f) x 0.2 + f x 8000 = 0.2 + 7999.8f
    - if there are 0.1% of page faults (f = 0.001), EAT = 8.2µs
- compared with the normal memory access time of 0.2 µs (200 ns), there is a factor of 40 times slowing down!

> is 0.1% page fault rate reasonable? does it matter with a slow down of 40 times? 

using SSD, disk time is reduced to about 16 µs— this would impact the EAT by 0.22µ
> 

# page replacement

- recall that to handle a page fault, a free frame is needed to hold the page to be swapped from disk into memory
- sooner or later, the system runs out of free frames
    - unused frame in memory should be freed
- if all frames are still in use, try to find some frame that is probably not used and remove it— this action of finding a frame in memory to be removed to free up the space in order to admit a newly needed page is called **page replacement**

### example

- executing instruction in page 1 (i.e. K) of user 1
    - instruction needs to access page 3 (i.e. M), which is not in memory
    - all 6 pages, 3 for each user, are used up already
    - we need to remove a page to free a frame to hold page M

![Screenshot 2024-03-12 at 10.31.55 PM.png](lecture%208%20e6a0886e5a444f66b775b140443b4178/Screenshot_2024-03-12_at_10.31.55_PM.png)

## page replacement mechanism

- modify page fault handler to include page replacement mechanism
    - it is more effective to remove a page that has not been changed or modified, since that page does not need to be written back on the disk
        - **program text** represents read-only pages that require no saving
        - sometimes some data pages may not be modified at the moment of page replacement
    - use a **modify or dirty bit** to indicate whether a page has been modified
- use of page replacement mechanism completes the puzzle that allows for separation between logical memory and physical memory
    - now, there can be a very large virtual memory for a program, but that can be supported upon a much smaller physical memory

![Screenshot 2024-03-12 at 10.33.10 PM.png](lecture%208%20e6a0886e5a444f66b775b140443b4178/Screenshot_2024-03-12_at_10.33.10_PM.png)

- steps in page fault handling with page replacement
    
    
    | find the location of the desired page on disk |
    | --- |
    | find a free frame:
    - if there is a free frame, use it
    - if there is no free frame, use a page replacement algorithm to select a victim frame
    
    write victim frame to disk if modified |
    | bring the desired page into the (new) free frame |
    | update the page table and frame information for both the victim and the new page |
    | return from page fault handler and resume the user process generating the page fault |

## page replacement algorithm

- replacing a page is an expensive I/O operation
    - our example of 0.1% page fault causes 40 times slow down— goal is to generate smallest number of page-faults; if we know the future perfectly, we can determine the best page to be replaced for each page fault
- there are a number of common page replacement algorithms
    
    
    | first in first out | if a page comes in earlier than another page, the earlier page will be replaced before the later page |
    | --- | --- |
    | optimal  | look into the future, and replace the page that will not be needed in the furthest future |
    | least recently used | look into the past, and replace the page that has not been used for the longest time |
- the performance of a page replacement algorithm is often evaluated by running it on a list of memory references and computing the number of page faults
- this list of memory references is abstracted into a reference string, showing only the page number
    - if we trace a process in execution, we might see a sequence of addresses generated: 0100, 0432, 0102, 0612, 0104, 0106, 0102, 0611, 0104, 0106, 0108, 0102, 0610, 0104, 0106, 0108, 0102, 0609, 0104, 0110
    - it looks like a loop referencing some data
- assuming that a page contains 100 bytes for simplicity, the reference string of page numbers generated is
1,4,1,6,1,1,1,6,1,1,1,1,6,1,1,1,1,6,1,1
- this is equivalent to 1,4,1,6,1,6,1,6,1,6,1, since consecutive accesses to the same page will not cause further page fault

![Screenshot 2024-03-12 at 10.37.45 PM.png](lecture%208%20e6a0886e5a444f66b775b140443b4178/Screenshot_2024-03-12_at_10.37.45_PM.png)

- if we have more free frames, then the number of page faults will be smaller — not necessarily true!

# FIFO page replacement

- when there is no free frame, the page that has resided in the memory for the longest (the earliest page admitted) will be replaced
    - there is a simple implementation using a circular queue with a pointer pointing to the oldest page
    - in this example, there are 15 page faults

![Screenshot 2024-03-12 at 10.39.08 PM.png](lecture%208%20e6a0886e5a444f66b775b140443b4178/Screenshot_2024-03-12_at_10.39.08_PM.png)

# optimal page replacement

- replace the page that will not be used for the longest period of time in future— it is clear that this will generate the smallest number of page faults
    - used as a benchmark to evaluate other algorithms
    - in this example, there are only 9 page faults

![Screenshot 2024-03-12 at 10.41.00 PM.png](lecture%208%20e6a0886e5a444f66b775b140443b4178/Screenshot_2024-03-12_at_10.41.00_PM.png)

# LRU page replacement

- replace the page that has not been used for the longest period of time
- LRU is very similar to using a mirror to look into the past to reflect what the future would look like— the behavior at time now+t can be reflected as now-t
- in this example, there are 12 page faults

![Screenshot 2024-03-12 at 10.43.08 PM.png](lecture%208%20e6a0886e5a444f66b775b140443b4178/Screenshot_2024-03-12_at_10.43.08_PM.png)

- LRU is performing well in practical cases
    - experience shows that the trick of using the past for the future turns out to be good
- LRU is the most popular algorithm used in real systems

- how could we implement LRU?
    - counter implementation
    - stack implementation

- approximation implementations
    - reference bit algorithm
    - additional reference bit algorithm
    - second chance (clock) algorithm
    
- counter implementation
    - every page entry has a counter
    - whenever a page is referenced through this entry, copy the clock value into the counter
    - when a page needs to be replaced, look at the counters to determine the page with the smallest value to be replaced
- example: red page would be replaced

![Screenshot 2024-03-12 at 10.45.03 PM.png](lecture%208%20e6a0886e5a444f66b775b140443b4178/Screenshot_2024-03-12_at_10.45.03_PM.png)

## stack implementation

- use stack of pages in double linked list with head/tail pointers— when a page is referenced, move it to the top of stack
- replace the page at the bottom of the stack
    - example: replace red page at bottom

![Screenshot 2024-03-12 at 10.45.52 PM.png](lecture%208%20e6a0886e5a444f66b775b140443b4178/Screenshot_2024-03-12_at_10.45.52_PM.png)

- **counter implementation** has a storage overhead of a clock value with each page entry
    - clock value would increase in size
    - a search is needed to find the entry with the smallest clock value to replace

- **stack implementation** needs up to 6 updates to the pointers to move a stack entry to the top on each reference
    - cost of pointers to double linked list could be expensive

### approximation algorithms

- use hardware to help to make page selection faster
- each page is associated with a reference bit, initially zero
- when a page is referenced, the reference bit is set to one by hardware
- basic algorithm: replace a page with a reference bit value of zero

## additional reference bit algorithm

- keep several bits or a byte with each page, initially 0
- at regular time interval, e.g. 100 ms, shift the reference bit into the high order bit position of the few bits or the byte
- now, these bits or the byte will contain an approximate time for the use of the page entry
- if a page is never referenced in the past 8 periods, it has a value of 0
- a page that is used once in each period has a **value of 255** (11111111)
- a page used in only past 4 periods (11110000) will have a **value larger than a page** used in only the last fifth to eighth
periods (00001111)
    - a page with smallest value is probably the least recently used— replace a page with the smallest value
    - use FIFO or random algorithm to replace a page (or all those pages) if there are several pages with the same smallest value

## second chance algorithm

- consider the list of pages like a simple circular queue (instead of a double linked stack)
- make use of the hardware reference bit
- if a page is to be replaced, look at the entry at the current pointer of the queue
    - if its **reference bit is zero**, replace the page and advance the queue pointer
    - if the **reference bit is one**, then reset the reference bit to zero and advance the queue pointer to next entry, until a page with a reference bit of zero is found for replacement
- pages are replaced according to the circular queue order and the advancement of the queue pointer looks like the hand of a clock (thus called the **clock algorithm**)
- a selected victim page with **reference bit of one** will be given a second chance
- both Unix and Linux use a variant of clock algorithm, but a few pages will be paged out together

![Screenshot 2024-03-12 at 10.50.42 PM.png](lecture%208%20e6a0886e5a444f66b775b140443b4178/Screenshot_2024-03-12_at_10.50.42_PM.png)

- example
    - try the following reference string on FIFO, optimal, and LRU with 3 frames: 0 1 3 2 4 0 2 4 1 3 1 0
        1. FIFO
            
            ![Screenshot 2024-03-12 at 10.53.54 PM.png](lecture%208%20e6a0886e5a444f66b775b140443b4178/Screenshot_2024-03-12_at_10.53.54_PM.png)
            
        2. optimal
            
            ![Screenshot 2024-03-12 at 10.54.02 PM.png](lecture%208%20e6a0886e5a444f66b775b140443b4178/Screenshot_2024-03-12_at_10.54.02_PM.png)
            
        3. LRU
            
            ![Screenshot 2024-03-12 at 10.54.10 PM.png](lecture%208%20e6a0886e5a444f66b775b140443b4178/Screenshot_2024-03-12_at_10.54.10_PM.png)
            

# allocation of frames

- in page replacement algorithms, we would assume that we know the number of frames for each process and look for a page to be replaced— if a system has F free frames, how are they allocated to a set of n processes?

- **fixed allocation**
    - **equal allocation**: each process gets an equal number of frames F/n
    - **proportional allocation**: each process gets a number of frames in proportion to its size, more frames for larger processes

- **variable allocation**
    - allow the number of frames allocated to each process to vary over time
    - processes that suffer from too many page faults should have more frames and vice versa
- page replacement is also related to frame allocation

- **local page replacement**:
    - a process will only seek for page replacement within its own set of frame
    

- **global page replacement**:
    - a process will seek for page replacement within all frames of all processes
    - a process may take away a frame from another process
- only local page replacement can be used for **fixed frame allocation**
- both global and local page replacement can be used for **variable frame allocation**

# thrashing

- if a process does not have enough frames allocated for it, it will generate many page faults, then the process spends a lot of time waiting for page I/O
- CPU utilization will be low

- OS thinks that it needs to increase degree of multiprogramming so as to increase CPU utilization and admits another process

- competition for frames will become more serious with more processes, especially if **global page replacement is employed**

- processes are waiting for I/O further, with even lower CPU utilization, and OS brings in yet more processes
- this is called thrashing
    - processes are busy **but they are just busy performing I/O**: swapping pages in and out, without doing any useful work
- thrashing occurs when the **number of frames available is smaller than the total size of active pages** of the processes in the ready queue— the solution is to reduce the degree of multiprogramming, not increasing it

![Screenshot 2024-03-12 at 11.01.23 PM.png](lecture%208%20e6a0886e5a444f66b775b140443b4178/Screenshot_2024-03-12_at_11.01.23_PM.png)

# working-set

- to reduce thrashing, we should ensure that there are sufficient frames for the active pages for the processes
- the set of active pages for a process is called its **working-set**
    - let ∆ be the working-set window, e.g. 10000 instructions or 1 second
- the working-set size of a process is the total number of pages referenced in the recent period of ∆ (= 10 in example below)— when the number of frames is smaller than the total working-set size, thrashing will occur—OS must select a victim process and suspend it, removing its frames for use by other processes

![Screenshot 2024-03-12 at 11.03.33 PM.png](lecture%208%20e6a0886e5a444f66b775b140443b4178/Screenshot_2024-03-12_at_11.03.33_PM.png)

# page fault frequency

- another solution to thrashing is to monitor the page faults, called Page Fault Frequency (PFF) algorithm
    - count the page faults for a process over a period of time
    - define an acceptable range of page fault rate
        - if the **rate is too low**, there are more frames than sufficient, and OS could take away some frames to other processes
        - if the **rate is too high**, the process may suffer from thrashing, and OS will give more frames to it
        - if many processes have a high rate, OS will select a victim process for suspension and free its frames to other processes

![Screenshot 2024-03-12 at 11.04.23 PM.png](lecture%208%20e6a0886e5a444f66b775b140443b4178/Screenshot_2024-03-12_at_11.04.23_PM.png)