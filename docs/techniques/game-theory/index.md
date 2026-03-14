# Game Theory

> Modeling strategic interactions between rational agents.

---

## Lecture

### 1. What Makes a Situation a "Game"?

A **game** is any situation where multiple decision-makers (**players**) choose actions whose combined effect determines each player's outcome. The five defining elements are:

| Element | Description | Military Example |
|---------|-------------|-----------------|
| **Players** | The decision-makers ($N = \{1, 2, \ldots, n\}$) | Two opposing commanders |
| **Strategies** | Complete plans of action available to each player ($S_i$ for player $i$) | Attack north, south, or feint |
| **Payoffs** | Utility each player receives: $u_i(s_1, \ldots, s_n)$ | Territory seized minus casualties |
| **Information** | What each player knows when choosing (perfect, imperfect, complete, incomplete) | Does the enemy know our order of battle? |
| **Timing** | Whether players move simultaneously or sequentially | Simultaneous assault vs. sequential advance |

**Contrast with Decision Analysis:** In Decision Analysis, uncertainty comes from *nature* — a passive, indifferent random process. In Game Theory, uncertainty arises from *adversarial* or *strategic* agents who act in their own interest and anticipate yours.

---

### 2. Normal Form vs. Extensive Form

**Normal (strategic) form** represents a game as a matrix of payoffs indexed by strategy profiles. It is most natural for simultaneous games.

$$
G = \bigl(N,\; \{S_i\}_{i \in N},\; \{u_i\}_{i \in N}\bigr)
$$

**Extensive form** represents a game as a tree, capturing sequence, information sets, and timing. It is essential for sequential (multi-stage) games.

| | Normal Form | Extensive Form |
|-|-------------|----------------|
| **Use when** | Players move simultaneously | Players move sequentially |
| **Representation** | Payoff matrix | Game tree with decision nodes |
| **Key solution concept** | Nash Equilibrium | Subgame Perfect Equilibrium |
| **Example** | Prisoner's Dilemma | Chess, Stackelberg competition |

!!! note "Check Your Understanding — Sections 1 & 2"
    1. Name the five elements of a game and give a military example of each.
    2. When would you prefer extensive form over normal form to model a conflict?
    3. How does Game Theory differ from classical Decision Analysis?

---

### 3. Dominant Strategies and IESDS

A strategy $s_i^*$ **strictly dominates** $s_i'$ if it yields a strictly higher payoff regardless of opponents' actions:

$$
u_i(s_i^*, s_{-i}) > u_i(s_i', s_{-i}) \quad \forall\, s_{-i} \in S_{-i}
$$

**Iterated Elimination of Dominated Strategies (IESDS):** Repeatedly remove strictly dominated strategies from all players' strategy sets until no further eliminations are possible. The surviving strategies form the IESDS solution.

!!! example "Military IESDS Example"
    Two commanders each choose one of three maneuvers: **Flank**, **Frontal**, or **Feint**. Analysis reveals:

    - For the attacker, **Feint** is dominated by **Flank** regardless of the defender's choice (it costs more resources for the same expected territory gain).
    - After removing **Feint**, **Frontal** becomes dominated by **Flank** against all remaining defender responses.

    IESDS reduces the 3×3 engagement to a unique predicted outcome without solving a full LP — a significant analytical simplification in large strategy spaces.

**IESDS and Nash Equilibrium:** Any strategy that survives IESDS can be part of a Nash Equilibrium. If IESDS yields a unique outcome, that outcome *is* the unique Nash Equilibrium.

---

### 4. Nash Equilibrium

**Definition (Nash, 1950):** A strategy profile $(s_1^*, \ldots, s_n^*)$ is a **Nash Equilibrium (NE)** if no player can benefit by unilaterally deviating:

$$
u_i(s_i^*, s_{-i}^*) \geq u_i(s_i, s_{-i}^*) \quad \forall\, s_i \in S_i,\; \forall\, i \in N
$$

**Existence theorem (Nash 1950):** Every finite game has at least one Nash Equilibrium in mixed strategies.

**Uniqueness vs. multiplicity:** NE may be unique (Prisoner's Dilemma), multiple in pure strategies (Battle of the Sexes, Stag Hunt), or exist only in mixed strategies (Matching Pennies).

**Mixed-strategy NE — Matching Pennies proof:**

Both players simultaneously reveal a coin. Player 1 wins if coins match; Player 2 wins if they differ.

$$
A = \begin{pmatrix} 1 & -1 \\ -1 & 1 \end{pmatrix}
$$

Let Player 1 mix Heads with probability $p$ and Player 2 mix Heads with probability $q$.

By the **indifference condition** (at NE, Player 1 must be indifferent between Heads and Tails):

$$
\underbrace{1 \cdot q + (-1)(1-q)}_{\text{P1 plays Heads}} = \underbrace{(-1) \cdot q + 1 \cdot (1-q)}_{\text{P1 plays Tails}}
\implies 2q - 1 = 1 - 2q \implies q = \tfrac{1}{2}
$$

By symmetry, $p = \tfrac{1}{2}$. The **unique NE** is the mixed strategy $(p^*, q^*) = (\tfrac{1}{2}, \tfrac{1}{2})$ with expected payoff $0$ for both players.

!!! note "Check Your Understanding — Sections 3 & 4"
    1. Can IESDS yield a unique outcome that is NOT a Nash Equilibrium? Explain why or why not.
    2. Prove the mixed-strategy Nash Equilibrium for Battle of the Sexes using the indifference condition.
    3. Can a game have more than one pure-strategy Nash Equilibrium? Give a concrete example.

---

### 5. Zero-Sum Games: Minimax Theorem and LP

In a two-player zero-sum game, Player 1's gain equals Player 2's loss ($u_1 + u_2 = 0$). The **minimax theorem** (von Neumann, 1928) guarantees:

$$
\max_{\mathbf{p}} \min_{\mathbf{q}}\; \mathbf{p}^\top A \mathbf{q}
= \min_{\mathbf{q}} \max_{\mathbf{p}}\; \mathbf{p}^\top A \mathbf{q}
= v^*
$$

where $\mathbf{p} \in \Delta^m$, $\mathbf{q} \in \Delta^n$ are mixed strategies and $v^*$ is the **game value**.

**Saddle points (pure-strategy optimum):** A saddle point exists when $a_{ij}^* = \min_j \max_i a_{ij} = \max_i \min_j a_{ij}$. In that case, neither player has incentive to deviate unilaterally in pure strategies.

**Solving for mixed strategies via LP:**

Player 1's optimal mixed strategy satisfies:

$$
\max_{v,\, \mathbf{p}}\; v \quad \text{s.t.} \quad A^\top \mathbf{p} \geq v\,\mathbf{1},\quad \mathbf{1}^\top \mathbf{p} = 1,\quad \mathbf{p} \geq 0
$$

**Red-team analysis:** Modelling an adversary as a minimax player provides a worst-case, conservative strategy. This is widely used in military planning (wargaming) and cybersecurity (threat modelling) to design robust defenses against the most capable attacker.

---

### 6. Non-Zero-Sum Games

**Prisoner's Dilemma:** Two players each choose Cooperate (C) or Defect (D) simultaneously.

$$
\begin{array}{c|cc}
 & \text{C} & \text{D} \\ \hline
\text{C} & (3,\,3) & (0,\,5) \\
\text{D} & (5,\,0) & (1,\,1)
\end{array}
$$

- **Defect strictly dominates** Cooperate for both players (5 > 3 and 1 > 0).
- Unique NE: **(D, D)** with payoff $(1, 1)$.
- Yet **(C, C)** with $(3, 3)$ is **Pareto superior** — both players would prefer it!

**Pareto efficiency:** An outcome is **Pareto efficient** if no player can be made strictly better off without making another strictly worse off. In the PD, (D, D) is the NE but is *not* Pareto efficient; (C, C) is Pareto efficient but not the NE.

**Coordination games:** Games such as **Battle of the Sexes** and **Stag Hunt** have multiple pure-strategy NE. The coordination challenge — selecting one equilibrium — is solved through communication, convention, focal points (Schelling), or repeated interaction.

---

### 7. Repeated Games and the Folk Theorem

**Repeated games:** When the same stage game is played repeatedly, players can condition future behavior on past actions, enabling cooperation to emerge as equilibrium behavior even when defection dominates in the one-shot game.

**Folk Theorem:** In an infinitely repeated game with discount factor $\delta$ sufficiently close to 1, *any* feasible payoff vector that is individually rational (at least as good as the minimax payoff for each player) can be sustained as a subgame perfect equilibrium.

**Tit-for-Tat (TfT):**

1. **Start** by cooperating.
2. **Each subsequent period**, copy the opponent's previous action.

Properties that make TfT successful:

| Property | Meaning |
|----------|---------|
| **Nice** | Never defects first |
| **Retaliatory** | Immediately punishes defection |
| **Forgiving** | Returns to cooperation after one retaliation |
| **Clear** | Simple enough for the opponent to recognize and model |

In Robert Axelrod's computer tournaments (1980, 1984), Tit-for-Tat won consistently against all other submitted strategies.

---

### 8. Stackelberg Games

In a **Stackelberg game**, one player (the **leader**) moves first and commits to a strategy; the **follower** observes this commitment and responds optimally.

**Solution by backward induction:**

1. Solve the follower's best-response function: $s_2^*(s_1) = \arg\max_{s_2} u_2(s_1, s_2)$.
2. The leader maximizes, anticipating the follower's response: $\max_{s_1}\; u_1\!\bigl(s_1,\, s_2^*(s_1)\bigr)$.

**First-mover advantage:** The leader typically achieves a higher payoff than in the simultaneous (Nash) equilibrium because they can shape the follower's best response to their own benefit.

!!! example "Military Application"
    In force-on-force sequential operations, the commander who can **credibly commit** to a course of action and communicate it to the adversary often gains strategic advantage. The adversary (follower) must respond to a fixed plan, limiting their ability to exploit uncertainty. This is the operational rationale behind deterrence postures and pre-announced rules of engagement.

!!! note "Check Your Understanding — Sections 5–8"
    1. What is a saddle point, and when does a pure-strategy optimal solution exist in a zero-sum game?
    2. Why does defection dominate in the one-shot Prisoner's Dilemma but not necessarily in the infinitely repeated version?
    3. Compare the Stackelberg and Nash equilibria for a pricing duopoly: who benefits from moving first and why?

---

### 9. Auctions as Games

Auctions are games of **incomplete information**: each bidder has a private valuation $v_i$ drawn from a distribution unknown to others.

| Auction Type | Winner | Price Paid | Dominant Strategy |
|-------------|--------|-----------|-------------------|
| **First-price sealed-bid** | Highest bidder | Own bid | Shade bid below $v_i$ |
| **Second-price (Vickrey)** | Highest bidder | Second-highest bid | Bid true value $v_i$ |
| **Ascending (English)** | Last bidder standing | Dropout price | Stay active until $v_i$ |
| **Dutch (descending)** | First to accept | Accepted price | Shade bid below $v_i$ |

**Vickrey auction — dominant strategy proof:**

With valuation $v_i$, consider any bid $b_i$:

- **If $b_i > v_i$:** you risk winning at a second price $p \in (v_i, b_i)$, yielding negative utility $v_i - p < 0$.
- **If $b_i < v_i$:** you may lose when $p \in (b_i, v_i)$, forfeiting a positive gain $v_i - p > 0$.
- **If $b_i = v_i$:** you win exactly when winning is profitable and lose when it is not. This weakly dominates all other bids.

Therefore, **truthful bidding is a weakly dominant strategy** in a second-price sealed-bid auction, regardless of others' valuations or bids.

!!! note "Check Your Understanding — Section 9"
    1. Why is bidding truthfully dominant in a second-price auction but not in a first-price auction?
    2. A military procurement officer uses sealed-bid auctions for defense contracts. Which format reduces strategic underbidding, and why?
    3. How does private valuation (incomplete information) change the nature of the strategic interaction compared to a complete-information game?

---

## Real-World Examples

### Example A — Military: Colonel Blotto Force Allocation Game

!!! example "Scenario"
    Two commanders each allocate exactly **120 combat-power units** across **5 battlefields** simultaneously. A commander wins a battlefield by committing more forces there; total score = battlefields won. Formulate as a symmetric zero-sum game. Enumerate strategies via Monte Carlo, approximate the Nash equilibrium with replicator dynamics, and visualize win-rate heatmaps.

**Open-Source Tools:** NumPy, Matplotlib, Python

```python
# pip install numpy matplotlib
import numpy as np
import matplotlib.pyplot as plt

np.random.seed(42)

N_UNITS   = 120   # total combat power per commander
N_FIELDS  = 5     # number of battlefields
N_STRATS  = 500   # Monte Carlo strategy pool size
N_ITER    = 300   # replicator dynamics iterations

# ── Strategy generation ───────────────────────────────────────────────────────
def random_allocation(n_units, n_fields, rng):
    """Random non-negative integer allocation summing to n_units (stars-and-bars)."""
    dividers = np.sort(rng.integers(0, n_units + 1, size=n_fields - 1))
    return np.diff(np.concatenate([[0], dividers, [n_units]]))

rng = np.random.default_rng(42)
strategies = np.array(
    [random_allocation(N_UNITS, N_FIELDS, rng) for _ in range(N_STRATS)]
)  # shape (N_STRATS, N_FIELDS)

# ── Win-rate payoff matrix ────────────────────────────────────────────────────
def win_rate(a, b):
    """Return win fraction for a vs b: 1=win, 0.5=tie, 0=loss."""
    w = np.sum(a > b)
    l = np.sum(b > a)
    return 1.0 if w > l else (0.0 if l > w else 0.5)

payoff = np.array(
    [[win_rate(strategies[i], strategies[j]) for j in range(N_STRATS)]
     for i in range(N_STRATS)]
)

# ── Replicator dynamics (approximate Nash equilibrium) ────────────────────────
weights = np.ones(N_STRATS) / N_STRATS
for _ in range(N_ITER):
    fitness     = payoff @ weights          # expected payoff of each strategy
    avg_fitness = weights @ fitness
    weights     = weights * fitness / avg_fitness
    weights    /= weights.sum()             # renormalise (numerical safety)

top_idx = np.argsort(weights)[-5:][::-1]
print("Colonel Blotto — Top Strategies by Replicator-Dynamics NE Weight")
print("=" * 60)
for rank, idx in enumerate(top_idx):
    alloc = strategies[idx]
    print(f"  Rank {rank+1}: {alloc.tolist():30s}  weight = {weights[idx]:.4f}")

# ── Heatmap: win rate vs. uniform opponent ────────────────────────────────────
# Fix field-1 (x-axis) and field-2 (y-axis) allocations;
# distribute remaining units evenly across fields 3–5.
uniform_alloc = np.array([N_UNITS // N_FIELDS] * N_FIELDS)  # [24,24,24,24,24]
grid = np.arange(0, N_UNITS + 1, 5)
G    = len(grid)
heat = np.full((G, G), np.nan)

for xi, x in enumerate(grid):
    for yi, y in enumerate(grid):
        remaining = N_UNITS - x - y
        if remaining < 0:
            continue
        r, extra = divmod(remaining, 3)
        alloc = np.array([x, y, r + extra, r, r])
        wins  = np.sum(alloc > uniform_alloc)
        heat[yi, xi] = wins / N_FIELDS      # fraction of battlefields won

fig, ax = plt.subplots(figsize=(7, 5))
im = ax.imshow(heat, origin="lower", aspect="auto",
               extent=[0, N_UNITS, 0, N_UNITS],
               cmap="RdYlGn", vmin=0, vmax=1)
plt.colorbar(im, ax=ax, label="Win Rate vs. Uniform Opponent")
ax.set_xlabel("Units to Battlefield 1")
ax.set_ylabel("Units to Battlefield 2")
ax.set_title(
    "Colonel Blotto — Win-Rate Heatmap\n"
    "(remaining units split evenly across fields 3–5)"
)
plt.tight_layout()
plt.savefig("blotto_heatmap.png", dpi=150)
plt.show()
print("Heatmap saved to blotto_heatmap.png")
```

**Key insight:** The uniform allocation (24 units per battlefield) is often near-optimal in the symmetric Blotto game because any concentration that wins more on one battlefield necessarily loses elsewhere, and the opponent can exploit the exposed fields.

---

### Example B — Civilian: Competitive Pricing Stackelberg Game

!!! example "Scenario"
    A **market leader (Firm A)** sets price first; **follower (Firm B)** observes and responds optimally. Demand follows a differentiated Bertrand model (substitute goods). Compute the Stackelberg equilibrium analytically, compare to the Nash (simultaneous) and cooperative (cartel) outcomes, plot reaction functions, and quantify the first-mover advantage.

**Open-Source Tools:** SymPy, NumPy, Matplotlib, Python

```python
# pip install sympy numpy matplotlib
import sympy as sp
import numpy as np
import matplotlib.pyplot as plt

# ── Model setup ───────────────────────────────────────────────────────────────
p_A, p_B = sp.symbols("p_A p_B", positive=True)

# Demand (Bertrand, substitute goods): q_i = a - b*p_i + c*p_j
a, b, c, mc = sp.Integer(100), sp.Integer(2), sp.Integer(1), sp.Integer(10)

q_A = a - b * p_A + c * p_B        # Firm A demand
q_B = a - b * p_B + c * p_A        # Firm B demand
pi_A = (p_A - mc) * q_A             # Firm A profit
pi_B = (p_B - mc) * q_B             # Firm B profit

# ── Reaction functions (best-response correspondences) ───────────────────────
rf_A_expr = sp.solve(sp.diff(pi_A, p_A), p_A)[0]   # p_A = R_A(p_B)
rf_B_expr = sp.solve(sp.diff(pi_B, p_B), p_B)[0]   # p_B = R_B(p_A)
print("Reaction Functions:")
print(f"  Firm A: p_A = {rf_A_expr}")
print(f"  Firm B: p_B = {rf_B_expr}")

# ── Nash equilibrium (simultaneous best response) ─────────────────────────────
nash    = sp.solve([sp.diff(pi_A, p_A), sp.diff(pi_B, p_B)], [p_A, p_B])
pA_nash, pB_nash = nash[p_A], nash[p_B]
piA_nash = pi_A.subs([(p_A, pA_nash), (p_B, pB_nash)])
piB_nash = pi_B.subs([(p_A, pA_nash), (p_B, pB_nash)])

# ── Stackelberg equilibrium (Firm A leads) ────────────────────────────────────
# Substitute Firm B's best response into Firm A's profit, then optimise
pi_A_stack = pi_A.subs(p_B, rf_B_expr)
pA_stack   = sp.solve(sp.diff(pi_A_stack, p_A), p_A)[0]
pB_stack   = rf_B_expr.subs(p_A, pA_stack)
piA_stack  = pi_A.subs([(p_A, pA_stack), (p_B, pB_stack)])
piB_stack  = pi_B.subs([(p_A, pA_stack), (p_B, pB_stack)])

# ── Cooperative (cartel) outcome ──────────────────────────────────────────────
pi_joint = pi_A + pi_B
coop     = sp.solve([sp.diff(pi_joint, p_A), sp.diff(pi_joint, p_B)], [p_A, p_B])
pA_coop, pB_coop = coop[p_A], coop[p_B]
piA_coop = pi_A.subs([(p_A, pA_coop), (p_B, pB_coop)])
piB_coop = pi_B.subs([(p_A, pA_coop), (p_B, pB_coop)])

# ── Print comparison table ────────────────────────────────────────────────────
print("\nEquilibrium Comparison")
print(f"{'Outcome':<20} {'p_A':>8} {'p_B':>8} {'π_A':>10} {'π_B':>10}")
print("-" * 60)
for label, pA, pB, piA, piB in [
    ("Nash",        pA_nash,  pB_nash,  piA_nash,  piB_nash),
    ("Stackelberg", pA_stack, pB_stack, piA_stack, piB_stack),
    ("Cooperative", pA_coop,  pB_coop,  piA_coop,  piB_coop),
]:
    print(f"{label:<20} {float(pA):>8.2f} {float(pB):>8.2f}"
          f" {float(piA):>10.2f} {float(piB):>10.2f}")

gain_A = float(piA_stack - piA_nash)
gain_B = float(piB_stack - piB_nash)
print(f"\nVs. Nash: Firm A gains {gain_A:.2f}, Firm B (follower) gains {gain_B:.2f}.")
print("In Bertrand price competition (strategic complements), commitment raises "
      "both prices; the follower gains more by responding optimally.")

# ── Plot reaction functions and equilibria ────────────────────────────────────
rf_A = sp.lambdify(p_B, rf_A_expr, "numpy")
rf_B = sp.lambdify(p_A, rf_B_expr, "numpy")

p_range = np.linspace(10, 75, 300)
fig, ax  = plt.subplots(figsize=(7, 6))

ax.plot(rf_A(p_range), p_range, lw=2, label=r"Firm A reaction: $p_A = R_A(p_B)$")
ax.plot(p_range, rf_B(p_range), lw=2, label=r"Firm B reaction: $p_B = R_B(p_A)$")

for (pA_val, pB_val, lbl, mkr, col) in [
    (float(pA_nash),  float(pB_nash),  "Nash NE",       "o", "tab:red"),
    (float(pA_stack), float(pB_stack), "Stackelberg",   "s", "tab:green"),
    (float(pA_coop),  float(pB_coop),  "Cooperative",   "^", "tab:purple"),
]:
    ax.scatter(pA_val, pB_val, s=120, marker=mkr, color=col,
               zorder=5, label=f"{lbl} ({pA_val:.1f}, {pB_val:.1f})")

ax.set_xlabel(r"Firm A price ($p_A$)")
ax.set_ylabel(r"Firm B price ($p_B$)")
ax.set_title("Stackelberg Pricing Game — Reaction Functions & Equilibria")
ax.legend(fontsize=9)
ax.grid(True, alpha=0.3)
plt.tight_layout()
plt.savefig("stackelberg_pricing.png", dpi=150)
plt.show()
print("Plot saved to stackelberg_pricing.png")
```

!!! note "Check Your Understanding — Part 2"
    1. In the Colonel Blotto game, why does concentrating all 120 units on a single battlefield fail as a general strategy?
    2. In the Stackelberg pricing game, why does the leader price *higher* than in the simultaneous Nash equilibrium?
    3. Why is the cooperative (cartel) outcome not a Nash equilibrium, even though both firms prefer it?

---

## Interactive Exercises

### Exercise 1 — Guided (Warm-Up): Nash Equilibrium Finder

!!! question "Task"
    Complete the two TODO blocks to implement a pure-strategy Nash Equilibrium detector. Test it on four classic games: **Prisoner's Dilemma**, **Battle of the Sexes**, **Matching Pennies**, and **Stag Hunt**. Verify that Matching Pennies has *no* pure-strategy NE.

```python
import numpy as np

def find_pure_ne(payoff_A, payoff_B):
    """
    Find all pure-strategy Nash Equilibria of a two-player bimatrix game.

    Parameters
    ----------
    payoff_A : ndarray, shape (m, n)  — Player 1's payoffs
    payoff_B : ndarray, shape (m, n)  — Player 2's payoffs

    Returns
    -------
    list of (i, j) index pairs that are pure-strategy NE
    """
    equilibria = []
    m, n = payoff_A.shape
    for i in range(m):
        for j in range(n):
            # TODO: Check if (i,j) is a best response for Player 1.
            # Player 1 best-responds to column j when no row i' gives
            # a strictly higher payoff against column j.
            # a_best = (payoff_A[i, j] == payoff_A[:, j].max())
            a_best = ...

            # TODO: Check if (i,j) is a best response for Player 2.
            # Player 2 best-responds to row i when no column j' gives
            # a strictly higher payoff against row i.
            # b_best = (payoff_B[i, j] == payoff_B[i, :].max())
            b_best = ...

            if a_best and b_best:
                equilibria.append((i, j))
    return equilibria

# ── Test games ────────────────────────────────────────────────────────────────
games = {
    "Prisoner's Dilemma": (
        np.array([[3, 0], [5, 1]]),   # Player 1: C=row0, D=row1
        np.array([[3, 5], [0, 1]]),
    ),
    "Battle of the Sexes": (
        np.array([[2, 0], [0, 1]]),
        np.array([[1, 0], [0, 2]]),
    ),
    "Matching Pennies": (
        np.array([[ 1, -1], [-1,  1]]),
        np.array([[-1,  1], [ 1, -1]]),
    ),
    "Stag Hunt": (
        np.array([[4, 0], [2, 2]]),
        np.array([[4, 2], [0, 2]]),
    ),
}

strategy_labels = {
    "Prisoner's Dilemma": ["Cooperate", "Defect"],
    "Battle of the Sexes": ["Opera", "Football"],
    "Matching Pennies":    ["Heads", "Tails"],
    "Stag Hunt":           ["Stag", "Hare"],
}

for name, (A, B) in games.items():
    nes = find_pure_ne(A, B)
    print(f"\n{name}")
    if nes:
        labels = strategy_labels[name]
        for i, j in nes:
            print(f"  NE: ({labels[i]}, {labels[j]})  "
                  f"payoffs = ({A[i,j]}, {B[i,j]})")
    else:
        print("  No pure-strategy NE — mixed-strategy NE exists")
```

---

### Exercise 2 — Exploratory: Repeated Prisoner's Dilemma Tournament

!!! question "Task"
    Implement an Axelrod-style round-robin tournament with **6 strategies** over **200 rounds per match**. Explore: Which strategy wins overall? How does adding **10% noise** (mistaken actions) change the rankings? Plot cumulative scores over rounds.

```python
# pip install numpy matplotlib
import numpy as np
import matplotlib.pyplot as plt

# Prisoner's Dilemma payoffs (T > R > P > S)
T, R, P, S = 5, 3, 1, 0   # Temptation, Reward, Punishment, Sucker

def stage_payoff(a1, a2):
    """Return (score1, score2) for actions a1, a2 ∈ {'C', 'D'}."""
    matrix = {
        ("C", "C"): (R, R),
        ("C", "D"): (S, T),
        ("D", "C"): (T, S),
        ("D", "D"): (P, P),
    }
    return matrix[(a1, a2)]

# ── Strategy definitions ──────────────────────────────────────────────────────
class Strategy:
    def __init__(self, name):
        self.name = name
    def action(self, my_history, opp_history):
        raise NotImplementedError
    def reset(self):
        pass

class AlwaysCooperate(Strategy):
    def action(self, my_history, opp_history): return "C"

class AlwaysDefect(Strategy):
    def action(self, my_history, opp_history): return "D"

class TitForTat(Strategy):
    def action(self, my_history, opp_history):
        return "C" if not opp_history else opp_history[-1]

class Grudger(Strategy):
    def __init__(self):
        super().__init__("Grudger")
        self._defected = False
    def action(self, my_history, opp_history):
        if "D" in opp_history:
            self._defected = True
        return "D" if self._defected else "C"
    def reset(self):
        self._defected = False

class Pavlov(Strategy):
    """Win-Stay-Lose-Shift: repeat last action if it yielded R or T, else switch."""
    def action(self, my_history, opp_history):
        if not my_history:
            return "C"
        same = (my_history[-1] == opp_history[-1])   # both C or both D → win
        return my_history[-1] if same else ("D" if my_history[-1] == "C" else "C")

class RandomStrategy(Strategy):
    def action(self, my_history, opp_history):
        return np.random.choice(["C", "D"])

# ── Tournament engine ─────────────────────────────────────────────────────────
def play_match(s1, s2, n_rounds=200, noise=0.0):
    """Return (total_score_s1, total_score_s2) after n_rounds."""
    h1, h2 = [], []
    score1 = score2 = 0
    s1.reset(); s2.reset()
    for _ in range(n_rounds):
        a1 = s1.action(h1, h2)
        a2 = s2.action(h2, h1)
        if np.random.random() < noise:   # apply noise
            a1 = "D" if a1 == "C" else "C"
        if np.random.random() < noise:
            a2 = "D" if a2 == "C" else "C"
        p1, p2 = stage_payoff(a1, a2)
        score1 += p1; score2 += p2
        h1.append(a1); h2.append(a2)
    return score1, score2

def run_tournament(strats, n_rounds=200, noise=0.0):
    """Round-robin; return array of total scores (one per strategy)."""
    n = len(strats)
    scores = np.zeros(n)
    for i in range(n):
        for j in range(n):
            if i == j:
                continue
            s1, s2 = strats[i], strats[j]
            sc1, sc2 = play_match(s1, s2, n_rounds, noise)
            scores[i] += sc1
            scores[j] += sc2
    return scores

np.random.seed(42)
strats = [
    AlwaysCooperate("Always Cooperate"),
    AlwaysDefect("Always Defect"),
    TitForTat("Tit-for-Tat"),
    Grudger(),
    Pavlov("Pavlov"),
    RandomStrategy("Random"),
]

print("=== No Noise ===")
scores_clean = run_tournament(strats)
for name, sc in sorted(zip([s.name for s in strats], scores_clean),
                        key=lambda x: -x[1]):
    print(f"  {name:<22}: {sc:.0f}")

print("\n=== 10% Noise ===")
scores_noisy = run_tournament(strats, noise=0.10)
for name, sc in sorted(zip([s.name for s in strats], scores_noisy),
                        key=lambda x: -x[1]):
    print(f"  {name:<22}: {sc:.0f}")

# ── Plot: cumulative scores over rounds (TfT vs Always Defect) ────────────────
s1, s2 = TitForTat("Tit-for-Tat"), AlwaysDefect("Always Defect")
h1, h2, cum1, cum2 = [], [], [0], [0]
s1.reset(); s2.reset()
for _ in range(200):
    a1 = s1.action(h1, h2); a2 = s2.action(h2, h1)
    p1, p2 = stage_payoff(a1, a2)
    cum1.append(cum1[-1] + p1); cum2.append(cum2[-1] + p2)
    h1.append(a1); h2.append(a2)

fig, ax = plt.subplots(figsize=(8, 4))
ax.plot(cum1, label="Tit-for-Tat")
ax.plot(cum2, label="Always Defect")
ax.set_xlabel("Round")
ax.set_ylabel("Cumulative Score")
ax.set_title("Repeated PD: Tit-for-Tat vs. Always Defect (200 rounds)")
ax.legend(); ax.grid(True, alpha=0.3)
plt.tight_layout()
plt.savefig("tft_vs_defect.png", dpi=150)
plt.show()
print("Plot saved to tft_vs_defect.png")
```

---

### Exercise 3 — Challenge: Cyber Attacker-Defender Game

!!! question "Task"
    Model a **zero-sum network security game**: an Attacker targets one of 6 nodes; a Defender allocates monitoring to one node. Attack success depends on whether the Defender is watching. Solve for the **mixed-strategy NE** using `scipy.optimize.linprog`. Simulate 1000 plays. Extend to a **Stackelberg** setting (Defender commits to a pure strategy first). Compare payoffs and interpret: does commitment help the Defender?

```python
# pip install scipy numpy matplotlib
import numpy as np
from scipy.optimize import linprog
import matplotlib.pyplot as plt

np.random.seed(42)
N_NODES = 6

# ── Payoff matrix ──────────────────────────────────────────────────────────────
# A[i, j] = attacker success probability when:
#   attacker targets node i (row), defender monitors node j (col)
# High-value nodes have higher intrinsic attack success.
node_values    = np.array([0.9, 0.8, 0.7, 0.6, 0.5, 0.4])
detection_drop = 0.6   # fraction of success probability lost if monitored

A_payoff = np.array(
    [[node_values[i] * (1 - detection_drop) if i == j else node_values[i]
      for j in range(N_NODES)]
     for i in range(N_NODES)]
)
print("Payoff Matrix (attacker success probability):")
print(np.round(A_payoff, 2))

# ── Defender LP: minimise game value v ────────────────────────────────────────
# Variables: [p_D (N), v]
# Minimise v  s.t.  A[i,:] · p_D ≤ v ∀i,  Σ p_D = 1,  p_D ≥ 0
m = n = N_NODES
c_def     = np.append(np.zeros(n), 1.0)                    # minimise v
A_ub_def  = np.hstack([A_payoff, -np.ones((m, 1))])        # A p_D - v ≤ 0
b_ub_def  = np.zeros(m)
A_eq_def  = np.array([np.append(np.ones(n), 0.0)])         # Σ p_D = 1
b_eq_def  = np.array([1.0])
bnd_def   = [(0.0, None)] * n + [(None, None)]

res_def   = linprog(c_def, A_ub=A_ub_def, b_ub=b_ub_def,
                    A_eq=A_eq_def, b_eq=b_eq_def, bounds=bnd_def)
p_D_star  = res_def.x[:n]
v_ne      = res_def.x[n]

# ── Attacker LP: maximise game value v ────────────────────────────────────────
# Variables: [p_A (m), v]
# Minimise -v  s.t.  -A[:,j] · p_A + v ≤ 0 ∀j,  Σ p_A = 1,  p_A ≥ 0
c_att     = np.append(np.zeros(m), -1.0)                   # maximise v
A_ub_att  = np.hstack([-A_payoff.T, np.ones((n, 1))])      # -A^T p_A + v ≤ 0
b_ub_att  = np.zeros(n)
A_eq_att  = np.array([np.append(np.ones(m), 0.0)])
b_eq_att  = np.array([1.0])
bnd_att   = [(0.0, None)] * m + [(None, None)]

res_att   = linprog(c_att, A_ub=A_ub_att, b_ub=b_ub_att,
                    A_eq=A_eq_att, b_eq=b_eq_att, bounds=bnd_att)
p_A_star  = res_att.x[:m]

print(f"\nMixed-Strategy Nash Equilibrium  (game value v* = {v_ne:.4f})")
print(f"{'Node':<8} {'Defender p_D*':>14} {'Attacker p_A*':>14}")
for k in range(N_NODES):
    print(f"  {k+1:<6} {p_D_star[k]:>14.4f} {p_A_star[k]:>14.4f}")

# ── Simulate 1000 plays under NE strategies ───────────────────────────────────
N_SIM     = 1000
att_plays = np.random.choice(N_NODES, N_SIM, p=p_A_star / p_A_star.sum())
def_plays = np.random.choice(N_NODES, N_SIM, p=p_D_star / p_D_star.sum())
sim_pay   = A_payoff[att_plays, def_plays]
print(f"\nSimulation (n={N_SIM}):  mean attacker success = {sim_pay.mean():.4f}"
      f"  (theory v* = {v_ne:.4f})")

# ── Stackelberg: Defender commits to a pure strategy first ────────────────────
# Defender picks j to minimise worst-case attacker success: min_j max_i A[i,j]
stack_vals = A_payoff.max(axis=0)          # worst-case for each pure Defender strategy
best_j     = int(np.argmin(stack_vals))
stack_val  = stack_vals[best_j]

print(f"\nStackelberg (Defender commits pure strategy):")
print(f"  Monitor node {best_j + 1}  →  worst-case attacker success = {stack_val:.4f}")
print(f"  NE game value = {v_ne:.4f}")
print(f"  Commitment {'HELPS' if stack_val < v_ne else 'HURTS'} the Defender "
      f"({'lower' if stack_val < v_ne else 'higher'} attacker success).")

# ── Bar chart: NE mixed strategies ────────────────────────────────────────────
fig, axes = plt.subplots(1, 2, figsize=(10, 4))
for ax, probs, title in zip(
    axes,
    [p_D_star, p_A_star],
    ["Defender NE Mixed Strategy", "Attacker NE Mixed Strategy"],
):
    ax.bar(range(1, N_NODES + 1), probs, color="steelblue", edgecolor="black")
    ax.set_xlabel("Node")
    ax.set_ylabel("Probability")
    ax.set_title(title)
    ax.set_ylim(0, 1)
plt.tight_layout()
plt.savefig("cyber_ne_strategies.png", dpi=150)
plt.show()
print("Strategy chart saved to cyber_ne_strategies.png")
```

!!! note "Check Your Understanding — Part 3"
    1. In Exercise 1, why does Matching Pennies have no pure-strategy NE? What does the pure-strategy check reveal?
    2. In Exercise 2, which strategy wins in the noisy (10% error) environment and why does noise hurt Tit-for-Tat?
    3. In Exercise 3, does the Defender benefit from committing first (Stackelberg)? Explain the intuition using the minimax theorem.

---

## Check Your Understanding

1. What are the five elements that define a game, and how does a zero-sum game differ structurally from a non-zero-sum game?
2. Explain the minimax theorem and its connection to Linear Programming. When does a pure-strategy saddle point exist?
3. How does the Folk Theorem resolve the Prisoner's Dilemma paradox in repeated settings, and what conditions ($\delta$, rationality) are required?

---

!!! tip "Prompt Template"
    Use the [Agent Prompt](prompt.md) for this topic, or the [Generic Prompt Template](../prompt-template.md) to generate additional lectures, examples, and exercises.
