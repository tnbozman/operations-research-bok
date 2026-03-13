# Agent Prompt — Stochastic Processes

> Copy the block below and paste it directly to your AI agent to build out this section.

---

```text
You are an Operations Research instructor creating a complete, interactive,
hands-on learning module for **Stochastic Processes**.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PART 1 — LECTURE (Conceptual Foundation)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Cover the following:

1. What is a stochastic process: random variables indexed by time, sample
   paths, state space taxonomy (discrete/continuous × finite/countable/
   uncountable).
2. The Markov property: formal definition, why "memorylessness" is powerful,
   and when it is a reasonable real-world assumption.
3. Discrete-Time Markov Chains (DTMC):
   - Transition matrix P, Chapman-Kolmogorov equations.
   - State classification: recurrent vs. transient, absorbing states.
   - Steady-state distribution: solve πP = π; existence and uniqueness
     conditions (irreducibility, aperiodicity).
   - First passage times: how to compute expected time to reach a state.
   - Absorbing chains: fundamental matrix N = (I - Q)^{-1}, absorption
     probabilities and expected absorption times.
4. Continuous-Time Markov Chains (CTMC):
   - Generator (rate) matrix Q, Kolmogorov forward/backward equations.
   - Steady-state: balance equations.
   - Connection to Queueing Theory (birth-death process).
5. Poisson Processes: definition, inter-arrival distribution, superposition
   and thinning, compound Poisson process.
6. Markov Decision Processes (MDP):
   - States, actions, transition probabilities, reward function, discount
     factor γ.
   - Bellman optimality equations.
   - Value Iteration and Policy Iteration algorithms — derive and implement.
   - Connection to reinforcement learning (brief teaser).
7. Hidden Markov Models (HMM): observable emissions from hidden states,
   Viterbi algorithm, military relevance (target state estimation).

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PART 2 — REAL-WORLD PRACTICAL EXAMPLES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Example A — Military: Fleet Readiness Markov Chain & Maintenance Policy MDP
  Scenario Part 1 (DTMC): Model a fleet of 50 vehicles transitioning daily
  among states FMC, PMC, NMC, and Under-Maintenance. Compute steady-state
  readiness and the expected days to recover from a 100% NMC situation.
  - Build and solve using NumPy (linear system for steady-state).
  - Simulate 365 days and compare simulated vs. analytical steady state.
  - Plot daily FMC percentage with ± 1 standard deviation band.

  Scenario Part 2 (MDP): The maintenance officer decides daily how many
  PMC vehicles to send for full repair (costs maintenance hours). Reward =
  FMC count tomorrow. Formulate and solve via Value Iteration to find the
  optimal maintenance dispatch policy as a function of current fleet state.
  - Implement Value Iteration in Python.
  - Visualize the optimal policy as a decision heatmap (state → action).

Example B — Civilian: Customer Lifecycle Churn MDP
  Scenario: Customers move through states (Active, At-Risk, Churned).
  A company can apply interventions (email, discount, call) at different
  costs. Formulate as an MDP to find the intervention policy minimizing
  expected churn rate over a 12-month horizon.
  - Solve with Policy Iteration.
  - Compare: no intervention vs. optimal policy vs. always-intervene.
  - Plot customer state distribution over 12 months under each policy.

All code complete, runnable, with pip install instructions and inline comments.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PART 3 — INTERACTIVE EXERCISES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Exercise 1 — Guided (Warm-Up): DTMC Simulator and Steady-State Validation
  Provide a Python skeleton with TODO blocks for the learner to:
  - Simulate a 4-state DTMC for 10,000 steps.
  - Estimate steady-state from simulation visit frequencies.
  - Solve for analytical steady-state (linear system).
  - Print and compare both, assert they match within 2%.

Exercise 2 — Exploratory: Poisson Process Analysis
  Given a dataset of observed arrival times (provide a CSV of 500 synthetic
  arrivals from a Poisson process, λ=5/hr):
  - Estimate λ from the data using MLE.
  - Plot the empirical inter-arrival CDF vs. the theoretical Exponential(λ).
  - Test for goodness of fit (Kolmogorov-Smirnov test, scipy.stats.kstest).
  - Explore: what happens if you split the time series in half? Is λ stable?

Exercise 3 — Challenge: Stochastic Combat Attrition with Lanchester Laws
  Model a force-on-force engagement where unit strengths are stochastic.
  Extend classical Lanchester equations to a CTMC where attrition rates
  are Poisson processes with strength-dependent intensity.
  - Simulate 200 battle replicates from the same initial conditions.
  - Plot force strength over time (mean ± CI) for both sides.
  - Estimate P(Blue wins) as a function of initial force ratio.
  - Compare stochastic simulation results to deterministic Lanchester
    predictions.
  - Discuss: when does randomness matter most (large forces vs. small forces)?

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
FORMAT & TOOLBOX
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Primary tools: NumPy, SciPy, matplotlib, pandas (for data exercises).
Output format: MkDocs-compatible Markdown with admonitions, fenced code
blocks, KaTeX math (all distribution/formula notation in $$).
Add "Check Your Understanding" (3 questions) after each Part.
All code must run with Python 3.10+ and only open-source packages.
```
