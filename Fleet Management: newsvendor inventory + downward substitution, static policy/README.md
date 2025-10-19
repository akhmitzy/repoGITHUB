# Two-Product Newsvendor Problem with Downward Substitution

A classical **newsvendor / inventory** problem with two product types and **downward substitution** (if the worse type runs out → we issue the better type at the price of the worse one).

No miscelania burdens like insurance or transaction costs were considered, we use independent Poisson  customers claims and dependent - Poisson-Poisson case (when lambda of claims of better cars depend from lambda of another cars in some simple concave way). We considered only static policy - the number $b$ of better cars used for substituting another cars is constant ($b$ lives in range [0, ..., 20]. We provide NVP and ROI for different $b$ when the number of cars of both sort lays in range [1,20]. 

The case of adaptive policy - when $b$ is dynamical we provide in other folder with corresponding character title.

We make the model adopting it to the CFO interest about NVP and ROI.  

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
\mathbb{E}\left[\min\left((Q_1 - D_1)^+, (D_2 - Q_2)^+\right)\right],
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
## 8. TBD: May be done in the future, later
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

# 10. Model Extensions (for future - to make it richer)

- **Two-period / multi-period dynamics (with inventory carryover)** → use **dynamic programming**.  
- **Differentiated pricing for substitution:** sometimes a substituted unit should not be sold at $P_2$ but at a discounted price — include that in your profit formula.  
- **Dependent demands $D_1, D_2$:** account for **positive or negative correlation** in the joint distribution.  
- **Variable purchase prices, volume discounts, or capacity/budget constraints** can also be added.

---

# 11. Practical Roadmap (possible Starting Steps)

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

----------------------------------------------------------------
------------------------------------------------------------
# Rental Fleet Model with Downward Substitution

This document presents a **comprehensive but practical structure** for modeling a rental car fleet with **downward substitution**, ranging from a simple static one-period setup to a realistic multi-day dynamic model, with practical heuristics and optimization algorithms.

The goal is a **"best-effort" model** that can be directly simulated and improved.

---

## 0. Basic Idea

The base unit is a vehicle of **type 1** (higher quality) or **type 2** (lower quality).

Customer demand arrives per period (day).  
Each car can be rented at most once per day (or — if you wish to model multi-day rentals — include return times and rental durations).

If there are not enough type-2 cars, we can substitute downward: give a **type-1** car to a **type-2** customer, but receive revenue as for type 2 ($P_2$).  
Type-1 customers always have priority for type-1 vehicles.

**Objective:** choose fleet sizing, daily allocation, and protection policy to **maximize expected total profit or net revenue**, accounting for purchase, holding, repair, and disposal costs.

---

## 1. Model — Notation (Dynamic, by Day)

### Parameters (fixed)

- $P_i$ — rental price per period for vehicle type $i$ ($i=1,2$).  
- $C_i$ — purchase (wholesale) cost per unit.  
- $S_i$ — salvage value upon de-fleet.  
- $H_i$ — per-period holding or operating cost (parking, insurance, depreciation).  
- $V_i = S_i - H_i$ — net residual value per unsold unit per period (can be negative).

### Demand distributions

$D_{i,t}$ — random daily demand for type $i$ on day $t$.  
Can be modeled as Poisson with rate $\lambda_i$ or empirically.

(Optional) **Rental duration:** $L$ (in days).  
If multi-day rentals, a car returns after a random $L$ days.

(Optional) **Relocation cost:** $B$, if multiple locations exist.

---

### State variables (day $t$)

- $I_{i,t}$ — available (idle) cars of type $i$ at the start of day $t$.  
  (Additional states like “in repair” or “in transit” can be added if needed.)

---

### Decision variables (policy)

- Allocation of daily demand $D_{1,t}, D_{2,t}$ given available inventory $I_{1,t}, I_{2,t}$ under the substitution rule:  
  if type 2 is short, type 1 can substitute; if type 1 is short, no upward substitution occurs.

- **Protection level policy:** reserve $b$ units of type 1 exclusively for type-1 customers.  
  Equivalently, allow substitution while $I_{1,t} - b > 0$.

- **Procurement / de-fleet decisions:** periodic fleet review (monthly/quarterly) for acquisitions and disposals.

---

### Transitions (Dynamics)

At the start of day $t$, the state is $I_{i,t}$.

Demand $(d_1, d_2)$ arrives.  
The service algorithm:

1. Serve $\min(d_1, I_{1,t})$ type-1 customers directly.  
   Unmet type-1 demand is lost (no upward substitution).

2. Serve $\min(d_2, I_{2,t})$ type-2 customers directly.

3. If $d_2 > I_{2,t}$ and $I_{1,t} - b > 0$,  
   satisfy up to $\min((I_{1,t}-b)^+, (d_2 - I_{2,t})^+)$ through substitution, earning $P_2$ per rental.

Daily revenue is the sum of all transaction revenues.

At the end of the day:  
- Cars rented for one day return immediately;  
- For multi-day rentals, vehicles return after $L$ days;  
- Update inventory, purchases, and disposals accordingly:

$$
I_{i,t+1} = I_{i,t} - \text{rented} + \text{returned} + \text{purchases} - \text{disposals}.
$$

If purchases/disposals occur, include costs $C_i$ and salvage $S_i$.

---

### Daily Profit

$$
\pi_t = P_1 \min(I_{1,t}, D_{1,t}) + P_2 \min(I_{2,t}, D_{2,t})+
 P_2 \min\big((I_{1,t} - b)^+, (D_{2,t} - I_{2,t})^+\big) -
 H_1 I_{1,t} - H_2 I_{2,t} - C_1 \cdot 1_{\text{new cars type 1}} - C_2 \cdot 1_{\text{new cars type 2}} + V_1 \cdot 1_{\text{disposed type 1}} + V_2 \cdot 1_{\text{disposed type 2}}
$$

(The formula can be detailed further depending on your accounting convention.)

---

## 2. Model Variants and Solution Approaches

### A. One-Period (Planning / Fleet Sizing) — Newsvendor with Substitution

Used when determining $Q_1, Q_2$ for one “season” or planning horizon.

Formulas follow the Newsvendor approach; optimum found via enumeration, simulation, or analytics given demand distributions $D$.

**Practical tip:** use classic Newsvendor solutions as a starting point, then adjust via simulation to account for substitution.

---

### B. Multi-Period MDP (Recommended for Operational Control)

State: $s_t = (I_{1,t}, I_{2,t}, \ldots)$  
Action: $a_t$ — allocation, purchase, and disposal policy.  
Reward: $r(s_t, a_t, D_t)$ — profit minus costs for the period.

Transitions: stochastic, driven by demand and returns.  
Objective: maximize discounted (or average) total profit.

**Solution methods:**
- Full **Dynamic Programming (DP)** for small fleets.  
- **Approximate Dynamic Programming (ADP)** or **Reinforcement Learning** (Q-learning, policy gradient, rollout) for larger state spaces.  
- **Parametric policies** (protection levels, base-stock, thresholds) — optimize parameters via simulation.

---

### C. Discrete-Event Simulation + Policy Optimization

Build a simulator for rentals, returns, and customer arrivals.

Test different policies (protection level $b$, priorities, procurement schedules) and evaluate long-run average profit.

Optimize parameters via:
- grid search,  
- Nelder-Mead,  
- CMA-ES, or  
- Bayesian optimization.

---

## 3. Recommended Practical Policy (Simple and Effective)

**Protection Policy (Nested Protection):**  
Keep $b$ type-1 cars reserved for type-1 customers; remaining type-1 can serve type-2 customers.

**Why it works:**  
It protects high-margin customers ($P_1 > P_2$) and limits “leakage” into low-revenue segments.

Optimize $b$ by simulation: evaluate profit at different $b$ values.

---

## 4. Marginal Logic and Parameter Tuning

To determine optimal $Q_1$, $Q_2$, or $b$, use **marginal profit** logic:

$$
\Delta_1 = P_1 \Pr(\text{rented as type 1}) + P_2 \Pr(\text{used as substitute})+
 V_1 \Pr(\text{unsold})- C_1
$$

Add type-1 cars while $\Delta_1 > 0$.  
Similarly for type-2.  
In multi-period versions, include discounting and future impact on inventory.

---

## 5. Accounting for Multi-Day Rentals and Returns

If rentals last longer than one day:

Each rented car leaves for $L$ days; returns are random.

State must include a **return schedule vector** — number of cars returning in 1, 2, … days (inventory position).

This becomes similar to a **G/G/c** system and is best handled by simulation.

---

## 6. Maintenance, Depreciation, and Repairs

Include average periodic maintenance and depreciation in $H_i$.

Model periodic repairs or servicing by moving cars into a “maintenance” state, reducing availability temporarily.

---
# Integration with Financial Planning (CAPEX / OPEX)

This section explains how to embed the **rental fleet model with downward substitution** into a full financial planning framework — connecting operational policies with long-term investment and profitability metrics.

---

## 1. CAPEX (Capital Expenditures)

These are **capital costs** related to acquisition and long-term investments:

- Purchasing vehicles from the manufacturer (at prices $C_1$, $C_2$)
- Infrastructure investments (parking facilities, charging stations for EVs)
- IT systems for fleet management

**In the model:**

- The decision variables $Q_1$, $Q_2$ (number of purchased vehicles) are directly tied to CAPEX.  
- Fleet renewal decisions — how many cars to buy and of which type — must align with the **investment budget**.

---

## 2. OPEX (Operational Expenditures)

These are **operational expenses** — all day-to-day costs of running and maintaining the fleet:

- Maintenance and repairs  
- Insurance and taxes  
- Fuel or electricity  
- Storage / parking costs ($H_1$, $H_2$)  
- Logistics of repositioning vehicles between locations  

**In the model:**

- $H_1$, $H_2$ (holding or operating costs) represent a key part of OPEX.  
- The residual value terms  
  $V_1 = S_1 - H_1, \quad V_2 = S_2 - H_2$
  reflect the **net retained value** of a car after operating expenses.

---

## 3. Why Integration Matters

If we model only operational flows — rentals, substitutions, salvage — we can locally maximize short-term profit (e.g., per month).  
But in practice, **financial management** needs to understand how operational decisions affect long-term returns:

> “If we buy more type-1 cars, CAPEX increases by X,  
> but operating profit (OPEX + revenue) changes by Y,  
> giving ROI = Z% after 3 years.”

Thus, decisions on $Q_1$, $Q_2$ should be consistent with both **current demand** $(D_1, D_2)$ and the **long-term investment plan**.

---

## 4. Integrated Structure in the Fleet Model with Substitution

### Revenue Components
- $P_1 \cdot \min(Q_1, D_1)$ — premium clients renting type-1 vehicles at premium price  
- $P_2 \cdot \min(Q_2, D_2)$ — standard clients renting type-2 vehicles  
- $P_2 \cdot \min((Q_1 - D_1)^+, (D_2 - Q_2)^+)$ — **downward substitution:**  
  a type-2 client is served by a type-1 vehicle but pays the type-2 price

### Cost Components
- $C_1 Q_1 + C_2 Q_2$ → **CAPEX** (procurement cost)  
- $H_1 Q_1 + H_2 Q_2$ → **OPEX** (operating cost per period)  
- **Salvage values** (when selling cars) → partial CAPEX recovery  

---

## 5. Financial Planning and Optimization

The model should answer key questions:

1. **Optimal fleet sizing:**  
   What $Q_1$, $Q_2$ maximize total profit given CAPEX constraints and OPEX costs?

2. **Investment payback:**  
   Over how many years does the purchase pay off (NPV / IRR)?

3. **Demand scenarios:**  
   Under which demand combinations $(D_1, D_2)$ does substitution create the most value?  
   (e.g., when demand for cheaper cars is high, type-1 vehicles often substitute downward.)

4. **Fleet mix optimization:**  
   How to allocate the fleet between segments 1 and 2 to minimize **TCO** (Total Cost of Ownership)  
   and maximize overall margin?

---

## 🔑 Summary

Integrating **CAPEX / OPEX** means that the rental fleet model with substitution should not be a purely operational decision tool (focused only on daily utilization), but part of the **corporate financial planning system**:

- **CAPEX:** vehicle acquisitions and long-term investments  
- **OPEX:** operation and maintenance  
- **Salvage / resale:** capital recovery  
- **Objective:** profitability and return-on-investment (ROI) over a multi-year horizon

---
# Multi-Period Rental Fleet Optimization Model with Downward Substitution and CAPEX/OPEX Integration

This document presents a **multi-period optimization model** for a rental car fleet with **downward substitution**, integrating **CAPEX**, **OPEX**, **salvage**, and **discounting** directly into the objective and constraints.  
The model can be implemented either as a **deterministic MILP** or as a **stochastic scenario-based problem**.

---

## 1. Indices and Sets

- $\( t = 1, \dots, T \)$ — periods (days, weeks, or months)  
- $\( i \in \{1, 2\} \)$ — vehicle types:  
  - $\( i=1 \)$: premium type (“better”),  
  - $\( i=2 \)$: standard type (“worse”).

---

## 2. Parameters

| Symbol | Meaning |
|:--------|:---------|
| $P_{i,t}$ | Rental price of type $i$ in period $t$. |
| $C_{i,t}$ | Purchase price (CAPEX) per unit of type $\(i\)$ in period \$(t\)$. |
| $S_{i,t}$ | Salvage value / resale revenue of type $i$ in period $t$. |
| $H_{i,t}$ | Operational cost (OPEX) per unit of type $i$ in period $t$. |
| $r$ | Discount rate per period (for NPV). |
| $D_{i,t}$ | Demand for type $i$ in period $t$. In stochastic form, $D_{i,t}^s$ for scenario $s$ with probability $\pi_s$. |
| $B_t$ | Investment (CAPEX) budget in period $t$. |

---

## 3. Decision Variables

| Symbol | Meaning |
|:--------|:---------|
| $Q_{i,t}$ | Fleet size (owned units) of type $i$ at the start of period $t$. |
| $x_{i,t}^{buy} \ge 0$ | Number of vehicles of type $i$ purchased in period $t$. |
| $x_{i,t}^{sell} \ge 0$ | Number of vehicles of type $i$ sold/retired in period $t$. |
| $a_{i,t}$ | Available units of type $i$ for rental in period $t$. |
| $y_{i,t}$ | Vehicles of type $i$ rented to their own segment in period $t$. |
| $z_t$ | Number of downward substitutions (type 1 rented as type 2) in period $t$. |
| $u_{i,t}$ | Idle/unused units of type $i$ at the end of period $t$. |

All variables are nonnegative. Integer constraints may be imposed on $x_{i,t}^{buy}$, $x_{i,t}^{sell}$, and $Q_{i,t}$ if desired.

---

## 4. Service and Substitution Logic

1. **Primary demand service:**
$y_{1,t} \le \min(a_{1,t}, D_{1,t})$,
$y_{2,t} \le \min(a_{2,t}, D_{2,t})$

3. **Downward substitution (type 1 → type 2):**
$z_t \le a_{1,t} - y_{1,t}$,
$z_t \le D_{2,t} - y_{2,t}, z_t \ge 0$

Each substitution yields revenue $P_{2,t}$ (the price of the lower segment).

5. **Availability:**
  $y_{1,t} + z_t \le a_{1,t}$,
$y_{2,t} \le a_{2,t}$

---

## 5. Fleet Balance (CAPEX/Defleet)

$Q_{i,t+1} = Q_{i,t} + x_{i,t}^{buy} - x_{i,t}^{sell}$

---

## 6. Budget Constraint

$\sum_{i=1}^2 C_{i,t}x_{i,t}^{buy} \le B_t, \quad \forall t$

---

## 7. Profit Components

### Revenue
$$
\text{Revenue}_t = P_{1,t}y_{1,t} + P_{2,t}y_{2,t} + P_{2,t}z_t
$$

### OPEX
$$
\text{Opex}_t = \sum_{i=1}^2 H_{i,t} Q_{i,t}
$$

### CAPEX
$$
\text{Capex}_t = \sum_{i=1}^2 C_{i,t} x_{i,t}^{buy}
$$

### Salvage
$$
\text{Salvage}_t = \sum_{i=1}^2 S_{i,t} x_{i,t}^{sell}
$$

---

## 8. Objective: Maximize NPV

$$
\max \sum_{t=1}^T \frac{1}{(1+r)^{t-1}}\Big( \text{Revenue}_t - \text{Opex}_t - \text{Capex}_t + \text{Salvage}_t \Big)
$$


Optionally, include terminal salvage:

$$
 \sum_{i} \frac{S_{i,T}^{term}Q_{i,T+1}}{(1+r)^T}
$$

---

## 9. Stochastic Extension

Let scenarios $s \in S$have probabilities $\pi_s$ and scenario-specific demand $D_{i,t}^s$.  
Then $y_{i,t}^s z_t^s$ become scenario-dependent, while $x_{i,t}^{buy}$ and $Q_{i,t}$ may remain first-stage decisions.

Objective:

$$
\max \sum_{t=1}^T \frac{1}{(1+r)^{t-1}}
\sum_{s \in S} \pi_s \Big( \text{Revenue}_t^s - \text{Opex}_t - \text{Capex}_t + \text{Salvage}_t^s \Big)
$$

---

## 10. Optional: Service-Level Constraints

To ensure a minimum service rate $\alpha_i$:

$$
\mathbb{E}_s \left[ \frac{y_{i,t}^s + \mathbf{1}_{\{i=1\}} z_t^s}{D_{i,t}^s} \right] \ge \alpha_i
$$

Implemented as:

$$
\sum_s \pi_s (y_{i,t}^s + \mathbf{1}_{\{i=1\}} z_t^s)
\ge \alpha_i \sum_s \pi_s D_{i,t}^s
$$

---

## 11. Accounting and Financial Integration

### Amortization
Instead of expensing CAPEX immediately, amortize it:
$$
A_{i,t} = \frac{C_i}{L}
$$
where $L$ is the expected vehicle lifetime (years or periods).

### Salvage
When a vehicle is sold:
$$
\text{Cash inflow} = S_{i,t}
$$
In the NPV objective, this acts as a positive discounted flow.

Depending on the accounting view:
- **Cash-based:** CAPEX at purchase, salvage at sale.  
- **Accrual-based (P&L):** Include amortization in OPEX.

---

## 12. Solution Approaches

| Method | Description |
|:--------|:-------------|
| **Deterministic MILP** | Solve with standard solvers (CPLEX, Gurobi, CBC). Suitable for planning given known demand forecasts. |
| **Two-stage stochastic program** | Purchases are first-stage; rentals/substitutions depend on realized demand scenarios. |
| **Minimax / robust variant** | Replace expectation by min–max to handle demand uncertainty without probabilities. |
| **Dynamic programming / rolling horizon** | For long horizons, use re-optimization each period as new data arrive. |

---

## 13. Implementation Roadmap

1. **Data inputs:** historical demand, prices, costs, resale values.  
2. **Parameter estimation:** forecast $D_{i,t}$, salvage schedules, discount rate $r$.  
3. **Solver setup:** formulate in Pyomo, Gurobi, or PuLP.  
4. **Scenario generation:** sample $D_{i,t}^s$ from empirical distributions.  
5. **Run deterministic baseline → stochastic extension.**  
6. **Sensitivity analysis:** CAPEX budget $B_t$, substitution effect $z_t$, discount rate $r$.  
7. **Dashboard:** visualize fleet composition, profit streams, substitution utilization.

---

## 14. Summary

This integrated CAPEX–OPEX–Substitution model transforms rental fleet optimization into a **financially grounded investment decision**.  
It connects:
- **Fleet operations** (rental, maintenance, substitution)  
- **Financial planning** (CAPEX, OPEX, salvage)  
- **Performance metrics** (NPV, ROI, service level)

and thus aligns short-term operational optimization with long-term ROI and corporate planning.

---

# Solving the Problem: Approaches and Algorithms

---

## 1. MILP (Deterministic Scenario)

Build a **Mixed Integer Linear Program (MILP)** with integer variables $x, Q, y, z$ and a linear objective (Net Present Value with discounting):

$$
\max_{x, y, z, Q}\sum_{t=1}^{T} \frac{1}{(1+r)^{t-1}}\left(
P_{1,t} y_{1,t} + P_{2,t} y_{2,t} + P_{2,t} z_t- \sum_i H_{i,t} Q_{i,t}-
\sum_i C_{i,t} x_{i,t}^{buy}+ \sum_i S_{i,t} x_{i,t}^{sell}\right)
$$

subject to balance, budget, and service constraints described earlier.

**Solvers:**
- Commercial: *Gurobi*, *CPLEX*  
- Open-source: *CBC*

Works well for small planning horizons $T$ and moderate fleet sizes $Q$.

---

## 2. Stochastic Programming (SAA)

Use **Sample Average Approximation (SAA)**:

- Generate $N$ demand scenarios $D_{i,t}^{(s)}$ via Monte Carlo simulation.  
- Solve the problem:

$$
\max \frac{1}{N} \sum_{s=1}^{N} \text{NPV}^{(s)}
$$

where first-stage (purchase) variables $x_{i,t}^{buy}$ are the same across all scenarios,  
and operational decisions $( y, z )$ can depend on each scenario.

**Solution methods:**
- Use *Benders decomposition* for large $N$.  
- Check SAA convergence by gradually increasing the number of scenarios.

---

## 3. Rolling-Horizon / Model Predictive Control

In practice, a **rolling-horizon** (model predictive) approach is often used:

- Every $k$ days, solve the optimization problem over a horizon $H$ (e.g., 12 weeks).  
- Implement only the decisions for the first period.  
- Then roll the horizon forward and re-optimize with updated data.

✅ **Advantages:** adaptivity, robustness to forecast errors, and computational tractability.

---

## 4. Simulation + Policy Search (Heuristic Approach)

If the model is large or scenario-rich, define a **parametric policy** instead:

Example: specify a *protection level* $b_t$, *reorder points*, or periodic review,  
and search for the best parameters.

**Search methods:**
- Grid search  
- Nelder–Mead  
- CMA-ES  
- Bayesian optimization  

Each policy is evaluated using **discrete-event simulation** (Monte Carlo).

---

## 5. Approximate Dynamic Programming / Reinforcement Learning

If rentals last for multiple periods or the system state is complex  
(e.g., includes returns, repairs, defleeting), you can apply **ADP or RL**:

- Learn a *value function* via approximation (basis functions, neural networks).  
- Use policy gradient methods for direct strategy optimization.  

These approaches offer flexible, adaptive policies but require more data and computation.

---

## Practical Implementation Roadmap

1. **Collect data:** historical $D_{i,t}$ (by day or week), rental durations, returns, repairs, CAPEX/budget data, and actual $P, C, H, S$.  
2. **Select time scale:** days or weeks.  
3. **Implement a basic simulator:** 1-day rentals, substitution, protection level $b_t$; verify logic.  
4. **Run “what-if” analysis:** grid search over $Q_1, Q_2, b$ with 10k–100k Monte Carlo runs to estimate long-run average profit and NPV.  
5. **Build MILP/SAA:** for optimal purchasing decisions under CAPEX and NPV constraints.  
6. **Implement rolling horizon:** optimize purchases and defleeting weekly with horizon $H$.  
7. **Track key KPIs:**  
   - Occupancy rate  
   - Utilization by vehicle type  
   - Lost demand  
   - Substitution rate  
   - OPEX per vehicle  
   - Fleet ROI, NPV, IRR  

---

## Practical Tips and Simplifications

- For large $T$ and $Q$: **relax integer constraints** and round later.  
- If demand is highly stochastic, optimize only the *protection policy* $b_t$ — this greatly simplifies the problem.  
- Include a **terminal salvage value** at the end of the horizon to prevent artificial vehicle buildup.  
- Always include a **CAPEX budget constraint** $B_t$, which often limits purchasing decisions.

---

## Example of Objective Function (NPV)

$$
\max_{x, y, z, Q}\sum_{t=1}^{T} \frac{1}{(1+r)^{t-1}}\left(
\underbrace{P_{1,t} y_{1,t} + P_{2,t} y_{2,t} + P_{2,t} z_t}_{\text{revenue}}-
\underbrace{\sum_i H_{i,t} Q_{i,t}}_{\text{OPEX}}-
\underbrace{\sum_i C_{i,t} x_{i,t}^{buy}}_{\text{CAPEX}}+
\underbrace{\sum_i S_{i,t} x_{i,t}^{sell}}_{\text{salvage}}\right)
$$

subject to balance, service, and budget constraints.

---
# Financial Performance Formulas

## 1. CAPEX (initial, current)

$$
C = C_1 Q_1 + C_2 Q_2
$$

Where:  
- $C$— total capital expenditure (CAPEX)  
- $C_1, C_2$ — cost per unit for types 1 and 2  
- $Q_1, Q_2$ — quantity of units purchased for each type  


## 2. Monthly Operating Net Cash Flow

$$
CF_m = \text{Revenue}_{month} - \text{OPEX}_{month}
$$

Where:  
- $CF_m$ — monthly cash flow  
- $\text{Revenue}_{month}$ — monthly rental or operating income  
- $\text{OPEX}_{month}$— monthly operating expenses  


## 3. Present Value (PV) of Monthly Cash Flows (Annuity)

$$
PV_{CF} = CF_m \cdot \frac{1 - (1 + r_m)^{-36}}{r_m}
$$

Where:  
- $PV_{CF}$ — present value of all monthly cash flows over 36 months  
- $CF_m$ — monthly cash flow  
- $r_m$ — monthly discount rate  


## 4. PV of Salvage Value

$$
PV_{salv} = \frac{S_1 Q_1 + S_2 Q_2}{(1 + r_m)^{36}}
$$

Where:  
- $PV_{salv}$ — present value of the total salvage (residual) value after 36 months  
- $S_1, S_2$ — salvage value per unit for each type  
- $Q_1, Q_2$ — quantities  
- $r_m$ — monthly discount rate  


## 5. Net Present Value (NPV)

$$
NPV = PV_{CF} + PV_{salv} - CAPEX
$$

Where:  
- $NPV$— net present value over 36 months  
- $PV_{CF}$— PV of monthly cash flows  
- $PV_{salv}$ — PV of salvage
- -------------------------
-
# What the CFO Expects to See and Why It Matters

**CAPEX $X$** — shows how much upfront investment is required (for example, \$2.22M in option E).

**Monthly Operating Cash Flow $Y$** — indicates how quickly these investments are “paid back” through operations (in option E we get \$121.5k/month).

**ROI / NPV / IRR** — measure the profitability and attractiveness of the project in terms of the time value of money.  
The CFO usually focuses on **NPV** and **IRR**, not just accounting profit.

---

### In practice, the CFO will ask:

- How do $\text{NPV}$ and $\text{IRR}$ change with a ±10% change in demand (stress test)?  
- How has the **annual volatility of the cash flow** changed?  
- Are there **CAPEX budget limits** by year (if yes — this must be taken into account)?  
- What is the **risk** if the **salvage value** turns out to be lower than estimated?

---

### Key Financial Metrics (for reference)

#### Initial CAPEX:
$$
C = C_1 Q_1 + C_2 Q_2
$$

#### Monthly Operating Net Cash Flow:
$$
CF_m = \text{Revenue}_{month} - \text{OPEX}_{month}
$$

#### Present Value of Monthly Cash Flows (Annuity, 36 months):
$$
PV_{CF} = CF_m \cdot \frac{1 - (1 + r_m)^{-36}}{r_m}
$$

#### Present Value of Salvage:
$$
PV_{salv} = \frac{S_1 Q_1 + S_2 Q_2}{(1 + r_m)^{36}}
$$

#### Net Present Value (NPV):
$$
NPV = PV_{CF} + PV_{salv} - CAPEX
$$

#### Simple ROI over 3 years:
$$
ROI = \frac{(\text{Total Net Cash Flow over 36 months} + \text{Salvage}) - CAPEX}{CAPEX}
$$

#### Annualized Return:
$$
R_{annualized} = (1 + ROI)^{1/3} - 1
$$


