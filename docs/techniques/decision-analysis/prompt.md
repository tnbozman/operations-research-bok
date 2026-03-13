# Agent Prompt — Decision Analysis

> Copy the block below and paste it directly to your AI agent to build out this section.

---

```text
You are an Operations Research instructor creating a complete, interactive,
hands-on learning module for **Decision Analysis**.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PART 1 — LECTURE (Conceptual Foundation)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Cover the following:

1. The decision analysis cycle: frame → model → solve → interpret → iterate.
   Why structuring the problem is harder than solving it.
2. Payoff matrices and decision criteria: EMV, Maximin, Maximax, Minimax
   Regret, and Hurwicz criterion. Derive each from first principles on the
   same worked 3×3 example so learners can directly compare.
3. Decision trees: nodes (decision □, chance ○, terminal ◇), backward
   induction (fold-back), and how sequential decisions are modelled.
   Walk through a 3-stage tree by hand.
4. EVPI (Expected Value of Perfect Information): derivation and operational
   interpretation as an upper bound on intelligence value.
5. EVSI (Expected Value of Sample Information): Bayesian updating with
   imperfect information sources. Step-by-step Bayes' theorem application.
6. Utility theory: risk-neutral, risk-averse (concave), risk-seeking (convex)
   utility functions. Certainty equivalent and risk premium. Eliciting a
   utility curve.
7. Multi-Attribute Utility Theory (MAUT): when decisions have multiple
   incommensurable objectives (cost, speed, risk, political feasibility).
8. Sensitivity analysis: one-way, two-way, and tornado diagrams.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PART 2 — REAL-WORLD PRACTICAL EXAMPLES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Example A — Military: Intelligence-Driven Strike Decision
  Scenario: A commander must decide to Strike, Conduct ISR, or Hold
  before acting. Enemy can be Present or Absent (prior probabilities given).
  ISR gives imperfect signal (probabilities of detection/false-alarm given).
  Payoffs reflect mission success, collateral damage, and opportunity cost.
  - Build and solve the full 3-stage decision tree using Python (networkx
    or a custom recursive tree structure).
  - Compute EVPI and EVSI from the ISR asset.
  - Determine: is the ISR sortie worth the time cost?
  - Visualize the decision tree using matplotlib or graphviz.
  - Perform one-way sensitivity analysis on prior P(enemy present).
    At what threshold does the decision flip?

Example B — Civilian: Pharmaceutical Drug Development Decision
  Scenario: A biotech firm must decide whether to advance a drug through
  Phase II trials (costly, uncertain) or license it out (smaller guaranteed
  return). If Phase II succeeds (p=0.4), Phase III decision follows.
  - Build the sequential decision tree in Python.
  - Apply a risk-averse utility function (exponential utility).
  - Compare EMV-optimal vs. utility-optimal decisions.
  - Plot a tornado diagram showing which probability assumption most affects
    the decision.

All code complete, runnable, with pip install instructions and inline comments.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PART 3 — INTERACTIVE EXERCISES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Exercise 1 — Guided (Warm-Up): Payoff Matrix Analysis
  Provide a 3×4 payoff matrix (3 actions, 4 states of nature with given
  probabilities) and a Python skeleton with TODO blocks for the learner to:
  - Compute EMV for each action.
  - Find Maximin and Minimax Regret decisions.
  - Compute EVPI.

Exercise 2 — Exploratory: COA Analysis with Sensitivity
  A battalion commander chooses between COA A, B, or C against 3 possible
  enemy courses of action (with estimated probabilities). Learner must:
  - Build the payoff matrix.
  - Solve with all 5 criteria.
  - Perform a sensitivity sweep: vary P(enemy COA 1) from 0.1 to 0.9 and
    plot how the recommended COA changes. Identify the breakeven probabilities.

Exercise 3 — Challenge: Full Multi-Stage Decision Tree with Bayesian Updating
  An analyst can collect intelligence at two levels (tactical recon: cheap,
  noisy; strategic ISR: expensive, reliable) before recommending action.
  Learner must:
  - Model all decision and chance nodes.
  - Implement backward induction recursively in Python.
  - Apply Bayes' theorem at each information node.
  - Build a MAUT scoring function with 3 attributes (mission success,
    friendly casualties, collateral damage). Elicit weights interactively.
  - Visualize the full tree and highlight the optimal path.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
FORMAT & TOOLBOX
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Primary tools: Python (NumPy, matplotlib), graphviz (python-graphviz),
optionally pymc for Bayesian computations.
Output format: MkDocs-compatible Markdown with admonitions, fenced code
blocks, KaTeX math.
Add "Check Your Understanding" (3 questions) after each Part.
All code must run with Python 3.10+ and only open-source packages.
```
