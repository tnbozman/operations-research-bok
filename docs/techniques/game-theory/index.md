# Game Theory

> Modeling strategic interactions between rational agents.

---

## Lecture

### What is Game Theory?

Game Theory studies strategic interactions where the outcome for each participant depends on the actions of all. Unlike Decision Analysis (where uncertainty is from "nature"), Game Theory models **adversarial or cooperative** settings with rational agents who anticipate each other's behavior.

### Types of Games

| Type | Description |
|------|-------------|
| **Zero-Sum** | One player's gain equals the other's loss |
| **Non-Zero-Sum** | Mutual gains or losses are possible |
| **Cooperative** | Players can form binding agreements |
| **Non-Cooperative** | Players act independently |
| **Simultaneous** | Players choose actions at the same time |
| **Sequential** | Players move in turns (extensive form) |
| **Repeated** | The same game is played multiple times |

### Key Concepts

- **Nash Equilibrium** — no player benefits by unilaterally changing strategy
- **Dominant strategy** — best response regardless of opponent's action
- **Mixed strategy** — randomizing over actions with specific probabilities
- **Minimax theorem** — optimal strategy in two-player zero-sum games
- **Pareto efficiency** — no player can be made better off without making another worse off
- **Payoff matrix** — tabular representation of outcomes

### Mathematical Formulation — Two-Player Zero-Sum

Player 1 (row) has strategies $i = 1, \ldots, m$ and Player 2 (column) has strategies $j = 1, \ldots, n$. Payoff matrix $A$ gives Player 1's payoff $a_{ij}$.

$$
\max_{\mathbf{p}} \min_{\mathbf{q}} \mathbf{p}^\top A \mathbf{q}
$$

Where $\mathbf{p}$ and $\mathbf{q}$ are mixed strategy probability vectors.

### Historical Context

Formalized by John von Neumann and Oskar Morgenstern (1944). John Nash's equilibrium concept (1950) earned him the Nobel Prize. Game theory has been central to Cold War strategy, nuclear deterrence theory, and modern cybersecurity.

### Relationship to Other OR Techniques

- Extends **Decision Analysis** to adversarial settings
- Zero-sum games are solvable via **Linear Programming**
- Repeated games connect to **Stochastic Processes** (Markov strategies)
- Often analyzed via **Simulation** (evolutionary game dynamics)

---

## Real-World Examples

### Example A — Military: Attacker-Defender Resource Allocation

!!! example "Scenario"
    Two adversaries allocate forces across three sectors. Each side wants to dominate as many sectors as possible. Model as a zero-sum game and find the Nash equilibrium mixed strategy.

**Open-Source Tools:** SciPy (linprog), Python

```python
# pip install scipy numpy
import numpy as np
from scipy.optimize import linprog

# Payoff matrix (Attacker's perspective)
# Rows = Attacker strategies, Cols = Defender strategies
# Values = net sectors won by attacker
A = np.array([
    [ 2, -1,  0],
    [-1,  3, -2],
    [ 1,  0,  1],
])

m, n = A.shape

# Solve for Attacker's optimal mixed strategy using LP
# Maximize v subject to: A^T p >= v*1, sum(p) = 1, p >= 0
# Reformulate: minimize -v
c = np.zeros(m + 1)
c[-1] = -1  # -v to minimize (maximize v)

A_ub = np.hstack([-A.T, np.ones((n, 1))])
b_ub = np.zeros(n)

A_eq = np.array([np.append(np.ones(m), 0)])
b_eq = np.array([1])

bounds = [(0, None)] * m + [(None, None)]

result = linprog(c, A_ub=A_ub, b_ub=b_ub, A_eq=A_eq, b_eq=b_eq, bounds=bounds)
p_star = result.x[:m]
v_star = result.x[m]

print("Attacker-Defender Game Analysis")
print("=" * 40)
print(f"Game Value: {v_star:.3f}")
print(f"Attacker Mixed Strategy:")
for i in range(m):
    print(f"  Strategy {i+1}: {p_star[i]:.3f}")
```

### Example B — Civilian: Pricing Competition (Prisoner's Dilemma)

!!! example "Scenario"
    Two competing firms must independently decide whether to price High or Low. Model the payoff structure and identify the Nash equilibrium.

**Open-Source Tools:** Python, Nashville (or manual)

```python
import numpy as np

# Payoff matrices: (Firm A payoff, Firm B payoff)
strategies = ["High Price", "Low Price"]
payoff_A = np.array([
    [50, 20],   # A: High, B: High | A: High, B: Low
    [60, 30],   # A: Low,  B: High | A: Low,  B: Low
])
payoff_B = np.array([
    [50, 60],
    [20, 30],
])

print("Pricing Game — Payoff Matrix")
print("=" * 45)
print(f"{'':>15} {'B: High':>10} {'B: Low':>10}")
for i, s in enumerate(strategies):
    row = f"  A: {s:<10}"
    for j in range(2):
        row += f" ({payoff_A[i,j]:>2},{payoff_B[i,j]:>2})  "
    print(row)

# Find Nash Equilibria (pure strategy)
print("\nNash Equilibrium Search (Pure Strategy):")
for i in range(2):
    for j in range(2):
        a_best = payoff_A[i, j] == payoff_A[:, j].max()
        b_best = payoff_B[i, j] == payoff_B[i, :].max()
        if a_best and b_best:
            print(f"  NE: A={strategies[i]}, B={strategies[j]}")
            print(f"  Payoffs: A={payoff_A[i,j]}, B={payoff_B[i,j]}")
```

---

## Interactive Exercises

### Exercise 1 — Guided (Warm-Up)

!!! question "Task"
    Implement a function to find all pure-strategy Nash equilibria of any 2-player bimatrix game. Test it on the classic games: Prisoner's Dilemma, Battle of the Sexes, and Matching Pennies.

```python
import numpy as np

def find_pure_ne(payoff_A, payoff_B):
    """Find all pure strategy Nash equilibria."""
    equilibria = []
    m, n = payoff_A.shape
    for i in range(m):
        for j in range(n):
            # TODO: Check if (i,j) is a best response for both players
            # a_best = ...
            # b_best = ...
            # if a_best and b_best:
            #     equilibria.append((i, j))
            pass
    return equilibria

# TODO: Test on Prisoner's Dilemma
# TODO: Test on Battle of the Sexes
# TODO: Test on Matching Pennies (should have NO pure NE)
```

### Exercise 2 — Exploratory (Intermediate)

!!! question "Task"
    Simulate a repeated Prisoner's Dilemma tournament with multiple strategies (Tit-for-Tat, Always Cooperate, Always Defect, Random, Pavlov). Run a round-robin tournament and rank strategies by total score. What strategy performs best?

### Exercise 3 — Challenge (Advanced)

!!! question "Task"
    Model a Colonel Blotto game (military allocation game) where two commanders allocate 100 units across 5 battlefields. Implement a simulation to explore the strategy space, find approximate Nash equilibria using evolutionary methods (DEAP), and visualize winning strategy distributions.

---

## Check Your Understanding

1. Why is the Prisoner's Dilemma paradoxical, and how does repetition change outcomes?
2. How does a mixed-strategy Nash equilibrium differ from a pure-strategy one?
3. How can a zero-sum game be solved using Linear Programming?

---

!!! tip "Prompt Template"
    Use the [Agent Prompt](prompt.md) for this topic, or the [Generic Prompt Template](../prompt-template.md) to generate additional lectures, examples, and exercises.
