# Lecture 3

## Lexically scoped variables - Suite of L_var languages (h -> f)

Today: Concrete syntax L0 and parser

L_var: Lexical scope with arithmetic, we will write concrete syntax for it (L-var-h)

<exp> := <atm> | <app> | (let <var> <exp> <exp>)
<atm> := <int> | <var>
<var> := <symbol>
<app> := (+ <exp> <exp>) | (- <exp>) | (- <exp> <exp>)

Example,

> (let x 5 (+ x 3))
> (let x (let y (+ x y) y) (-x y)) 

Note: Latter expression is valid, we are not talking about evaluation yet

### Abstract syntax for L-var-h will be defined with a bunch of structs, which is as follows

> (struct int (val) | #:transparent)

\#:transparent will help us compare structs. Eg, (equal? (struct 5) (struct 5)) will return #t

> (struct var-ref (sym) #:transparent)
> (struct prim (op rands) #:transparent)

rands will be list of operands

> (struct let-ast (b-var b-ast body) #:transparent)


### Contracts on constructors

We define contracts to make sure if we make an ast using above structs, it is a valid ast. List of contracts given in notes.

## Parser

parse: datum? -> ast?
(Datum: concrete syntax)

Example,

(parse 5) => (int 5)

Test case for this,
> (check-equal? (parse 5) ; actual
		(int 5)) ; expected
		
Another example test case
> (check-equal? (parse 'x) (var-ref 'x))

Yet another,
> (check-equal? (parse '(+ 3 2)) (prim 'x (int 3) (int 2)))

For parsing let statements of this of L-var-h,

example, (check-equal? (parse '(let x 5 (+ x 3)) (let-ast 'x (int 5) (prim '+ (list (var-ref 'x) (int 3))))))

### Writing a parser for this

(define (parse e)
	(match e
		[(? integer?) (int e)] ; ? is a racket syntax thing. Specifies a question has to be asked. What question? 'integer?'.
		[(? symbol?) (var-ref e)]
		[(list '+ e1 e2) (prim '+ (list (parse e1) (parse e2)))]
		[(list 'let (? symbol? b-var) b-exp body) (let-ast b-var (parse b-exp) (parse body))]))

Note: integer? and symbol? exist in racket already

## Interpreter

> (+ x 3)

ast + env -> [eval-ast] -> ans?

env are dictionaries with variable to value mappings

(define (eval-ast ast env)
	(match ast
		[(int val) val]
		[(var-ref sym) (lookup-env env sym)]
		[(prim op rands) (apply (lookup-env *op-table* op) (map (lambda (a) (eval-ast a env)) (rands)))]
		[(let-ast b-var b-ast body) (let ([v (eval-ast b-ast env)]) (let ([new-env (extend-env env b-var v)]) (eval-ast body new-env)))]))



