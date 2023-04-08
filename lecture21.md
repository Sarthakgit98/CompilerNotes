# Lecture 21

- Memory Model of a program
	- Registers
	- Local Stack
	- Shadow Stack
	- Heap
	- What happens when we do allocation? How does this memory model behave?
	- x = (Vector 1 2 #t)
		- Allocate memory --> Is there enough space? --Yes--> Give
							|		 ^
							|		 |
							V		 |
							------No------> GC
		- Initialize vector
			- Tag --> 0th position (Decorate with MetaData)
			- Initialize vector
				- vec[1] = 1, vec[2] = 2, vec[3] = #t
		- Put the address of new memory in x
	- Two space copy collector
		- Local stack has a global metadata frame with pointers to the start of from-space and to-space. It also stores the free ptr. This frame is accessible by all functions for communication between GC and function frames.
		- Free_ptr: First instance of accessible/usable heap memory
	- Example on page 108
		- Source to source transformation
		- No need to add anything extra in flow map for (Prim 'Vector)
	- How is GC initialized?
		- Page 108, Ch 6 EOC
	- How to ensure that every root is on the shadow stack?
		- We modify liveness analysis + register allocator
		- Type type variable being spilled --> Allocate on shadow stack
		- Call to collect
			- Needs every memory reference to be on the shadow stack
				- Things could be on registers
			- Solution: Any live tuple variable across a call to GC must be seen to interfere with all registers
		- Function call --> Create shadow stack frame
		- (vector-ref tup n)
		```
		movq tup, %r11
		movq 8(n+1)(%r11), lhs
		```
		- (vector-set! typ n rhs)
		```
		movq tup, %r11
		movq %rhs, 8(n+1)(%r11)
		movq $0, lhs
		```
		- Do not use %rax for these things as that is used for return values and can mess up patch instructions
			- tup could be on stack, to bring something from stack, patch-instructions could use rax to bring it
			- Instead we use %r11
		- (Vector-length tup)
			- Get tag, do bit arithmetic
		- (allocate len type)
			- Inline allocation
			```
			movq free_ptr(%rip), %r11
			addq 8(len+1), free_ptr(%rip) ;;We allocate 8 bytes for even bools because we lazy asf
			movq $tag, 0(%r11)
			movq %r11, lhs
			```
		- (collect bytes)
			- collect (rootstackptr, bytes)
			```
			movq %r15. %rdi
			movq $bytes, %rsi
			callq collect
			```
