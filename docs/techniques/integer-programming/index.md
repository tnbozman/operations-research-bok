# Integer Programming

> Optimization where some or all variables are constrained to integer values.

---

## Lecture

### What is Integer Programming?

Integer Programming (IP) extends Linear Programming by requiring some or all decision variables to take integer (whole-number) values. In the real world, many decisions are inherently discrete: you cannot deploy half a tank, open a fraction of a warehouse, or schedule 2.7 guards. IP provides the mathematical framework to handle such decisions exactly.

**Plain-language definition:** Find the best assignment of discrete (and optionally continuous) decision variables that satisfies a set of constraints and optimizes an objective.

### Types of Integer Programs

| Type | Variables | Typical Uses |
|------|-----------|--------------|
| **Pure IP** | All integer | Personnel assignments, scheduling |
| **Mixed-Integer Program (MIP)** | Some integer, some continuous | Production planning, network design |
| **Binary IP (0-1 IP)** | All 0 or 1 | Yes/no decisions, selection problems |

**Why integrality matters:** Consider scheduling aircraft sorties. An LP might say "fly 2.4 sorties of aircraft type A." Rounding to 2 can be infeasible (misses coverage) or suboptimal. Only an IP guarantees a valid, optimal integer solution.

### Mathematical Formulation

**General Mixed-Integer Program:**

$$
\begin{align}
\text{Minimize } & \mathbf{c}^\top \mathbf{x} + \mathbf{d}^\top \mathbf{y} \\
\text{subject to } & \mathbf{A}\mathbf{x} + \mathbf{B}\mathbf{y} \leq \mathbf{b} \\
& \mathbf{x} \geq 0,\; \mathbf{x} \in \mathbb{R}^n \\
& \mathbf{y} \geq 0,\; \mathbf{y} \in \mathbb{Z}^p
\end{align}
$$

**Binary IP** is the special case where $y_i \in \{0, 1\}$ for all $i$.

**Pure IP** requires $\mathbf{x} \in \mathbb{Z}^n$ as well.

---

### The LP Relaxation

The **LP relaxation** of an IP drops the integrality requirements and solves the resulting LP. It provides:

- An **upper bound** on the optimal IP objective (for maximization)
- A fast, solvable benchmark via Simplex or interior-point methods
- The starting point for Branch and Bound

!!! warning "Why You Cannot Simply Round"
    Consider this small IP:

    $$
    \begin{align}
    \text{Maximize } & 5x_1 + 4x_2 \\
    \text{subject to } & 6x_1 + 4x_2 \leq 24 \\
    & x_1 + 2x_2 \leq 6 \\
    & x_1, x_2 \geq 0,\; x_1, x_2 \in \mathbb{Z}
    \end{align}
    $$

    - **LP relaxation optimum:** $x_1 = 3,\; x_2 = 1.5 \Rightarrow z_{LP} = 21$
    - **Round down** $x_2$: $x_1 = 3,\; x_2 = 1$ — feasible ($18+4=22\leq 24$, $3+2=5\leq 6$), but $z = 19$
    - **Round up** $x_2$: $x_1 = 3,\; x_2 = 2$ — infeasible ($18+8=26 > 24$)
    - **True IP optimum:** $x_1 = 3,\; x_2 = 1 \Rightarrow z_{IP} = 19$

    Rounding in either direction either yields a suboptimal or infeasible result. The LP relaxation value of 21 is **not achievable** with integers.

    **Lesson:** The LP relaxation optimum is an *upper bound*; the true IP optimum may be strictly less.

The **LP relaxation gap** (or **MIP gap**) is:

$$
\text{MIP gap} = \frac{z_{LP} - z_{IP}}{|z_{IP}|} \times 100\%
$$

A large gap means the IP is hard; solvers aim to close this gap through branching and cutting.

---

### Branch and Bound

Branch and Bound (B&B) is the foundational algorithm for IP. It systematically enumerates candidate solutions by splitting the problem into subproblems (branching) and uses bounds to discard unpromising subproblems (pruning).

**Algorithm outline:**

1. Solve the LP relaxation of the current node. If infeasible, prune.
2. If all integer variables are integral, record as incumbent (best integer solution found).
3. If LP bound ≤ incumbent, prune (cannot improve).
4. Select a fractional variable $x_j = v$ (non-integer). Create two child nodes:
   - **Branch down:** add constraint $x_j \leq \lfloor v \rfloor$
   - **Branch up:** add constraint $x_j \geq \lceil v \rceil$
5. Recurse on child nodes.

**Visual B&B Tree (small example):**

```
                    [Root: LP z=21.0, x1=3, x2=1.5]
                           /              \
          [x2 ≤ 1: z=19.0]            [x2 ≥ 2: z=20.67]
          x1=3, x2=1 ✓INT              x1=2.67, x2=2
          INCUMBENT z=19               /            \
                             [x1 ≤ 2: z=20]    [x1 ≥ 3: INFEAS]
                             x1=2, x2=2 ✓INT    PRUNED
                             z=18 < 19
                             PRUNED (≤ incumbent)

Final optimal: x1=3, x2=1, z=19
```

