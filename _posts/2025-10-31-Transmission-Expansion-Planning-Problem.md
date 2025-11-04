---
layout: post
---

Historically, electrical grid infastructure, particularly in many parts of North America, has been developed with the expectation that energy load will be . With the increasing adoption of large language models and electric transportation in recent times (want this to be in large part due to AI since that's the buzz right now), both of which requiring vast amounts of (electric) resources, current electrical grid infrastructure will likely be tested without accommodating for accompanying increases in electrticty demand, especially at peak load times. As a result, grid designers (?) will need to anticipate the increase in forecasted demnand and ensure that future development focuses on the robustness of grid infastructure under heavy loads. This is especially the case given that OpenAI has reported plans to develop 10 large data centers, which are estimated to require 50 GW of power, while Apple and Google have announced similar plans to upgrade their current computing resources dedicated to AI, according to {% cite ? %}. While, many data centers appear to be resorting to generating their own energy, there is likely undoublty a factor of their construction which will require further development of our current grid infastructure. To account for the projected increase in demand, transportation and generation of energy will need to be accounted for. Provided that generation is already able to meet increased, grid design should therefore focus on transmission expansion to prepare for . 

[comment]: https://www.landapp.com/post/the-history-of-the-power-grid-in-the-united-states
[comment]: https://www.technologyreview.com/2025/05/20/1116327/ai-energy-usage-climate-footprint-big-tech/
[comment]: https://www.microsoft.com/en-us/research/wp-content/uploads/2024/03/GPU_Power_ASPLOS_24.pdf
[comment]: https://huggingface.co/AIEnergyScore
[comment]: https://www.energy.gov/articles/doe-releases-new-report-evaluating-increase-electricity-demand-data-centers
[comment]: https://spectrum.ieee.org/the-ev-transition-explained-2658463709
[comment]: https://www.landapp.com/post/the-history-of-the-power-grid-in-the-united-states
[comment]: https://www.cfr.org/backgrounder/how-does-us-power-grid-work

## Transmission Expansion Planning Model

In an abstract sense, TEP is modeled as an undirected graph $(V,E)$ with edge set $E$ and set of vertices $V$ corresponding to lines and buses within the electrical network, respectively. As TEP's goal is to determine how many, and which lines from a set of candidate lines should be installed to meet predicted demand, the edge set $E$ will be partitioned so as to distinguish edges that can potentially be installed from those which are already installed. Table 1 below more precisely describes parameters, variables, and sets which are used to formalize the transmission of electricity across grid instances in the context of TEP.   

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
    <td> $C_{k}^{\text{newline}}$ </td> <td> Cost for new line $k\in \mathcal{E}^{\nu}\sqcup \mathcal{E}^{\epsilon}$ </td>
  </tr>
  <tr>
    <td> $P_{g}^{\max}$ </td> <td> Maximum production level for generator $g\in \mathcal{G}$ </td>
  </tr>
  <tr>
    <td> $F_{k}$ </td> <td> Thermal limit of line $k\in \mathcal{E}^{\nu} \sqcup \mathcal{E}^{\epsilon}$ </td>
  </tr>
  <tr>  
    <td> $B_{k}$ </td> <td> Susceptance on line $k\in \mathcal{E}^{\nu} \sqcup \mathcal{E}^{\epsilon}$ </td>
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
    <td> $P_{k}$ </td> <td> Flow on line $k\in\mathcal{E}^{\nu}\sqcup \mathcal{E}^{\epsilon}$, $k$ defined from $s\in \mathcal{B}$ to $r\in\mathcal{B}$ </td>
  </tr>
  <tr>
    <td> $P_{g}$ </td> <td> Real power output of generator $g\in\mathcal{G}$ </td>
  </tr>
  <tr>
    <td> $w_{k}$ </td> <td> Decision to install line $k\in\mathcal{E}^{\nu}$ </td>
  </tr>
</table>

Using the sets, variables, and paramaters previously outlined in Table 1, TEP is often mathematically written, in DC form, as follows:

$$
\begin{alignat}{1}
\text{minimize }\ & \sum_{g\in\mathcal{G}}P_{g}C_{g} + \sum_{k\in\mathcal{E}^{\nu}}C_{k}^{\text{newline}}w_{k},\\ 
\text{subject to}\ & 0\leq P_{g}\leq P_{g}^{\max},\quad \text{ for all } g\in \mathcal{G}, \\
& -F_{k}\leq P_{k} \leq F_{k}, \quad \text{ for all } k\in \mathcal{E}^{\epsilon} \\ 
& P_{k} = B_{k}[\delta^{r}_{n} - \delta^{s}_{n}],\quad \text{ for all } k\in\mathcal{E}^{\epsilon}, \\
&  -F_{k}w_{k}\leq P_{k}\leq F_{k}w_{k}, \quad \text{ for all } k\in \mathcal{E}^{\nu},\\ 
& -(1-w_{k})M\leq P_{k}-B_{k}[\delta_{n}^{r}-\delta_{n}^{s}],\ \text{ for all } k\in \mathcal{E}^{\nu},\\
& P_{k}-B_{k}[\delta_{n}^{r}-\delta_{n}^{s}] \leq (1-w_{k})M,\ \text{ for all } k\in \mathcal{E}^{\nu},\\ 
& \sum_{k=n^{s}}P_{k} - \sum_{k=n^{r}}P_{k} = \sum_{g=n}P_{g} - d_{n},\quad \text{ for all } n\in \mathcal{B}\\ 
& w_{k} \in \{0,1\},\quad \text{ for all } k\in \mathcal{E}^{\nu},
\end{alignat}
$$

where $M$ is often chosen to be large enough so that the polyedron defined by the feasible region permits high quality solutions without impacting solution times. One possible strategy for selecting $M$ is to do so in a fashion which ensures that the constraints involving $M$ define the tightest-fitting inequality for the feasible region described by TEP. By choosing $M$ such that the preceding is true, it would necessarily follow that the resulting $M$ would need to be the minimum $M$ needed to acquire high-quality feasible solutions, while still providing benefits from the provided formulation. 

In the above model, since $P_{g}$ is a variable describing the real power output from generator $g\in \mathcal{G}$, and $w_{k}$ is the binary decision pertaining to the installation of new line $k\in\mathcal{E}^{\nu}$, the objective function (1) calculates the total cost of installing the new line $k$ while simultaneously considering the total cost of power production (MW) for generator $g$. In addition, (2) ensures that each generator $g\in\mathcal{G}$ has an upper limit on the amount of power it may produce, so as to not exceed its total capacity, while also enforcing only non-negative amounts of power is produced by each generator. Similarly, transmission lines must also adhere to their rated thermal limit. To enforce this, (3) limits power flow, $P_{k}$, for each existing transmission line $k\in\mathcal{E}^{\epsilon}$ so that maximum flow capacity, $F_{k}$, is not exceeded. Power flow $P_{k}$ on each line is then further modeled through the DC power flow equations (4), which utilizes the susceptance of line $k$ and differences of phase angles for each sending bus $n^{s}$ and receiving bus $n^{r}$. In contrast to (3), however, which describes the thermal limit of existing lines, (5) enforces thermal limit ratings $F_{k}$ to be satisfied for new line $k\in \mathcal{E}^{k}$, only if $k$ is installed (or, equivalently, when $w_{k} = 1$). It should then be the case that (9) indicates that line $k\in\mathcal{E}^{\nu}$ is to be installed provided that $w_{k}=1$, whereas line $k$ is not to be installed should instead $w_{k}=0$. Moreover, whenever the latter is true, it necessarily follows from (5) that $P_{k}=0$. Normally, however, TEP also considers bilinear terms associated with Kirchoff's second law, which significantly increases the complexity of practically solving the TEP mathematical programming model. Due to the difficulties that may arise when attempting to encorporate non-convexities into a mathematical program, measures were taken to reformulate the constraints which regulate the line flow behavior according to Kirchoff's second law, 

$$
  P_{k} - B_{k}w_{k}(\delta_{n}^{r} - \delta_{n}^{s}) = 0,\ \text{ for each } k \in \mathcal{E}^{\nu}, 
$$

while further ensuring that the flows on each line to be installed, $k\in \mathcal{E}^{\nu}$, are within their specified bounds, $F_{k}$. To this end, a linear reformulation of (10) was constructed to obtain (6)-(7), whereby Kirchoff's second law (10) is expressed in disjunctive form. Lastly, nodal power balance is ensured through (8). The ultimate goal of solving (1)-(9) is, therefore, to minimize costs of operations and installations subject to the physical constraints previously outlined. 

[comment]: Mathematically, TEP can be thought of as of a generalization of the minimum spanning tree problem,
[comment]: and is a special case of more general capacitated network design problems,
[comment]: such as those used for telecommunication network planning, route planning, to name a few (sources). 
[comment]: (not sure if this should be said here ... )

## Brief Background 

The Transmission Expansion Planning Problem considered above seeks to find the least cost of operating and installing new transmission lines, with the goal of meeting future demand for a given network instance. While the aforentioned model makes use of several simplifying assumptions to accomplish this goal, in general, however, due to the highly non-linear and non-convex nature of TEP, coupled with its discreteness, solving TEP in its AC form is, in general, very difficult, with larger network sizes posing significant challenges in practice. Moreover, it is known that a solution to the deterministic TEP is the same as a minimum cost Steiner tree with more than two terminal vertices, and therefore TEP is NP-hard {% cite Moulin2010 %} (see figure 1). 

Many authors which study TEP have employed reformulations or relaxations as a means of tackling many of the difficulties presented by its original AC formulation. For instance, through relaxation of the binary variables, as done in (citations here); . Several common modifications are also utilized in (1)-(9) above, specifically when considering the DC linear approximation and reformulation of bilinear terms in Kirchoff's law to include large penalty terms, $M$. By removing these nonlinearities, in particular, TEP can be formulated as a mixed-integer linear program (MILP) so that traditional exact methods such as Branch and Bound (BnB), Branch and Cut (BnC), or some variation of these two standard methods can be applied. Typically, reformulations are an effective method in global optimization, especially when dealing with integer programs that include nonlinearities. Unfortunately, however, in the context of TEP there are not many effective reformulation techniques for the AC power flow constraints (), which include trignometric functions that are difficult for many solvers to handle. As a result, it is common practice to approximate AC power flow equations linearly using DC power flow, as done (above) in (the provided model with eq numbers) (citation maybe instead?).  

[image here comparing a minimum steiner tree and a solution to TEP] 

In TEP's MILP form, there have been several methods from BnB and BnC which have been developed for solving .  

# Results

## Assumptions and Procedures

There are several assumptions, particularly with respect to not only the modeling aspect but also the data used for defining the instance considered, are considered. While, arguably the Garver-6 and IEEE-24 test systems have been the most popular instances for testing TEP methodology according to (cite paper), numerical testing will instead consider the IEEE-RTS-96 test system provided by {% cite  %}. For context, the IEEE-RTS-96 data set was originally introduced in {% cite %}, as a replacement for previously used benchmarking instances in the context of network reliability analysis. 

The IEEE-RTS-96 test system includes 73 buses, ? generators, and 121 existing lines. Because TEP considers the installation of new lines, adding new lines ...  

Some important modifications ... It is assumed, firstly, that over the next 10 years the load at each bus $n\in\mathcal{B}$ will be tripled, with the peak load being the only factor used to load the transmission lines (??). Moreover, it is also assumed that the load will remain constant. To service the increased load at each bus, it is further assumed that the number of generators in the system is tripled. 

With regard to generation, all generators are assumed to be committed, however start-up, shutdown, and no-load costs are not considered. For new transmission lines, each new line is assumed to be installed parallel to existing transmission lines, with each of new line considered having similar parameters ($B_{k}$) as the lines they are parallel to. It then follows, from this construction, that candidate lines should have the same sending bus, $s$, and receiving bus, $r$, as the lines they are parallel to. (more context leading to next sentence). Newly installed lines with 230 kV rating will incur a cost of $900,000 per mile installed, whereas newly installed lines with 138 kV rating will contribute a cost of $400,000 per mile installed. On the other hand, transformers are assumed to have infinite rating and do not require upgrading over the system's lifetime. In total, ?? new lines are considered in the modified data for potential installation. 

For $k $, $C_{k}^{\text{newline}}$ is obtained by multiplying the length of the in which $k$ is parallel to by the cost of installation of the new line, based on the line rating distinction previously described. Moreover, IEEE-RTS-96, as provided by {% cite %} 

## Numerical Experiments

Generating data for the new lines and generators as described in the previous subsection, along with the provided data for the existing lines, generators, and buses, resulted in the final model having $2,023$ constraints (including bounds) and $708$ variables. Of the $708$ variables considered in our formulation, $109$ were binary, while the remaining were continuous.

Solutions were acquired using an M1 MacBook Pro with 16GB of RAM. Solving returns 12 lines installed, with a particular solution instance being: 

$$
\begin{alignat}{1}
\{k \in\mathcal{E}^{\nu} : w_{k}^{\star} = 1\} &= \{11, 13, 14, 28, 41, 51, 52, 53, 85, 86, 87, 97\} \nonumber \\
&=: \mathcal{I}, 
\end{alignat}
$$

in which $k\in\mathbb{Z}$ is used to indicate the index of line $k\in\mathcal{E}^{\nu}$ with orientation from bus $s\in\mathcal{B}$ to bus $r\in\mathcal{B}$. However, solving using CPLEX in place of HiGHS results in an alterative solution

$$
\begin{equation}
\{k \in\mathcal{E}^{\nu} : w_{k}^{\star} = 1\} = \{12, 13, 14, 28, 41, 51, 52, 53, 85, 86, 87, 97\}
\end{equation}
$$

with the same optimal objective value and installation costs, suggesting that the solution obtained by HiGHS and CPLEX may not be unique. Regardless, for each line in $\mathcal{I}$, their respective installation costs $C_{k}^{\text{newline}}$ entail that 

$$
\begin{equation}
\sum_{k\in \mathcal{I}}C_{k}^{\text{newline}} = 1.1500\times10^{8}.
\end{equation}
$$

Therefore, the total contribution to the final cost from installation of new lines is $\$1.1500\times10^{8}$. This indicates that installation of new lines contributed a majority of the cost associated with the optimal objective value, as the optimal cost returned was approximately $\$1.1586\times10^{8}$, with relative optimality gap

$$
\begin{alignat}{1}
\texttt{rel\_gap} &= \frac{|\texttt{upper\_bound}-\texttt{lower\_bound}|}{|\texttt{upper\_bound}|} \\
&= 0,\nonumber
\end{alignat}
$$

where $\texttt{upper\_bound}$ is the primal objective value at iteration $t$, and $\texttt{lower\_bound}$ is the dual objective value at iteration $t$. When calculating optimality gap, tolerance for the optimality gap at the default value of $0.01\%$ for HiGHS and $?\%$ for CPLEX. Total violation of the constraints is negligible at approximately $3.1494\times10^{-14}$, primarily in the binary portion of the solution. In addition, HiGHS requires a total of 7,035 simplex iterations and finds 19 nodes, terminating as \texttt{OPTIMAL} with the aforementioned solution in approximately two seconds. HiGHS computes simplex iterations using the dual simplex method by default, which was left using this option as well (?). 

With the increased load considered, several generators were found to be set to their production limits. Remaining costs from the optimal objective value, totaling 

$$
\begin{equation}
\$1.1586\times10^{8} -\$1.1500\times10^{8} \approx \$8.6\times10^{5},
\end{equation}
$$

are a consequence of the generation output from the updated collection of generators. 

As discussed previously, to keep up with the increase in demand a total of 198 new generators were installed. Despite all generators being assumed to be on and available to service the given load, 21 of the new generators were found to not be using any of their available capacity, while five were using some but not all of their available capacity, and 172 were using their full capacity. However, out of the total 297 generators we considered, $241$ were observed to be using their full capacity, while $12$ were using some but not all of their available capacity, and $44$ were not using any of their available capacity. These results are summarized in Table 2 below.  

<table>
  <caption> Table 2: Generator output for newly added generators and all generators (all, some, or none). </caption>
  <tr>
    <td> <strong> All </strong> </td> <td> <strong> At Least Some </strong></td> <td> <strong> None </strong></td>
  </tr>
  <tr>
    <td columnspan = "3"> <strong> New Generators</strong> </td>
  </tr>
  <tr> 
    <td>172 </td> <td> 5</td> <td> 21 </td>
  </tr>
  <tr>
    <td columnspan = "3"> <strong> All Generators </strong> </td>
  </tr>
  <tr>
    <td> 241</td> <td> 12 </td> <td> 44 </td>
  </tr>
</table

# Conclusion

