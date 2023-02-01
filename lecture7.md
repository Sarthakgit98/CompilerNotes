# Lecture 7

Example program after liveness analysis

1.  movq $1, v		{rsp}
2.  movq $42, w	{v, rsp}	
3.  movq v, x		{v, w, rsp}
4.  addq $7, x		{w, x, rsp}
5.  movq x, y		{w, x, rsp}
6.  movq x, z		{w, x, y, rsp}
7.  addq w, z		{w, y, z, rsp}
8.  movq y, t		{y, z, rsp}
9.  negq t		{t, z, rsp}
10. movq z, %rax	{t, z, rsp}
11. addq t, %rax	{rax, t, rsp}
12. jmp conclusion	{rax, rsp}

We assume rax will have the final value and rsp will be in use all the time.

### Liveness analysis on this

11.t -> 9.t -> 8.t
8.y -> 5.y
7.z -> 6.z
7.w -> 2.w
5.x -> 4.x -> 3.x
3.v -> 1.v
fin.rax -> 11.rax -> 10.rax

We know we will have {rax, rsp} at the end. We work our way back up.

For any set X, we determine previous set X' by removing the variable being written to and adding the one being read from. If it is an add/sub, we do not do the removal.

{rax, rsp}
..--(12)--> {rax, rsp} 
..--(11)--> {t, rax, rsp} 
..--(10)--> {t, z, rsp}
..--(9)--> {t, z, rsp} 
..--(8)--> {y, z, rsp} 
..--(7)--> {w, y, z, rsp}
..--(6)--> {w, x, y, rsp} 
..--(5)--> {w, x, rsp} 
..--(4)--> {w, x, rsp}
..--(3)--> {v, w, rsp} 
..--(2)--> {v, rsp} 
..--(1)--> {rsp}

### Interference graph

We can build an interference graph using this.

We will add edges incrementally. 

Going backwards in the above graph, we get edges incrementally as

(1)	v-rsp
(2)	w-v, v-rsp
(3)	x-w, x-rsp
(4)	phi
(5)	y-w, y-rsp (We don't add y-x because the instruction is mov x, y. This makes them the same. We COULD add the edge, but it makes the result inefficient.)
(6)	z-w, z-y, z-rsp
(7) 	phi
(8) 	t-z, t-rsp
(9) 	phi
(10) 	rax-t, rax-rsp
(11) 	phi
(12) 	phi

### Colour the above graph

We map each variable to a register using the above graph

Book follows the saturation method. We follow that.