**Search strategies:**

| Strategy | Description | Trade-off |
|----------|-------------|-----------|
| **Best-First** | Explore node with highest LP bound | Finds tight bound early; high memory |
| **Depth-First** | Go deep before backtracking | Low memory; finds feasible solutions fast |
| **Best-Bound + DFS hybrid** | Default in most solvers | Balanced |

---

### Cutting Planes

Cutting planes add new linear constraints (cuts) that are valid for all integer solutions but cut off the current fractional LP optimum — tightening the relaxation without removing any integer feasible points.

**Gomory Cuts:** Derived algorithmically from the LP tableau. For a row $i$ of the optimal simplex tableau with fractional right-hand side $\bar{b}_i$:

$$
\sum_j \bar{a}_{ij} x_j \geq \lceil \bar{b}_i \rceil
$$

where $\bar{a}_{ij}$ and $\bar{b}_i$ are tableau entries. Gomory proved that a finite sequence of such cuts yields the integer optimum.

**Cover Inequalities** (for 0-1 knapsack structure): If $S \subseteq N$ is a **cover** (i.e., $\sum_{j \in S} a_j > b$), then:

$$
\sum_{j \in S} x_j \leq |S| - 1
$$

This says: you cannot select all items in a cover set — at least one must be excluded.

**Intuition:** Cuts slice off fractional regions of the LP polytope that contain no integer points, forcing the LP optimum toward integer solutions.

---

### Branch and Cut

Modern IP solvers (CBC, HiGHS, SCIP, Gurobi) use **Branch and Cut**, which interleaves cutting planes at each B&B node:

1. At each node, solve the LP relaxation.
2. Generate cuts (Gomory, cover, clique, flow, etc.) and re-solve.
3. Repeat until cuts no longer improve the bound, then branch.

This dramatically reduces the size of the B&B tree compared to pure branching or pure cutting.

**Key solver capabilities:**

| Solver | License | Strengths |
|--------|---------|-----------|
| **CBC** | EPL (open) | Robust, integrated in PuLP |
| **HiGHS** | MIT (open) | State-of-the-art open-source |
| **SCIP** | Academic free | Research-grade, highly customizable |
| **Gurobi** | Commercial | Industry benchmark |

---

### Modeling Tricks

#### Big-M Formulation

Used to model logical implications: "if binary variable $y = 1$, then constraint $g(x) \leq 0$ is active."

$$
g(\mathbf{x}) \leq M(1 - y)
$$

where $M$ is a large enough constant so the constraint is non-binding when $y = 0$.

!!! warning "Big-M Pitfall"
    An $M$ that is too large weakens the LP relaxation. Choose the tightest valid $M$ (e.g., the maximum possible value of $g(\mathbf{x})$ over the feasible region).

**Example:** Model "facility $j$ can only serve customers if it is open ($y_j = 1$)":

$$
x_{ij} \leq y_j \quad \forall i, j
$$

(Here $M=1$ since $x_{ij}$ is binary — no big-M needed. Big-M is needed when $x$ is continuous.)

#### SOS Constraints

**SOS1 (Special Ordered Set type 1):** At most one variable in the set may be non-zero.
**SOS2:** At most two consecutive variables may be non-zero (used in piecewise linear approximations).

#### Linearizing Products of Binary Variables

If $z = x \cdot y$ where $x, y \in \{0,1\}$, replace with auxiliary variable $z \in \{0,1\}$ and add:

$$
z \leq x, \quad z \leq y, \quad z \geq x + y - 1
$$

For $z = x \cdot w$ where $x \in \{0,1\}$ and $w \in [0, U]$, use:

$$
z \leq Ux, \quad z \leq w, \quad z \geq w - U(1-x), \quad z \geq 0
$$

---

### Computational Complexity

- **LP** is solvable in polynomial time (ellipsoid method, interior-point).
- **IP is NP-hard** in general — no polynomial-time algorithm is known.
- **Practical limits** (approximate, hardware-dependent):

| Problem Class | Variables | Tractable? |
|--------------|-----------|-----------|
| Assignment/matching | 10,000s | Yes (polynomial special structure) |
| General binary IP | ~1,000 binary vars | Often yes with B&C |
| General MIP | 10,000s vars + 1,000s int | Depends heavily on structure |
| VRP, TSP (general) | 50–200 nodes | Hard; heuristics often used |

**When to accept near-optimal:** When the MIP gap is below a threshold (e.g., 1–5%), or when time constraints prevent exact solve. Modern solvers return the best incumbent and its gap, letting you decide.

---

### Check Your Understanding — Part 1

!!! question "Question 1"
    Why does adding integrality constraints make optimization problems harder than LP? What is the key structural property of LP that IP loses?

!!! question "Question 2"
    A planner solves the LP relaxation and gets $x_1 = 4.7, x_2 = 2.3$ with objective value 100. After solving the IP exactly, the optimal value is 94. What is the MIP gap? Why can't you recover the IP optimum by simply rounding the LP solution?

