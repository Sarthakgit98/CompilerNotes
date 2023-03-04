# Lecture 13

Liveness analysis on X-if

### New cases

1. cmpq <arg1> <arg2>	(R = {arg1, arg2}, W = phi)
2. xorq $1 <arg1>	(R = {arg1}, W = {arg1})
3. movzpq <reg> <arg>	(R = {reg}, W = {arg})
4. set <cc> <reg>	(R = phi, W = {reg})
5. j <cc> label		(R = phi, W = phi)

### Example

```
(let (x read)
	(let (y read)
		(if (if (< x 1)
			(eq? x 0)
			(eq? x 2))
		(+ y 2)
		(+ y 10))))
```

```
callq read_int
movq %rax, x
callq read_int
movq %rax, y
compq x, 1
jl b1
jmp b2
```

1. Create a graph of blocks
2. Traverse in topological sort order, all intructions last-to-first, while performing live-analysis
3. When two paths merge in the block graph, the live sets from both need to be union'd for the parent block

























