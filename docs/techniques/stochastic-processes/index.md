# Stochastic Processes

> Modeling systems that evolve probabilistically over time.

---

## Part 1 — Lecture

### 1. What is a Stochastic Process?

A **stochastic process** is a collection of random variables $\{X(t) : t \in T\}$ indexed by a parameter $t$ (usually time). Each realization of the process over time is called a **sample path**. They model systems where outcomes evolve randomly — equipment degradation, force attrition, demand fluctuations, customer behavior, and epidemic spread.

#### State Space Taxonomy

The state space $S$ (the set of values $X(t)$ can take) and the index set $T$ together define four canonical types:

| Index Set $T$ | State Space $S$ | Process Type | Example |
|--------------|----------------|--------------|---------|
| Discrete | Discrete/Finite | Discrete-Time Markov Chain | Daily equipment status |
| Discrete | Continuous | Time series, AR processes | Daily temperature |
| Continuous | Discrete/Finite | CTMC, birth-death | Call center queues |
| Continuous | Continuous | Brownian motion, diffusion | Asset prices |

#### Historical Context

Andrey Markov introduced Markov chains in 1906. The mathematical framework was systematized through the 20th century, with major contributions from Kolmogorov (differential equations for CTMCs, 1930s), Feller (probability theory), and Bellman (dynamic programming/MDPs, 1957).

#### Relationship to Other OR Techniques

- Foundation for **Queueing Theory** (birth-death processes are a special CTMC)
- MDPs are the mathematical basis for **Reinforcement Learning**
- Drives **Simulation** models (generating random trajectories)
- Informs **Decision Analysis** (sequential decisions under uncertainty)

---

!!! question "Check Your Understanding — Stochastic Processes"
    1. A system records whether a soldier is "fit for duty," "injured," or "recovered" each day. What type of stochastic process is this, and why?
    2. Explain the difference between a discrete-state and a continuous-state stochastic process. Give one military and one civilian example of each.
    3. Why is the concept of a *sample path* useful when thinking about stochastic processes?

---

### 2. The Markov Property

A stochastic process satisfies the **Markov property** if the future depends only on the present state, not on the history of how the process arrived there:

$$
P(X_{n+1} = j \mid X_n = i, X_{n-1} = i_{n-1}, \ldots, X_0 = i_0) = P(X_{n+1} = j \mid X_n = i)
$$

This is called **memorylessness**: the current state is a sufficient statistic for the future.

#### When Is the Markov Assumption Reasonable?

- **Reasonable**: Equipment status depends primarily on *current condition*, not full maintenance history; queue length at a server depends only on *current* number of customers.
- **Less reasonable**: Customer churn where historical engagement matters; medical prognosis where past treatment history is critical.

!!! tip "Practical Guidance"
    Even when the strict Markov property doesn't hold, Markov models often provide useful approximations by defining states broadly enough to capture relevant history (e.g., augmenting the state with recent history).

---

!!! question "Check Your Understanding — The Markov Property"
    1. A logistics system tracks whether a supply route is "open," "degraded," or "closed." If route status tomorrow depends only on today's status, what property does this satisfy? Formally write down the conditional probability equation.
    2. Give a real-world scenario where the Markov property is clearly *violated*, and suggest how you might modify the state space to recover it.
    3. The Markov property is sometimes called "memorylessness." Why is this a potentially misleading label for high-dimensional state spaces?

---

### 3. Discrete-Time Markov Chains (DTMC)

A **Discrete-Time Markov Chain (DTMC)** is a stochastic process $\{X_n : n = 0, 1, 2, \ldots\}$ on a finite or countable state space $S$ satisfying the Markov property. The dynamics are completely described by the **transition matrix** $\mathbf{P}$.

#### Transition Matrix

$$
P_{ij} = P(X_{n+1} = j \mid X_n = i), \quad \sum_{j \in S} P_{ij} = 1 \text{ for all } i
$$

$\mathbf{P}$ is a **row-stochastic matrix**: every row sums to 1.

#### Chapman-Kolmogorov Equations

The $n$-step transition probability is:

$$
P_{ij}^{(n)} = P(X_{n} = j \mid X_0 = i) = [\mathbf{P}^n]_{ij}
$$

That is, simply raise the transition matrix to the $n$-th power.

#### State Classification

| Classification | Definition |
|---------------|-----------|
| **Recurrent** | Starting from $i$, the chain returns to $i$ with probability 1 |
| **Transient** | Starting from $i$, the chain may never return (probability < 1) |
| **Absorbing** | $P_{ii} = 1$; once entered, the state is never left |
| **Communicating** | States $i$ and $j$ communicate if both are reachable from each other |
| **Irreducible** | All states communicate (one equivalence class) |
| **Aperiodic** | The GCD of return times to any state is 1 |

#### Steady-State Distribution

For an **irreducible** and **aperiodic** chain, a unique steady-state distribution $\boldsymbol{\pi}$ exists satisfying:

$$
\boldsymbol{\pi} \mathbf{P} = \boldsymbol{\pi}, \quad \sum_{i} \pi_i = 1, \quad \pi_i \geq 0
$$

This is solved as a linear system. Replace one equation with the normalization constraint:

$$
(\mathbf{P}^\top - \mathbf{I})\boldsymbol{\pi}^\top = \mathbf{0}
$$

#### First Passage Times

The **expected first passage time** $m_{ij}$ from state $i$ to state $j$ satisfies:

$$
m_{ij} = 1 + \sum_{k \neq j} P_{ik} \, m_{kj}
$$

For recurrent states, $m_{ii} = 1/\pi_i$ (mean recurrence time).

#### Absorbing Chains and the Fundamental Matrix

For an absorbing chain with transient states $T$ and absorbing states $A$, partition:

$$
\mathbf{P} = \begin{pmatrix} \mathbf{Q} & \mathbf{R} \\ \mathbf{0} & \mathbf{I} \end{pmatrix}
$$