!!! question "Question 3"
    In Branch and Bound, when is a node **pruned**? Describe all three pruning conditions and explain why each is valid.

---

## Real-World Examples

### Example A — Military: Multi-Asset Weapon-Target Assignment (WTA)

!!! example "Scenario"
    A joint task force has **8 weapon systems** (2 cruise missiles, 2 artillery batteries, 2 attack helicopters, 2 fighter-bomber sorties) and **6 high-value targets** (command node, radar site, air defense battery, fuel depot, bridge, ammunition cache). Each weapon-target pair has a **probability of kill (Pk)**. Each weapon system can be assigned to at most one target. Weapon type expenditure is limited. **Maximize total expected kills.**

**Open-Source Tools:** PuLP (CBC), matplotlib, seaborn, Python

```python
# pip install pulp matplotlib seaborn numpy
import pulp
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

# ── Problem data ────────────────────────────────────────────────────────────
weapons = ["CM-1", "CM-2", "ARTY-1", "ARTY-2", "AH-1", "AH-2", "FB-1", "FB-2"]
targets = ["CMD", "RADAR", "SAM", "FUEL", "BRIDGE", "AMMO"]

# Probability of kill (Pk) for each weapon-target pair
# Rows = weapons, Cols = targets
pk_data = {
    #         CMD   RADAR  SAM   FUEL  BRIDGE AMMO
    "CM-1":  [0.85, 0.75,  0.80, 0.60, 0.70,  0.65],
    "CM-2":  [0.80, 0.78,  0.75, 0.55, 0.68,  0.62],
    "ARTY-1":[0.40, 0.35,  0.50, 0.70, 0.60,  0.75],
    "ARTY-2":[0.38, 0.32,  0.48, 0.72, 0.58,  0.77],
    "AH-1":  [0.55, 0.60,  0.65, 0.50, 0.45,  0.55],
    "AH-2":  [0.52, 0.58,  0.62, 0.48, 0.42,  0.52],
    "FB-1":  [0.70, 0.80,  0.75, 0.65, 0.80,  0.60],
    "FB-2":  [0.68, 0.77,  0.72, 0.63, 0.78,  0.58],
}

# Convert to dict of dicts for PuLP indexing
pk = {w: {t: pk_data[w][j] for j, t in enumerate(targets)} for w in weapons}

# Weapon type limits: at most 1 of each cruise missile on CMD/RADAR
# (modeling a salvo constraint — each CM can fire only once)
weapon_types = {
    "cruise_missile": ["CM-1", "CM-2"],
    "artillery":      ["ARTY-1", "ARTY-2"],
    "helicopter":     ["AH-1", "AH-2"],
    "fighter_bomber": ["FB-1", "FB-2"],
}

# ── Build the MIP ────────────────────────────────────────────────────────────
prob = pulp.LpProblem("Weapon_Target_Assignment", pulp.LpMaximize)

# Binary decision variable: assign weapon w to target t?
x = pulp.LpVariable.dicts("x", [(w, t) for w in weapons for t in targets], cat="Binary")

# Objective: maximize sum of Pk for all assignments
prob += pulp.lpSum(pk[w][t] * x[w, t] for w in weapons for t in targets), "Total_Expected_Kills"

# Constraint 1: Each weapon assigned to at most one target
for w in weapons:
    prob += pulp.lpSum(x[w, t] for t in targets) <= 1, f"weapon_once_{w}"

# Constraint 2: Each target receives at most 2 weapons (avoid overkill)
for t in targets:
    prob += pulp.lpSum(x[w, t] for w in weapons) <= 2, f"target_max2_{t}"

# Constraint 3: Each target must receive at least 1 weapon (all targets engaged)
for t in targets:
    prob += pulp.lpSum(x[w, t] for w in weapons) >= 1, f"target_min1_{t}"

# Constraint 4: High-value targets (CMD, RADAR, SAM) must receive a high-Pk weapon
# Require Pk >= 0.60 — modeled by restricting which weapons can engage them
hvt = ["CMD", "RADAR", "SAM"]
for t in hvt:
    eligible = [w for w in weapons if pk[w][t] >= 0.60]
    prob += pulp.lpSum(x[w, t] for w in eligible) >= 1, f"hvt_quality_{t}"

# ── Solve ────────────────────────────────────────────────────────────────────
prob.solve(pulp.PULP_CBC_CMD(msg=0))

print(f"Solver Status : {pulp.LpStatus[prob.status]}")
print(f"Expected Kills: {pulp.value(prob.objective):.4f}\n")

print(f"{'Weapon':<10} {'Target':<8} {'Pk':>6}")
print("-" * 28)
assignment_matrix = np.zeros((len(weapons), len(targets)))
for i, w in enumerate(weapons):
    for j, t in enumerate(targets):
        if x[w, t].varValue and x[w, t].varValue > 0.5:
            assignment_matrix[i, j] = pk[w][t]
            print(f"{w:<10} {t:<8} {pk[w][t]:>6.2f}")

# ── Visualize: Assignment heatmap ────────────────────────────────────────────
fig, ax = plt.subplots(figsize=(9, 5))
sns.heatmap(
    assignment_matrix,
    xticklabels=targets,
    yticklabels=weapons,
    annot=True,
    fmt=".2f",
    cmap="YlOrRd",
    linewidths=0.5,
    linecolor="gray",
    cbar_kws={"label": "Probability of Kill (Pk)"},
    ax=ax,
)
ax.set_title("WTA Assignment Matrix — Expected Kills by Weapon-Target Pair", fontsize=12)
ax.set_xlabel("Target")
ax.set_ylabel("Weapon System")
plt.tight_layout()
plt.savefig("wta_heatmap.png", dpi=150)
plt.show()
print("\nHeatmap saved to wta_heatmap.png")
```

