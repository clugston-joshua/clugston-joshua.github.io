---
layout: post
---


## Introduction
Mathematical models which include integer variables are ubiquitous in many contexts of significant importance within scientific inquiry. Such problems typically are empirically seen as much more difficult to solve than similar models containing only continuous, real-valued variables. The primary difficulty of models with integer variables is that their solution spaces explodes combinatorially as the number of decisions to be made increases. Despite mathematical programs with integer variables suffering from the issue of combinatorial explosion resulting from the presence of integer variables, in the case of problems with linear objective functions and constraints, or problems referred to as *linear programs* (LPs) and *mixed-integer linear programs* (MILPs), several approaches have been developed for obtaining high-quality solutions very quickly with much success. 

Among the more successful methods specifically for solving MILPs, the most popular of which regularly consider some methodology based on the idea of reducing the feasible region in an attempt to obtain its convex hull, or at the very least an approximation thereof. More generally, however, are integer programs which contain non-nonlinearities either within their objective function or constraints, called *mixed-integer nonlinear programs* (MINLPs). MINLPs are, in general, much more difficult to solve than MILPs. Because of their inherent difficulty, obtaining exact solutions to MINLPs is especially challenging, and typical approaches for obtaining global solutions generally rely on relaxing integrality restrictions and applying a branch-and-bound-like approach to both continuous and integer variables. In contrast to traditional branch-and-bound, additional difficulty with this approach is a consequence of each relaxed problem being a constrained nonlinear program (NLP), which may be non-convex. A common approach for overcoming further difficulty imposed by non-convexity is through the application of convex envelope relaxations, whose goal is to further acquire tight approximations for the original NLP. Branch-and-bound-like methods such as those previously described for MINLPs are regularly restrictive, and empirical performance is often observed to be less than practical for many problems of relevance.

In an attempt to overcome less than ideal performance in terms of computational speed of exact branch-and-bound-like algorithms, heuristics are regularly enlisted as a means of producing approximate solutions very quickly. Some examples of heuristic algorithms for solving MINLPs are those proposed by {% cite Fomeni-Letchford %} and {% cite FENNICH2024102 %}, which both utilize a dynamic programming approach in the context of a binary knapsack problem with nonlinearities. Further examples of more general heuristics, particularly designed for solving MINLPs, include sub-NLP and NLP-diving. Both of these approaches attempt to explore the solution space by exploiting the decrease in difficulty obtained from excluding integral restrictions through either fixing integer variables to a constant numerical value, or entirely removing integerality from the model before solving. Between both of the two aforementioned heuristics, the idea is to iteratively perform the relaxation of integerality restriction or fixing of integer variables until a good quality solution is attained. Effectively, each of two latter-most heuristics result in solving NLP subproblems to achieve approximations to the original problem. 

One additional possible method for solving potentially large-scale MINLPs is to apply approximations within the traditional dynamic programming scheme, thereby taking an approximate dynamic programming (ADP) approach. To the best of the author's knowledge, however, this approach is not common for MINLPs, despite several methods utilizing ADP for MILPs. For instance, {% cite SECOMANDI20001201 %} apply neuro-dynamic programming to the vehicle routing problem with stochastic demands, while {% cite Bertazzi2012 %} use rollout to solve the 0-1 knapsack problem. On the other hand, there is at least one instance of rollout being applied to MINLPs, as done in {% cite Bai10147895 %} for electric vehicle charging planning. In this document, the goal is to further explore the capability of ADP approaches for solving challenging MINLPs. 


## Background 
Consider the 0-1 quadratic knapsack problem:  

$$
\begin{alignat}{1}
\text{maximize }\ & \sum_{i=1}^{n}\sum_{j=1}^{n}p_{ij}x_{i}x_{j}, \\
\text{subject to }\ & \sum_{i=1}^{n}w_{i}x_{i} \leq C, \\
& \mathbf{x}\in \{0,1\}^{n} =: \mathbb{B}^{n}. 
\end{alignat}
$$

