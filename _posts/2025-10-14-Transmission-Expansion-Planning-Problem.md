---
layout: post
---

# Introduction 


## Transmission Expansion Planning Model

<table> 
  <caption>Table 1: Variable, parameter, and set descriptions for TEP.</caption>
  <tr>
    <td> <strong>Symbol</strong> </td> <td><strong>Description</strong> </td> 
  </tr>
  <tr>
    <td colspan = "2"> Sets </td> 
  </tr>
  <tr>
    <td> $\mathcal{G}$ </td> <td> Set of generators </td> 
  </tr>
  <tr>
    <td> $\mathcal{E}^{\nu}$ </td> <td> Set of new lines </td>
  </tr>
  <tr>
    <td> $\mathcal{E}^{\epsilon}$ </td> <td> Set of existing lines </td>
  </tr>
  <tr>
    <td> $\mathcal{B}$ </td> <td> Set of buses </td>
  </tr>
  <tr>
    <td colspan = "2"> Parameters </td>
  </tr>
  <tr>
    <td> $C_{g}$ </td> <td> Marginal cost for generator $g\in \mathcal{G}$ </td>
  </tr>
  <tr>
    <td> $C_{k}^{\text{newline}}$ </td> <td> Cost for new line $k\in \mathcal{E}^{\nu}\cup \mathcal{E}^{\epsilon}$ </td>
  </tr>
  <tr>
    <td> $P_{g}^{\max}$ </td> <td> Maximum production level for generator $g\in \mathcal{G}$ </td>
  </tr>
  <tr>
    <td> $F_{k}$ </td> <td> Thermal limit of line $k\in \mathcal{E}^{\nu} \cup \mathcal{E}^{\epsilon}$ </td>
  </tr>
  <tr>  
    <td> $B_{k}$ </td> <td> Susceptance on line $k\in \mathcal{E}^{\nu} cup \mathcal{E}^{\epsilon}$ </td>
  </tr>
  <tr>  
    <td> $d_{n}$ </td> <td> Power demand at bus $n\in\mathcal{B}$ </td>
  </tr>
  <tr>
    <td colspan = "2"> Variables </td>
  </tr>
  <tr>
    <td> $\delta_{n}^{r}$ </td> <td> Voltage angle at receiving bus $r\in\mathcal{B}$ </td>
  </tr>
  <tr>
    <td> $\delta_{n}^{s}$ </td> <td> Voltage angle at sending bus $r\in\mathcal{B}$ </td>
  </tr>
  <tr>
    <td> $P_{k}$ </td> <td> Flow on line $k\in\mathcal{E}^{\nu}\cup \mathcal{E}^{\epsilon}$, $k$ defined from $s\in \mathcal{B}$ to $r\in\mathcal{B}$ </td>
  </tr>
  <tr>
    <td> $P_{g}$ </td> <td> Real power output of generator $g\in\mathcal{G}$ </td>
  </tr>
  <tr>
    <td> $w_{k}$ </td> <td> Decision to install line $k\in\mathcal{E}^{\nu}$ </td>
  </tr>
</table>

[comment]A common formulation of TEP is as follows.... 
[comment]$$
[comment]  \begin{alignat*}{1}
[comment]    \text{minimize }\ & \sum_{g}P_{g}C_{g} + \sum_{k}C_{k}^{\text{newline}}w_{k}, \label{objective}\\ 
[comment]\text{subject to}\ & 0\leq P_{g}\leq P_{g}^{\max},\quad \text{ for all } g\in \mathcal{G}, \\
[comment]& -F_{k}\leq P_{k} \leq F_{k}, \quad \text{ for all } k\in \mathcal{E}^{\epsilon} \\ 
[comment]##& P_{k} = B_{k}[\delta^{r}_{n} - \delta^{s}_{n}],\quad \text{ for all} k\in\mathcal{E}^{\epsilon}, \\
[comment]##&  -F_{k}w_{k}\leq P_{k}\leq F_{k}w_{k}, \quad \text{ for all } k\in \mathcal{E}^{\nu},\\ 
[comment]#& -(1-w_{k})M\leq P_{k}-B_{k}[\delta_{n}^{r}-\delta_{n}^{s}],\ \text{ for all } k\in \mathcal{E}^{\nu},\\
[comment]#& P_{k}-B_{k}[\delta_{n}^{r}-\delta_{n}^{s}] \leq (1-w_{k})M,\ \text{ for all} k\in \mathcal{E}^{\nu},\\ 
[comment]#& \sum_{k=n^{s}}P_{k} - \sum_{k=n^{r}}P_{k} = \sum_{g=n}P_{g} - d_{n},\quad \text{ for all } n\in \mathcal{B}\\ 
[comment]#& w_{k} \in \{0,1\},\quad \text{ for all } k\in \mathcal{E}^{\nu},
[comment]#  \end{alignat*}
[comment]#$$

