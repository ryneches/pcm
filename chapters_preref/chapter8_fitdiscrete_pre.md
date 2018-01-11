# Chapter 8: Fitting models of discrete character evolution

[pdf version]({{ site.baseurl }}/pdf/chapter8_fitdiscrete.pdf)

R markdown to [recreate analyses]({{ site.baseurl }}/rmarkdown/chapter8_squamatelimbs.html)

## Biological motivation: The evolution of limbs and limblessness



![



Key Questions
-	How do we calculate the likelihoods of Mk and extended-Mk models on phylogenetic trees?
-	How can we use these approaches to test hypotheses about character evolution?

## Fitting Mk models to comparative data






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




When we have comparative data the situation is more complex. If we knew the ancestral character states and states at every node in the tree, then calculation of the overall likelihood would be straightforward – we could just apply the approach above many times, once for each branch of the tree. However, there are two problems. First, we don’t know the starting state of the character at the root of the tree, and must treat that as an unknown. Second, we are modeling a process that is happening independently on many branches in a phylogenetic tree, and only observe the states at the end of these branches. All of the character states at internal nodes of the tree are unknown. The likelihood that we want to calculate has to be summed across all of these unknown character state possibilities on the internal branches of the tree.



---

### Box 8.1: Felsenstein's pruning algorithm

Felsenstein’s pruning algorithm [-@Felsenstein1973-oj] is an example of dynamic programming, a type of algorithm that has many applications in comparative biology. In dynamic programming, we break down a complex problem into a series of simpler steps that have a nested structure. This allows us to reuse computations in an efficient way and speeds up the time required to make calculations.

The best way to illustrate Felsenstein’s algorithm is through an example, which is presented in the panels below. We are trying to calculate the likelihood for a three-state character on a phylogenetic tree that includes six species.

![
Figure 8.2A. Each tip and internal node in the tree has four boxes, which will contain the probabilities for the three character states at that point in the tree.
]({{ site.baseurl }}/images/figure8-2A.png)



1.	The first step in the algorithm is to fill in the probabilities for the tips. In this case, we know the states at the tips of the tree. We mathematically state that we know precisely the character states at the tips; the probability that that species has the state that we observe is 1, and all other states have probability zero:


![
Figure 8.2B. We put a one in the box that corresponds to the actual character state and zeros in all others.
]({{ site.baseurl }}/images/figure8-2B.png)


2.	Next, we identify a node where all of its immediate descendants are tips. There will always be at least one such node; often, there will be more than one, in which case we will arbitrarily choose one. For this example, we will choose the node that is the most recent common ancestor of species A and B, labeled as node 1 in Figure 8.2B.

3.	We then use equation 7.6 to calculate the conditional likelihood for each character state for the subtree that includes the node of interest and its tip descendants. For each character state, the conditional likelihood is the probability, given the data and the model, of obtaining the tip character states if you start with that character state at the root. In other words, we keep track of the likelihood for the tipward parts of the tree, including our data, if the node we are considering had each of the possible character states. This calculation is:

(eq. 8.2)
<div>
$$
L_P(i) = (\sum\limits_{x \in k}Pr(x|i,t_L)L_L(x)) \cdot (\sum\limits_{x \in k}Pr(x|i,t_R)L_R(x))
$$
</div>

Where i and x are both indices for the k character states, with sums taken across all possible states at the branch tips ($x$), and terms calculated for each possible state at the node ($i$). Each piece of equation 8.2 has two parts: the probability of starting and ending with each state along the two branches being considered, and the current conditional likelihoods that enter the equation at the tips of the subtree ($L_L(x)$ and $L_R(x)$). Branch lengths are denoted as $t_L$ and $t_R$ for the left and right, respectively. Branches can be assigned as left or right arbitrarily without affecting the final outcome, and the approach also works for polytomies (but the equation is slightly different).

One can think of the likelihood "flowing" down the branches of the tree, and conditional likelihoods for the left and right branches get combined via multiplication at each node, generating the conditional likelihood for the parent node ($L_P(i)$).



(eq. 8.2)
<div>
$$
L_P(0) = (\sum\limits_{x \in k}Pr(x|0,t_L=1.0)L_L(x)) \cdot (\sum\limits_{x \in k}Pr(x|0,t_R=1.0)L_R(x))
$$
</div>


Now notice that, since the left character state is known to be zero, $L_L(0)=1$ and $L_L(1)=L_L(2)=0$. Similarly, the right state is one, so $L_R(1)=1$ and $L_R(0)=L_R(2)=0$.

Next, we can calculate the probability terms from the probability matrix $\mathbf{P}$. In this case $t_L=t_R=1.0$, so for both the left and right branch:

(eq. 8.3)
<div>
$$
\mathbf{Q}t =
\begin{bmatrix}
-2 & 1 & 1 \\
1 & -2 & 1 \\
1 & 1 & -2 \\
\end{bmatrix} \cdot 1.0 =
\begin{bmatrix}
-2 & 1 & 1 \\
1 & -2 & 1 \\
1 & 1 & -2 \\
\end{bmatrix}
$$
</div>

So that:
(eq. 8.4)
<div>
$$
\mathbf{P} = e^{Qt} =
\begin{bmatrix}
0.37 & 0.32 & 0.32 \\
0.32 & 0.37 & 0.32 \\
0.32 & 0.32 & 0.37 \\
\end{bmatrix}
$$
</div>

We can now fill in the two parts of equation 8.2:

(eq. 8.5)
<div>
$$
\sum\limits_{x \in k}Pr(x|0,t_L=1.0)L_L(x) = 0.37 \cdot 1 + 0.32 \cdot 0 + 0.32 \cdot 0 = 0.37
$$
</div>

and

<div>
$$
\sum\limits_{x \in k}Pr(x|0,t_R=1.0)L_R(x) = 0.37 \cdot 0 + 0.32 \cdot 1 + 0.32 \cdot 0 = 0.32
$$
</div>

So:

(eq. 8.6)
<div>
$$
L_P(0) = 0.37 \cdot 0.32 = 0.12.
$$
</div>

We can use a similar approach to find that:

(eq. 8.7)
<div>
$$
L_P(1) = 0.32 \cdot 0.37 = 0.12.
$$
</div>

<div>
$$
L_P(2) = 0.32 \cdot 0.32 = 0.10.
$$
</div>



![
Figure 8.2C. Conditional likelihoods entered for node 1.
]({{ site.baseurl }}/images/figure8-2C.png)



![
Figure 8.2D. Conditional likelihoods entered for all nodea.
]({{ site.baseurl }}/images/figure8-2D.png)


5.	We can now calculate the likelihood across the whole tree using the conditional likelihoods for the three states at the root of the tree.

(eq. 8.8)
<div>
$$
L = \sum\limits_{x \in k} \pi_x L_{root} (x)
$$
</div>


Where $\pi_x$ is the prior probability of that character state at the root of the tree. For this example, we will take these prior probabilities to be uniform, equal for each state ($\pi_x = 1/k = 1/3$). The likelihood for our example, then, is:

(eq. 8.9)
<div>
$$
L = 1/3 \cdot 0.00150 + 1/3 \cdot 0.00151 + 1/3 \cdot 0.00150 = 0.00150
$$
</div>

Note that if you try this example in another software package, like GEIGER or PAUP*, the software will calculate a ln-likelihood of -6.5, which is exactly the natural log of the value calculated here.

---



## Using maximum likelihood to estimate parameters of the Mk model



If we apply the pruning algorithm across a range of different values of $q$, the likelihood changes. To find the ML estimate of $q$, we can again use numerical optimization methods.

Applying this method to the lizard data, we obtain a maximum liklihood estimate of $q = 0.001850204$ corresponding to $lnL = -80.487176$.

The example above considers maximization of a single parameter, which is a relatively simple problem. When we extend this to a multi-parameter model – for example, the extended Mk model will all rates different (ARD) – maximizing the likelihood becomes much more difficult. R packages solve this problem by using sophisticated algorithms and applying them multiple times to make sure that the value found is actually a maximum. 

We can also analyze this model using a Bayesian MCMC framework. We can modify the standard approach to Bayesian MCMC (see chapter 2):



2.	Given the current parameter value, select new proposed parameter values using the proposal density $Q(q'|q)$. For example, we might use a uniform proposal density with width 0.2, so that $Q(q'|q) ~ U(q - 0.1, q + 0.1)$.

3.	Calculate three ratios:

b.	The proposal density ratio, $R_{proposal}$. In this case our proposal density is symmetrical, so $R_{proposal}$ = 1.

4.	Find $R_{accept}$ as the product of the prior odds, proposal density ratio, and the likelihood ratio. In this case, both the prior odds and proposal density ratios are 1, so $R_{accept} = R_{likelihood}$ 


We can run this analysis on our squamate data, obtaining a posterior with a mean estimate of $q = 0.001980785$ and a 95% credible interval of $0.001174813 - 0.003012715$.

## Exploring Mk: the "total garbage" test

One problem that arises sometimes in maximum likelihood optimization happens when instead of a peak, the likelihood surface has a long flat “ridge” of equally likely parameter values. In the case of the Mk model, it is common to find that all values of $q$ greater than a certain value have the same likelihood. This is because above a certain rate, evolution has been so rapid that all traces of the history of evolution of that character have been obliterated. After this point, character states of each lineage are random, and have no relationship to the shape of the phylogenetic tree. Our optimization techniques will not work in this case because there is no value of q that has a higher likelihood than other values. Once we get onto the ridge, all values of $q$ have the same likelihood.





(eq. 8.10)
<div>
$$
L_{garbage} = p^{n_0} (1-p)^{n-n_0}
$$
</div>


This equation gives the likelihood of the “total garbage” model for any value of $p$. Equation 8.10 is related to a binomial distribution (lacking only the factorial term). We also know from probability theory that the ML estimate of $p$ is $n_0 / n$, with likelihood given by the above formula.





For the squamates, we have $n = 258$ and $n_0 = 207$. We calculate $p = n_0 / n =  207/258 = 0.8023256$. So the likelihood of our garbage model is $L_{garbage} = p^{n_0} (1-p)^{n-n_0} = 0.8023256^207 (1-0.8023256)^51 = 1.968142e-56$. This calculation is both easier and more useful, though, on a natural-log scale: $lnL_{garbage} = n_0 \cdot ln(p) + (n-n_0) \cdot ln(1-p) = 207 \cdot ln(0.8023256) + 51 \cdot ln(1-0.8023256) = -128.2677$. Compare this
to the log-likelihood of our Mk model, $lnL = -80.487176$, and you will see that the garbage model is a terrible fit to these data.

## Testing for differences in the forwards and backwards rate of character change


I have been referring to an example of lizard limb evolution throughout this chapter, but we have not yet tested the hypothesis that I stated in the introduction: that transition rates for losing limbs are higher than rates of gaining limbs.



(eq. 8.11)		
<div>
$$
\mathbf{Q_ER} =
\begin{bmatrix}
-q & q \\
q & -q \\
\end{bmatrix}
$$
</div>

<div>
$$
\mathbf{\pi_ER} =
\begin{bmatrix}
1/2 & 1/2 \\
\end{bmatrix}
$$
</div>

And for model 2, asymmetric:

(eq. 8.12)		
<div>
$$
\mathbf{Q_ASY} =
\begin{bmatrix}
-q_1 & q_1 \\
q_2 & -q_2 \\
\end{bmatrix}
$$
</div>

<div>
$$
\mathbf{\pi_ASY} =
\begin{bmatrix}
1/2 & 1/2 \\
\end{bmatrix}
$$
</div>

Notice that the ER model has one parameter, while the ASY model has two. Also we have specified equal probabilities of each character at the root of the tree, which may not be justified. But this comparison is still useful as a simple example.

One can compare the two nested models using standard methods discussed in previous chapters – that is, a likelihood-ratio test, AIC, BIC, or other similar methods.

We can apply all of the above methods to analyze the evolution of limblessness in squamates. We can use the tree and character state data from Brandley et al. [-@Brandley2008-wr], which is plotted with ancestral state reconstructions as Figure 8.3.

![Figure 8.3. Reconstructed patterns of the evolution of limbs and limblessness across squamates. Tips show states of extant taxa (here, I classified species with neither fore- nor hindlimbs as limbless, which is conservative given the variation across this clade (see chapter 7). Pie charts on internal nodes show proportional marginal likelihoods for ancestral state reconstruction. Data from [@Brandley2008-wr]]({{ site.baseurl }}/images/figure8-3.png)

If we fit an Mk model to these data assuming equal state frequencies at the root of the tree, we obtain a lnL of -80.5 and an estimate of the $Q_ER$ matrix as:

(eq. 8.13)		
<div>
$$
\mathbf{Q_ER} =
\begin{bmatrix}
-0.0019 & 0.0019 \\
0.0019 & -0.0019 \\
\end{bmatrix}
$$
</div>




(eq. 8.13)		
<div>
$$
\mathbf{Q_ASY} =
\begin{bmatrix}
-0.0016 & 0.0016 \\
0.0038 & -0.0038 \\
\end{bmatrix}
$$
</div>




A Bayesian analysis of the ASY model gives similar conclusions (Figure 8.3). We can see that the posterior distribution for the backwards rate (q21) is higher than the forwards rate (q12), but that the two distributions are broadly overlapping.



You might wonder about how we can reconcile these results, which suggest that squamates gain limbs at least as frequently as they lose them, with our biological intuition that limbs should be much more difficult to gain than they are to lose. But keep in mind that our comparative analysis is not using any information other than the states of extant species to reconstruct these rates. In particular, identifying irreversible evolution using comparative methods is a problem that is known to be quite difficult, and might require outside information in order to resolve conclusively. For example, if we had some information about the relative number of mutational steps required to gain and lose limbs, we could use an informative prior – which would, I suspect, suggest that limbs are more difficult to gain than they are to lose. Such a prior could dramatically alter the results presented in Figure 8.4. We will return to the problem of irreversible evolution later in the book (Chapter 13).

## Chapter summary

In this chapter I describe how Felsenstein’s pruning algorithm can be used to calculate the likelihoods of Mk and extended-Mk models on phylogenetic trees. I have also described both ML and Bayesian frameworks that can be used to test hypotheses about character evolution. This chapter also includes a description of the “total garbage” test, which will tell you if your data has information about evolutionary rates of a given character.

Analyzing our example of lizard limbs shows the power of this approach; we can estimate transition rates for this character over macroevolutionary time, and we can say with some certainty that transitions between limbed and limbless have been asymmetric. In the next chapter, we will build on the Mk model and further develop our comparative toolkit for understanding the evolution of discrete characters.

## References