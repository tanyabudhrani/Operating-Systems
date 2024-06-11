# lecture 11

# file concept

- a **file** is a named collection of related information that is stored on a secondary storage, such as a disk
    - a file is the smallest unit of allocation to the secondary storage that can be **seen by a user**
    - a user can only access data stored on the secondary storage via the file

- information in a file is defined by its creator

- information about files are often maintained in the directory structure
- a **file system** is a collection of files in an organized way, with proper storage and directory structure

- a disk may be divided into many parts
    - **partition**: a part may hold an individual file system— sometimes, several disks are combined

## file attributes

| name | textual file name, the only information that a human can read directly |
| --- | --- |
| identifier  | unique tag or number that identifies a file within the file system |
| type | needed for systems that support different types of files, e.g. text versus binary file |
| location  | pointer to the file location on storage device |
| size  | current file size and/or maximum size (in bytes or blocks) |
| user or owner identification | who creates or owns the file |
| protection  | decide on who can do reading, writing, executing |
| time and date information | when a file was first created, last modified or last accessed |
- values of the attributes for a file are stored in the directory structure or directory entry

## file operations

- a file is an **abstract data type (ADT)**, that provides a basic set of operations
    
    
    | create | allocate storage for a file and create an entry to store the file attributes |
    | --- | --- |
    | read | return data from the file, normally start reading data from the current read pointer |
    | write | store data into the file, often start from the current write pointer |
    | reposition within file | move the read and write pointers |
    | delete  | remove the file entry and release the allocated storage |
    | truncate | delete all contents of the file to make it an empty file |
- besides the basic operations of reading data from and writing data to a file, two more common operations are often provided in the implementation of most file systems

- **open**: make a file ready for reading/writing
    - actions involve searching for the file entry containing file attributes in the directory structure and moving the content of the entry into memory

- **close**: mark the completion of operations on file
    - actions involve moving the content of the file entry in memory back to directory structure on disk
- to manage an opened file, some pieces of data have to be maintained by OS
    
    
    | file pointer | a pointer to the last read/write location | there is one pointer for each process that has opened the file |
    | --- | --- | --- |
    | file-open count | a counter for the number of times that the file is opened | a file entry in memory (open-file table) can be deleted when the last process using the file closes it— this is also called the reference count (as used in java garbage collection) |
    | disk location  | where the file resides on the disk | a copy of this information is often stored in memory |
    | access rights | access mode that the file is opened by a process | there is one set of rights for each process on each file |
    

## file types

- **program files**
    - source code
    - object code
    - executable program

- **data files**
    - character or ASCII file
    - binary file
    - free-formatted text file
    - formatted or structured file
- there are different subtypes of files
    - they are often indicated by the **file extension**

## file structure

- a file may be structured or unstructured
    
    
    | unstructured | the file is just a sequence of words or bytes |
    | --- | --- |
    | simple record structure | each record is stored in a line or a fixed number of lines
    
    fixed length records and variable length records |
    | complex structure | formatted document
    
    relocatable load file |
- **unix** only supports a simple unstructured file of consecutive bytes
    - application programs must interpret the file content by themselves
    - magic number of a file helps with this step

## access methods

| access Method | description |
| --- | --- |
| sequential access | data are accessed in order, from beginning to the end-- most systems support sequential access |
| direct access | also called relative access-- file is composed of fixed-length records; data can be accessed directly, based on record number |
| indexed access | a separate index file contains pointers to the data blocks of a data file (direct file or relative file)-- direct access to required data blocks can be achieved via searching the index |

### sequential access operations

- **read next**: return next data item and advance file pointer
- **reset**: put the file pointer back to the beginning
    - it is like the rewind operation

- **write next**: update next data item and advance file pointer
- **skip forward/backward**: move the file pointer forward without reading or move it backward
    - backward skipping is only supported in some systems

### direct access operations

- **read n**: return the n th data item or block

- **write n**: update the n th data item or block
- a system that provides sequential access operations read next and write next can support the two direct access operations above through a new operation position n
    - **position n**: move the file pointer to the n th data item or block
    - read n and write n could be implemented using position n and then read next and write next
- sequential access can also be provided easily by a system that only supports direct access

### indexed access

