# Lecture 15

Liveness analysis, contd.

We have,

> Monotonic function F
> Vector of subsets of program variables A
> Equation X' = F(X)
> Poset relation on A

where, monotonic function F is,

> bi = Bi(ai)
> ai = U (bi+) [if split]
> ai = bi+ [if join]

Today, we will solve the set of equations bi = Bi(ai) OR X' = F(X).

## Fixed point iteration and application to liveness analysis

Liveness analysis: Solving a system of set equations

bi = Fi(ai) for some block Bi

bi: Live before
ai: Live after

We can construct a monotonic F for the list of all live after sets X as F(X), so we get,

X' = F(X), X and X': List of all live-after and live-before sets

> X = [a1 b1 a2 b2 ... an bn]
> X' = [a1 b1 a2 b2 ... an bn]

We solve this equation to find all ai and bi.

### Fixed point iteration

X'i : [1 ... 2n] -> Set[Loc]

We impose some structure on this,

([1 ... 2n] -> Set[Loc], subsetsq (some ordering)) is a poset

> x subsetsq y iff,
>	xi is subset of yi

```
Let (P, subsetsq) be a poset
Let A is a subset of P
Let x belong to P

x belong to A-up (Upperbound: Set of all sets that are supersets of x) if
a subsetsq x for all a belong to A
```

```
Let (P, subsetsq) be a poset
x belong to P is minimal if,

for all y belong to P,
y subsetsq x => y = x
```

```
if for some y,
y subsetsq x and y != x,
y is abssubsetsq of x (subset but not possibly equal)
```

```
Notation V A = join of A, minimal of upper bound of A
```

```
Let (P, subsetsq) be a poset
Let F : P -> P be a map
F is monotone iff

for some a subsetsq b, F(a) subsetsq F(b)
```

```
Knaster-tasrki theorem (specialized for subsets)

Let (P, subset) be a poset, over the subset relation
The set of fixed points of a monotone map over P form a poset (actually a lattice) with a minimum element
```

Lattice: Hasse diagram

```
Let 'bottom' be a mapping of all ai/bis with empty sets
Applying F on ai/bis updates the value of the mapping

We apply F repeatedly until we reach a fixed point where no more changes are occuring
```

### Example from section 5.2 of EoC (p87)

Applying F on one block

```
Block B1:
		---a1 - {i} - {sum} = a1 - {i, sum}
	movq $0, sum
		---a1 - {i}
	movq $5, i
		---a1
	jmp block5
		---a1
```
Update a1 as b of next block.
b1 = B1(a1) = a1 - {i, sum}

Initially, all bi are phi. 









