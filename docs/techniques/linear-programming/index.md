# Linear Programming

> Optimizing a linear objective function subject to linear constraints.

---

## Part 1 — Lecture

### What is Linear Programming?

**Plain-language definition:** Linear Programming (LP) finds the best value of a quantity you care about (profit, cost, effectiveness) by choosing how much of several things to do, subject to a limited set of "rules" — where both what you're measuring and all the rules are straight-line (linear) relationships. No curves, no products of variables.

**Formal definition:** An LP minimizes (or maximizes) a linear objective function over a set of decision variables that must satisfy a finite number of linear equality and inequality constraints.

$$
\begin{align}
\text{Minimize (or Maximize)} \quad & \mathbf{c}^\top \mathbf{x} \\
\text{subject to} \quad & \mathbf{A}\mathbf{x} \leq \mathbf{b} \\
                        & \mathbf{x} \geq \mathbf{0}
\end{align}
$$

| Symbol | Meaning |
|--------|---------|
| $\mathbf{x} \in \mathbb{R}^n$ | Decision variable vector — *what we choose* |
| $\mathbf{c} \in \mathbb{R}^n$ | Objective coefficient vector — *cost or profit per unit* |
| $\mathbf{A} \in \mathbb{R}^{m \times n}$ | Constraint coefficient matrix |
| $\mathbf{b} \in \mathbb{R}^m$ | Right-hand side (resource limits) |
| Feasible region | The convex polytope $\{\mathbf{x} \mid \mathbf{A}\mathbf{x} \leq \mathbf{b},\; \mathbf{x} \geq \mathbf{0}\}$ |

### Mathematical Formulation

#### Standard Form

Every LP can be written in **standard form** (equalities only) by introducing **slack variables** $\mathbf{s} \geq \mathbf{0}$:

$$
\text{Minimize} \quad \mathbf{c}^\top \mathbf{x} \quad \text{s.t.} \quad \mathbf{A}\mathbf{x} + \mathbf{s} = \mathbf{b}, \quad \mathbf{x} \geq \mathbf{0}, \quad \mathbf{s} \geq \mathbf{0}
$$

#### Canonical Form

The **canonical (inequality) form** keeps constraints as inequalities. Both forms are equivalent — solvers typically work with standard form internally.

#### Slack, Surplus, and Artificial Variables

| Variable type | Purpose | Sign |
|---------------|---------|------|
| **Slack** $s_i$ | Absorbs unused capacity in a $\leq$ constraint | $\geq 0$ |
| **Surplus** $e_i$ | Measures excess above a $\geq$ constraint minimum | $\geq 0$ |
| **Artificial** $a_i$ | Provides an initial feasible basis for Phase-I simplex | $\geq 0$, penalized to 0 at optimum |

!!! note "Why standard form?"
    Solvers like HiGHS and the revised Simplex algorithm operate on square basis matrices derived from the equality constraints in standard form. Converting to this form is handled automatically by modelling languages like PuLP and Pyomo.

### The Simplex Algorithm

The **Simplex algorithm** (Dantzig, 1947) exploits the key theorem: *if an LP has a finite optimum, at least one optimal solution occurs at a vertex (extreme point) of the feasible region.* Simplex walks from vertex to vertex, improving the objective at each step.

#### Step-by-Step Walkthrough

Consider the small 2-variable LP:

$$
\begin{align}
\text{Maximize} \quad & z = 5x_1 + 4x_2 \\
\text{s.t.} \quad & 6x_1 + 4x_2 \leq 24 \\
                  & x_1 + 2x_2 \leq 6  \\
                  & x_1,\, x_2 \geq 0
\end{align}
$$

**Step 1 — Convert to standard form** (add slacks $s_1, s_2$):

$$
6x_1 + 4x_2 + s_1 = 24, \qquad x_1 + 2x_2 + s_2 = 6
$$

**Step 2 — Initial basic feasible solution (BFS):** Set $x_1 = x_2 = 0$; then $s_1 = 24, s_2 = 6$. This is vertex $O = (0, 0)$, $z = 0$.

**Step 3 — Choose entering variable:** Most negative reduced cost. $\bar{c}_1 = -5$ (most negative) → $x_1$ enters.

**Step 4 — Choose leaving variable (minimum ratio test):**

$$
\min\left(\frac{24}{6},\; \frac{6}{1}\right) = \min(4, 6) = 4 \quad \Rightarrow \quad s_1 \text{ leaves}
$$

**Step 5 — Pivot:** $x_1 = 4$, $x_2 = 0$, $s_1 = 0$, $s_2 = 2$. Vertex $A = (4, 0)$, $z = 20$.

