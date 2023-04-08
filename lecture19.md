# Lecture 19

Today: Tuples and garbage collection

In our new language, the exp consists of the following (<fid> is function name)

<exp> :=	<int> | <bool> | <var>
		| (if <exp> <exp> <exp>)
		| (let <var> <exp> <exp>)
		| (<op> <exp> <exp>...)
		| (apply <fid> <exp>...)
		| (begin <exp> <exp>...) [This can be expressed as a sequence of nested lets]
		| (vector <exp> <exp>...)
		| (vector-ref <exp> <int>)
		| (vector-set! <exp> <int> <exp>) [Will return Void]
	
## Tuples

We have only needed stack locations and registers so far. Now we require notion of memory/heap.

Tuples are roughly equivalent to vectors in racket.

Example of how we could use vector,

> (let t1 (vector 5 #t 7)
> 	(let t2 (vector #f t1)
>		(vector-ref t2 0)))

Above code will give #f

Also,

> (let t1 (vector 5 #t 7)
> 	(let t2 (vector #f t1)
>		(vector-set! t2 0 t1)))

We will get new t2 as (vector t1 t1). There are two interpretations, that both t1 are copies at different locations or that both point to a t1 in a singular memory location.

We shall assume the latter.

We don't have a 'free' function because our language will support garbage collection.

Because of the single memory location assumption,

> (let t1 (vector 5 #t 7)
> 	(let t2 (vector #f t1)
>		(begin
>			(vector-set! t2 0 t1)
>			(eq? (vector-ref t2 0) t1))))

Above code will return #t.

### Example

A language like racket has set-bang function which can work as follows,

> (let ([x 5])
>	(begin
>		(set! x 7)
>			x)))
			
Above, the 5 in [x 5] is internally a vector of size 1. Any mutable value is a vector. 

So, if we ever see a set-bang, we can simply replace the relevant variable with a vector of size 1 and all set-bangs with vector-set!'s.

We can either do this dynamically or assume every variable is mutable and swap them with vectors as follows.

```
(let ([x (vector 5)])
	(begin
		(vector-set! x 0 7)
		(vector-ref x 0)))
```

The main point is that identifiers are not allowed to be mutable. Only vectors.

Now, we look at the following code,

```
(let x (vector 5 7)
	(begin
		(vector-set! x 0 #t)
		(vector-ref x 0)))
```

We make an AST, and decorate each node with an environment.

> Env: Id --(fn)--> Val
> Val: Int U Bool U (Vector Val Val...)

Above code will get rejected by typechecker because we changed integer to bool btw.

### Another environment model

> Store: Address -> Val+, Address (Second address is boundary)
> Env: Id -> Val
> Val: Int U Bool U Address