The **fundamental matrix** $\mathbf{N} = (\mathbf{I} - \mathbf{Q})^{-1}$ gives:

- $N_{ij}$: expected number of times in transient state $j$ before absorption, starting from $i$
- $\mathbf{t} = \mathbf{N}\mathbf{1}$: expected steps before absorption
- $\mathbf{B} = \mathbf{N}\mathbf{R}$: absorption probabilities ($B_{ij}$ = probability of being absorbed into state $j$ starting from $i$)

---

!!! question "Check Your Understanding — DTMC"
    1. A 3-state chain has transition matrix $\mathbf{P}$ where $P_{11}=1$. What type of state is state 1? How does this affect the steady-state distribution?
    2. An equipment item transitions: Operational→Degraded with probability 0.1 per day; Degraded→Failed with 0.3; Failed is absorbing. Write the fundamental matrix $\mathbf{N}$ and compute the expected days to failure starting from Operational.
    3. Why do both irreducibility *and* aperiodicity need to hold for a unique steady-state distribution to exist? Give a counterexample for each condition alone.

---

### 4. Continuous-Time Markov Chains (CTMC)

In a **Continuous-Time Markov Chain (CTMC)**, the process $\{X(t) : t \geq 0\}$ can transition at any moment. The key is that holding times in each state are **exponentially distributed** (the only memoryless continuous distribution).

#### Generator (Rate) Matrix

The **generator matrix** $\mathbf{Q}$ (not to be confused with the transient sub-matrix $Q$ in absorbing chains) has entries:

$$
Q_{ij} = \begin{cases} q_{ij} \geq 0 & i \neq j \quad \text{(transition rate from } i \text{ to } j\text{)} \\ -\sum_{j \neq i} q_{ij} & i = j \quad \text{(total departure rate from } i\text{)} \end{cases}
$$

Every row of $\mathbf{Q}$ sums to **zero**.

#### Kolmogorov Equations

Let $p_{ij}(t) = P(X(t) = j \mid X(0) = i)$. The **forward equations** are:

$$
\frac{d}{dt}\mathbf{P}(t) = \mathbf{P}(t)\mathbf{Q}
$$

with solution $\mathbf{P}(t) = e^{\mathbf{Q}t}$ (matrix exponential).

#### Steady-State Balance Equations

The steady-state distribution $\boldsymbol{\pi}$ satisfies:

$$
\boldsymbol{\pi} \mathbf{Q} = \mathbf{0}, \quad \sum_i \pi_i = 1
$$

This is equivalent to **global balance**: flow into each state equals flow out.

#### Connection to Queueing Theory

The M/M/1 queue is a birth-death CTMC on states $\{0, 1, 2, \ldots\}$ with:

- Birth rate $\lambda$ (arrival rate), Death rate $\mu$ (service rate)
- Generator: $Q_{i,i+1} = \lambda$, $Q_{i,i-1} = \mu$, $Q_{ii} = -(\lambda + \mu)$
- Steady-state: $\pi_n = (1 - \rho)\rho^n$, where $\rho = \lambda/\mu < 1$

---

!!! question "Check Your Understanding — CTMC"
    1. Why must all rows of the generator matrix $\mathbf{Q}$ sum to zero? What is the probabilistic interpretation?
    2. A CTMC has states {Operational, Degraded, Failed} with rates: Operational→Degraded at rate 0.05/day, Degraded→Failed at rate 0.2/day, Failed→Operational (repair) at rate 0.5/day. Write the generator matrix $\mathbf{Q}$ and set up the steady-state equations.
    3. Explain the connection between the exponential distribution and the Markov property in continuous time. Why can't holding times follow a normal or uniform distribution in a CTMC?

---

### 5. Poisson Processes

A **Poisson process** $\{N(t) : t \geq 0\}$ with rate $\lambda > 0$ counts the number of events (arrivals, failures, attacks) by time $t$. It is a CTMC on $\{0, 1, 2, \ldots\}$ with only births (rate $\lambda$, no deaths).

#### Key Properties

$$
P(N(t) = k) = \frac{(\lambda t)^k e^{-\lambda t}}{k!}, \quad k = 0, 1, 2, \ldots
$$

- **Inter-arrival times** $T_i = t_i - t_{i-1}$ are i.i.d. $\text{Exponential}(\lambda)$: $f(t) = \lambda e^{-\lambda t}$
- **Independent increments**: $N(t+s) - N(t) \perp N(t)$
- **Stationary increments**: distribution of $N(t+s) - N(t)$ depends only on $s$

#### Superposition

If $N_1(t), \ldots, N_k(t)$ are independent Poisson processes with rates $\lambda_1, \ldots, \lambda_k$, their sum is Poisson with rate $\lambda_1 + \cdots + \lambda_k$:

$$
N_1(t) + \cdots + N_k(t) \sim \text{Poisson}((\lambda_1 + \cdots + \lambda_k)\,t)
$$

#### Thinning

If each event is independently classified as "Type A" with probability $p$, the Type A events form a Poisson process with rate $p\lambda$. This is called **Bernoulli thinning**.

#### Compound Poisson Process

A compound Poisson process adds random jump sizes $Y_i$ (i.i.d.) at each event:

$$
S(t) = \sum_{i=1}^{N(t)} Y_i
$$

Used to model cumulative damage, insurance claims, and supply consumption.

---

!!! question "Check Your Understanding — Poisson Processes"
    1. Enemy attacks arrive at a forward operating base as a Poisson process with rate $\lambda = 3$/day. What is the probability of exactly 2 attacks in a given day? What is the expected time between attacks?
    2. Three independent Poisson streams of supply requests arrive with rates 2/hr, 5/hr, and 3/hr. A random 40% of requests are classified as "urgent." What is the rate of urgent requests?
    3. A compound Poisson process models equipment failures where failures arrive at rate $\lambda = 0.5$/week and each failure costs an i.i.d. Uniform(1000, 5000) amount. What is the expected total cost over 4 weeks?

