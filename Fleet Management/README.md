# INTRODUCTION

In the famous book of Richard S. Sutton and Andrew G. Barto *Reinforcement Learning* you can find an example of car rental fleet managememt. 

Example 4.2: Jack’s Car Rental Jack manages two locations for a nationwide car
rental company. Each day, some number of customers arrive at each location to rent cars.
If Jack has a car available, he rents it out and is credited $10 by the national company.
If he is out of cars at that location, then the business is lost. Cars become available for
renting the day after they are returned. To help ensure that cars are available where
they are needed, Jack can move them between the two locations overnight, at a cost of
$2 per car moved. We assume that the number of cars requested and returned at each
location are Poisson random variables. Suppose the parameters are 3 and 4 for rental requests at
the first and second locations and 3 and 2 for returns. To simplify the problem slightly,
we assume that there can be no more than 20 cars at each location (any additional cars
are returned to the nationwide company, and thus disappear from the problem) and a
maximum of five cars can be moved from one location to the other in one night. We take
the discount rate to be # = 0.9 and formulate this as a continuing finite MDP, where
the time steps are days, the state is the number of cars at each location at the end of
the day, and the actions are the net numbers of cars moved between the two locations
overnight. 
The Authors provide the the sequence of policies found by policy iteration starting
from the policy that never moves any cars.

---

We extend their model with configuration space of locations - we work with the rental duration and the car returning directly.
We model the overnight vehicle relocation problem as a Mixed-Integer Linear Program (MILP). The goal is to decide how many cars to place at each branch overnight to maximize the next day’s expected profit.




---
# Exact formulation as an MIP (Mixed-Integer Linear Program) via precomputing profit for each possible stock level
---
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

*1___* Branch $f_i$ each day has $1 + \mathrm{Poisson}(\lambda_i)$ rental requests.  
   If a request arrives but there is no car — the request is lost, and all subsequent requests for that day are also lost.
   
*2___* If there is a car for the request, the branch earns $p \cdot c$, where:

   $$
   c = 1 + \mathrm{Poisson}(\lambda_{\text{rent}})
   $$

   is the number of days the car is rented, and $p$ = $10.
   
*3___* Each car chooses a branch to return to according to a matrix of probabilities:  $\mathrm{RETURN}(i, j)$ of size $I \times I$, where:

   $$
   \sum_{j=1}^I \mathrm{RETURN}(i, j) = 1
   $$

  

The rental duration of each car becomes known in the morning, so by the evening we already know the values $s_1, s_2, \dots, s_I$.

The cost of overnight transfer is $c_{\text{move}} = 1$ per mile.  
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
\max \sum_{i=1}^{I} \sum_{k=0}^{K} r_{ik} y_{ik} - c_{\text{move}} \cdot \left( \sum_{i=1}^I \left| \mathrm{perevoz}_i \right| \right)
$$

Subject to:

$$
\sum_{k=0}^K y_{ik} = 1,  \ \ i = 1,  2, \ \ \dots,  I \\
$$

$$
\sum_{k=0}^K k \cdot y_{jk} = s_j - \mathrm{perevoz}_j, \ \  j = 1,  2, \dots,  I \\
$$

$$
\sum_{j=1}^I (s_j - \mathrm{perevoz}_j) = S 
$$

i.e. 

$$ \sum_{j=1}^I \mathrm{perevoz}_j = 0 $$


$y_{ik} \in$ { 0, 1 } ,   $-M - 1 < \mathrm{perevoz}_j < M + 1$

---

## Parameters
- $I = 6$
- $S = 30$
- $D(i, j)$ — distances between locations $1, 2, 3, 4, 5, 6$ along a straight line, distance between nearest neighbors = $1$
- $M = 5$
- Initial configuration: $s_1 = s_2 = \dots = s_I = 5$
- $\lambda_i = i$ for $i = 2, \dots, 6$ and $\lambda_1 = 8$
- $\lambda_{\text{rent}} = 3$
-  and $\mathrm{RETURN}(j, j) = 0.2$,  all non-diagonal elements being equal.

---

## Goal
Tomorrow Jack starts operations and we need to find:

$$
\{\mathrm{perevoz}_i\}, \quad \text{total  average income}.
$$

---
## What do we use for optimization

---

**OR-Tools** is a free library from Google for solving optimization, search, and scheduling problems.  
It supports:

- **Linear and Mixed-Integer Programming (MIP)** — similar to PuLP, but with faster built-in solvers.
- **Constraint Programming** — solving problems with discrete constraints (well-suited for complex combinatorial tasks).
- **Routing Optimization** (VRP, TSP) — built-in algorithms for logistics, delivery, and routing.
- **Graph Algorithms** — flows, shortest paths, coloring, etc.
- **Scheduling** — calendar planning, timetabling problems.
---
**What the code does — in brief**
---
Construction of $( r_{i,k} )$ (expected profit from having $( k )$ cars at branch $f_i$ on the next working day),  with exact calculation of $( E[\min(D_i, k)] )$ for $( D_i = 1 + \text{Poisson}(\lambda_i) )$.