!!! note "MIP Gap Discussion"
    CBC solves this 48-variable binary IP instantly (< 0.1 s). As Pk thresholds on high-value targets increase (e.g., from 0.60 to 0.80), fewer weapons qualify, the feasible region shrinks, and the MIP gap — the distance between the LP relaxation bound and the best integer solution — may widen. Try tightening the `hvt_quality` constraints and observe how the expected kills change.

---

### Check Your Understanding — Part 2

!!! question "Question 1"
    In the WTA problem, why is the LP relaxation bound always ≥ the IP optimum? Can the LP optimum ever equal the IP optimum without any branching?

!!! question "Question 2"
    How does the **cover inequality** concept apply to the WTA problem? Identify a set of weapon-target assignments that forms a "cover" and write the corresponding constraint.

!!! question "Question 3"
    The facility location problem below uses CP-SAT rather than a MIP solver. What is the key algorithmic difference between CP-SAT (constraint propagation + SAT) and Branch and Cut? When would you prefer one over the other?

---

### Example B — Civilian: Capacitated Facility Location

!!! example "Scenario"
    A retail chain evaluates **5 candidate distribution centers** (DCs) and must serve **12 customers**. Each DC has a fixed opening cost and a capacity limit. Each customer has a demand. Transportation cost is proportional to distance. **Minimize total fixed + transportation cost** subject to capacity constraints. Perform sensitivity analysis on how the number of open DCs changes as the fixed cost multiplier increases.

**Open-Source Tools:** OR-Tools CP-SAT, matplotlib, numpy, Python

