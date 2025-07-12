---
layout: post
---


## Introduction
Mathematical models which include integer variables are ubiquitous in many contexts of significant importance within scientific inquiry. Such problems typically are empirically seen as much more difficult to solve than similar models containing only continuous, real-valued variables. The primary difficulty of models with integer variables is that their solution spaces explodes combinatorially as the number of decisions to be made increases. Despite mathematical programs with integer variables suffering from the issue of combinatorial explosion resulting from the presence of integer variables, in the case of problems with linear objective functions and constraints, or problems referred to as *linear programs* (LPs) and *mixed-integer linear programs* (MILPs), several approaches have been developed for obtaining high-quality solutions very quickly with much success. 

Among the more successful methods specifically for solving MILPs, the most popular of which regularly consider some methodology based on the idea of reducing the feasible region in an attempt to obtain its convex hull, or at the very least an approximation thereof. More generally, however, are integer programs which contain non-nonlinearities either within their objective function or constraints, called \textit{mixed-integer nonlinear programs} (MINLPs). MINLPs are, in general, much more difficult to solve than MILPs. Because of their inherent difficulty, obtaining exact solutions to MINLPs is especially challenging, and typical approaches for obtaining global solutions generally rely on relaxing integrality restrictions and applying a branch-and-bound-like approach to both continuous and integer variables. In contrast to traditional branch-and-bound, additional difficulty with this approach is a consequence of each relaxed problem being a constrained nonlinear program (NLP), which may be non-convex. A common approach for overcoming further difficulty imposed by non-convexity is through the application of convex envelope relaxations, whose goal is to further acquire tight approximations for the original NLP. Branch-and-bound-like methods such as those previously described for MINLPs are regularly restrictive, and empirical performance is often observed to be less than practical for many problems of relevance.

In an attempt to overcome less than ideal performance in terms of computational speed of exact branch-and-bound-like algorithms, heuristics are regularly enlisted as a means of producing approximate solutions very quickly. Some examples of heuristic algorithms for solving MINLPs are those proposed by \cite{Fomeni-Letchford} and \cite{FENNICH2024102}, which both utilize a dynamic programming approach in the context of a binary knapsack problem with nonlinearities. Further examples of more general heuristics, particularly designed for solving MINLPs, include sub-NLP and NLP-diving. Both of these approaches attempt to explore the solution space by exploiting the decrease in difficulty obtained from excluding integral restrictions through either fixing integer variables to a constant numerical value, or entirely removing integerality from the model before solving. Between both of the two aforementioned heuristics, the idea is to iteratively perform the relaxation of integerality restriction or fixing of integer variables until a good quality solution is attained. Effectively, each of two latter-most heuristics result in solving NLP subproblems to achieve approximations to the original problem. 

One additional possible method for solving potentially large-scale MINLPs is to apply approximations within the traditional dynamic programming scheme, thereby taking an approximate dynamic programming (ADP) approach. To the best of the author's knowledge, however, this approach is not common for MINLPs, despite several methods utilizing ADP for MILPs. For instance, cite SECOMANDI20001201) apply neuro-dynamic programming to the vehicle routing problem with stochastic demands, while (cite Bertazzi2012) use rollout to solve the 0-1 knapsack problem. On the other hand, there is at least one instance of rollout being applied to MINLPs, as done in (cite 10147895) for electrical vehicle charging planning. In this document, the goal is to further explore the capability of ADP approaches for solving challenging MINLPs. 


## Background 
Consider the 0-1 quadratic knapsack problem:  





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


## Conclusion
As discussed in the previous section, rollout is comparable to solving (2a)-(2g) with CPLEX for QKP instances of moderate size and density, to large density. When considering large and difficult QKP instances, it is clear that rollout scales much better than solving (2a)-(2g) with CPLEX. Thus, rollout appears to be a viable option for a scalable algorithm which may be used to solve large-scale QKP instances. Given the clear improvement in computational speed, accuracy of the proposed approach could potentially be further improved by employing a multi-step lookahead rollout approach. Furthermore, given that McCormick outperforms rollout in some instances, a different base policy could be attempted in later work to improve the results observed. For instance, a potential base policy which may provide additional improvement would consider solving the LP relaxation of (2a)-(2g). Future work should then compare the performance of the proposed approach against other well-known approaches such as those introduced in \cite{Fomeni-Letchford} and \cite{FENNICH2024102}, as well as against the approach in which subgradient methods or the LP relaxation of (2a)-(2g) are used. A more extensive study across varying instances could also be conducted to provide more evidence toward the efficacy of each proposed approach.    

## References

<!-- {% bibliography --cited %} -->
