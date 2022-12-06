---
layout: blog
title:  "Bananagrams is NP-complete"
date:   2022-11-08 21:26:21 -0600
permalink: /bananagrams/
---

Have you ever wondered if [Bananagrams](https://bananagrams.com/) is NP-complete? No? Well I think it is, and I'll prove it! 

In this post we'll dive in to an abstract version of the game Bananagrams, prove it is NP-complete, and discuss some future extensions.

# The Bananagrams Decision Problem

We will be interested in a simplified, abstract version of Bananagrams. Assume we are given $$N$$ tiles, where each tile can be any "letter" from an alphabet of unique letters $$V$$. We can represent these tiles with a multiset $$M$$, so $$\lvert M \rvert = N$$. Consider a dictionary $$D$$ of ordered sequences of letters ("words"), where each letter is also from $$V$$. We define a Bananagram decision problem $$B(M, D)$$ to be the following: is it possible to make a two dimensional crossword-style grid that uses all tiles from $$M$$ exactly once, such that each contiguous group of letters in the grid going left to right or top to bottom is in $$D$$?

When you play Bananagrams with your family or friends, you probably sensibly set $$D$$ to be the Oxford English Dictionary or the Scrabble Dictionary or some common sense shared vocabulary that you definitely won't argue over, for sure. The (English version) of the game comes with only the $$26$$ tiles of the English alphabet, so $$V = \{a, b, c, d, \ldots, x, y, z\}$$. During play, you frequently end up with some multiset $$M$$ of tiles that you need to turn into a crossword-style grid, and because the word distribution $$D$$ is pretty friendly the decision problem $$B(M, D)$$ will probably evaluate to "yes". Let's run through a couple of quick examples here to show this clearly. 

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

$$\begin{align*}C = \{&\{0, 1, 2\}, \{2, 3, 4\} \\
    &\{2, 3, 4\}, \{4, 5, 6\}, \\
    &\{6, 7, 8\}, \{8, 9, 10\}, \\
    &\{10, 11, 0\}\}\end{align*}$$

Then the answer to this decision problem is $$no$$, because if we pick the only set with $$3$$
then we can't pick the only sets with $$1$$ and $$5$$. On the other hand, if 

$$C = \{\{0, 1, 2\}, \{3, 4, 5\}, \{6, 7, 8\}, \{9, 10, 11\}\}$$

then the answer to the decision problem is $$yes$$, with $$C' = C$$. We will reduce X3C to $$B(M,D)$$ in our proof below.


# Proof that $$B(M, D)$$ is NP-complete

We will now prove that the Bananagrams decision problem $$B(M,D)$$ is NP-Complete.

## Formalizing $$B(M,D)$$
We will first formalize $$B(M,D)$$. WLOG, impose an arbitrary indexing scheme on $$M$$, so $$M = \{t_1, \ldots, t_{\lvert M \vert}\}$$. A potential solution $$S$$ of $$B(M,D)$$ is a placement of each tile $$t_i \in V$$
in $$M$$ on an infinite integer grid, which we formalize by specifying $$\lvert M \rvert$$ unique
integer tuples $$(x_i, y_i)$$, $$x_i, y_i \in \mathbb{z}$$. Let $$T = \{(x_i, y_i)\}$$, and in a slight abuse of notation let $$T[(x_i, y_i)] = t_i$$. Then $$S$$ is valid iff
1. **The grid is connected.** That is, if we consider the grid as a graph $$G$$, where each tile $$t_i$$ is a node and there is an edge between $$t_i$$ and $$t_j$$ if $$\lvert x_i - x_j \rvert + \lvert y_i - y_j \rvert= 1$$, then $$G$$ is connected.
2. **Every horizontal and vertical word is in $$D$$.** 
For every $$i$$, we define a tuple $$H_i$$ as the "horizontal word" containing tile $$i$$ and a tuple $$V_i$$ as the "vertical word" containing tile $$i$$. Formally, to define $$H_i$$, let $$l$$ be the smallest integer such that $$(k, y_i) \in T$$ for all $$k \in [l, x_i]$$ and let $$r$$ be the largest integer such that $$(k, y_i) \in T$$ for all $$k \in [x_i, r]$$. Then $$H_i$$ is the following tuple:

    $$H_i = \left(T[(l, y_i)], T[(l + 1, y_i)], \ldots, T[(r, y_i)]\right) $$

    Similarly, to define $$L_i$$, let $$b$$ be the smallest integer such that $$(x_i, b) \in T$$ for all $$k \in [b, y_i]$$ and let $$t$$ be the largest integer such that $$(x_i, t) \in T$$ for all $$k \in [y_i, t]$$. Then $$V_i$$ is the following tuple:

    $$V_i = \left(T[(x_i, b)], T[(x_i, b + 1)], \ldots, T[(x_i, t)]\right) $$

    Recall that $$D = \{W_1, W_2, \ldots, W_k\}$$, where each $$W_i$$ is a tuple of elements of $$V$$. We then require $$V_i \in D$$ if $$\lvert V_i \rvert > 0 $$, and we require $$H_i \in D$$ if $$\lvert H_i \rvert > 0$$, for all $$i \in [1, \ldots, \lvert M \rvert]$$. 
    

If a valid solution $$S$$ exists, then the answer to $$B(M,D)$$ is $$yes$$; otherwise it is $$no$$.

The following image displays an examples of these concepts. The grid is connected,
and the highlighted horizontal (green) and vertical (red) words are all in $$D$$.

<img src="/assets/images/bananagrams_words.png" style="display:block; margin-left: auto; margin-right: auto;" width="200">
## $$B(M,D)$$ is in NP

First, we show that $$B(M, D)$$ is in NP. Consider some possible assignment $$S$$, with $$T$$ defined the same way as above. Algorithm $$A$$ that can verify $$S$$ in polynomial time is as follows:
* Verify that no tile is isolated by building a graph $$G$$ as described above and checking if it is connected. Such a graph can at worst be built in $$O(\lvert M \rvert^2)$$ by looping through every two tiles and checking if they are adjacent, and if so adding an edge between them. Checking if a graph is connected is a standard operation that we can do in time $$O(\lvert M \rvert)$$. 
    

* Verify that all words are valid. We can use the following pesudocode, which
runs in polynomial time. The proof is left as an exercise to the reader (no for real it's not too bad!).
  
    {% highlight python %}
    def verify_valid(M, D)
        for t_i in M:
            find H_i, V_j
            if H_i not in D:
                return false
            if V_i not in D:
                return false
        return true
    {% endhighlight %}


These algorithms are thus polynomial in the sizes of $$M$$ and $$D$$, so $$B(M,D)$$ is in NP.


## $$B(M,D)$$ is NP-Hard

We will prove this by reducing X3C to $$B(M,D)$$. 

### Reduction definition
Recall that in the X3C decision problem we are given a set $$X$$, with $$\lvert X \rvert = 3q$$, and a collection $$C$$ of $$3$$-element subsets of $$X$$. The first step of our reduction is to let 

$$M = X \cup R(y, 2 \lvert X \rvert ) \cup R(z, \lvert X \rvert + 1)$$

Now, let $$\lvert C \rvert = k$$ and denote 

$$\begin{align*}C_1 &= \{a_1, a_2, a_3\},\\ C_2 &= \{a_4, a_5, a_6\}, \ldots \\ C_k &= \{a_{3k - 2}, a_{3k - 1}, a_{3k}\} \end{align*}$$

Then we define $$D$$ as follows:

$$\begin{aligned}
D_1 &= \{(z, z, a_{3j+1}, a_{3j+2}, a_{3j+3}, z, z) \lvert j \in [0, k - 1]\}\\
D_2 &= \{(y, a, y) | a \in X\}\\
D_3 &= \{(y, y, y)\}\\
D &= D_1 \cup D_2 \cup D_3 \end{aligned}$$

In other words (pun intended), these are the words in our dictionary $$D$$:

<img src="/assets/images/possibilities.png" style="display:block; margin-left: auto; margin-right: auto;" width="300">


### $$X3C$$ is yes -> $$B(M, D)$$ is yes
Assume there is some valid cover $$C' = \{C_{j_1}, \ldots, C_{j_n}\}$$. Then we claim the following is a valid solution of the reduced Bananagrams problem:

<img src="/assets/images/bananagrams_big_correct.png" style="display:block; margin-left: auto; margin-right: auto;" width="600">

We will make most of our analysis informally from the image here, but we could formalize it (verbosely) in terms of $$(x_i, y_i)$$ if necessary. The solution clearly satisfies property 1, since the tiles are part of one structure and thus form a connected graph. Moreover, property 2 is satisfied because every vertical and horizontal word is valid by our construction of $$D$$ above: every horizontal and vertical word is one of $$(y, y, y)$$, $$(y, a_i, y)$$, and $$(z, z, a_{3j+1}, a_{3j + 2}, a_{3j + 3}, z, z)$$, all of which in $$D$$. Finally, this construction uses every tile in $$M$$ exactly once: we use each element in $$X$$ exactly once because $$C'$$ is a set cover and every set in $$C$$ corresponds to a single word in the solution, we use $$2\lvert X \rvert$$ copies of $$y$$ around the elements of $$X$$, and we use $$\lvert X \rvert + 1$$ copies of $$z$$ between words. Note that this analysis works too if $$X$$ is empty: the grid will contain just a single $$z$$ tile.

### $$X3C$$ is no -> $$B(M, D)$$ is no

First, we note that we do not need to consider the case when $$X$$ is the empty set, since $$X3C$$ is always yes in this case, making the left side of the if statement false. Thus we only consider cases $$X$$ when $$\lvert X \rvert \ge 3$$. In these cases, by the definition of $$M$$, $$S$$ must contain at least $$4$$ $$z$$ tiles.

We now define a $$Z$$-word: a $$Z$$-word is a horizontal or vertical word that contains a $$z$$ with length greater than $$2$$ that is in $$D$$. These are all of the form 

$$(z, z, a_{3i+1}, a_{3i+2}, a_{3i+3})$$

Each of the at least $$4$$ $$z$$ tiles must be connected to the rest of the tiles in the grid in a horizontal or vertical word, which by definition is a $$Z$$-word. Thus, there is at least one $$Z$$-word in $$S$$.

Next, we prove a few lemmas.

**Lemma 1**: In any valid solution $$S$$, every tile is in a $$Z$$-word or adjacent to a tile in a $$Z$$-word. To see this, we proceed by contradiction. Say a tile $$t_i$$ is not in a $$Z$$-word and is not adjacent to a tile in a $$Z$$-word. Consider a path through the connected tile graph from $$t_i$$ to a $$Z$$-word. WLOG, we can assume that this path does not traverse through a $$Z$$-word (if it does, we can just choose that shorter path). Let $$t_2$$ be a tile along this path of distance $$2$$ from a $$Z$$-word and let $$t_1$$ be the adjacent tile to $$t_1$$ with distance $$1$$ from a $$Z$$-word. Neither $$t_1$$ nor $$t_2$$ can be adjacent to a $$z$$, since then they
would be in a $$z$$ word. Thus, WLOG the following two possibilities are the only ones that could be valid (with other tiles in possibly other places represented by the "?" blocks):

<img src="/assets/images/could_this_work.png" style="display:block; margin-left: auto; margin-right: auto;" width="400">

However, $$(t_2, t_1, a_i)$$ must then form some part of a vertical word. However, the only words that have two letters followed by a letter from $$X$$ are $$Z$$-words, so $$t_2$$ and $$t_1$$ must in a $$Z$$-word, which is a contradiction. Thus our assumption is incorrect and every tile in $$S$$ must be in a $$Z$$-word or adjacent to a $$Z$$-word. 


**Lemma 2**: In any valid solution $$S$$, every $$y$$ is adjacent to a tile from $$X$$ (some $$a_j$$). There is no word in $$D$$ that contains both a $$y$$ and a $$z$$, so a $$y$$ cannot be in a $$Z$$-word. Thus, by Lemma $$1$$, it must be adjacent to a tile in a $$Z$$-word. Again, a $$y$$ cannot be adjacent to a $$z$$ tile, so it must be adjacent to an $$a_j$$ tile (the only other type of tile in $$Z$$-words). 

**Lemma 3** In any valid solution $$S$$, every $$a_j \in X$$ is in a $$Z$$-word. By Lemma $$1$$, every $$a_j$$ is either in a $$Z$$-word or adjacent to a $$Z$$-word, so we just need to show that $$a_j$$ cannot be adjacent to a $$Z$$-word. By contradiction, if $$a_j$$ was not in a $$Z$$-word, it would need to be in a word of the form $$(y, a_j, y)$$. However, this would then mean that $$y$$ would need to be in a $$Z$$-word in order for Lemma $$1$$ to be satisfied, but since there are no words in $$D$$ with both $$z$$ and $$y$$ in them this is a contradiction and every $$a_j \in X$$ is in a $$Z$$-word.

**Lemma 4** Every $$a_j \in X$$ is in just *one* $$Z$$-word. Assume by contradiction that this is not the case, and some $$a_k$$ is in two $$Z$$-words. Since every $$a_j$$ is in a $$Z$$-word (Lemma $$3$$), at least two tiles next to every $$a_j$$ are not a $$y$$ (since $$y$$s are not in $$Z$$-words). Since $$a_k$$ is in two $$Z$$-words (one horizontal and one vertical), all $$4$$ adjacent tiles to $$a_k$$ will not be $$y$$. This leaves at most $$\lvert X \rvert * 2 - 2$$ possible places for $$y$$ next to $$a$$s. By Lemma $$2$$, $$y$$s can only go in these positions. Since there are $$2\lvert X \rvert$$ $$y$$ tiles by the definition of $$M$$, but only $$\lvert X \rvert * 2 - 2$$ places to put them, this is a contradiction and the assumption must be false, and thus every $$a_j \in X$$ is in just *one* $$Z$$-word.

Finally, we are ready to prove that $$X3C$$ is no -> $$B(M, D)$$ is no. Assume by contradiction this was not the case, and there was some $$X$$ such that $$X3C$$ was no and $$B(M,D)$$ was yes; in other words, we had a valid $$S$$. We can then take the set of all $$Z$$-words on the board $$\{Z_i\}$$, where we let $$Z_i = \{z, z, a_{i,1}, a_{i,2}, a_{i,3}, z, z\}$$, and consider 


$$C' = \{\{a_{i,1}, a_{i,2}, a_{i,3}\} | Z_i\}$$


We know that every element in $$X$$ occurs in at least one element of $$C'$$ because $$S$$ is valid and uses all of the tiles of $$X$$, and by Lemma $$3$$ all of the tiles in $$X$$ are in some $$Z$$-word. Furthermore, we know that no element of $$X$$ occurs in two sets in $$C'$$ by Lemma $$4$$. Thus, this $$C'$$ is a valid cover for $$X3C$$, which is a contradiction, and $$X3C$$ is no -> $$B(M, D)$$ is no. 

$$\blacksquare$$

[1] - [Exact Cover by 3-Sets](https://npcomplete.owu.edu/2014/06/10/exact-cover-by-3-sets/)