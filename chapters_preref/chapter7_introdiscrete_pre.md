# Chapter 7: Models of discrete character evolution

[pdf version]({{ site.baseurl }}/pdf/chapter7_introdiscrete.pdf)

R markdown to [recreate analyses]({{ site.baseurl }}/rmarkdown/chapter8_squamatelimbs.html)

## Biological motivation: Limblessness as a discrete trait

Squamates, the clade that includes all living species of lizards, are well known for their diversity. From the gigantic Komodo dragon of Indonesia (Figure 7.1A, *Varanus komodoensis*) to tiny leaf chameleons of Madagascar (Figure 7.1B, *Brookesia*), squamates span an impressive range of form and ecological niche use [@Vitt2003-bl; @Pianka2017-aw]. Even the snakes (Figure 7.1C and D), extraordinarily diverse in their own right (~3,500 species), are actually a clade that is nested within squamates [@Streicher2017-wb]. The squamate lineage that is ancestral to snakes became limbless about 170 million years ago (see [Timetree of Life](www.timetree.org)) – and also underwent a suite of changes to their head shape, digestive tract, and other traits associated with their limbless lifestyle. In other words, snakes are lizards – highly modified lizards, but lizards nonetheless. And snakes are not the only limbless lineage of squamates. In fact, lineages within squamates have lost their limbs over and over again through their history (e.g. Figure 7.1E and F), with some estimates that squamates have lost their limbs at least 26 times in the past 240 million years [@Brandley2008-wr].

![Figure 7.1. Squamates, legged and legless. A. Komodo dragon, B. Brookesia chameleon, C. and D. snakes, E. and F. legless lizards.
]({{ site.baseurl }}/images/figure7-1.png)



Limblessness is an example of a discrete trait – a trait that can occupy one of a set of distinct character states. Analyzing the evolution of discrete traits requires a different modeling approach than what we used for continuous traits. In this chapter, I will discuss the Mk model [@Lewis2001-bu], which is a general approach to modeling the evolution of discrete traits on trees. Fitting this model to comparative data will help us understand the evolution of traits like limblessness where species can be placed into one of a number of discrete character states.

Key Questions
-	How can we model the evolution of discrete characters that have a set number of fixed states?
-	How can we change the parameters of the Mk model to construct more elaborate models of discrete character evolution?
-	How do we simulate the evolution of a discrete character?


## Modeling the evolution of discrete states



We will consider discrete characters where each species might exhibit one of $k$ states. (In the limbless example above, $k=2$). For characters with more than two states, there is a key distinction between ordered and unordered characters. Ordered characters can be placed in an order so that transitions only occur between adjacent states. For example, I might include “intermediate” species that are somewhere in between limbed and limbless – for example, the “mermaid skinks” (Sirenoscincus) from Madagascar, so called because they lack hind limbs [Figure 7.2, @Moch2011-fp]. An ordered model might only allow transitions between limbless and intermediate, and intermediate and limbed; it would be impossible under such a model to go directly from limbed to limbless without first becoming intermediate. For unordered characters, any state can change into any other state. In this chapter, I will focus mainly on unordered characters; we will return to ordered characters later in the book.

![Figure 7.2. Mermaid skink]({{ site.baseurl }}/images/figure7-2.png)





Any discrete character can be modeled in a similar way as gene sequences. When considering phenotypic characters, we should keep in mind two main differences from the analysis of DNA sequences. First, arbitrary discrete characters may have any number of states (beyond the four associated with DNA sequence data). Second, characters are typically analyzed independently rather than combining long sets of characters and assuming that they share the same model of change.

## The Mk Model

The most basic model for discrete character evolution is called the Mk model. First developed for trait data by Pagel [-@Pagel1994-ui; although the name Mk comes from @Lewis2001-bu]. The Mk model is a direct analogue of the Jukes-Cantor (JC) model for sequence evolution. The model applies to a discrete character having $k$ unordered states. Such a character might have $k = 2$, $k = 3$, or even more states. Evolution involves changing between these $k$ states (Figure 7.3).

![Figure 7.3. Examples of discrete characters with (A) $k = 2$, (B) $k = 3$, and (C) $k = 4$ states.]({{ site.baseurl }}/images/figure7-3.png)



The basic version of the Mk model assumes that transitions among these states follow a Markov process. This means that the probability of changing from one state to another depends only on the current state, and not on what has come before. For example, it makes no difference if a lineage has just evolved the trait of “feathers,” or whether they have had feathers for millions of years – the probability of evolving a different character state is the same in both cases. The basic Mk model also assumes that every state is equally likely to change to any other states. 

For the basic Mk model, we can denote the instantaneous rate of change between states using the parameter $q$. In general, $q_{ij}$ is called the instantaneous rate between character states $i$ and $j$. It is defined as the limit of the rate measured over very short time intervals. Imagine that you calculate a rate of character change by counting the number of changes of state of a character over some time interval, $t$. You can calculate the rate of change as $n_{changes} / t$. The instantaneous rate is the value that this rate approaches as $t$ gets smaller and smaller so that the time interval is nearly zero. Again, for the basic Mk model, instantaneous rates between all pairs of characters are equal; that is, $q_{ij} = q_{mn}$ for all $i \neq j$ and $m \neq n$. 