```python
# pip install ortools matplotlib numpy
import numpy as np
import matplotlib.pyplot as plt
from ortools.sat.python import cp_model

# ── Problem data ──────────────────────────────────────────────────────────────
np.random.seed(42)

n_facilities = 5
n_customers  = 12

# 2D coordinates for visualization
facility_coords = np.array([
    [10, 80], [30, 50], [60, 70], [80, 30], [50, 20]
])
customer_coords = np.array([
    [15, 90], [25, 70], [40, 85], [55, 75], [70, 60],
    [20, 40], [35, 30], [50, 45], [65, 35], [80, 50],
    [45, 15], [75, 10]
])

# Customer demands (units)
demand = [8, 12, 6, 10, 9, 7, 11, 5, 14, 8, 6, 10]

# Facility capacity and fixed cost
capacity   = [45, 40, 50, 40, 35]
fixed_cost = [120, 100, 140, 110, 90]  # base fixed costs

# Transport cost: Euclidean distance × 2 (integer, scaled ×10 for CP-SAT)
def transport_cost(cust_idx, fac_idx, scale=10):
    d = np.linalg.norm(customer_coords[cust_idx] - facility_coords[fac_idx])
    return int(d * scale)

transport = [[transport_cost(i, j) for j in range(n_facilities)]
             for i in range(n_customers)]


def solve_facility_location(fixed_cost_multiplier=1.0):
    """Solve the capacitated facility location MIP via CP-SAT."""
    model = cp_model.CpModel()

    # ── Variables ──────────────────────────────────────────────────────────
    # y[j] = 1 if facility j is open
    y = [model.NewBoolVar(f"open_{j}") for j in range(n_facilities)]
    # x[i][j] = 1 if customer i assigned to facility j
    x = [[model.NewBoolVar(f"assign_{i}_{j}")
          for j in range(n_facilities)]
         for i in range(n_customers)]

    # ── Constraints ────────────────────────────────────────────────────────
    # Each customer assigned to exactly one facility
    for i in range(n_customers):
        model.AddExactlyOne(x[i])

    # Can only assign to open facilities
    for i in range(n_customers):
        for j in range(n_facilities):
            model.Add(x[i][j] <= y[j])

    # Capacity constraints
    for j in range(n_facilities):
        model.Add(sum(demand[i] * x[i][j] for i in range(n_customers)) <= capacity[j])

    # ── Objective ──────────────────────────────────────────────────────────
    # Scale fixed costs and multiply by multiplier (use integer arithmetic)
    scaled_fixed = [int(fixed_cost[j] * fixed_cost_multiplier * 10)
                    for j in range(n_facilities)]
    total_fixed     = sum(scaled_fixed[j] * y[j] for j in range(n_facilities))
    total_transport = sum(transport[i][j] * x[i][j]
                         for i in range(n_customers)
                         for j in range(n_facilities))
    model.Minimize(total_fixed + total_transport)

    # ── Solve ──────────────────────────────────────────────────────────────
    solver = cp_model.CpSolver()
    solver.parameters.max_time_in_seconds = 30.0
    status = solver.Solve(model)

    if status in (cp_model.OPTIMAL, cp_model.FEASIBLE):
        open_facs = [j for j in range(n_facilities) if solver.Value(y[j]) == 1]
        assignment = [
            next(j for j in range(n_facilities) if solver.Value(x[i][j]) == 1)
            for i in range(n_customers)
        ]
        return open_facs, assignment, solver.ObjectiveValue()
    return [], [], float("inf")


# ── Solve base case ──────────────────────────────────────────────────────────
open_facs, assignment, obj = solve_facility_location(1.0)

print(f"Total Cost (scaled): {obj:.0f}")
print(f"Open facilities: {[f'DC-{j}' for j in open_facs]}")
for j in open_facs:
    served = [i for i in range(n_customers) if assignment[i] == j]
    load   = sum(demand[i] for i in served)
    print(f"  DC-{j} (cap={capacity[j]}): customers {served}, load={load}")

# ── Visualize: Facility map ──────────────────────────────────────────────────
colors = plt.cm.tab10.colors
fig, ax = plt.subplots(figsize=(9, 7))

# Draw assignment lines
for i in range(n_customers):
    j = assignment[i]
    ax.plot(
        [customer_coords[i, 0], facility_coords[j, 0]],
        [customer_coords[i, 1], facility_coords[j, 1]],
        color=colors[j], alpha=0.4, linewidth=1.2
    )

# Plot facilities
for j in range(n_facilities):
    marker = "^" if j in open_facs else "x"
    size   = 200 if j in open_facs else 80
    ax.scatter(facility_coords[j, 0], facility_coords[j, 1],
               c=[colors[j]], marker=marker, s=size, zorder=5,
               edgecolors="black", linewidths=1.2)
    ax.annotate(f"DC-{j}{'✓' if j in open_facs else '✗'}",
                facility_coords[j], textcoords="offset points",
                xytext=(6, 6), fontsize=9, fontweight="bold")

# Plot customers
for i in range(n_customers):
    j = assignment[i]
    ax.scatter(customer_coords[i, 0], customer_coords[i, 1],
               c=[colors[j]], marker="o", s=60, zorder=4,
               edgecolors="black", linewidths=0.8)
    ax.annotate(f"C{i}({demand[i]})", customer_coords[i],
                textcoords="offset points", xytext=(4, 4), fontsize=7)

ax.set_title("Capacitated Facility Location — Open DCs and Customer Assignments",
             fontsize=12)
ax.set_xlabel("X Coordinate")
ax.set_ylabel("Y Coordinate")
ax.legend(
    [plt.Line2D([0], [0], marker="^", color="w", markerfacecolor="gray",
                markersize=10, markeredgecolor="black"),
     plt.Line2D([0], [0], marker="x", color="w", markerfacecolor="gray",
                markersize=10, markeredgecolor="black"),
     plt.Line2D([0], [0], marker="o", color="w", markerfacecolor="gray",
                markersize=8, markeredgecolor="black")],
    ["Open DC (▲)", "Closed DC (✗)", "Customer (●)"],
    loc="lower right"
)
plt.tight_layout()
plt.savefig("facility_location.png", dpi=150)
plt.show()

# ── Sensitivity analysis: fixed cost multiplier vs. open DCs ─────────────────
multipliers   = [0.5, 0.75, 1.0, 1.25, 1.5, 1.75, 2.0, 2.5, 3.0]
open_dc_counts = []
total_costs    = []

for m in multipliers:
    of, _, cost = solve_facility_location(m)
    open_dc_counts.append(len(of))
    total_costs.append(cost)
    print(f"Fixed cost ×{m:.2f}: {len(of)} DCs open, total cost = {cost:.0f}")

fig, (ax1, ax2) = plt.subplots(1, 2, figsize=(12, 4))
ax1.plot(multipliers, open_dc_counts, "bo-", linewidth=2)
ax1.set_xlabel("Fixed Cost Multiplier")
ax1.set_ylabel("Number of Open DCs")
ax1.set_title("Open DCs vs. Fixed Cost Multiplier")
ax1.grid(True, alpha=0.3)
ax1.set_yticks(range(0, n_facilities + 1))

ax2.plot(multipliers, total_costs, "rs-", linewidth=2)
ax2.set_xlabel("Fixed Cost Multiplier")
ax2.set_ylabel("Total Cost (scaled)")
ax2.set_title("Total Cost vs. Fixed Cost Multiplier")
ax2.grid(True, alpha=0.3)

plt.suptitle("Sensitivity Analysis: Fixed Cost Impact on Facility Location",
             fontsize=12)
plt.tight_layout()
plt.savefig("facility_sensitivity.png", dpi=150)
plt.show()
print("\nSensitivity plot saved to facility_sensitivity.png")
```

