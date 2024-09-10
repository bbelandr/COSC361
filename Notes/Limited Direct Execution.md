**Direct Execution Motives:**
We need performance. Let the program run directly on the CPU.
Problems with Direct Execution
- We don't know if the program will behave correctly or if it will give up the CPU when the time is up

# Restricted Operations
We need a hardware support for a 'user mode' that keeps you from doing bad things on the computer. This strips the process from complete control of the computer.
In order to allow the process to have temporary kernel mode access, there need to be System Calls defined.
### Trap Table
The trap table is where the System Calls are defined in the OS. Every time any of these system calls are made by a process, there needs to be some sort of switch from the process to the OS on the CPU (we are assuming one core for simplicity).
# How can both a process and the OS run at the same time? (assuming one core)
We need to do something called a context switch.
### Cooperative Approach
Assumes the processes will behave reasonably. Wait for the process to make a system call to get into the OS.
- Probably a yield system call to allow the OS to work

Illegal operations will also give the OS control.
If the process doesn't behave, the only solution is to reboot the system.
### Non-Cooperative Approach
Needs hardware support to set up a timer that automatically does the **context switch**. The timer interrupt saves the process's registers onto the kernel stack (k-stack) and then handles the OS system calls.
The hardware must save enough state to resume the user process.

In both cases, after the OS is finished with its operations, it returns control to the process.
# Scheduling
Workload - The processes running on the system.
- In order to build a context switch policy, you must know a lot about the workload.
- Some (unrealistic) assumptions
	- Each job runs for the same amount of time
	- All jobs arrive at the same time
	- When started each job runs to completion
	- All jobs only use the CPU (no I/O)
	- Known runtime

Common scheduling algorithms
- FIFO
	- The most basic scheduling algorithm
	- If three jobs arrive at time 0, let's assume each job takes around 10 seconds to finish. 
		- A takes 10 seconds to finish after it arrives. 
		- B takes 20 seconds because it has to wait for A to finish first
		- C takes 30 seconds because it has to wait for both A and B
		- The average turnaround time for these is 20 seconds.
	- This is not the best algorithm because there can be scenarios where a very long job gets scheduled first and the shorter jobs have to wait for the long one.
- Shortest Job First (SJF)
	- This makes the longer jobs have low priority. This is much better because this makes the average turnaround lower.
		- Job A that takes 100 seconds goes last when jobs A and B that take 10 seconds go first.
	- Assuming that our jobs arrive all at the same time, this is the best. However, if the jobs don't arrive at the same time, the longer job could just end up arriving first and the scheduler would still put the long job at the front of the schedule, making the short jobs wait.
- Shortest Time-to-Completion First (STCF) or Preemptive Shortest Job First (PSJF)
	- This relaxes the idea that jobs must be run to completion
	- This allows the long job, A, to start but then be put on the backburner when new and shorter jobs show up
	- This is likely optimal assuming:
		- We know job lengths ahead of time
		- Jobs only use the CPU (no I/O)
		- Only metric is turnaround time
### Response Time
We can introduce a new metric called **Response Time**.
Now users are typing at a terminal. When they input something, they want a quick response.
Response time is calculated as the time from when the job is first added to the schedule from the time when the job actually begins working.

- Round Robin (RR)
	- Uses **Time Slices** to choose how long a given job is allowed to run for before switching to the next job. 
	- We can define a time slice to 1 second per job. Assuming 3 jobs of 5 seconds of length, the time slice would go through each job for one second each. This makes the average response time to 1 second.
	- ![[Pasted image 20240823142356.png]]
	- This is not good for turnaround time. The average turnaround time is 14 seconds in this example.
	- There also is a cost to context switching between the jobs frequently
### Incorporating I/O
Now jobs can have I/O. Overlap of CPU and Disk time allows us to use time more efficiently.
	![[Pasted image 20240823142741.png]]

There is a trade off between response time and turnaround time.