(eq. 7.1)
<div>
$$
\mathbf{Q} =
\begin{bmatrix}
-d_1 & q_{12} & \dots & q_{1k} \\
q_{21} & -d_2 & \dots & q_{2k} \\
\vdots & \vdots & \ddots & \vdots\\
q_{k1} & q{k2} & \dots & -d_k \\
\end{bmatrix}
$$
</div>




(eq. 7.2)
<div>
$$
d_1 = \sum_{i=2}^{k} q_{1i}
$$
</div>





(eq. 7.3)
<div>
$$
\mathbf{Q} =
\begin{bmatrix}
-q & q \\
q & -q \\
\end{bmatrix}
$$
</div>



(eq. 7.4)	
<div>
$$
\mathbf{Q} =
\begin{bmatrix}
-2 q & q & q \\
q & -2 q & q \\
q & q & -2 q \\
\end{bmatrix}
$$
</div>

In general,

(eq. 7.5)	
<div>
$$
\mathbf{Q} = q
\begin{bmatrix}
1-k & 1 & \dots & 1 \\
1 & 1-k & \dots & 1 \\
\vdots & \vdots & \ddots & \vdots\\
1 & 1 & \dots & 1\\
\end{bmatrix}
$$
</div>

Once we have this transition rate matrix, we can calculate the probability distribution of trait states after any time interval $t$ using the equation [@Lewis2001-bu]:

(eq. 7.6)
<div>
$$
\mathbf{P}(t) = e^{\mathbf{Q}t}
$$
</div>



(eq. 7.7)	
<div>
$$
\begin{array}{l}
p_{ii}(t) = \frac{1}{k} + \frac{k-1}{k} e^{-kqt} \\
p_{ij}(t) = \frac{1}{k} - \frac{1}{k} e^{-kqt} \\
\end{array}
$$
</div>

In particular, when k = 2,

(eq. 7.8)	
<div>
$$
\begin{array}{l}
p_{ii}(t) = \frac{1}{k} + \frac{k-1}{k} e^{-kqt} = \frac{1}{2} + \frac{2-1}{2}e^{-2qt}=\frac{1+e^{-2qt}}{2} \\
p_{ij}(t) = \frac{1}{k} - \frac{1}{k} e^{-kqt} = \frac{1}{2} - \frac{1}{2}e^{-2qt}=\frac{1-e^{-2qt}}{2} \\
\end{array}
$$
</div>

If we consider what happens when time gets very large in these equations, we see an interesting pattern. Any term that has $e^{-t}$ in it gets closer and closer to zero as t increases. Because of this, for all values of $k$, each $p_{ij}(t)$ converges to a constant value, $1/k$. This is the stationary distribution of character states, $\pi$, defined as the equilibrium frequency of character states if the process is run many times for a long enough time period. In general, the stationary distribution of an Mk model is:

(eq. 7.9)
<div>
$$
\pi =
\begin{bmatrix}
1/k & 1/k & \dots & 1/k\\
\end{bmatrix}
$$
</div>




<div>
$$
\pi =
\begin{bmatrix}
1/2 & 1/2 \\
\end{bmatrix}
$$
</div>

## The Extended Mk Model

The Mk model assumes that transitions among all possible character states occur at the same rate. However, that may not be a valid assumption. For example, it is often supposed that it is easier to lose a complex character than to gain one. We might want to fit models that allow for such asymmetries in rates.

For models of DNA sequence evolution there are a wide range of models allowing different rates between distinct types of nucleotides [@Yang2006-md]. Unequal rates are usually incorporated into the Mk model in two ways. First, one can consider the symmetric model [SYM; @Paradis2004-xk]. In the symmetric model, the rate of change between any two character states is the same forwards as it is backwards (that is, rates of change are symmetric; $q_{ij} = q_{ji}$). The rate for a particular pair of states might differ from other pairs of character stats. The rate matrix for this model has as many free rate parameters as there are pairs of character states, $k (k - 1) / 2$.

(eq. 7.11)
<div>
$$
p = \frac{k(k-1)}{2}
$$
</div>



(eq. 7.12)
<div>
$$
\pi_{SYM} =
\begin{bmatrix}
\pi_1 & \pi_2 & \dots & 1 - \sum_{i=1}^{n-1} \pi_i
\end{bmatrix}
$$
</div>



(eq. 7.13)
<div>
$$
p = \frac{k(k-1)}{2} + n-1
$$
</div>



(eq. 7.14)
<div>
$$
\mathbf{Q} =
\begin{bmatrix}
\cdot & r_1 & \dots & r_{n-1} \\
r_1 & \cdot & \dots & \vdots \\
\vdots & \vdots & \cdot & r_{k(k-1)/2} \\
r_{n-1} & \dots r_{k(k-1)/2} & \cdot \\
\end{bmatrix}
\begin{bmatrix}
\pi_1 & 0 & 0 & 0 \\
0 & \pi_2 & 0 & 0 \\
0 & 0 & \ddots & 0 \\
0 & 0 & 0 & \pi_n \\
\end{bmatrix}
$$
</div>