---

## Interactive Exercises

### Exercise 1 — Guided (Warm-Up): 0-1 Knapsack

!!! question "Task"
    A special operations team can carry a rucksack with **capacity 60 kg**. Select equipment items to maximize total mission value. Fill in the `TODO` blocks, then verify your output matches the expected result.

```python
# pip install pulp
import pulp

# Equipment items: name → (value, weight_kg)
items  = ["NVG", "Explosives", "MedKit", "Radio", "AmmoBox", "DroneKit", "Rations"]
value  = {"NVG": 90,  "Explosives": 120, "MedKit": 80,  "Radio": 110,
          "AmmoBox": 70, "DroneKit": 150, "Rations": 50}
weight = {"NVG": 10,  "Explosives": 25,  "MedKit": 12,  "Radio": 8,
          "AmmoBox": 20, "DroneKit": 30,  "Rations": 15}
capacity = 60

prob = pulp.LpProblem("SOF_Knapsack", pulp.LpMaximize)

# TODO: Define binary decision variables (1 = take the item, 0 = leave it)
# Hint: use pulp.LpVariable.dicts with cat="Binary" and the items list as keys
# x = ...

# TODO: Objective — maximize total mission value
# prob += ...

# TODO: Constraint — total weight must not exceed capacity
# prob += ...

prob.solve(pulp.PULP_CBC_CMD(msg=0))

print(f"Status: {pulp.LpStatus[prob.status]}")
total_weight = 0
for i in items:
    if x[i].varValue == 1:
        print(f"  ✓ {i:<12} value={value[i]:>4}, weight={weight[i]:>3} kg")
        total_weight += weight[i]
print(f"Total Value : {pulp.value(prob.objective):.0f}")
print(f"Total Weight: {total_weight} / {capacity} kg")
```

!!! success "Expected Output"
    ```
    Status: Optimal
      ✓ NVG          value=  90, weight= 10 kg
      ✓ MedKit       value=  80, weight= 12 kg
      ✓ Radio        value= 110, weight=  8 kg
      ✓ DroneKit     value= 150, weight= 30 kg
    Total Value : 430
    Total Weight: 60 / 60 kg
    ```

    !!! tip "Hint"
        The optimal solution selects **NVG + MedKit + Radio + DroneKit** — exactly filling the 60 kg capacity for a total value of **430**. Notice that Explosives (25 kg, value 120) is left behind even though it has high value, because including it would require dropping two lighter, higher-value items. If you get a different answer, check that your weight constraint uses `<=` (not `==`) and that your binary variables are defined with `cat="Binary"`.

---

### Exercise 2 — Exploratory (Intermediate): Crew Shift Scheduling

!!! question "Task"
    A forward operating base (FOB) requires guard coverage across **24 hours**. Guards work **8-hour shifts** starting at hours 0, 8, or 16. The minimum coverage requirement varies by time block. Formulate as an integer program, solve, and explore what happens when minimum coverage requirements increase by 20%.

```python
# pip install pulp numpy
import pulp
import numpy as np

# Shift definitions: shift s starts at hour starts[s] and covers hours starts[s] to starts[s]+7
shifts = ["Day (0-7)", "Eve (8-15)", "Night (16-23)"]
starts = [0, 8, 16]

# Minimum guards required per shift window (base requirement)
min_coverage = {"Day (0-7)": 3, "Eve (8-15)": 4, "Night (16-23)": 2}

# Each guard can work at most 1 shift per day
# Number of available guards
n_guards = 12

prob = pulp.LpProblem("FOB_Shift_Scheduling", pulp.LpMinimize)

# Decision variable: how many guards assigned to each shift
g = pulp.LpVariable.dicts("guards", shifts, lowBound=0, cat="Integer")

# Objective: minimize total guards deployed (or use total cost if guards have rates)
prob += pulp.lpSum(g[s] for s in shifts), "Total_Guards_Deployed"

# Coverage constraints: meet minimum per shift
for s in shifts:
    prob += g[s] >= min_coverage[s], f"min_coverage_{s}"

# Total guards cannot exceed available pool
prob += pulp.lpSum(g[s] for s in shifts) <= n_guards, "Guard_Pool"

prob.solve(pulp.PULP_CBC_CMD(msg=0))

print("=== Base Schedule ===")
print(f"Status: {pulp.LpStatus[prob.status]}")
for s in shifts:
    print(f"  {s}: {int(g[s].varValue)} guards (min={min_coverage[s]})")
print(f"Total deployed: {int(pulp.value(prob.objective))}")

# ── Exploration: increase minimum coverage by 20% ─────────────────────────
print("\n=== +20% Coverage Requirement ===")
min_coverage_20 = {s: int(np.ceil(v * 1.2)) for s, v in min_coverage.items()}

prob2 = pulp.LpProblem("FOB_Shift_Scheduling_20pct", pulp.LpMinimize)
g2 = pulp.LpVariable.dicts("guards", shifts, lowBound=0, cat="Integer")
prob2 += pulp.lpSum(g2[s] for s in shifts)
for s in shifts:
    prob2 += g2[s] >= min_coverage_20[s], f"min_cov_{s}"
prob2 += pulp.lpSum(g2[s] for s in shifts) <= n_guards, "Guard_Pool"
prob2.solve(pulp.PULP_CBC_CMD(msg=0))

print(f"Status: {pulp.LpStatus[prob2.status]}")
for s in shifts:
    print(f"  {s}: {int(g2[s].varValue)} guards (min={min_coverage_20[s]})")
if prob2.status == 1:
    print(f"Total deployed: {int(pulp.value(prob2.objective))}")
else:
    print("  ⚠ Infeasible — guard pool exhausted! Need to expand available guards.")

# TODO: What is the minimum pool size needed to satisfy +20% requirements?
# Hint: Remove the guard pool constraint and re-solve.
```

