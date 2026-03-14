# Queueing Theory

> Analyzing waiting lines and service systems to optimize capacity, throughput, and delay.

---

## Part 1 — Lecture

### 1.1 Queueing System Anatomy

A **queueing system** consists of five core components:

| Component | Description | Military Example |
|-----------|-------------|-----------------|
| **Arrival process** | How entities enter the system (rate, distribution) | Casualties arriving at a field hospital |
| **Service mechanism** | How many servers, service time distribution | Trauma surgeons treating patients |
| **Queue discipline** | Rule for selecting next entity from the queue | Triage priority (most urgent first) |
| **System capacity** | Maximum number of entities allowed in system | Hospital beds available |
| **Calling population** | Size of the source population | Finite battalion vs. infinite stream |

!!! example "Motivating Example — Military Resupply Checkpoint"
    Convoys arrive at a Forward Operating Base (FOB) supply checkpoint at an average rate of 6 per hour. Each convoy requires an average of 8 minutes for inspection and unloading. There are 2 inspection teams. Entities queue in FIFO order. This is a classic **M/M/2** system.

    - Arrival process: Poisson (rate λ = 6/hr)
    - Service mechanism: 2 servers, exponential service (μ = 60/8 = 7.5/hr each)
    - Queue discipline: FIFO
    - System capacity: unlimited (∞)
    - Calling population: unlimited (large theater)

---

### 1.2 Kendall's Notation: A/S/c/K/N/D

Every queueing model can be described compactly using **Kendall's notation**:

$$
A / S / c / K / N / D
$$

| Symbol | Meaning | Common Values |
|--------|---------|---------------|
| **A** | Arrival process distribution | **M** (Markov/Poisson), **D** (Deterministic), **G** (General) |
| **S** | Service time distribution | **M**, **D**, **G** |
| **c** | Number of servers | 1, 2, 3, … |
| **K** | System capacity (max in system) | ∞ (default if omitted) |
| **N** | Calling population size | ∞ (default if omitted) |
| **D** | Queue discipline | FIFO (default), LIFO, SIRO, Priority |

**Common model examples:**

| Model | Description | Use Case |
|-------|-------------|----------|
| **M/M/1** | Single server, Poisson arrivals, exponential service | Help desk, single toll booth |
| **M/M/c** | c servers, Poisson arrivals, exponential service | Call center, multi-lane checkpoint |
| **M/D/1** | Single server, deterministic service time | Assembly line, fixed-time processes |
| **G/G/1** | General distributions for both arrival and service | Real-world systems with complex timing |
| **M/M/1/K** | M/M/1 with finite capacity K | Parking lot, finite buffer systems |

!!! note "Why M?"
    **M** stands for *Memoryless* (Markovian). The exponential distribution is the only continuous distribution with the memoryless property: the probability of service completing in the next instant is independent of how long service has already taken. This mathematical property makes steady-state analysis tractable.

---

### 1.3 Poisson Arrivals and Exponential Service: The Markov Property

#### Poisson Arrivals

If arrivals follow a **Poisson process** with rate λ, then:

- The number of arrivals in any interval $[0, t]$ follows a Poisson distribution: $P(N(t) = k) = \frac{(\lambda t)^k e^{-\lambda t}}{k!}$
- Inter-arrival times are **exponentially distributed** with mean $1/\lambda$
- Arrivals in non-overlapping intervals are **independent**

#### Exponential Service

Service times $S \sim \text{Exp}(\mu)$ with mean $1/\mu$ satisfy the **memoryless property**:

$$
P(S > s + t \mid S > s) = P(S > t) \quad \forall s, t \geq 0
$$

This means: knowing that a server has been busy for $s$ minutes tells us *nothing* about when it will finish. This is the Markov property applied to service.

#### Birth-Death Chain Formulation

The M/M/1 queue can be modeled as a **continuous-time Markov chain** (CTMC) where the state $n$ = number of customers in the system.

```
         λ         λ         λ         λ
   ──────────> ──────────> ──────────> ──────────>
0            1            2            3           ...
   <────────── <────────── <────────── <──────────
         μ         μ         μ         μ
```

**Balance equations** (flow in = flow out at each state):

$$
\lambda \cdot P_n = \mu \cdot P_{n+1} \quad \Rightarrow \quad P_{n+1} = \frac{\lambda}{\mu} P_n = \rho^{n+1} P_0
$$

Normalizing ($\sum_{n=0}^{\infty} P_n = 1$):

$$
P_0 \sum_{n=0}^{\infty} \rho^n = 1 \quad \Rightarrow \quad P_0 = 1 - \rho \quad \text{(requires } \rho < 1 \text{)}
$$

$$
\boxed{P_n = (1 - \rho)\rho^n, \quad n = 0, 1, 2, \ldots}
$$

!!! check "Check Your Understanding — Part 1.1–1.3"
    1. A military checkpoint **with one inspector** processes vehicles at a fixed rate of exactly 5 minutes each (deterministic). Vehicles arrive following a Poisson process at 10/hr. What Kendall notation describes this system?
    2. Why does the memoryless property of the exponential distribution make M/M/c queues tractable to analyze analytically while G/G/1 queues require simulation?
    3. In the birth-death chain for M/M/1, what happens physically when the system is in state $n=0$ and a new arrival occurs? What happens when it is in state $n=1$ and a service completion occurs?

---

### 1.4 Core M/M/1 Results: Derivation of L, Lq, W, Wq

Starting from $P_n = (1-\rho)\rho^n$, we derive all four performance metrics.

#### Average Number in System (L)

$$
L = \sum_{n=0}^{\infty} n \cdot P_n = (1-\rho) \sum_{n=0}^{\infty} n \rho^n
$$

Using $\sum_{n=0}^{\infty} n x^n = \frac{x}{(1-x)^2}$:

$$
\boxed{L = \frac{\rho}{1 - \rho}}
$$

