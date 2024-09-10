A process is a running program. There can be many processes that run on a computer at the same time.

Someone could run 300 processes on their computer without having 300 CPUs. This is possible through Virtualization of the CPU.

# [[Virtualization]]
* The OS virtualizes the CPU by running one process, stopping it and running another.
	* This is known as time sharing.

In order to share time, we need policies that use the mechanisms intelligently. These are called scheduling policies.

# Machine State (Parts of a process)
- Memory
	- Instructions lie in memory
	- The data that the process reads/writes is in memory
	- The memory that the process can address is its **address space**
- Registers
	- Special registers that are extremely important
		- Program counter/Instruction pointer
		- Stack Pointer and Frame Pointer
- I/O Information
	- Interacting with storage devices and file descriptors
# Process API
There is an interface provided for any modern operating system that all processes use.
- Create - make a new process
- Destroy - Destroy a process forcefully
- Wait - wait for a process to stop running
- Miscellaneous Control - Suspend/Resume, etc.
- Status - Give info about process like running time, state, etc
# Creation of a Process
- Load code and static data into memory
	- Early OSes would load the entire program into memory, but nowadays we do this lazily, meaning that we only load the parts of the program that are needed.
- Allocate memory for the program's stack and heap
- Initialize IO
- Start execution at main() and transferring control of the CPU to the new process
# States of a Process
- 3 common states (there can be more like ZOMBIE, SLEEPING, UNUSED, etc.)
	- Running - Executing instructions
	- Ready - Ready but not running anything
	- Blocked - Waiting for an event to happen (like an IO request from stdin)
	