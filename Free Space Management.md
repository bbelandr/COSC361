How should free space be managed?
- Malloc takes a single argument, a size and returns a void*
- Free takes a single argument, a pointer from malloc

This means that the library must be able to solve memory size
- We need a data structure to keep up with free space
- Focus on [[Segmentation#OS Support|external fragmentation]]
	- Internal fragmentation is having memory gaps within the program
		- If the OS actually gave you 8 KB but you only asked for 5 KB, this is internal
- No compaction of free space
- Assume heap is a single fixed size
# Splitting
When we malloc() for less than a free chunk of memory, we split it.
![[Pasted image 20240906135614.png]]
This split the 30 bytes into a group of 10 free, 10 used, and 10 free again.
![[Pasted image 20240906135817.png]]
This represents the heap's free space
# Coalesing
We need to add the freed memory back to the heap, so you get this:
![[Pasted image 20240906135911.png]]
But there's a better way to represent the freed memory that just puts everything back together:
![[Pasted image 20240906135959.png]]
# Tracking the size of allocated regions
When free() is called, magic numbers are used to detect corruption quickly. Magic numbers are just fixed numbers that are expected to be there in memory
- This means that malloc() needs to find space that is just a little bit bigger than what it is asked to take

![[Pasted image 20240906140335.png]]
Allocated chunks have magic numbers and unallocated chunks have a poitner to the next unallocated chunk
![[Pasted image 20240906140612.png]]
# Growing the Heap
What if the heap runs out of space? You ask the OS to extend the heap through sbrk().
- sbrk() aka s-break literally just gives the heap more room

# Basic Strategies (Policies)
### Best Fit
- Search the list for the smallest chunk that can fulfull the request
- Can be expensive to exhaustive search for hte correct free chunk
### Worst Fit
- Search the list for the largest chunk that can fulfill the request
	- This tries to leave big chunks free instead of losts of small chunks
- Can be expensive to exhaustive search for the correct free chunk
### First Fit
- Pick the first chunk that can service the request
- This is fast but can pollute the free list with small objects
- Often paired with address-based ordering
### Next Fit
- Instead of searching from the beginning of the list every time, we remember where we last looked in the list
- Now from this point we pick the first chunk that fits
- This avoids splintering the beginning of the list while giving similar performance to the first fit
### Segregated Lists
- If a particular application has one or a few popular sized requests, keep a list just to manage objects of that size
- Other requests go to a general allocator
- Benefits
	- Allocating the popular size is quick and easy
	- Fragmentation is less of a concern
- Issues
	- How much memory do we didicate to this pool of memory
- Slab allocator
	- Designed for Solaris kernel
	- Object caches made at boot up for common kernel structures
		- Locks, inodes, etc
	- When cache runs low, it asks for more memory from the general allocator
### Buddy Allocation
- Tries to make coalescing easier
- Cuts a given chunk of memory in half until it can just barely give you enough memory that you asked for
- When a block is freed, the allocator checks whether the 'buddy' is freed
![[Pasted image 20240906141920.png]]