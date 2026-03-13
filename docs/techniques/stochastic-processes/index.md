# Stochastic Processes

> Modeling systems that evolve probabilistically over time.

---

## Lecture

### What are Stochastic Processes?

A stochastic process is a collection of random variables $\{X(t) : t \in T\}$ indexed by time (or another parameter). They model systems where outcomes evolve randomly — equipment degradation, force attrition, demand fluctuations, and epidemic spread.

### Types of Stochastic Processes

| Type | Description |
|------|-------------|
| **Discrete-Time Markov Chains (DTMC)** | State transitions at discrete steps with memoryless property |
| **Continuous-Time Markov Chains (CTMC)** | State transitions at any time; exponential holding times |
| **Poisson Processes** | Counting arrivals of random events over time |
| **Markov Decision Processes (MDP)** | Markov chains with decisions and rewards |
| **Renewal Processes** | Generalized counting processes with i.i.d. inter-arrival times |
| **Brownian Motion** | Continuous-path process for modeling diffusion |

### Key Concepts

- **Markov property** — the future depends only on the present, not the past
- **Transition matrix** $\mathbf{P}$ — $P_{ij} = P(X_{n+1} = j \mid X_n = i)$
- **Steady-state distribution** — $\boldsymbol{\pi} = \boldsymbol{\pi} \mathbf{P}$ with $\sum \pi_i = 1$
- **Absorbing states** — states that, once entered, cannot be left
- **Ergodicity** — long-run time averages equal ensemble averages
- **First passage time** — expected time to reach a state for the first time

### Markov Decision Processes

MDPs combine stochastic transitions with **decisions** and **rewards**:

$$
V^*(s) = \max_a \left[ R(s, a) + \gamma \sum_{s'} P(s' \mid s, a) V^*(s') \right]
$$

Where $V^*(s)$ is the optimal value function, $\gamma$ is a discount factor, and the equation is solved via **value iteration** or **policy iteration**.

### Historical Context

Andrey Markov introduced Markov chains (1906). Stochastic processes were systematized through the 20th century, with major contributions from Kolmogorov, Feller, and Bellman (dynamic programming/MDPs in the 1950s).

### Relationship to Other OR Techniques

- Foundation for **Queueing Theory** (birth-death processes)
- MDPs are the mathematical basis for reinforcement learning
- Drives **Simulation** models (generating random trajectories)
- Informs **Decision Analysis** (sequential decisions under uncertainty)

---

## Real-World Examples

### Example A — Military: Equipment Readiness Markov Chain

!!! example "Scenario"
    Model the readiness state of a military vehicle fleet. Vehicles transition between states: Fully Mission Capable (FMC), Partially Mission Capable (PMC), and Not Mission Capable (NMC). Analyze long-run readiness rates.

**Open-Source Tools:** NumPy, Python

```python
# pip install numpy
import numpy as np

states = ["FMC", "PMC", "NMC"]

# Transition matrix (daily)
P = np.array([
    [0.85, 0.10, 0.05],  # FMC -> FMC, PMC, NMC
    [0.30, 0.50, 0.20],  # PMC -> FMC, PMC, NMC
    [0.10, 0.40, 0.50],  # NMC -> FMC, PMC, NMC
])

# Verify rows sum to 1
assert np.allclose(P.sum(axis=1), 1)

# Steady-state distribution: π = πP, sum(π) = 1
# Solve (P^T - I)π = 0 with constraint sum = 1
A = P.T - np.eye(3)
A[-1] = [1, 1, 1]
b = np.array([0, 0, 1])
pi = np.linalg.solve(A, b)

print("Equipment Readiness Markov Chain")
print("=" * 40)
print(f"\nSteady-State Distribution:")
for s, p in zip(states, pi):
    print(f"  {s}: {p:.1%}")

# Simulate forward
print(f"\nSimulated evolution (starting all FMC):")
state_dist = np.array([1.0, 0.0, 0.0])
for day in [1, 7, 30, 90]:
    state_dist_t = state_dist @ np.linalg.matrix_power(P, day)
    fmc_pct = state_dist_t[0]
    print(f"  Day {day:>3}: FMC = {fmc_pct:.1%}")
```

### Example B — Civilian: Customer Churn Modeling

!!! example "Scenario"
    Model customer subscription states (Active, At-Risk, Churned) as an absorbing Markov chain. Calculate expected time before a customer churns and probability of recovery.

**Open-Source Tools:** NumPy, Python

```python
import numpy as np

states = ["Active", "At-Risk", "Churned"]

# Transition matrix (monthly)
P = np.array([
    [0.80, 0.15, 0.05],  # Active
    [0.25, 0.50, 0.25],  # At-Risk
    [0.00, 0.00, 1.00],  # Churned (absorbing)
])

# For absorbing chain analysis, extract Q (transient) and R (absorbing)
Q = P[:2, :2]   # transient states: Active, At-Risk
R = P[:2, 2:3]  # absorption into Churned

# Fundamental matrix N = (I - Q)^-1
N = np.linalg.inv(np.eye(2) - Q)

# Expected steps before absorption
expected_steps = N.sum(axis=1)
print("Customer Churn Analysis")
print("=" * 40)
print(f"Expected months before churning:")
print(f"  Starting Active:  {expected_steps[0]:.1f} months")
print(f"  Starting At-Risk: {expected_steps[1]:.1f} months")

# Absorption probabilities (all go to Churned since it's the only absorbing state)
B = N @ R
print(f"\nAbsorption is certain (probability = {B[0,0]:.2f})")
```

---

## Interactive Exercises

### Exercise 1 — Guided (Warm-Up)

!!! question "Task"
    Implement a Discrete-Time Markov Chain simulator. Given a transition matrix, simulate a trajectory of 100 steps and estimate the steady-state distribution from the simulation. Compare with the analytical result.

```python
import numpy as np

np.random.seed(42)

P = np.array([
    [0.7, 0.2, 0.1],
    [0.3, 0.4, 0.3],
    [0.2, 0.3, 0.5],
])
states = ["A", "B", "C"]

# TODO: Simulate 1000 steps starting from state 0
# trajectory = [0]
# for step in range(1000):
#     current = trajectory[-1]
#     next_state = ...  # sample from P[current]
#     trajectory.append(next_state)

# TODO: Estimate steady-state from simulation
# counts = ...
# estimated_pi = ...

# TODO: Compute analytical steady-state and compare
```

### Exercise 2 — Exploratory (Intermediate)

!!! question "Task"
    Model a simple inventory system as an MDP. Each period you observe stock level and choose how much to order (0, 1, or 2 units). Demand is random. Implement value iteration to find the optimal ordering policy. How does the optimal policy change with different holding and shortage costs?

### Exercise 3 — Challenge (Advanced)

!!! question "Task"
    Build a combat attrition model using Lanchester's laws as a continuous-time stochastic process. Compare deterministic Lanchester equations with a stochastic simulation. Analyze how force ratio, combat effectiveness, and reinforcement timing affect battle outcomes. Visualize force levels over time with confidence intervals.

---

## Check Your Understanding

1. What does the Markov property imply, and when is it a reasonable assumption?
2. How do you determine if a Markov chain has a unique steady-state distribution?
3. What is the relationship between MDPs and reinforcement learning?

---

!!! tip "Prompt Template"
    Use the [Agent Prompt](prompt.md) for this topic, or the [Generic Prompt Template](../prompt-template.md) to generate additional lectures, examples, and exercises.
