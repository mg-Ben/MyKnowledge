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
Peripherials that allows the interaction between the computer and the external world. For example:
- Keyboard, mouse (input device, because data goes from user to the computer)
- Microphone (input device)
- Monitor display (output device)
- Headphones (output device)

The I/O can interrupt the currently running process by, for instance:
- Waiting for network communication
- Waiting for user keyboard input
- Waiting to write or read files
## Program
_Also known as Process_
A set of instructions and data. When running a program, its instructions and data are passed to [[#Main memory (RAM)]] and then processed by [[#CPU]].
### IPC
_IPC stands for Inter-Process Communication_
## Secondary memory (Disk)
_Often referred as HDD (Hard Disk Drive) or SSD (Solid-State Drive)_
The long-term storage memory.
## Main memory (RAM)
_Also known as RAM (Random Access Memory)_
It is the memory where the currently running [[#Program|programs]] (i.e. instructions and data) are stored. The [[#CPU]] is directly connected to RAM to read those instructions and compute them.
### Kernel
The **Kernel** is the portion of [[#Main memory (RAM)|RAM]] memory where the Operating System is hosted. It contains the common-used features.
#### MicroKernel
The most important part of the Kernel where the few important functions lies (memory and processes management and [[#IPC]]).
### Virtual memory (swap memory/space)
A portion inside [[#Secondary memory (Disk)]] that is used as if it were RAM. Processes are **swapped in** RAM and **swapped out** RAM when needed. That's why the **space** used to swap processes is often known as **swap space** or **swap memory**.

![[virtual-memory-swap.png]]
## CPU
_CPU stands for Central Processor Unit_
Firstly, we introduce IC (Integrated Circuit). It is a physical Hardware component, like this:

![[CPU.png]]
Inside an IC, there can be:
- One CPU
- Several CPUs (called **Multi-core processor**)

Besides, a computer system can have:
- One IC with one CPU
- One IC with several CPUs
- Several ICs with one CPU each
- Several ICs with several CPUs each

We will define [[#Multi-processor system]] as a system that as >1 CPU, and [[#Mono-processor system]] as the system containing just 1 CPU.

CPU is the combinational Hardware circuit that reads instructions from [[#Main memory (RAM)]] and executes it. Executing an instruction means _carry the bits to the suitable ways in order to get the instruction done_. Note that one CPU can only run one instruction at a time, although it can be "pipelinised".
### Mono-processor system
A system with only 1 CPU.
### Multi-processor system
A system with > 1 CPU.  It can be either with one IC with several CPUs inside, with several ICs with one CPU inside or with several ICs with several CPUs inside each one.
All the CPUs share the same [[#Main memory (RAM)]].
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