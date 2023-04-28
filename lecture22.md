# Lecture 22

Today: Lexically scoped functions

Refer to chapter 8 of EOC for this topic.

We essentially want to convert L-lambda to L-fun by creating top level function equivalents of all lambdas. We also provide closures as arguments for all these functions, which contain all free variable assignments.

### Example

```
(define (f x)
	(lambda (y)
		(+ x y)))

(f 5)
```

Above code is legal.

```
(define (f x)
	(g (+ x 1))

(define (g y)
	(* 2 y))
```

Order of top level functions is not important.

```
1 (let 
2 	([f (lambda x 
3 		(lambda y 
4 			(+ x y)))]) 
5 	(f 5))
```

### EOC Figure 8.7 Pg 154 Simplified

```
(define (f x)
	(let ([y 4])
		(lambda (z)
			(+ x y z))))
			
(define (main)
	(let ([g (f 5)])
		(let ([h (f 3)])
			(+ (g 11) (h 15)))))
```

Conversion to L-fun roughly gives us

```
(define (f clo x)
	(let ([y 4])
		(closure 1 (list (fun-ref lambda2 1) x y))))

(define (lambda2 clo z)
	(let ([x (vector-ref clo 1)])
		(let ([y (vector-ref clo 2)])
			(+ x y z))))
			
(define (main)
	(let ([g (let ([clos1 (closure 1 (list (fun-ref f 1)))])
		((vector-ref clos1 0) clos1 5))) <----- Contains closure returned by f
		(let ([h (let [(clos2 (closure 1 (list (fun-ref f 1))))]
				((vector-ref clos2 0) clos2 3))) <----- Contains closure returned by f
			(+ ((vector-ref g 0) g 11) ((vector-ref h 0) h 15))))) <----- Each one is a call to lambda2 as per closures returned by f, but with different free vars and z (11 and 15)
```

Abstract syntax of closure:

> (closure arity (list (Funref name fun-arity) free-vars))

The closure can be thought of as an object that carries the method to be called and the list of values for its free variables.

Essentially, instead of calling functions directly, now we generate closures and call functions through them. When returning lambdas, we are only returning closures and perhaps using them to call the lambda function later.














```