!!! question "Exploration Prompts"
    1. What happens to feasibility if you increase the Night shift minimum to 5 guards?
    2. Modify the problem to minimize **cost** instead of guards, where Day guards cost $200/shift, Eve guards $250/shift, Night guards $300/shift.
    3. Add a constraint that each individual guard works exactly 1 shift (introduce a 2D assignment matrix $x_{g,s} \in \{0,1\}$ for guard $g$, shift $s$).

---

### Exercise 3 — Challenge (Advanced): Vehicle Routing Problem (VRP)

!!! question "Task"
    Plan delivery routes for **3 vehicles** serving **15 customer locations** from a central depot. Each vehicle has capacity **100 units**. Use OR-Tools routing library to find minimum-distance routes. Visualize routes with distinct colors. Analyze: does adding a 4th vehicle reduce total distance?

```python
# pip install ortools matplotlib numpy
import numpy as np
import matplotlib.pyplot as plt
from ortools.constraint_solver import routing_enums_pb2
from ortools.constraint_solver import pywrapcp

# ── Problem data ─────────────────────────────────────────────────────────────
np.random.seed(7)

# 16 locations: index 0 = depot, 1-15 = customers
n_locations = 16
coords = np.random.randint(0, 100, size=(n_locations, 2))
coords[0] = [50, 50]  # depot at center

# Customer demands (depot has demand 0)
demands = [0] + list(np.random.randint(5, 20, size=n_locations - 1))

# Distance matrix (integer, scaled ×10)
def dist_matrix(coords, scale=10):
    n = len(coords)
    mat = np.zeros((n, n), dtype=int)
    for i in range(n):
        for j in range(n):
            mat[i, j] = int(np.linalg.norm(coords[i] - coords[j]) * scale)
    return mat

distance_matrix = dist_matrix(coords)

def solve_vrp(n_vehicles=3, vehicle_capacity=100, time_limit=30):
    """Solve the CVRP using OR-Tools routing library."""
    manager  = pywrapcp.RoutingIndexManager(n_locations, n_vehicles, 0)
    routing  = pywrapcp.RoutingModel(manager)

    # Distance callback
    def distance_callback(from_index, to_index):
        return distance_matrix[manager.IndexToNode(from_index)][manager.IndexToNode(to_index)]
    transit_cb_idx = routing.RegisterTransitCallback(distance_callback)
    routing.SetArcCostEvaluatorOfAllVehicles(transit_cb_idx)

    # Demand callback
    def demand_callback(from_index):
        return demands[manager.IndexToNode(from_index)]
    demand_cb_idx = routing.RegisterUnaryTransitCallback(demand_callback)
    routing.AddDimensionWithVehicleCapacity(
        demand_cb_idx,
        0,                          # no slack
        [vehicle_capacity] * n_vehicles,
        True,                       # start cumul at zero
        "Capacity"
    )

    # Search parameters
    search_params = pywrapcp.DefaultRoutingSearchParameters()
    search_params.first_solution_strategy = (
        routing_enums_pb2.FirstSolutionStrategy.PATH_CHEAPEST_ARC
    )
    search_params.local_search_metaheuristic = (
        routing_enums_pb2.LocalSearchMetaheuristic.GUIDED_LOCAL_SEARCH
    )
    search_params.time_limit.seconds = time_limit

    solution = routing.SolveWithParameters(search_params)

    if not solution:
        return None, float("inf")

    routes = []
    total_dist = 0
    for v in range(n_vehicles):
        route = []
        idx   = routing.Start(v)
        while not routing.IsEnd(idx):
            route.append(manager.IndexToNode(idx))
            idx = solution.Value(routing.NextVar(idx))
        route.append(0)  # return to depot
        route_dist = sum(
            distance_matrix[route[k]][route[k+1]] for k in range(len(route)-1)
        )
        routes.append(route)
        total_dist += route_dist
    return routes, total_dist / 10  # un-scale


def plot_routes(routes, coords, title, filename):
    """Plot VRP routes on a 2D map."""
    colors = ["tab:blue", "tab:orange", "tab:green", "tab:red", "tab:purple"]
    fig, ax = plt.subplots(figsize=(9, 7))

    # Plot locations
    ax.scatter(coords[1:, 0], coords[1:, 1], c="gray", s=80, zorder=5,
               edgecolors="black", linewidths=0.8)
    ax.scatter(coords[0, 0], coords[0, 1], c="red", s=200, marker="*",
               zorder=6, label="Depot")

    for i in range(1, n_locations):
        ax.annotate(f"C{i}\n({demands[i]})", coords[i],
                    textcoords="offset points", xytext=(5, 5), fontsize=7)

    # Plot routes
    for v, route in enumerate(routes):
        c = colors[v % len(colors)]
        for k in range(len(route) - 1):
            ax.annotate(
                "",
                xy=coords[route[k+1]],
                xytext=coords[route[k]],
                arrowprops=dict(arrowstyle="->", color=c, lw=1.8)
            )
        label = f"Vehicle {v+1} ({len(route)-2} stops)"
        ax.plot([], [], color=c, linewidth=2, label=label)

    ax.set_title(title, fontsize=11)
    ax.set_xlabel("X"); ax.set_ylabel("Y")
    ax.legend(loc="upper left", fontsize=8)
    plt.tight_layout()
    plt.savefig(filename, dpi=150)
    plt.show()
    print(f"Saved: {filename}")


# ── Solve with 3 vehicles ─────────────────────────────────────────────────
routes3, dist3 = solve_vrp(n_vehicles=3)
print(f"\n3 Vehicles — Total Distance: {dist3:.1f} units")
for v, r in enumerate(routes3):
    load = sum(demands[n] for n in r if n != 0)
    print(f"  Vehicle {v+1}: {r}  (load={load}/{100})")
plot_routes(routes3, coords, "CVRP — 3 Vehicles", "vrp_3_vehicles.png")

# ── Solve with 4 vehicles — sensitivity analysis ──────────────────────────
routes4, dist4 = solve_vrp(n_vehicles=4)
print(f"\n4 Vehicles — Total Distance: {dist4:.1f} units")
reduction = ((dist3 - dist4) / dist3) * 100 if dist3 > 0 else 0
print(f"Distance reduction with 4th vehicle: {reduction:.1f}%")

# ── Nearest-neighbor heuristic (bonus comparison) ─────────────────────────
def nearest_neighbor_vrp(n_vehicles=3, vehicle_capacity=100):
    """Simple nearest-neighbor heuristic for CVRP."""
    unvisited = set(range(1, n_locations))
    routes = []
    for _ in range(n_vehicles):
        if not unvisited:
            routes.append([0, 0])
            continue
        route = [0]
        load  = 0
        current = 0
        while unvisited:
            candidates = [n for n in unvisited if load + demands[n] <= vehicle_capacity]
            if not candidates:
                break
            nearest = min(candidates, key=lambda n: distance_matrix[current][n])
            route.append(nearest)
            load += demands[nearest]
            unvisited.discard(nearest)
            current = nearest
        route.append(0)
        routes.append(route)
    return routes

nn_routes = nearest_neighbor_vrp()
nn_dist   = sum(
    sum(distance_matrix[r[k]][r[k+1]] for k in range(len(r)-1))
    for r in nn_routes
) / 10
print(f"\nNearest-Neighbor Heuristic — Total Distance: {nn_dist:.1f} units")
print(f"MIP/GLS improvement over NN: {((nn_dist - dist3) / nn_dist * 100):.1f}%")
```