**Night MIP (CP-SAT)** chooses integer transfers (from $f_i$ to $f_j$) $\( f_{i,j} \in \{0, \dots, M\} \)$ and binary $\( y_{i,k} \)$ such that:

- Each branch has exactly one value of $\( k \)$ after transfers.
- $\sum_k y_{i,k} = s_i + \sum_{\text{in}} f_{\text{in}} - \sum_{\text{out}} f_{\text{out}}$
- Outgoing flow $\(\le\)$ number of cars available in the evening.
- Pairwise limit $f_{i,j} \le M$ is set by the variable’s domain.

**Day:**
- Generate exact orders (rule: $1 + \text{Poisson}(\lambda_i)$; if no car is available, further orders disappear).
- Generate exact rental duration $1 + \text{Poisson}(\lambda_{\text{rent}})$ and random return location from $\text{RETURN}(i, \cdot)$.
- A car is returned exactly after ( c ) days (precisely accounted for in `scheduled_returns`).

Repeat for 10 days, output daily logs and final totals,
cars at branch  $f_i$ on the next working day,  
with exact calculation of $E[\min(D_i, k)]$ for $D_i = 1 + \text{Poisson}(\lambda_i)$.

---
**Key Implementation Points**

---
- Choose  K = S  (the maximum number of cars in any branch does not exceed the total stock  S.

- $r_{i,k} = p \cdot E[c] \cdot E[\min(D_i, k)] )$,  
  where $D_i = 1 + \text{Pois}(\lambda_i)$,  
  $c = 1 + \text{Pois}(\lambda_{\text{rent}})$,  
  and $E[c] = 1 + \lambda_{\text{rent}}$.

- **Pair constraint:** each variable $f_{i,j}$ has domain $[0, M]$.

- **Additional constraint:** the total outgoing flow from a branch does not exceed the evening stock $s_i$.

- CP-SAT works with integer coefficients in the objective function —  
  for correctness, we scale real-valued $r_{i,k}$ (and costs) by an integer factor `scale` (e.g., 100),  
  then divide by `scale` when outputting results.
---
**Key Changes and Ideas**
---
- The MIP now uses only *actually available* cars (those not currently rented) — we pass $s_{\text{available}}$  into the model instead of “all cars ( S )”.

- The forced global conservation constraint $\sum_i x_i = S$ was removed  
  (since some cars may be rented and will return later).  
  The MIP operates only on the cars physically available now.

- To always guarantee a feasible solution, each balance equation now includes nonnegative
  **slack variables** — shortage $\text{shortage}_i \ge 0$ and surplus $\text{surplus}_i \ge 0$:  

  $$
  \sum_k k \, y_{i,k} + \text{shortage}_i - \text{surplus}_i
    = s_{\text{available}}[i] + \text{incoming} - \text{outgoing}
  $$

  Slacks give the model an “escape route” when constraints conflict.

- Slacks are penalized in the objective:  
  a large penalty $\text{PENALTY SLACK}$ (e.g., $10^6$) per car in slack —  
  this almost guarantees the solver will avoid slacks if possible,  
  but still allows finding a solution if otherwise infeasible.

- If in the optimal solution $\sum (\text{shortage}_i + \text{surplus}_i) > 0$,  
  we print a warning showing which branch/day had inconsistencies  
  and how much slack was required — this indicates which constraints  
  (e.g., M, available cars) were too tight.

- The constraint $f_{i,j} \le M$ is applied pairwise,  
  and $\text{outgoing} \le s_{\text{available}}[i]$  
  (cannot send more cars than are available).

- In the 10-day simulation, only actually available cars  
  (those currently in the depot) are used; rented cars are tracked  
  in the return schedule, and the MIP sees only the available count.

- The code scales to any I and S (for instance, I = 50, S = 300.  
  For large sizes, solving may take longer.
  ----
  **Scaling can make the solution impossible, so in that case we have Quick Solution: - we add  Fallback Heuristic (always returns a result)**

Below is a ready-to-use script. It:

- Attempts to solve CP-SAT each night.
- If CP-SAT fails to produce a solution within the time limit or returns an invalid status —  
  it automatically applies a greedy heuristic:
  - Computes shortage/surplus for each branch.
  - Transfers cars between the closest pairs until reaching the limits \( M \) and available cars.
- Logs the reason for the fallback (`timeout` / `infeasible`)  
  and outputs useful plots (daily revenue, cumulative revenue, transfers, stock matrix).

  ---------
PLots to go:
  **Daily revenue** — two lines: MIP vs. no action.

**Cumulative revenue** — two lines showing how profit accumulates over time.

**Heatmap** of available cars distribution across branches  
(X-axis — days, Y-axis — branches, color — number of cars).

**Line plots** of available cars by branch (different colors).

**Number of transfers** (`perevoz`) across all locations over 10 days  
(either as a separate heatmap or line plots).



