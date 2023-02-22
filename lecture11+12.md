# Lecture 11

Formal definitions regarding flow analysis for X-if

With the change from L-var to L-if, we now have branches rather than just a single chain.

Some steps involved for X-if, 

1. Parsing (indexing, parent)
2. f and f* maps
3. Flow maps (k/d and br)
4. Code generation (b)
5. Block merging
6. Liveness analysis for each block

Output of 3 is AST and Control flow graph.

## Structural maps: Domains and ranges

I(e) is index of some expression e

e : I(e) -> Flat term (Done in step 1)
P : I(e) -> Kont(e)
f : I(e) -> I(e) (Index of expression to index of subexpression)
f*: I(e) -> I(e)
k : I(e) -> Kont(e)
d : I(e) -> Loc(e)
F : I(e) -> Kont(e) x Loc(e) [k x d]
br: I(e) -> I(e) x I(e) (Two possible routes)

1. Vars(e) range is the set of variables ocurring in e
2. consts(e) range is the set of constants ocurring in e
3. Kont(e) range is I(e) U {ret} (continuation space of e), K: I(e) -> Kont(e)
4. Loc(e) range is I(e) U Vars(e)(location space of e), d: I(e) -> Loc(e)

## Definitions

### ei

e: Partial map, 
```
N -> 	({let} x Var x N x N) 
	U ({if} x N^3) 
	U (Bop x N^2) 
	U (Uop x N) 
	U Var 
	U Const
```

N: Natural number set
bop: Binary op
uop: Unary op
Var: Variable
Const: Constant

Eg, e1 = (let x 2 3), e2 = (if n1 n2 n3)

### fi

Cases->

1. ei is atomic: fi = i
2. ei = (bop m n): fi = m
3. ei = (uop m): fi = m
4. ei = (if j m n): fi = j
5. ei = (let x j m): j

### f*i

f*i = lim-map(f)

### di

The variable that block i is obligated to send

### ki

The block that we must go to after current block wraps up. Refers to Kont of i.

# Lecture 12

### Flow map (Fi)

Fi = (ki/di)
F : I(e) -> Kont(e) x Loc(e) [k x d]

1. em = (if i j n): 

	Fi = m/i, Fj = km/dm, Fn = km/dm

2. em = (let x ei ej): 

	Fi = f\*(j)/x, Fj = km/dm
	
3. em = (bop ei ej): 

	Fi = f\*(j)/i, Fj = m/j
	
4. em = (uop ei): 

	Fi = m/i
	
5. if Pm = ret: 

	Fm = ret/m
	
6. if em is atomic, do nothing

### Branch map (bri)

1. ei = (if ej em en): bri = (f\*(m), f\*(n))

### Code generation

Since all necessary info is in the maps above, we can generate codes relatively easily

Necessary mappings: ei, ki, di, bri

## Code map

tr: tail recursion

tr(ret, l) = ret l

tr(m, _) = goto m

1. ei is atomic:

	bi = [di = ei; tr(ki, di)]
	
2. ei is a bop, ei = (bop em en)
	
	bi = [di = (bop dm dn); tr(ki, di)]
	
3. ei is a uop, ei = (uop em)

	bi = [di = (uop dm); tr(ki, di)]
	
4. ei is a let, ei = (let x em en)

	bi = NA [di = []]
	
5. ei is an if, ei = (if em en el)

	bi = [if dm goto pi1(bri), else goto pi2(bri)] ; bri is a pair, pi1 is the first and pi2 is the second value of that pair. In this case, 1st and 2nd branch.


## Example

```
(let x (read)
	(let y (read)
		(if	
			(if
				(< x 1)
				(= x 0)
				(= x 2))
			(+ y 2)
			(+ y 10))))
```

Note: Output from book and the algo taught in class have differences

The result of flow analysis of an AST is a decoration of all nodes with k/d values.






