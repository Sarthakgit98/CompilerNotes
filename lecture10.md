# Lecture 10

L-if continued

### Example

Example1

> (define ex4 5)
```
i	ei		fi	f*i	ki	di	bi
1	$5		1	1	ret	1	[t1:=$5; ret t1]
```

Example 2

> (define e5 '(+ 8 5))
```
i	ei		fi	f*i	ki	di	bi
1	$8		1	1	2	1	[t1:=$8; goto b2]
2	$5		2	2	3	2	[t2:=$5; goto b3]
3	(+ 1 2)		1	1	ret	3	[t3:=(+ t1 t2); ret t3]
```

Example 3

> (define ex6 '(let x 8 9))
```
i	ei		fi	f*i	ki	di	bi
1	x		1	1	2	x	[]
2	$8		2	2	3	x	[t2:=$8; x:=$8; goto b3]
3	$9		3	3	ret	3	[t3:=$9; ret t3]
4	(let 1 2 3)	2	2	ret	4	[t4:=t3; ret t4]

```

Example 4

```
(define ex-eoc-fig4-4.12
	'(let x (read)
		(let y (read)
			(if (if (< x 1)
				(eq? x 0)
				(eq? x 2))
			(+ y 2)
			(+ y 10)))))
```

Make the ast and decorate the nodes in postorder fashion starting from 1.

```
i	ei		fi	f*i	ki	di	bi	br_info
1	x		
2	read				b4	x
3	y
4	read				b5	y
5	x				b6	t5
6	$1
7	(< 5 6)
8	x
9	$0
10	(eq? 8 9)
11	x
12	$2
13	(eq? 11 12)			b21	t14
14	(if 7 10 13)			b21	t14		[10, 13]
15	y				b16	t15
16	$2
17	(+ 15 16)			ret	t21
18	y				b19	t18
19	$10
20	(+ 18 19)			ret	t21
21	(if 14 17 20)			ret	t21
22	(let 3 4 21)			ret	t22
23	(let 1 2 22)			ret	t23
```

























