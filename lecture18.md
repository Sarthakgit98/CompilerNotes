# Lecture 18

Today: Converting L-fun to C-fun

## Example

```
(add (a, b)
	(+ (+ a 0) (+ b 0)))
	
(let x 5
	(add (+ x 3)
		(+ x 6)))		
```

Now, we will have an AST for each function and the final code.

We already know how to convert L to C normally. For functions, the arguments should be treated as children.

For a function with two arguments as above,

```
i = (add j k)
j = (+ x 3)
k = (+ x 6)

Pj = i
Pk = i

fi = j
f*i =	fi ,if i = fi
	f*(fi)

For k/d, treat the function similar to a prim for the arguments j and k.

Fj = F*k/j
Fk = i/k

for i,

di = (add dj dk)
ret/jmp
^ is not enough

```

#### Code generation for function

```
We could always use callq add directly, but the indirect function call as follows is more optimal somehow

leaq add(%rip), %rbx
callq *%rbx

*%rbx means the label is already resolved. The address has been given instead. Just jump to that.

We will write the generated code for i/function as

tmp = leaq(add)
di = callq tmp ...
ret/jmp
```

When creating callq, we need to handle

1. Passing parameters
2. Caller and callee saved registers
3. Pushing and popping frames

For now we assume we will have no more than 6 arguments

Note: We can figure out if a function is a tail call function by seeing if the function call is the last call right before a return or not. If function is tailcall, we do not need to execute its start and prelude each kind, we will simply overwrite what we have. To implement this, we simply use jmp instead of callq for subsequent recursive tail calls.

#### Liveness analysis

Suppose we have code

```
a = 5
b = 10
d = sum(a, b)
c = a + d
```

We don't save a and b in caller saved register. 

