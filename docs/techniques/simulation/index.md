# Simulation

> Modeling stochastic systems to estimate performance and risk.

---

## Part 1 — Lecture: Conceptual Foundation

### 1.1 When to Simulate vs. Use Analytical Models

Simulation is a powerful tool, but it is not always the right one. The choice between simulation and an analytical model depends on two competing pressures: **tractability** and **fidelity**.

| Criterion | Analytical Model | Simulation |
|-----------|-----------------|------------|
| **Speed** | Fast — closed-form solution | Slow — requires many runs |
| **Insight** | Direct sensitivity via parameters | Requires designed experiments |
| **Complexity** | Limited to tractable math | Arbitrary system complexity |
| **Randomness** | Often assumes specific distributions | Any distribution; non-stationary |
| **Interactions** | Usually simplified | Captures feedback and contention |
| **Validation** | Mathematical proof | Statistical testing against data |

**Use simulation when:**

- The system has too many interacting stochastic components for closed-form analysis
- Distributions are empirical (non-standard) or time-varying
- You need to capture transient behavior, not just steady state
- You want to test interventions (policy changes, resource additions) in a risk-free environment

**Use analytical models when:**

- A closed-form solution exists and its assumptions hold (e.g., M/M/c queue)
- You need rapid, repeated evaluations (e.g., optimization inner loop)
- Transparency and interpretability are paramount

!!! note "Rule of Thumb"
    Start with the simplest analytical model. If it captures the key behavior, stop. Simulate only when analytical models demonstrably fail to capture something important.

---

### 1.2 Taxonomy of Simulation Types

#### Discrete-Event Simulation (DES)

The system state changes only at discrete points in time called **events**. Between events, nothing changes. The simulation advances from event to event, skipping idle periods.

**Core components:**

- **Event calendar** — a priority queue of future events, ordered by time
- **System state** — counters, resource levels, queue lengths
- **Process** — a sequence of events and delays that model an entity's lifecycle

SimPy uses a **process-based** style: each entity (aircraft, customer, vehicle) is a Python generator that `yield`s delays and resource requests.

```python
# pip install simpy
import simpy

def aircraft(env, name, runway):
    """Aircraft requests a runway, performs sortie, releases runway."""
    print(f"{name} requests runway at t={env.now:.1f}")
    with runway.request() as req:
        yield req                          # wait for runway
        print(f"{name} takes off at t={env.now:.1f}")
        yield env.timeout(45)              # sortie duration (min)
        print(f"{name} lands at t={env.now:.1f}")

env = simpy.Environment()
runway = simpy.Resource(env, capacity=2)  # 2 runways

for i in range(4):
    env.process(aircraft(env, f"AC-{i+1}", runway))

env.run()
```

#### Monte Carlo Simulation

Uses repeated **random sampling** to estimate numerical quantities. There are no events — just many independent trials drawn from probability distributions. The law of large numbers guarantees convergence as the number of trials $N \to \infty$.

$$\hat{\mu} = \frac{1}{N}\sum_{i=1}^{N} f(X_i), \quad \text{SE}(\hat{\mu}) = \frac{\hat{\sigma}}{\sqrt{N}}$$

#### Agent-Based Modeling (ABM)

Each entity (agent) has its own state and decision rules. Macro-level behavior **emerges** from micro-level interactions. Prefer ABM when:

- Heterogeneous agents with individual strategies (e.g., combat units with different tactics)
- Spatial interactions matter
- Adaptive behavior or learning is present

#### System Dynamics

Continuous-time models described by **stocks** (accumulations), **flows** (rates of change), and **feedback loops**. Governed by ordinary differential equations. Best for strategic, aggregate analysis (e.g., epidemic spread, force attrition).

---

### 1.3 Random Variate Generation

To simulate stochastic systems, we must generate samples from probability distributions. All methods start from $U \sim \text{Uniform}(0,1)$, available in every language.

#### Inverse CDF Transform

If $F^{-1}$ is analytically available, set $X = F^{-1}(U)$:

$$X = -\frac{1}{\lambda}\ln(1 - U) \quad \Rightarrow \quad X \sim \text{Exponential}(\lambda)$$

#### Acceptance-Rejection

When $F^{-1}$ is intractable, sample from an envelope distribution $g(x)$ and accept with probability $f(x)/[c \cdot g(x)]$.

#### Python/NumPy Examples

```python
# pip install numpy scipy
import numpy as np
from scipy import stats

rng = np.random.default_rng(seed=42)
N = 5

# Uniform(a, b)
u = rng.uniform(low=0, high=10, size=N)
print(f"Uniform:     {u.round(2)}")

# Exponential(rate=lambda) — mean = 1/lambda
exp_samples = rng.exponential(scale=1/2.0, size=N)   # lambda=2
print(f"Exponential: {exp_samples.round(2)}")

# Normal(mu, sigma)
norm_samples = rng.normal(loc=5.0, scale=1.5, size=N)
print(f"Normal:      {norm_samples.round(2)}")

# Triangular(low, mode, high)
tri_samples = rng.triangular(left=1, mode=4, right=10, size=N)
print(f"Triangular:  {tri_samples.round(2)}")

# Poisson(lambda) — discrete
pois_samples = rng.poisson(lam=3.0, size=N)
print(f"Poisson:     {pois_samples}")

# Inverse CDF transform — Exponential manually
u_raw = rng.uniform(size=N)
exp_manual = -np.log(1 - u_raw) / 2.0              # lambda=2
print(f"Exp (manual):{exp_manual.round(2)}")
```

