# Two-Product Newsvendor Problem with Downward Substitution

A classical **newsvendor / inventory** problem with two product types and **downward substitution** (if the worse type runs out → we issue the better type at the price of the worse one).

Below is a **formal model**, variants of analytical–numerical solutions, and a **practical algorithm** (including simulation and iterative optimization).

Formulas are given in general form (for random $D_1, D_2$, any distributions), followed by common special cases (Poisson, discrete).

---

## 1. Notation and Assumptions

Types:

$$
\[
1 = \text{“better”}, \quad 2 = \text{“worse”}.
\]
$$

Decision variables (integer, nonnegative):

$$
\[
Q_1, Q_2 = \text{ordered/held quantities of each type.}
\]
$$

Random demands:

$$
\[
D_1, D_2
\]
$$

(possibly independent or dependent).

Selling prices:

$$
\[
P_1, P_2.
\]
$$

Wholesale (procurement) costs:

$$
\[
C_1, C_2.
\]
$$

Salvage values:

$$
\[
S_1, S_2,
\]
$$

— revenue from unsold units upon liquidation or disposal.

Holding costs (per unit):

$$
\[
H_1, H_2.
\]
$$

Define net leftover value:

$$
\[
V_i = S_i - H_i
\]
$$

(the net residual value of one unsold unit of type $i$).

---

### Downward Substitution Rule

If

$$
\[
D_2 > Q_2
\]
$$

and there is a surplus of type 1

$$
\[
Q_1 - D_1 > 0,
\]
$$

(i.e., after meeting its own demand $D_1$, some type-1 units remain),  
they may be used to satisfy part of $D_2$.  
In that case, the firm receives the selling price $P_2$ per substituted unit (selling type 1 “as” type 2).

Each unit can be sold at most once, and substitution occurs **after** fulfilling its own primary demand.

Priority: sell type 1 to its own customers $D_1$ first, then use the leftover 
for $D_2$.

---

## 2. Random Profit Function

For given  $Q_1, Q_2$ and a realized scenario $(d_1, d_2)$, the profit is:

$$
\begin{aligned}
\pi(d_1, d_2; Q_1, Q_2)
&= P_1 \min(Q_1, d_1) + P_2 \min(Q_2, d_2) \\
&\quad + P_2 \min\big((Q_1 - d_1)^+, (d_2 - Q_2)^+\big) \\
&\quad - C_1 Q_1 - C_2 Q_2 \\
&\quad + V_1 (Q_1 - d_1)^+ + V_2 (Q_2 - d_2)^+.
\end{aligned}
$$

**Interpretation:**

- The first two terms — direct sales for own demand of each type.  
- The third term — extra revenue from substitution: each leftover type-1 unit used to satisfy excess type-2 demand earns $P_2$.  
- Then we subtract procurement costs.  
- Finally, add salvage/holding residuals $V_i$ from unsold units (this can be negative if $H_i > S_i$).

If you wish to distinguish between “salvage paid only for unused units” or “holding cost incurred throughout the period,” adjust $V_i$ accordingly.

---

## 3. Expected Profit

$$
\Pi(Q_1, Q_2) = \mathbb{E}_{D_1, D_2}[\pi(D_1, D_2; Q_1, Q_2)].
$$

Optimization problem:

$$
\max_{Q_1, Q_2 \in \mathbb{Z}_{\ge 0}} \Pi(Q_1, Q_2).
$$

In practice, $\Pi$ can be computed **analytically** (rarely) or **numerically**.

---

## 4. Useful Expectations (for Discrete $D$)

For discrete random variable $D$,

$$
\mathbb{E}[\min(Q, D)]
= \sum_{k=0}^{Q-1} k\, \Pr(D=k) + Q\, \Pr(D \ge Q).
$$

Similarly,

$$
\mathbb{E}[(Q - D)^+]
= \sum_{k=0}^{Q-1} (Q - k)\, \Pr(D=k)
= Q - \mathbb{E}[\min(Q, D)].
$$

For the substitution term,

$$
\mathbb{E}\!\left[\min\!\left((Q_1 - D_1)^+, (D_2 - Q_2)^+\right)\right],
$$

one may compute via a double sum over $d_1, d_2$ (if supports are small) or via **convolutions** if $D_1, D_2$ are independent.

---

## 5. Common Demand Assumptions

A frequent and convenient assumption is **Poisson demand**:

$$
D_i \sim \text{Poisson}(\lambda_i).
$$

Then convolutions and probabilities are easy to compute and simulate.

If $D$ are large and approximately normal, a normal approximation may be used — but beware of discreteness and nonnegativity issues.

---

## 6. Optimization Approaches

### (A) Exact Numerical Evaluation + Enumeration

If the feasible ranges of $Q_1, Q_2$ are not large (e.g., up to a few hundred), one can:

1. For each $(Q_1, Q_2)$, compute $\Pi(Q_1, Q_2)$ via 2-D summation or integration.  
2. Select the maximum.

This guarantees the **exact optimal** solution.

---

### (B) Simulation-Based Optimization

If analytical computation is infeasible, use Monte-Carlo simulation:

1. Generate many random pairs $(D_1, D_2)$.  
2. For each pair, compute $\pi(D_1, D_2; Q_1, Q_2)$.  
3. Average to estimate $\Pi(Q_1, Q_2)$.  
4. Search over a grid or use iterative heuristics (e.g., coordinate search or gradient-free optimization).

---

### (C) Approximation and Heuristics

For large supports or continuous $D$, use approximations based on:

- **Critical fractile** logic (when substitution probability is small).  
- **Normal approximation** for cumulative demand.  
- **Convexity** properties of $\Pi(Q_1, Q_2)$ for efficient search.

---

## 7. Possible Extensions

- Upward substitution (reverse direction, less common).  
- Multiple (>2) product types.  
- Correlated demands $D_1, D_2$.  
- Capacity constraints or shared storage.  
- Time-dependent replenishment and dynamic programming extensions.

---

### References

- Nahmias, S. (1982). *Perishable inventory theory: A review*. Operations Research.  
- Parlar, M., & Goyal, S. (1984). *Optimal ordering decisions for two substitutable products*.  
- Mahajan, S., & van Ryzin, G. (2001). *Stocking retail assortments under dynamic substitution*. Operations Research.

---






---------------------------------------------------