!!! question "Analysis Prompts"
    1. How does the total distance change when you add a 4th vehicle? Is the marginal improvement worth the added cost?
    2. What is the difference in solution quality between the nearest-neighbor heuristic and the OR-Tools GLS solution?
    3. Increase the number of customers to 30. How does solve time change? At what point does the GLS solution become impractical?

---

### Check Your Understanding — Part 3

!!! question "Question 1"
    In the 0-1 Knapsack exercise, what is the LP relaxation optimum if you allow fractional item selection? How does it compare to the IP optimum?

!!! question "Question 2"
    The shift scheduling problem is an IP with integer (not binary) variables. Could you reformulate it as a binary IP? What would the binary variables represent, and what constraints would change?

!!! question "Question 3"
    The VRP exercise uses OR-Tools' routing library rather than a generic MIP solver. What specialized algorithmic techniques does OR-Tools use for routing that make it more efficient than pure Branch and Cut for this problem class?

---

## Check Your Understanding — Full Module

1. Why does adding integrality constraints make problems harder than LP?
2. What is the LP relaxation gap and why does it matter for solver termination?
3. When is a greedy heuristic a good alternative to solving an IP exactly?
4. Describe two situations where the big-M formulation can cause numerical issues and how to mitigate them.
5. Compare Branch and Bound, Cutting Planes, and Branch and Cut. In which scenarios does each approach dominate?

---

!!! tip "Prompt Template"
    Use the [Agent Prompt](prompt.md) for this topic, or the [Generic Prompt Template](../prompt-template.md) to generate additional lectures, examples, and exercises.
