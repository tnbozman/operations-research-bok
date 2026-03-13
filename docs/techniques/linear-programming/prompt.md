# Agent Prompt — Linear Programming

> Copy the block below and paste it directly to your AI agent to build out this section.

---

```text
You are an Operations Research instructor creating a complete, interactive,
hands-on learning module for **Linear Programming (LP)**.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PART 1 — LECTURE (Conceptual Foundation)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Cover the following in a structured, engineer-friendly format:

1. Plain-language definition, then formal definition using standard LP notation
   (objective function, constraints, decision variables, feasible region).
2. Mathematical formulation: standard form, canonical form, and the role of
   slack/surplus/artificial variables.
3. The Simplex algorithm — step-by-step walkthrough on a small 2-variable
   example that can be visualized geometrically.
4. Duality theory: primal-dual pairs, complementary slackness, shadow prices,
   and their operational interpretation.
5. Software landscape: when to use PuLP vs. Pyomo vs. SciPy vs. OR-Tools.
6. Limitations and failure modes: unbounded problems, infeasibility, degeneracy.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PART 2 — REAL-WORLD PRACTICAL EXAMPLES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Example A — Military: Multi-Echelon Resource Allocation
  Scenario: A theater commander must allocate aviation assets (attack
  helicopters, transport helicopters, UAVs) across three operational axes
  subject to fuel, crew hours, and maintenance constraints. Maximize
  expected mission effectiveness.
  - Formulate the LP fully (define variables, objective, all constraints).
  - Solve using PuLP with the HiGHS solver.
  - Print dual/shadow prices and interpret: "what is the marginal value
    of one extra crew-hour?"
  - Perform a right-hand side sensitivity analysis using PuLP's
    constraint dual values.

Example B — Civilian: Blending / Diet Problem
  Scenario: A food manufacturer must blend raw ingredients to meet
  nutritional requirements at minimum cost.
  - Formulate and solve with SciPy linprog (HiGHS backend).
  - Visualize the 2-variable feasible region and optimal point using
    matplotlib (shaded polygon, iso-profit lines).
  - Discuss sensitivity: how much can ingredient cost change before the
    optimal blend changes?

All code must be complete, runnable, and include pip install instructions.
Use inline comments to explain each modelling step.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PART 3 — INTERACTIVE EXERCISES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Exercise 1 — Guided (Warm-Up): Transportation Problem
  Provide starter PuLP code with TODO blocks for the learner to:
  - Define supply/demand variables
  - Set the objective
  - Add supply and demand constraints
  Include the expected optimal cost and flow table as expected output.

Exercise 2 — Exploratory: Logistics Network What-If
  Pose a 3-warehouse / 5-customer distribution problem. Ask the learner to:
  - Solve the base case.
  - Remove the highest-utilization route and re-solve.
  - Quantify the cost impact and explain using shadow prices.

Exercise 3 — Challenge: Multi-Period Production Planning
  A manufacturer plans production over 6 periods with holding costs,
  demand variability, and capacity constraints. Learner must:
  - Formulate a multi-period LP.
  - Solve and plot production + inventory levels over time.
  - Perform a parametric analysis on holding cost rate.
  - Interpret the dual variables in operational terms.
  Bonus: convert to Pyomo and solve with the GLPK solver.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
FORMAT & TOOLBOX
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Primary tools: PuLP, SciPy (linprog/HiGHS), Pyomo, NumPy, matplotlib.
Output format: MkDocs-compatible Markdown with admonitions (tip, warning,
note, example), fenced code blocks with language tags, KaTeX math.
Add "Check Your Understanding" (3 questions) after each Part.
All code must run with Python 3.10+ and only open-source packages.
```