#### Average Number in Queue (Lq)

$L_q$ counts only customers *waiting*, not being served. Since exactly one customer is being served when $n \geq 1$:

$$
L_q = \sum_{n=1}^{\infty}(n-1)P_n = L - (1 - P_0) = \frac{\rho}{1-\rho} - \rho = \frac{\rho^2}{1-\rho}
$$

$$
\boxed{L_q = \frac{\rho^2}{1 - \rho}}
$$

#### Average Time in System (W) and Queue (Wq) — via Little's Law

**Little's Law** states $L = \lambda W$, giving:

$$
\boxed{W = \frac{L}{\lambda} = \frac{1}{\mu - \lambda}}
$$

$$
\boxed{W_q = \frac{L_q}{\lambda} = \frac{\rho}{\mu - \lambda} = \frac{\lambda}{\mu(\mu - \lambda)}}
$$

**Summary table:**

| Metric | Formula | Interpretation |
|--------|---------|----------------|
| $\rho$ | $\lambda / \mu$ | Traffic intensity (server utilization) |
| $L$ | $\rho / (1-\rho)$ | Avg customers in system |
| $L_q$ | $\rho^2 / (1-\rho)$ | Avg customers waiting in queue |
| $W$ | $1 / (\mu - \lambda)$ | Avg time in system |
| $W_q$ | $\rho / (\mu - \lambda)$ | Avg wait time in queue |

#### Non-Linear Blow-Up as ρ → 1

```python
# pip install numpy matplotlib
import numpy as np
import matplotlib.pyplot as plt

mu = 1.0
rho_vals = np.linspace(0.01, 0.99, 500)
lam_vals = rho_vals * mu

Lq = rho_vals**2 / (1 - rho_vals)
Wq = rho_vals / (mu - lam_vals)

fig, axes = plt.subplots(1, 2, figsize=(12, 4))

axes[0].plot(rho_vals, Lq, color="steelblue", linewidth=2)
axes[0].axvline(1.0, color="red", linestyle="--", alpha=0.5, label="ρ = 1 (instability)")
axes[0].set_xlabel("Traffic Intensity ρ = λ/μ")
axes[0].set_ylabel("Avg Queue Length $L_q$")
axes[0].set_title("M/M/1: Queue Length vs. Utilization")
axes[0].set_ylim(0, 20)
axes[0].legend()
axes[0].grid(True, alpha=0.3)

axes[1].plot(rho_vals, Wq, color="darkorange", linewidth=2)
axes[1].axvline(1.0, color="red", linestyle="--", alpha=0.5, label="ρ = 1 (instability)")
axes[1].set_xlabel("Traffic Intensity ρ = λ/μ")
axes[1].set_ylabel("Avg Wait Time $W_q$ (service times)")
axes[1].set_title("M/M/1: Wait Time vs. Utilization")
axes[1].set_ylim(0, 20)
axes[1].legend()
axes[1].grid(True, alpha=0.3)

plt.suptitle("Non-linear blow-up as ρ → 1", fontsize=13)
plt.tight_layout()
plt.savefig("mm1_blowup.png", dpi=150)
plt.show()
print(f"At ρ=0.5: Wq = {0.5/(1-0.5):.2f} service times")
print(f"At ρ=0.8: Wq = {0.8/(1-0.8):.2f} service times")
print(f"At ρ=0.9: Wq = {0.9/(1-0.9):.2f} service times")
print(f"At ρ=0.95: Wq = {0.95/(1-0.95):.2f} service times")
```

!!! warning "The Capacity Trap"
    At 50% utilization, $W_q = 1$ service time. At 90% utilization, $W_q = 9$ service times — a **9×** increase from a 40 percentage point rise in load. Planning a system to run at 95%+ utilization almost always leads to catastrophic queue buildup.

---

### 1.5 M/M/c Model: Erlang-C Formula

With **c** identical servers (each with rate μ), traffic intensity per server is $\rho = \lambda / (c\mu)$. Stability requires $\rho < 1$.

#### Computing P₀ (Probability System is Empty)

$$
P_0 = \left[\sum_{n=0}^{c-1} \frac{(c\rho)^n}{n!} + \frac{(c\rho)^c}{c!(1-\rho)}\right]^{-1}
$$

#### Erlang-C Formula C(c, λ/μ)

The probability that an arriving customer must wait (all servers busy):

$$
C(c, \lambda/\mu) = \frac{\frac{(c\rho)^c}{c!(1-\rho)} \cdot P_0}{1} = \frac{(c\rho)^c \cdot P_0}{c!(1-\rho)}
$$

#### Performance Metrics

$$
L_q = C(c, \lambda/\mu) \cdot \frac{\rho}{1-\rho}, \qquad W_q = \frac{L_q}{\lambda}, \qquad W = W_q + \frac{1}{\mu}, \qquad L = \lambda W
$$

**Step-by-step numerical example (M/M/3):**

```python
# pip install numpy
from math import factorial

def mmc_metrics(lam, mu, c):
    """Compute M/M/c steady-state performance metrics."""
    rho = lam / (c * mu)
    if rho >= 1:
        raise ValueError(f"System unstable: ρ = {rho:.3f} ≥ 1")

    # Step 1: Compute P0
    sum_term = sum((c * rho)**n / factorial(n) for n in range(c))
    last_term = (c * rho)**c / (factorial(c) * (1 - rho))
    p0 = 1.0 / (sum_term + last_term)

    # Step 2: Erlang-C — probability of waiting
    erlang_c = ((c * rho)**c / (factorial(c) * (1 - rho))) * p0

    # Step 3: Performance metrics
    Lq = erlang_c * rho / (1 - rho)
    Wq = Lq / lam                    # hours
    W  = Wq + 1.0 / mu               # hours
    L  = lam * W

    return {
        "rho": rho, "P0": p0,
        "Erlang_C": erlang_c,
        "L": L, "Lq": Lq,
        "W_min": W * 60, "Wq_min": Wq * 60,
    }

# Example: λ=12 calls/hr, μ=5 calls/hr/agent, c=3 agents
m = mmc_metrics(lam=12, mu=5, c=3)
print(f"ρ (per server)  = {m['rho']:.3f}")
print(f"P(system empty) = {m['P0']:.4f}")
print(f"Erlang-C P(wait)= {m['Erlang_C']:.4f}")
print(f"Avg in system L = {m['L']:.2f}")
print(f"Avg wait Wq     = {m['Wq_min']:.2f} min")
print(f"Avg time W      = {m['W_min']:.2f} min")
```

