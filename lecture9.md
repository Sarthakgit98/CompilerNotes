# Lecture 9

Continuing with L-if

## AST for L-if

l-if: num | var | ->bool<- | prim-app | let | ->if<-

Reminder: We want to turn an ast to a sequence of instructions

Today: Move from ast to instruction sequence with L-if context

### Flow

Due to if, we can have multiple blocks of instructions with branching.


Block1	-> Block2 -> Blockfin
	-> Block3 ->
	
We can either go block2 or 3 before the final block.

### Example:

L-var exp,
> e = (+ (- 2) (+ 4 x))

We can draw ast for above.

We name each node in the above tree with a number,

```
+root: 6
-: 2
2: 2
+2: 5
4: 3
x: 4
```

Thus we get

```
>e1 = 2
>e2 = (- e1)
>e3 = 4
>e4 = x
>e5 = (+ e3 e4)
>e6 = (+ e2 e5)
```

Solution of each subtree is the evaluation of the subtrees under it. We are almost half way to sequence of instructions now.

The above is a flat system of syntax expressions.

We will now do eval of above step by step (with absolutely zero optimization)

```
Instructions	f	f*	k/d
e1 = 2		e1	e1	b2/t1
e2 = (- e1)	e1	e1	b3/t2
e3 = 4		e3	e3	b4/t3
e4 = x		e4	e4	b5/t4
e5 = (+ e3 e4)	e3	e3	b6/t5
e6 = (+ e2 e5)	e2	e1	ret/t6
```

1. f: Immediate next sub-exp to solve
2. f*: Deepest sub-exp to go to from f
3. t: temp variables and locations (t's given below)
4. b: blocks (b's given below)
5. e: expressions
6. k: Continuation, to the f* of sibling/next-operand or to the block computing the current e
7. d: Temp value to take while going to k
8. k/d: Flow Map (F) (Rules given in class notes)

For d, we need to think about what to take. We can't just take every kind of value generated at f or before it.

We get following blocks of code

```
b1: 
	t1 = 2
	goto b2

b2: 
	t2 = (- t1)
	goto b3
	
b3:
	t3 = 4
	goto b4
	
b4:
	t4 = x (Pretend x is coming from somewhere)
	goto b5

b5:
	t5 = (+ t3 t4)
	goto b6
	
b6:
	t6 = (+ t2 t5)
	ret t6

```

For optimization, we can fuse a lot of blocks together since they only have one goto.

We can also do 'constant folding'. We don't need a temp variable for constants, so we can skip that.

### Another example, with if this time

Let,
> e = (+ (if (> (-x) 3) (+ (-4) y) (- z (- x))) (+ x 6))

We get an ast from this. We are doing constant folding and skipping 'e=const/var' this time.

From that, we get the following equations

```
e1 = -x
e2 = (> e1 3)
e3 = (if e2 e5 e7)
e4 = (- 4)
e5 = (+ e4 y)
e6 = (- x)
e7 = (- z e6)
e8 = (+ x 6)
e9 = (+ e3 e8)
```
And then the following table (Br = branch, we only worry about this with if statements)

```
Instructions		f	f*	F=k/d	Br
e1 = -x			e1	e1	b2/t1	
e2 = (> e1 3)		e1	e1	b3/t2	
e3 = (if e2 e5 e7)	e2	e1	b8/t3	[b4, b6]	
e4 = (- 4)		e4	e4	b5/t4	
e5 = (+ e4 y)		e4	e4	b8/t3	
e6 = (- x)		e6	e6	b7/t6	
e7 = (- z e6)		e6	e6	b8/t3	
e8 = (+ x 6)		e8	e8	b9/t8	
e9 = (+ e3 e8)		e3	e1	ret/t9	
```

For e7, we skipped left child because it was a const/var. It's a shortcut.

The important F assignments are for e3, e5 and e7.












