# Heuristics & Metaheuristics

> Approximate methods for large or intractable optimization problems.

---

## Lecture

### What are Heuristics and Metaheuristics?

When exact optimization methods (LP, IP) become computationally intractable for large-scale problems, **heuristics** provide good (but not guaranteed optimal) solutions quickly. **Metaheuristics** are high-level frameworks that guide the search process, often inspired by natural phenomena.

### Taxonomy

| Category | Methods |
|----------|---------|
| **Constructive Heuristics** | Greedy algorithms, nearest-neighbor |
| **Local Search** | Hill climbing, 2-opt, 3-opt |
| **Population-Based** | Genetic algorithms, particle swarm, ant colony |
| **Trajectory-Based** | Simulated annealing, tabu search |
| **Hybrid** | Matheuristics (heuristic + exact methods) |

### Key Metaheuristics

- **Simulated Annealing (SA)** — accepts worse solutions with decreasing probability, escaping local optima
- **Genetic Algorithms (GA)** — evolve a population using selection, crossover, and mutation
- **Tabu Search** — local search with memory to avoid revisiting solutions
- **Particle Swarm Optimization (PSO)** — particles explore the search space guided by personal and global best positions
- **Ant Colony Optimization (ACO)** — artificial ants deposit pheromones to guide future search

### Key Concepts

- **Exploration vs. exploitation** — balancing search breadth vs. depth
- **Solution representation** — how to encode solutions (permutations, bit strings, real vectors)
- **Neighborhood structure** — defining "nearby" solutions for local search
- **Convergence** — when to stop searching
- **No Free Lunch theorem** — no single metaheuristic dominates all problems

### Historical Context

Heuristics have existed since antiquity (greedy methods). Modern metaheuristics emerged in the 1980s–90s: Simulated Annealing (Kirkpatrick, 1983), Genetic Algorithms (Holland, 1975; Goldberg, 1989), Tabu Search (Glover, 1986). They are now mainstream for combinatorial optimization.

### Relationship to Other OR Techniques

- Applied when **Integer Programming** is too slow
- Often benchmarked against **Linear Programming** relaxation bounds
- Can incorporate **Simulation** for fitness evaluation
- Complement **Network Analysis** for routing problems (TSP, VRP)

---

## Real-World Examples

### Example A — Military: Patrol Route Optimization (TSP)

!!! example "Scenario"
    Plan a patrol route visiting 20 waypoints and returning to base, minimizing total distance. The exact solution is computationally expensive, so use a genetic algorithm.

**Open-Source Tools:** DEAP, Python

```python
# pip install deap numpy matplotlib
import random
import numpy as np
from deap import base, creator, tools, algorithms

random.seed(42)
np.random.seed(42)

# Generate 20 random waypoints
N = 20
waypoints = np.random.rand(N, 2) * 100  # 100km x 100km area

def distance(route):
    """Total route distance (return to start)."""
    d = sum(np.linalg.norm(waypoints[route[i]] - waypoints[route[i+1]])
            for i in range(len(route) - 1))
    d += np.linalg.norm(waypoints[route[-1]] - waypoints[route[0]])
    return (d,)

# DEAP setup
creator.create("FitnessMin", base.Fitness, weights=(-1.0,))
creator.create("Individual", list, fitness=creator.FitnessMin)

toolbox = base.Toolbox()
toolbox.register("indices", random.sample, range(N), N)
toolbox.register("individual", tools.initIterate, creator.Individual, toolbox.indices)
toolbox.register("population", tools.initRepeat, list, toolbox.individual)
toolbox.register("evaluate", distance)
toolbox.register("mate", tools.cxOrdered)
toolbox.register("mutate", tools.mutShuffleIndexes, indpb=0.1)
toolbox.register("select", tools.selTournament, tournsize=3)

# Run GA
pop = toolbox.population(n=200)
result, log = algorithms.eaSimple(pop, toolbox, cxpb=0.7, mutpb=0.2,
                                   ngen=300, verbose=False)

best = tools.selBest(result, 1)[0]
print(f"Best route distance: {distance(best)[0]:.2f} km")
print(f"Route: {best}")
```

### Example B — Civilian: Job Shop Scheduling

