How do we virtualize memory with pages to avoid the issues with segmentation?
- Instead of variable sized segments, let's have fixed-size pages
- The virtual address is in pages
- The physical address is in page frames
- Simple for free-space management because of fixed-size units

![[Pasted image 20240906142250.png]]
# Address Translation
OS maintains a page table per-process.
VPN is the virtual page number.
![[Pasted image 20240906142339.png]]
After the address translation, we know that we are going to go to 0111, or the seventh page frame.
![[Pasted image 20240906142444.png]]
# Where are page tables stored?
Page tables can get very large
Imagine 32 bit address space with 4KB pages
- 20-bit VPN + 12 bit offset (The 12 bit offset allows you to go to anywhere in the 4KB)
- That's 2^20 translations per process!
- If each entry was 4 bytes, that's 4MB per process needed for page tables

Thus, we don't store the page table in the MMU but somewhere in physical memory.
# What's actually in the page table?
- The Physical Frame Number (PFN)
- Valid bit
- Protection bits (RWX)
- Present bit (It's in physical memory)
- Dirty bit (Has it been modified?)
- Reference/Accessed bit (Is it being used?)
# Paging is also too slow
Paging requires an extra memory access for every memory access!
- Two major issues
	- Memory usage
	- Extra memory references