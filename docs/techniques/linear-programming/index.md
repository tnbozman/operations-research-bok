# Linear Programming

> Optimizing a linear objective function subject to linear constraints.

---

## Lecture

### What is Linear Programming?

Linear Programming (LP) is the foundational optimization technique in operations research. It seeks to find the best outcome (maximum profit, minimum cost, optimal allocation) given a linear objective function and a set of linear inequality or equality constraints.

### Mathematical Formulation

$$
\begin{align}
\text{Minimize } & \mathbf{c}^\top \mathbf{x} \\
\text{subject to } & \mathbf{A}\mathbf{x} \leq \mathbf{b} \\
& \mathbf{x} \geq 0
\end{align}
$$

Where:

- $\mathbf{x}$ — decision variable vector
- $\mathbf{c}$ — cost/objective coefficient vector
- $\mathbf{A}$ — constraint coefficient matrix
- $\mathbf{b}$ — right-hand side constraint vector

### Key Concepts

- **Feasible region** — the convex polytope defined by the constraints
- **Optimal solution** — found at a vertex (extreme point) of the feasible region
- **Simplex method** — pivots along vertices to find the optimum
- **Interior point methods** — alternative polynomial-time algorithms
- **Duality** — every LP has a dual problem providing bounds and sensitivity insights

### Historical Context

LP was developed during World War II by George Dantzig and others to solve military logistics and planning problems. The Simplex algorithm (1947) remains one of the most important algorithms in applied mathematics.

### Relationship to Other OR Techniques

- Extends naturally to **Integer Programming** (adding integrality constraints)
- Subproblems in **Network Analysis** are often LPs
- Foundation for **Stochastic Processes** models via stochastic programming

---

## Real-World Examples

### Example A — Military: Force Allocation

!!! example "Scenario"
    A theater commander must allocate limited units across multiple objectives to maximize overall mission effectiveness, subject to personnel, equipment, and time constraints.

**Open-Source Tools:** PuLP, Python

```python
# pip install pulp
import pulp

# Define the problem
prob = pulp.LpProblem("Force_Allocation", pulp.LpMaximize)

# Decision variables: units allocated to each objective
objectives = ["OBJ_Alpha", "OBJ_Bravo", "OBJ_Charlie"]
x = pulp.LpVariable.dicts("units", objectives, lowBound=0, cat="Continuous")

# Objective: maximize total mission effectiveness score
effectiveness = {"OBJ_Alpha": 8, "OBJ_Bravo": 6, "OBJ_Charlie": 10}
prob += pulp.lpSum([effectiveness[o] * x[o] for o in objectives])

# Constraints
prob += pulp.lpSum([x[o] for o in objectives]) <= 100, "Total_Personnel"
prob += x["OBJ_Alpha"] >= 20, "Min_Alpha"
prob += x["OBJ_Charlie"] <= 40, "Max_Charlie"

# Solve
prob.solve(pulp.PULP_CBC_CMD(msg=0))
print(f"Status: {pulp.LpStatus[prob.status]}")
for o in objectives:
    print(f"  {o}: {x[o].varValue} units")
print(f"Total Effectiveness: {pulp.value(prob.objective)}")
```

### Example B — Civilian: Production Planning

!!! example "Scenario"
    A factory produces two products with different profit margins, sharing limited machine hours and raw materials. Determine the optimal production mix.

**Open-Source Tools:** SciPy, Python

```python
# pip install scipy numpy
from scipy.optimize import linprog

# Objective: maximize profit (linprog minimizes, so negate)
c = [-20, -30]  # profit per unit of Product A, Product B

# Constraints: A_ub @ x <= b_ub
A_ub = [
    [1, 2],   # machine hours
    [4, 2],   # raw material
]
b_ub = [100, 200]  # available machine hours, raw material

# Bounds
x_bounds = [(0, None), (0, None)]

result = linprog(c, A_ub=A_ub, b_ub=b_ub, bounds=x_bounds, method="highs")
print(f"Product A: {result.x[0]:.1f} units")
print(f"Product B: {result.x[1]:.1f} units")
print(f"Max Profit: ${-result.fun:.2f}")
```

---

## Interactive Exercises

### Exercise 1 — Guided (Warm-Up)

!!! question "Task"
    Complete the TODO blocks to solve a diet problem: minimize cost while meeting minimum nutritional requirements.

```python
import pulp

prob = pulp.LpProblem("Diet_Problem", pulp.LpMinimize)

foods = ["Rice", "Beans", "Chicken", "Vegetables"]
cost = {"Rice": 1.5, "Beans": 2.0, "Chicken": 5.0, "Vegetables": 3.0}
protein = {"Rice": 4, "Beans": 15, "Chicken": 30, "Vegetables": 5}
calories = {"Rice": 200, "Beans": 150, "Chicken": 250, "Vegetables": 50}

# TODO: Define decision variables (servings of each food, >= 0)
# x = ...

# TODO: Set objective — minimize total cost
# prob += ...

# TODO: Add constraint — at least 50g protein
# prob += ...

# TODO: Add constraint — at least 2000 calories
# prob += ...

prob.solve(pulp.PULP_CBC_CMD(msg=0))
for f in foods:
    print(f"  {f}: {x[f].varValue:.1f} servings")
print(f"Total Cost: ${pulp.value(prob.objective):.2f}")
```

### Exercise 2 — Exploratory (Intermediate)

!!! question "Task"
    A logistics depot must ship supplies from 3 warehouses to 4 forward operating bases. Formulate and solve the transportation problem. Then explore: What happens if one warehouse is destroyed? How does the optimal cost change?

### Exercise 3 — Challenge (Advanced)

!!! question "Task"
    Model a multi-period production planning problem where inventory can carry over between periods, demand varies, and there are setup costs. Formulate as an LP, solve, and visualize the production and inventory levels over time using matplotlib.

---

## Check Your Understanding

1. Why must the feasible region of an LP be convex?
2. What does the dual of an LP tell you about the original problem?
3. When would you choose an interior-point method over Simplex?

---

!!! tip "Prompt Template"
    Use the [Agent Prompt](prompt.md) for this topic, or the [Generic Prompt Template](../prompt-template.md) to generate additional lectures, examples, and exercises.
