# Lecture 8

We now move on to L-if and C-if

Today we do

L-if: Syntax, interpreter and typechecker
C-if: Syntax

Later, L-if -> C-if (Explicate control)

After that, Xvar Liveness analysis/reg alloc

### Bool

it is #t or #f

New abstract syntax for L-if is roughly,

<exp> ::= <const> | <var> | <let> | <prim-app> | (and <exp> <exp>) | (or <exp> <exp>) | (not <exp>) | (if <exp> <exp> <exp>)
<const> ::= <int> | <bool>
<int> ::= integer?
<bool> ::= #t | #f
<var> ::= symbol?
<prim-app> ::= (+ <exp> <exp>) | (- <exp> <exp>) | (- <exp>) | (<rel-op> <exp> <exp>)
<rel-op> ::= eq? | < | <= | > | >=
<let> ::= (let ([<var> <exp>]) <body>)

<type> ::= bool | int

Example,

> (if (> 3 5) 7 8) => 8

We will assume that the types of the last two expressions in the if statement are same.

We want to parse to make sure syntax tree is valid, then run a type checker to see if the data types are all valid.

By all means, (+ #t 3) should fail at the type checker phase even though it parses correctly as a syntax tree.

Note: There is no implicit type conversion from bool to int in the language we will build. We won't change #f to 0. But any non bool value can be implicitly converted to #t by default.

## Type checker

Given a type-env + ast, typechecker gives an ans.

ans: <type> | <err>

Examples,

> Grammer {}, 5 => int
> Grammer {x: int}, (+ 2 x) => (int int int)

Here,	+: {int, int} => int
	2: => int
	x: Grammer {x: int} => int

If we had,
> Grammer {x: bool}, (+ 2 x) => Err

Here,	+: {int, bool} => Err
	2: => int
	x: Grammer {x: bool} => bool
	
### Type checker rules for operators

The below rules assume that all subtree typechecks give valid outputs

+: {int, int} => int
-: {int, int} => int
-: {int} => int
<, >, <=, >=: {int, int} => bool
eq?: {<type>, <type>} => bool
and, or: {bool, bool} => bool (Implicitly convert int to bool)
not: {bool} => bool
if: {bool, <type>, <type>} => <type> (both types must be same)

For let, Suppose I had some variable e1: tau (e1 has type tau).
If I take this and assign this to x, we end up with x: tau.
If body is e2: tau2

Under above assumptions,

(let x e1 e2) => tau2

Therefore,
let: {something <type1> <type2>} => {<type1> <type1> <type2>} => {<type2>}

Example, (let x 5 (+ x 3)) => {int int {+ int int}} => {int int int} => int

### Algo for let

For (let var ast body),

add var: {(typeof ast)} to type-env of body. Return (typeof body) as (typeof let).

