# Is every function computable?

>_"A function of a variable quantity is an analytic expression composed in any way whatsoever of the variable quantity and numbers or constant quantities."_,  Leonhard Euler, 1748.



We saw that NAND programs can compute every finite function.
A natural guess is that NAND++ programs could compute every infinite function.
However, this turns out to be _false_, even for  functions with $0/1$ output.
That is, there exists a function $F:\{0,1\}^* \rightarrow \{0,1\}$ that is  _uncomputable_!
This is actually quite surprising, if you think about it.
Our intuitive notion of a "function" (and the notion most scholars had until the 20th century) is that it defines some implicit or explicit way of computing the output $f(x)$ from the input $x$.^[In the 1800's, with the invention of the  Fourier series and with the systematic study of  continuity and differentiability, people have starting looking at more general kinds of functions, but the modern definition of a function as an arbitrary mapping was not yet universally accepted. For example, in 1899 Poincare wrote "we have seen a mass of bizarre functions which appear to be forced to resemble as little as possible honest functions which serve some purpose. ... they are invented on purpose to show that our ancestor's reasoning was at fault, and we shall never get anything more than that out of them".]


> # {.theorem title="Uncomputable functions" #uncomputable-func}
There exists a function $F^*:\{0,1\}^* \rightarrow \{0,1\}$ that is not computable by any NAND++ program.

> # {.proof data-ref="uncomputable-func"}
The proof is illustrated in [diagonal-fig](){.ref}.
We start by defining the following function $G:\{0,1\}^* \rightarrow \{0,1\}$:
>
For every string $x\in\bits^*$, if $x$ satisfies __(1)__ $x$ is a valid representation of a NAND++ program $P_x$ and __(2)__ when the program $P_x$ is executed on the input $x$ it  halts and  produces an output,  then we define $G(x)$ as the first  bit of this output.  Otherwise (i.e., if $x$ is not a valid representation of a program, or the program $P_x$  never halts on $x$)  we define $G(x)=0$.
We define $F^*(x) := 1 - G(x)$.
>
We claim that there is no NAND++ program that computes $F^*$.
Indeed, suppose, towards the sake of contradiction,  that there was some program $P$ that computed $F^*$, and let $x$ be the binary string that represents the program $P$.
Then on input $x$, the program $P$ outputs $F^*(x)$.
But by definition, the program should also output $1-F^*(x)$, hence yielding a contradiction.

![We construct an uncomputable function by defining for every two strings $x,y$ the value $1-P_y(x)$ which equals to $0$ if the program described by $y$ outputs $1$ on $x$, and equals to $1$ otherwise.  We then define $F^*(x)$ to be the "diagonal" of this table, namely $F^*(x)=1-P_x(x)$ for every $x$. The function $F^*$ is uncomputable, because if it was computable by some program whose string description is $x^*$ then we would get that $P_{x^*}(x^*)=F(x^*)=1-P_{x^*}(x^*)$.](../figure/diagonal_proof.png){#diagonal-fig .class width=300px height=300px}


The proof of [uncomputable-func](){.ref} is short but subtle, and it crucially uses the dual view of a program as both instructions for computation, as well as a string that can be an input for this computation.
I suggest that you pause here and go back to read it again and think about it - this is a proof that  is worth reading at least twice if not three or four times.
It is not often the case that a few lines of mathematical reasoning establish a  deeply profound fact - that there are problems we simply _cannot_ solve and the "firm conviction" that Hilbert alluded to above is simply false.
The type of argument used to prove [uncomputable-func](){.ref} is known as  _diagonalization_ since it can be described as defining a function based on the diagonal entries of a table as in [diagonal-fig](){.ref}.



## The Halting problem

[uncomputable-func](){.ref} shows that there is _some_ function that cannot be computed.
But is this function the equivalent of the "tree that falls in the forest with no one hearing it"?
That is, perhaps it is a function that no one actually _wants_ to compute.  
It turns out that there are natural uncomputable functions:

> # {.theorem title="Uncomputability of Halting function" #halt-thm}
Let $HALT:\{0,1\}^* \rightarrow \{0,1\}$ be the function such that $HALT(P,x)=1$ if the program $P$ halts on input $x$ and equals to $0$ if it does not.
Then $HALT$ is not computable.

Before turning to prove [halt-thm](){.ref}, we note that $HALT$ is a very natural function to want to compute.
For example, one can think of $HALT$ as a special case of the task of managing an "App store".
That is,  given the code of some application, the gatekeeper for the store needs  to decide if this code  is safe enough to allow in the store or not.
At a minimum, it seems that we should verify that the code would not go into an infinite loop.



![We prove that $HALT$ is uncomputable using a _reduction_ to the uncomputability of the function $F^*$. We assume that we had an algorithm that computes $HALT$ and use that to obtain an algorithm that computes $F^*$.](../figure/halt-reduction.png){#halt-fig .class width=300px height=300px}

> # {.proof data-ref="halt-thm"}
The proof is by a _reduction_ to [uncomputable-func](){.ref} , as illustrated in [halt-fig](){.ref}.
That is, we will assume, towards a contradiction, that there is NAND++ program $P^*$ that can compute the $HALT$ function, and use that to derive that there is some NAND++ program $Q^*$ that computes the function  $F^*$ defined above, contradicting [uncomputable-func](){.ref}.
>   
Indeed, suppose that  $P^*$ was a NAND++ program that computes $HALT$.
Then we can write a NAND++ program $Q^*$ that does the following on input $x\in \{0,1\}^*$:
>
1. Compute $z=P^*(x,x)$ \
2. If $z=0$ then output $1$. \
3. Otherwise, if $z=1$ then let $y$ be the first bit of $EVAL(x,x)$ (i.e., evaluate the program described by $x$ on the input $x$). If $y=1$ then output $0$. Otherwise output $1$.
>
__Claim:__ For every $x\in \{0,1\}^*$, if  $P^*(x,x)=HALT(x,x)$ then  the program $Q^*(x)=F^*(x)$ where $F^*$ is the function from the proof of [uncomputable-func](){.ref}.
>
Note that the claim immediately implies that our assumption that $P^*$ computes $HALT$ contradicts  [uncomputable-func](){.ref}, where we proved that the function $F^*$ is uncomputable.
Hence the claim is sufficient to prove the theorem.    
>
__Proof of claim:__: Let $x$ be any string.
If the program described by $x$ halts on input $x$ and its first output bit is  $1$ then $F^*(x)=0$ and the output $Q^*(x)$ will also equal $0$ since $z=HALT(x,x)=1$, and hence in step 3 the program $Q^*$ will run in a finite number of steps (since the program described by $x$ halts on $x$), obtain the value $y=1$ and output $0$.
>
Otherwise, there are two cases.
Either the program described by $x$ does not halt on $x$, in which case $z=0$ and $Q^*(x)=1=F^*(x)$.
Or the program halts but its first output bit is not $1$.
In this case $z=1$ but the value $y$ computed by $Q^*(x)$ is not $1$ and so $Q^*(x)=1=F^*(x)$.

### Is the Halting problem really hard?

Many people's first instinct when they see the proof of [halt-thm](){.ref} is to not believe it.
That is, most people  do  believe the mathematical statement, but intuitively it doesn't seem that the Halting problem is really that hard.
After all, being uncomputable only means that $HALT$ cannot be computed by a NAND++ program.
But  programmers seem to solve $HALT$ all the time by informally or formally arguing that their programs halt.
While it does occasionally happen that a program unexpectedly enters an infinite loop, is there really no way to solve the halting problem?
Some people argue that _they_ can, if they think hard enough, determine whether any concrete program that they are given will halt or not.
Some have even [argued](https://en.wikipedia.org/wiki/Roger_Penrose#Physics_and_consciousness) that humans in general have the ability to do that, and hence humans have inherently superior intelligence to computers or anything else modeled by NAND++ programs (aka Turing machines).^[This argument has also  been connected to the issues of consciousness and free will. I am not completely sure of its relevance  but perhaps the reasoning is  that humans have the ability to solve the halting problem but they exercise their free will and consciousness by choosing not to do so.]


The best answer we have so far is that there  truly is no way to solve $HALT$, whether using Macs, PCs, quantum computers, humans,  or any other combination of mechanical and biological devices.
Indeed this assertion is the content of the Church-Turing Thesis.
This of course does not mean that for _every_ possible  program $P$, it is hard to decide if $P$ enter an infinite loop.
Some programs don't even have loops at all (and hence trivially halt), and  there are   many other far less trivial examples of programs that we can certify to never enter an infinite loop  (or programs that we know for sure that _will_ enter such a loop).
However, there is no  _general procedure_ that would determine for an _arbitrary_ program $P$ whether it halts or not.
Moreover, there are some very simple programs for which it not known whether they halt or not.
For example, the following Python program will  halt if and only if [Goldbach's conjecture](https://en.wikipedia.org/wiki/Goldbach%27s_conjecture) is false:



~~~~ { .python .numberLines }
def isprime(p):
    return all(p % i  for i in range(2,p-1))

def Goldbach(n):
    return any( (isprime(p) and isprime(n-p))
           for p in range(2,n-1))

n = 4
while True:
    if not Goldbach(n): break
    n+= 2
~~~~

Given that Goldbach's Conjecture has been open since 1742, it is unclear that humans have any magical ability to say whether this (or other similar programs) will halt or not.

![[XKCD](https://xkcd.com/1266/)'s take on solving the Halting problem, using the principle that "in the long run, we'll all be dead".](../figure/halting_problem_2x.png){#xkcdhaltingfig .class width=300px height=300px}


### Reductions

The Halting problem turns out to be a linchpin of uncomputability, in the sense that [halt-thm](){.ref} has been used to show the uncomputability of a great many interesting functions.
We will see several  examples in such results in the lecture and the exercises, but there are many more such results in the literature (see [haltreductions](){.ref}).


The idea behind such uncomputability results is conceptually simple but can  at first be quite confusing.
If we know that $HALT$ is uncomputable, and we want to show that some other function $BLAH$ is uncomputable, then we can do so via a _contrapositive_ argument (i.e., proof by contradiction).
That is, we show that _if_ we had a NAND++ program that computes $BLAH$ _then_ we could have a NAND++ program that computes $HALT$.
(Indeed, this is exactly how we showed that $HALT$ itself is uncomputable, by reducing to the uncomputability of the function $F^*$ from [uncomputable-func](){.ref}.)

For example, to prove that $BLAH$ is uncomputable,  we could show that there is a  computable function $R:\{0,1\}^* \rightarrow \{0,1\}^*$ such that for every $x\in \{0,1\}^*$, $HALT(x)=BLAH(R(x))$.
Such a function is known as a _reduction_.
The confusing part about reductions is that we are assuming something we _believe_ is false (that $BLAH$ has an algorithm) to derive something that we _know_ is false (that $HALT$ has an algorithm).
For this reason Michael Sipser described such results as having the form "If pigs could whistle then horses could fly".

At the end of the day this is just like any other proof by contradiction, but the fact that it involves hypothetical algorithms that don't really exist tends to make it quite confusing.
The one silver lining is that at the end of the day the notion of reductions is mathematically quite simple, and so it's not that bad even if you have to go back to first principles every time you need to remember what is the direction that a reduction should go in.
(If this discussion itself is confusing, feel free to ignore it; it might become clearer after you see an example of a reduction such as the proof of [spec-thm](){.ref}.)




![Some of the functions that have been proven uncomputable. An arrow from problem X to problem Y means that the proof that Y is uncomputable follows by reducing computing X to computing Y.  Black arrows correspond to proofs that are shown in this and the next lecture while pink arrows correspond to proofs that are known but not shown here. There are  many other functions that have been shown uncomputable via a reduction from the Halting function $HALT$. ](../figure/reductions_from_halting.png){#haltreductions .class width=300px height=300px}


## Impossibility of general software verification


The uncomputability of the Halting problem turns out to be a special case of a much more general phenomenon.
Namely, that _we cannot certify semantic properties of general purpose programs_.
"Semantic properties" mean properties of the function that the program computes, as opposed to properties that depend on the particular syntax.
For example, we can easily check whether or not  a given C program contains no comments, or whether all function names begin with an upper case letter.
As we've seen, we cannot check whether a given program enters into an infinite loop or not.
But we could still hope to check some other properties of the program, for example verifying that if it _does_ halt then it will conform with some specification.
Alas, this turns out to be not the case.


We start by proving a simple generalization of the Halting problem:

> # {.theorem title="Halting without input" #haltonzero-thm}
Let $HALTONZERO:\{0,1\}^* \rightarrow\{0,1\}$ be the function that on input $P\in \{0,1\}^*$, maps $P$ to $1$ if and only if the NAND++ program represented by $P$ halts when supplied the single bit $0$ as input.
Then $HALTONZERO$ is uncomputable.

The proof of [haltonzero-thm](){.ref} is below, but before reading it you might want to pause for a couple of minutes and think how you would prove it yourself.
This is an excellent way to get some initial comfort with the notion of proofs by reduction.


> # {.proof data-ref="haltonzero-thm"}
The proof is by reduction to $HALT$. Suppose, towards the sake of contradiction, that there was an algorithm $A$ such that $A(P')=HALTONZERO(P)$ for every $P'\in \{0,1\}^*$. Then, we will construct an algorithm $B$ that solves $HALT$.
On input a program $P$ and some input $x$, the algorithm $B$ will construct a program $P'$ such that $P'(0)=P(x)$ for every $x$, and then feed this to $A$, returning $A(P')$. Constructing such a program is very simple: add $|x|$ lines in the beginning of $P'$ of the form `myx_`$\expr{i}$` := ` $\expr{x_i}$ for every $i < |x|$. Then add the program $P$, but change any reference to `x_`$\expr{i}$ to `myx_`$\expr{i}$.
Clearly, regardless of its input, $P'$ always behaves like $P$ on input $x$.
In particular $P'$ will halt on the input $0$ if and only if $P$ halts on the input $x$.
Thus if $A(P')=HALTONZERO(P')$ for every $P'$ then $B(P,x)=HALT(P,x)$ for every $P,x$, contradicting the uncomputability of $HALT$.


Once we show the uncomputability of $HALTONZERO$ we can extend to various other natural functions:

> # {.theorem title="Computing all zero function" #allzero-thm}
Let $ZEROFUNC:\{0,1\}^* \rightarrow \{0,1\}$ be the function that on input $P\in \{0,1\}^*$, maps $P$ to $1$ if and only if the NAND++ program represented by $P$ outputs $0$ on every input $x\in \{0,1\}^*$. Then $ZEROFUNC$ is uncomputable.

> # {.proof data-ref="allzero-thm"}
The proof is by reduction to $HALTONZERO$. Suppose, towards the sake of contradiction, that there was an algorithm $A$ such that $A(P')=ZEROFUNC(P')$ for every $P'\in \{0,1\}^*$. Then we will construct an algorithm $B$ that solves $HALTONZERO$.
Given a program $P$, Algorithm $B$ will construct the following program $P'$: on input $x\in \{0,1\}^*$, $P'$ will first run $P(0)$, and then output $0$.
>
Now if $P$ halts on $0$ then $P'(x)=0$ for every $x$, but if $P$ does not halt on $0$ then $P'$ will never halt on every input and in particular will not compute $ZEROFUNC$. Hence, $ZEROFUNC(P')=1$ if and only if $HALTONZERO(P)=1$. Thus if we  define algorithm  $B$ as $B(P)=A(P')$ (where a program $P$ is mapped to $P'$ as above) then we see that if $A$ computes $ZEROFUNC$ then $B$ computes $HALTONZERO$, contradicting [haltonzero-thm](){.ref} .

We can simply prove the following:

> # {.theorem title="Uncomputability of verifying parity" #spec-thm}
The following function is uncomputable
$$
COMPUTES\text{-}PARITY(P) = \begin{cases} 1 & P \text{ computes the parity function } \\ 0 & \text{otherwise} \end{cases}
$$

We leave the proof of [spec-thm](){.ref} as an exercise.


### Rice's Theorem

[spec-thm](){.ref} can be generalized far beyond the parity function
and in fact it rules out  verifying any type of semantic specification on programs.
Define a _semantic specification_ on programs to be some property that does not depend on the code of the program but just on the function that the program computes.
More formally, for a   subset  $\mathcal{S}$ of the functions from $\{0,1\}^*$ to $\{0,1\}^*$, define the function $COMPUTES\text{-}\mathcal{S}:\{0,1\}^*\rightarrow \{0,1\}$ as follows: if $P\in \{0,1\}^*$ is a description of a NAND++ program that computes some $F\in \mathcal{S}$ then $COMPUTES\text{-}\mathcal{S}(P)=1$ and otherwise $COMPUTES\text{-}\mathcal{S}(P)=0$.
The following theorem shows that $COMPUTES\text{-}\mathcal{S}$ is either trivial or non-computable:



> # {.theorem title="Rice's Theorem (slightly restricted version)" #rice-thm}
Say that $\mathcal{S}$ as above is _trivial_ if either there is no computable function $F\in\mathcal{S}$ (and hence $COMPUTES\text{-}\mathcal{S}$ is identically $0$) or every computable function belongs to $\mathcal{S}$ (and hence $COMPUTES\text{-}\mathcal{S}$ is identically $1$).
If $\mathcal{S}$ is not trivial then $COMPUTES\text{-}\mathcal{S}$ is uncomputable.

The proof of [rice-thm](){.ref} follows by generalizing the proof of [spec-thm](){.ref} and we leave it to the reader as [rice-ex](){.ref}.

## Restricted vs unrestricted computational models


We have mentioned before that many natural computational models turn out to be _equivalent_ to one another, in the sense that we can transform a "program" of that other model (such as a $\lambda$ expression, or a game-of-life configurations) into a NAND++ program.
This equivalence implies that we can translate the uncomputability of the Halting problem for NAND++ programs into uncomputability for Halting in other models.
For example:

> # {.theorem title="Turing Machine Halting" #halt-tm}
Let $TMHALT:\{0,1\}^* \rightarrow \{0,1\}$ be the function that on input  strings $M\in\{0,1\}^*$ and $x\in \{0,1\}^*$ outputs $1$ if the Turing machine described by $M$ halts on the input $x$ and outputs $0$ otherwise. Then $TMHALT$ is uncomputable.

> # {.proof }
We have seen that for every NAND++ program $P$ there is an equivalent Turing machine $M(P)$ such that for every $x$, $M(P)$ halts on $x$ if and only $P$ halts on $x$ (and moreover if they both halt, they produce the same output).
The transformation of $P$ to $M(P)$ was completely algorithmic and hence it can be thought of as a computable function $M:\{0,1\}^* \rightarrow \{0,1\}^*$.
We see that $HALT(P,x)=TMHALT(M(P),x)$ and hence if we assume (towards the sake of a contradiction) that $TMHALT$ is computable then we get that $HALT$ is computable, hence contradicting [halt-thm](){.ref}.

The same proof carries over to other computational models such as the _$\lambda$ calculus_, _two dimensional_ (or even one-dimensional) _automata_ etc... Hence for example, there is no algorithm to decide if a $\lambda$ expression evaluates the identity function, and no algorithm to decide whether an initial configuration of the game of life will result in eventually coloring the cell $(0,0)$ black or not.

__Restricted  computational models.__
The uncomputability of  halting and other semantic specification problems motivates coming up with _restricted_ computational models that are __(a)__ powerful enough to capture a set of functions useful for certain application but __(b)__ weak enough that we can still solve semantic specification problems on them.
Here are some examples:





TO BE COMPLETED


Another example of uncomputable functions arises from the notions of _grammars_.
The idea of a grammar is best illustrated by an example.
Consider the set of all valid arithmetical expressions involving natural numbers, and the operators $+,-,\times,\div$.
We can describe this set recursively as follows:

$$
digit := 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9
$$

$$
number := digit | digit number
$$

$$
expression := number | (expression + expression) | (expression - expression) | (expression \times expression) | (expression \div expression)
$$

A valid expression is any string in $\Sigma = \{ 0,1,2,3,4,5,6,7,8,9,0,(,),+,-,\times,\div \}^*$ that can be obtained by repeatedly applying some of these rules to the initial symbol $expression$ until we remain a string that does not contain $number$,$digit$ or $expression$ but  only symbols in $\Sigma$ (and hence cannot be reduced further).

^[TODO: maybe add here more example of context-sensitive grammar and then a proof that grammars are undecidable if there is in fact a simple proof of this (maybe based on lambda calculus?).]

More generally, a _grammar_ over an alphabet $\Sigma$ consists of a set of pairs of strings $(\alpha,\beta)$ where $\alpha,\beta \in (\Sigma \cup N)^*$ and $N$ is some finite set disjoint from $\Sigma$ containing a special symbol we call $start$.
$\Sigma$ is known as the set of _terminals_ and $N$ as the set of _non terminals_.

A grammar defines a subset $L \subseteq \Sigma^*$ (known as a _language_) as follows:
$x$ is in $\Sigma$ if and only if there exists some finite sequence of rules such that if we start from the string $start$ and each time replace a substring of the current string $\alpha$ with the corresponding righthand side of the rule $\beta$, then we eventually end up with $x$.







## Uncountable sets

The diagonalization argument is useful outside the realm of computation.
One of its most striking applications (one that preceded and inspired the results of Gödel and  Turing discussed in this lecture and the next) is Cantor's proof that there is a hierarchy of types of infinity.
What  can something like that mean?

We know that there are infinitely many natural numbers, and that there are also infinitely many even natural numbers.
There seem to be "more" natural numbers than natural even numbers, but Cantor said that in fact the size of these two sets is the same.
The reason is that there is a one-to-one map from the natural numbers $\N$ to the set $EVEN$ of even natural numbers, namely the map $f(x)=2x$.
Similarly, while we may think that there set $\N^2$ of pairs of natural numbers is larger than the set of natural numbers, it turns out that we can consider them as having the same size as well, since there is a one-to-one map $PAIR:\N^2 \rightarrow \N$  (there are many ways to define such a map including $PAIR(x,y)=2^x3^y$; we've use such a  map in embedding two-dimensional arrays in one-dimensional arrays).

Cantor defined two sets $A$ and $B$ to have the _same cardinality_ if there is a one-to-one map $f:A \rightarrow B$ and a one-to-one map $g:B \rightarrow A$.
One can verify that if $A$ and $B$ are finite then they have the same cardinality under this definition if and only if they have the same number of elements (can you see why?).
Cantor denoted the cardinality of the set $\N$ of natural numbers by $\aleph_0$  and sets with such cardinality are called _countable_.

A natural question is whether there is an _uncountable_ infinite set. That is, a infinite set $S$ that is _bigger_ than the natural numbers, in the sense that there is no one-to-one function from $S$ to $\N$.
It turns out that the answer is _yes_:

> # {.theorem title="Cantor's Theorem" #realsuncountable}
The set $\mathbb{R}$ of real numbers is uncountable.

> # {.proof data-ref="readlsuncountable"}
To prove the theorem we need to show that there is no one-to-one map from $\mathbb{R}$ to $\N$. In fact, we will show the stronger statement that there is no one-one-map from $S$ to $\N$ where $S$ is some subset of $\mathbb{R}$ (and even of the interval $[0,1]$).
We have already shown this, using different language, in [cantor-ex](){.ref}.
We will now repeat an outline of the argument.
>
We let $S$ be the set of numbers in $[0,1]$ whose decimal expansion is $x=0.x_1x_2x_3\cdots$ where $x_i \in \{0,1\}$.
Note that every $x\in S$ has a unique decimal expansion involving only the numbers $0$ and $1$. (By restricting to $S$ we avoid technicalities such as the equivalence of $0.0999999\ldots$ and $0.1$.)
Suppose for the sake of contradiction that there is a one-to-one map $f:S \rightarrow\N$.
Then there is an onto map $g:\N \rightarrow S$.
We can use diagonalization to obtain a contradiction by constructing a number $x^* \in S$ such that $g(n) \neq x^*$ for every $n\in\N$.
Write the decimal expansion of  $x^* = 0.x^*_1x^*_2x^*_3\cdots$.
We will let $x^*_n$ equal $1$ if the $n^{th}$ digit following the decimal point of $g(n)$ equals $0$ and will define $x^*_n=0$ otherwise.
Now we can see that the $n^{th}$ digit of $g(n)$ differs from $x^*_n$ and hence $g(n) \neq x^*$ for every $n\in\N$, thus contradicting our assumption that $g$ is onto and completing the proof.

Cantor denoted the cardinality of the real numbers by $\aleph$.
It turns out that there are sets with even larger cardinality than $\aleph$, and sets with even greater cardinality as well. These levels of inifinity is sometimes known as
[transfinite ordinals](https://en.wikipedia.org/wiki/Transfinite_number).
Cantor conjectured that there is no set with cardinality strictly between $\aleph_0$ and $\aleph$.
This is known as the [Continuum Hypothesis](https://en.wikipedia.org/wiki/Continuum_hypothesis) and was presented by Hilbert in 1900 as the first in his list  of the most important open questions in mathematics.
In a twist of fate, using  techniques originating from the works  Gödel and Turing,  Paul Cohen showed in 1963 that the Continuum Hypothesis is independent of the axioms of set theory, which means that neither it nor its negation is provable from these axioms and hence in some sense  can be considered as "neither true nor false".






## Lecture summary

* Unlike the finite case, there are actually functions that are _iherenently uncomputable_ in the sense that they cannot be computed by _any_ NAND++ program.

* These include not only some "degenerate" or "esoteric" functions but also functions that people have deeply cared about and conjectured that could be computed.

* If the Church-Turing thesis holds then a function $F$ that is uncomputable according to our definition cannot  be computed by any finite means.

## Exercises

> # {.exercise title="Halting problem" #halting-alt-ex}
Give an alternative, more direct, proof for the uncomputability of the Halting problem.
Let us define $H:\{0,1\}^* \rightarrow \{0,1\}$ to be the function such that $H(P)=1$ if, when we interpret $P$ as a program, then $H(P)$ equals $1$ if  $P(P)$ halts (i.e., invoke $P$ on its own string representation) and $H(P)$ equals $0$ otherwise.
Prove that  there no  program $P^*$ that computes $H$, by building from such a supposed $P^*$ a program $Q$ such  that, under the assumption that $P^*$ computes $H$, $Q(Q)$ halts if and only if it does not halt.^[__Hint:__ See Christopher Strachey's letter in the biographical notes.]





> # {.exercise title="Rice's Theorem (slightly restricted form)" #rice-ex}
1. Generalize the result that $COMPUTES\text{-}PARITY$ is uncomputable as follows. Prove that for every computable function $F:\{0,1\}^* \rightarrow \{0,1\}^*$, the function $COMPUTES\text{-}F$ which on input a NAND++ program $P$, outputs $1$ iff it holds that $P(x)=F(x)$ for every $x$, is uncomputable. \
2. Generalize this even further to show that for  every nontrivial (neither emptry nor the entire set) subset $\mathcal{S}$ of the set $\mathbb{R}$ of the  computable functions from $\{0,1\}^*$ to $\{0,1\}^*$, the function $COMPUTES\text{-}\mathcal{S}$ that outputs $1$ on a program $P$ if and only if $P$ computes some function in $\mathcal{S}$, is uncomputable.^[__Hint:__ Pick some $F\in\mathcal{S}$ and for every Turing machine $Q$ and input $x$, construct a machine $P_{Q,x}$ that either computes $F$ or computes nothing, based on whether $Q$ halts on $x$.]



## Bibliographical notes

^[TODO:  Add letter of Christopher Strachey to the editor of The Computer Journal.
Explain right order of historical achievements.
Talk about intuitionistic, logicist, and formalist approaches for the foudnations of mathematics.
Perhaps analogy to veganism.
State the full Rice's Theorem and say that it follows from the same proof as in the exercise.]

## Further explorations

Some topics related to this lecture that might be accessible to advanced students include: (to be completed)


## Acknowledgements