- use of **index** allows direct access to data
- data is stored in a direct or relative file
- multiple indexes could be maintained to allow direct access to different parts of data based on the index

# directory structure

- a **directory** is a collection of nodes or entries containing information about all files
- both directory structure and files reside on the disk
- in unix/linux, the directory structure itself is implemented as a file

- operations on a directory:
    
    
    | search for a file | find whether a particular file exists and get its entry |
    | --- | --- |
    | create a file | add a new entry for a new file |
    | delete a file | remove an entry for a file |
    | list a directory | get the files in a directory and their file entries |
    | rename a file | change the name of the file inside the entry |
    | traverse the file system | access every directory and then all files within stored under the file system |

## single-level directory

- all files are stored in a single directory
    - adopted in old macintosh file system
- **advantage**: simple

- **naming problem**: two users may try to use the same name for different files

- **grouping problem**: how to distinguish files belonging to a user from another versus isolating only files of a particular type (e.g. batch files)

## two-level directory

- separate directory for each user
    - each user now owns a separate one-level directory
    - a **master file directory (MFD)** contains **individual user file directories (UFD)**
    - each user may create a file with same name as other users which allows for faster searching for a file and easier access control
- more difficult for a user to access files of another user when they cooperate: access rights need to be controlled and users need to name another user’s file

## tree-structured directories

- extend two-level directory into multiple levels like a tree

- more efficient searching on even fewer files
- grouping capability of files belonging to a particular group, e.g. files for a certain subject or with a certain nature
- a **current working directory** is maintained to define the default searching place for files
    - path names should be given to locate for a file
    - can use absolute or relative path names (from the current working directory)
- creating a new subdirectory is done in the current directory
- deleting a subdirectory could lead to the deletion of all files under it
- adopted in MS-DOS

## acyclic-graph directories

- allow for shared subdirectories and files in tree-structured directories so as to form a graph

- allow for files or directories to be shared
    - file may be referenced via two different names
        - **aliasing**: it is the same physical file (a single copy) accessible under two different names
    - changing the content of the shared file via one name will cause changes to the physical file and these changes will be reflected via another name

- aliasing of a file name could create problems
    - **dangling pointer problem:** if we count the total number of files, the same file under two names would be double-counted
        - in C, you may copy a pointer to another and both pointers refer to the same memory block allocated via `malloc()` but deletion of the block via one pointer via `free()` will cause problem when it is accessed via the other pointer, especially the freed memory is reallocated

- **solution**:
    - do not delete a file until there is no more access path (or link) to it
    - we need to keep all the access paths or links to each file
    - **reference count**: instead of keeping all the links to a file, we can keep a count of the number of links— when the number of links goes to zero, the file can be deleted
    

## general graph directories

- allow for cycles in directories to exist to form a **general graph**

- the **general graph with a cycle** could cause some problems (e.g. how to count the total number of files?)
- a poorly designed algorithms will fall into an infinite loop— if we need a recursive backup of all the files down the directories, how can that be done?
- how can we delete a file using reference count?
    - **without cycle**, a deleted file has a reference count of zero
    - **with cycle**, deleted files may have non-zero reference count
    - garbage collection techniques are needed, but garbage collection algorithms are expensive to run
- we could prevent the problems from happening
    - ensure that there is no cycle in the graph
    - allow only links to file, not to subdirectories
    - every time a new link is added, use a cycle detection algorithm to determine whether there is a cycle created

# file sharing

- file sharing on multi-user systems is desirable
- sharing may be done through a **protection scheme**
    - user IDs identify users, allowing permissions and protections to be set on a per-user basis
    - group IDs allow users to be in groups, permitting group access rights
- with distributed systems where multiple machines are connected together via a network, files may be shared across the network
- remote file systems use networking to support accesses to file systems under different systems
    - manually via programs like FTP or winscp
    - automatically using distributed file systems, e.g. transparent use of J: drive on both PC and unix/linux
    - semi-automatically via the web

