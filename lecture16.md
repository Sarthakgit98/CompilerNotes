# Lecture 16

Starting functions (Skipping while loops. Tail recursion will use same concepts.)

So far, we had

L-if: Consts, Var, primitive applications, conditionals

Now,

L-fun: Functions, User defined functions + L-if

## Abstract syntax

> For a prim: (+ x y)
> For a function (f x y), we have: (@ f x y)

<pgm> = <f-def> <f-def>... <exp>

The idea is that, before evaluating an expression, we have all function definitions in place

We use letrec to define a series of functions here in L-fun.

<f-def> = (fun <f-name> <formal>... <body>) [<formal> means formal parameters]

So some function f can be defined as,

(define f (x y) (+ x (- y))), parameters: x y, body: (+ x (- y))

Then we can call it inside another function definition g as,

(define g (z) (f z z))

Now we can have an expression,

(let x 5 (f (g 3) 4))

For L-fun AST,

<exp> = <const> | <varref> | <prim> | (if-else <exp> <exp> <exp>) | (let <var> <exp> <exp>)
	| (@ <exp> <exp>...) [first <exp> is function name, remainder are arguments]
<pgm> = <f-def> <f-def>... <exp>
<f-def> = (fun <f-name> <formal>... <body>) [<formal> means formal parameters]
closure: [formals, body, environment]

Each function is bound to a closure. In the environment, closure replaces a value for a variable denoting a function.

### Example

```
(define f (x y) (+ x (- y)))	Env = {f : Cf}, Cf = <(x y) (+ x (- y)) '()>
(define g (z) (f z z))		Env = {g : Cg}, Cg = <(z) (@ f z z) '()>
(let x 5 (f x x))
```

We first define above environments, then create a new env Env-fin = {f : Cf, g : Cg}, and now substitute this into all closures as the third element 'Environment'.

So, we finally get,

```
(define f (x y) (+ x (- y)))	Env-f = {f : Cf}
(define g (z) (f z z))		Env-g = {g : Cg}

Env-fin = {f : Cf, g : Cg}
Cf = <(x y) (+ x (- y)) Env-fin>
Cg = <(z) (@ f z z) Env-fin>

(let x 5 (f x x))
```






