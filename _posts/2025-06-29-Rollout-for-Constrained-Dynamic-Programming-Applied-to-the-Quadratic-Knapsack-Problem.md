---
layout: post
---
## hello 
Mathematical models which include integer variables are ubiquitous in many contexts of significant importance within scientific inquiry. Such problems typically are empirically seen as much more difficult to solve than similar models containing only continuous real-valued variables *linear programs* (LPs) and *mixed-integer linear programs* (MILPs) *mixed-integer nonlinear programs* (MINLPs)
hello

$$
\begin{alignat}{1}
\text{maximize }\ & \sum_{i=1}^{n}\sum_{j=1}^{n}p_{ij}x_{i}x_{j}, \\
\text{subject to }\ & \sum_{i=1}^{n}w_{i}x_{i} \leq C, \\
& \mathbf{x}\in \{0,1\}^{n} =: \mathbb{B}^{n}. 
\end{alignat}
$$

Specifically, in (1a)-(1c) above, $C$ is the available space in the knapsack, $w_{i}$ for $i=1,\ldots,n$ are non-negative weights associated with inluding item $i$ in the knapsack, and $p_{ij}$ for $i=1,\ldots,n$, $j=1,\ldots,n$ are non-negative profits associated with including item $i$ and item $j$ together in the knapsack. By solving (1a)-(1c), the ultimate goal is to maximize profit corresponding to packing items in the knapsack, while simultaneously considering item interaction and ensuring that capacity restrictions are satisfied. 

