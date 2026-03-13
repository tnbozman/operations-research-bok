# Queueing Theory

> Analyzing waiting lines and service systems.

---

## Lecture

### What is Queueing Theory?

Queueing Theory provides mathematical models for analyzing waiting lines — systems where entities (customers, vehicles, packets, wounded soldiers) arrive, wait for service, and depart. It predicts performance metrics like wait times, queue lengths, and server utilization.

### Kendall's Notation: A/S/c/K/N/D

| Symbol | Meaning | Common Values |
|--------|---------|---------------|
| A | Arrival process | M (Markovian/Poisson), D (Deterministic), G (General) |
| S | Service distribution | M, D, G |
| c | Number of servers | 1, 2, ... |
| K | System capacity | ∞ (default) |
| N | Population size | ∞ (default) |
| D | Queue discipline | FIFO (default), LIFO, Priority |

### Key Results — M/M/1 Queue

For arrival rate $\lambda$ and service rate $\mu$ with $\rho = \lambda/\mu < 1$:

$$
\begin{align}
L &= \frac{\rho}{1 - \rho} & \text{(avg number in system)} \\
W &= \frac{1}{\mu - \lambda} & \text{(avg time in system)} \\
L_q &= \frac{\rho^2}{1 - \rho} & \text{(avg queue length)} \\
W_q &= \frac{\rho}{\mu - \lambda} & \text{(avg wait in queue)}
\end{align}
$$

### Key Concepts

- **Little's Law:** $L = \lambda W$ — universally applicable, distribution-free
- **Traffic intensity:** $\rho = \lambda / (c \cdot \mu)$ — must be < 1 for stability
- **Erlang formulas** — M/M/c and M/M/c/K models
- **Priority queues** — preemptive vs. non-preemptive
- **Networks of queues** — Jackson networks, open and closed systems

### Historical Context

A.K. Erlang developed queueing theory in the early 1900s for telephone network design. It expanded during WWII into military logistics and has since become fundamental to telecommunications, healthcare, and service operations.

### Relationship to Other OR Techniques

- Analytical complement to **Simulation** (closed-form vs. computational)
- Built on **Stochastic Processes** (Markov chains, birth-death processes)
- Informs **Decision Analysis** about capacity planning

---

## Real-World Examples

### Example A — Military: Field Hospital Triage

!!! example "Scenario"
    Analyze a field hospital where casualties arrive following a Poisson process during an operation. Compare staffing levels (M/M/c model) on average wait times.

**Open-Source Tools:** Python, NumPy

```python
# pip install numpy
import numpy as np
from math import factorial

def mmc_metrics(lam, mu, c):
    """Compute M/M/c queue performance metrics."""
    rho = lam / (c * mu)
    if rho >= 1:
        return {"stable": False, "rho": rho}

    # P(0) — probability system is empty
    sum_term = sum((c * rho)**n / factorial(n) for n in range(c))
    last_term = (c * rho)**c / (factorial(c) * (1 - rho))
    p0 = 1 / (sum_term + last_term)

    # Erlang-C: P(wait)
    erlang_c = ((c * rho)**c / (factorial(c) * (1 - rho))) * p0

    Lq = erlang_c * rho / (1 - rho)
    Wq = Lq / lam
    W = Wq + 1 / mu
    L = lam * W

    return {
        "stable": True, "rho": rho,
        "L": L, "Lq": Lq,
        "W_min": W * 60, "Wq_min": Wq * 60,
        "utilization": rho,
    }

lam = 10   # casualties per hour
mu = 4     # service rate per medical team (per hour)

print("Field Hospital Staffing Analysis")
print("=" * 50)
for c in [3, 4, 5, 6]:
    m = mmc_metrics(lam, mu, c)
    if m["stable"]:
        print(f"\n{c} medical teams (utilization: {m['rho']:.1%}):")
        print(f"  Avg wait in queue: {m['Wq_min']:.1f} min")
        print(f"  Avg time in system: {m['W_min']:.1f} min")
        print(f"  Avg patients in system: {m['L']:.1f}")
    else:
        print(f"\n{c} teams: UNSTABLE (ρ = {m['rho']:.2f} ≥ 1)")
```

### Example B — Civilian: Call Center Staffing

!!! example "Scenario"
    Determine how many agents a call center needs to maintain an average wait time under 2 minutes, given known arrival and service rates.

**Open-Source Tools:** Python

```python
from math import factorial

def find_min_agents(lam, mu, max_wait_min):
    """Find minimum agents to achieve target wait time."""
    max_wait = max_wait_min / 60  # convert to hours
    for c in range(1, 200):
        rho = lam / (c * mu)
        if rho >= 1:
            continue
        sum_term = sum((c * rho)**n / factorial(n) for n in range(c))
        last_term = (c * rho)**c / (factorial(c) * (1 - rho))
        p0 = 1 / (sum_term + last_term)
        erlang_c = ((c * rho)**c / (factorial(c) * (1 - rho))) * p0
        Wq = (erlang_c * rho / (1 - rho)) / lam
        if Wq <= max_wait:
            return c, Wq * 60
    return None, None

lam = 120   # calls per hour
mu = 12     # calls handled per agent per hour

agents, wait = find_min_agents(lam, mu, max_wait_min=2.0)
print(f"Minimum agents needed: {agents}")
print(f"Resulting avg wait: {wait:.1f} minutes")
```

---

## Interactive Exercises

### Exercise 1 — Guided (Warm-Up)

!!! question "Task"
    Implement the M/M/1 queue formulas. Given $\lambda = 8$ arrivals/hr and $\mu = 10$ services/hr, calculate $L$, $L_q$, $W$, $W_q$, and utilization $\rho$. Fill in the TODOs.

```python
lam = 8   # arrival rate
mu = 10   # service rate

# TODO: Calculate traffic intensity
# rho = ...

# TODO: Calculate average number in system (L)
# L = ...

# TODO: Calculate average number in queue (Lq)
# Lq = ...

# TODO: Calculate average time in system (W) in minutes
# W = ...

# TODO: Calculate average wait in queue (Wq) in minutes
# Wq = ...

# print(f"ρ = {rho:.2f}")
# print(f"L = {L:.2f}, Lq = {Lq:.2f}")
# print(f"W = {W:.1f} min, Wq = {Wq:.1f} min")
```

### Exercise 2 — Exploratory (Intermediate)

!!! question "Task"
    Compare analytical M/M/c results with a SimPy simulation for the same parameters. Plot both on one chart. How large does the simulation need to be for results to converge within 5% of the analytical values?

### Exercise 3 — Challenge (Advanced)

!!! question "Task"
    Model a priority queueing system for a military MEDEVAC operation with 3 triage categories (urgent, priority, routine). Implement both analytical formulas and a SimPy simulation. Analyze how the arrival rate of urgent cases affects wait times for routine cases.

---

## Check Your Understanding

1. What does Little's Law tell us, and why is it so powerful?
2. Why does a system become unstable when $\rho \geq 1$?
3. In what situations would an M/M/c model be inadequate?

---

!!! tip "Prompt Template"
    Use the [Agent Prompt](prompt.md) for this topic, or the [Generic Prompt Template](../prompt-template.md) to generate additional lectures, examples, and exercises.