!!! info "Modern NumPy RNG"
    Use `numpy.random.default_rng(seed)` for reproducible, high-quality randomness (PCG64 generator). Avoid the legacy `np.random.seed()` API in new code.

---

### 1.4 Output Analysis

Simulation output is **random**. A single run is meaningless; we need statistical inference.

#### Warm-Up Period (Transient Removal)

Most systems start in an artificial state (empty queues, idle resources). The **warm-up period** is the initial interval during which the simulation is driven toward steady state. Collect data only *after* the warm-up.

Detection methods:
- **Welch's graphical method** — plot moving averages across replications
- **MSER heuristic** — minimize mean-squared error of the truncation point

#### Replication-Deletion Method

Run $R$ independent replications, each with a warm-up of length $T_w$, collecting data only from $[T_w, T_{\text{end}}]$. Each replication yields one observation $Y_r$, so observations are i.i.d.

$$\bar{Y} = \frac{1}{R}\sum_{r=1}^R Y_r, \qquad
s^2 = \frac{1}{R-1}\sum_{r=1}^R (Y_r - \bar{Y})^2$$

$$\text{95\% CI:} \quad \bar{Y} \pm t_{R-1,\,0.025} \cdot \frac{s}{\sqrt{R}}$$

#### Batch Means (for Single Long Runs)

Divide one long run into $k$ batches. If batches are large enough, inter-batch correlation becomes negligible and batch means are approximately i.i.d.

#### Python: Confidence Intervals Across Replications

```python
import numpy as np
from scipy import stats

def confidence_interval(data, confidence=0.95):
    """95% CI using t-distribution."""
    n = len(data)
    mean = np.mean(data)
    se = stats.sem(data)             # standard error of the mean
    h = se * stats.t.ppf((1 + confidence) / 2, df=n - 1)
    return mean, mean - h, mean + h

# Example: 20 replication results (daily sortie rates)
rng = np.random.default_rng(7)
replication_results = rng.normal(loc=14.5, scale=2.1, size=20)

mean, lo, hi = confidence_interval(replication_results)
print(f"Mean daily sorties: {mean:.2f}  95% CI: [{lo:.2f}, {hi:.2f}]")
```

---

### 1.5 Variance Reduction Techniques

Variance reduction (VR) techniques reduce the standard error $s/\sqrt{R}$ without increasing the number of replications — effectively getting more precision for the same computational cost.

#### Common Random Numbers (CRN)

Use the **same random seed sequence** across all design alternatives being compared. The random variation that inflates each estimate cancels when you take the *difference*, reducing variance of the comparison.

```python
import numpy as np

def system_mean(config, seed):
    rng = np.random.default_rng(seed)
    # Simulate system with given config; return mean KPI
    service_times = rng.exponential(scale=1/config["mu"], size=1000)
    return service_times.mean()

seeds = range(20)
mu_A, mu_B = 1.5, 2.0

# Without CRN: independent seeds
diffs_no_crn = [
    system_mean({"mu": mu_B}, seed=s*2+1) - system_mean({"mu": mu_A}, seed=s*2)
    for s in seeds
]

# With CRN: same seed for both configs
diffs_crn = [
    system_mean({"mu": mu_B}, seed=s) - system_mean({"mu": mu_A}, seed=s)
    for s in seeds
]

print(f"Var(diff) without CRN: {np.var(diffs_no_crn):.6f}")
print(f"Var(diff) with CRN:    {np.var(diffs_crn):.6f}  ← smaller")
```

#### Antithetic Variates

For each sample $U$, also use $1-U$. The two estimates are negatively correlated, so their average has lower variance than either alone.