| model | description | characteristics |
| --- | --- | --- |
| client-server model | allows clients to mount remote file systems from servers | a server can serve multiple clients-- standard operating system file calls are translated into remote calls to be executed at the remote machine |
| network file system (NFS) | developed by sun (now Oracle)-- it is a common distributed file-sharing method | it is the standard UNIX client-server file sharing approach |
| storage-area network (SAN) | provides large storage capacities to a large user population | it uses a high-speed network dedicated to the task of transporting data for storage and retrieval |
| cloud storage | the newest technology to host data in a collection of nodes over the cloud | service is usually provided by a third party, typically a data center-- common storage: icloud, dropbox, google drive |

## file storage allocation

### linked allocation

- simple
- directory points to first data block
- a pointer at the end of first data block points to a second data block and so on
- only efficient for sequential access, but bad for random access

### indexed allocation

- combine all indexes for accessing each file into a common block
- can store them in a FAT table
- more efficient for random access

## secondary storage

- recall that a file is a named collection of related information that is stored on a secondary storage, such as a disk
    - secondary storage refers to storage outside memory
    - magnetic drum
    - magnetic tape
    - hard disk
    - solid state drive (SSD)
    - networked disk / storage
- secondary storage can also be configured to store data in specialized format, e.g. information in a database

## disk storage

- a computer system can access its local storage (local disk / local drive), or storage over the network (remote disk / network drive)
    - local storage is easy to use, but it cannot be shared easily— you may install dropbox or foxy to allow external access, but you may lose control over who can access what
    - remote storage is flexible, but it relies on the availability of network

### host-attached storage

- this is the most standard arrangement— storage is directly attached to computer and is accessed via I/O port
    - IDE or ATA hard disk (at most 2 per I/O bus)
    - SCSI hard disk (at most 16 per I/O bus)
- to improve fault-tolerance, i.e. to make hard disk surviving disk failure, one could use mirror hard disk
    - two hard disks writing the same data simultaneously
        - **primary hard disk** provides read/write and **secondary (mirror) hard disk** serves as data backup
    - if primary fails, secondary can provide data for reading which is simple, but expensive
- mirror hard disk arrangement can be generalized into RAID (Redundant Array of Inexpensive Disk)
    - there are 7 levels of RAID: RAID 0 to RAID 6
    - improved parallelism for data access, and fault-tolerance over single hard disk for RAID 1 to RAID 6

| raid 0 means nothing | no overhead |
| --- | --- |
| raid 1 means mirror  | 100% overhead |
| raid 5 is the most commonly adopted | 1/n overhead for n data disk |

### network-attached storage

- **network-attached storage** (NAS) is a special purpose storage system accessible over the network
    - the same network is used for normal networking operation and accessing remote data
- **unix NFS** (network file system) is often NAS-based
    - remote procedure call can be used to access file content
    - NFS makes remote file access transparent to a user
- **departmental storage** had also been NFS-based
    - in unix/linux, compare access to local files under /tmp and remote files under /home/12345678d
    - you may not see any difference, but when the network is slow, you will notice a difference

### storage-area network

- **storage area network (SAN)** is a current technology to provide large storage to a large user population
    - instead of sharing the same network with other services, it has a high-speed private data network dedicated to the task of transporting data for storage and retrieval
    - operate on special storage protocols, not network protocols
    - connect together computers and storage devices to allow sharing of the pool of storage devices
    - adopted by large institutions, e.g. J: drive attached to SAN

## tertiary storage

- **primary storage refers to memory**: directly accessible by CPU

- **secondary storage refers to hard disk or SSD**: needs to access via I/O bus and DMA to bring into memory for
usage

- **tertiary storage refers to CD-ROM and DVD**: accessible via I/O bus only if they are placed inside the disk drive
- this forms the **hierarchical storage structure**, increasing in capacity, increasing in access time, but decreasing in cost
    
    
    | to complete this hierarchy, cache memory sits between primary memory and CPU |
    | --- |
    | virtual memory frame sits between secondary disk and memory |
    | temporarily cached file sits between tertiary storage and local disk |
- tertiary storage is used to store gigantic amount of data, e.g. video recorded via surveillance cameras, traffic monitoring images, medical images, SETI signals, data gathered by CERN LHC (large hadron collider)
- tertiary storage is commonly used for backup and more for archival, targeted at storing for 100 years
    - large storage and slow access in storage hierarchy
