---
layout: post
title:  "Hopfield Networks are useless. Here’s why you should learn about them."
date:   2019-11-04 11:18:14 +0100
categories: neural networks
---
<script type="text/x-mathjax-config">
  MathJax.Hub.Config({
    tex2jax: {
      inlineMath: [ ['$','$'], ["\\(","\\)"] ],
      processEscapes: true
    }
  });
</script>

<script type="text/javascript" async
src='https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.2/MathJax.js?config=TeX-MML-AM_CHTML'>
</script>


<div style="text-align: justify">
Hopfield networks were invented in 1982 by <a href="https://www.pnas.org/content/79/8/2554">J.J. Hopfield</a>, and by then a number of different neural network models have been put together giving way better performance and robustness in comparison. To my knowledge, they are mostly introduced and mentioned in textbooks when approaching Boltzmann Machines and Deep Belief Networks, since they are built upon Hopfield’s work. Nevertheless, they provide such a different and alternative perspective on learning systems compared to the current state of deep learning that they are worth understanding, if anything, for the sake of it. Let’s find out how they work.
</div>

## A basic Hopfield Net and how it works

<div style="text-align: justify">
At its core a Hopfield Network is a model that can reconstruct data after being fed with corrupt versions of the same data. We can describe it as a network of nodes — or units, or neurons — connected by links. Each unit has one of two states at any point in time, and we are going to assume these states can be +1 or -1. We can list the state of each unit
 at a given time in a vector <i>V</i>. Links represent connections between units and they are symmetric. In other words, the link between node-i and node-j is identical whichever direction you go in the graph. In particular, what is identical is
 the number representing the strength of the connection between each unit. We can list these numbers in a matrix <i>W</i>.
</div>

{% include image.html url="/assets/hopfield_1.jpeg" description="Fig. 1 - A Hopfield Network" %}

If we have a network like the one in Fig. 1, we can describe it with:

<!---
\\(ax^2 + bx + c = 0\\)
-->
<div style="text-align: center">
$$
V =
\begin{bmatrix}
+1 \\
-1 \\
-1 \\
+1
\end{bmatrix}
,\qquad
W =
\begin{bmatrix}
w_{aa} & w_{ab} & w_{ac} & w_{ad} \\
w_{ba} & w_{bb} & w_{bc} & w_{bd} \\
w_{ca} & w_{cb} & w_{cc} & w_{cd} \\
w_{da} & w_{db} & w_{dc} & w_{dd}
\end{bmatrix}
$$
</div>

<div style="text-align: justify">
How do we build W? We do it by first noticing that there is no connection between a node and itself, so we make this explicit by zeroing $w_{aa}, w_{bb}, w_{cc}, w_{dd}$. We also know that links -or weights- are symmetric, so for instance $w_{ab} = w_{ba}$. So W is zero-diagonal and symmetric. The rest of the elements are filled in this way: $w_{ab} = V_a · V_b$. The result is matrix W. This turns out to be a smart choice for the connection weights, as it follows the Hebbian rule we will see again later.
</div>

<div style="text-align: justify">
$$W =
\begin{bmatrix}
0  & -1 & -1 & +1 \\
-1 &  0 & +1 & -1 \\
-1 & +1 &  0 & -1 \\
+1 & -1 & -1 & 0
\end{bmatrix}
$$
</div>

<div style="text-align: justify">
Now that we built up the weight matrix we need to define a rule to determine the states of each node. Since we have binary states we can use this law:
$$ V_i = f(x_i) $$
$$
V_i=
    \begin{cases}
      +1, & \text{if}\ x_i = \sum_{j\neq i} w_{ij} \cdot V_j > 0\\
      -1, & \text{if}\ x_i = \sum_{j\neq i} w_{ij} \cdot V_j < 0
    \end{cases}
$$
By using the weight matrix defined above we have, similarly:
$$
V_i=
    \begin{cases}
      +1, & \text{if}\ x_i = W[i,:] \cdot V_j > 0\\
      -1, & \text{if}\ x_i = W[i,:] \cdot V_j < 0
    \end{cases}
$$
Now we can get an intuition of how Hopfield Networks actually work.
Say we have a corrupt version of V, and we will call it $V' =$ [1 -1 -1 -1], such that the last bit was reversed. We can initialize the network states with $V’$.

{% include image.html url="/assets/hopfield_2.png" description="Fig. 2 - Network initialized with corrupted states: d is now -1" %}

