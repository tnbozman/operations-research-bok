# Agent Prompt — Game Theory

> Copy the block below and paste it directly to your AI agent to build out this section.

---

```text
You are an Operations Research instructor creating a complete, interactive,
hands-on learning module for **Game Theory**.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PART 1 — LECTURE (Conceptual Foundation)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Cover the following:

1. What makes a situation a "game": players, strategies, payoffs, information,
   timing. Contrast with Decision Analysis (nature vs. adversary).
2. Normal (strategic) form vs. extensive (tree) form — when to use each.
3. Dominant strategies and iterated elimination of dominated strategies (IESDS).
   Walk through a military example where iterative reasoning reduces a large
   strategy space.
4. Nash Equilibrium (NE): formal definition, existence theorem (Nash 1950),
   uniqueness vs. multiplicity. Prove the mixed-strategy NE for Matching
   Pennies by indifference condition.
5. Zero-sum games: minimax theorem, saddle points, how to solve for mixed
   strategies via LP. Connect to military "red team" analysis.
6. Non-zero-sum games: Prisoner's Dilemma (defection dominates, but
   cooperation is efficient), Pareto efficiency, coordination games.
7. Repeated games: Folk theorem, how repetition enables cooperation.
   Tit-for-Tat and its properties.
8. Stackelberg games: leader-follower structure, first-mover advantage.
   Application to force-on-force sequential decisions.
9. Auctions as games: sealed-bid, ascending, dominant strategies in
   second-price (Vickrey) auctions.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PART 2 — REAL-WORLD PRACTICAL EXAMPLES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Example A — Military: Colonel Blotto Force Allocation Game
  Scenario: Two commanders each allocate 120 combat power units across 5
  battlefields simultaneously. A commander wins a battlefield if they commit
  more forces there; total score = battlefields won.
  - Formulate as a symmetric zero-sum game.
  - Enumerate a large strategy sample via Monte Carlo (random allocations).
  - Approximate Nash equilibrium via iterative best-response or replicator
    dynamics.
  - Visualize: heatmap of win rates for different allocation splits.
  - Discuss: why uniform allocation is often near-optimal in symmetric Blotto.

Example B — Civilian: Competitive Pricing Stackelberg Game
  Scenario: A market leader (Firm A) sets price first; a follower (Firm B)
  observes and responds. Model demand curves, compute the Stackelberg
  equilibrium, and compare to the Nash (simultaneous) equilibrium and
  cooperative (cartel) outcome.
  - Solve analytically with SymPy.
  - Plot reaction functions and mark all three equilibria.
  - Compute and compare profits at each equilibrium.
  - Discuss the first-mover advantage quantitatively.

All code complete, runnable, with pip install instructions and inline comments.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PART 3 — INTERACTIVE EXERCISES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Exercise 1 — Guided (Warm-Up): Nash Equilibrium Finder
  Provide a Python function skeleton that takes two payoff matrices (A, B)
  and returns all pure-strategy Nash equilibria. TODO blocks for:
  - Testing if strategy (i,j) is a best response for Player 1.
  - Testing if strategy (i,j) is a best response for Player 2.
  Test on: Prisoner's Dilemma, Battle of the Sexes, Matching Pennies,
  Stag Hunt. Report findings and verify with expected theory results.

Exercise 2 — Exploratory: Repeated Prisoner's Dilemma Tournament
  Implement an Axelrod-style tournament with at least 6 strategies:
  Always Cooperate, Always Defect, Tit-for-Tat, Grudger, Pavlov, Random.
  Round-robin over 200 rounds per match. Learner explores:
  - Which strategy wins overall?
  - How does adding noise (10% chance of mistaken move) affect rankings?
  - Plot cumulative scores over rounds for all strategies.

Exercise 3 — Challenge: Cyber Attacker-Defender Game
  Model a network security game: an Attacker chooses which of 6 network
  nodes to target; a Defender allocates limited monitoring resources.
  Attack success probabilities depend on allocation. Model as a zero-sum
  bimatrix game.
  - Solve for mixed-strategy NE using SciPy linprog.
  - Simulate 1000 plays under NE strategies.
  - Extend to a sequential Stackelberg game (Defender moves first).
  - Compare NE vs. Stackelberg payoffs and interpret: does commitment help
    the Defender?

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
FORMAT & TOOLBOX
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Primary tools: NumPy, SciPy (linprog for zero-sum NE), SymPy (analytical
solutions), matplotlib.
Output format: MkDocs-compatible Markdown with admonitions, fenced code
blocks, KaTeX math.
Add "Check Your Understanding" (3 questions) after each Part.
All code must run with Python 3.10+ and only open-source packages.
```