(eq. 7.15)
<div>
$$
\mathbf{Q} =
\begin{bmatrix}
\cdot & 1 \\
2 & \cdot \\
\end{bmatrix}
\begin{bmatrix}
0.75 & 0 \\
0 & 0.25 \\
\end{bmatrix}
=
\begin{bmatrix}
\cdot & 0.25 \\
1.5 & \cdot \\
\end{bmatrix}
=
\begin{bmatrix}
-0.25 & 0.25 \\
1.5 & -1.5 \\
\end{bmatrix}
$$
</div>

It is worth noting that this approach of setting parameters that define equilibrium state frequences, although borrowed from molecular evolution, is not completely standard in the comparative methods literature. One also sees equilibrium frequencies treated as a fixed property of the model, and assumed to be either equal across states or tied directly to the parameters in the $\mathbf{Q}$-matrix.



The same algorithm can be used to calculate the likelihood for both of these extended Mk models (SYM and ARD). These models have more parameters than the standard Mk. To find maximum likelihood solutions, we must optimize the likelihood across the entire set of unknown parameters (see Chapter 7).

## Simulating the Mk model on a tree

We can also use the equations above to simulate evolution under an Mk or extended-Mk model on a tree [@Felsenstein2004-eo]. To do this, we simulate character evolution on each branch of the tree, starting at the root and progressing towards the tips. At speciation, we assume that both daughter species inherit the character state of their parental species immediately following speciation, and then evolve independently after that. At the end of the simulation, we will obtain a set of character states, one for each tip in the tree. The distribution of character states will depend on the shape of the phylogenetic tree (both its topology and branch lengths) along with the parameters of our model of character evolution.





We can understand this algorithm perfectly well by thinking about what happens on each branch of the tree, and then extending that algorithm to all of the branches (as described above). For each branch, we first calculate $\mathbf{P}(t)$, the transition probability matrix, given the length of the branch and our model of evolution as summarized by $\mathbf{Q}$ and the branch length $t$. We then focus on the row of $\mathbf{P}(t)$ that corresponds to the character state at the beginning of the branch. For example, let’s consider a basic two-state Mk model with $q = 0.5$. We will call the states 0 and 1. We can calculate $\mathbf{P}(t)$ for a branch with length $t = 3$ as:

(eq. 7.16)
<div>
$$
\mathbf{P}(t) = e^{\mathbf{Q} t} = exp(
\begin{bmatrix}
-0.5 & 0.5 \\
0.5 & -0.5 \\
\end{bmatrix}
\cdot 3) =
\begin{bmatrix}
0.525 & 0.475 \\
0.475 & 0.525 \\
\end{bmatrix}
$$
</div>





(eq. 7.17)
<div>
$$
\begin{array}{l}
p_{ii}(t) = \frac{1}{k} + \frac{k-1}{k} e^{-kqt} = \frac{1}{10}+\frac{9}{10}e^{-2 \cdot 0.5 \cdot 3} = 0.145\\
p_{ij}(t) = \frac{1}{k} - \frac{1}{k} e^{-kqt} \frac{1}{10} - \frac{1}{10}e^{-2 \cdot 0.5 \cdot 3} = 0.095\\
\end{array}
$$
</div>	

We focus on the first row of $\mathbf{P}(t)$, which has elements:

<div>
$$
\begin{bmatrix}
0.145 & 0.095 & 0.095 & 0.095 & 0.095 & 0.095 & 0.095 & 0.095 & 0.095 & 0.095 \\
\end{bmatrix}
$$
</div>



<div>
$$
\begin{bmatrix}
0.145 & 0.240 & 0.335 & 0.430 & 0.525 & 0.620 & 0.715 & 0.810 & 0.905 & 1.000 \\
\end{bmatrix}
$$
</div>



We can apply this approach to simulate the evolution of limblessness in squamates. Below, I present the results of three such simulations. These simulations are a little different than what I describe above because they consider all changes in the tree, rather than just character states at nodes and tips; but the model (and the principal) is the same. You can see that the model leaves an imprint on the pattern of changes in the tree, and you can imagine that one might be able to reconstruct the model using a phylogenetic comparative approach. Of course, typically we know only the tip states, and have to reconstruct changes along branches in the tree. We will discuss parameter estimation for the Mk and extended-Mk models in the next chapter.

![Figure 7.4. Simulated character evolution on a phylogenetic tree of squamates [from @Brandley2008-wr] under an equal-rates Mk model with slow, fast, and asymmetric transition rates (from right to left). In all three cases, I assumed that the ancestor of squamates had limbs. ]({{ site.baseurl }}/images/figure7-4.png)



## Chapter summary

<!---
<img data-gifffer="{{ site.baseurl }}/images/figure7-animated.png" />
-->


In this chapter I have described the Mk model, which can be used to describe the evolution of discrete characters that have a set number of fixed states. We can also elaborate on the Mk model to allow more complex models of discrete character evolution (the extended-Mk model). These models can all be used to simulate the evolution of discrete characters on trees. 

## References