!!! example "Scenario"
    Schedule jobs on machines to minimize total completion time (makespan). Use simulated annealing to search the permutation space.

**Open-Source Tools:** Python

```python
import random
import math

random.seed(42)

# 5 jobs, 3 machines — processing times
processing = [
    [3, 2, 4],  # Job 0 on machines 0,1,2
    [2, 3, 1],
    [4, 1, 3],
    [1, 4, 2],
    [3, 3, 3],
]
n_jobs = len(processing)
n_machines = len(processing[0])

def makespan(order):
    """Calculate makespan for a given job order (flow shop)."""
    times = [[0] * n_machines for _ in range(n_jobs)]
    for idx, j in enumerate(order):
        for m in range(n_machines):
            prev_job = times[order[idx - 1]][m] if idx > 0 else 0
            prev_machine = times[j][m - 1] if m > 0 else 0
            times[j][m] = max(prev_job, prev_machine) + processing[j][m]
    return times[order[-1]][n_machines - 1]

def simulated_annealing(n_iter=5000, T_start=100, T_end=0.1):
    current = list(range(n_jobs))
    random.shuffle(current)
    current_cost = makespan(current)
    best, best_cost = current[:], current_cost

    T = T_start
    alpha = (T_end / T_start) ** (1 / n_iter)

    for i in range(n_iter):
        # Swap two random jobs
        neighbor = current[:]
        a, b = random.sample(range(n_jobs), 2)
        neighbor[a], neighbor[b] = neighbor[b], neighbor[a]
        new_cost = makespan(neighbor)

        delta = new_cost - current_cost
        if delta < 0 or random.random() < math.exp(-delta / T):
            current, current_cost = neighbor, new_cost
            if current_cost < best_cost:
                best, best_cost = current[:], current_cost

        T *= alpha

    return best, best_cost

best_order, best_ms = simulated_annealing()
print(f"Best job order: {best_order}")
print(f"Makespan: {best_ms}")
```

---

## Interactive Exercises

### Exercise 1 — Guided (Warm-Up)

!!! question "Task"
    Implement a simple hill-climbing algorithm for the TSP. Start with a random tour and repeatedly apply 2-opt swaps (reverse a segment) until no improvement is found. Fill in the TODOs.

```python
import numpy as np

np.random.seed(42)
N = 15
cities = np.random.rand(N, 2) * 100

def tour_distance(tour):
    d = sum(np.linalg.norm(cities[tour[i]] - cities[tour[(i+1) % N]])
            for i in range(N))
    return d

def two_opt(tour):
    """Try all 2-opt swaps, return improved tour or None."""
    best = tour_distance(tour)
    for i in range(1, N - 1):
        for j in range(i + 1, N):
            # TODO: Create new tour by reversing segment [i:j+1]
            # new_tour = ...
            # TODO: Calculate new distance
            # new_dist = ...
            # TODO: If improved, return new tour
            pass
    return None  # No improvement found

# TODO: Run hill climbing until no improvement
# tour = list(range(N))
# while True:
#     improved = two_opt(tour)
#     if improved is None:
#         break
#     tour = improved
# print(f"Final distance: {tour_distance(tour):.2f}")
```

### Exercise 2 — Exploratory (Intermediate)

!!! question "Task"
    Compare three metaheuristics (hill climbing, simulated annealing, genetic algorithm) on the same 30-city TSP instance. Run each 10 times and plot the distribution of solution quality. Which is most consistent?

### Exercise 3 — Challenge (Advanced)

!!! question "Task"
    Solve a military sensor placement problem: place $k$ sensors in a grid to maximize area coverage, subject to terrain obstacles and communication range constraints. Implement using Particle Swarm Optimization (PSO) and visualize the optimized placement with coverage heatmaps.

---

## Check Your Understanding

1. What is the difference between a heuristic and a metaheuristic?
2. How does the "temperature" schedule in simulated annealing affect solution quality?
3. Why does the No Free Lunch theorem matter in practice?

---

!!! tip "Prompt Template"
    Use the [Agent Prompt](prompt.md) for this topic, or the [Generic Prompt Template](../prompt-template.md) to generate additional lectures, examples, and exercises.
