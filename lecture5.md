# Lecture 5

We will look at 3 transformations.
We did explicate_control last time that turned L-var-flat to C-var.

Now we go as follows

L-var-flat ---(explicate control)---> C-var ---(select instructions)---> X-var ---(assign homes)---> x86-stack (? name pending lul) ---(patch)---> x86-var-patched ---(prelude and conclude)---> x86-var

X-var: Uses x86-like instructions but retains variables.
x86-stack(?): Uses x86-like instructions and variables are converted to locations/offsets on stack.
x86-patched: We don't want instructions like (addq a1 a2) where both a1 and a2 are memory locations. So we normalize it.
x86-var: x86 with basic instruction support. No flow control or any such thing.

### Select instructions

We convert C-Var to x86-like instructions with variables.

### Assign homes

Convention: Stack grows DOWNWARDS

Given a stack as,

|  | <- rbp
|x1| <- x1 converted to address that looks something like -8[%rbp]
|x2|
|x3|
|x4|
====

### Patch

addq => a2 := a1 + a2
a2 is a memory location
a1 must only be immediate or register value according to x86 standards. That is why patching is required.
Each instruction should only have one memory location at once.

## General things to make for each step

For each language along the way, we must have an evaluator (eval-L1 for L1) and a validator (L1? for an L1). The evaluators for each language along the path L1->L2->L3->L4 will give the same result.

At each stage we are building something like the following,

L-c <---(parser and unparser)---> L-a ---(L-a?)---> B
				   +---(eval)---> val
				   +---(t(L->L'))---> L'-a (Next step in the compilation pipeline after everything else checks out)
+---(L-c?)----------------------------------------> B

B: Result of predicate for verification purposes.

The flatten pass assumed that the language was closed. Generally, we want to make as few assumptions as possible when creating parsers, or else this decision will propagate.

## Select instructions

### Defining X-var

C-var looks something like
1. var := (+ atm1 atm2) | (- atm1 atm2) | (- atm)
2. ret atm
3. ret var

For 1, the X-var equivalents will be as,
> var := (+ var atm2) => (addq atm2 var) [Even takes (+ var var) into account, because atm2 can be var as well)
> var := (+ atm1 var) => (addq atm1 var)
> var := (+ atm1 atm2) => (movq atm1 var); (addq atm2 var);

## Assign homes

(+ 52 (- 10)),

start:
	movq $10 -8(rbp)
	negq -8(rbp)
	addq $52 -8(rbp)
	movq ....
	
## Prelude and conclude

First, we add return address to stack

rsp: stack pointer
rbp: beginning pointer of program in stack

For each program, run a main first as follows. We will assign a block of memory fo the program.
main:
	push rbp
	movq rsp rbp
	subq $16 rsp (16byte convention to store variables, hence program itself is moved 16bytes further)
	jmp start

After running the program,

conclusion:
	addq $16 rsp
	popq %rbp
	retq