**Step 6 — Repeat** until all reduced costs $\geq 0$. Next pivot brings us to vertex $B = (3, 1.5)$, $z = 21$.

**Step 7 — Optimality:** All reduced costs non-negative → $\mathbf{x}^* = (3, 1.5)$, $z^* = 21$.

!!! note "Geometric insight"
    In 2D the feasible region is a convex polygon. Simplex moves along edges from one corner to the next, always increasing (maximising) the objective — picture iso-profit lines sweeping until they just touch the feasible polygon.

#### Geometric Visualization

```python
# pip install numpy matplotlib
import numpy as np
import matplotlib.pyplot as plt
from matplotlib.patches import Polygon
from matplotlib.collections import PatchCollection

fig, ax = plt.subplots(figsize=(7, 5))

# Shade feasible region vertices (manually computed for this small LP)
vertices = np.array([[0, 0], [4, 0], [3, 1.5], [0, 3]])
polygon = Polygon(vertices, closed=True, facecolor="#c6e2ff", edgecolor="navy", linewidth=1.5)
ax.add_patch(polygon)

# Constraint lines
x = np.linspace(0, 5, 300)
ax.plot(x, (24 - 6*x) / 4, label=r"$6x_1+4x_2\leq24$", color="steelblue")
ax.plot(x, (6 - x) / 2,    label=r"$x_1+2x_2\leq6$",   color="darkorange")

# Iso-profit lines
for z in [10, 16, 21]:
    ax.plot(x, (z - 5*x) / 4, "--", color="green", alpha=0.5)
ax.plot(x, (21 - 5*x) / 4, "--", color="green", linewidth=2, label="Iso-profit z=21 (optimal)")

# Mark vertices
for v, label in zip(vertices, ["O(0,0)", "A(4,0)", "B(3,1.5)", "C(0,3)"]):
    ax.plot(*v, "ko", ms=6)
    ax.annotate(label, v, textcoords="offset points", xytext=(6, 4), fontsize=9)

ax.set_xlim(-0.3, 5); ax.set_ylim(-0.3, 4.5)
ax.set_xlabel(r"$x_1$"); ax.set_ylabel(r"$x_2$")
ax.set_title("Simplex: feasible region and iso-profit lines")
ax.legend(fontsize=8); ax.grid(True, alpha=0.3)
plt.tight_layout()
plt.savefig("simplex_geometry.png", dpi=150)
plt.show()
```

### Duality Theory

Every LP (the **primal**) has a paired **dual** LP. For the standard minimisation primal:

$$
\begin{align}
\textbf{Primal} \quad & \min \mathbf{c}^\top \mathbf{x} \quad \text{s.t.} \quad \mathbf{A}\mathbf{x} \geq \mathbf{b},\; \mathbf{x} \geq \mathbf{0} \\
\textbf{Dual}   \quad & \max \mathbf{b}^\top \mathbf{y} \quad \text{s.t.} \quad \mathbf{A}^\top \mathbf{y} \leq \mathbf{c},\; \mathbf{y} \geq \mathbf{0}
\end{align}
$$

**Key results:**

| Theorem | Statement |
|---------|-----------|
| Weak duality | $\mathbf{b}^\top \mathbf{y} \leq \mathbf{c}^\top \mathbf{x}$ for any feasible primal–dual pair |
| Strong duality | At optimality, $\mathbf{b}^\top \mathbf{y}^* = \mathbf{c}^\top \mathbf{x}^*$ |
| Complementary slackness | $x_j^* (\mathbf{A}^\top \mathbf{y}^* - \mathbf{c})_j = 0$ and $y_i^* (\mathbf{A}\mathbf{x}^* - \mathbf{b})_i = 0$ |

**Shadow prices** (dual variables $y_i^*$) answer: *"If I relax constraint $i$ by one unit, how much does the optimal objective improve?"* This is enormously useful for resource allocation decisions.

!!! note "Operational interpretation"
    A shadow price of \$3.50 on a fuel constraint means: procuring one additional litre of fuel is worth up to \$3.50 in mission value. If fuel costs less than \$3.50/litre, buy more.

### Software Landscape

| Library | When to use | Backend solver |
|---------|-------------|----------------|
| **PuLP** | Teaching, rapid prototyping, LP/MIP | CBC, HiGHS, GLPK, Gurobi (optional) |
| **SciPy `linprog`** | Pure LP, no modelling overhead, scientific workflows | HiGHS (default since v1.7) |
| **Pyomo** | Complex models, multi-stage, NLP, academic research | GLPK, HiGHS, CPLEX, Gurobi |
| **OR-Tools** | Large-scale combinatorial, scheduling, routing | GLOP (LP), SCIP (MIP) |

