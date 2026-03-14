# Heuristics & Metaheuristics

> Approximate methods for large or intractable optimization problems.

---

## Part 1 — Lecture

### 1.1 Why Exact Methods Fail: NP-Hardness and Exponential Scaling

Many real-world optimization problems are **NP-hard**: there is no known polynomial-time algorithm guaranteed to find the optimal solution. The Travelling Salesman Problem (TSP) with $n$ cities has $(n-1)!/2$ possible tours; for $n = 30$ this exceeds $4 \times 10^{30}$ — far beyond exhaustive enumeration.

| Problem size ($n$) | Exact tours | At $10^9$ evals/sec |
|--------------------|-------------|------------------|
| 10 | 181 440 | < 1 ms |
| 20 | $6 \times 10^{16}$ | ~2 years |
| 30 | $4 \times 10^{30}$ | > age of universe |

**Key insight:** for large $n$, a *good enough* solution found quickly is far more valuable than an optimal solution that arrives too late.

### 1.2 Heuristic vs. Metaheuristic

| Term | Definition |
|------|-----------|
| **Heuristic** | A problem-specific rule of thumb that constructs or improves a solution. Fast, but quality depends on domain knowledge. |
| **Metaheuristic** | A high-level, problem-independent framework that guides a heuristic search. Trades optimality guarantees for broad applicability. |

**Taxonomy of approximate methods:**

| Category | Examples |
|----------|---------|
| Constructive heuristics | Greedy nearest-neighbour, greedy knapsack |
| Local search | Hill climbing, 2-opt, 3-opt, Or-opt |
| Trajectory-based | Simulated Annealing, Tabu Search |
| Population-based | Genetic Algorithms, PSO, ACO |
| Hybrid (matheuristics) | RINS, LNS (heuristic + exact solver) |

### 1.3 Constructive Heuristics

**Nearest-Neighbour (NN) for TSP**

Start at a depot, repeatedly travel to the nearest unvisited city:

$$d_{\text{NN}} \leq \frac{1}{2}(\lceil \log_2 n \rceil + 1) \cdot d^*$$

where $d^*$ is the optimal tour length. The approximation ratio grows logarithmically — acceptable for moderate $n$, poor for large $n$.

**Greedy Knapsack**

Sort items by value-to-weight ratio $v_i / w_i$ (descending) and fill until capacity is reached. Achieves at least half the optimal value for the 0-1 knapsack, and is optimal for the fractional knapsack.

### 1.4 Local Search: Neighbourhood Structures and the Local Optimum Trap

A **neighbourhood** $\mathcal{N}(s)$ of solution $s$ is the set of solutions reachable in one move. Common TSP neighbourhoods:

- **2-opt** — reverse a segment of the tour (removes 2 edges, reinserts 2)
- **3-opt** — remove 3 edges and reconnect in the best of 8 ways
- **Or-opt** — relocate a chain of 1, 2, or 3 consecutive cities

**Hill climbing** iteratively moves to the best neighbour until no improvement exists. It converges to a **local optimum** which may be far from the global optimum — the *local optimum trap*.

$$s^* = \arg\min_{s' \in \mathcal{N}(s)} f(s')$$

Escape strategies: random restarts, perturbation, or accepting non-improving moves (SA, Tabu Search).

### 1.5 Simulated Annealing (SA)

Inspired by the physical annealing of metals. The **Metropolis criterion** allows accepting a worse solution $s'$ with probability:

