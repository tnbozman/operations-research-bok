# Integer Programming

> Optimization where some or all variables are constrained to integer values.

---

## Lecture

### What is Integer Programming?

Integer Programming (IP) extends Linear Programming by requiring some or all decision variables to take integer values. This is essential for modeling discrete decisions — yes/no choices, indivisible units, scheduling slots, and combinatorial structures.

### Types of Integer Programs

| Type | Description |
|------|-------------|
| **Pure IP** | All variables are integers |
| **Mixed-Integer Program (MIP)** | Some variables are integers, others continuous |
| **Binary IP (0-1 IP)** | Variables are restricted to 0 or 1 |

### Mathematical Formulation

$$
\begin{align}
\text{Minimize } & \mathbf{c}^\top \mathbf{x} \\
\text{subject to } & \mathbf{A}\mathbf{x} \leq \mathbf{b} \\
& x_i \in \mathbb{Z} \quad \forall i \in I \\
& \mathbf{x} \geq 0
\end{align}
$$

### Key Concepts

- **LP Relaxation** — solving the IP without integrality constraints to get a bound
- **Branch and Bound** — systematic enumeration with pruning
- **Cutting Planes** — adding valid inequalities to tighten the relaxation
- **Branch and Cut** — combining branch-and-bound with cutting planes
- **NP-Hardness** — IPs are generally NP-hard; solution time can grow exponentially

### Historical Context

IP gained prominence in the 1950s–60s with Gomory's cutting plane methods and Land & Doig's branch-and-bound. Modern solvers (CBC, SCIP, HiGHS) combine multiple strategies to solve large instances.

### Relationship to Other OR Techniques

- Direct extension of **Linear Programming**
- Core method for **Network Analysis** problems (shortest path, assignment)
- Often combined with **Heuristics & Metaheuristics** for large instances
- Models discrete aspects of **Decision Analysis** problems

---

## Real-World Examples

### Example A — Military: Weapon-Target Assignment

!!! example "Scenario"
    Assign available weapon systems to a set of enemy targets to maximize total expected damage, where each weapon can be assigned to at most one target.

**Open-Source Tools:** PuLP, Python

```python
# pip install pulp
import pulp

weapons = ["W1", "W2", "W3", "W4"]
targets = ["T1", "T2", "T3"]

# Expected damage if weapon w is assigned to target t
damage = {
    ("W1", "T1"): 80, ("W1", "T2"): 60, ("W1", "T3"): 40,
    ("W2", "T1"): 50, ("W2", "T2"): 90, ("W2", "T3"): 70,
    ("W3", "T1"): 60, ("W3", "T2"): 55, ("W3", "T3"): 85,
    ("W4", "T1"): 70, ("W4", "T2"): 65, ("W4", "T3"): 60,
}

prob = pulp.LpProblem("Weapon_Target_Assignment", pulp.LpMaximize)

# Binary decision: assign weapon w to target t?
x = pulp.LpVariable.dicts("assign", (weapons, targets), cat="Binary")

# Maximize total expected damage
prob += pulp.lpSum([damage[(w, t)] * x[w][t] for w in weapons for t in targets])

# Each weapon assigned to at most one target
for w in weapons:
    prob += pulp.lpSum([x[w][t] for t in targets]) <= 1

# Each target must receive at least one weapon
for t in targets:
    prob += pulp.lpSum([x[w][t] for w in weapons]) >= 1

prob.solve(pulp.PULP_CBC_CMD(msg=0))
print(f"Status: {pulp.LpStatus[prob.status]}")
for w in weapons:
    for t in targets:
        if x[w][t].varValue == 1:
            print(f"  {w} -> {t} (damage: {damage[(w, t)]})")
print(f"Total Expected Damage: {pulp.value(prob.objective)}")
```

### Example B — Civilian: Facility Location

!!! example "Scenario"
    A company must decide which candidate warehouse locations to open and how to assign customers to them, minimizing total fixed + transportation costs.

**Open-Source Tools:** OR-Tools, Python

```python
# pip install ortools
from ortools.linear_solver import pywraplp

solver = pywraplp.Solver.CreateSolver("CBC")

facilities = [0, 1, 2]
customers = [0, 1, 2, 3, 4]
fixed_cost = [100, 150, 120]
transport = [
    [10, 20, 30],  # customer 0 to each facility
    [25, 10, 15],
    [30, 15, 10],
    [20, 25, 20],
    [15, 30, 25],
]

# Binary: open facility j?
y = [solver.BoolVar(f"open_{j}") for j in facilities]
# Binary: assign customer i to facility j?
x = [[solver.BoolVar(f"assign_{i}_{j}") for j in facilities] for i in customers]

# Minimize fixed + transport costs
solver.Minimize(
    sum(fixed_cost[j] * y[j] for j in facilities)
    + sum(transport[i][j] * x[i][j] for i in customers for j in facilities)
)

# Each customer assigned to exactly one facility
for i in customers:
    solver.Add(sum(x[i][j] for j in facilities) == 1)

# Can only assign to open facilities
for i in customers:
    for j in facilities:
        solver.Add(x[i][j] <= y[j])

status = solver.Solve()
if status == pywraplp.Solver.OPTIMAL:
    for j in facilities:
        if y[j].solution_value() == 1:
            assigned = [i for i in customers if x[i][j].solution_value() == 1]
            print(f"Facility {j} OPEN — serves customers {assigned}")
    print(f"Total Cost: {solver.Objective().Value()}")
```

---

## Interactive Exercises

### Exercise 1 — Guided (Warm-Up)

!!! question "Task"
    Complete a knapsack problem: select items to maximize total value without exceeding weight capacity. Fill in the TODO blocks.

```python
import pulp

items = ["A", "B", "C", "D", "E"]
value =  {"A": 60, "B": 100, "C": 120, "D": 80, "E": 90}
weight = {"A": 10, "B": 20, "C": 30, "D": 15, "E": 25}
capacity = 50

prob = pulp.LpProblem("Knapsack", pulp.LpMaximize)

# TODO: Define binary decision variables
# x = ...

# TODO: Objective — maximize total value
# prob += ...

# TODO: Constraint — total weight <= capacity
# prob += ...

prob.solve(pulp.PULP_CBC_CMD(msg=0))
for i in items:
    if x[i].varValue == 1:
        print(f"  Select {i} (value={value[i]}, weight={weight[i]})")
print(f"Total Value: {pulp.value(prob.objective)}")
```

### Exercise 2 — Exploratory (Intermediate)

!!! question "Task"
    A military planner must schedule guard shifts across a week. Each guard has availability windows and a maximum number of shifts. Formulate as a binary IP, solve, and explore: What happens when you reduce the number of available guards by 20%?

### Exercise 3 — Challenge (Advanced)

!!! question "Task"
    Model a vehicle routing problem (VRP) with capacity constraints using OR-Tools. Visualize the routes on a 2D plot. Analyze the trade-off between number of vehicles and total distance traveled.

---

## Check Your Understanding

1. Why does adding integrality constraints make problems harder than LP?
2. What is the LP relaxation gap and why does it matter?
3. When is a greedy heuristic a good alternative to solving an IP exactly?

---

!!! tip "Prompt Template"
    Use the [Agent Prompt](prompt.md) for this topic, or the [Generic Prompt Template](../prompt-template.md) to generate additional lectures, examples, and exercises.