!!! tip "Recommended starting point"
    For most LP problems, start with **PuLP + HiGHS**. Graduate to Pyomo when models become multi-block or nonlinear.

### Limitations and Failure Modes

| Failure mode | Cause | Detection |
|--------------|-------|-----------|
| **Infeasibility** | Constraints contradict each other (empty feasible region) | Solver returns `Infeasible`; check Phase-I residuals |
| **Unboundedness** | Objective can improve without limit | Solver returns `Unbounded`; a constraint is missing |
| **Degeneracy** | Multiple constraints pass through the same vertex | Simplex may cycle (use Bland's rule or perturbation) |
| **Numerical instability** | Very large or very small coefficients | Pre-scale the model; use HiGHS with scaling enabled |

!!! warning "Always validate your formulation"
    Infeasibility and unboundedness almost always indicate a modelling error (forgotten constraint, wrong variable bounds) rather than a genuine property of the real-world problem.

---

### Check Your Understanding — Part 1

1. A constraint $3x_1 + 2x_2 \geq 12$ is converted to standard form. What type of variable is added, and what sign does it take?
2. Why must the feasible region of an LP be convex, and why does this guarantee that Simplex terminates at a global optimum?
3. A shadow price of zero on a resource constraint tells you what about that resource?

---

## Part 2 — Real-World Examples

### Example A — Military: Multi-Echelon Resource Allocation

!!! example "Scenario"
    A theater commander must allocate three aviation asset types — Attack Helicopters (AH), Transport Helicopters (TH), and UAVs — across three operational axes (North, Center, South) to maximise expected mission effectiveness. Constraints cap total available fuel-hours, crew-hours, and maintenance slots per asset type.

**Open-Source Tools:** PuLP, Python 3.10+

```python
# pip install pulp
import pulp

# ── Data ──────────────────────────────────────────────────────────────────────
# Decision variable x[asset][axis]: sorties assigned
assets = ["AH", "TH", "UAV"]
axes   = ["North", "Center", "South"]

# Effectiveness score (mission value per sortie)
effectiveness = {
    ("AH",  "North"):  9, ("AH",  "Center"): 7, ("AH",  "South"):  8,
    ("TH",  "North"):  5, ("TH",  "Center"): 6, ("TH",  "South"):  4,
    ("UAV", "North"):  6, ("UAV", "Center"): 8, ("UAV", "South"):  7,
}

# Resource consumption per sortie (fuel-hrs, crew-hrs, maintenance-slots)
fuel  = {"AH": 4.0, "TH": 2.5, "UAV": 1.0}
crew  = {"AH": 3.0, "TH": 2.0, "UAV": 0.5}
maint = {"AH": 2.0, "TH": 1.5, "UAV": 0.8}

# Available resources
fuel_cap  = {"AH": 60, "TH": 50, "UAV": 40}   # fuel-hours per asset type
crew_cap  = 120                                  # total crew-hours across all assets
maint_cap = 80                                   # total maintenance slots

# Minimum sorties per axis (commander's intent)
min_sorties = {"North": 4, "Center": 3, "South": 3}

# ── Model ─────────────────────────────────────────────────────────────────────
prob = pulp.LpProblem("Aviation_Allocation", pulp.LpMaximize)

x = pulp.LpVariable.dicts("sorties",
                           [(a, ax) for a in assets for ax in axes],
                           lowBound=0)

# Objective: maximise total effectiveness
prob += pulp.lpSum(effectiveness[(a, ax)] * x[(a, ax)]
                   for a in assets for ax in axes), "Total_Effectiveness"

# Fuel constraint per asset type
for a in assets:
    prob += (pulp.lpSum(fuel[a] * x[(a, ax)] for ax in axes) <= fuel_cap[a],
             f"Fuel_{a}")

# Crew constraint (shared pool)
prob += (pulp.lpSum(crew[a] * x[(a, ax)] for a in assets for ax in axes) <= crew_cap,
         "Crew_Total")

# Maintenance constraint (shared pool)
prob += (pulp.lpSum(maint[a] * x[(a, ax)] for a in assets for ax in axes) <= maint_cap,
         "Maint_Total")

# Minimum sorties per axis
for ax in axes:
    prob += (pulp.lpSum(x[(a, ax)] for a in assets) >= min_sorties[ax],
             f"Min_{ax}")

# ── Solve with HiGHS ──────────────────────────────────────────────────────────
solver = pulp.HiGHS_CMD(msg=False)
prob.solve(solver)

print(f"Status : {pulp.LpStatus[prob.status]}")
print(f"Objective (Effectiveness): {pulp.value(prob.objective):.1f}\n")

print(f"{'Asset':<6} {'Axis':<8} {'Sorties':>8}")
print("-" * 26)
for a in assets:
    for ax in axes:
        v = x[(a, ax)].varValue
        if v and v > 0.01:
            print(f"{a:<6} {ax:<8} {v:>8.1f}")

# ── Shadow Prices (Dual Values) ───────────────────────────────────────────────
print("\n── Shadow Prices ──────────────────────────────────────────")
print(f"{'Constraint':<20} {'Shadow Price':>14} {'Slack':>10}")
print("-" * 46)
for name, constraint in prob.constraints.items():
    shadow = constraint.pi      # dual variable y_i
    slack  = constraint.slack   # residual (b_i - A_i x*)
    print(f"{name:<20} {shadow if shadow is not None else 'N/A':>14} {slack if slack is not None else 'N/A':>10}")

print("""
Interpretation:
  • Crew_Total shadow price ≈ marginal value of one extra crew-hour.
    If hiring one additional crew costs less than this value, it is worthwhile.
  • A shadow price of 0 means that constraint is not binding — relaxing it
    further yields no benefit at the current optimum.
""")

# ── Right-Hand Side Sensitivity Analysis ──────────────────────────────────────
print("── RHS Sensitivity: Crew Hours ────────────────────────────")
print(f"{'Crew Cap':>10} {'Objective':>12}")
for extra in range(0, 41, 10):
    prob2 = prob.copy()
    prob2.constraints["Crew_Total"].constant = -(crew_cap + extra)
    prob2.solve(pulp.HiGHS_CMD(msg=False))
    print(f"{crew_cap + extra:>10}  {pulp.value(prob2.objective):>12.1f}")
```

**Expected output (approximate):**
```
Status : Optimal
Objective (Effectiveness): 198.0

Asset  Axis     Sorties
--------------------------
AH     North       15.0
AH     South        0.0
TH     Center      20.0
UAV    South       40.0
...

── Shadow Prices ──────────────────────────────────────────
Constraint           Shadow Price      Slack
----------------------------------------------
Fuel_AH                      1.25       0.00
Crew_Total                   0.83       0.00
...
```

### Example B — Civilian: Blending / Diet Problem

!!! example "Scenario"
    A food manufacturer blends two raw ingredients, **Grain** and **Legume**, to produce a nutritional supplement. Each kilogram of Grain costs \$2 and contains 3 g protein and 6 g carbs. Each kilogram of Legume costs \$5 and contains 9 g protein and 2 g carbs. The blend must provide at least 12 g protein and 10 g carbs per serving. Minimise cost.

**Open-Source Tools:** SciPy, NumPy, Matplotlib, Python 3.10+

```python
# pip install scipy numpy matplotlib
import numpy as np
import matplotlib.pyplot as plt
from matplotlib.patches import Polygon
from matplotlib.collections import PatchCollection
from scipy.optimize import linprog

# ── LP Formulation ────────────────────────────────────────────────────────────
# Variables: x0 = kg Grain, x1 = kg Legume
# Minimize: 2*x0 + 5*x1
# Subject to: -3x0 - 9x1 <= -12  (protein >= 12, flipped for linprog <=)
#             -6x0 - 2x1 <= -10  (carbs   >= 10)
#             x0, x1 >= 0

c     = [2.0, 5.0]
A_ub  = [[-3, -9], [-6, -2]]
b_ub  = [-12, -10]
bounds = [(0, None), (0, None)]

result = linprog(c, A_ub=A_ub, b_ub=b_ub, bounds=bounds, method="highs")

x0_opt, x1_opt = result.x
cost_opt = result.fun
print(f"Optimal blend  : {x0_opt:.3f} kg Grain, {x1_opt:.3f} kg Legume")
print(f"Minimum cost   : ${cost_opt:.2f} per serving")
print(f"Protein content: {3*x0_opt + 9*x1_opt:.1f} g (min 12 g)")
print(f"Carbs content  : {6*x0_opt + 2*x1_opt:.1f} g (min 10 g)")

# ── Dual (Shadow) Prices ──────────────────────────────────────────────────────
# ineq_marginals gives marginals for inequality constraints (negated sense)
if hasattr(result, "ineqlin"):
    marginals = result.ineqlin.marginals
    print(f"\nShadow price (protein constraint): ${-marginals[0]:.3f}/g")
    print(f"Shadow price (carbs constraint)  : ${-marginals[1]:.3f}/g")

# ── Sensitivity: cost of Grain from $1 to $4 ─────────────────────────────────
print("\n── Cost Sensitivity: Grain price ──────────────────────────")
print(f"{'Grain $':>8} {'Legume $':>9} {'Min Cost':>10} {'x0 (Grain)':>12} {'x1 (Legume)':>13}")
for grain_price in np.arange(1.0, 4.5, 0.5):
    r = linprog([grain_price, 5.0], A_ub=A_ub, b_ub=b_ub, bounds=bounds, method="highs")
    print(f"{grain_price:>8.1f} {5.0:>9.1f} {r.fun:>10.2f} {r.x[0]:>12.3f} {r.x[1]:>13.3f}")

# ── Feasible Region Visualisation ────────────────────────────────────────────
fig, ax = plt.subplots(figsize=(7, 5))
x0 = np.linspace(0, 5, 400)

# Constraint boundary lines
ax.plot(x0, (12 - 3*x0) / 9, label="Protein: $3x_0+9x_1 \\geq 12$", color="steelblue", lw=2)
ax.plot(x0, (10 - 6*x0) / 2, label="Carbs:   $6x_0+2x_1 \\geq 10$",  color="darkorange", lw=2)

# Shade the feasible region (above both constraint lines and in first quadrant).
# Vertices are corners of the unbounded-above feasible polygon, clipped to the
# plot window.  We enumerate them counter-clockwise:
#   1. Intersection of the two active constraints (Cramer's rule):
#         3x0 + 9x1 = 12  →  x0_int = (12·2 - 10·9) / (3·2 - 9·6)
#         6x0 + 2x1 = 10  →  x1_int = (12 - 3·x0_int) / 9
x0_int = (12*2 - 10*9) / (3*2 - 9*6)   # = (24-90)/(6-54) = -66/-48 ≈ 1.375
x1_int = (12 - 3*x0_int) / 9
vertices_feas = np.array([
    [x0_int, x1_int],   # intersection of the two active constraints
    [0, 10/2],          # x0=0, on the carbs boundary (6·0+2x1=10 → x1=5)
    [0, 6.0],           # upper-left plot boundary corner (x0=0, x1=plot max)
    [4.5, 6.0],         # upper-right plot boundary corner (plot max)
    [4.5, 0],           # lower-right plot boundary corner (x1=0, plot max)
    [12/3, 0],          # x1=0, on the protein boundary (3x0+9·0=12 → x0=4)
])
poly = Polygon(vertices_feas, closed=True, facecolor="#d4edda", edgecolor="none", alpha=0.5, label="Feasible region")
ax.add_patch(poly)

# Iso-cost lines
for cost in [4, 7, cost_opt]:
    x1_iso = (cost - 2*x0) / 5
    lbl = f"Cost=${cost:.2f} (optimal)" if abs(cost - cost_opt) < 0.01 else f"Cost=${cost:.1f}"
    ls  = "-" if abs(cost - cost_opt) < 0.01 else "--"
    ax.plot(x0, x1_iso, ls, color="green", alpha=0.7, label=lbl)

# Optimal point
ax.plot(x0_opt, x1_opt, "r*", ms=14, zorder=5, label=f"Optimum ({x0_opt:.2f}, {x1_opt:.2f})")

ax.set_xlim(-0.1, 4.5); ax.set_ylim(-0.1, 6)
ax.set_xlabel("Grain (kg)"); ax.set_ylabel("Legume (kg)")
ax.set_title("Diet Problem — Feasible Region and Optimal Blend")
ax.legend(fontsize=8, loc="upper right"); ax.grid(True, alpha=0.3)
plt.tight_layout()
plt.savefig("diet_feasible_region.png", dpi=150)
plt.show()
```

**Expected output:**
```
Optimal blend  : 1.375 kg Grain, 0.875 kg Legume
Minimum cost   : $7.13 per serving
Protein content: 12.0 g (min 12 g)
Carbs content  : 10.0 g (min 10 g)

── Cost Sensitivity: Grain price ──────────────────────────
Grain $  Legume $   Min Cost   x0 (Grain)   x1 (Legume)
    1.0       5.0       4.75          1.375         0.875
    1.5       5.0       6.13          1.375         0.875
    2.0       5.0       7.50          1.375         0.875
    ...
```

!!! note "Reading the sensitivity table"
    When the Grain price rises above a threshold, the optimal blend switches to more Legume. The **shadow price** on each nutritional constraint tells you how much more you'd pay (per gram of nutrient) if you tightened the requirement.

---

### Check Your Understanding — Part 2

1. In Example A, a shadow price of 0.83 on `Crew_Total` means what operationally? Under what condition would you recommend procuring additional crew hours?
2. In Example B, both constraints are active (binding) at the optimum. What does this say about the slack variables $s_1$ and $s_2$, and how does complementary slackness relate to the dual variables?
3. If you ran the sensitivity table in Example B and found that the optimal solution changes when Grain exceeds \$3.50/kg, what is that breakpoint called in LP sensitivity analysis?

---

## Part 3 — Interactive Exercises

### Exercise 1 — Guided (Warm-Up): Transportation Problem

!!! question "Task"
    Complete the TODO blocks to solve a classic transportation LP: ship goods from 3 supply nodes to 4 demand nodes at minimum cost.

    **Supply:** Depot A = 120 units, Depot B = 80 units, Depot C = 100 units  
    **Demand:** Site 1 = 70, Site 2 = 90, Site 3 = 80, Site 4 = 60 units  
    **Expected minimum cost:** \$2,970

```python
# pip install pulp
import pulp

# Supply and demand data
supply = {"A": 120, "B": 80, "C": 100}
demand = {"1": 70,  "2": 90, "3": 80, "4": 60}

# Unit shipping costs [depot][site]
cost = {
    ("A","1"): 2, ("A","2"): 3, ("A","3"): 1, ("A","4"): 5,
    ("B","1"): 4, ("B","2"): 2, ("B","3"): 3, ("B","4"): 2,
    ("C","1"): 3, ("C","2"): 5, ("C","3"): 4, ("C","4"): 1,
}

prob = pulp.LpProblem("Transportation", pulp.LpMinimize)

# TODO: Define decision variables x[(d, s)] >= 0 for each depot d, site s.
#   Hint: keys should be a list of (depot, site) tuples, e.g.
#         [(d, s) for d in supply for s in demand]
#   x = pulp.LpVariable.dicts("ship", [(d, s) for d in supply for s in demand],
#                              lowBound=0)

# TODO: Set objective — minimise total shipping cost.
#   Hint: prob += pulp.lpSum(cost[(d, s)] * x[(d, s)] for d in supply for s in demand)

# TODO: Add supply constraints — each depot ships at most its supply.
#   Hint: for d in supply:
#             prob += pulp.lpSum(x[(d, s)] for s in demand) <= supply[d], f"Supply_{d}"

# TODO: Add demand constraints — each site receives exactly its demand.
#   Hint: for s in demand:
#             prob += pulp.lpSum(x[(d, s)] for d in supply) == demand[s], f"Demand_{s}"

prob.solve(pulp.PULP_CBC_CMD(msg=0))
print(f"Status: {pulp.LpStatus[prob.status]}")
print(f"Min Cost: ${pulp.value(prob.objective):.0f}  (expected $2,970)\n")

# Print flow table
print(f"{'From\\To':>8}", end="")
for s in demand: print(f"{s:>8}", end="")
print()
for d in supply:
    print(f"{d:>8}", end="")
    for s in demand:
        print(f"{x[(d,s)].varValue:>8.0f}", end="")
    print()
```

**Hint — shadow prices:**  After solving, inspect `prob.constraints[name].pi` for each supply/demand constraint. Negative duals on demand constraints tell you the marginal saving from reducing that site's requirement by one unit.

---

### Exercise 2 — Exploratory: Logistics Network What-If

!!! question "Task"
    A regional distributor operates **3 warehouses** (W1, W2, W3) supplying **5 customers** (C1–C5). Solve the base-case distribution problem, then answer the what-if questions below.

**Data:**

| | C1 | C2 | C3 | C4 | C5 | **Capacity** |
|--|--|--|--|--|--|--|
| **W1** | 3 | 1 | 7 | 4 | 2 | 200 |
| **W2** | 5 | 3 | 2 | 6 | 4 | 150 |
| **W3** | 4 | 6 | 3 | 1 | 5 | 180 |
| **Demand** | 80 | 70 | 90 | 60 | 100 | — |

```python
# pip install pulp
import pulp

warehouses = ["W1", "W2", "W3"]
customers  = ["C1", "C2", "C3", "C4", "C5"]

capacity = {"W1": 200, "W2": 150, "W3": 180}
demand   = {"C1": 80, "C2": 70, "C3": 90, "C4": 60, "C5": 100}

unit_cost = {
    ("W1","C1"): 3, ("W1","C2"): 1, ("W1","C3"): 7, ("W1","C4"): 4, ("W1","C5"): 2,
    ("W2","C1"): 5, ("W2","C2"): 3, ("W2","C3"): 2, ("W2","C4"): 6, ("W2","C5"): 4,
    ("W3","C1"): 4, ("W3","C2"): 6, ("W3","C3"): 3, ("W3","C4"): 1, ("W3","C5"): 5,
}

def solve_network(capacity_override=None):
    cap = capacity_override or capacity
    prob = pulp.LpProblem("Logistics_Network", pulp.LpMinimize)
    x = pulp.LpVariable.dicts("ship",
                               [(w, c) for w in warehouses for c in customers],
                               lowBound=0)
    prob += pulp.lpSum(unit_cost[(w,c)] * x[(w,c)] for w in warehouses for c in customers)
    for w in warehouses:
        prob += pulp.lpSum(x[(w,c)] for c in customers) <= cap[w], f"Cap_{w}"
    for c in customers:
        prob += pulp.lpSum(x[(w,c)] for w in warehouses) >= demand[c], f"Dem_{c}"
    prob.solve(pulp.PULP_CBC_CMD(msg=0))
    return prob, x

# --- Base case ---
base_prob, base_x = solve_network()
print(f"Base-case cost: ${pulp.value(base_prob.objective):.0f}")

# --- What-if questions ---
# Q1: Which warehouse–customer route has the highest utilisation?
print("\nTop routes by flow:")
flows = sorted(
    [(w, c, base_x[(w,c)].varValue) for w in warehouses for c in customers],
    key=lambda t: -t[2]
)
for w, c, v in flows[:5]:
    print(f"  {w} → {c}: {v:.0f} units")

# Q2: What if W2 is closed (capacity set to 0)?
closed_cap = {**capacity, "W2": 0}
closed_prob, _ = solve_network(capacity_override=closed_cap)
delta = pulp.value(closed_prob.objective) - pulp.value(base_prob.objective)
print(f"\nCost with W2 closed: ${pulp.value(closed_prob.objective):.0f}")
print(f"Cost increase:       ${delta:.0f} ({delta/pulp.value(base_prob.objective)*100:.1f}%)")

# Q3: Read shadow prices on warehouse capacity constraints
print("\nShadow prices on warehouse capacity ($/unit of extra capacity):")
for name, con in base_prob.constraints.items():
    if name.startswith("Cap_"):
        print(f"  {name}: {con.pi:.3f}")
```

**Discussion questions:**
- Which warehouse is the binding bottleneck? How do the shadow prices tell you this?
- If W2's cost per unit shipped could be reduced by \$1 across all routes, would you? Use shadow prices to answer.
- After closing W2, does any demand constraint become infeasible? Why or why not?

---

### Exercise 3 — Challenge: Multi-Period Production Planning

!!! question "Task"
    A manufacturer plans production over **6 periods** (months). Each period has known demand, a production capacity, and holding costs for inventory carried forward. Formulate and solve the LP, plot results, and perform a parametric analysis on the holding cost rate.

    **Bonus:** Reformulate in Pyomo and solve with the GLPK solver.

```python
# pip install pulp numpy matplotlib
import pulp
import numpy as np
import matplotlib.pyplot as plt

# ── Problem data ──────────────────────────────────────────────────────────────
T = 6                                            # number of periods
demand   = [120, 180, 140, 200, 160, 130]        # units demanded each period
prod_cap = [200, 200, 200, 200, 200, 200]        # max production per period
inv_cap  = 250                                   # max ending inventory
prod_cost = 50                                   # $/unit produced
holding_rate = 0.05                              # % of prod_cost per unit per period
holding_cost = holding_rate * prod_cost          # $/unit/period carried

# ── Model ─────────────────────────────────────────────────────────────────────
def solve_production_plan(h_cost):
    prob = pulp.LpProblem("MultiPeriod_Production", pulp.LpMinimize)

    # Decision variables
    p = [pulp.LpVariable(f"prod_{t}", lowBound=0, upBound=prod_cap[t]) for t in range(T)]
    inv = [pulp.LpVariable(f"inv_{t}", lowBound=0, upBound=inv_cap) for t in range(T)]

    # Objective: production cost + holding cost
    prob += (pulp.lpSum(prod_cost * p[t] for t in range(T)) +
             pulp.lpSum(h_cost * inv[t] for t in range(T)))

    # Inventory balance: inv[t] = inv[t-1] + prod[t] - demand[t]
    # Period 0: start with 0 inventory
    prob += inv[0] == p[0] - demand[0], "Balance_0"
    for t in range(1, T):
        prob += inv[t] == inv[t-1] + p[t] - demand[t], f"Balance_{t}"

    # Non-negative inventory (no backlogging)
    # Already handled by lowBound=0

    prob.solve(pulp.PULP_CBC_CMD(msg=0))

    prod_vals = [p[t].varValue for t in range(T)]
    inv_vals  = [inv[t].varValue for t in range(T)]
    obj_val   = pulp.value(prob.objective)
    duals     = [prob.constraints[f"Balance_{t}"].pi for t in range(T)]
    return prod_vals, inv_vals, obj_val, duals

prod_vals, inv_vals, obj_val, duals = solve_production_plan(holding_cost)
print(f"Optimal total cost: ${obj_val:,.0f}")
print(f"\n{'Period':>8} {'Demand':>8} {'Produce':>9} {'Inventory':>11} {'Dual':>8}")
print("-" * 50)
for t in range(T):
    print(f"{t+1:>8} {demand[t]:>8} {prod_vals[t]:>9.1f} {inv_vals[t]:>11.1f} {duals[t]:>8.2f}")

# ── Plot production and inventory ─────────────────────────────────────────────
periods = list(range(1, T+1))
fig, axes = plt.subplots(2, 1, figsize=(9, 6), sharex=True)

axes[0].bar(periods, prod_vals, color="steelblue", alpha=0.8, label="Production")
axes[0].plot(periods, demand, "r--o", label="Demand", linewidth=1.5)
axes[0].axhline(prod_cap[0], color="gray", linestyle=":", label=f"Capacity={prod_cap[0]}")
axes[0].set_ylabel("Units"); axes[0].set_title("Multi-Period Production Plan")
axes[0].legend(); axes[0].grid(True, alpha=0.3)

axes[1].bar(periods, inv_vals, color="darkorange", alpha=0.8, label="Ending Inventory")
axes[1].set_xlabel("Period"); axes[1].set_ylabel("Units")
axes[1].set_title("Inventory Levels"); axes[1].legend(); axes[1].grid(True, alpha=0.3)

plt.tight_layout()
plt.savefig("production_plan.png", dpi=150)
plt.show()

# ── Parametric analysis: holding cost rate 1% → 15% ──────────────────────────
print("\n── Parametric Analysis: Holding Cost Rate ──────────────")
print(f"{'Rate':>6} {'Total Cost':>12} {'Avg Inventory':>15}")
for rate in np.arange(0.01, 0.16, 0.02):
    h = rate * prod_cost
    _, inv_v, obj_v, _ = solve_production_plan(h)
    avg_inv = np.mean([v for v in inv_v if v is not None])
    print(f"{rate:>6.0%} {obj_v:>12,.0f} {avg_inv:>15.1f}")

print("""
Dual interpretation:
  The dual variable on Balance_t (the inventory balance constraint for period t)
  represents the marginal value of carrying one extra unit into period t.
  A large negative dual means increasing supply in that period is very valuable.
""")

# ── Bonus: Pyomo + GLPK ───────────────────────────────────────────────────────
# Uncomment if pyomo and glpk are installed: pip install pyomo; apt install glpk-utils
#
# from pyomo.environ import (ConcreteModel, Var, NonNegativeReals, Objective,
#                             Constraint, SolverFactory, minimize, value, summation)
#
# m = ConcreteModel()
# m.T   = range(T)
# m.p   = Var(m.T, domain=NonNegativeReals, bounds=lambda m,t: (0, prod_cap[t]))
# m.inv = Var(m.T, domain=NonNegativeReals, bounds=(0, inv_cap))
#
# m.obj = Objective(
#     expr=sum(prod_cost * m.p[t] + holding_cost * m.inv[t] for t in m.T),
#     sense=minimize
# )
# m.bal = Constraint(m.T, rule=lambda m, t:
#     m.inv[t] == (m.inv[t-1] if t > 0 else 0) + m.p[t] - demand[t]
# )
#
# solver = SolverFactory("glpk")
# result = solver.solve(m)
# print(f"Pyomo/GLPK cost: ${value(m.obj):,.0f}")
```

---

### Check Your Understanding — Part 3

1. In Exercise 1, why does a balanced transportation problem (total supply = total demand) always have a feasible solution, and what is special about the structure of its constraint matrix?
2. In Exercise 2, if the shadow price on `Cap_W1` is −2.5, what does that mean operationally, and what action would you recommend?
3. In Exercise 3, the dual variable on the inventory balance constraint for period 4 is large and negative. What does this tell you about the value of increasing production capacity in period 3 or 4?

---

## Relationship to Other OR Techniques

- Extends naturally to **Integer Programming** (adding integrality constraints)
- Subproblems in **Network Analysis** (min-cost flow, shortest path) are special LPs
- Foundation for **Stochastic Programming** (two-stage LP under uncertainty)
- LP relaxation used as a bound in **Branch-and-Bound** for MIP solvers

---

!!! tip "Prompt Template"
    Use the [Agent Prompt](prompt.md) for this topic, or the [Generic Prompt Template](../prompt-template.md) to generate additional lectures, examples, and exercises.
