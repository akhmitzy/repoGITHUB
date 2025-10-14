# Two-Product Newsvendor Problem with Downward Substitution

A classical **newsvendor / inventory** problem with two product types and **downward substitution** (if the worse type runs out → we issue the better type at the price of the worse one).

Below is a **formal model**, variants of analytical–numerical solutions, and a **practical algorithm** (including simulation and iterative optimization).

Formulas are given in general form (for random $D_1, D_2$, any distributions), followed by common special cases (Poisson, discrete).

---

## 1. Notation and Assumptions

Types:

$$
1 = \text{“better”}, \quad 2 = \text{“worse”}
$$

Decision variables (integer, nonnegative):

$$
Q_1, Q_2 = \text{ordered/held quantities of each type}
$$

Random demands:

$$
D_1, D_2
$$

(possibly independent or dependent)

Selling prices:

$$
P_1, P_2
$$

Wholesale (procurement) costs:

$$
C_1, C_2
$$

Salvage values:

$$
S_1, S_2
$$

— revenue from unsold units upon liquidation or disposal.

Holding costs (per unit):

$$
H_1, H_2
$$

Define net leftover value:

$$
V_i = S_i - H_i
$$

(the net residual value of one unsold unit of type $i$).

---

### Downward Substitution Rule

If

$$
D_2 > Q_2
$$

and there is a surplus of type 1

$$
Q_1 - D_1 > 0,
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

--------------------------------------
# 6. Optimization Approaches (Alternatives)

## A) Exact Numerical Computation + Enumeration

If the ranges of $Q_1$ and $Q_2$ are not very large (say, up to a few hundred), you can:

1. For each $(Q_1, Q_2)$, compute $\Pi(Q_1, Q_2)$ via a two-dimensional sum or an integral.
2. Take the maximum.

This gives a **guaranteed optimal** solution.

**Hint:**  
Store the table of probabilities $Pr(D_1 = d_1, D_2 = d_2)$.  
If $D_1$ and $D_2$ are independent, store them separately and multiply the probabilities when needed.

---

## B) Simulation (Monte Carlo) + Stochastic Optimization

If analytical summation is difficult (e.g. complex/high-dimensional distributions or dependency between $D_1$ and $D_2$), use simulation:

- For fixed $Q_1, Q_2$, simulate $N$ scenarios $(d_1, d_2)$ and estimate $\hat{\Pi}$;
- Then use a simple grid search over $Q$ or a gradient-free optimizer (e.g. **Nelder–Mead**, **CMA-ES**) over $(Q_1, Q_2)$ to maximize $\hat{\Pi}$.

This method is flexible, stable, and easy to implement.

---

## C) Iterative Fixation (Coordinate Descent)

A practical approach:

1. Fix $Q_2$, and optimize $Q_1$ (a one-dimensional newsvendor-like problem with substitution considered);
2. Then fix $Q_1$ and optimize $Q_2$;
3. Repeat until convergence.

This often converges quickly to a (locally) optimal solution.

---

## D) Approximation via a Generalized Critical Fractile

For a single product, the classical newsvendor condition is:

$$
F(Q^*) = \frac{p - c}{p - s},
$$

where:
- $p$ — retail price,  
- $c$ — purchase cost,  
- $s$ — salvage value.

With substitution, we can try to obtain an analogous condition by replacing the “effective” revenue from the last unit of type 1 with the **marginal revenue of type 1**:

$$
\text{marginal revenue of type 1} = P_1Pr(\text{unit sold to} D_1)+
P_2Pr(\text{unit sold as substitute to} D_2) + V_1Pr(\text{unsold}).
$$

Then, an inequality “less/greater” gives the **stopping condition**.  
However, calculating these probabilities analytically is difficult, so this formulation is mainly used as an intuition for **numerical/simulation-based methods** and for interpreting results.

---

# 7. How to Compute Marginal Benefit (Optimization Intuition)

Let’s consider adding one unit to $Q_1$ (keeping $Q_2$ fixed).

That additional unit will bring:

- $P_1$, if it satisfies demand $D_1$ (i.e., if $D_1 \ge q_{1,\text{old}} + 1$, meaning that without it some demand would be unmet);
- $P_2$, if after satisfying $D_1$ it is used as a **substitute** when $Q_2$ is insufficient;
- $V_1$, if it remains **unsold**.

Therefore, the **expected marginal profit** is:

$$
\Delta_1(q_1) = P_1Pr(\text{sold to } D_1)+ P_2Pr(\text{used as substitute})+
V_1Pr(\text{unsold}) - C_1.
$$

The optimum for $Q_1$ is reached when $\Delta_1(q_1)$ **ceases to be positive**  
(in discrete terms — keep adding units while the next one still contributes positively).

The same logic applies to $Q_2$.

This formula is easy to estimate via **simulation**:
empirically calculate event probabilities and compute the marginal profit for each potential inventory level.
---
## 8. is absent
---
# 9. Examples of Special Cases and Practical Tips

### Independent Poisson Demands

If $D_i \sim Pois(\lambda_i)$ — this is convenient: cumulative probabilities are computed quickly, and simulation is very fast.

- If substitution is rare (for example, $\lambda_1$ is large so demand for type 1 is high, leaving little overflow),  
  then the solution is close to two **independent newsvendor** problems.

- If substitution is significant — type 1 serves as a **buffer** for type 2.  
  Usually, this increases the optimal $Q_1$ (compared to the independent solution)  
  and decreases $Q_2$ (since part of $D_2$ demand is satisfied through substitution).  
  The exact direction, however, depends on the parameters $P_i, C_i, V_i$.

**Sensitivity:**  
It is useful to plot $\Pi(Q_1, Q_2)$ surfaces, study **break-even levels** and **elasticity**.

---

# 10. Model Extensions (if you want to make it richer)

- **Two-period / multi-period dynamics (with inventory carryover)** → use **dynamic programming**.  
- **Differentiated pricing for substitution:** sometimes a substituted unit should not be sold at $P_2$ but at a discounted price — include that in your profit formula.  
- **Dependent demands $D_1, D_2$:** account for **positive or negative correlation** in the joint distribution.  
- **Variable purchase prices, volume discounts, or capacity/budget constraints** can also be added.

---

# 11. Practical Roadmap (Recommended Starting Steps)

1. **Choose or estimate demand distributions** $D_1, D_2$  
   — from historical data (Poisson, empirical, or grid-based).

2. **Implement a simulator** of the profit function $\Pi$  
   using the formula from Section §2.

3. **Run in sequence:**
   - Independent newsvendor solutions (as a reference);
   - Then **coordinate descent** (fix $Q_2$, optimize $Q_1$, then reverse),  
     using simulation to estimate $\Pi$.

4. **Test parameter sensitivity:**  
   Build a **heatmap of** $\Pi(Q_1, Q_2)$ to visualize dependence on $P, C, V, \lambda$.

5. **If necessary:**  
   Move to a full optimization (e.g. **CMA-ES** or grid search within reasonable bounds).

---

# 12. Ready to Implement

Once these steps are in place, you can directly proceed to coding and experimentation —  
the framework scales easily to more products, correlated demands, or dynamic inventory settings.

---


---






---------------------------------------------------

