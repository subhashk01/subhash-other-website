---
layout: blog
title:  "Bananagrams is NP-complete"
date:   2022-11-08 21:26:21 -0600
permalink: /bananagrams/
---

Have you ever wondered if [Bananagrams](https://bananagrams.com/) is NP-complete? No? Well I have, and I think it is! 

In this post we'll dive in to an abstract version of the game Bananagrams, prove it is NP-complete, and discuss some future extensions.

# The Bananagrams Decision Problem

We will be interested in a simplified, abstract version of Bananagrams. Assume we are given $$N$$ tiles, where each tile can be any "letter" from an alphabet of unique letters $$V$$. We can represent these tiles with a multiset $$M$$, so $$\lvert M \rvert = N$$. Consider a dictionary $$D$$ of ordered sequences of letters ("words"), where each letter is also from $$V$$. We define a Bananagram decision problem $$B(M, D)$$ to be the following: is it possible to make a two dimensional crossword-style grid that uses all tiles from $$M$$ exactly once, such that each contiguous group of letters in the grid going left to right or top to bottom is in $$D$$?

When you play Bananagrams with your family or friends, you probably sensibly set $$D$$ to be the Oxford English Dictionary or the Scrabble Dictionary or some common sense shared vocabulary that you definitely won't argue over, for sure. The (English version) of the game comes with only the $$26$$ tiles of the English alphabet, so $$V = \{a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z\}$$. During play, you frequently end up with some multiset $$M$$ of tiles that you need to turn into a crossword-style grid, and because the word distribution $$D$$ is pretty friendly the decision problem $$B(M, D)$$ will probably evaluate to "yes". Let's run through a couple of quick examples here to show this clearly. 

Say we have $$M = \{a, a, a, a, c, e, t, t, p\}$$ and $$D$$ is the Scrabble Dictionary. Then $$B(M, D) = yes$$. For example, we can lay out the tiles like so:

<img src="/assets/images/bananagrams.drawio.png" style="display:block; margin-left: auto; margin-right: auto;" width="200">

On the other hand, if we set $$D$$ to be the words in the Scrabble Dictionary that contain the letter $$b$$, then $$B(M, D) = no$$.

One important note: according to the official [Bananagrams rules](https://www.bananagrams-intl.com/instructions.html), "Any available dictionary may be used". Therefore, we do not require any restrictions on $$D$$, $$V$$, or $$M$$ beyond what we have already stated.

# What's NP, NP-Hard, and NP-Complete?
NP, NP-Hard, and NP-Complete are *complexity classes*, which is a fancy way to say
they are groupings of decision problems that share a certain trait. 

NP problems are decision problems that can be verified in polynomial time. That is, we can
write an algorithm that takes a potential solution to the problem and determines whether it is valid or not in time on the order of some polynomial, like $$x^2$$ or $$57x^{99}$$. 

NP-Hard problems are problems that are "as hard" as any other problem in NP. Formally,
if a problem $$M$$ is NP-Hard, then we can *reduce* any problem in NP to
$$M$$ in polynomial time. A "reduction" from decision problem $$A$$ to $$M$$ means 
that we are creating an instance of $$M$$ that has the same yes/no answer as $$A$$. 

Finally, an NP-Complete problem is a problem that is in both NP and is NP-Hard.
One way to prove that a problem is NP-Complete is to to prove that it is in NP
and that some other NP-Hard problem can be reduced to it in polynomial time.
This method implies the problem is in NP-Hard because we then know that we can reduce any
problem in NP to the first NP-Hard problem in polynomial time and then
that instance to our goal problem also in polynomial time. This combination of
polynomial time reductions is itself a polynomial time reduction.

For more information, check out the [excellent wikipedia article](https://en.wikipedia.org/wiki/NP-completeness)
on this subject.

# Exact Cover by 3-Sets ($$X3C$$)
Exact cover by 3-sets is an NP-Complete problem [1] with the following definition (taken verbatim from [1]):
> Given a set $$X$$, with $$\lvert X \rvert = 3q$$ (so, the size of $$X$$ is a multiple of $$3$$), and a collection $$C$$ of $$3$$-element subsets of $$X$$.  Can we find a subset $$C’$$ of $$C$$ where every element of $$X$$ occurs in exactly one member of $$C’$$?  (So, $$C’$$ is an “exact cover” of $$X$$).

Here is an example. Consider

$$X = \{0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11\}$$

and

$$C = \{\{0, 1, 2\}, \{2, 3, 4\}, \{4, 5, 6\}, \{6, 7, 8\}, \{8, 9, 10\}, \{10, 11, 0\}\}$$

Then the answer to this decision problem is $$no$$, because if we pick the only set with $$3$$
then we can't pick the only sets with $$1$$ and $$5$$. On the other hand, if 

$$C = \{\{0, 1, 2\}, \{3, 4, 5\}, \{6, 7, 8\}, \{9, 10, 11\}\}$$

then the answer to the decision problem is $$yes$$, with $$C' = C$$. We will reduce X3C to $$B(M,D)$$ in our proof below.

# Proof that $$B(M, D)$$ is NP-complete

We will now prove that the Bananagrams decision problem $$B(M,D)$$ is NP-Complete.

## Formalizing $$B(M,D)$$
We will first formalize $$B(M,D)$$. WLOG, impose an arbitrary indexing scheme on $$M$$, so $$M = \{t_1, \ldots, t_{\lvert M \vert}\}$$. A potential solution $$S$$ of $$B(M,D)$$ is a placement of each tile $$t_i \in V$$
in $$M$$ on an infinite integer grid, which we formalize by specifying $$\lvert M \rvert$$ unique
integer tuples $$(x_i, y_i)$$, $$x_i, y_i \in \mathbb{Z}$$. Let $$T = \{(x_i, y_i)\}$$, and in a slight abuse of notation let $$T[(x_i, y_i)] = t_i$$. Then $$S$$ is valid iff
1. **The grid is connected.** That is, if we consider the grid as a graph $$G$$, where each tile $$t_i$$ is a node and there is an edge between $$t_i$$ and $$t_j$$ if $$\lvert x_i - x_j \rvert + \lvert y_i - y_j \rvert= 1$$, then $$G$$ is connected.
2. **Every horizontal and vertical word is in $$D$$.** 
For every $$i$$, we define a tuple $$H_i$$ as the "horizontal word" containing tile $$i$$ and a tuple $$V_i$$ as the "vertical word" containing tile $$i$$. Formally, to define $$H_i$$, let $$l$$ be the smallest integer such that $$(k, y_i) \in T$$ for all $$k \in [l, x_i]$$ and let $$r$$ be the largest integer such that $$(k, y_i) \in T$$ for all $$k \in [x_i, r]$$. Then $$H_i$$ is the following tuple:

    $$H_i = \left(T[(l, y_i)], T[(l + 1, y_i)], \ldots, T[(r - 1, y_i)], T[(r, y_i)]\right) $$

    Similarly, to define $$L_i$$, let $$b$$ be the smallest integer such that $$(x_i, b) \in T$$ for all $$k \in [b, y_i]$$ and let $$t$$ be the largest integer such that $$(x_i, t) \in T$$ for all $$k \in [y_i, t]$$. Then $$V_i$$ is the following tuple:

    $$V_i = \left(T[(x_i, b)], T[(x_i, b + 1)], \ldots, T[(x_i, t-1)], T[(x_i, t)]\right) $$

    Recall that $$D = \{W_1, W_2, \ldots, W_k\}$$, where each $$W_i$$ is a tuple of elements of $$V$$. We then require $$V_i \in D$$ if $$\lvert V_i \rvert > 0 $$, and we require $$H_i \in D$$ if $$\lvert H_i \rvert > 0$$, for all $$i \in [1, \ldots, \lvert M \rvert]$$. 

If a valid solution $$S$$ exists, then the answer to $$B(M,D)$$ is $$yes$$; otherwise it is $$no$$.
## $$B(M,D)$$ is in NP

First, we show that $$B(M, D)$$ is in NP. Consider some possible assignment $$S$$, with $$T$$ defined the same way as above. Algorithm $$A$$ that can verify $$S$$ in polynomial time is as follows:
* Verify that no tile is isolated by building a graph $$G$$ as described above and checking if it is connected.
    
* Verify that all words are valid with the following pesudocode:
{% highlight python %}
def verify_words_valid(M, D)
    for t_i in M:
        find H_i and V_j as defined above
        if H_i not in M:
            return false
        if V_i not in M:
            return false
    return true
{% endhighlight %}

These algorithms are clearly polynomial in the sizes of $$M$$ and $$D$$, so $$B(M,D)$$ is in NP.

## $$B(M,D)$$ is NP-Hard

We will prove this by reducing X3C to $$B(M,D)$$. 

### Reduction definition
Let $$Y$$ and $$Z$$ be new symbols not in $$X$$. Define $$R(a, c)$$ to be the multiset containing $$c$$ copies of $$a$$.  Then the first step of our reduction is to let 

$$M = X \cup R(Y, 2\|X\|) \cup R(Z, \|X\| + 1)$$

The next step is to define $$D$$, which we do as follows. Let $$\|C\| = k$$ and $$C_i = \{c_{i_1}, c_{i_2}, c_{i_3}\}$$. Then

$$\begin{aligned}
D_1 &= \{(Z, Z, c_{1_1}, c_{1_2}, c_{1_3}, Z, Z), (Z, Z, c_{2_1}, c_{2_2}, c_{2_3}, Z, Z) \ldots, (Z, Z, c_{k_1}, c_{k_2}, c_{k_3}, Z, Z)\}\\
D_2 &= \{(Y, x, Y) | x \in X\}\\
D_3 &= \{(Y, Y, Y), (Z)\}\\
D &= D_1 \cup D_2 \cup D_3 \end{aligned}$$

### $$X3C$$ is yes -> $$B(M, D)$$ is yes
Assume there is some valid cover $$C' = \{C_{j_1}, \ldots, C_{j_n}\}$$. 
For ease of notation, let $${c_{j_m}}_1 = a_{3m - 2}, {c_{j_m}}_2 = a_{3m - 1}, {c_{j_m}}_3 = a_{3m}$$.
Then we claim the following is a valid solution of the reduced Bananagrams problem:

<img src="/assets/images/bananagrams_big_correct.png" style="display:block; margin-left: auto; margin-right: auto;" width="600">

We will make most of our analysis informally from the image here, but we could formalize it (verbosely) in terms of $$(x_i, y_i)$$ if necessary. Visually, the diagram satisfies property 1, since every tile is connected to another tile. Moreover, property 2 is satisfied because every vertical and horizontal word is valid by our construction of $$D$$ above: $$(Y, Y, Y)$$, $$(Y, a_i, Y)$$, and $$(Z, Z, a_{3j+1}, a_{3j + 2}, a_{3j + 3}, Z, Z)$$ are all in $$D$$. Finally, this construction uses every tile in $$M$$ exactly once: we use each element in $$X$$ because $$C'$$ is a set cover, we use $$2\|X\|$$ copies of $$Y$$ around the elements of $$X$$, and we use $$\|X\| + 1$$ copies of $$Z$$ between words.

### $$X3C$$ is no -> $$B(M, D)$$ is no

First we will prove a few lemmas:

**Lemma 1**: In any valid solution $$S$$, every tile is less than or equal to $$3$$ units away from a $$Z$$.

**Lemma 1**: In any valid solution $$S$$, every $$Y$$ is adjacent to a tile from $$X$$ (some $$a_j$$). To see this, we proceed by contradiction. Say a $$Y$$ was not adjacent to some $$a_j$$. Since no words in $$D$$ have a $$Y$$ next to a $$Z$$, such a $$Y$$ would have to be neighbors only with other $$Y$$s. Take a path on $$G$$ consisting of all $$Y$$s that eventually reaches some $$a_j$$ (guaranteed to be possible since $$G$$ is connected). We now consider the tile $$Y_1$$ that is next to $$a_j$$ and the tile $$Y_2$$ that is next to $$Y_1$$ along the path. An example scenario is depicted below, with the path in purple and the initial arbitrary $$Y$$ not next to any $$a_j$$ in green.

<img src="/assets/images/bananagrams_possible_bad.png" style="display:block; margin-left: auto; margin-right: auto;" width="300">

WLOG, we can assume $$Y_1$$ is above $$a_j$$ (if it is in a different direction, we can just rotate the board and keep the properties of the solution the same). $$Y_2$$ cannot be above $$Y_1$$, since no words with more than one $$Y$$ and an $$a$$ are in $$D$$. Thus, $$Y_2$$ must be left or right of $$a$$, again WLOG let $$Y_2$$ be to the left of $$A$$ (we can mirror the board and keep the properties of the solution the same). 

## Citations

[1] - [Exact Cover by 3-Sets](https://npcomplete.owu.edu/2014/06/10/exact-cover-by-3-sets/)