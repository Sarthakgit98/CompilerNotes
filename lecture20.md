# Lecture 20

Today: Garbage collector (Theory)

- Works at runtime.
- Types of GC
	- Conservative
		Do not require compiler support. 
	- Precise
		Require compiler support. Here, GC can tell the difference between int and int*.
- Basic concept of a GC
	- Find all live memory
		Root set
	- Reclaim memory
		Collection
- Conservative garbage collection
	- How is root set created?
		Goes through all stack memory, stack and heap. Checks if there is any live reference from reg to heap and clears it.
	- Why is it conservative?
		int and int* differentiation is not available at runtime. So conservative compilers end up with over-approximation errors where,
		> int a = 0x00080
		ends up being read as if 0x00080 is an address when in fact it is a literal value. So it frees memory that does not need to be freed.
- Precise garbage collection
	- How to track root set?
		- Need to distinguish between pointers and integers inside the vector
		- Possible options:
			- Attach tag to each object: Sad for a statically typed language
			- Store different types of objects in different regions
			- Use type info to guide collection by generating type specific code
		- Option 3 is the most performant but also high complexity
		- We use a mix of option 1 and 2
		- Use Root stack/shadow stack: Spill pointers on root/shadow stack and values on normal stack.
		- How to distinguish between pointers inside the vector?
			- Create a tag
			- Tag --> 1bit forwardingPtr, 7 bits length, 50 bits pointer mask
			- Example,
			For A = (vector int int (vector int)). A will be in SS. The vector will be in heap. In heap, we have 3 contigous values int int and pointer. We cannot distinguish between these in HEAP. So we assign some tags for this.
			- Our pointer mask is bad, a production quality compiler (cue LLVM) would allow arbitrarily sized tuples.
		- Collection algorithms
			- Two space copying collector
			- BFS: Cheney's algo
			
- Tag example
```
	[Unused | Pointer Mask | Vector Length | Forwarding]
	(Vector int int (Vector int))
	[Unused (all 0) | ... 0 0 1 0 0 | 0 0 0 0 1 1 | X]
	For the pointer mask, rightmost bit refers to leftmost or first vector val.
	Assume Forwarding pointer is a 0.
	
	The tag is attached to the vector itself as an extra 64bits when the vector is stored in the heap.
```

- Two space collection example: Page 103, Fig 6.5
