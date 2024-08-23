---
tags:
  - Computer_Systems
---
# Core principles
## Definition
An Operating System (OS) is a [[#Program|program]] that controls the execution of other programs in the system. To fully understand the OS idea, you have to imagine that the [[#CPU]] is dummy and only runs what it reads from [[#Main memory (RAM)]]. If OS didn't exist, a lot of problems might happen, such as:
- CPU runs an instruction that accesses to other user's data in the OS
- CPU runs an instruction that accesses to disk storage or any other [[#I/O devices|I/O device]] to break it or get some information the user has not access
_Summarizing: someone needs to control CPU, and that is the Operating System!_

OS can also be viewed as a layer over the **Hardware layer** that eases programmers the task of dealing with hardware.

![[OS-layerView.png]]

The OS provides [[#Utilities]] to ease programmers the use of the system. It also provides access to [[#I/O devices]] as well as processes execution planning and Hardware error control, among others.
As [[#I/O devices]] are very slow because requires the interaction with users or external devices which might make the [[#CPU]] stop, there are two OS models:
- [[#Mono-programming]]
- [[#Multi-programming]]
## Mono-programming
_Also known as Single-task_
Only one program can execute at a time. The [[#CPU]] must run the entire program and cannot change to another one until the execution of the current program finishes.
This means that, in case the program requires access to some [[#I/O devices|I/O device]], it will wait until that I/O device returns data, what is very time inefficient.

![[monoprogramming-ioDevice.png]]

Nowadays, mono-programming is obsolete. Current OSs use [[#Multi-programming]].
### Single batch systems
An example of Mono-programming is Single batch systems. In these ancient systems, there is a **Monitor** (analogous to the current concept of Operating System) that consisted of several instructions loaded in [[#Main memory (RAM)]] that ordered [[#CPU]] to jump to the next user program once the previous one had finished. The portion in [[#Main memory (RAM)]] where the **Monitor** program was hosted was called **Resident Monitor** (analogous to the current concept of [[#Kernel]]).
Note that the user's program instructions can't modify Monitor's program instructions while they are running. In that case, the CPU Hardware must detect it and transfer the control to Monitor program, that will abort the user's program. The user's program must also not access to other user's program instructions.
## Multi-programming
_Also known as Multi-task_
It makes full use of [[#CPU]]. Whenever a process needs to wait, the CPU can switch the currently running process to another one:

![[multi-programming.png]]
### Multi-programmed batch systems
An example of Multi-programming is Multi-programmed batch systems.
Note that in multi-programmed batch systems, several program pieces come into play. Therefore, when the first one stops because it is waiting for external resource, there must be a mechanism to switch to another program piece. The next program piece to run cannot be an arbitrary program, but it must want to be executed. The programs that wanna be executed are waiting in a **queue**!
That queue is the [[#Short-term queue]], inside the [[#Main memory (RAM)]].

#### Short-term queue
A list of program pieces in [[#Main memory (RAM)]] waiting to be executed by [[#CPU]].
#### Long-term queue

## Utilities
The utilities are, for example, code editors (e.g. [[UNIX#vi (Visual Editor)]]) and debuggers.

Note that, if these utilities didn't exist, the programmer should deal with Hardware, entering bit by bit the code instructions inside the [[#Main memory (RAM)]] to be processed later by [[#CPU]]. In fact, that's what happened in the 1940s and 1950s, when programmers needed to use a console with lights and switches. In fact, programs were written in machine code, and the output was made of lights indicating the state. The debugging was very hard to do, as it was necessary to examine the [[#Main memory (RAM)]] and register contents inside [[#CPU]].
## I/O devices
_I/O devices stands for Input/Ouput devices_
Peripherials that allows the interaction between the computer and the external world (hardware components that can accept input data (e.g. commands) or produce output data (display information on screen). For example:
- Keyboard, mouse (input device, because data goes from user to the computer)
- Microphone (input device)
- Monitor display (output device)
- Headphones (output device)

The I/O can interrupt the currently running process by, for instance:
- Waiting for network communication
- Waiting for user keyboard input
- Waiting to write or read files

When user presses a key on the keyboard or moves the mouse, an [[#IRQ]] is generated.
## Program
_Also known as Process_
A set of instructions and data. When running a program, its instructions and data are passed to [[#Main memory (RAM)]] and then processed by [[#CPU]].
### IPC
_IPC stands for Inter-Process Communication_
## Secondary memory (Disk)
_Often referred as HDD (Hard Disk Drive) or SSD (Solid-State Drive)_
The long-term storage memory.
For example, in [[GNU]]/[[Linux]] Operating Systems, we denote the disks as `/dev/sdX` (e.g. `/dev/sda`, `/dev/sdb`, `/dev/sdc` and so on).
### Partition
_Also known as Volume_
Each disk can be divided into one or more partitions, which are subdivisions of that disk.
For example, in [[GNU]]/[[Linux]] Operating Systems, we denote the partitions as `/dev/sdXY` (e.g. `/dev/sda1`, `/dev/sda2`, `/dev/sdb1` and so on).
#### File system
There is a single and unified file system for the entire Operating System. However, each partition is mounted inside some directory of that file system.
There are several File System types that determine how data is organized within a partition:
- **EXT4**: widely used in [[Linux]]
- **NTFS**: widely used in [[Windows]]
- **HFS+ and APFS**: widely used in MacOS
- **FAT32/exFAT**: widely used in removable devices
Each partition can have its own file system type (e.g. `/dev/sda1` is EXT4, `/dev/sda2` is NTFS, `/dev/sdb1` is EXT4...).
##### Mount point
Each partition has got a mount point. When we mount the partition in the unified file system, the data for that partition will be accessible from the directory we have mounted the partition (i.e. _Mount point_).
For example, we can mount `/dev/sda1` inside the directory `/data` and `/dev/sdb3` inside `/home`.
## Main memory (RAM)
_Also known as RAM (Random Access Memory)_
It is the memory where the currently running [[#Program|programs]] (i.e. instructions and data) are stored. The [[#CPU]] is directly connected to RAM to read those instructions and compute them.
RAM is **volatile**, which means that data is lost when computer is turned off.
### Cache RAM
It is actually a different component, closer to [[#CPU]] (Cache memory is built into the CPU chip or placed very close to it to minimize access time).

There are typically different cache levels:
- `L1`: the smallest; the fastest. For example, it can have 64KB of space
- `L2`: intermediate level. For example, it can have 512KB of space
- `L3`: larger, but slower. For example, it can have 8MB of space
_Some manufacturers might include the cache space in the product specifications. E.g. when you see a specification stating 8GB RAM, it refers to the main system memory, not including the cache memory_

Cache RAM works with the following principle: _If the data is found (cache hit), it can be accessed more quickly than if it had to be retrieved from RAM. If the data is not in the cache (cache miss), it is fetched from RAM and stored in the cache for future access._
### Kernel
The **Kernel** is the portion of [[#Main memory (RAM)|RAM]] memory where the Operating System is hosted. It contains the common-used features.
The Kernel manages the Hardware, Memory and Processes, because those are the tasks of an Operating System.
#### MicroKernel
The most important part of the Kernel where the few important functions lies (memory and processes management and [[#IPC]]).
### Virtual memory (swap memory/space)
An Operating System technique that involves using a portion inside [[#Secondary memory (Disk)]] as if it were RAM. Processes are **swapped in** RAM and **swapped out** RAM when needed. That's why the **space** used to swap processes is often known as **swap space** or **swap memory**.

![[virtual-memory-swap.png]]
## CPU
![[cpu-overview.png]]
_CPU stands for Central Processor Unit_
_We often use the term **Processor** instead of **CPU** to avoid confusions_
The **CPU or processor** is the IC (Integrated Circuit) which is installed in the motherboard:

![[CPU.png]]
Inside an IC, there can be:
- One **core**
- Several **cores** (called **Multi-core processor**)

Besides, a computer system can have:
- One processor with one core
- One processor with several cores
- Several processors with one core each
- Several processors with several cores each

We will define [[#Multi-processor system]] as a system that has >1 processor (chip), and [[#Mono-processor system]] as the system containing only 1 processor (chip).
### Core
 The processor core is the combinational Hardware circuit that reads instructions from [[#Main memory (RAM)]] and executes it. Executing an instruction means _carry the bits to the suitable ways in order to get the instruction done_.
#### Thread
Each core (i.e. combinational circuit) can do some magic to appear as if it was running several instructions at a time. These advanced techniques are:
- **Pipelining**
- **Superscalar architecture**
- **SMT** (_Simultaneous Multithreading_)
### Mono-processor system
A system with only 1 processor (CPU).
**Most modern PCs are monoprocessor systems.**
### Multi-processor system
A system with > 1 processor (CPU).
All the CPUs share the same [[#Main memory (RAM)]].
### Interrupts
#### IRQ
_IRQ stands for Interrupt ReQuest_
A signal sent from a hardware or software (keyboard, network card, graphics card, USB ports, sound cards...) to a computer's processor to momentarily stop (interrupt) its operations, indicating that an event needs immediate attention.
There are two types:
- **SoftIRQs**: Software IRQs (generated by the kernel rather than hardware devices)
- **HardIRQs**: Hardware IRQs (generated by Hardware components)
In [[Linux]] systems, you can get this information from [[Linux#/proc|/proc]] directory.
### CPU times
#### IRQ Time
The IRQ Time is the period during which the **CPU** is handling [[#IRQ]]s.
#### I/O Time
The I/O Time is the period during which the **process** is waiting for reading from or writing to an [[#I/O devices|I/O device]] (typically data transfer operations between [[#CPU]] and [[#I/O devices]]). In the meanwhile, the CPU can perform other tasks or go idle.
#### Nice Time
_Only for [[UNIX|UNIX-like]] [[Operating System|Operating Systems]]_
The Nice Time is the time taken by CPU to manage low-priotity processes that have been adjusted through [[UNIX#Nice processes|nice mechanism]].
### CPU Architectures
#### AArch64 or ARMv8-A
Developed by ARM Holdings. Used in:
- Mobile devices: smartphones, tablets, wearables (low power consumption devices).
- Embedded systems: IoT devices, smart TVs.
#### x86_64 or AMD64/Intel64
Developed by Intel and AMD. Used in:
- Desktop computers and laptops
- Gaming consoles
- Servers and datacenters
#### PowerPC
Used in [[AIX]] OS.
## Shell
A computer program that exposes to the user the Operating System's services through a **CLI** (Command Line Interface) or a **GUI** (Graphical User Interface). In CLI cases, the shell has got a **shell-specific language**.