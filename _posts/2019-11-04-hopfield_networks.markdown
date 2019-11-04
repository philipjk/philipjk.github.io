---
layout: post
title:  "Hopfield Networks are useless. Here’s why you should learn about them."
date:   2019-11-04 11:18:14 +0100
categories: neural networks
---


<div style="text-align: justify">
Hopfield networks were invented in 1982 by <a href="https://www.pnas.org/content/79/8/2554">J.J. Hopfield</a>, and by 
then a number of different neural network models have been put together giving way better performance and robustness in 
comparison. To my knowledge, they are mostly introduced and mentioned in textbooks when approaching Boltzmann Machines 
and Deep Belief Networks, since they are built upon Hopfield’s work. Nevertheless, they provide such a different and 
alternative perspective on learning systems compared to the current state of deep learning that they are worth 
understanding, if anything, for the sake of it. Let’s find out how they work.
</div>

## A basic Hopfield Net and how it works

<div style="text-align: justify">
At its core a Hopfield Network is a model that can reconstruct data after being fed with corrupt versions of the same 
data. We can describe it as a network of nodes — or units, or neurons — connected by links. Each unit has one of two 
states at any point in time, and we are going to assume these states can be +1 or -1. We can list the state of each unit
 at a given time in a vector <i>V</i>. Links represent connections between units and they are symmetric. In other words, the 
link between node-i and node-j is identical whichever direction you go in the graph. In particular, what is identical is
 the number representing the strength of the connection between each unit. We can list these numbers in a matrix <i>W</i>.
</div>

{% include image.html url="/assets/hopfield_1.jpeg" description="Fig. 1 - A Hopfield Network" %}

If we have a network like the one in Fig. 1, we can describe it with:

\(ax^2 + bx + c = 0\)
