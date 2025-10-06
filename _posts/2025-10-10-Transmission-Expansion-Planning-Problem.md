---
layout: post
---

#Introduction 


##Transmission Expansion Planning Model

<table> 
  <caption>Table 1: Variable, parameter, and set descriptions for TEP.</caption>
  <tr>
    <td> <strong>Symbol</strong> </td> <strong>Description</strong> </td> 
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

\begin{alignat}{1}
\text{minimize }\ & \sum_{g}P_{g}C_{g} + \sum_{k}C_{k}^{\text{newline}}w_{k}, \label{objective}\\ % OBJ FNC & cost of  new line (line 1)
\text{subject to}\ & P_{g}\leq P_{g}^{\max},\quad \forall g\in \mathcal{G}, \\ % (line 2)
% ignoring P_g_min that is nonzero (would be a unit commitment problem // not typical)
& -F_{k}\leq P_{k} \leq F_{k}, \quad \forall k\in \mathcal{E}^{\epsilon} \\ % (line 3)
% "Pk should be limited to the range that it is"
& P_{k} = B_{k}[\delta^{r}_{n} - \delta^{s}_{n}],\quad \forall k\in\mathcal{E}^{\epsilon}, \\ % (line 4)
% Pk is equation given based on power flow formulation that we learned
&  -F_{k}w_{k}\leq P_{k}\leq F_{k}w_{k}, \quad \forall k\in \mathcal{E}^{\nu},\\ % (line 5)
% For any new line we have a diff. line flow limit formulation w/ binary variable of wk included
& -(1-w_{k})M\leq P_{k}-B_{k}[\delta_{n}^{r}-\delta_{n}^{s}],\ \forall k\in \mathcal{E}^{\nu},\\
& P_{k}-B_{k}[\delta_{n}^{r}-\delta_{n}^{s}] \leq (1-w_{k})M,\ \forall k\in \mathcal{E}^{\nu},\\ % (line 6)
% Power flor equation ONLY if the line is installed
& \sum_{k=n^{s}}P_{k} - \sum_{k=n^{r}}P_{k} = \sum_{g=n}P_{g} - d_{n},\quad \forall n\in \mathcal{B}\\ % (line 7)
% nodal power balance constraint b_theta formulation of the power flow
& w_{k} \in \{0,1\},\quad \forall k\in \mathcal{E}^{\nu},\\ % (line 8)
& P_{g} \geq 0,\quad \forall g\in \mathcal{G}, \label{power-generation} % (line 9)
\end{alignat}

##Survey of Prior Literature 

#Results

##Assumptions and Procedures

##Numerical Experiments

#Conclusion
