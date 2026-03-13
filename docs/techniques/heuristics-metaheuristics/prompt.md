# Agent Prompt — Heuristics & Metaheuristics

> Copy the block below and paste it directly to your AI agent to build out this section.

---

```text
You are an Operations Research instructor creating a complete, interactive,
hands-on learning module for **Heuristics and Metaheuristics**.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PART 1 — LECTURE (Conceptual Foundation)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Cover the following:

1. Why exact methods fail on large instances: NP-hardness, exponential
   scaling, and the practical need for good-enough solutions fast.
2. Heuristic vs. metaheuristic: what each term means precisely. The role
   of problem-specific knowledge in pure heuristics.
3. Constructive heuristics: greedy nearest-neighbor for TSP, greedy
   knapsack filling. Analyse solution quality (approximation ratio).
4. Local search: neighbourhood structures (2-opt, 3-opt, Or-opt), hill
   climbing, and the local optimum trap.
5. Simulated Annealing (SA): thermodynamic analogy, Metropolis criterion,
   cooling schedules (geometric, adaptive). Convergence theory.
6. Tabu Search: short-term memory (tabu list), aspiration criteria,
   long-term memory (frequency, recency). Key parameters to tune.
7. Genetic Algorithms (GA): representation, fitness, selection (tournament,
   roulette), crossover (order, PMX), mutation (swap, inversion), elitism.
   Schema theorem intuition.
8. Particle Swarm Optimization (PSO): particle positions/velocities, personal
   and global best, social vs. cognitive components.
9. Ant Colony Optimization (ACO): pheromone deposition and evaporation,
   construction heuristic, AS vs. ACS variants.
10. No Free Lunch theorem: implications for algorithm selection and the
    importance of problem-specific tuning.
11. Benchmarking and comparison: how to statistically compare metaheuristics
    (Wilcoxon test, convergence plots, box plots).

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PART 2 — REAL-WORLD PRACTICAL EXAMPLES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Example A — Military: UAV Waypoint Tour Optimization (TSP)
  Scenario: A reconnaissance UAV must visit 30 waypoints and return to base
  minimizing total flight distance (fuel/time). Exact solver is too slow
  for real-time replanning.
  - Implement and compare: Nearest-Neighbour greedy + 2-opt local search
    vs. Genetic Algorithm (DEAP) vs. Simulated Annealing.
  - For each, plot the best route on a 2D map using matplotlib.
  - Plot convergence curves (best solution vs. iteration).
  - Report: solution quality vs. computation time trade-off table.
  - Discuss: which method is most suitable for embedded real-time replanning?

Example B — Civilian: Hospital Staff Scheduling
  Scenario: Schedule 20 nurses across 7-day, 3-shift hospital rota.
  Each nurse has availability constraints and preferences. Minimize
  cost (overtime) while satisfying minimum coverage per shift.
  - Model the problem as a permutation/assignment optimization.
  - Solve with Tabu Search (implement from scratch).
  - Compare with a GA solution (DEAP).
  - Visualize the final schedule as a colour-coded heatmap (nurse × day/shift).
  - Report constraint violations and overtime cost.

All code complete, runnable, with pip install instructions and inline comments.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PART 3 — INTERACTIVE EXERCISES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Exercise 1 — Guided (Warm-Up): 2-opt TSP Local Search
  Provide a starter Python skeleton with TODO blocks for the learner to:
  - Implement the 2-opt swap operation.
  - Implement the hill-climbing loop (keep swapping while improvement found).
  - Run on a 20-city random instance.
  - Report initial (random) tour distance vs. 2-opt improved distance.

Exercise 2 — Exploratory: Simulated Annealing Parameter Tuning
  Given a 50-city TSP instance, the learner experiments with SA:
  - Vary initial temperature T0 ∈ {10, 100, 1000}.
  - Vary cooling rate α ∈ {0.99, 0.995, 0.999}.
  - Run each combination 5 times; plot a 3×3 grid of convergence curves.
  - Identify the best parameter combination and explain why it works.

Exercise 3 — Challenge: Multi-Objective Sensor Placement
  Place k=8 sensors on a 50×50 grid to maximize coverage of 100 target
  points while minimizing mutual interference (sensors too close).
  This is a bi-objective problem.
  - Implement NSGA-II (use DEAP's built-in implementation) to approximate
    the Pareto front.
  - Plot the Pareto front (coverage vs. interference trade-off).
  - For each Pareto-optimal solution, visualize sensor positions on the grid.
  - Discuss which solution a military planner would prefer and why.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
FORMAT & TOOLBOX
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Primary tools: DEAP (GA, PSO, NSGA-II), NumPy, matplotlib.
Output format: MkDocs-compatible Markdown with admonitions, fenced code
blocks, KaTeX math.
Add "Check Your Understanding" (3 questions) after each Part.
All code must run with Python 3.10+ and only open-source packages.
```