!!! check "Check Your Understanding — Part 1.4–1.5"
    1. For M/M/1 with λ = 6/hr and μ = 8/hr, compute L, Lq, W, and Wq by hand. Verify with the formulas above.
    2. Why does doubling the number of servers in an M/M/c model reduce waiting time more than proportionally compared to simply doubling the service rate?
    3. What is the Erlang-C value physically representing, and why is it critical for staffing decisions?

---

### 1.6 Little's Law

!!! abstract "Little's Law"
    For any stable queueing system in steady state:
    $$\boxed{L = \lambda W}$$
    where $L$ = average number in system, $\lambda$ = average arrival rate, $W$ = average time in system.

**Intuitive proof:** Imagine time-stamping each customer. In a long observation period $T$, a total of $\lambda T$ customers arrive. Each customer $i$ spends time $w_i$ in the system, contributing $w_i / T$ to the time-average count $L$. Summing over all customers: $L \approx \frac{1}{T} \sum_i w_i = \frac{\lambda T \cdot \bar{W}}{T} = \lambda W$.

**Why is it so powerful?** Little's Law holds for *any* stable queue, regardless of:

- Arrival distribution (not just Poisson)
- Service distribution (not just exponential)
- Number of servers
- Queue discipline (FIFO, LIFO, priority)
- Network topology (applies to each node and to the whole network)

**Three Applications:**

| Application | Known | Find |
|-------------|-------|------|
| 1. Airport security: 200 passengers waiting, throughput 50/min | L=200, λ=50/min | W = L/λ = 4 min average wait |
| 2. Hospital ER: patients spend avg 3 hrs, 8 arrive/hr | W=3 hr, λ=8/hr | L = λW = 24 patients in ER on average |
| 3. Software pipeline: 15 jobs in system, 5 complete/min | L=15, λ=5/min | W = 3 min average processing time |

```python
# Little's Law examples
scenarios = [
    ("Airport Security",   200,  50,   None),  # L, λ → W
    ("Hospital ER",       None,   8,      3),  # λ, W → L
    ("Software Pipeline",   15,   5,   None),  # L, λ → W
]

print("Little's Law: L = λW\n")
for name, L, lam, W in scenarios:
    if W is None:
        W = L / lam
        print(f"{name}: L={L}, λ={lam}/min → W = {W:.2f} min")
    else:
        L = lam * W
        print(f"{name}: λ={lam}/hr, W={W} hr → L = {L:.1f} in system")
```

---

### 1.7 Priority Queues: Preemptive vs. Non-Preemptive

Priority queueing assigns different urgency levels to customer classes. Class 1 has highest priority; class k has lowest.

| Type | Description | Example |
|------|-------------|---------|
| **Non-preemptive** | Higher-priority customer jumps the queue but waits if a server is busy | MEDEVAC T1 waits for current surgery to finish |
| **Preemptive** | Higher-priority customer immediately takes over the server, interrupting current service | Emergency broadcast interrupts scheduled programming |

#### Two-Priority M/M/1 Non-Preemptive Formula

For two priority classes with arrival rates $\lambda_1$, $\lambda_2$, service rates $\mu_1 = \mu_2 = \mu$, and total utilization $\rho = (\lambda_1 + \lambda_2)/\mu < 1$:

Let $\rho_i = \lambda_i / \mu$ and $\rho = \rho_1 + \rho_2$.

The mean waiting time in queue for each class (non-preemptive):

$$
W_{q,1} = \frac{W_0}{1 - \rho_1}
$$

$$
W_{q,2} = \frac{W_0}{(1 - \rho_1)(1 - \rho)}
$$

where $W_0 = \frac{\rho}{\mu}$ is the mean residual service time seen by an arriving customer. For exponential service with rate $\mu$, the residual service time of a customer already in service is also $\text{Exp}(\mu)$ (by the memoryless property), giving:

$$
W_0 = \frac{\rho_1 + \rho_2}{\mu} = \frac{\rho}{\mu}
$$

```python
def priority_mm1_nonpreemptive(lam1, lam2, mu):
    """
    Two-class non-preemptive priority M/M/1 queue.
    Class 1 = high priority, Class 2 = low priority.
    """
    rho1 = lam1 / mu
    rho2 = lam2 / mu
    rho  = rho1 + rho2
    if rho >= 1:
        raise ValueError(f"System unstable: ρ = {rho:.3f}")

    # Mean residual service time seen by arriving customer
    W0 = rho / mu

    Wq1 = W0 / (1 - rho1)
    Wq2 = W0 / ((1 - rho1) * (1 - rho))

    return {
        "rho": rho, "rho1": rho1, "rho2": rho2,
        "Wq1_min": Wq1 * 60, "Wq2_min": Wq2 * 60,
    }

# Example: MEDEVAC helicopter, λ1=2/hr urgent, λ2=5/hr routine, μ=10/hr
r = priority_mm1_nonpreemptive(lam1=2, lam2=5, mu=10)
print(f"Total utilization: {r['rho']:.2f}")
print(f"High-priority wait (Wq1): {r['Wq1_min']:.1f} min")
print(f"Low-priority wait  (Wq2): {r['Wq2_min']:.1f} min")
print(f"Penalty ratio Wq2/Wq1:    {r['Wq2_min']/r['Wq1_min']:.2f}x")
```