Specifically, in (1)-(3) above, $C$ is the available space in the knapsack, $w_{i}$ for $i=1,\ldots,n$ are non-negative weights associated with inluding item $i$ in the knapsack, and $p_{ij}$ for $i=1,\ldots,n$, $j=1,\ldots,n$ are non-negative profits associated with including item $i$ and item $j$ together in the knapsack. By solving (1)-(3), the ultimate goal is to maximize profit corresponding to packing items in the knapsack, while simultaneously considering item interaction and ensuring that capacity restrictions are satisfied. 

The model described in (1)-(3) lends itself to many applications, resulting from its general form. Most popularly, the QKP is well-known for its application to a decision problem utilized for determining optimal satellite station locations. Given its applicability in this context, it should likely not come as a suprise to the reader that the QKP has similarly seen use in the context of other optimal location, planning problems. For instance, QKP has been used in determining the optimal placement of airports and railway and freight handling stations. In addition, some other popular applications of QKP involve compiler design,  very large scale integration, and financial applications which, for example, compare optimal investment strategies. It it further observed that QKP is a generalization of a difficult combinatorial problem called the maximum clique problem, which itself has many applications ranging from social network analysis to chemistry.  

Despite its general applicability, model (1)-(3) has several difficulties which present it as a very challenging problem to solve, especially for instances of practical size. Due to the presence of bilinear terms, $x_{i}x_{j}$ for $i=1,\ldots,n$ and $j= 1,\ldots,n$, which are non-convex, (1)-(3) is referred to as a *non-convex mixed-integer nonlinear program*. More precisely, it is observed that the continuous relaxation of (1)-(3) is a non-convex NLP. In which case, non-convexity present an especially difficult challenge to resolve due to the potential for local minima to hinder the search for global solutions. Moreover, because (1)-(3) can be seen as a generalization of the maximum clique problem, which is known to be NP-hard in the strong sense, it follows that QKP is NP-hard in the strong sense.  Therefore, in contrast to the standard 0-1 knapsack problem, unless $\text{P} = \text{NP}$, it is unlikely that there exists a pseudo-polynomial time algorithm which may be used to solve QKP. In practice, particular difficulty stems from the data $p_{ij}$, and solving QKP may be too computationally intensive for moderately sized problems in some cases, provided that these data are restrictive. This latter point will be discussed in more detail in **Section 4**.  

To overcome some of the difficult, it is common to reformulate QKP to a simpler form. A popular technique regularly employed in global optimization to deal with bilinear terms such as $x_{i}x_{j}$ utilizes tight-fitting convex/concave under and over-approximators, called *McCormick envelopes*. Through the introduction of an auxiliary variable $u_{ij} = x_{i}x_{j}$ for all $i= 1,\ldots,n$ and $j=1,\ldots, n$, model (1)-(3) may be rewritten in relaxed form as follows:

$$
\begin{alignat}{1}
\text{maximize }\ & \sum_{i=1}^{n}\sum_{j=1}^{n}p_{ij}u_{ij}, \\
\text{subject to }\ & u_{ij} \geq x_{j}+x_{i}-1,\text{ for all $i\in [n]$, $j\in [n]$} \\
& u_{ij} \leq x_{i},\text{ for all $i\in [n]$, $j\in [n]$},\\
& u_{ij} \leq x_{j},\text{ for all $i\in [n]$, $j\in [n]$},\\
& \sum_{i=1}^{n}w_{i}x_{i}\leq C,\\
& \mathbf{x} \in \mathbb{B}^{n},\ \mathbf{u}\in\mathbb{R}_{\geq0}^{n\times n}.
\end{alignat}
$$

