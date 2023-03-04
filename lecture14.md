# Lecture 14

## Liveness analysis

For some addq u, v

livebefore = (liveafter - {v}) U {u, v} = liveafter U {u, v}

Each instruction is like a transfer function from one set of live variables to another

> Liveafter = a
> Livebefore = b

a --(Fi)--> b

x0 --(i0)--> x1 --(i1)--> x2 --...--> xn

x0 --B--> xn

a --B--> b

b = B(a), B is a composition of functions that do multiple transfers for a number of functions

We can see B as a block.

### General observations for liveness analysis

> bi = Bi(ai)
> ai = U (bi+)
> We do not rely on bi+ = U (ai) or bi+ = Intersection (ai) due to conservation violations

Domain of livesets: Variables in the program

For some program,

```
a1 = ...
b1 = ...
a2 = ...
b2 = ...
a3 = ...
b3 = ...
```

We can make a vector of these and call it x-bar

```
a1 = Fa1(a1 ... b3) = Fa1(x-bar)
b2 = Fb1(a1 ... b3) = Fb1(x-bar)
.
.
.
b3 = Fb3(a1 ... b3) = Fb3(x-bar)
```

We can coalesce these as x-bar = F(x-bar)

x-bar = Vector of sets of locations

In more general terms, 

> X-bar-cap = F(X-bar)

F = Transform function

X-bar ---F---> X-bar-cap

### Example

Let Ii = addq u, v

We have Fi for it

> X --Fi--> X'

Suppose for a second we do,

```
Xa --Fi--> Xa'
Xb --Fi--> Xb'
```
If Xa is subset of Xb, then Xa' is subset of Xb' (assuming no changes in read/write sets)

X' = (X - Wi) U Ri

#### Partial order

R is partial order if

1. R is reflexive (For all a : A, (a, a) belongs to R)
2. R is antisymmetric (For all a, b : A, if (a, b) belongs to R and (b, a) belongs to R then a = b)
3. R is transitive (For all a, b, c : A, if (a, b) belongs to R and (b, c) belongs to R, then (a, c) belongs to R)

#### Monotone function

> Let (A, subset?) be a partially ordered set (poset) ('subset?' is the comparator)
> Let F : A -> A
> F is called a monotone function iff, for all a, b : A, a is a subset of b => F(a) is a subset of F(b)

Basically, bigger input to function leads to bigger output. 

The transform function F discussed above is a monotone function

The functions that comprise F are also all monotone.

### Example

[a1 b1 a2 b2 a3 b3]

[phi {x} {u, v} {v, z} phi {w, x}]

[{u, v} {z} {u,v} {z} {w} {x, y, z}]

We can compare phi and {u, v}

We cannot compare {x} and {z}

But we might run into a pair across these sets that we can compare

##### So we define a new poset on this,

Let V be a finite set of program locations

There is a normal subset ordering between subsets of V

Consider [v1 ... vn], vi is a subset of V

Let A be the set of all vectors of lengths n comprising of elements that are subsets of V (yes we are assuming same length for now)

x-bar [x1 ... xn], xi is a subset of V

y-bar [y1 ... yn], yi is a subset of V

Define the following relation (sqsubset-A)

> x-bar sqsubset-A y-bar, iff

xi is a subset of yi, (A, sqsubset-A) is a poset

Also, F : A -> A

### Now we have,

Monotonic function F
Vector of subsets of program variables A
Equation X' = F(X)
Poset relation on A

Next time, we will solve the set of equations bi = Bi(ai) OR X' = F(X)