---

### 6. Markov Decision Processes (MDP)

A **Markov Decision Process** extends a Markov chain by introducing a **decision-maker** (agent) who selects actions to maximize cumulative rewards. An MDP is defined by the tuple $(S, A, P, R, \gamma)$:

| Component | Symbol | Description |
|-----------|--------|-------------|
| States | $S$ | All possible situations the system can be in |
| Actions | $A(s)$ | Actions available in state $s$ |
| Transition probabilities | $P(s' \mid s, a)$ | Probability of moving to $s'$ from $s$ under action $a$ |
| Reward function | $R(s, a)$ | Immediate reward for taking action $a$ in state $s$ |
| Discount factor | $\gamma \in [0,1)$ | Weights future rewards relative to immediate ones |

#### Bellman Optimality Equations

The **optimal value function** $V^*(s)$ satisfies:

$$
V^*(s) = \max_{a \in A(s)} \left[ R(s, a) + \gamma \sum_{s' \in S} P(s' \mid s, a)\, V^*(s') \right]
$$

The **optimal policy** $\pi^*(s) = \arg\max_a \left[ R(s,a) + \gamma \sum_{s'} P(s'\mid s,a) V^*(s') \right]$

#### Value Iteration

Start with $V_0(s) = 0$ for all $s$. Iterate until convergence:

$$
V_{k+1}(s) = \max_{a} \left[ R(s, a) + \gamma \sum_{s'} P(s' \mid s, a)\, V_k(s') \right]
$$

Convergence is guaranteed: $\|V_{k+1} - V^*\|_\infty \leq \gamma^k \|V_0 - V^*\|_\infty$.

#### Policy Iteration

1. **Policy Evaluation**: Given policy $\pi$, solve the linear system $V^\pi(s) = R(s, \pi(s)) + \gamma \sum_{s'} P(s' \mid s, \pi(s)) V^\pi(s')$
2. **Policy Improvement**: $\pi'(s) = \arg\max_a [R(s,a) + \gamma \sum_{s'} P(s'|s,a) V^\pi(s')]$
3. Repeat until policy does not change. Converges in finite steps (finite policy space).

#### Connection to Reinforcement Learning

When $P(s'|s,a)$ and $R(s,a)$ are **unknown**, the agent must learn from interactions. **Q-learning**, **SARSA**, and **policy gradient** methods are model-free RL algorithms that solve MDPs without a known model — they estimate the optimal value function through experience.

---

!!! question "Check Your Understanding — MDP"
    1. A maintenance officer chooses daily whether to perform "light," "medium," or "heavy" maintenance on a fleet. Model this as an MDP: define $S$, $A$, and explain what $P(s'|s,a)$ and $R(s,a)$ would represent.
    2. Explain the difference between Value Iteration and Policy Iteration. Which would you prefer when the state space is large? Why?
    3. If the discount factor $\gamma = 0$, what policy will Value Iteration find? What if $\gamma \to 1$? What does this mean practically for a military commander making decisions?

---

### 7. Hidden Markov Models (HMM)

In many real-world applications, the true state of the system is **not directly observable**. A **Hidden Markov Model** separates:

- **Hidden states** $\{X_n\}$: a Markov chain (e.g., whether a target is moving, stationary, or retreating)
- **Observations** $\{Y_n\}$: noisy emissions from the hidden state (e.g., sensor readings)

#### HMM Parameters

| Parameter | Symbol | Description |
|-----------|--------|-------------|
| Initial distribution | $\boldsymbol{\pi}$ | $P(X_1 = i)$ |
| Transition matrix | $\mathbf{A}$ | $A_{ij} = P(X_{n+1}=j \mid X_n = i)$ |
| Emission matrix | $\mathbf{B}$ | $B_{ij} = P(Y_n=j \mid X_n = i)$ |

#### The Viterbi Algorithm

Given observations $y_1, y_2, \ldots, y_T$, the **Viterbi algorithm** finds the most likely hidden state sequence via dynamic programming:

$$
\delta_t(i) = \max_{x_1,\ldots,x_{t-1}} P(x_1,\ldots,x_{t-1}, X_t=i, y_1,\ldots,y_t)
$$

Recursion: $\delta_{t+1}(j) = \left(\max_i \delta_t(i) \cdot A_{ij}\right) \cdot B_{j,y_{t+1}}$

Back-track through the $\psi$ (argmax) pointers to recover the optimal sequence.

#### Military Relevance: Target State Estimation

HMMs are used in **target tracking** and **situational awareness**:

- **Hidden states**: target tactical state (maneuvering, stationary, fleeing, engaging)
- **Observations**: sensor readings (radar cross-section, speed, direction changes), which are noisy
- **Goal**: infer the most likely sequence of target states given a stream of sensor observations

This enables prediction of target behavior and supports engagement decisions.

---

!!! question "Check Your Understanding — HMM"
    1. In a military HMM for target tracking, the hidden states are {Stationary, Maneuvering, Retreating}. An observation of "high speed + direction change" is most likely emitted from which state? How does the Viterbi algorithm use this emission probability?
    2. An HMM has transition matrix $A$ with $A_{11}=0.9$ (state 1 is very "sticky"). How does this affect the Viterbi decoding of a sequence of observations? What is the practical implication for tracking a target?
    3. Compare a DTMC and an HMM: what is the key structural difference, and why does the hidden state assumption make inference more challenging?

---

## Part 2 — Real-World Examples

### Example A — Military: Fleet Readiness Markov Chain & Maintenance Policy MDP

!!! example "Scenario"
    A battalion operates a fleet of 50 combat vehicles. Each vehicle is in one of four daily states: **FMC** (Fully Mission Capable), **PMC** (Partially Mission Capable), **NMC** (Not Mission Capable), or **UM** (Under Maintenance). We first model this as a DTMC to analyze steady-state readiness, then formulate an MDP to find the optimal maintenance dispatch policy.

**Open-Source Tools:** NumPy, SciPy, matplotlib, Python

#### Part A1 — DTMC: Steady-State Readiness & 365-Day Simulation

```python
# pip install numpy scipy matplotlib
import numpy as np
import matplotlib.pyplot as plt

np.random.seed(42)

# ── States ────────────────────────────────────────────────────────────────────
STATES = ["FMC", "PMC", "NMC", "UM"]
n_states = len(STATES)
FLEET_SIZE = 50

# ── Transition matrix (daily per-vehicle probabilities) ───────────────────────
# Rows: current state; Columns: next state (FMC, PMC, NMC, UM)
P = np.array([
    [0.80, 0.12, 0.03, 0.05],  # FMC  → stays FMC most of the time
    [0.25, 0.55, 0.10, 0.10],  # PMC  → repair or degrade
    [0.05, 0.20, 0.50, 0.25],  # NMC  → sent to maintenance or stays NMC
    [0.60, 0.30, 0.05, 0.05],  # UM   → mostly returns as FMC or PMC
])
assert np.allclose(P.sum(axis=1), 1.0), "Transition matrix rows must sum to 1"

# ── Analytical steady-state: solve πP = π, Σπ = 1 ────────────────────────────
A = (P.T - np.eye(n_states)).copy()
A[-1] = 1.0           # replace last equation with normalization
b = np.zeros(n_states)
b[-1] = 1.0
pi = np.linalg.solve(A, b)

print("Fleet Readiness Markov Chain — Analytical Steady-State")
print("=" * 55)
for s, p in zip(STATES, pi):
    print(f"  {s:4s}: {p:.1%}  ({p * FLEET_SIZE:.1f} vehicles of {FLEET_SIZE})")

# Expected days to recover from 100% NMC (first passage time to ≥80% FMC)
# — approximated as mean time to reach FMC-dominated distribution
print(f"\nExpected steady-state FMC: {pi[0] * FLEET_SIZE:.1f} vehicles")

# ── 365-day Monte Carlo simulation ────────────────────────────────────────────
n_days = 365
n_sims = 200

fmc_history = np.zeros((n_sims, n_days))

for sim in range(n_sims):
    # Start from 100% NMC (worst case)
    vehicle_states = np.full(FLEET_SIZE, 2, dtype=int)  # 2 = NMC
    for day in range(n_days):
        # Transition each vehicle independently
        new_states = np.array([
            np.random.choice(n_states, p=P[s]) for s in vehicle_states
        ])
        vehicle_states = new_states
        fmc_history[sim, day] = np.sum(vehicle_states == 0) / FLEET_SIZE

# Summary statistics across simulations
mean_fmc = fmc_history.mean(axis=0)
std_fmc  = fmc_history.std(axis=0)

# ── Plot ──────────────────────────────────────────────────────────────────────
fig, ax = plt.subplots(figsize=(12, 5))
days = np.arange(1, n_days + 1)

ax.fill_between(days, mean_fmc - std_fmc, mean_fmc + std_fmc,
                alpha=0.3, color="steelblue", label="±1 Std Dev")
ax.plot(days, mean_fmc, color="steelblue", lw=2, label="Mean FMC %")
ax.axhline(pi[0], color="crimson", ls="--", lw=2,
           label=f"Analytical Steady-State ({pi[0]:.1%})")

ax.set_xlabel("Day")
ax.set_ylabel("FMC Fraction")
ax.set_title("Fleet Readiness: 365-Day Simulation (200 replicates, start 100% NMC)")
ax.legend()
ax.set_ylim(0, 1)
ax.yaxis.set_major_formatter(plt.FuncFormatter(lambda y, _: f"{y:.0%}"))
plt.tight_layout()
plt.savefig("fleet_readiness_simulation.png", dpi=150)
plt.show()

print(f"\nDay-365 simulated FMC: {mean_fmc[-1]:.1%} ± {std_fmc[-1]:.1%}")
print(f"Analytical steady-state FMC: {pi[0]:.1%}")
```

#### Part A2 — MDP: Optimal Maintenance Dispatch Policy (Value Iteration)

```python
# Simplified MDP state: fmc_count (0..FLEET_SIZE) — the number of FMC vehicles.
# Non-FMC vehicles (FLEET_SIZE - fmc_count) are treated as a single "repairable" pool.
# Actions: fraction of non-FMC vehicles to dispatch to full maintenance (0 to 100%).
# Reward: expected FMC count tomorrow minus maintenance cost.
# Discount: γ = 0.95 (daily, ~20-day effective horizon)

import numpy as np
import matplotlib.pyplot as plt

np.random.seed(0)
FLEET_SIZE = 50
GAMMA = 0.95
MAINT_COST = 0.5        # reward penalty per vehicle sent to maintenance
EPSILON = 1e-6

# For tractability the MDP uses a 1-D state: fmc_count (0..FLEET_SIZE).
# All non-FMC vehicles (PMC + NMC) are aggregated into a single repairable pool.

states = list(range(FLEET_SIZE + 1))   # 0..50 = current FMC count
n_s = len(states)

# Transition probabilities: if we have f FMC vehicles (rest are PMC/NMC),
# tomorrow's FMC depends on degradation of current FMC and repair of others.
# Action a = fraction of non-FMC sent to UM (0.0, 0.2, 0.4, 0.6, 0.8, 1.0)
ACTIONS = [0.0, 0.2, 0.4, 0.6, 0.8, 1.0]   # dispatch fractions
n_a = len(ACTIONS)

def build_transitions():
    """P[s, a, s'] = probability of s' given state s and action a."""
    p_fmc_stays  = 0.80   # FMC → FMC (stays operational)
    p_um_to_fmc  = 0.70   # UM → FMC (repaired overnight)
    p_pmc_to_fmc = 0.25   # PMC → FMC (partial repair without dispatching)

    T = np.zeros((n_s, n_a, n_s))
    for si, f in enumerate(states):
        non_fmc = FLEET_SIZE - f
        for ai, a in enumerate(ACTIONS):
            n_um = round(non_fmc * a)
            n_pmc = non_fmc - n_um

            # Expected FMC tomorrow (mean of binomial sums):
            # - f FMC vehicles: each stays FMC with p_fmc_stays
            # - n_um UM vehicles: each returns as FMC with p_um_to_fmc
            # - n_pmc PMC vehicles: each becomes FMC with p_pmc_to_fmc
            mu = (f * p_fmc_stays
                  + n_um  * p_um_to_fmc
                  + n_pmc * p_pmc_to_fmc)
            # Approximate next-state distribution as Gaussian, clip to [0, FLEET_SIZE]
            var = (f * p_fmc_stays * (1 - p_fmc_stays)
                   + n_um  * p_um_to_fmc  * (1 - p_um_to_fmc)
                   + n_pmc * p_pmc_to_fmc * (1 - p_pmc_to_fmc))
            sigma = max(var**0.5, 0.5)

            # Build a probability vector over next states
            probs = np.zeros(n_s)
            for sj in range(n_s):
                from scipy.stats import norm
                probs[sj] = norm.cdf(sj + 0.5, mu, sigma) - norm.cdf(sj - 0.5, mu, sigma)
            probs = np.clip(probs, 0, None)
            probs /= probs.sum()
            T[si, ai, :] = probs
    return T

T = build_transitions()

def reward(s, a_idx):
    """Reward = FMC count tomorrow — maintenance cost."""
    f = states[s]
    non_fmc = FLEET_SIZE - f
    n_um = round(non_fmc * ACTIONS[a_idx])
    # Expected FMC tomorrow
    exp_fmc_tomorrow = np.sum(T[s, a_idx, :] * np.array(states))
    return exp_fmc_tomorrow - MAINT_COST * n_um

# ── Value Iteration ────────────────────────────────────────────────────────────
V = np.zeros(n_s)
policy = np.zeros(n_s, dtype=int)

for iteration in range(1000):
    V_old = V.copy()
    for si in range(n_s):
        q_vals = np.array([
            reward(si, ai) + GAMMA * T[si, ai, :] @ V
            for ai in range(n_a)
        ])
        policy[si] = np.argmax(q_vals)
        V[si] = q_vals[policy[si]]
    if np.max(np.abs(V - V_old)) < EPSILON:
        print(f"Value Iteration converged in {iteration + 1} iterations.")
        break

# ── Display optimal policy ─────────────────────────────────────────────────────
print("\nOptimal Maintenance Dispatch Policy")
print(f"{'FMC Vehicles':>14} {'Dispatch Fraction':>18} {'Action':>8}")
print("-" * 44)
for si in range(0, n_s, 5):
    a_str = f"{ACTIONS[policy[si]]:.0%}"
    print(f"{states[si]:>14}  {ACTIONS[policy[si]]:>17.0%}  {a_str:>8}")

# ── Decision heatmap ──────────────────────────────────────────────────────────
fig, ax = plt.subplots(figsize=(10, 4))
cmap = plt.colormaps.get_cmap("YlOrRd").resampled(n_a)
scatter = ax.scatter(
    states, [1] * n_s,
    c=[policy[si] for si in range(n_s)],
    cmap=cmap, s=200, vmin=0, vmax=n_a - 1, zorder=3
)
cbar = plt.colorbar(scatter, ax=ax, ticks=range(n_a))
cbar.set_label("Dispatch Fraction Index")
cbar.ax.set_yticklabels([f"{a:.0%}" for a in ACTIONS])
ax.set_xlabel("Current FMC Vehicle Count")
ax.set_title("Optimal Maintenance Policy: Dispatch Fraction vs. Fleet State")
ax.set_yticks([])
ax.set_xlim(-1, FLEET_SIZE + 1)
plt.tight_layout()
plt.savefig("maintenance_policy_heatmap.png", dpi=150)
plt.show()
```

---

### Example B — Civilian: Customer Lifecycle Churn MDP

!!! example "Scenario"
    A subscription company manages customers across three states: **Active** (engaged), **At-Risk** (declining engagement), and **Churned** (cancelled). Each month the company may apply an intervention: *No Action* (free), *Email Campaign* (low cost), *Discount Offer* (medium cost), or *Personal Call* (high cost). Formulate as an MDP to minimize long-run churn.

**Open-Source Tools:** NumPy, matplotlib, Python

```python
# pip install numpy matplotlib
import numpy as np
import matplotlib.pyplot as plt

np.random.seed(0)

# ── MDP Definition ─────────────────────────────────────────────────────────────
STATES  = ["Active", "At-Risk", "Churned"]
ACTIONS = ["None", "Email", "Discount", "Call"]
n_s, n_a = len(STATES), len(ACTIONS)
GAMMA = 0.90   # monthly discount (~10-month effective horizon)

# Transition matrices P[a][s, s'] for each action
# Rows = current state, Cols = next state (Active, At-Risk, Churned)
P = np.array([
    # Action 0: No intervention
    [[0.80, 0.15, 0.05],   # Active
     [0.10, 0.60, 0.30],   # At-Risk
     [0.00, 0.00, 1.00]],  # Churned (absorbing — cannot un-churn)
    # Action 1: Email campaign (mild improvement)
    [[0.82, 0.14, 0.04],
     [0.18, 0.62, 0.20],
     [0.00, 0.00, 1.00]],
    # Action 2: Discount offer (moderate improvement)
    [[0.85, 0.13, 0.02],
     [0.35, 0.55, 0.10],
     [0.00, 0.00, 1.00]],
    # Action 3: Personal call (highest improvement, highest cost)
    [[0.88, 0.10, 0.02],
     [0.50, 0.45, 0.05],
     [0.00, 0.00, 1.00]],
])

# Intervention costs (monthly per-customer)
COST = {"None": 0.0, "Email": 2.0, "Discount": 15.0, "Call": 40.0}

# Reward: customer value less intervention cost
# Active customer is worth $100/month, At-Risk $40/month, Churned $0
VALUE = {"Active": 100.0, "At-Risk": 40.0, "Churned": 0.0}

def reward(s, a):
    """Immediate reward = state value − intervention cost."""
    return [VALUE["Active"], VALUE["At-Risk"], VALUE["Churned"]][s] \
           - list(COST.values())[a]

R = np.array([[reward(s, a) for a in range(n_a)] for s in range(n_s)])

# ── Policy Iteration ───────────────────────────────────────────────────────────
def policy_evaluation(policy, tol=1e-8):
    """Solve V = R_π + γ P_π V as a linear system."""
    P_pi = np.array([P[policy[s], s, :] for s in range(n_s)])   # (n_s, n_s)
    R_pi = np.array([R[s, policy[s]] for s in range(n_s)])
    # (I - γ P_π) V = R_π
    A = np.eye(n_s) - GAMMA * P_pi
    V = np.linalg.solve(A, R_pi)
    return V

def policy_improvement(V):
    """Greedy policy improvement."""
    new_policy = np.zeros(n_s, dtype=int)
    for s in range(n_s):
        q = R[s, :] + GAMMA * P[:, s, :] @ V   # shape (n_a,)
        new_policy[s] = np.argmax(q)
    return new_policy

# Start with "No Action" everywhere
pi_policy = np.zeros(n_s, dtype=int)

for iteration in range(100):
    V = policy_evaluation(pi_policy)
    new_policy = policy_improvement(V)
    if np.array_equal(new_policy, pi_policy):
        print(f"Policy Iteration converged in {iteration + 1} iterations.")
        break
    pi_policy = new_policy

print("\nOptimal Policy:")
for s, a in enumerate(pi_policy):
    print(f"  {STATES[s]:>8}: {ACTIONS[a]} (reward = {R[s, a]:.1f}, V* = {V[s]:.1f})")

# ── Compare policies over 12-month horizon ─────────────────────────────────────
def simulate_policy(policy_indices, months=12, n_customers=1000):
    """Simulate customer distribution under a fixed policy."""
    dist = np.array([0.7, 0.3, 0.0])   # initial: 70% active, 30% at-risk
    history = [dist.copy()]
    for _ in range(months):
        new_dist = np.zeros(n_s)
        for s in range(n_s):
            a = policy_indices[s]
            new_dist += dist[s] * P[a, s, :]
        dist = new_dist
        history.append(dist.copy())
    return np.array(history)

MONTHS = 12
# Policy: always no intervention
hist_none    = simulate_policy([0, 0, 0])
# Policy: always intervene with full call
hist_always  = simulate_policy([3, 3, 3])
# Optimal MDP policy
hist_optimal = simulate_policy(pi_policy)

months = np.arange(MONTHS + 1)
fig, axes = plt.subplots(1, 3, figsize=(15, 5), sharey=True)
titles = ["No Intervention", "Always Call", "Optimal (MDP)"]
hists  = [hist_none, hist_always, hist_optimal]
colors = [["#2196F3", "#FF9800", "#F44336"]] * 3

for ax, title, hist, cols in zip(axes, titles, hists, colors):
    ax.stackplot(months, hist[:, 0], hist[:, 1], hist[:, 2],
                 labels=STATES, colors=["#4CAF50", "#FF9800", "#F44336"], alpha=0.85)
    ax.set_title(title)
    ax.set_xlabel("Month")
    ax.set_ylim(0, 1)
    ax.yaxis.set_major_formatter(plt.FuncFormatter(lambda y, _: f"{y:.0%}"))

axes[0].set_ylabel("Customer Distribution")
axes[1].legend(loc="upper right", fontsize=9)
plt.suptitle("Customer Lifecycle: 12-Month State Distribution Under Each Policy",
             fontsize=13, y=1.02)
plt.tight_layout()
plt.savefig("customer_churn_policies.png", dpi=150)
plt.show()

# ── Summary table ──────────────────────────────────────────────────────────────
print("\n12-Month Churned Fraction by Policy:")
print(f"  No Intervention:  {hist_none[-1, 2]:.1%}")
print(f"  Optimal MDP:      {hist_optimal[-1, 2]:.1%}")
print(f"  Always Call:      {hist_always[-1, 2]:.1%}")
```

---

## Part 3 — Interactive Exercises

### Exercise 1 — Guided (Warm-Up): DTMC Simulator & Steady-State Validation

!!! question "Task"
    Implement a 4-state DTMC simulator from scratch. Simulate 10,000 steps, estimate the steady-state distribution from visit frequencies, solve analytically, and verify they agree within 2%.

```python
# pip install numpy scipy
import numpy as np
from scipy.linalg import solve

np.random.seed(2024)

# 4-state transition matrix
P = np.array([
    [0.60, 0.20, 0.15, 0.05],
    [0.10, 0.50, 0.30, 0.10],
    [0.20, 0.10, 0.55, 0.15],
    [0.05, 0.25, 0.20, 0.50],
])
STATES = ["A", "B", "C", "D"]
N_STEPS = 10_000

# ── TODO 1: Simulate a DTMC for N_STEPS steps starting from state 0 ───────────
# trajectory = [0]
# for step in range(N_STEPS):
#     current = trajectory[-1]
#     next_state = np.random.choice(len(STATES), p=P[current])
#     trajectory.append(next_state)

# ── TODO 2: Estimate steady-state from visit frequency ────────────────────────
# counts = np.bincount(trajectory, minlength=len(STATES))
# sim_pi = counts / counts.sum()

# ── TODO 3: Solve for analytical steady-state ─────────────────────────────────
# A = (P.T - np.eye(len(STATES))).copy()
# A[-1] = 1.0
# b = np.zeros(len(STATES)); b[-1] = 1.0
# analytic_pi = solve(A, b)

# ── TODO 4: Print and compare; assert within 2% ───────────────────────────────
# print(f"{'State':>6} {'Simulated':>12} {'Analytical':>12} {'Diff':>8}")
# print("-" * 42)
# for s, sim, ana in zip(STATES, sim_pi, analytic_pi):
#     diff = abs(sim - ana)
#     print(f"{s:>6}  {sim:>11.4f}  {ana:>11.4f}  {diff:>7.4f}")
#     assert diff < 0.02, f"State {s}: simulation vs. analytical differ by {diff:.4f} > 2%"
# print("\n✓ All states agree within 2%.")
```

!!! tip "Solution Hints"
    - Use `np.random.choice(n, p=row)` to sample the next state from a row of the transition matrix.
    - `np.bincount` efficiently tallies integer visits.
    - The linear system for steady-state replaces the last row with `[1, 1, ..., 1]` and sets `b[-1] = 1`.

---

### Exercise 2 — Exploratory: Poisson Process Analysis

!!! question "Task"
    You are given a CSV of 500 synthetic arrival timestamps from a Poisson process. Estimate $\lambda$ via MLE, plot the empirical vs. theoretical inter-arrival CDF, run a KS goodness-of-fit test, and investigate whether $\lambda$ is stable across the first and second halves of the data.

```python
# pip install numpy scipy matplotlib pandas
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
from scipy import stats

np.random.seed(7)

# ── Generate synthetic dataset (λ = 5/hr) ─────────────────────────────────────
TRUE_LAMBDA = 5.0       # arrivals per hour
n_arrivals = 500

inter_arrivals = np.random.exponential(1.0 / TRUE_LAMBDA, size=n_arrivals)
arrival_times  = np.cumsum(inter_arrivals)

# Save and reload as if given a CSV
df = pd.DataFrame({"arrival_time_hr": arrival_times})
df.to_csv("/tmp/poisson_arrivals.csv", index=False)

# ── Load data ─────────────────────────────────────────────────────────────────
df = pd.read_csv("/tmp/poisson_arrivals.csv")
times = df["arrival_time_hr"].values
iats  = np.diff(np.concatenate([[0], times]))   # inter-arrival times

# ── TODO 1: MLE estimate of λ ─────────────────────────────────────────────────
# For Exponential(λ), MLE: λ̂ = 1 / mean(inter-arrivals)
# lambda_hat = ...
# print(f"MLE λ̂ = {lambda_hat:.3f}/hr  (true λ = {TRUE_LAMBDA})")

# ── TODO 2: Plot empirical vs. theoretical CDF ───────────────────────────────
# sorted_iats = np.sort(iats)
# empirical_cdf = np.arange(1, len(iats) + 1) / len(iats)
# theoretical_cdf = 1 - np.exp(-lambda_hat * sorted_iats)
# fig, ax = plt.subplots(figsize=(8, 5))
# ax.plot(sorted_iats, empirical_cdf, label="Empirical CDF", lw=2)
# ax.plot(sorted_iats, theoretical_cdf, label=f"Exp(λ̂={lambda_hat:.2f})", ls="--", lw=2)
# ax.set_xlabel("Inter-arrival Time (hr)"); ax.set_ylabel("CDF")
# ax.set_title("Inter-Arrival Time: Empirical vs. Theoretical CDF")
# ax.legend(); plt.tight_layout(); plt.show()

# ── TODO 3: Kolmogorov-Smirnov goodness-of-fit test ──────────────────────────
# ks_stat, p_value = stats.kstest(iats, "expon", args=(0, 1/lambda_hat))
# print(f"KS statistic = {ks_stat:.4f}, p-value = {p_value:.4f}")
# if p_value > 0.05:
#     print("Fail to reject H₀: data is consistent with Exponential(λ̂).")
# else:
#     print("Reject H₀: data does NOT fit Exponential(λ̂) at 5% significance.")

# ── TODO 4: Stability — compare λ̂ in first vs. second half ──────────────────
# half = len(iats) // 2
# lambda_first  = 1 / iats[:half].mean()
# lambda_second = 1 / iats[half:].mean()
# print(f"First-half λ̂ = {lambda_first:.3f},  Second-half λ̂ = {lambda_second:.3f}")
# stable = 'stable' if abs(lambda_first - lambda_second) < 0.5 else 'unstable'
# print(f"λ appears {stable}")
```

---

### Exercise 3 — Challenge: Stochastic Combat Attrition with Lanchester Laws

!!! question "Task"
    Model stochastic Lanchester attrition where Blue and Red force strengths are integer-valued states and attrition events follow Poisson processes. This is implemented as a discrete-time approximation (step size DT=0.1 hours) rather than a true CTMC, which makes the simulation tractable for large force sizes. Simulate 200 battle replicates from the same initial conditions. Plot mean force strength ± 95% CI for both sides, estimate P(Blue wins) as a function of initial force ratio, and compare to deterministic Lanchester predictions.

```python
# pip install numpy scipy matplotlib
import numpy as np
import matplotlib.pyplot as plt
from scipy.integrate import odeint

np.random.seed(314)

# ── Parameters ────────────────────────────────────────────────────────────────
BLUE_INIT = 100          # initial Blue force
RED_INIT  = 80           # initial Red force
BLUE_EFF  = 0.05         # Blue combat effectiveness (Lanchester): kills per unit per time
RED_EFF   = 0.04         # Red combat effectiveness
T_MAX     = 30.0         # battle duration (hours)
DT        = 0.1          # time step (hours)
N_REPS    = 200          # Monte Carlo replicates

time_grid = np.arange(0, T_MAX + DT, DT)

# ── Stochastic simulation ─────────────────────────────────────────────────────
def simulate_battle(b0, r0, blue_eff, red_eff, time_grid):
    """Stochastic Lanchester: attrition as Poisson process per time step."""
    B, R = float(b0), float(r0)
    blue_hist = [B]; red_hist = [R]
    for i in range(1, len(time_grid)):
        dt = time_grid[i] - time_grid[i-1]
        if B <= 0 or R <= 0:
            blue_hist.append(B); red_hist.append(R)
            continue
        # Blue kills Red: Poisson(B * blue_eff * dt)
        red_losses  = np.random.poisson(max(B, 0) * blue_eff * dt)
        # Red kills Blue: Poisson(R * red_eff * dt)
        blue_losses = np.random.poisson(max(R, 0) * red_eff * dt)
        B = max(0.0, B - blue_losses)
        R = max(0.0, R - red_losses)
        blue_hist.append(B); red_hist.append(R)
    return np.array(blue_hist), np.array(red_hist)

all_blue = np.zeros((N_REPS, len(time_grid)))
all_red  = np.zeros((N_REPS, len(time_grid)))

for rep in range(N_REPS):
    b, r = simulate_battle(BLUE_INIT, RED_INIT, BLUE_EFF, RED_EFF, time_grid)
    all_blue[rep] = b
    all_red[rep]  = r

# ── Deterministic Lanchester ODE ───────────────────────────────────────────────
def lanchester_ode(y, t, alpha, beta):
    """dB/dt = -β R,  dR/dt = -α B  (square law)."""
    B, R = y
    return [-beta * max(R, 0), -alpha * max(B, 0)]

det_sol = odeint(lanchester_ode, [BLUE_INIT, RED_INIT],
                 time_grid, args=(BLUE_EFF, RED_EFF))
det_blue = np.maximum(det_sol[:, 0], 0)
det_red  = np.maximum(det_sol[:, 1], 0)

# ── Statistics ────────────────────────────────────────────────────────────────
mean_blue = all_blue.mean(axis=0)
mean_red  = all_red.mean(axis=0)
ci95_blue = 1.96 * all_blue.std(axis=0) / N_REPS**0.5
ci95_red  = 1.96 * all_red.std(axis=0) / N_REPS**0.5

p_blue_wins = np.mean(all_blue[:, -1] > all_red[:, -1])
print(f"P(Blue wins) from {N_REPS} replicates: {p_blue_wins:.1%}")

# ── Plot force strength over time ─────────────────────────────────────────────
fig, ax = plt.subplots(figsize=(12, 6))

ax.fill_between(time_grid, mean_blue - ci95_blue, mean_blue + ci95_blue,
                alpha=0.25, color="royalblue")
ax.fill_between(time_grid, mean_red - ci95_red, mean_red + ci95_red,
                alpha=0.25, color="crimson")
ax.plot(time_grid, mean_blue, "royalblue", lw=2, label="Blue (stochastic mean)")
ax.plot(time_grid, mean_red,  "crimson",   lw=2, label="Red (stochastic mean)")
ax.plot(time_grid, det_blue, "royalblue", lw=2, ls="--", label="Blue (deterministic)")
ax.plot(time_grid, det_red,  "crimson",   lw=2, ls="--", label="Red (deterministic)")

ax.set_xlabel("Time (hours)"); ax.set_ylabel("Force Strength")
ax.set_title(f"Stochastic vs. Deterministic Lanchester Attrition\n"
             f"(B₀={BLUE_INIT}, R₀={RED_INIT}, α={BLUE_EFF}, β={RED_EFF}; "
             f"P(Blue wins)={p_blue_wins:.1%})")
ax.legend(); ax.set_ylim(0)
plt.tight_layout()
plt.savefig("lanchester_attrition.png", dpi=150)
plt.show()

# ── P(Blue wins) vs. initial force ratio ─────────────────────────────────────
ratios = np.linspace(0.5, 2.5, 13)   # Blue/Red ratio
p_wins = []

for ratio in ratios:
    b0 = int(RED_INIT * ratio)
    wins = 0
    for _ in range(N_REPS):
        b, r = simulate_battle(b0, RED_INIT, BLUE_EFF, RED_EFF, time_grid)
        wins += int(b[-1] > r[-1])
    p_wins.append(wins / N_REPS)

fig, ax = plt.subplots(figsize=(8, 5))
ax.plot(ratios, p_wins, "o-", color="royalblue", lw=2, ms=7)
ax.axhline(0.5, color="gray", ls="--", label="50% win probability")
ax.set_xlabel("Initial Force Ratio (Blue / Red)")
ax.set_ylabel("P(Blue Wins)")
ax.set_title("Stochastic Lanchester: Win Probability vs. Force Ratio")
ax.legend(); ax.set_ylim(0, 1)
plt.tight_layout()
plt.savefig("lanchester_win_probability.png", dpi=150)
plt.show()

print("\nDiscussion:")
print("  - With large forces the stochastic and deterministic results converge.")
print("  - With small forces, randomness has greater impact: P(Blue wins) shows")
print("    more variance around the deterministic threshold, meaning even the")
print("    smaller force can win by chance — the 'small unit action' effect.")
```

---

## Check Your Understanding — Full Module

1. What does the Markov property imply, and when is it a reasonable assumption in a military context?
2. How do you determine if a Markov chain has a unique steady-state distribution?
3. What is the relationship between MDPs and reinforcement learning?

---

!!! tip "Prompt Template"
    Use the [Agent Prompt](prompt.md) for this topic, or the [Generic Prompt Template](../prompt-template.md) to generate additional lectures, examples, and exercises.
