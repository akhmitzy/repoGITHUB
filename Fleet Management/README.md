# Exact formulation as an MIP (Mixed-Integer Linear Program) via precomputing profit for each possible $x$

Let at the beginning of the night Jack has $s_1, s_2, \dots, s_I$ cars in $M$ branches $(f_1, f_2, \dots, f_I)$:

$$
\sum_{i=1}^I s_i = S, \quad S > 0
$$

We are interested in how many cars should be in each branch to obtain maximum profit (formula given below).

After (instantaneous — paperwork is ready right away, and transfers are done overnight so that by morning the car is ready to work) relocations, the stock for tomorrow in branch $f_v$ is:

$$
x_v = s_v + \mathrm{perevoz}_v
$$

where $\mathrm{perevoz}_v$ is the number of cars that were (overnight) ordered *to* location $f_v$ (if $\mathrm{perevoz}_v > 0$) or were (overnight) ordered *from* location $f_v$ (if $\mathrm{perevoz}_v < 0$).

The values $s_1, s_2, \dots, s_I$ are obtained by simulating the flow of cars:

1. Branch $f_i$ each day has $1 + \mathrm{Poisson}(\lambda_i)$ rental requests.  
   If a request arrives but there is no car — the request is lost, and all subsequent requests for that day are also lost.
2. If there is a car for the request, the branch earns $p \cdot c$, where:

   $$
   c = 1 + \mathrm{Poisson}(\lambda_{\text{rent}})
   $$

   is the number of days the car is rented, and $p = \$10$.
3. Each car chooses a branch to return to according to a probability matrix $\mathrm{RETURN}(i, j)$ of size $I \times I$, where:

   $$
   \sum_{j=1}^I \mathrm{RETURN}(i, j) = 1
   $$

   and $\mathrm{RETURN}(j, j) = 0.2$, all non-diagonal elements being equal.

The rental duration of each car becomes known in the morning, so by the evening we already know the values $s_1, s_2, \dots, s_I$.

The cost of overnight transfer is $c_{\text{move}} = \$1$ per mile.  
Distances between branches are given by the matrix $D(i, j)$ (with zero diagonal).  
From one branch to another no more than $M$ cars can be relocated overnight.

---

## Idea
The stock $x_i$ at branch $f_i$ can take a finite set of values $k = 0, 1, 2, \dots, K$.  
We precompute for each $k$ the expected rental profit for branch $f_i$:

$$
r_{ik} = p \cdot \mathbb{E}[\min(D_i, k)]
$$

where $D_i$ is the random demand in branch $f_i$.

We introduce binary variables $y_{ik}$ — “in branch $f_i$ there will be exactly $k$ cars” ($y_{ik} = 1$ for the chosen $k$, otherwise $0$).

---

## Model
Maximize the total average profit:

$$
\max \sum_{i=1}^{I} \sum_{k=0}^{K} r_{ik} y_{ik}
- c_{\text{move}} \cdot \left( \sum_{i=1}^I \left| \mathrm{perevoz}_i \right| \right)
$$

Subject to:

$$
\begin{aligned}
&\sum_{k=0}^K y_{ik} = 1, &&\forall i = 1, 2, \dots, I \\
&\sum_{k=0}^K k \cdot y_{jk} = s_j - \mathrm{perevoz}_j, &&\forall j = 1, 2, \dots, I \\
&\sum_{j=1}^I (s_j - \mathrm{perevoz}_j) = S \quad \text{i.e.} \quad \sum_{j=1}^I \mathrm{perevoz}_j = 0 \\
&y_{ik} \in \{0, 1\} \\
&-M - 1 < \mathrm{perevoz}_j < M + 1
\end{aligned}
$$

---

## Parameters
- $I = 6$
- $S = 30$
- $D(i, j)$ — distances between locations $1, 2, 3, 4, 5, 6$ along a straight line, distance between nearest neighbors = $1$
- $M = 5$
- Initial configuration: $s_1 = s_2 = \dots = s_I = 5$
- $\lambda_i = i$ for $i = 2, \dots, 6$ and $\lambda_1 = 8$
- $\lambda_{\text{rent}} = 3$

---

## Goal
Tomorrow Jack starts operations and we need to find:

$$
\{\mathrm{perevoz}_i\}, \quad \text{total average income}.
$$