However, because $x_{i} \in\mathbb{B}$ for all $i$, the aforementioned relaxed problem is actually an exact reformulation of (1)-(3), so solving (4)-(9) equates to solving the original QKP formulation. This is especially beneficial since now the continuous relaxation of (4)-(9) can be applied to obtain an over-estimate of the solution to the original problem.

An approach for solving (1)-(3) first considers instead a relaxation-and-penalization related problem, whereby a non-negative penalty term is added to the original objective function following the relaxation of a complicating constraint. Particularly, if one relaxes the capacity constraint and penalizes its violation using a penalty term $\lambda \in\mathbb{R}_{\geq 0}$, the following, so called, \textit{Lagrangian relaxation}

\begin{equation}
\mathcal{L}(\mathbf{x},\lambda) = \sum_{i=1}^{n}\sum_{j=1}^{n}p_{ij}x_{i}x_{j} + \lambda \left(C - \sum_{i=1}^{n}w_{i}x_{i}\right) 
\end{equation}

is obtained, in which $\lambda$ is referred to as a *Lagrange multiplier*. 

To obtain an approximation for the original problem, it is then necessary to consider solving

\begin{equation}
\text{maximize }\mathcal{L}(\mathbf{x},\lambda),\ \text{subject to } \mathbf{x}\in\mathbb{B}^{n}.
\end{equation}

Taking the minimum over $\lambda\in \mathbb{R}_{\geq 0}$ in the preceding expression (11) will ultimately yield the solution to QKP, though minimization of (11) is with respect to a non-smooth objective function due the presence of integer variables, so standard methods such as gradient ascent or Newton's method cannot be applied outright in this context. Instead, a basic method which may be used for maximizing is the so called *subgradient method*, whereby subgradients 

\begin{equation}
\psi(\mathbf{x}) = C - \sum_{i=1}^{n}w_{i}x_{i}
\end{equation}

are introduced, and an approach similar to gradient ascent is taken by iteratively considering multiplier updates

\begin{equation}
\lambda^{k+1} = \lambda^{k}+s^{k}\psi(\mathbf{x}^{k})
\end{equation}

for all $k\in \mathbb{Z}_{\geq 0}$. Within each multiplier update, a previous solution $\mathbf{x}$ is obtained by solving (11), and subgradients are scaled according to a positive steps size $s^{k}$, which can be defined in multiple ways. Particularly, in what follows, $s^{k} = s^{0}$ for some $s^{0}\in\mathbb{R}$ for all $k$. The benefit of considering this approach is that, while solving (4)-(9) directly using common methods based on branch-and-bound in the worst case are exponential, solving instead with the subgradient method may be less computationally demanding within this context, especially for difficult problems of reasonably large size.  

% The solution quality of solving the relaxed problem is comparable to solving the McCormick relaxation with binary restriction lifted, however the computational effort of solving the relaxed problem in place of the McCormick relaxation is reduced significantly. 

## Method
\label{method}
By considering $r \in [C]$, $k\in [n]$, the original problem is restricted and written as:

\begin{equation}
\max\left\\{\sum_{i=1}^{k}\sum_{j=1}^{k}p_{ij}x_{i}x_{j} : \sum_{i=1}^{k}w_{i}x_{i} = r,\ \mathbf{x}\in\mathbb{B}^{k}\right\\},
\end{equation}

where $k$ denotes the current item's index and $r$ denotes the current amount of remaining space in the knapsack when considering the $k$th item. Then, similar to before, a subgradient approach is considered, with the restricted problem (7) having restricted Lagrangian relaxation subproblems 

\begin{equation}
\max\left\\{\sum_{i=1}^{k}\sum_{j=1}^{k}p_{ij}x_{i}x_{j} + \lambda\left(r - \sum_{i=1}^{k}w_{i}x_{i}\right) : \mathbf{x}\in\mathbb{B}^{k},\ \lambda\in \mathbb{R}\right\\}.
\end{equation}

