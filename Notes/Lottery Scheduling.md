Tickets represent the share of a resource that an entity should receive.
Lottery scheduling achieves this probabilistically by holding a lottery.
# Ticket Mechanisms
### Ticket Currency
Ticket currency allows a user to create their own currency.
For example, user A and user B both are given 100 global tickets
- User A runs two jobs A1 and A2
	- A1 and A2 are given 500 "A bucks" each
- User B runs one job B1
	- B1 is given 10 "B bucks"

A1 and A2 have 50% of the "A bucks" so they both have 50 global tickets.
B1 has 100% of the "B bucks" so it has 100 global tickets.
### Ticket Transfer
Allows a process to lend tickets to another process. This is useful in a cooperative setting like client/server running on the same machine
### Ticket Inflation
Also only really useful in a cooperation environment (malicious processes can just own the CPU)
- If a process knows it's going to need more CPU time, it can simple boost its tickets without coordinating with other processes
# Implementation
Lottery Scheduling is simple. You need:
- RNG
- A data structure to track processes
- A total number of tickets

Steps:
- Generate a number, N
- Traverse the list of processes adding up their ticket values
- Winner once the total is greater than N
### Fairness
If we have two jobs of the same length, how fair is the scheduler?
$$
fairness = jobFinish1/  jobFinish2
$$
We would ideally want fairness to be 1. This is not exactly the fairest algorithm.
### Stride Scheduling
This is a deterministic fair-share scheduler.
Assign each job a stride which is the inverse in proportion to the number of tickets it has.
$$processStride = someLargeNum/processTickets$$
Each process ran exactly in proportion to its tickets so why use lottery scheduling at all?
- No global state
- If a new process comes along, what should its pass be?
### Linux Completely Fair Scheduler (CFS)
![[Pasted image 20240826144050.png]]
Highly efficient and scalable fair-share scheduler.
- Aims to spend very little time making decisions
	- Makes sure to not waste resources (google still uses 5% of the CPU scheduling)

Each time a process runs, it accumulates vruntime
- CFS always picks the process with the lowest vruntime
- CFS varies the time slice size with sched_latency
	- sched_latency is the largest time slice size possible
	- Time slices are determined by sched_latency / numProcesses
##### Niceness
Adds weighting to the time slice calculation.
- Time slice = portion of weight all processes running * max time slice
- Vruntime = previous vruntime time just ran * weighting based on niceness

Niceness is an integer based around 0. Different values of niceness are assigned a specific value of weighting for the calculations above.

