[Textbook Page](https://pages.cs.wisc.edu/~remzi/OSTEP/vm-segmentation.pdf)
How to support a large address space with potentially a lot of free space between the stack and the heap?

# Generalized Base/Bounds
Instead of having just one base and bounds pair in the MMU, we should just instead make segments of the local address space.

What segment are we looking at?
### Explicit approach:
We use the top few bits of the virtual address to determine which segment we're in.
- 00 means Code
- 01 means Heap
- 10 means free space
- 11 means Stack

With only 3 segments, we're wasting virtual address space with two bits. 
### Implicit Approach:
Hardware determines segment by noticing how the address was formed
- Program counter address means code segment
- Address based off stack pointer means stack segment
- Any other address must be to the heap

We also must keep track which direction the segments will grow in, since the stack grows in the negative direction rather than the positive.![[Pasted image 20240904141000.png]]
![[Pasted image 20240904141412.png]]![[Pasted image 20240904141423.png]]
16.1 shows the virtual address space and 16.2 shows the physical address. Translating a place in memory from virtual to physical goes like this:
- I need to translate 1.5KB from the virtual to the physical.
- According to 16.1, we know that this falls in the code section. 
- Then you just find the code base offset (in this case 32KB according to 16.4) and add it to 1.5KB.

If you do this for the stack, it's a little weirder:
- I need to translate 14.5KB to the physical address
- We are in the stack
- According to 16.4 the max size of the stack is 4KB. 
- We know that the whole virtual chunk is 16KB. 
	- 16 - 14.5 = 1.5KB to go backwards from the base of the stack
- 28 - 1.5 = 26.5KB, the physical address

# OS Support
- The OS must save segment registers on a context switch
- The OS must allow segments to grow or even shrink
	- Calling malloc() might need to allocate more space in the heap segment
- OS must manage free space in physical memory
	- We end up with little holes of free space. They're often useless. This is called external fragmentation\
![[Pasted image 20240904142849.png]]
### Compact Physical Memory
In order to fight the fragmentation, we could just put all the fragments together
- We could also compact the physical memory, but this is expensive
- Makes requests to grow segments hard to serve

We could also use various free-list management algorithms (there are many and none are definitive solutions to the issue)
Compacting the memory gives more space for additional memory usage if other processes need to be loaded. We will never eliminate external fragmentation.