- robotic optical jukebox allows fast insertion of a selected DVD from a collection of cartridges of DVDs to the disk drive to support tertiary storage
    - a cartridge can hold 30 to 50 discs, with 20 to 40 cartridges
    - a quad layer blu-ray disc can store 128GB, with future technology storing 200GB
    - a storage unit could hold more than 100 discs
    - this is commonly used in many companies to store large amount of information, e.g. banks
    - access time is about 5 to 10 seconds from jukebox to load
- offline-storage physically isolates the storage to sustain disasters like fire or earthquake

# protection

- mechanisms and policy to keep programs and users from accessing or changing stuff they should not do
    - issues internal to operating system
- OS consists of a collection of objects (hardware or software resources), each has a unique name and can be accessed through a well-defined set of operations
- **goal**: to ensure that each object is accessed correctly and only by those processes that are allowed to do so
- **guiding principles**: principle of least privilege
    - programs, users and systems should be given just enough privileges to perform their tasks
    - limit damage if entity has a bug or gets abused
    - can be **static** (during life of system or process) or **dynamic** (changed by process as needed, e.g. domain switching, privilege escalation)
- separate policy from mechanism

- **mechanism**: the stuff built into the OS to make protection work

- **policy**: the information that says who can do what to whom
- a domain structure is built for protection of resources or objects— a process should be allowed to access only those resources that it has authority
- **need to know principle**: a process should only access those resources that it requires currently to complete its task
    - damage is limited in case of a bug in the process, like the process boundary limit enforced in memory management
- a process operates within a protection domain to access an object
    - this process inside a domain is often considered a **subject**

## domain

- a domain specifies a set of objects and their operations
- the ability to execute an operation on an object is an access right to the object
- a domain is a collection of objects and their associated access rights
- **access-right = <object-name, rights-set>**
    - the rights-set is a subset of all valid operations that can be performed on the object

## access matrix

- access control is achieved via access matrix
- view protection need as a matrix
    - rows represent domains
    - columns represent objects
- **access(i, j)** is the set of operations that a process executing in domain$_i$ can invoke on object$_j$
- if a process in domain $D_i$ tries to do “op” on object $O_j$, then “op” must be in the access matrix
- user who creates object can define access column for that object
- can be expanded to dynamic protection
    - operations to add, delete access rights
- access matrix design separates mechanism from policy

- **mechanism**: OS provides access matrix and rules and ensures matrix is only manipulated by authorized agents and rules are strictly enforced

- **policy**: user dictates policy on who can access what object and in what mode

# security

- authentication of user, validation of messages, malicious or accidental introduction of flaws, etc
    - issues external to operating system

## implementation

| method | description |
| --- | --- |
| global table | store ordered triples \<domain, object, rights-set\> in a table-- check for \<Di, Oj, Rk\> to execute operation M on object Oj within domain Di |
| access control lists for objects | each column implemented as an access list for one object, resulting per-object list consists of ordered pairs \<domain, rights-set\>-- defining all domains with a non-empty set of access rights for the object |
| capability lists for domains | each row implemented as a capability list for one domain-- object represented by its name or address, called a capability; to execute operation M on object Oj, process requests operation and specifies a possessed capability as a parameter, a capability is like a “secure pointer” implemented by the OS and accessed indirectly |

## protection on file systems

- files should be protected against indiscriminate or unauthorized access
- file owner or creator should be able to control:
    - what operations can be executed on the file
    - who can perform operations on the file
- types of access to control:
    
    
    | read | read the file |
    | --- | --- |
    | write | write the file |
    | execute | load and execute the file |
    | append | add new information to the end of the file  |
    | delete  | remove the file and free its space |
    | list | give the name and attributes of the file |
- most common mechanism is access control list
    - each file is associated with a list, showing who can do what
- in unix:
    - **there are three modes of accesses**: read, write, execute
    - **there are three classes of users**: owner, group, other (public or universe)
    - for example, the command chmod 751 myfile will define the access control list on myfile to be something like
        
        > <owner: read, write, execute>
        <group: read, execute>
        <other: execute>
        > 
- a group is defined by the OS (as instructed by the system administrator)
    - users are added to the group by the administrator, e.g. students can be classified into groups of year1, year2, year3, year4
