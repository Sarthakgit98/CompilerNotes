# Compiler

Lang A -> Through compiler -> Lang B
   |			        |
   |				v
   |		            Interpreter B
   |		        	|
   |		        	v
   L-----Interpreter A-----> Answer

Looking at each piece of this diagram

Today: Lang A and interpreter A
Later: h->f->s->l->x

### Convention

h : high level language of arithmetic expressions
f : flat arithmetic expressions + variables + local bindings
s : statement oriented languages with assignments
l : llvm (virtual machine)
x : x86

## Defining Lang A: h

<exp> ::= <int> | <app>
<app> ::= <prim>
<prim> ::= <op> <exp> ...	(Primitive construct)
<op> ::= + | -

OR

<exp> ::= <int> | <app>
<app> ::= prim <op> <exp> ...
<op> ::= + | -

Examples:
> (+ 5 3)
> (+ (- 3 2) (-5))

### AST

> (int? (int 5)) => #t
int: Type Constructor
5: argument
int?: type predicate

In python: type(int(5)) == int

Suppose,
> (check-true (h-ast? (int 5)))
check-true: Verify by running this that a function (h-ast? here) returns true
h-ast?: Will it make a valid AST

> (check-true (h-ast?
		(prim '+ (list
			  (int 5)
			  (int 8)))))
			  
			  
### Type predication and structures

> (struct int (val))
Defining a structure with an integer named val

> (provide (contract-out [struct int ((val integer?))]))

> (struct prim (op rands))

> (define op? (or/c '+ '-))
or/c: Any one of the following/consists of
Defining a type predicate op here

> (define app? prim?)
Defining a type predicate as another type predicate

> (define h-ast? (or/c int? app?))
Defining type predicate with other type predicates

> (check-true (h-ast? (prim '+
				(list
				(prim '- (list (int 7)))
				(int 8)))))
				
> (provide (contract-out [struct prim((op op?) (rands (listof h-ast?)))]))

### h-eval

> (check-equal? 5 (h-eval (int 5)))
h-eval will evaluate (int 5) to 5 and check if it is equal to 5

> (check-equal? 7 (h-eval (prim '+ (list (int 3) (int 4)))))

### map

Takes a function and list and applies function to each element and returns list

> (map add1 '(2 6 1))
'(3 7 2)

Here add1 is a function that adds 1 to a value

> (define \*op-table\* `((+ . ,+) (- . ,-)))

` : Quotes each element
, : Unquotes given element

Same as
> (define \*op-table\* (('+ . +) ('- . -)))

Here, we associate the symbol + with function + and symbol - with function -.

### Cons

>(cons 1 2)
'(1 . 2)

It is essentially a pair. Cons is the constructor for it.

Read up on: match, dict-ref, int-val, apply, args, fun

### Writing an interpreter for h

(define answer? integer?)

(define (h-eval a)
	(match a
		[(int n) n]
		[(prim op as)
		 (let ([args (map h-eval as)]))
		 	[fun (dict-ref *op-table* op)])
		 (apply fun args))]))
		 
(define \*op-table\* (('+ . +) ('- . -)))