!!! warning "Impact on Lower-Priority Customers"
    In the example above, low-priority patients wait significantly longer than high-priority ones. As $\rho_1 \to 1$, the low-priority wait $W_{q,2} \to \infty$ even if $\rho < 1$. Priority queues trade fairness for urgency.

---

### 1.8 Jackson Networks: Product-Form Solution

A **Jackson network** is an open network of M/M/c queues where:

1. External arrivals to each node follow a Poisson process
2. Service times at each node are exponential
3. After service at node $i$, a customer routes to node $j$ with probability $p_{ij}$ or leaves the network with probability $1 - \sum_j p_{ij}$

**Key result (Jackson's theorem):** The steady-state joint probability distribution of the number of customers at all nodes is the **product of the marginal distributions**:

$$
P(n_1, n_2, \ldots, n_K) = \prod_{i=1}^{K} P_i(n_i)
$$

where $P_i(n_i)$ is the marginal distribution of node $i$ as if it were an **isolated M/M/c queue** with effective arrival rate $\lambda_i$.

**Effective arrival rates** are found by solving the traffic equations:

$$
\lambda_i = \gamma_i + \sum_{j=1}^{K} \lambda_j p_{ji} \quad \forall i
$$

where $\gamma_i$ is the external Poisson arrival rate to node $i$.

**Example: Two-Node Military Supply Chain**

```
External arrivals (γ=4/hr) → [Node 1: Intake, μ=6/hr]
                                        ↓ (80% of output)
                              [Node 2: Processing, μ=5/hr]
                                        ↓ (all output leaves)
                                Exit network
```

```python
# Jackson network: two-node open network
def jackson_two_node(gamma, mu1, mu2, p12):
    """
    gamma: external arrival rate to node 1
    mu1, mu2: service rates at nodes 1 and 2
    p12: fraction routed from node 1 to node 2
    """
    # Traffic equations: λ1 = γ, λ2 = λ1 * p12
    lam1 = gamma
    lam2 = lam1 * p12

    rho1 = lam1 / mu1
    rho2 = lam2 / mu2

    if rho1 >= 1 or rho2 >= 1:
        raise ValueError(f"Network unstable: ρ1={rho1:.2f}, ρ2={rho2:.2f}")

    # By Jackson's theorem, treat each node independently
    L1 = rho1 / (1 - rho1)
    L2 = rho2 / (1 - rho2)
    W1 = 1 / (mu1 - lam1)
    W2 = 1 / (mu2 - lam2)

    print(f"Node 1: λ={lam1:.1f}/hr, ρ={rho1:.2f}, L={L1:.2f}, W={W1*60:.1f} min")
    print(f"Node 2: λ={lam2:.1f}/hr, ρ={rho2:.2f}, L={L2:.2f}, W={W2*60:.1f} min")
    print(f"Avg total customers in network: {L1+L2:.2f}")
    print(f"Avg time in network (via Little's Law): {(L1+L2)/gamma*60:.1f} min")

jackson_two_node(gamma=4, mu1=6, mu2=5, p12=0.8)
```

!!! check "Check Your Understanding — Part 1.6–1.8"
    1. A manufacturing plant has 5 workers on a production line and produces 2 items per minute. Using Little's Law, estimate the average time each item spends in the system. What additional information would you need to find the average queue length?
    2. In a two-priority non-preemptive queue, why does increasing the arrival rate of high-priority customers disproportionately hurt low-priority customers even if total utilization stays constant?
    3. Jackson's theorem applies when each node behaves as an isolated M/M/c queue. What assumption about the arrival process at each node makes this possible, even though departures from one queue feed into another?

---

## Part 2 — Real-World Practical Examples

### Example A — Military: Field Hospital Capacity Planning

!!! example "Scenario"
    During a multi-phase operation, casualties arrive at a field hospital following a Poisson process. In the **low-intensity phase**, the arrival rate is **λ = 5/hr**. In the **high-intensity phase**, it rises to **λ = 18/hr**. Each trauma surgeon can handle an average of **μ = 6 patients/hr** (10-min average surgery). Two surgeons are initially available.

    Questions:
    (a) What are steady-state metrics under each phase with 2 surgeons (M/M/2)?
    (b) What is the minimum number of surgeons to keep mean wait below 20 minutes in the high-intensity phase?
    (c) How does a 3rd surgeon change utilization and wait?

**Open-Source Tools:** Python, NumPy, Matplotlib

```python
# pip install numpy matplotlib
import numpy as np
import matplotlib.pyplot as plt
from math import factorial

def mmc_metrics(lam, mu, c):
    """Compute M/M/c queue steady-state metrics. Returns dict or None if unstable."""
    rho = lam / (c * mu)
    if rho >= 1:
        return None

    sum_term = sum((c * rho)**n / factorial(n) for n in range(c))
    last_term = (c * rho)**c / (factorial(c) * (1 - rho))
    p0 = 1.0 / (sum_term + last_term)

    erlang_c = ((c * rho)**c / (factorial(c) * (1 - rho))) * p0
    Lq = erlang_c * rho / (1 - rho)
    Wq = Lq / lam
    W  = Wq + 1.0 / mu
    L  = lam * W

    return {
        "rho": rho, "P0": p0, "Erlang_C": erlang_c,
        "L": L, "Lq": Lq,
        "W_min": W * 60, "Wq_min": Wq * 60,
    }

mu = 6.0   # surgeons treat 6 patients/hr each

# ── (a) Steady-state with 2 surgeons ────────────────────────────────────────
print("=" * 55)
print("(a) Steady-State Metrics with 2 Surgeons")
print("=" * 55)
for phase, lam in [("Low-intensity (λ=5/hr)", 5), ("High-intensity (λ=18/hr)", 18)]:
    m = mmc_metrics(lam, mu, c=2)
    print(f"\n{phase}")
    if m:
        print(f"  Server utilization ρ  = {m['rho']:.2%}")
        print(f"  P(system empty) P₀    = {m['P0']:.4f}")
        print(f"  Erlang-C P(wait)      = {m['Erlang_C']:.4f}")
        print(f"  Avg patients in system L  = {m['L']:.2f}")
        print(f"  Avg wait in queue Wq  = {m['Wq_min']:.2f} min")
        print(f"  Avg time in system W  = {m['W_min']:.2f} min")
    else:
        print("  *** SYSTEM UNSTABLE (ρ ≥ 1) ***")

# ── (b) Minimum surgeons for Wq < 20 min in high-intensity phase ────────────
print("\n" + "=" * 55)
print("(b) Minimum Surgeons for Wq < 20 min (High Phase)")
print("=" * 55)
lam_high = 18
THRESHOLD_MIN = 20.0
for c in range(2, 15):
    m = mmc_metrics(lam_high, mu, c)
    if m:
        status = "✓ MEETS THRESHOLD" if m["Wq_min"] < THRESHOLD_MIN else "✗"
        print(f"  c={c}: ρ={m['rho']:.2f}, Wq={m['Wq_min']:.1f} min  {status}")
        if m["Wq_min"] < THRESHOLD_MIN:
            print(f"\n  → Minimum surgeons needed: {c}")
            break
    else:
        print(f"  c={c}: UNSTABLE")

# ── (c) Effect of 3rd surgeon vs 2 surgeons (high-intensity) ────────────────
print("\n" + "=" * 55)
print("(c) Impact of 3rd Surgeon (High-Intensity Phase)")
print("=" * 55)
for c in [2, 3]:
    m = mmc_metrics(18, mu, c)
    if m:
        print(f"  {c} surgeons: ρ={m['rho']:.2%}, Wq={m['Wq_min']:.2f} min, L={m['L']:.2f}")
    else:
        print(f"  {c} surgeons: UNSTABLE")

# ── Plot: Mean Wait vs. Number of Surgeons ───────────────────────────────────
fig, ax = plt.subplots(figsize=(9, 5))
surgeon_counts = range(2, 12)
threshold = 20.0

for phase_label, lam, color, marker in [
    ("Low phase (λ=5/hr)",  5,  "steelblue",  "o"),
    ("High phase (λ=18/hr)", 18, "firebrick", "s"),
]:
    wq_vals = []
    valid_c  = []
    for c in surgeon_counts:
        m = mmc_metrics(lam, mu, c)
        if m:
            wq_vals.append(m["Wq_min"])
            valid_c.append(c)
    ax.plot(valid_c, wq_vals, marker=marker, color=color,
            linewidth=2, markersize=7, label=phase_label)

ax.axhline(threshold, color="darkgreen", linestyle="--", linewidth=1.5,
           label=f"Service threshold: {threshold} min")
ax.set_xlabel("Number of Surgeons (c)", fontsize=12)
ax.set_ylabel("Mean Wait in Queue $W_q$ (minutes)", fontsize=12)
ax.set_title("Field Hospital: Mean Wait vs. Surgeon Count\n(M/M/c Model)", fontsize=13)
ax.legend(fontsize=10)
ax.set_xticks(list(surgeon_counts))
ax.grid(True, alpha=0.3)
plt.tight_layout()
plt.savefig("field_hospital_wait.png", dpi=150)
plt.show()
```

!!! info "Key Insight"
    Under the high-intensity phase, **4 surgeons** are needed to drop below the 20-minute threshold (ρ = 0.75 per server). With only 3 surgeons, ρ = 18/(3×6) = 1.0 exactly — right at the stability boundary — causing infinite queue buildup. Adding a 4th surgeon brings ρ to 0.75 and brings wait time well under 20 minutes.

---

### Example B — Civilian: Airport Security Lane Optimization

!!! example "Scenario"
    A major airport security checkpoint faces **Poisson arrivals at λ = 120 passengers/hr** with **exponential screening times averaging 4 minutes (μ = 15/hr per lane)**. Each lane costs **$200/hr** to operate; passenger waiting time costs **$0.50/min** per passenger. Find the optimal number of lanes.

**Open-Source Tools:** Python, NumPy, Matplotlib

```python
# pip install numpy matplotlib
import numpy as np
import matplotlib.pyplot as plt
from math import factorial

def mmc_metrics(lam, mu, c):
    """M/M/c metrics, returns None if unstable."""
    rho = lam / (c * mu)
    if rho >= 1:
        return None
    sum_term = sum((c * rho)**n / factorial(n) for n in range(c))
    last_term = (c * rho)**c / (factorial(c) * (1 - rho))
    p0 = 1.0 / (sum_term + last_term)
    erlang_c = ((c * rho)**c / (factorial(c) * (1 - rho))) * p0
    Lq = erlang_c * rho / (1 - rho)
    Wq_min = (Lq / lam) * 60   # minutes
    L  = lam * (Wq_min/60 + 1/mu)
    return {"rho": rho, "Lq": Lq, "Wq_min": Wq_min, "L": L}

def total_cost(lam, mu, c, lane_cost_hr=200, wait_cost_min=0.50):
    """Total hourly cost = lane operating cost + passenger waiting cost."""
    m = mmc_metrics(lam, mu, c)
    if m is None:
        return None
    operating_cost = c * lane_cost_hr
    # Avg passengers waiting × cost per minute × 60 min/hr
    waiting_cost = m["Lq"] * wait_cost_min * 60
    return {
        "total": operating_cost + waiting_cost,
        "operating": operating_cost,
        "waiting": waiting_cost,
        "Wq_min": m["Wq_min"],
        "rho": m["rho"],
    }

lam_base = 120    # passengers/hr
lam_surge = 120 * 1.30  # +30% surge
mu = 15           # passengers/hr per lane

lanes = range(8, 25)

print(f"{'Lanes':>5} {'ρ':>6} {'Wq(min)':>9} {'OpCost':>8} {'WaitCost':>10} {'Total':>9}")
print("-" * 55)

costs_base  = []
costs_surge = []
valid_lanes = []

for c in lanes:
    r = total_cost(lam_base, mu, c)
    if r:
        costs_base.append(r["total"])
        valid_lanes.append(c)
        print(f"{c:>5} {r['rho']:>6.2f} {r['Wq_min']:>9.2f} "
              f"${r['operating']:>7.0f} ${r['waiting']:>9.0f} ${r['total']:>8.0f}")

print()
opt_idx_base = int(np.argmin(costs_base))
opt_c_base = valid_lanes[opt_idx_base]
print(f"Optimal lanes (base λ=120):  c = {opt_c_base}  "
      f"(total cost = ${costs_base[opt_idx_base]:,.0f}/hr)")

# Surge scenario
costs_surge = []
valid_surge = []
for c in lanes:
    r = total_cost(lam_surge, mu, c)
    if r:
        costs_surge.append(r["total"])
        valid_surge.append(c)

opt_idx_surge = int(np.argmin(costs_surge))
opt_c_surge = valid_surge[opt_idx_surge]
print(f"Optimal lanes (+30% surge λ={lam_surge:.0f}): c = {opt_c_surge}  "
      f"(total cost = ${costs_surge[opt_idx_surge]:,.0f}/hr)")

# ── Plot ──────────────────────────────────────────────────────────────────────
fig, ax = plt.subplots(figsize=(10, 5))
ax.plot(valid_lanes, costs_base, "o-", color="steelblue", linewidth=2,
        label=f"Base λ=120/hr  (optimum: c={opt_c_base})")
ax.plot(valid_surge, costs_surge, "s--", color="firebrick", linewidth=2,
        label=f"Surge λ={lam_surge:.0f}/hr  (optimum: c={opt_c_surge})")
ax.axvline(opt_c_base,  color="steelblue", linestyle=":", alpha=0.6)
ax.axvline(opt_c_surge, color="firebrick",  linestyle=":", alpha=0.6)

ax.set_xlabel("Number of Security Lanes (c)", fontsize=12)
ax.set_ylabel("Total Hourly Cost ($/hr)", fontsize=12)
ax.set_title("Airport Security: Cost Optimization\n"
             "(Lane cost + Passenger waiting cost)", fontsize=13)
ax.legend(fontsize=10)
ax.grid(True, alpha=0.3)
plt.tight_layout()
plt.savefig("airport_security_cost.png", dpi=150)
plt.show()
```

!!! info "Key Insight"
    The cost curve has a clear minimum — too few lanes creates expensive waiting lines, while too many lanes waste operating budget. A 30% surge in arrivals shifts the optimal lane count upward, illustrating why capacity planning must account for peak demand scenarios.

!!! check "Check Your Understanding — Part 2"
    1. In the field hospital example, why is the system with λ=18/hr and c=2 surgeons technically unstable? What is the utilization ρ, and what does it mean operationally?
    2. For the airport security model, if the waiting cost per passenger per minute doubled to $1.00/min, which direction would the optimal number of lanes shift? Why?
    3. Both examples use M/M/c. What real-world factors would make these models inaccurate, and how could you improve the model?

---

## Part 3 — Interactive Exercises

### Exercise 1 — Guided (Warm-Up): M/M/1 Metrics Calculator

!!! question "Task"
    Implement each M/M/1 formula by filling in the TODO blocks. Test with λ=8 arrivals/hr and μ=10 services/hr. Add a stability check that raises an error if ρ ≥ 1.

```python
def mm1_metrics(lam, mu):
    """
    Compute M/M/1 steady-state performance metrics.
    
    Parameters
    ----------
    lam : float   Arrival rate (customers per unit time)
    mu  : float   Service rate (customers per unit time)
    
    Returns
    -------
    dict with keys: rho, L, Lq, W_min, Wq_min
    """
    # TODO 1: Compute traffic intensity (utilization)
    # rho = ...
    
    # TODO 2: Check stability — raise ValueError if rho >= 1
    # if ...:
    #     raise ValueError(...)
    
    # TODO 3: Compute average number in system
    # L = ...
    
    # TODO 4: Compute average number in queue
    # Lq = ...
    
    # TODO 5: Compute average time in system (convert to minutes)
    # W_min = ...      # hint: use Little's Law or W = 1/(mu - lam), then * 60
    
    # TODO 6: Compute average wait in queue (convert to minutes)
    # Wq_min = ...
    
    # NOTE: all variables (rho, L, Lq, W_min, Wq_min) must be filled in above
    return {
        "rho": rho,
        "L": L, "Lq": Lq,
        "W_min": W_min, "Wq_min": Wq_min,
    }

# ── Test ─────────────────────────────────────────────────────────────────────
result = mm1_metrics(lam=8, mu=10)
print(f"Traffic intensity ρ   = {result['rho']:.2f}")
print(f"Avg in system     L   = {result['L']:.3f}")
print(f"Avg in queue      Lq  = {result['Lq']:.3f}")
print(f"Avg time in sys   W   = {result['W_min']:.2f} min")
print(f"Avg wait in queue Wq  = {result['Wq_min']:.2f} min")
# Expected output:
# ρ = 0.80, L = 4.000, Lq = 3.200, W = 30.00 min, Wq = 24.00 min
```

??? success "Solution"
    ```python
    def mm1_metrics(lam, mu):
        rho = lam / mu
        if rho >= 1:
            raise ValueError(f"System unstable: ρ = {rho:.3f} ≥ 1 (lam={lam}, mu={mu})")
        L    = rho / (1 - rho)
        Lq   = rho**2 / (1 - rho)
        W_min  = (1 / (mu - lam)) * 60
        Wq_min = (rho / (mu - lam)) * 60
        return {"rho": rho, "L": L, "Lq": Lq, "W_min": W_min, "Wq_min": Wq_min}
    ```

---

### Exercise 2 — Exploratory: Analytical vs. SimPy Simulation Validation

!!! question "Task"
    Use M/M/1 analytical formulas **and** a SimPy discrete-event simulation with the same parameters. Sweep λ from 0.1μ to 0.95μ in 10 steps. Plot analytical and simulated $W_q$ on the same chart. Determine the minimum number of events for results to converge within 5% of the analytical value.

```python
# pip install simpy numpy matplotlib
import simpy
import random
import numpy as np
import matplotlib.pyplot as plt

# ── Analytical M/M/1 Wq ──────────────────────────────────────────────────────
def mm1_wq(lam, mu):
    rho = lam / mu
    if rho >= 1:
        return np.inf
    return rho / (mu - lam)

# ── SimPy M/M/1 Simulation ───────────────────────────────────────────────────
def simulate_mm1(lam, mu, n_customers=5000, seed=42):
    """
    Simulate M/M/1 queue and return mean waiting time in queue (Wq).
    """
    random.seed(seed)
    env = simpy.Environment()
    server = simpy.Resource(env, capacity=1)
    wait_times = []

    def customer(env):
        arrival = env.now
        with server.request() as req:
            yield req
            wait = env.now - arrival
            wait_times.append(wait)
            yield env.timeout(random.expovariate(mu))

    def generator(env):
        for _ in range(n_customers):
            env.process(customer(env))
            yield env.timeout(random.expovariate(lam))

    env.process(generator(env))
    env.run()
    return np.mean(wait_times)

# ── Sweep λ from 0.1μ to 0.95μ ───────────────────────────────────────────────
mu = 1.0
rho_targets = np.linspace(0.1, 0.95, 10)
lam_vals = rho_targets * mu

print(f"{'λ':>6} {'ρ':>6} {'Wq Analytical':>15} {'Wq Simulated':>14} {'Error %':>9}")
print("-" * 60)

wq_analytical = []
wq_simulated  = []

for lam in lam_vals:
    wq_an  = mm1_wq(lam, mu)
    wq_sim = simulate_mm1(lam, mu, n_customers=5000)
    err    = abs(wq_sim - wq_an) / wq_an * 100
    wq_analytical.append(wq_an)
    wq_simulated.append(wq_sim)
    print(f"{lam:6.3f} {lam/mu:6.2f} {wq_an:>15.4f} {wq_sim:>14.4f} {err:>8.1f}%")

# ── Plot ──────────────────────────────────────────────────────────────────────
plt.figure(figsize=(9, 5))
plt.plot(rho_targets, wq_analytical, "o-", color="steelblue",
         linewidth=2, label="Analytical $W_q$")
plt.plot(rho_targets, wq_simulated, "s--", color="firebrick",
         linewidth=2, label="SimPy simulated $W_q$")
plt.xlabel("Traffic Intensity ρ = λ/μ", fontsize=12)
plt.ylabel("Mean Wait in Queue $W_q$ (service times)", fontsize=12)
plt.title("M/M/1: Analytical vs. SimPy Simulation\n"
          "(n = 5,000 customers per run)", fontsize=13)
plt.legend(fontsize=10)
plt.grid(True, alpha=0.3)
plt.tight_layout()
plt.savefig("mm1_analytical_vs_sim.png", dpi=150)
plt.show()

# ── Convergence study at ρ = 0.8 ─────────────────────────────────────────────
print("\nConvergence Study at ρ = 0.8")
print(f"{'N customers':>12} {'Wq Sim':>10} {'Error %':>9}")
print("-" * 35)
lam_conv = 0.8
wq_true  = mm1_wq(lam_conv, mu)
for n in [100, 500, 1000, 2000, 5000, 10000, 20000]:
    wq_s = simulate_mm1(lam_conv, mu, n_customers=n, seed=123)
    err  = abs(wq_s - wq_true) / wq_true * 100
    print(f"{n:>12,} {wq_s:>10.4f} {err:>8.1f}%")
```

---

### Exercise 3 — Challenge: Multi-Class Priority MEDEVAC Queue

!!! question "Task"
    Model a MEDEVAC helicopter system with **3 triage priority classes** (T1 urgent, T2 priority, T3 routine). Single helicopter available. Non-preemptive priority discipline.
    
    - Arrival rates: T1 = 2/hr, T2 = 5/hr, T3 = 10/hr
    - Service rate: μ = 20/hr (avg 3 min per patient — note: 30-min service with these arrival rates gives ρ=8.5, which is unstable; we use 3-min service to illustrate a realistic stable scenario)
    
    Implement analytical multi-class wait time formulas, validate with SimPy, and plot wait time CDFs per class.

```python
# pip install simpy numpy matplotlib
import simpy
import random
import numpy as np
import matplotlib.pyplot as plt
from math import factorial

# ── Analytical: Multi-Class Non-Preemptive Priority Queue ────────────────────
def multiclass_priority_wq(lam_list, mu):
    """
    Non-preemptive priority M/M/1 with K priority classes.
    Class 1 = highest priority, class K = lowest.

    Mean Wq for class k:
        Wq_k = W0 / [(1 - σ_{k-1})(1 - σ_k)]
    where σ_k = Σ_{i=1}^{k} ρ_i  and  W0 = Σ_i ρ_i / μ
    """
    K    = len(lam_list)
    rho  = [l / mu for l in lam_list]
    rho_total = sum(rho)
    if rho_total >= 1:
        raise ValueError(f"System unstable: ρ_total = {rho_total:.3f}")

    W0 = sum(r / mu for r in rho)       # mean residual service time

    # σ_0 = 0, σ_k = ρ_1 + ... + ρ_k
    sigma = [0.0] + [sum(rho[:k+1]) for k in range(K)]

    Wq = []
    for k in range(K):
        denom = (1 - sigma[k]) * (1 - sigma[k + 1])
        Wq.append(W0 / denom)

    return Wq, rho, rho_total, W0

lam_list = [2.0, 5.0, 10.0]    # T1, T2, T3 arrival rates (per hr)
mu       = 20.0                  # service rate (per hr) — avg 3 min per patient

Wq_an, rho, rho_total, W0 = multiclass_priority_wq(lam_list, mu)

print("MEDEVAC Priority Queue — Analytical Results")
print(f"Total utilization ρ = {rho_total:.2f}")
print()
labels = ["T1 (Urgent)", "T2 (Priority)", "T3 (Routine)"]
for i, (label, wq) in enumerate(zip(labels, Wq_an)):
    print(f"  {label}: ρ={rho[i]:.2f}, Wq = {wq*60:.2f} min ({wq:.4f} hr)")

# ── SimPy Simulation ─────────────────────────────────────────────────────────
def simulate_priority_queue(lam_list, mu, n_total=20000, seed=42):
    """
    Non-preemptive priority M/M/1 simulation using SimPy PriorityResource.
    Priority class 1 = highest (lowest SimPy priority number).
    """
    random.seed(seed)
    env      = simpy.Environment()
    server   = simpy.PriorityResource(env, capacity=1)
    wait_by_class = [[] for _ in range(len(lam_list))]

    def patient(env, priority_class):
        arrival = env.now
        with server.request(priority=priority_class) as req:
            yield req
            wait = env.now - arrival
            wait_by_class[priority_class].append(wait)
            yield env.timeout(random.expovariate(mu))

    def arrival_stream(env, priority_class, lam, n):
        for _ in range(n):
            env.process(patient(env, priority_class))
            yield env.timeout(random.expovariate(lam))

    # Generate arrivals proportional to arrival rates
    total_lam = sum(lam_list)
    for cls, lam in enumerate(lam_list):
        n_cls = int(n_total * lam / total_lam)
        env.process(arrival_stream(env, cls, lam, n_cls))

    env.run()
    return wait_by_class

print("\nRunning SimPy simulation (n=20,000 patients)...")
wait_by_class = simulate_priority_queue(lam_list, mu, n_total=20000)

print("\nComparison: Analytical vs. Simulated Mean Wq")
print(f"{'Class':<16} {'Analytical':>12} {'Simulated':>12} {'Error %':>9}")
print("-" * 52)
for i, label in enumerate(labels):
    an  = Wq_an[i] * 60       # minutes
    sim = np.mean(wait_by_class[i]) * 60
    err = abs(sim - an) / an * 100
    print(f"  {label:<14} {an:>12.2f} {sim:>12.2f} {err:>8.1f}%")

# ── Wait time CDF per class ───────────────────────────────────────────────────
fig, ax = plt.subplots(figsize=(10, 5))
colors = ["firebrick", "darkorange", "steelblue"]

for i, (label, color) in enumerate(zip(labels, colors)):
    data = np.array(wait_by_class[i]) * 60   # convert to minutes
    data_sorted = np.sort(data)
    cdf = np.arange(1, len(data_sorted) + 1) / len(data_sorted)
    ax.plot(data_sorted, cdf, color=color, linewidth=2, label=label)

ax.set_xlabel("Wait Time in Queue (minutes)", fontsize=12)
ax.set_ylabel("CDF  P(Wq ≤ t)", fontsize=12)
ax.set_title("MEDEVAC Priority Queue: Wait Time CDF by Triage Class\n"
             "(Non-Preemptive, SimPy Simulation, n=20,000)", fontsize=13)
ax.legend(fontsize=10)
ax.set_xlim(0, 300)
ax.grid(True, alpha=0.3)
plt.tight_layout()
plt.savefig("medevac_priority_cdf.png", dpi=150)
plt.show()

# ── Bonus: How many T3 patients waiting when a T1 arrives? ───────────────────
# In steady state, Lq for T3 = λ3 * Wq3
Lq_T3 = lam_list[2] * Wq_an[2]
print(f"\nBonus — T3 patients waiting on average: {Lq_T3:.2f}")
print(f"(This is Lq_T3 = λ_T3 × Wq_T3 = {lam_list[2]} × {Wq_an[2]*60:.2f} min)")
```

!!! check "Check Your Understanding — Part 3"
    1. In Exercise 1, what would happen if you called `mm1_metrics(lam=10, mu=10)`? What real-world condition does this represent?
    2. In Exercise 2, why does the simulated $W_q$ systematically *underestimate* the analytical value at high ρ (say ρ = 0.95)? What simulation design choice causes this?
    3. In Exercise 3, the T3 (Routine) mean wait time is dramatically higher than T1. What policy change could reduce T3 waiting without changing the helicopter capacity, and what trade-off would it introduce?

---

## Summary

| Concept | Key Formula | When to Use |
|---------|------------|-------------|
| M/M/1 | $L = \rho/(1-\rho)$, $W = 1/(\mu-\lambda)$ | Single server, Poisson/exponential |
| M/M/c | Erlang-C formula for $P(\text{wait})$ | Multi-server, same distributions |
| Little's Law | $L = \lambda W$ | Any stable queue, distribution-free |
| Non-preemptive priority | $W_{q,k} = W_0 / [(1-\sigma_{k-1})(1-\sigma_k)]$ | Priority scheduling, no interruption |
| Jackson network | Product-form: $P(\mathbf{n}) = \prod_i P_i(n_i)$ | Networks of M/M/c queues |

---

!!! tip "Prompt Template"
    Use the [Agent Prompt](prompt.md) for this topic, or the [Generic Prompt Template](../prompt-template.md) to generate additional lectures, examples, and exercises.