Let the triple $\mathbf{y} = (k,r,S)$ denote the current state at the $k$th item with capacity used, $r$, and set of indices of currently included knapsack items $S$. The immediate profit earned by considering $\mathbf{y}$ and $\lambda$ is defined as in \cite{Fomeni-Letchford} using 

\begin{equation}
g(\mathbf{y},u) = p_{kk} + 2\sum_{j\in S\setminus \\{k\\}}p_{kj},
\end{equation}

which is the profit of including item $k$ with capacity used $r$. A viable rollout approach introduces approximate costs-to-go function 

\begin{equation}
\tilde{J}(\mathbf{y}) = \max\left\\{\sum_{i=1}^{k}\sum_{j=1}^{k}p_{ij}x_{i}x_{j} + \lambda\left(r - \sum_{i=1}^{k}w_{i}x_{i}\right) : \mathbf{x}\in\mathbb{B}^{k},\ \lambda\in \mathbb{R}\right\\}
\end{equation}

which is an upper bound on (7), so that approximate $Q-$factors 

\begin{equation}
\tilde{Q}(\mathbf{y},u) = g(\mathbf{y},u) + \tilde{J}(\mathbf{y}),
\end{equation}

with $u \in \mathcal{U}(\mathbf{y})=:\\{0,1\\}$ such that $u = 1$ if item $k$ is included in the knapsack while $w_{k}\leq r$, and $u = 0$ otherwise. Using the aforementioned ingredients, a proposed rollout algorithm is therefore described in the following pseudocode. 


$$
\begin{array}{ll}
\hline\hline
\textbf{Rollout for QKP} &\\
\hline
0: & \text{Initialize }\ \lambda^{0},\ s^{0},\ S\leftarrow [n],\ r\leftarrow C\\
1: & \textbf{for}\ k\in [n]\ \textbf{do} \\
2: & \hspace{.75cm} \hat{S} = \left\{i\in S: w_{i}\leq r\right\}\\
3: & \hspace{.75cm} \tilde{i} = \text{arg}\max_{u\in\mathcal{U}(\hat{\mathbf{y}})}\tilde{Q}(\hat{\mathbf{y}})\\
4: & \hspace{.75cm} \mathbf{x}^{k} = \text{arg}\max_{\mathbf{x}} \tilde{J}(\hat{\mathbf{y}})\\
5: & \hspace{.75cm} g(\mathbf{x}^{k}) = r-\sum_{i=1}^{k}w_{i}x_{i}\\
6: & \hspace{.75cm} \lambda^{k+1} = \lambda^{k} + s^{0}g(\mathbf{x}^{k}) \\
7: & \hspace{.75cm} S = S\setminus\left\{\tilde{i}\right\},\ r = r - w_{\tilde{i}} \\
8: & \hspace{.75cm} \textbf{if}\ r = 0\ \text{or}\ S = \varnothing\\
9: & \hspace{1.25cm} \textbf{return}\ \sum_{i\in S\setminus[n]}\sum_{j\in S\setminus[n]}p_{ij}\\
10: & \hspace{.75cm} \textbf{end}\\
11: & \textbf{end} \\
\hline\hline
\end{array}
$$

Some remarks with respect to the proposed rollout algorithm are in order. First of all, it is important to note that in the **Rollout for QKP** algorithm shown in the preceding, $\hat{\mathbf{y}} = (k,r,\hat{S})$, with $\hat{S}\subseteq S$ denoting the set of feasible item indices for the current state. Second, lines three and four are performed synchronously, for which computation of $\tilde{i}$ is performed and the value $\mathbf{x}^{k}$ is simultaneously attained. Third, it is clear that the algorithm performance is largely dependent on the difficulty of the embedded optimization problem conducted on line four. Therefore, it is important that a function which is efficiently computed be defined as the approximate cost-to-go function to ensure adequate performance with respect to computational speed. Finally, given the wide applicability of subgradient methods applied to non-convex functionals and rollout to combinatorial problems, the **Rollout for QKP** algorithm is fairly general, and slight modification to the algorithm may still be seen as fruitful for approximating the solutions of other MINLPs. With respect to this latter point, however, it is noted that generally a limitation of the subgradient method is that it can under perform when compared to other techniques for optimizing non-smooth functionals. Future work may instead introduce alternative means for updating either the multipliers or $\mathbf{x}^{k}$ to further improve performance of the proposed algorithm. Alternative techniques pertaining to the former may explore using bundle methods in place of traditional subgradient methods, for instance.