We can proceed updating the network states with the rules we established before. For $V_a$ we will have:
$$
V_a = f(w_{ab} · V_b + w_{ac} · V_c + w_{ad} · V_d) = +1
$$

Note that $w_{ab} · V_b$ and $w_{ac} · V_c$ have both positive contributions to x, despite Vb and Vc being -1, pushing Va to be +1, while the corrupted bit contributes in the opposite direction. In other words, by building W as before, we force nodes to settle in opposite states when they are supposed to be of opposite sign, and to settle to the same state when they are supposed to be equal. Opposite states repel each other, while same states attract instead.
</div>
This is one formulation of the Hebbian rule:

__Hebbian Rule__: *Neurons that fire together, wire together*

Similarly:

$$
V_b = f(w_{ba} ·V_a + w_{bc} · V_c + w_{bd} · V_d) = -1 $$

$$
V_c = f(w_{ca} · V_a + w_{cb} · V_b + w_{cd} · V_d) = -1
$$

Now the corrupted bit:

$$
V_d = f(w_{da} · V_a + w_{dc} · V_c + w_{db} · V_b) = +1
$$

<div style="text-align: justify">
And $V_a = +1 $ attracts node d to be positive by having $w_{da} · V_a>0 $, while $V_c=V_d= -1$ repel node d by giving an overall positive contribution $w_{dc} · V_c + w_{db} · V_d >0 $.
Nodes that were originally the same, are driven to be the same, nodes that were originally of opposite sign repel each other to be opposite.
</div>

*The original V is magically recovered!*

## But *why* does it work?

So far we saw that once we completely define the network -its $W$- with a state vector $V$ that we want to recover after corruption, we can do it by just updating the network states.


In other words, after initializing the network states with $V’$ we let the network evolve with the laws we defined before, and it will converge to the states we wanted in the first place. Not only that, but it will remain there no matter how many updates we keep on doing.

Let’s find out *why*.

We can define a function that depends on the states of the graph and the $W$ matrix. We will call this function the Energy function associated with the network states and denote it with:

<div style="text-align: justify">
$$
E = -\frac{1}{2} \sum_i \sum_j w_{ij}V_i V_j
$$
$$
E = -\frac{1}{2} V^T W V
$$
</div>
#### Result n° 1
If a node $V_i$ changes its state from +1 to -1 or vice versa, we will have that:

<div style="text-align: justify">
$$
\Delta E_i = \Delta V_i \sum_{j\neq i} w_{ij} V_j
$$
$$
\Delta E_i = \Delta V_i x_i
$$
$$
\Delta E_i = \Delta V_i \cdot W[i,:]\cdot V_i
$$
</div>

*Now: If Vi changed from -1 to +1, then $\Delta V_i = +2$, which means x has to be positive and in turn, the Energy delta has to be negative.*
This means that if we update the network according to our rules, the energy function will always decrease, it is monotonically decreasing, and it will try to reach its lowest point.

#### Result n° 2
But is there a lowest point or will the energy keep decreasing to negative infinity?
In other words, we are trying to figure out if the Energy delta can be zero.
To have $\Delta E_i = 0$ we need, for instance, to have $\Delta V_i = 0$, which is true only when $V_i(k-1)’ = V_i(k)’$, with $V_i(k-1)’$ being the node state before the update and $V_i(k)’$ after the update.
Let’s suppose we have $V_i(k-1)’ = +1$, we want $V_i(k)’ = +1$, or similarly $x_i(k) > 0$. Which is:

$$
x_i(k) = \sum_{j\neq i}w_{ij}V_j'(k-1) = \sum_{i\neq j}V_i V_j V_j'(k-1)
$$

But when $V_j(k-1)’ = V_j$ then $x_i(k)$ is always positive!
In one shot, we showed that when the states assume the original value (the uncorrupted value) the Energy function will not change anymore. In other words, $\Delta V_i = 0 $ and the node will not update to different values — the configuration is said to be stable.

<div style="text-align: justify">
So we proved the Energy is always decreasing until the network reaches a stable configuration of the node states. Even more, the stable configuration is the configuration that corresponds to the restored state vector, a local minimum of the energy function.
</div>


There are a number of implementation details that were spared here, but a basic, working Hopfield Network is in this Jupyter Notebook I prepared [here](https://github.com/philipjk/genetic_algorithm_optimization_sklearn-based/blob/master/hopfield_networks.ipynb).