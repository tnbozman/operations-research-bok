# Simulation

> Modeling stochastic systems to estimate performance and risk.

---

## Lecture

### What is Simulation?

Simulation uses computational models to imitate the operation of real-world systems over time. Unlike optimization (which finds the *best* solution), simulation explores *what happens* under given assumptions, capturing randomness, dynamics, and complex interactions that defy closed-form analysis.

### Types of Simulation

| Type | Description |
|------|-------------|
| **Discrete-Event Simulation (DES)** | Models systems as sequences of events at discrete time points |
| **Monte Carlo Simulation** | Uses random sampling to estimate numerical quantities |
| **Agent-Based Modeling (ABM)** | Autonomous agents follow rules; emergent behavior arises |
| **System Dynamics** | Continuous-time models using stocks, flows, and feedback loops |

### Key Concepts

- **Random variate generation** — sampling from probability distributions
- **Replication and confidence intervals** — statistical rigor in output analysis
- **Warm-up period** — discarding initial transient data
- **Verification** — does the model run correctly?
- **Validation** — does the model represent reality?
- **Variance reduction** — techniques to improve estimate precision (common random numbers, antithetic variates)

### Historical Context

Monte Carlo methods were developed during the Manhattan Project (1940s) by Ulam and von Neumann. Discrete-event simulation grew alongside computing in the 1960s–70s, becoming a standard tool for logistics, manufacturing, and military analysis.

### Relationship to Other OR Techniques

- Often used alongside **Queueing Theory** (simulation validates analytical models)
- Can embed **Linear/Integer Programming** for optimization within simulation
- Models **Stochastic Processes** directly
- Supports **Decision Analysis** by generating outcome distributions

---

## Real-World Examples

### Example A — Military: Logistics Convoy Simulation

!!! example "Scenario"
    Simulate a convoy resupply operation where vehicles arrive at a checkpoint, are inspected, and then proceed. Random arrival times and inspection durations create queues. Estimate average wait time and throughput.

**Open-Source Tools:** SimPy, Python

```python
# pip install simpy numpy
import simpy
import random
import numpy as np

random.seed(42)

def convoy_vehicle(env, name, checkpoint):
    """A vehicle arrives, waits for inspection, and departs."""
    arrival = env.now
    with checkpoint.request() as req:
        yield req
        wait = env.now - arrival
        # Inspection takes 5–15 minutes
        yield env.timeout(random.uniform(5, 15))
        return wait

def run_simulation(num_vehicles=50, num_inspectors=3):
    env = simpy.Environment()
    checkpoint = simpy.Resource(env, capacity=num_inspectors)
    wait_times = []

    def vehicle_generator():
        for i in range(num_vehicles):
            proc = env.process(vehicle_process(env, f"V{i}", checkpoint, wait_times))
            # Vehicles arrive every 2–8 minutes
            yield env.timeout(random.uniform(2, 8))

    def vehicle_process(env, name, checkpoint, results):
        arrival = env.now
        with checkpoint.request() as req:
            yield req
            wait = env.now - arrival
            yield env.timeout(random.uniform(5, 15))
            results.append(wait)

    env.process(vehicle_generator())
    env.run()

    print(f"Inspectors: {num_inspectors}")
    print(f"Avg Wait: {np.mean(wait_times):.1f} min")
    print(f"Max Wait: {np.max(wait_times):.1f} min")
    print(f"Throughput: {num_vehicles / env.now * 60:.1f} vehicles/hour")

run_simulation(num_vehicles=50, num_inspectors=3)
```

### Example B — Civilian: Monte Carlo Risk Analysis

!!! example "Scenario"
    Estimate the probability that a construction project exceeds its budget, given uncertain task costs following triangular distributions.

**Open-Source Tools:** NumPy, Matplotlib, Python

```python
# pip install numpy matplotlib
import numpy as np
import matplotlib.pyplot as plt

np.random.seed(42)

# Task costs: (min, most_likely, max)
tasks = {
    "Foundation":  (50_000,  70_000,  100_000),
    "Framing":     (80_000, 100_000,  140_000),
    "Electrical":  (20_000,  30_000,   50_000),
    "Plumbing":    (15_000,  25_000,   40_000),
    "Finishing":   (40_000,  55_000,   80_000),
}
budget = 310_000
n_simulations = 10_000

total_costs = np.zeros(n_simulations)
for name, (lo, mode, hi) in tasks.items():
    total_costs += np.random.triangular(lo, mode, hi, n_simulations)

prob_over = np.mean(total_costs > budget)
print(f"P(over budget): {prob_over:.1%}")
print(f"Mean cost: ${np.mean(total_costs):,.0f}")
print(f"95th percentile: ${np.percentile(total_costs, 95):,.0f}")

plt.hist(total_costs, bins=60, edgecolor="black", alpha=0.7)
plt.axvline(budget, color="red", linestyle="--", label=f"Budget: ${budget:,}")
plt.xlabel("Total Project Cost ($)")
plt.ylabel("Frequency")
plt.title("Monte Carlo Cost Risk Analysis")
plt.legend()
plt.tight_layout()
plt.savefig("monte_carlo_risk.png", dpi=150)
plt.show()
```

---

## Interactive Exercises

### Exercise 1 — Guided (Warm-Up)

!!! question "Task"
    Simulate a simple M/M/1 queue (single server, exponential inter-arrival and service times) using SimPy. Fill in the TODO blocks to track and report average wait time.

```python
import simpy
import random

random.seed(42)

ARRIVAL_RATE = 1.0   # customers per minute
SERVICE_RATE = 1.5   # customers per minute
NUM_CUSTOMERS = 200

def customer(env, server, results):
    arrival = env.now
    with server.request() as req:
        yield req
        wait = env.now - arrival
        # TODO: Sample service time from exponential distribution
        # yield env.timeout(...)
        results.append(wait)

def generator(env, server, results):
    for i in range(NUM_CUSTOMERS):
        env.process(customer(env, server, results))
        # TODO: Sample inter-arrival time from exponential distribution
        # yield env.timeout(...)

env = simpy.Environment()
server = simpy.Resource(env, capacity=1)
results = []
env.process(generator(env, server, results))
env.run()

# TODO: Print average and maximum wait times
```

### Exercise 2 — Exploratory (Intermediate)

!!! question "Task"
    Use Monte Carlo simulation to estimate the value of $\pi$ by randomly sampling points in a unit square and checking if they fall inside a quarter-circle. Plot convergence as sample size increases.

### Exercise 3 — Challenge (Advanced)

!!! question "Task"
    Build a discrete-event simulation of a military airfield with multiple runways, aircraft arrivals (scheduled + unscheduled), maintenance requirements, and weather disruptions. Track daily sortie rates and analyze how adding a runway affects throughput. Visualize with plotly.

---

## Check Your Understanding

1. When should you use simulation instead of an analytical model?
2. How many replications do you need, and how do you determine this?
3. What is the difference between verification and validation?

---

!!! tip "Prompt Template"
    Use the [Agent Prompt](prompt.md) for this topic, or the [Generic Prompt Template](../prompt-template.md) to generate additional lectures, examples, and exercises.
