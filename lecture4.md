# Lecture 4

We are looking at L-var: A family of languages as

L-var-h - High level lang with arithmetic and lexical scope
L-var-c - Closed L-var-h expressions
L-var-u - All variable bindings use distinct names
L-var-f - Flattened expressions
C-var - Assembly basically

L-var-h --- L-var-c
        L-- L-var-u
        L-- L-var-f
        
Closed expression: No free variables. 

We will have an L-var-h 
-> Check if it is closed to make sure it is L-var-c 
-> Convert it to L-var-u with uniquify 
-> Convert it to L-var-f with flattening
-> Convert that to C-var

Today we skip the uniquify step for lulz and learnings

## Goal of flattening

Take an expression of form
> (let x e1 e2)

as
> (let x (+ 2 (- 3)) (+ x (+ 4 (let y 3 (+ y 2)))))

turn it into a series of atomic expressions

x1 = ....
x2 = ....
x3 = ....
.
.
.
.
return val


## L-var-flat

The above series of atomic expressions is part of L-var-flat

i.e., an expression is simple if it is atomic or an app whose arguments are also atomic
A flat expression is either simple or is a let expression whose binding expression is simple and whose body is flat

Eg, 

> 2 (atomic ... simple)
> x (atomic ... simple)
> (+ 2 x) (Operands atomic ... simple)
> (+ (- x) 5) (Not simple)
> (let x 5 x) (Not simple, since neither atomic nor application. BUT it is flat)

Today we will convert L-var-c to L-var-f

### Defining this transformation

Take a tree, convert to another tree that evaluates as a flat

> (+ 2 3) => (+ 2 3)


> (let x (+ 2 (- 3)) (+ x 4)) => (let g1 (- 3)
					(let x (+ 2 g1)
						(+ x 4)))
						
> (let x (let y (+ 2 (- 3)) (+ y 5)) (+ x (- 1))) => (let g1 (- 3)
								(let y (+ 2 g1)
									(let x (+ y 5)
										(let g2 (- 1)
											(+ x g2)))))

The expression on the left HAS to be closed. RHS is L-var-f.

### Intuition

1. Draw AST of lhs
2. Decorate each node with a name or a constant (atomic or identifiable value)
	We decorate the root with ans, and continue propogation of names. 
	Left child name propogates to middle child as name
	Root name propagates to right child as name
	Constants named as constants themselves
	For non simple apps like (+ 2 (- 3)), we make a new identifier name like g1 for the - operator as g1 -> (- 3)
3. Thus, for (let y (+ 2 (- 3)) (+ y 5)), we get mappings as	
	g1 -> (- 3)
	y -> (+ 2 g1)
	x -> (+ y 5) [Using x because it propogates from the let x super tree of the 3rd example above]
	Now for the 3rd example above, 
	(let x [x] (+ x (- 1))) [[x] is from the let y subtree]
	g2 -> (- 1)
	ans -> (+ x g2)
4. Now go through each of these mappings in order to create
	(let g1 (- 3)
		(let y (+ 2 g1)
			(let x (+ y 5)
				(let g2 (- 1)
					(+ x g2)))))
			
We can create the g1, g2 etc names with gensym in racket

### Conversion to C-var

Above example turns to

(assign g1 (- 3))
(assign y (+ 2 g1))
(assign x (+ y 5))
(assign g2 (- 1))
(return (+ x g2))






