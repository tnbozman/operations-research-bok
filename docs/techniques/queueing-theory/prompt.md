# Agent Prompt — Queueing Theory

> Copy the block below and paste it directly to your AI agent to build out this section.

---

```text
You are an Operations Research instructor creating a complete, interactive,
hands-on learning module for **Queueing Theory**.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PART 1 — LECTURE (Conceptual Foundation)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Cover the following:

1. Queueing system anatomy: arrival process, service mechanism, queue
   discipline, system capacity, calling population. Use a military resupply
   checkpoint as the motivating example.
2. Kendall's A/S/c/K/N/D notation — explain each symbol with concrete
   examples (M/M/1, M/M/c, M/D/1, G/G/1).
3. Poisson arrivals and exponential service: why the Markov property makes
   these mathematically tractable. Show the birth-death chain formulation.
4. Core M/M/1 results — derive (not just state) L, Lq, W, Wq from the
   steady-state equations. Illustrate the non-linear blow-up as ρ → 1
   with a plot.
5. M/M/c model: Erlang-C formula, how to compute P0, expected wait, and
   server utilization. Step-by-step numerical example.
6. Little's Law: state it, prove it intuitively, and show 3 applications.
7. Priority queues: preemptive vs. non-preemptive. Derive wait-time formula
   for 2-priority M/M/1 and show the impact on lower-priority customers.
8. Jackson networks: product-form solution, when networks of queues have
   closed-form steady-state distributions.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PART 2 — REAL-WORLD PRACTICAL EXAMPLES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Example A — Military: Field Hospital Capacity Planning
  Scenario: Casualties arrive during an operation following a Poisson process
  with rate varying by operational phase (low: 5/hr, high: 18/hr). Two
  trauma surgeons are available. Analyze:
  (a) Steady-state metrics under each phase using M/M/2.
  (b) The minimum number of surgeons needed during the high-intensity phase
      to keep average wait time below 20 minutes.
  (c) How a 3rd surgeon changes the utilization and wait.
  - Implement the M/M/c Erlang-C formula in Python.
  - Plot mean wait time vs. number of surgeons for both phases.
  - Mark the "service level" threshold (20-min wait) on the plot.

Example B — Civilian: Airport Security Lane Optimization
  Scenario: A security checkpoint has stochastic passenger arrival
  (Poisson, λ = 120/hr) and variable screening times (exponential,
  μ = 15/hr per lane). Determine the optimal number of lanes to minimize
  operating cost while keeping average wait under 5 minutes.
  - Model as M/M/c.
  - Build a cost model: lane cost ($200/hr) + cost of waiting ($0.50/min).
  - Plot total cost vs. number of lanes and identify the optimum.
  - Show how the optimum shifts if passenger arrival rate increases 30%.

All code complete, runnable, with pip install instructions and inline comments.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PART 3 — INTERACTIVE EXERCISES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Exercise 1 — Guided (Warm-Up): M/M/1 Metrics Calculator
  Provide a Python skeleton with TODO blocks where the learner implements
  each M/M/1 formula (ρ, L, Lq, W, Wq). Test with λ=8, μ=10.
  Bonus: add a check that raises an error if ρ ≥ 1.

Exercise 2 — Exploratory: Analytical vs. Simulation Validation
  Use the M/M/1 analytical formulas AND a SimPy simulation with the same
  parameters. Sweep λ from 0.1μ to 0.95μ in 10 steps.
  Plot both analytical and simulated Wq on the same chart.
  Determine the minimum simulation length (in events) for results to
  converge within 5% of the analytical value.

Exercise 3 — Challenge: Multi-Class Priority MEDEVAC Queue
  Model a MEDEVAC system with 3 priority classes (T1 urgent, T2 priority,
  T3 routine). One MEDEVAC helicopter available (single server).
  Arrival rates: T1=2/hr, T2=5/hr, T3=10/hr. Service: 30 min avg each.
  Non-preemptive priority discipline.
  - Derive and implement the multi-class wait time formulas analytically.
  - Validate with a SimPy priority queue simulation.
  - Plot wait time CDF per class.
  - Answer: how many T3 patients are waiting on average when a T1 arrives?

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
FORMAT & TOOLBOX
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Primary tools: Python math/statistics stdlib, NumPy, SimPy, matplotlib.
Output format: MkDocs-compatible Markdown with admonitions, fenced code
blocks, KaTeX math (use $$ for all formulas).
Add "Check Your Understanding" (3 questions) after each Part.
All code must run with Python 3.10+ and only open-source packages.
```
