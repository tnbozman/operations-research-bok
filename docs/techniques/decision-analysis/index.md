# Decision Analysis

> Structuring decisions under uncertainty using probability and utility theory.

---

## Lecture

### What is Decision Analysis?

Decision Analysis (DA) is a systematic framework for making decisions under uncertainty. It structures the problem into **decisions** (what we control), **uncertainties** (what we don't), and **outcomes** (what we value), then uses probability theory and utility functions to recommend the best course of action.

### Core Framework

A decision problem consists of:

1. **Decision alternatives** — the available courses of action
2. **Uncertain events** — states of nature with associated probabilities
3. **Consequences** — outcomes for each decision-event combination
4. **Value model** — a utility function capturing decision-maker preferences

### Key Concepts

- **Expected Monetary Value (EMV)** — probability-weighted average payoff
- **Decision trees** — graphical representation of sequential decisions
- **Expected Value of Perfect Information (EVPI)** — upper bound on what information is worth
- **Expected Value of Sample Information (EVSI)** — value of partial information
- **Utility functions** — capturing risk attitude (risk-averse, risk-neutral, risk-seeking)
- **Multi-attribute utility** — decisions with multiple competing objectives
- **Bayes' theorem** — updating probabilities with new evidence
- **Sensitivity analysis** — how does the decision change with assumptions?

### Decision Criteria

| Criterion | Description |
|-----------|-------------|
| **Maximax** | Choose the alternative with the best possible outcome (optimist) |
| **Maximin** | Choose the alternative with the best worst-case outcome (pessimist) |
| **Expected Value** | Maximize probability-weighted payoff |
| **Minimax Regret** | Minimize the maximum opportunity cost |
| **Hurwicz** | Weighted combination of optimistic and pessimistic outcomes |

### Historical Context

Decision analysis emerged from utility theory (von Neumann & Morgenstern, 1944) and was formalized by Howard Raiffa and Ron Howard in the 1960s. It is used extensively in military course-of-action analysis and in industries with large capital-at-risk decisions (oil & gas, pharma).

### Relationship to Other OR Techniques

- Closely related to **Game Theory** (adversarial uncertainty rather than nature)
- Uses **Stochastic Processes** for sequential decisions (MDPs)
- Informed by **Simulation** for complex outcome distributions
- Often contrasted with **Linear/Integer Programming** (optimization vs. decision structuring)

---

## Real-World Examples

### Example A — Military: Course of Action Analysis

!!! example "Scenario"
    A battalion commander must choose between three courses of action (COAs) for an offensive operation. Each has different outcomes depending on enemy disposition (which is uncertain). Use a payoff matrix and decision criteria to recommend a COA.

**Open-Source Tools:** Python, NumPy

```python
# pip install numpy
import numpy as np

# Payoff matrix: rows = COAs, cols = enemy states
# Values represent mission success score (0-100)
coas = ["COA 1: Frontal Assault", "COA 2: Flanking Maneuver", "COA 3: Air Assault"]
enemy_states = ["Defensive", "Retrograde", "Counterattack"]
probabilities = [0.4, 0.35, 0.25]

payoff = np.array([
    [60, 80, 30],  # COA 1
    [75, 70, 55],  # COA 2
    [50, 90, 45],  # COA 3
])

print("Decision Analysis: COA Comparison")
print("=" * 55)

# Expected Value
ev = payoff @ probabilities
for i, coa in enumerate(coas):
    print(f"  {coa}: EV = {ev[i]:.1f}")
print(f"  → EV Best: {coas[np.argmax(ev)]}\n")

# Maximin (pessimist)
worst = payoff.min(axis=1)
print("  Maximin (worst-case):")
for i, coa in enumerate(coas):
    print(f"    {coa}: worst = {worst[i]}")
print(f"  → Maximin Best: {coas[np.argmax(worst)]}\n")

# EVPI
ev_with_info = sum(p * payoff[:, j].max() for j, p in enumerate(probabilities))
evpi = ev_with_info - ev.max()
print(f"  EVPI = {evpi:.1f} (max value of gathering intel)")
```

### Example B — Civilian: New Product Launch Decision

!!! example "Scenario"
    A company must decide whether to launch a new product, conduct a market study first, or abandon the project. Model as a decision tree with sequential decisions and uncertain market outcomes.

**Open-Source Tools:** Python

```python
# Simple decision tree evaluation

# Direct launch outcomes
p_success = 0.6
profit_success = 500_000
profit_failure = -200_000

ev_launch = p_success * profit_success + (1 - p_success) * profit_failure
ev_abandon = 0

# Market study branch (costs $50,000)
study_cost = 50_000
# Study can be Favorable (70% of time) or Unfavorable (30%)
p_favorable = 0.7
# Updated probabilities after study
p_success_given_fav = 0.85
p_success_given_unfav = 0.2

ev_launch_fav = p_success_given_fav * profit_success + (1 - p_success_given_fav) * profit_failure
ev_launch_unfav = p_success_given_unfav * profit_success + (1 - p_success_given_unfav) * profit_failure

# At each study outcome, choose best option (launch vs abandon)
best_fav = max(ev_launch_fav, 0)  # launch if positive, else abandon
best_unfav = max(ev_launch_unfav, 0)

ev_study = p_favorable * best_fav + (1 - p_favorable) * best_unfav - study_cost

print("Decision Tree Analysis")
print("=" * 45)
print(f"  EV(Launch directly):  ${ev_launch:>12,.0f}")
print(f"  EV(Abandon):          ${ev_abandon:>12,.0f}")
print(f"  EV(Market study):     ${ev_study:>12,.0f}")
print(f"\n  → Best decision: ", end="")
best = max([("Launch", ev_launch), ("Abandon", ev_abandon), ("Study", ev_study)],
           key=lambda x: x[1])
print(f"{best[0]} (EV = ${best[1]:,.0f})")
```

---

## Interactive Exercises

### Exercise 1 — Guided (Warm-Up)

!!! question "Task"
    Build a payoff matrix for a logistics decision: choosing between 3 shipping methods under 3 weather scenarios. Calculate EMV for each alternative. Fill in the TODOs.

```python
import numpy as np

methods = ["Ground", "Air", "Sea"]
weather = ["Clear", "Storm", "Fog"]
probabilities = [0.5, 0.3, 0.2]

# TODO: Define the payoff matrix (cost savings, higher is better)
# payoff = np.array([...])

# TODO: Calculate expected value for each method
# ev = ...

# TODO: Identify the best method by EMV
# best = ...
# print(f"Best method: {best}")
```

### Exercise 2 — Exploratory (Intermediate)

!!! question "Task"
    Implement a sensitivity analysis for the military COA problem above. Vary the probability of "Counterattack" from 0.05 to 0.50 and plot how each COA's expected value changes. At what probability does the recommended COA switch?

### Exercise 3 — Challenge (Advanced)

!!! question "Task"
    Model a multi-stage intelligence collection decision problem as a decision tree with Bayesian updating. An analyst can order a satellite pass (imperfect info), then decide whether to task a drone (better info), then recommend a strike/no-strike. Implement the full tree, compute EVSI at each stage, and visualize the tree structure using graphviz or matplotlib.

---

## Check Your Understanding

1. What is the difference between EVPI and EVSI?
2. How does a risk-averse decision-maker's choice differ from the EMV-maximizing choice?
3. When is minimax regret preferable to expected value?

---

!!! tip "Prompt Template"
    Use the [Agent Prompt](prompt.md) for this topic, or the [Generic Prompt Template](../prompt-template.md) to generate additional lectures, examples, and exercises.