$$\text{Var}\left(\frac{X + X'}{2}\right) = \frac{\text{Var}(X) + \text{Var}(X') + 2\text{Cov}(X,X')}{4}$$

Since $\text{Cov}(X, X') < 0$, variance is reduced.

#### Control Variates

Use a correlated quantity $C$ whose expected value $\mu_C$ is *known* to correct the estimator:

$$Y^* = Y - \beta(C - \mu_C), \quad \beta^* = \frac{\text{Cov}(Y,C)}{\text{Var}(C)}$$

The optimal $\beta^*$ maximizes variance reduction; even a rough estimate of $\beta$ usually helps.

---

### 1.6 Verification vs. Validation

| Concept | Question | How |
|---------|---------|-----|
| **Verification** | "Are we building the model right?" — does the code match the conceptual model? | Unit tests, trace runs, extreme-value tests, structured walk-throughs |
| **Validation** | "Are we building the right model?" — does the model represent reality? | Compare output to historical data, expert judgment, sensitivity analysis |

**Verification checklist:**

- [ ] Run with zero arrivals — system stays idle
- [ ] Run with infinite capacity — no queueing occurs
- [ ] Conservation checks — every entity that enters must exit
- [ ] Trace a single entity through the full lifecycle

**Validation checklist:**

- [ ] Output statistics match historical operational data within tolerance
- [ ] Model behavior changes direction correctly when parameters are stressed
- [ ] Subject-matter experts agree that emergent behavior is plausible

---

### Check Your Understanding — Part 1

!!! question "Question 1"
    A field hospital analyst wants to assess the impact of adding a second trauma surgeon. She has access to 6 months of patient arrival records. Should she build an analytical queueing model or a discrete-event simulation? What is the key deciding factor?

!!! question "Question 2"
    You run 30 independent replications of a supply chain simulation and compute the 95% CI for average lead time as $[18.2, 21.4]$ days. A colleague suggests running a single 30× longer run instead. What advantage do the 30 replications have?

!!! question "Question 3"
    Explain why antithetic variates reduce variance. Under what conditions would this technique *fail* to reduce variance?

---

## Part 2 — Real-World Practical Examples

### Example A — Military: Airfield Sortie Generation Simulation

!!! example "Scenario"
    Simulate an airfield with **2 runways** and **20 aircraft** (3 types). Aircraft have stochastic maintenance requirements (exponential time-to-failure / time-to-repair) and random weather disruptions (Poisson events that close runways). Track daily sortie rate, aircraft utilization, and maintenance backlog over a **30-day campaign**. Run **20 replications** and compute a **95% CI** on mean daily sortie rate. Identify the binding constraint.

**Open-Source Tools:** SimPy, NumPy, SciPy, matplotlib

```python
# pip install simpy numpy scipy matplotlib
import simpy
import numpy as np
from scipy import stats
import matplotlib.pyplot as plt

# ── Configuration ──────────────────────────────────────────────
SIM_DAYS        = 30
WARMUP_DAYS     = 2          # discard first 2 days of data
N_REPLICATIONS  = 20
N_AIRCRAFT      = 20
N_RUNWAYS       = 2
N_MAINT_CREWS   = 4          # maintenance technician teams

# Aircraft types and their parameters
AIRCRAFT_TYPES = {
    "Fighter":   {"count": 10, "sortie_duration": (45, 75),   # (min, max) minutes
                  "ttf_mean": 8.0, "ttr_mean": 3.0},           # days
    "Tanker":    {"count":  6, "sortie_duration": (120, 180),
                  "ttf_mean": 12.0, "ttr_mean": 4.0},
    "ISR":       {"count":  4, "sortie_duration": (180, 300),
                  "ttf_mean": 10.0, "ttr_mean": 2.5},
}

# Weather disruption: Poisson arrivals, each closes all runways for Exp(mean) minutes
WEATHER_ARRIVAL_RATE = 1.5   # disruptions per day
WEATHER_DURATION_MEAN = 45   # minutes per disruption

MINUTES_PER_DAY = 1440

def run_airfield(seed: int, n_runways: int = N_RUNWAYS,
                 n_maint_crews: int = N_MAINT_CREWS,
                 with_weather: bool = True):
    """Run one 30-day replication; return list of daily sortie counts."""
    rng = np.random.default_rng(seed)
    env = simpy.Environment()

    # Shared resources (sizes controlled by parameters)
    runways = simpy.Resource(env, capacity=n_runways)
    maint_crews = simpy.Resource(env, capacity=n_maint_crews)

    daily_sorties = [0] * SIM_DAYS

    # ── Weather process ────────────────────────────────────────
    def weather_generator():
        while True:
            # Time until next weather event (Poisson inter-arrival)
            gap = rng.exponential(scale=MINUTES_PER_DAY / WEATHER_ARRIVAL_RATE)
            yield env.timeout(gap)
            duration = rng.exponential(scale=WEATHER_DURATION_MEAN)
            # Seize all runways to block operations
            reqs = [runways.request() for _ in range(n_runways)]
            for req in reqs:
                yield req
            yield env.timeout(duration)
            for req in reqs:
                runways.release(req)

    # ── Aircraft process ───────────────────────────────────────
    def aircraft_process(ac_type: str, params: dict):
        while True:
            # Fly sorties until next maintenance event
            ttf = rng.exponential(scale=params["ttf_mean"] * MINUTES_PER_DAY)
            end_of_service = env.now + ttf

            while env.now < end_of_service:
                # Request a runway
                with runways.request() as req:
                    yield req
                    # Perform sortie
                    min_dur, max_dur = params["sortie_duration"]
                    duration = rng.uniform(min_dur, max_dur)
                    yield env.timeout(duration)

                # Record sortie in correct day (skip warm-up)
                day = int(env.now // MINUTES_PER_DAY)
                if WARMUP_DAYS <= day < SIM_DAYS:
                    daily_sorties[day] += 1

                # Brief turnaround before next sortie
                yield env.timeout(rng.uniform(30, 90))

            # ── Maintenance ────────────────────────────────────
            with maint_crews.request() as req:
                yield req
                ttr = rng.exponential(scale=params["ttr_mean"] * MINUTES_PER_DAY)
                yield env.timeout(ttr)

    # ── Weather disruption generator (optional) ────────────────
    if with_weather:
        env.process(weather_generator())

    # ── Spawn aircraft ─────────────────────────────────────────
    for ac_type, params in AIRCRAFT_TYPES.items():
        for _ in range(params["count"]):
            # Stagger initial start times slightly
            env.process(aircraft_process(ac_type, params))

    env.run(until=SIM_DAYS * MINUTES_PER_DAY)

    # Return daily sortie rates (sorties/day) for post-warm-up days
    return [daily_sorties[d] for d in range(WARMUP_DAYS, SIM_DAYS)]


# ── Run replications ───────────────────────────────────────────
all_daily = []
for rep in range(N_REPLICATIONS):
    daily = run_airfield(seed=rep * 100)
    all_daily.append(daily)

all_daily = np.array(all_daily)   # shape: (20 reps, 28 days)

# ── Per-replication mean daily sortie rate ─────────────────────
rep_means = all_daily.mean(axis=1)
grand_mean, ci_lo, ci_hi = (
    rep_means.mean(),
    *stats.t.interval(0.95, df=N_REPLICATIONS - 1,
                      loc=rep_means.mean(),
                      scale=stats.sem(rep_means))
)
print(f"Mean daily sortie rate: {grand_mean:.1f}")
print(f"95% CI: [{ci_lo:.1f}, {ci_hi:.1f}] sorties/day")

# ── Plot: mean sortie rate over campaign ────────────────────────
day_mean = all_daily.mean(axis=0)
day_lo   = np.percentile(all_daily, 2.5, axis=0)
day_hi   = np.percentile(all_daily, 97.5, axis=0)
days     = np.arange(WARMUP_DAYS, SIM_DAYS)

fig, ax = plt.subplots(figsize=(10, 4))
ax.fill_between(days, day_lo, day_hi, alpha=0.3, label="95% CI band")
ax.plot(days, day_mean, linewidth=2, label="Mean daily sorties")
ax.axhline(grand_mean, color="red", linestyle="--",
           label=f"Campaign mean: {grand_mean:.1f}")
ax.set_xlabel("Campaign Day")
ax.set_ylabel("Sorties")
ax.set_title("Airfield Sortie Generation — 20 Replications (2-day warm-up excluded)")
ax.legend()
plt.tight_layout()
plt.savefig("airfield_sorties.png", dpi=150)
plt.show()

# ── Identify binding constraint ────────────────────────────────
# Heuristic: re-run once with doubled runways / doubled crews / no weather
def identify_constraint():
    results = {}
    for label, (nr, nc, ww) in [
        ("baseline",        (N_RUNWAYS,     N_MAINT_CREWS,     True)),
        ("2× runways",      (N_RUNWAYS * 2, N_MAINT_CREWS,     True)),
        ("2× maint crews",  (N_RUNWAYS,     N_MAINT_CREWS * 2, True)),
        ("no weather",      (N_RUNWAYS,     N_MAINT_CREWS,     False)),
    ]:
        # Quick 5-rep estimate, passing config into run_airfield
        totals = [np.mean(run_airfield(seed=s + 999,
                                       n_runways=nr,
                                       n_maint_crews=nc,
                                       with_weather=ww))
                  for s in range(5)]
        results[label] = np.mean(totals)
    return results

print("\nBinding Constraint Analysis (5-rep estimate):")
constraint_results = identify_constraint()
baseline = constraint_results["baseline"]
for label, val in constraint_results.items():
    improvement = (val - baseline) / baseline * 100 if label != "baseline" else 0
    print(f"  {label:<20} {val:.1f} sorties/day  ({improvement:+.1f}%)")
```

---

### Example B — Civilian: Supply Chain Risk via Monte Carlo

!!! example "Scenario"
    A 5-stage supply chain (Raw Material → Component Manufacturing → Assembly → Distribution → Retail) has uncertain lead times and a probability of disruption at each stage. Estimate the probability that total lead time exceeds **30 days** and the **95th-percentile lead time**. Perform a tornado chart sensitivity analysis. Demonstrate variance reduction with **common random numbers**.

**Open-Source Tools:** NumPy, SciPy, matplotlib

```python
# pip install numpy scipy matplotlib
import numpy as np
import matplotlib.pyplot as plt
from scipy import stats

N_TRIALS = 100_000

# ── Stage parameters ───────────────────────────────────────────
# Each stage: (min_days, mode_days, max_days, disruption_prob, disruption_extra_days)
STAGES = {
    "Raw Material":    (2,  4,  8,  0.05, 5),
    "Component Mfg":  (5,  8, 14,  0.10, 7),
    "Assembly":        (3,  5, 10,  0.08, 6),
    "Distribution":    (2,  3,  6,  0.06, 4),
    "Retail":          (1,  2,  4,  0.03, 2),
}
RISK_THRESHOLD = 30   # days

def simulate_supply_chain(rng: np.random.Generator, n: int) -> np.ndarray:
    """Vectorized simulation; returns array of total lead times."""
    total = np.zeros(n)
    for name, (lo, mode, hi, p_disrupt, extra) in STAGES.items():
        lead = rng.triangular(lo, mode, hi, size=n)
        disrupted = rng.random(size=n) < p_disrupt
        total += lead + disrupted * rng.exponential(scale=extra, size=n)
    return total

# ── Standard Monte Carlo ───────────────────────────────────────
rng_std = np.random.default_rng(42)
total_lead_std = simulate_supply_chain(rng_std, N_TRIALS)

prob_exceed = np.mean(total_lead_std > RISK_THRESHOLD)
p95          = np.percentile(total_lead_std, 95)
mean_lt      = total_lead_std.mean()
print(f"Mean lead time:         {mean_lt:.1f} days")
print(f"P(lead time > 30 days): {prob_exceed:.1%}")
print(f"95th-percentile:        {p95:.1f} days")

# ── Variance reduction: Common Random Numbers ──────────────────
# Compare two policies: baseline vs. expediting Raw Material (mode 3 instead of 4)
def sim_policy(policy: str, rng: np.random.Generator, n: int) -> np.ndarray:
    total = np.zeros(n)
    for name, (lo, mode, hi, p_disrupt, extra) in STAGES.items():
        if name == "Raw Material" and policy == "expedite":
            mode = 3   # expedited mode
        lead = rng.triangular(lo, mode, hi, size=n)
        disrupted = rng.random(size=n) < p_disrupt
        total += lead + disrupted * rng.exponential(scale=extra, size=n)
    return total

# Without CRN: independent seeds
rng_a = np.random.default_rng(100)
rng_b = np.random.default_rng(200)
diff_no_crn = sim_policy("baseline", rng_a, N_TRIALS) - sim_policy("expedite", rng_b, N_TRIALS)

# With CRN: same seed drives both policies
rng_c = np.random.default_rng(300)
rng_d = np.random.default_rng(300)   # ← same seed
diff_crn = sim_policy("baseline", rng_c, N_TRIALS) - sim_policy("expedite", rng_d, N_TRIALS)

print(f"\nVariance Reduction via CRN:")
print(f"  Var(diff) without CRN: {np.var(diff_no_crn):.4f}")
print(f"  Var(diff) with CRN:    {np.var(diff_crn):.4f}")
print(f"  Reduction:             {(1 - np.var(diff_crn)/np.var(diff_no_crn)):.1%}")

# ── Histogram with risk thresholds ────────────────────────────
fig, axes = plt.subplots(1, 2, figsize=(14, 5))

ax = axes[0]
ax.hist(total_lead_std, bins=80, color="steelblue", edgecolor="white",
        alpha=0.8, density=True)
ax.axvline(RISK_THRESHOLD, color="red", linewidth=2, linestyle="--",
           label=f"Risk threshold: {RISK_THRESHOLD}d")
ax.axvline(p95, color="orange", linewidth=2, linestyle=":",
           label=f"P95: {p95:.1f}d")
ax.axvline(mean_lt, color="green", linewidth=2, linestyle="-",
           label=f"Mean: {mean_lt:.1f}d")
ax.set_xlabel("Total Lead Time (days)")
ax.set_ylabel("Density")
ax.set_title("Supply Chain Lead Time Distribution\n(100,000 Monte Carlo trials)")
ax.legend()

# ── Tornado chart ──────────────────────────────────────────────
# Measure variance contribution of each stage by toggling uncertainty
baseline_var = np.var(total_lead_std)
contributions = {}
for stage_name in STAGES:
    # Fix this stage at its mode; measure remaining variance
    rng_t = np.random.default_rng(42)
    total_fixed = np.zeros(N_TRIALS)
    for name, (lo, mode, hi, p_disrupt, extra) in STAGES.items():
        if name == stage_name:
            total_fixed += mode   # deterministic mode
        else:
            lead = rng_t.triangular(lo, mode, hi, size=N_TRIALS)
            disrupted = rng_t.random(size=N_TRIALS) < p_disrupt
            total_fixed += lead + disrupted * rng_t.exponential(scale=extra, size=N_TRIALS)
    reduced_var = np.var(total_fixed)
    contributions[stage_name] = baseline_var - reduced_var   # variance removed by fixing

ax = axes[1]
labels = list(contributions.keys())
values = [contributions[l] for l in labels]
order  = np.argsort(values)[::-1]
ax.barh([labels[i] for i in order], [values[i] for i in order],
        color="coral", edgecolor="black", alpha=0.8)
ax.set_xlabel("Variance Reduction (days²)")
ax.set_title("Tornado Chart — Stage Variance Contribution")
ax.invert_yaxis()

plt.tight_layout()
plt.savefig("supply_chain_risk.png", dpi=150)
plt.show()
```

---

### Check Your Understanding — Part 2

!!! question "Question 4"
    In Example A, the binding-constraint analysis doubles runways, doubles maintenance crews, and eliminates weather separately. Why is it important to change *one factor at a time* rather than all at once?

!!! question "Question 5"
    In Example B, the tornado chart ranks stages by variance contribution. If "Component Mfg" dominates, what are two interventions a supply chain manager could take, and how would you model them in the simulation?

!!! question "Question 6"
    Example B uses `numpy.random.default_rng(300)` twice (same seed) to implement CRN. Under what circumstances would this CRN implementation *not* work correctly — i.e., the two generators diverging and using different random draws for the same stage?

---

## Part 3 — Interactive Exercises

### Exercise 1 — Guided (Warm-Up): M/M/1 Queue via SimPy

!!! question "Task"
    Build an M/M/1 queue simulation using SimPy. Fill in every `# TODO` block. At the end, compare your simulated mean wait time with the analytical M/M/1 formula $W_q = \rho / (\mu - \lambda)$.

```python
# pip install simpy numpy scipy
import simpy
import numpy as np
from scipy import stats

ARRIVAL_RATE  = 0.9   # lambda: customers per minute
SERVICE_RATE  = 1.2   # mu:     customers per minute
SIM_TIME      = 5_000 # minutes (long run for steady state)
WARMUP        = 500   # minutes to discard

# Analytical M/M/1 benchmark
rho       = ARRIVAL_RATE / SERVICE_RATE
Wq_theory = rho / (SERVICE_RATE - ARRIVAL_RATE)
print(f"Analytical Wq = {Wq_theory:.3f} min  (rho = {rho:.2f})")

wait_times  = []   # will be filled by the simulation

def customer(env, server):
    """Customer arrives, waits for server, gets served, departs."""
    arrival = env.now
    with server.request() as req:
        yield req                   # wait until server free
        wait = env.now - arrival

        # TODO 1: sample service time from Exp(SERVICE_RATE) using random.expovariate
        #         Hint: random.expovariate(SERVICE_RATE) gives Exp(mu)
        raise NotImplementedError("TODO 1: replace this line with service_time = ...")
        # yield env.timeout(service_time)  # ← uncomment after completing TODO 1

        # TODO 2: record wait_time only if we are past the warm-up period
        #         Hint: check env.now > WARMUP before appending to wait_times
        pass                        # ← replace pass

def arrival_generator(env, server):
    """Generate customers according to a Poisson process."""
    while True:
        # TODO 3: yield a timeout drawn from Exp(ARRIVAL_RATE) using random.expovariate
        #         Hint: inter-arrival time = random.expovariate(ARRIVAL_RATE)
        yield env.timeout(0)        # ← replace 0

        env.process(customer(env, server))

import random
random.seed(99)

env    = simpy.Environment()
server = simpy.Resource(env, capacity=1)
env.process(arrival_generator(env, server))
env.run(until=SIM_TIME)

# TODO 4: compute and print the simulated mean and 95% CI of wait_times
#         Compare with Wq_theory above
#         Hint: use scipy.stats.sem and scipy.stats.t.interval
print(f"Simulated Wq  = ???")      # ← replace with real computation
```

??? success "Solution"
    ```python
    import simpy, random, numpy as np
    from scipy import stats

    ARRIVAL_RATE = 0.9
    SERVICE_RATE = 1.2
    SIM_TIME     = 5_000
    WARMUP       = 500

    wait_times = []

    def customer(env, server):
        arrival = env.now
        with server.request() as req:
            yield req
            wait = env.now - arrival
            service_time = random.expovariate(SERVICE_RATE)    # TODO 1 ✓
            yield env.timeout(service_time)
            if env.now > WARMUP:                               # TODO 2 ✓
                wait_times.append(wait)

    def arrival_generator(env, server):
        while True:
            yield env.timeout(random.expovariate(ARRIVAL_RATE))# TODO 3 ✓
            env.process(customer(env, server))

    random.seed(99)
    env    = simpy.Environment()
    server = simpy.Resource(env, capacity=1)
    env.process(arrival_generator(env, server))
    env.run(until=SIM_TIME)

    n    = len(wait_times)
    mean = np.mean(wait_times)
    se   = stats.sem(wait_times)
    ci   = stats.t.interval(0.95, df=n-1, loc=mean, scale=se)
    rho  = ARRIVAL_RATE / SERVICE_RATE
    Wq_theory = rho / (SERVICE_RATE - ARRIVAL_RATE)
    print(f"Analytical  Wq = {Wq_theory:.3f} min")            # TODO 4 ✓
    print(f"Simulated   Wq = {mean:.3f} min  95% CI {ci[0]:.3f}–{ci[1]:.3f}")
    ```

---

### Exercise 2 — Exploratory: Monte Carlo Pi Estimation

!!! question "Task"
    Estimate $\pi$ by sampling points uniformly in the unit square $[0,1]^2$. A point falls inside the quarter-circle of radius 1 if $x^2 + y^2 \leq 1$. The fraction of points inside equals $\pi/4$.

    1. Plot the convergence of your estimate vs. sample size on a log-scale x-axis.
    2. Determine empirically how many samples $N$ are needed for 4 decimal places of accuracy ($|\hat{\pi} - \pi| < 5 \times 10^{-4}$).
    3. Apply **antithetic variates**: for each pair $(u_1, u_2)$, also use $(1-u_1, 1-u_2)$. Compare convergence speed.

```python
# pip install numpy matplotlib
import numpy as np
import matplotlib.pyplot as plt

rng = np.random.default_rng(42)

sample_sizes = np.logspace(2, 6, 60).astype(int)
estimates_std      = []
estimates_antithetic = []

for N in sample_sizes:
    # Standard estimate
    pts  = rng.random((N, 2))
    frac = np.mean(pts[:, 0]**2 + pts[:, 1]**2 <= 1.0)
    estimates_std.append(4 * frac)

    # TODO 1: Antithetic variates
    # Hint: generate N//2 points, compute their complements (1-pt),
    #       stack both halves, estimate pi from all N points
    estimates_antithetic.append(0.0)   # ← replace with antithetic estimate

# TODO 2: Plot both curves vs. sample size (log scale x-axis)
#         Add a horizontal reference line at np.pi
#         Label axes and add a legend

# TODO 3: Find first N where |estimate - pi| < 5e-4 for both methods
```

??? success "Solution"
    ```python
    import numpy as np
    import matplotlib.pyplot as plt

    rng = np.random.default_rng(42)
    sample_sizes = np.logspace(2, 6, 60).astype(int)
    estimates_std, estimates_anti = [], []

    for N in sample_sizes:
        pts  = rng.random((N, 2))
        estimates_std.append(4 * np.mean(pts[:, 0]**2 + pts[:, 1]**2 <= 1.0))

        # Antithetic variates (TODO 1 ✓)
        half = rng.random((N // 2, 2))
        both = np.vstack([half, 1 - half])
        estimates_anti.append(4 * np.mean(both[:, 0]**2 + both[:, 1]**2 <= 1.0))

    # TODO 2 ✓
    fig, ax = plt.subplots(figsize=(9, 4))
    ax.semilogx(sample_sizes, estimates_std,  label="Standard MC", alpha=0.8)
    ax.semilogx(sample_sizes, estimates_anti, label="Antithetic variates", alpha=0.8)
    ax.axhline(np.pi, color="black", linestyle="--", label="True π")
    ax.set_xlabel("Number of samples (log scale)")
    ax.set_ylabel("Estimated π")
    ax.set_title("Monte Carlo π Estimation — Convergence")
    ax.legend()
    plt.tight_layout()
    plt.show()

    # TODO 3 ✓
    target = 5e-4
    for label, ests in [("Standard", estimates_std), ("Antithetic", estimates_anti)]:
        for n, e in zip(sample_sizes, ests):
            if abs(e - np.pi) < target:
                print(f"{label}: first N reaching 4dp = {n:,}")
                break
    ```

---

### Exercise 3 — Challenge: Combat Casualty Simulation

!!! question "Task"
    Simulate a 72-hour ground combat operation. Casualties arrive according to a **non-homogeneous Poisson process** (high rates during "attack" phases, low during "consolidation"). Each casualty is classified as:

    - **Urgent** — must be treated within 1 hour or survival probability drops sharply
    - **Priority** — treated within 4 hours
    - **Routine** — treated within 24 hours

    Two surgical teams are available. Implement **priority pre-emption** in the MEDEVAC queue. Run **50 replications**. Report 95% CIs on:
    - Overall survival rate (time-to-treatment determines survival probability)
    - Peak queue length per triage category

    Visualize with **plotly** (interactive time-series with CI bands).

```python
# pip install simpy numpy scipy plotly
import simpy
import numpy as np
from scipy import stats
import plotly.graph_objects as go
from plotly.subplots import make_subplots

# ── Parameters ─────────────────────────────────────────────────
SIM_HOURS       = 72
N_SURGICAL_TEAMS = 2
N_REPLICATIONS  = 50

# Phase schedule: (start_hour, end_hour, total_casualty_rate_per_hour)
PHASES = [
    (0,  6,  2.0),    # initial assault — high casualties
    (6,  18, 0.8),    # consolidation
    (18, 24, 3.5),    # enemy counterattack — surge
    (24, 48, 1.0),    # sustained operations
    (48, 66, 0.5),    # wind-down
    (66, 72, 1.5),    # extraction under fire
]

# Triage probabilities and treatment durations (hours)
TRIAGE = {
    "urgent":   {"prob": 0.20, "treatment_mean": 2.0, "survival_curve": lambda w: np.exp(-w / 1.0)},
    "priority": {"prob": 0.35, "treatment_mean": 1.5, "survival_curve": lambda w: np.exp(-w / 4.0)},
    "routine":  {"prob": 0.45, "treatment_mean": 0.75,"survival_curve": lambda w: np.exp(-w / 24.0)},
}
PRIORITY_ORDER = {"urgent": 0, "priority": 1, "routine": 2}

def run_combat_sim(seed: int):
    """Single replication. Returns (survival_rate, peak_queues_by_category)."""
    rng = np.random.default_rng(seed)
    env = simpy.Environment()

    surgical_teams = simpy.PriorityResource(env, capacity=N_SURGICAL_TEAMS)
    survival_outcomes = []
    queue_lengths = {"urgent": [], "priority": [], "routine": []}
    current_queues = {"urgent": 0, "priority": 0, "routine": 0}

    def casualty_process(category: str):
        current_queues[category] += 1
        arrival_time = env.now

        priority_val = PRIORITY_ORDER[category]
        with surgical_teams.request(priority=priority_val) as req:
            yield req
            wait_hours = (env.now - arrival_time) / 60   # convert min → hours
            current_queues[category] -= 1

            # Treatment
            treat_dur = rng.exponential(
                scale=TRIAGE[category]["treatment_mean"]
            ) * 60   # hours → minutes
            yield env.timeout(treat_dur)

            # Determine survival
            p_survive = TRIAGE[category]["survival_curve"](wait_hours)
            survived  = rng.random() < p_survive
            survival_outcomes.append(survived)

        # Record peak queue every 30 sim minutes
        for cat in TRIAGE:
            queue_lengths[cat].append(current_queues[cat])

    def arrival_generator():
        """Non-homogeneous Poisson: step through phase schedule."""
        for (start_h, end_h, rate_per_h) in PHASES:
            start_min = start_h * 60
            end_min   = end_h   * 60
            # Advance to phase start if needed
            if env.now < start_min:
                yield env.timeout(start_min - env.now)

            while env.now < end_min:
                # Exponential inter-arrival within phase
                iat = rng.exponential(scale=60 / rate_per_h)   # minutes
                if env.now + iat > end_min:
                    yield env.timeout(end_min - env.now)
                    break
                yield env.timeout(iat)
                # Assign triage category
                probs = [TRIAGE[c]["prob"] for c in TRIAGE]
                cat   = rng.choice(list(TRIAGE.keys()), p=probs)
                env.process(casualty_process(cat))

    env.process(arrival_generator())
    env.run(until=SIM_HOURS * 60)

    survival_rate = np.mean(survival_outcomes) if survival_outcomes else 0.0
    peak_queues   = {cat: max(queue_lengths[cat], default=0) for cat in TRIAGE}
    return survival_rate, peak_queues


# ── Run replications ───────────────────────────────────────────
survival_rates = []
peak_queues_all = {"urgent": [], "priority": [], "routine": []}

for rep in range(N_REPLICATIONS):
    sr, pq = run_combat_sim(seed=rep * 37)
    survival_rates.append(sr)
    for cat in TRIAGE:
        peak_queues_all[cat].append(pq[cat])

# ── Compute CIs ────────────────────────────────────────────────
def ci95(data):
    arr = np.array(data, dtype=float)
    mean = arr.mean()
    lo, hi = stats.t.interval(0.95, df=len(arr)-1, loc=mean, scale=stats.sem(arr))
    return mean, lo, hi

sr_mean, sr_lo, sr_hi = ci95(survival_rates)
print(f"\nSurvival Rate: {sr_mean:.1%}  95% CI [{sr_lo:.1%}, {sr_hi:.1%}]")
print("\nPeak Queue Lengths (95% CI):")
for cat in TRIAGE:
    m, lo, hi = ci95(peak_queues_all[cat])
    print(f"  {cat:<10} mean={m:.1f}  CI [{lo:.1f}, {hi:.1f}]")

# ── Plotly: survival rate distribution + peak queue CIs ────────
fig = make_subplots(rows=1, cols=2,
                    subplot_titles=["Survival Rate Distribution",
                                    "Peak Queue Length by Category (95% CI)"])

# Histogram of survival rates
fig.add_trace(go.Histogram(
    x=survival_rates, nbinsx=20, name="Survival rate",
    marker_color="steelblue", opacity=0.8
), row=1, col=1)
fig.add_vline(x=sr_mean, line_dash="dash", line_color="red",
              annotation_text=f"Mean {sr_mean:.1%}", row=1, col=1)

# Bar chart with CI error bars for peak queues
categories = list(TRIAGE.keys())
ci_results = {c: ci95(peak_queues_all[c]) for c in categories}
means  = [ci_results[c][0] for c in categories]
errors = [ci_results[c][0] - ci_results[c][1] for c in categories]

fig.add_trace(go.Bar(
    x=categories, y=means,
    error_y=dict(type="data", array=errors, visible=True),
    marker_color=["crimson", "orange", "steelblue"],
    name="Peak queue"
), row=1, col=2)

fig.update_layout(
    title="Combat Casualty Simulation — 50 Replications",
    showlegend=False, height=450
)
fig.write_html("combat_casualty.html")
fig.show()
print("\nInteractive chart saved to combat_casualty.html")
```

---

### Check Your Understanding — Part 3

!!! question "Question 7"
    In Exercise 1, the analytical formula $W_q = \rho/(\mu - \lambda)$ assumes the system is in steady state. What role does the warm-up period (`WARMUP = 500`) play, and what happens to the simulated $W_q$ if you remove it?

!!! question "Question 8"
    In Exercise 2, antithetic variates use $(u, 1-u)$ pairs. The indicator function $\mathbf{1}[u_1^2 + u_2^2 \leq 1]$ is a monotone function of the inputs. Why does monotonicity matter for the antithetic variates method to be effective?

!!! question "Question 9"
    In Exercise 3, `simpy.PriorityResource` is used so urgent cases pre-empt lower-priority patients for surgical team access. What modeling limitation does this introduce, and how would you modify the simulation to capture the reality that a surgery already in progress cannot be interrupted?

---

## Summary

| Concept | Key Takeaway |
|---------|-------------|
| DES vs. Monte Carlo | DES for time-ordered events; MC for independent sampling |
| Warm-up period | Discard transient data before collecting statistics |
| Replication-deletion | Multiple i.i.d. runs → valid confidence intervals |
| Common random numbers | Same seeds across alternatives → variance of *differences* decreases |
| Antithetic variates | Pair $U$ with $1-U$ → negative correlation → lower variance |
| Verification | Code correctness: extreme-value tests, entity conservation |
| Validation | Model realism: compare to historical data + expert review |

---

!!! tip "Prompt Template"
    Use the [Agent Prompt](prompt.md) for this topic, or the [Generic Prompt Template](../prompt-template.md) to generate additional lectures, examples, and exercises.
