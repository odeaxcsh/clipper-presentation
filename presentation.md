The paper I am about to present is titled "CLIPPER: A graph theoratic framework for robust data association".

Data association is the practice of finding a true correspondence between two sets of objects, given a putative set of assosiations. And by true correspondance I mean it fits a transformation model bestter than any other.

This problem is cosidered to be hard, because even in the case of perfect observations, the computational complexity of the prolem grows exponentially as the size of datasets grow.

And furthuer more, it is expected for a practical algorithm to be immune to heavily noised data and abundance of outliers.

That being said, Data association has many applications in computer science and robotics.
To mention a few:
1. Point cloud registration
2. Multi-object tracking
3. Object Detection
4. Shape alignment
5. and a lot more

To solve the problem we leverage the concept of geometric consistancy between pairs of assciations. What is geometric consistency, you may ask? Well, it's the fact that the true associations must preserve the geometric structure of the data or more specifically, they preserve the invariant properties of the transformation model.

For example, in the case of point cloud registration, we know that euclidean transformation is length preserving, and so, if u = (a, a') and v = (b, b') are two putative associations, then the true association must satisfy the following equation: ||a - b|| = ||a' - b'||. And this is what we call geometric consistency. 

Notice, that this is a very strong constraint, and it's not always possible to find a true association that satisfies this equation. Specifically, when the data is noisy, or the transformation model is not accurate enough, for example when we selected semi invariant properties which I'll get to later.

So we use a function, namely measure of consistency to measure the degree of geometric consistency between two associations. This function can be binary or continuous, for example a simple threshold or gussian likelihood function.

Now, let's see how we can use this concept to solve the data association problem. We start by constructing a graph, consistency graph, which in each node represents an association. and if consistency measure of two associations is greater than zero then there is an edge between their respective nodes.


Now it is intuative that we are going to select the biggest clique, or desnset subgrpah, but we'er gonna be more specific about it in a minute.


Now, before we dive into how this paper solves the problem, I would like to iterate over some similar works that use the same idea.


First category of solutions are those who threshold consistency graph. And then try to find maximum clique. Maximum Clique problem is known to be NP-Hard, so exact algorithms are usualy time-consuming. There is a really good relaxation which I'll get to later. 

Here I want to show, why thresholding might increase the error or even fail in some cases. In this illustration, you can see that before thresholding there is strong clique between (u_1 u_2 and u_4), but after we threshold the graph, there are two compiting cliques, which by only looking at the graph we can't disgtinuish which one is the correct one. 

So yeah, thresholding in some sense is throwing some cursial information away.


Second category of solutions deal with weighted graph, but they discard clique constraints. They just look for densest subgraph. We can find densest subgraph in a reasonable amount of time, compared to MCP.

Here, the algorithm would select a random edge which is not consistent with some nodes at all, and adds it to the solution in order to increase density.

And finally, Maximum weighted clique problem, which deals with weighted graphs, but because sum of weights is not normalized then it doesn't return desnst clique, only the biggest with highest sum of edge.
In this illustration as well you can see thtat u_2 is added to graph while it is not consistent as others. (u1 u5 u3) alone are more denst that this one.

So we need to keep clique constraints while searching for the densest subgraph.


To simplify formulations a little bit, we take adjacency matrix of $G$ and fill it's diagonal elements, and name it $M$, affinity matrix.

So, here is the formulation clipper proposes. And now, I hope it make a lot of sense, what we are doing here is finding the desnst clique. But model by it self is hard to solve, so we need a relaxation.


This relaxation here is the one I was talking about in solving MC problem. "Belachew" and "Gilis" has shown that this relaxation have many good properties for binary matrices. And the authors of this paper has extended some of those properties to weighted case.

In this relaxation you can see that we are again searching for the densest subgraph, and normalization factor is here, in the constraints. But here we modify affinity matrix penalize violoation of cliques by $d$.

One of those good properties I was talking about was, that if you select $d$ large enough clique constraints are guaranteed in solutions.
And for binary case it solutions actually converge to a binary vector so, we don't need to binarize them afterward.


So to solve the optimization problem, CLIPPER presents a polynomial-time algorithm,
What this algorithm is essentially doing is that it start with sovling relaxed densest sub-graph formulation, by putting simply $d$ equals zero, using a projected gradient ascent, and then uses the solution as a warm start for the next interation, when $d$ is incrases. 
Algorithm does this iteratively, incraseing $d$ and warm starting projected gradient ascent with $u$ until $d$ is large enough.

and because we are sure that non-zero elements of u create a clique we then jsut need to binarize them. and to do so, clipper uses a densest subgraph approximation.
So this is the whole pipline. but for further information you can refer to the paper.


About the results. Comparing clipper to other algorithm which I mentioned, you can see that in precession and recall, clipper keep higher accuracy when dealing with large amount of outliers. and you can see that precision of binary clipper in which we used binary measure of consistecy drops sooner.


In the runtime too, clipper maintain a good runtime across different outlier regimes.


Finally for future work, I would like to mention thsese few
First thing is that when we are dealing with all-to-all association set, the algorithm become impractical for medium size sets.

For example if we have two sets of objects each size of $100$ then we would have appoximately $10$ tousand approximations. which would take clipper a couple of seconds to solve. We may explore how can we reduce run-time or number of approximation in those cases.


I felt the need for a more thourgh explanation about the cases that don't have pairwise invariant rather tuple of three elements or more. for example in similarity based registration. 


Finally, when we are dealing with structured noise, for example in distortion correction, the noise isn't constant and equally distbuted trough the data. and considering it's distribution model and embedding it into the algorithm can improve accuracy.


Thanks for listening.
I'm avaiable for your questions