### Data Generation
Data is generated as normally done within the context of QKP, and follows closely the description provided in \cite{Fomeni-Letchford}. Precisely, the profits, 

\begin{equation}
p_{ij} \sim \begin{cases}0 & \text{with probability } 1-\Delta, \\\\ \mathcal{U}\\{1,\ldots,100\\} & \text{with probability } \Delta\end{cases}
\end{equation}

for each $i=1\ldots,n$ and $j=1,\ldots,n$, where $\Delta$ is referred to as the *density*. The density, $\Delta$, is particularly important for determining the difficulty of the problem instance in consideration. For larger values of $\Delta$, the impact of its effects is observed through the objective function, in which a larger $\Delta$ may yield an objective function with greatly many more terms. Consequently, this entails that there more likely will be many interactions that will need to be considered when optimizing, therein resulting in a significantly more challenging problem. On the other hand, the remaining data $w_{i} \sim \mathcal{U}\\{1,\ldots,n\\}$ for each $i$, whereas $C \sim \mathcal{U}\\{n,\ldots,\sum_{i=1}^{n}w_{i}\\}$.

## Numerical Results
Comparison was made between the proposed rollout algorithm with base policy as defined in **Section 3**, and by using CPLEX version 1.0.3 to solve the original problem directly. Additionally, a further comparison between the proposed rollout algorithm and solving (4)-(9) directly using CPLEX was made. For each comparison, numerical experiments were performed using Julia on an M1 Macbook Pro with 16 GB of RAM. Data were selected according to **Section 4**, with step size $s^{0} = 1$ throughout, total number of items and density being considered in the CPLEX comparison for (1)-(3) being $n\in \\{50,100,150\\}$ and $\Delta \in \\{0.8,0.65,0.5\\}$, respectively, and $n\in \\{50,100,200\\}$ and $\Delta \in \\{0.8,0.65,0.8\\}$ for the case when CPLEX was applied to solve (4)-(9). Default parameters for CPLEX are used for all numerical experiments, with the exception of the time limit in the comparison against solving (4)-(9) using CPLEX. In this comparison, a time limit of 600 seconds was imposed.  

To compare performance, a *gap* is reported for the proposed rollout method which depends on the solution obtained by CPLEX in each case. Instead of using a gap to determine performance of the rollout method in the traditional sense, the idea is to compare the solution obtained by the proposed rollout method with the solution obtained by the exact method used by CPLEX. Particularly, the gap reported is computed according to

\begin{equation}
\texttt{Gap} = \frac{|z^{r}-z^{c}|}{z^{c}},
\end{equation}

where the objective value for rollout, $z^{r}$, and the objective value for CPLEX, $z^{c}$, are acquired at termination throughout. Seeing as a time limit is imposed in the McCormick case, the average gap across 10 runs of solving is reported, as opposed to when CPLEX is used to solve (1)-(3) directly. The reason for this inclusion is a consequence of the fact that CPLEX may not terminate within the prescribed time limit, as was observed on several runs during numerical experiments. Regardless, across all comparisons made, numerical values such as the number of seconds needed for termination and gap at termination, are reported computing the arithmetic mean over 10 runs for each method. 

