# Agent Prompt — Simulation

> Copy the block below and paste it directly to your AI agent to build out this section.

---

```text
You are an Operations Research instructor creating a complete, interactive,
hands-on learning module for **Simulation** (Discrete-Event Simulation and
Monte Carlo methods).

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PART 1 — LECTURE (Conceptual Foundation)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Cover the following:

1. When to simulate vs. use analytical models — the key trade-offs of
   tractability vs. fidelity.
2. Taxonomy of simulation types:
   - Discrete-Event Simulation (DES): event calendar, SimPy process model,
     resource management.
   - Monte Carlo: random sampling, law of large numbers, convergence.
   - Agent-Based Modeling: emergent behavior, when ABM beats DES.
   - System Dynamics: stocks, flows, feedback loops (brief overview).
3. Random variate generation: inverse CDF transform, acceptance-rejection.
   Show how to sample from Uniform, Exponential, Normal, Triangular,
   and Poisson distributions in Python/NumPy.
4. Output analysis: warm-up period (transient removal), replication-deletion
   method, batch means. How to compute confidence intervals on simulation
   KPIs.
5. Variance reduction techniques: common random numbers, antithetic variates,
   control variates — explain with intuition and code examples.
6. Verification vs. Validation: how to test that (a) the code is correct and
   (b) the model represents reality.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PART 2 — REAL-WORLD PRACTICAL EXAMPLES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Example A — Military: Airfield Sortie Generation Simulation
  Scenario: Simulate an airfield with 2 runways, 20 aircraft of 3 types,
  stochastic maintenance requirements (exponential TTF/TTR), random
  weather disruptions (Poisson events), and a surge operations schedule.
  Track daily sortie rate, aircraft utilization, and maintenance backlog
  across a 30-day campaign.
  - Build with SimPy (processes for aircraft, maintenance crews, runways).
  - Run 20 replications.
  - Compute 95% confidence intervals on mean daily sortie rate.
  - Plot sortie rate over time (mean ± CI band) using matplotlib.
  - Identify the binding constraint (runway, maintenance, or weather).

Example B — Civilian: Supply Chain Risk via Monte Carlo
  Scenario: A 5-stage supply chain from raw material to retail. Each stage
  has uncertain lead time (triangular distribution) and a probability of
  disruption. Estimate the probability that total lead time exceeds 30 days
  and the 95th-percentile lead time.
  - Implement with NumPy (vectorized, 100,000 trials).
  - Plot histogram of total lead time with risk thresholds marked.
  - Tornado chart: which stage's uncertainty contributes most to variance?
  - Demonstrate variance reduction using common random numbers.

All code must be complete, runnable, include pip install instructions,
and use inline comments.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PART 3 — INTERACTIVE EXERCISES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Exercise 1 — Guided (Warm-Up): M/M/1 Queue via SimPy
  Starter code with TODO blocks for the learner to:
  - Implement the customer arrival generator (Poisson process).
  - Implement the service process (exponential service time).
  - Track and report mean wait time and queue length.
  - Compare results with the M/M/1 analytical formula.

Exercise 2 — Exploratory: Monte Carlo Pi Estimation
  Ask the learner to estimate π by sampling random points in a unit
  square, then:
  - Plot convergence of the estimate vs. sample size (log scale x-axis).
  - Quantify how many samples are needed for 4 decimal places of accuracy.
  - Apply antithetic variates and compare convergence speed.

Exercise 3 — Challenge: Combat Casualty Simulation
  Simulate a 72-hour ground combat operation with:
  - Stochastic casualty arrivals (non-homogeneous Poisson by phase).
  - MEDEVAC queue (priority: urgent, priority, routine).
  - Limited medical treatment capacity (2 surgical teams).
  - Resupply events that affect medical consumables.
  Run 50 replications. Report confidence intervals on:
  - Survival rate (function of time-to-treatment).
  - Peak queue length per category.
  Visualize with plotly (interactive time-series with CI bands).

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
FORMAT & TOOLBOX
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Primary tools: SimPy, NumPy, SciPy, matplotlib, plotly.
Output format: MkDocs-compatible Markdown with admonitions, fenced code
blocks, KaTeX math.
Add "Check Your Understanding" (3 questions) after each Part.
All code must run with Python 3.10+ and only open-source packages.
```