After some magic, we arrive at instead: 
$$
\begin{alignat}{1}
\text{minimize }\ & \sum_{g}P_{g}C_{g} + \sum_{k}C_{k}^{\text{newline}}w_{k}, \label{objective}\\ 
\text{subject to}\ & 0\leq P_{g}\leq P_{g}^{\max},\quad \forall g\in \mathcal{G}, \\
& -F_{k}\leq P_{k} \leq F_{k}, \quad \forall k\in \mathcal{E}^{\epsilon} \\ 
& P_{k} = B_{k}[\delta^{r}_{n} - \delta^{s}_{n}],\quad \forall k\in\mathcal{E}^{\epsilon}, \\
&  -F_{k}w_{k}\leq P_{k}\leq F_{k}w_{k}, \quad \forall k\in \mathcal{E}^{\nu},\\ 
& -(1-w_{k})M\leq P_{k}-B_{k}[\delta_{n}^{r}-\delta_{n}^{s}],\ \forall k\in \mathcal{E}^{\nu},\\
& P_{k}-B_{k}[\delta_{n}^{r}-\delta_{n}^{s}] \leq (1-w_{k})M,\ \forall k\in \mathcal{E}^{\nu},\\ 
& \sum_{k=n^{s}}P_{k} - \sum_{k=n^{r}}P_{k} = \sum_{g=n}P_{g} - d_{n},\quad \forall n\in \mathcal{B}\\ 
& w_{k} \in \{0,1\},\quad \forall k\in \mathcal{E}^{\nu},
\end{alignat}
$$

where $M$ is often chosen to be large enough so that the polyedron defined by the feasible region permits high quality solutions without impacting solution times. One possible strategy for selecting $M$ is to do so in a fashion which ensures that the constraints involving $M$ define facet defining inequalities for the feasible region described by TEP. By choosing $M$ such that the preceding is true, it would necessarily follow that the resulting $M$ would be the minimum $M$ needed to acquire high-quality solutions, while still obtaining benefits from the provided formulation. Accomplishing this is, however, in itself very challenging to do, as it . 

In the above model, since $P_{g}$ is a variable describing the real power output from generator $g\in \mathcal{G}$, and $w_{k}$ is the binary decision pertaining to the installation of new line $k\in\mathcal{E}^{\nu}$, the objective function (1) calculates the total cost of installing the new line $k$ while simultaneously considering the total cost of MW production for generator $g$. In addition, (2) ensures that each generator $g\in\mathcal{G}$ has an upper limit on the amount of power it may produce, so as to not exceed its total capacity, while also enforcing only non-negative amounts of power is produced by each generator. Similarly, transmission lines must also adhere to their rated thermal limit. To enforce this, (3) limits power flow, $P_{k}$, for each existing transmission line $k\in\mathcal{E}^{e}$ so that maximum flow capacity, $F_{k}$, is not exceeded. Power flow $P_{k}$ on each line is then further modeled through the linear approximation of DC power flow (4), which utilizes the susceptance of line $k$ and differences of phase angle for each bus $n$ to (more detail here). In contrast to (3), however, which describes the thermal limit of existing lines, (5) enforces thermal limit ratings $F_{k}$ to be satisfied for new line $k\in \mathcal{E}^{k}$, only if $k$ is installed (or, equivalently, when $w_{k} = 1$). It should then be the case that (9) indicates that line $k\in\mathcal{E}^{\nu}$ is to be installed provided that $w_{k}=1$, whereas line $k$ is not to be installed should instead $w_{k}=0$. Moreover, whenever the latter is true, it necessarily follows from (5) that $P_{k}=0$. Normally, however, TEP also considers (bilinear terms), which increases the complexity of solving this model. Due to the difficulties that may arise when attempting to encorporate non-convexities into a mathematical program, measures were taken to reformulate the constraints which regulate the line flow behavior according to Kirchoff's second law,

$$
  P_{k} - B_{k}w_{k}(\delta_{n}^{r} - \delta_){n}^{s}) = 0,\ \text{ for each } k \in \mathcal{E}^{\nu}
$$

while further ensuring that the flows on each line to be installed, $k\in \mathcal{E}^{\nu}$, are with their specified bounds, $F_{k}$. To this end, a linear formulation was acquired to obtain (6)-(7), whereby Kirchoff's second law was initially expressed in disjunctive form. Lastly, nodal power balance is ensured through (8). The ultimate goal of solving (1)-(10) is, therefore, to minimize costs of operations and installations subject to the physical constraints previously outlined. 

## Brief Survey of Prior Literature 

The Transmission Planning Problem seeks to find the least cost of operating and installing new transmission lines, with the goal of meeting future demand for a given network instance. Due to the highly non-linear and non-convex nature of TEP, coupled with its discreteness, solving TEP in its AC form is, in general, very hard, with larger network sizes posing significant challenges in practice. More precisely, it is known that a solution to the deterministic TEP is the same as a minimum cost Steiner tree, and therefore TEP is NP-hard {% cite ? %}. Consequently, many authors which study TEP have imposed reformulations or relaxations as a means of tackling many of the difficulties presented by its original AC formulation. Several such modifications are also utilized in (1)-(10) above, for instance, when considering the DC linear approximation and reformulation of bilinear terms in Kirchoff's law to include large penalty terms. These modifications ...  

# Results

## Assumptions and Procedures

## Numerical Experiments

# Conclusion