<table>
  <caption>Table 1: QKP comparison between the proposed rollout method and CPLEX for varying number of available items and densities.</caption>
  <tr>
    <td colspan = "2"> </td> <td colspan ="2"> <strong>Rollout</strong> </td> <td colspan ="1"> <strong>CPLEX</strong> </td> 
  </tr>
  <tr>
    <td> $n$ </td> <td> $\boldsymbol{\Delta}$ </td> <td><strong>Seconds</strong> </td> <td><strong>Gap</strong></td> <td><strong>Seconds</strong></td>
  </tr>
  <tr>
    <td>50</td> <td>0.8</td> <td>2.985</td> <td>0.089</td> <td>35.346</td> 
  </tr>
  <tr>
    <td>100</td> <td>0.65</td> <td>64.284</td> <td>0.058</td> <td>147.883</td> 
  </tr>
  <tr>
    <td>150</td> <td>0.5</td> <td>57.799</td> <td>0.071</td> <td>121.692</td>
  </tr>
</table>

<table>
    <caption>Table 2: QKP comparison between the proposed rollout method and McCormick for varying number of available items and densities.</caption>
  <tr>
    <td colspan = "2"> </td> <td colspan ="2"> <strong>Rollout</strong> </td> <td colspan ="2"> <strong>CPLEX</strong> </td> 
  </tr>
  <tr>
    <td> $n$ </td> <td> $\boldsymbol{\Delta}$ </td> <td><strong>Seconds</strong> </td> <td><strong>Gap</strong></td> <td><strong>Seconds</strong></td> <td><strong>Gap</strong></td>
  </tr>
  <tr>
    <td>50</td> <td>0.8</td> <td>4.139</td> <td>0.104</td> <td>1.147</td> <td>0.01</td> 
  </tr>
  <tr>
    <td>100</td> <td>0.65 </td> <td>16.562</td> <td>0.077</td> <td>14.351</td> <td>0.01</td>  
  </tr>
  <tr>
    <td>200</td> <td>0.8</td> <td>206.144</td> <td>0.108</td> <td>342.467</td> <td>0.383</td> 
  </tr>
</table>

From **Table 1** it is seen that the rollout approach consistently outperforms CPLEX in terms of computational speed, while simultaneously obtaining similar quality solutions for each instance considered. However, when comparing rollout with the same moderately sized instances of QKP to the McCormick solution, it is instead observed that CPLEX outperforms rollout on average. Because the improvement over rollout is seemingly minor for the $n=50$ and $n=100$ cases, a comparison was further made for a large and difficult instance to see if McCormick's solution with CPLEX scales. In doing so, it was observed that, on average, rollout instead outperformed McCormick, with the variance of both the final solution time between solutions being much larger for McCormick than rollout. In particular, there were many instances in the $n=200$ case where McCormick reached the time limit before admitting a global solution, whereas rollout consistently terminated with a comparable solution to McCormick with around 200 seconds on average. 

## Conclusion
As discussed in the previous section, rollout is comparable to solving (4)-(9) with CPLEX for QKP instances of moderate size and density, to large density. When considering large and difficult QKP instances, it is clear that rollout scales much better than solving (4)-(9) with CPLEX. Thus, rollout appears to be a viable option for a scalable algorithm which may be used to solve large-scale QKP instances. Given the clear improvement in computational speed, accuracy of the proposed approach could potentially be further improved by employing a multi-step lookahead rollout approach. Furthermore, given that McCormick outperforms rollout in some instances, a different base policy could be attempted in later work to improve the results observed. For instance, a potential base policy which may provide additional improvement would consider solving the LP relaxation of (4)-(9). Future work should then compare the performance of the proposed approach against other well-known approaches such as those introduced in {% cite Fomeni-Letchford %} and {% cite FENNICH2024102 %}, as well as against the approach in which subgradient methods or the LP relaxation of (4)-(9) are used. A more extensive study across varying instances could also be conducted to provide more evidence toward the efficacy of each proposed approach.    

## References

{% bibliography --cited %}