$$P(\text{accept}) = \exp\!\left(-\frac{f(s') - f(s)}{T}\right)$$

where $T$ is the current *temperature*. As $T \to 0$, SA converges to pure hill climbing.

**Cooling schedules:**

| Schedule | Formula | Notes |
|----------|---------|-------|
| Geometric | $T_{k+1} = \alpha T_k,\ \alpha \in (0.9, 0.999)$ | Simple, widely used |
| Adaptive | Adjust $\alpha$ to keep acceptance rate in target range | Better tuning |
| Logarithmic | $T_k = c / \ln(k+2)$ | Theoretical convergence guarantee |

!!! tip "Tuning SA"
    Set $T_0$ so the initial acceptance rate $\approx 80\%$, and let the cooling schedule reduce it to near 0 by the iteration budget.

### 1.6 Tabu Search

Tabu Search is local search augmented with **memory** to avoid cycling.

- **Short-term memory (tabu list):** forbids moves that reverse recent changes for $t$ iterations (tabu tenure $t \approx 7$–20).
- **Aspiration criterion:** override the tabu status if the move leads to a solution *better than the best known*.
- **Long-term memory:** frequency-based diversification (penalise frequently visited attributes) and intensification (bias toward the best region found).

$$s_{k+1} = \arg\min_{s' \in \mathcal{N}(s_k) \setminus \text{Tabu}^*} f(s')$$

where $\text{Tabu}^*$ respects aspiration.

### 1.7 Genetic Algorithms (GA)

GAs maintain a **population** of candidate solutions and evolve them using operators inspired by natural selection.

| Component | Description |
|-----------|------------|
| **Representation** | Chromosome encodes a solution (binary, permutation, real) |
| **Fitness** | Objective value (or transformation thereof) |
| **Selection** | Tournament, roulette-wheel, rank |
| **Crossover** | Order (OX), PMX, uniform — combines two parents |
| **Mutation** | Swap, inversion, insertion — introduces new material |
| **Elitism** | Copy best individual(s) unchanged to next generation |

**Schema theorem (Holland):** Short, low-order schemata with above-average fitness receive exponentially increasing representation. Intuition: building blocks are discovered and combined.

GA update rule per generation:

$$P_{t+1} = \text{Select}(P_t) \xrightarrow{\text{crossover}} \xrightarrow{\text{mutate}} P_{t+1}$$

### 1.8 Particle Swarm Optimization (PSO)

A swarm of particles explore a continuous search space. Each particle $i$ has position $\mathbf{x}_i$ and velocity $\mathbf{v}_i$:

$$\mathbf{v}_i^{t+1} = w\mathbf{v}_i^t + c_1 r_1 (\mathbf{p}_i - \mathbf{x}_i^t) + c_2 r_2 (\mathbf{g} - \mathbf{x}_i^t)$$

$$\mathbf{x}_i^{t+1} = \mathbf{x}_i^t + \mathbf{v}_i^{t+1}$$

where $\mathbf{p}_i$ is the particle's personal best, $\mathbf{g}$ is the global best, $w$ is inertia weight, $c_1, c_2$ are cognitive/social coefficients ($\approx 2.0$), and $r_1, r_2 \sim U(0,1)$.

### 1.9 Ant Colony Optimization (ACO)

Artificial ants construct solutions probabilistically, guided by **pheromone** $\tau_{ij}$ (learned) and **heuristic** $\eta_{ij}$ (domain knowledge):

$$P(j \mid i) = \frac{\tau_{ij}^\alpha \cdot \eta_{ij}^\beta}{\sum_{k \in \mathcal{N}} \tau_{ik}^\alpha \cdot \eta_{ik}^\beta}$$

**Pheromone update (Ant System, AS):**

$$\tau_{ij} \leftarrow (1 - \rho)\,\tau_{ij} + \sum_{k=1}^{m} \Delta\tau_{ij}^k$$

**Ant Colony System (ACS)** adds a *local* pheromone update after each step (reduces pheromone to encourage exploration) and a pseudo-random-proportional rule favouring the best edge.

### 1.10 No Free Lunch Theorem

**Theorem (Wolpert & Macready, 1997):** Averaged over all possible problems, every search algorithm performs identically. No metaheuristic is universally superior.

**Practical implication:**

- Algorithm selection must be problem-specific.
- Incorporate domain knowledge into representation, neighbourhood, or fitness.
- Always benchmark multiple algorithms on *your* instances.

### 1.11 Statistical Benchmarking of Metaheuristics

Because metaheuristics are stochastic, single-run comparisons are misleading. Best practice:

1. Run each algorithm **30+ independent times** with different seeds.
2. Report: best, mean, median, standard deviation, worst.
3. Use the **Wilcoxon rank-sum test** (non-parametric) to test significance.
4. Plot **convergence curves** (best-found vs. iteration) and **box plots** of final quality.

!!! warning "Avoid cherry-picking"
    Report the full distribution of results, not only the best run. An algorithm with lower median but occasional great runs may not be reliable in deployment.

---

!!! question "Check Your Understanding — Part 1"
    1. A nearest-neighbour heuristic for TSP runs in $O(n^2)$. Why is the optimal TSP solution still computationally expensive to find even though NN is fast?
    2. Describe the role of the *tabu tenure* parameter in Tabu Search. What happens if it is set too large or too small?
    3. Explain the No Free Lunch theorem in your own words. Does it mean all metaheuristics are equally good for a specific military routing problem? Why or why not?

---

## Part 2 — Real-World Practical Examples

### Example A — Military: UAV Waypoint Tour Optimization (TSP)

!!! example "Scenario"
    A reconnaissance UAV must visit 30 waypoints and return to base, minimizing total flight distance (fuel/time). Three methods are compared: **Nearest-Neighbour + 2-opt**, **Simulated Annealing**, and a **Genetic Algorithm** (DEAP). The best method for embedded real-time replanning is identified.

**Open-Source Tools:** `deap`, `numpy`, `matplotlib`

```python
# pip install deap numpy matplotlib
import random
import math
import time
import numpy as np
import matplotlib.pyplot as plt
from deap import base, creator, tools, algorithms

random.seed(42)
np.random.seed(42)

N = 30  # waypoints
waypoints = np.random.rand(N, 2) * 100  # 100 km × 100 km grid

def tour_length(route):
    """Closed-tour distance."""
    return sum(
        np.linalg.norm(waypoints[route[i]] - waypoints[route[(i + 1) % N]])
        for i in range(N)
    )

# ── Nearest-Neighbour + 2-opt ──────────────────────────────────────────────
def nearest_neighbour():
    unvisited = list(range(N))
    tour = [0]
    unvisited.remove(0)
    while unvisited:
        last = tour[-1]
        nxt = min(unvisited, key=lambda c: np.linalg.norm(waypoints[last] - waypoints[c]))
        tour.append(nxt)
        unvisited.remove(nxt)
    return tour

def two_opt(tour):
    best = tour[:]
    improved = True
    while improved:
        improved = False
        for i in range(1, N - 1):
            for j in range(i + 1, N):
                new_tour = best[:i] + best[i:j + 1][::-1] + best[j + 1:]
                if tour_length(new_tour) < tour_length(best):
                    best = new_tour
                    improved = True
    return best

t0 = time.perf_counter()
nn_route = nearest_neighbour()
nn2_route = two_opt(nn_route)
t_nn2 = time.perf_counter() - t0
d_nn2 = tour_length(nn2_route)

# ── Simulated Annealing ────────────────────────────────────────────────────
def sa_tsp(n_iter=60_000, T_start=500.0, alpha=0.9997):
    tour = list(range(N))
    random.shuffle(tour)
    cost = tour_length(tour)
    best, best_cost = tour[:], cost
    T = T_start
    history = []
    for _ in range(n_iter):
        i, j = sorted(random.sample(range(N), 2))
        nb = tour[:i] + tour[i:j + 1][::-1] + tour[j + 1:]
        nc = tour_length(nb)
        # max(T, 1e-9) prevents ZeroDivisionError when temperature reaches zero
        if nc < cost or random.random() < math.exp(-(nc - cost) / max(T, 1e-9)):
            tour, cost = nb, nc
            if cost < best_cost:
                best, best_cost = tour[:], cost
        T *= alpha
        history.append(best_cost)
    return best, best_cost, history

t0 = time.perf_counter()
sa_route, d_sa, sa_history = sa_tsp()
t_sa = time.perf_counter() - t0

# ── Genetic Algorithm (DEAP) ───────────────────────────────────────────────
if not hasattr(creator, "FitnessMinTSP"):
    creator.create("FitnessMinTSP", base.Fitness, weights=(-1.0,))
if not hasattr(creator, "IndividualTSP"):
    creator.create("IndividualTSP", list, fitness=creator.FitnessMinTSP)

def eval_tsp(ind):
    return (tour_length(ind),)

tb = base.Toolbox()
tb.register("indices", random.sample, range(N), N)
tb.register("individual", tools.initIterate, creator.IndividualTSP, tb.indices)
tb.register("population", tools.initRepeat, list, tb.individual)
tb.register("evaluate", eval_tsp)
tb.register("mate", tools.cxOrdered)
tb.register("mutate", tools.mutShuffleIndexes, indpb=0.05)
tb.register("select", tools.selTournament, tournsize=3)

t0 = time.perf_counter()
pop = tb.population(n=300)
hof = tools.HallOfFame(1)
stats = tools.Statistics(lambda ind: ind.fitness.values)
stats.register("min", np.min)
pop, log = algorithms.eaSimple(
    pop, tb, cxpb=0.7, mutpb=0.2, ngen=500,
    stats=stats, halloffame=hof, verbose=False
)
t_ga = time.perf_counter() - t0
d_ga = tour_length(hof[0])
ga_history = log.select("min")

# ── Summary table ─────────────────────────────────────────────────────────
print(f"\n{'Method':<22} {'Distance (km)':>14} {'Time (s)':>10}")
print("-" * 48)
print(f"{'Nearest-Neighbour+2-opt':<22} {d_nn2:>14.2f} {t_nn2:>10.3f}")
print(f"{'Simulated Annealing':<22} {d_sa:>14.2f} {t_sa:>10.3f}")
print(f"{'Genetic Algorithm':<22} {d_ga:>14.2f} {t_ga:>10.3f}")

# ── Plot convergence and best route ───────────────────────────────────────
fig, axes = plt.subplots(1, 2, figsize=(13, 5))

# Convergence curves
axes[0].plot(sa_history, label="SA", color="tab:orange", linewidth=0.8)
axes[0].plot(ga_history, label="GA", color="tab:blue", linewidth=0.8)
axes[0].axhline(d_nn2, color="tab:green", linestyle="--", linewidth=1.2,
                label=f"NN+2-opt ({d_nn2:.0f} km)")
axes[0].set_xlabel("Iteration / Generation")
axes[0].set_ylabel("Tour Distance (km)")
axes[0].set_title("Convergence Curves — UAV Tour")
axes[0].legend()
axes[0].grid(True, alpha=0.3)

# Best tour (GA)
ga_pts = waypoints[list(hof[0]) + [hof[0][0]]]
axes[1].plot(ga_pts[:, 0], ga_pts[:, 1], "-o", color="tab:blue",
             markersize=4, linewidth=1, label="GA best")
axes[1].plot(*waypoints[0], "r*", markersize=14, label="Base")
for k, wp in enumerate(waypoints):
    axes[1].annotate(str(k), wp, fontsize=6, ha="center", va="bottom")
axes[1].set_title(f"GA Best Tour — {d_ga:.1f} km")
axes[1].set_xlabel("x (km)")
axes[1].set_ylabel("y (km)")
axes[1].legend()
axes[1].grid(True, alpha=0.3)

plt.tight_layout()
plt.savefig("uav_tour.png", dpi=100)
plt.show()
```

!!! note "Real-time replanning"
    **NN+2-opt** runs in milliseconds and is well-suited for embedded real-time replanning (low CPU, deterministic). **SA** and **GA** yield better solution quality given a few seconds of compute budget, suitable for pre-mission planning on a ground station.

### Example B — Civilian: Hospital Staff Scheduling

!!! example "Scenario"
    Schedule 20 nurses across a 7-day, 3-shift (morning/evening/night) rota. Each nurse has random availability constraints. The objective minimises: shift coverage deficits (hard penalty), unavailability violations, and excess shifts per nurse, while rewarding shift preferences. Solved with **Tabu Search** (from scratch) and **GA** (DEAP); results displayed as colour-coded heatmaps.

**Open-Source Tools:** `deap`, `numpy`, `matplotlib`

```python
# pip install deap numpy matplotlib
import random
import numpy as np
import matplotlib.pyplot as plt
from deap import base, creator, tools, algorithms

random.seed(0)
np.random.seed(0)

N_NURSES = 20
N_DAYS = 7
N_SHIFTS = 3        # 0 = morning, 1 = evening, 2 = night
MIN_COVER = 3       # nurses required per shift per day
MAX_SHIFTS = 5      # max shifts per nurse per week

# Availability: each (nurse, day, shift) slot is independently available with
# probability 0.75. This gives ~75 % availability per slot; individual nurses
# may have more or fewer available slots by chance.
avail = np.random.choice([0, 1], size=(N_NURSES, N_DAYS, N_SHIFTS), p=[0.25, 0.75])
# Preferences: 0–2 (higher = preferred shift)
prefs = np.random.randint(0, 3, size=(N_NURSES, N_DAYS, N_SHIFTS))

SIZE = N_NURSES * N_DAYS * N_SHIFTS

def decode(flat):
    return np.array(flat, dtype=int).reshape(N_NURSES, N_DAYS, N_SHIFTS)

def cost(flat):
    """Lower cost = better schedule."""
    S = decode(flat)
    c = 0
    # Coverage deficit (hard penalty)
    for d in range(N_DAYS):
        for s in range(N_SHIFTS):
            deficit = max(0, MIN_COVER - int(S[:, d, s].sum()))
            c += 100 * deficit
    # Unavailability penalty
    c += 50 * int(((S == 1) & (avail == 0)).sum())
    # Overtime penalty
    for n in range(N_NURSES):
        c += 20 * max(0, int(S[n].sum()) - MAX_SHIFTS)
    # Preference reward
    c -= int((S * prefs).sum())
    return c

def random_schedule():
    """Feasible initial solution: assign exactly MIN_COVER nurses per slot."""
    S = np.zeros((N_NURSES, N_DAYS, N_SHIFTS), dtype=int)
    for d in range(N_DAYS):
        for s in range(N_SHIFTS):
            chosen = np.random.choice(N_NURSES, MIN_COVER, replace=False)
            S[chosen, d, s] = 1
    return S.flatten().tolist()

# ── Tabu Search ────────────────────────────────────────────────────────────
def tabu_search(max_iter=400, tabu_tenure=20, sample_size=60):
    current = random_schedule()
    best = current[:]
    best_c = cost(best)
    tabu_list = []
    history = [best_c]

    for _ in range(max_iter):
        # Sample a subset of bit-flip neighbours
        indices = random.sample(range(SIZE), min(sample_size, SIZE))
        best_nb, best_nb_c, best_move = None, float("inf"), None

        for idx in indices:
            nb = current[:]
            nb[idx] = 1 - nb[idx]
            nb_c = cost(nb)
            # Accept if not tabu OR aspiration criterion met
            if idx not in tabu_list or nb_c < best_c:
                if nb_c < best_nb_c:
                    best_nb, best_nb_c, best_move = nb, nb_c, idx

        if best_nb is None:
            break

        current = best_nb
        tabu_list.append(best_move)
        if len(tabu_list) > tabu_tenure:
            tabu_list.pop(0)

        if best_nb_c < best_c:
            best, best_c = current[:], best_nb_c

        history.append(best_c)

    return best, best_c, history

ts_sched, ts_cost_val, ts_history = tabu_search()

# ── Genetic Algorithm (DEAP) ───────────────────────────────────────────────
if not hasattr(creator, "FitnessMinSched"):
    creator.create("FitnessMinSched", base.Fitness, weights=(-1.0,))
if not hasattr(creator, "IndividualSched"):
    creator.create("IndividualSched", list, fitness=creator.FitnessMinSched)

def eval_sched(ind):
    return (cost(list(ind)),)

def make_individual():
    return creator.IndividualSched(random_schedule())

tb2 = base.Toolbox()
tb2.register("individual", make_individual)
tb2.register("population", tools.initRepeat, list, tb2.individual)
tb2.register("evaluate", eval_sched)
tb2.register("mate", tools.cxUniform, indpb=0.5)
tb2.register("mutate", tools.mutFlipBit, indpb=1.0 / SIZE)
tb2.register("select", tools.selTournament, tournsize=3)

pop2 = tb2.population(n=100)
hof2 = tools.HallOfFame(1)
stats2 = tools.Statistics(lambda ind: ind.fitness.values)
stats2.register("min", np.min)
pop2, log2 = algorithms.eaSimple(
    pop2, tb2, cxpb=0.6, mutpb=0.3, ngen=200,
    stats=stats2, halloffame=hof2, verbose=False
)
ga_sched = list(hof2[0])
ga_cost_val = cost(ga_sched)
ga_hist2 = log2.select("min")

print(f"Tabu Search cost : {ts_cost_val}")
print(f"GA cost          : {ga_cost_val}")

# ── Convergence + Heatmaps ─────────────────────────────────────────────────
shift_abbr = ["M", "E", "N"]
col_labels = [f"D{d+1}-{shift_abbr[s]}" for d in range(N_DAYS) for s in range(N_SHIFTS)]

fig, axes = plt.subplots(1, 3, figsize=(18, 6))

# Convergence
axes[0].plot(ts_history, label="Tabu Search", color="tab:orange")
axes[0].plot(ga_hist2, label="GA", color="tab:blue")
axes[0].set_xlabel("Iteration / Generation")
axes[0].set_ylabel("Cost")
axes[0].set_title("Convergence — Hospital Scheduling")
axes[0].legend()
axes[0].grid(True, alpha=0.3)

# Heatmaps
for ax, sched_flat, title in zip(
    axes[1:],
    [ts_sched, ga_sched],
    ["Tabu Search Schedule", "GA Schedule"],
):
    data = decode(sched_flat).reshape(N_NURSES, -1)
    im = ax.imshow(data, cmap="Blues", aspect="auto", vmin=0, vmax=1)
    ax.set_title(title)
    ax.set_xlabel("Day–Shift")
    ax.set_ylabel("Nurse")
    ax.set_xticks(range(N_DAYS * N_SHIFTS))
    ax.set_xticklabels(col_labels, rotation=90, fontsize=7)
    ax.set_yticks(range(N_NURSES))
    ax.set_yticklabels([f"N{n+1}" for n in range(N_NURSES)], fontsize=7)

plt.colorbar(im, ax=axes[1:], label="Assigned")
plt.tight_layout()
plt.savefig("hospital_schedule.png", dpi=100)
plt.show()
```

---

!!! question "Check Your Understanding — Part 2"
    1. In Example A, NN+2-opt finishes in milliseconds whereas the GA takes several seconds. When would you *prefer* the faster but weaker method in a military context?
    2. In Example B, why is the **aspiration criterion** important for Tabu Search? What could go wrong if all tabu moves were strictly forbidden?
    3. The hospital scheduling cost function combines hard constraints (coverage deficit) with soft constraints (preferences) using penalty weights. How would you choose the relative magnitudes of these weights?

---

## Part 3 — Interactive Exercises

### Exercise 1 — Guided (Warm-Up): 2-opt TSP Local Search

!!! question "Task"
    Complete the skeleton below to implement 2-opt hill climbing on a 20-city random TSP instance. Fill in all `TODO` blocks, then report the percentage improvement over the random starting tour.

```python
# pip install numpy
import random
import numpy as np

random.seed(42)
np.random.seed(42)

N = 20
cities = np.random.rand(N, 2) * 100  # 100 km × 100 km

def tour_distance(tour):
    """Sum of Euclidean distances for a closed tour."""
    return sum(
        np.linalg.norm(cities[tour[i]] - cities[tour[(i + 1) % N]])
        for i in range(N)
    )

def two_opt_swap(tour, i, j):
    """Return a new tour with the segment tour[i:j+1] reversed."""
    # TODO: build and return the new tour
    # new_tour = tour[:i] + tour[i:j+1][::-1] + tour[j+1:]
    pass

def hill_climb_2opt(tour):
    """Improve tour with 2-opt moves until no improving swap exists."""
    best = tour[:]
    best_dist = tour_distance(best)
    improved = True
    while improved:
        improved = False
        for i in range(1, N - 1):
            for j in range(i + 1, N):
                # TODO: generate candidate and check improvement
                # candidate = two_opt_swap(best, i, j)
                # cand_dist = tour_distance(candidate)
                # if cand_dist < best_dist:
                #     best, best_dist = candidate, cand_dist
                #     improved = True
                pass
    return best, best_dist

# TODO: Create a random starting tour, run hill climbing, report results
# initial = list(range(N))
# random.shuffle(initial)
# initial_dist = tour_distance(initial)
# final_tour, final_dist = hill_climb_2opt(initial)
# improvement = (initial_dist - final_dist) / initial_dist * 100
# print(f"Initial distance : {initial_dist:.2f} km")
# print(f"After 2-opt      : {final_dist:.2f} km")
# print(f"Improvement      : {improvement:.1f}%")
```

??? success "Reference solution"
    ```python
    import random
    import numpy as np

    random.seed(42)
    np.random.seed(42)

    N = 20
    cities = np.random.rand(N, 2) * 100

    def tour_distance(tour):
        return sum(
            np.linalg.norm(cities[tour[i]] - cities[tour[(i + 1) % N]])
            for i in range(N)
        )

    def two_opt_swap(tour, i, j):
        return tour[:i] + tour[i:j + 1][::-1] + tour[j + 1:]

    def hill_climb_2opt(tour):
        best = tour[:]
        best_dist = tour_distance(best)
        improved = True
        while improved:
            improved = False
            for i in range(1, N - 1):
                for j in range(i + 1, N):
                    candidate = two_opt_swap(best, i, j)
                    cand_dist = tour_distance(candidate)
                    if cand_dist < best_dist:
                        best, best_dist = candidate, cand_dist
                        improved = True
        return best, best_dist

    initial = list(range(N))
    random.shuffle(initial)
    initial_dist = tour_distance(initial)
    final_tour, final_dist = hill_climb_2opt(initial)
    improvement = (initial_dist - final_dist) / initial_dist * 100
    print(f"Initial distance : {initial_dist:.2f} km")
    print(f"After 2-opt      : {final_dist:.2f} km")
    print(f"Improvement      : {improvement:.1f}%")
    ```

### Exercise 2 — Exploratory: Simulated Annealing Parameter Tuning

!!! question "Task"
    Run the SA below on a 50-city TSP over a 3 × 3 grid of parameters ($T_0 \in \{10, 100, 1000\}$, $\alpha \in \{0.99, 0.995, 0.999\}$). Each combination is run 5 times. Plot a 3 × 3 grid of convergence curves and identify the best parameter combination. Explain *why* it works better.

```python
# pip install numpy matplotlib
import random
import math
import numpy as np
import matplotlib.pyplot as plt

random.seed(7)
np.random.seed(7)

N = 50
cities = np.random.rand(N, 2) * 100

def tour_dist(tour):
    return sum(
        np.linalg.norm(cities[tour[i]] - cities[tour[(i + 1) % N]])
        for i in range(N)
    )

def sa_run(T0, alpha, n_iter=20_000, seed=0):
    random.seed(seed)
    tour = list(range(N))
    random.shuffle(tour)
    cost = tour_dist(tour)
    best_cost = cost
    T = T0
    history = [best_cost]
    for _ in range(n_iter):
        i, j = sorted(random.sample(range(N), 2))
        nb = tour[:i] + tour[i:j + 1][::-1] + tour[j + 1:]
        nc = tour_dist(nb)
        # max(T, 1e-9) prevents ZeroDivisionError when temperature reaches zero
        if nc < cost or random.random() < math.exp(-(nc - cost) / max(T, 1e-9)):
            tour, cost = nb, nc
            if cost < best_cost:
                best_cost = cost
        T *= alpha
        history.append(best_cost)
    return best_cost, history

T0_values = [10, 100, 1000]
alpha_values = [0.99, 0.995, 0.999]
results = {}

fig, axes = plt.subplots(3, 3, figsize=(12, 9))
for i, T0 in enumerate(T0_values):
    for j, alpha in enumerate(alpha_values):
        best_run_cost = float("inf")
        best_hist = None
        for seed in range(5):
            bc, hist = sa_run(T0, alpha, seed=seed)
            if bc < best_run_cost:
                best_run_cost, best_hist = bc, hist
        results[(T0, alpha)] = best_run_cost
        ax = axes[i][j]
        ax.plot(best_hist, color="tab:orange", linewidth=0.7)
        ax.set_title(f"T0={T0}, a={alpha}\nbest={best_run_cost:.0f}", fontsize=8)
        ax.set_xlabel("Iter", fontsize=7)
        ax.set_ylabel("Distance", fontsize=7)
        ax.tick_params(labelsize=6)

plt.suptitle("SA Parameter Grid — 50-City TSP (best of 5 runs)", fontsize=11)
plt.tight_layout()
plt.savefig("sa_grid.png", dpi=100)
plt.show()

best_params = min(results, key=results.get)
print(f"\nBest: T0={best_params[0]}, α={best_params[1]} "
      f"→ distance = {results[best_params]:.2f}")
```

!!! tip "Guidance"
    - A very low $T_0$ never escapes local optima.  
    - A very slow $\alpha$ (e.g. 0.999) keeps the temperature high too long, wasting budget on random walks.  
    - The best combination typically balances exploration early and exploitation late within the iteration budget.

### Exercise 3 — Challenge: Multi-Objective Sensor Placement (NSGA-II)

!!! question "Task"
    Place $k = 8$ sensors on a 50 × 50 grid to **maximise coverage** of 100 target points while **minimising mutual interference** (sensor pairs closer than 5 units). Use DEAP's NSGA-II to approximate the Pareto front, plot it, and visualise one balanced Pareto-optimal solution. Discuss which solution a military planner would prefer.

```python
# pip install deap numpy matplotlib
import random
import numpy as np
import matplotlib.pyplot as plt
from deap import base, creator, tools, algorithms

random.seed(123)
np.random.seed(123)

GRID = 50
K = 8
N_TARGETS = 100
COVER_RADIUS = 8.0
MIN_DIST = 5.0

targets = np.random.rand(N_TARGETS, 2) * GRID

def decode_sensors(ind):
    return np.array(ind).reshape(K, 2)

def coverage(ind):
    """Number of targets covered by at least one sensor."""
    sensors = decode_sensors(ind)
    dists = np.linalg.norm(sensors[:, None, :] - targets[None, :, :], axis=2)
    return int((dists.min(axis=0) <= COVER_RADIUS).sum())

def interference(ind):
    """Number of sensor pairs closer than MIN_DIST."""
    sensors = decode_sensors(ind)
    count = 0
    for a in range(K):
        for b in range(a + 1, K):
            if np.linalg.norm(sensors[a] - sensors[b]) < MIN_DIST:
                count += 1
    return count

def evaluate(ind):
    """Objectives: maximise coverage (negate), minimise interference."""
    return (-coverage(ind), interference(ind))

if not hasattr(creator, "FitnessMO"):
    creator.create("FitnessMO", base.Fitness, weights=(-1.0, -1.0))
if not hasattr(creator, "IndividualMO"):
    creator.create("IndividualMO", list, fitness=creator.FitnessMO)

tb3 = base.Toolbox()
tb3.register("attr_float", random.uniform, 0, GRID)
tb3.register(
    "individual", tools.initRepeat, creator.IndividualMO, tb3.attr_float, n=K * 2
)
tb3.register("population", tools.initRepeat, list, tb3.individual)
tb3.register("evaluate", evaluate)
tb3.register(
    "mate", tools.cxSimulatedBinaryBounded, low=0, up=GRID, eta=15
)
tb3.register(
    "mutate", tools.mutPolynomialBounded, low=0, up=GRID, eta=20, indpb=1.0 / (K * 2)
)
tb3.register("select", tools.selNSGA2)

pop3 = tb3.population(n=200)
for ind in pop3:
    ind.fitness.values = tb3.evaluate(ind)
# Run selNSGA2 once to assign crowding_dist and rank attributes to every
# individual. selTournamentDCD uses these for dominance-based comparisons
# and raises AttributeError if they are not set before the first generation.
pop3 = tb3.select(pop3, len(pop3))

NGEN = 150
for gen in range(NGEN):
    offspring = tools.selTournamentDCD(pop3, len(pop3))
    offspring = [tb3.clone(o) for o in offspring]
    for c1, c2 in zip(offspring[::2], offspring[1::2]):
        if random.random() < 0.9:
            tb3.mate(c1, c2)
            del c1.fitness.values, c2.fitness.values
    for mutant in offspring:
        if random.random() < 0.2:
            tb3.mutate(mutant)
            del mutant.fitness.values
    invalid = [ind for ind in offspring if not ind.fitness.valid]
    for ind in invalid:
        ind.fitness.values = tb3.evaluate(ind)
    pop3 = tb3.select(pop3 + offspring, len(pop3))

pareto = tools.sortNondominated(pop3, len(pop3), first_front_only=True)[0]
covs = [-ind.fitness.values[0] for ind in pareto]
intfs = [ind.fitness.values[1] for ind in pareto]

# Sort by coverage for a clean plot
order = np.argsort(covs)
covs = [covs[i] for i in order]
intfs = [intfs[i] for i in order]
pareto = [pareto[i] for i in order]

fig, axes = plt.subplots(1, 2, figsize=(13, 5))

# Pareto front scatter
axes[0].scatter(intfs, covs, color="tab:purple", s=60, zorder=3)
axes[0].plot(intfs, covs, color="gray", alpha=0.4)
for idx, (c, itf) in enumerate(zip(covs, intfs)):
    axes[0].annotate(str(idx), (itf, c), fontsize=7, ha="center", va="bottom")
axes[0].set_xlabel("Interference (sensor pairs too close)")
axes[0].set_ylabel("Coverage (targets covered)")
axes[0].set_title("Pareto Front — Sensor Placement")
axes[0].grid(True, alpha=0.3)

# Balanced solution (middle of front)
mid = len(pareto) // 2
sensors = decode_sensors(pareto[mid])
axes[1].scatter(targets[:, 0], targets[:, 1], c="gray", s=20, alpha=0.5, label="Targets")
for s in sensors:
    circle = plt.Circle(s, COVER_RADIUS, color="tab:blue", alpha=0.15)
    axes[1].add_patch(circle)
axes[1].scatter(sensors[:, 0], sensors[:, 1], c="tab:red", s=90,
                zorder=5, label="Sensors")
axes[1].set_xlim(0, GRID)
axes[1].set_ylim(0, GRID)
axes[1].set_aspect("equal")
axes[1].set_title(
    f"Balanced Pareto Solution (idx {mid})\n"
    f"Coverage: {covs[mid]}, Interference: {intfs[mid]}"
)
axes[1].legend()
axes[1].grid(True, alpha=0.3)

plt.tight_layout()
plt.savefig("sensor_pareto.png", dpi=100)
plt.show()

print(f"\nPareto front has {len(pareto)} solutions.")
print(f"Coverage range   : {min(covs)} – {max(covs)}")
print(f"Interference range: {min(intfs)} – {max(intfs)}")
```

!!! tip "Military Planning Perspective"
    A military planner values **maximum coverage** to detect all threats. However, closely-spaced sensors are vulnerable to a single attack. A solution with high coverage and zero (or low) interference represents the most resilient deployment. Use the Pareto front to make an explicit, informed trade-off rather than arbitrarily weighting the two objectives.

---

!!! question "Check Your Understanding — Part 3"
    1. In Exercise 1, why does 2-opt hill climbing get stuck in local optima even on small instances? What additional move types could help?
    2. In Exercise 2, what effect does a very slow cooling rate ($\alpha = 0.999$) combined with a low $T_0 = 10$ have on the SA search trajectory?
    3. In Exercise 3, the Pareto front approximates a continuous trade-off curve. How would you advise a decision-maker to choose a single solution from the front when hard coverage requirements (e.g., "≥ 80 targets must be covered") must be met?

---

## Summary

| Method | Type | Key Strength | Key Weakness |
|--------|------|-------------|--------------|
| Nearest-Neighbour | Constructive | Very fast ($O(n^2)$) | Often 20–25 % from optimal |
| 2-opt / 3-opt | Local search | Simple, effective | Local optima |
| Simulated Annealing | Trajectory | Escape local optima, few parameters | Sensitive to cooling schedule |
| Tabu Search | Trajectory | Memory prevents cycling | Tabu tenure tuning |
| Genetic Algorithm | Population | Parallelism, diverse search | Many hyperparameters |
| PSO | Population | Continuous spaces, easy to implement | Premature convergence |
| ACO | Population | Excellent for routing/graph problems | Slow pheromone convergence |
| NSGA-II | Multi-objective | Pareto front in one run | Computationally expensive |

---

!!! tip "Prompt Template"
    Use the [Agent Prompt](prompt.md) for this topic, or the [Generic Prompt Template](../prompt-template.md) to generate additional lectures, examples, and exercises.
