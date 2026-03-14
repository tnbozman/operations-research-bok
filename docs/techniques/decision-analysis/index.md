# Decision Analysis

> Structuring decisions under uncertainty using probability and utility theory.

---

## Lecture

### 1. The Decision Analysis Cycle

Decision Analysis (DA) is a systematic, quantitative framework for making decisions when outcomes are uncertain. Unlike pure optimisation (which finds the mathematically best answer to a well-posed model), DA spends most of its effort *structuring* the problem correctly before any numbers are computed.

The canonical five-step cycle:

| Step | Activity | Key question |
|------|----------|--------------|
| **Frame** | Define decision, uncertainties, and objectives | What are we actually deciding? |
| **Model** | Build payoff matrix or decision tree; assign probabilities | What does the world look like? |
| **Solve** | Apply backward induction or decision criteria | What does the model recommend? |
| **Interpret** | Translate back to operational language | What does this mean for action? |
| **Iterate** | Revise after sensitivity / stakeholder review | Is the model good enough? |

!!! note "Why structuring is harder than solving"
    The computer can solve any well-posed tree in milliseconds. The hard work is agreeing on which alternatives exist, which uncertainties matter, and how to measure value. Poor framing produces precise answers to the wrong question.

---

### 2. Payoff Matrices and Decision Criteria

A **payoff matrix** $A$ has:

- $m$ rows — decision alternatives $d_1, \ldots, d_m$
- $n$ columns — states of nature $s_1, \ldots, s_n$ with probabilities $p_1, \ldots, p_n$, where $\sum_j p_j = 1$
- entry $A_{ij}$ — consequence (payoff) if decision $i$ is taken and state $j$ occurs

#### Worked 3 × 3 Example

Three decisions and three states of nature:

| | $s_1$ ($p=0.30$) | $s_2$ ($p=0.50$) | $s_3$ ($p=0.20$) |
|--|--:|--:|--:|
| $d_1$ | 40 | 60 | 20 |
| $d_2$ | 50 | 50 | 45 |
| $d_3$ | 30 | 80 | 10 |

**Expected Monetary Value (EMV)**

$$\text{EMV}(d_i) = \sum_{j=1}^{n} p_j \, A_{ij}$$

- EMV($d_1$) = 0.30×40 + 0.50×60 + 0.20×20 = **46**
- EMV($d_2$) = 0.30×50 + 0.50×50 + 0.20×45 = **49**
- EMV($d_3$) = 0.30×30 + 0.50×80 + 0.20×10 = **51** ← best

**Maximin** (pessimist — maximise the worst-case payoff)

$$d^* = \arg\max_{i} \min_{j} A_{ij}$$

Row minima: 20, **45**, 10 → best is $d_2$.

**Maximax** (optimist — maximise the best-case payoff)

$$d^* = \arg\max_{i} \max_{j} A_{ij}$$

Row maxima: 60, 50, **80** → best is $d_3$.

**Minimax Regret** — the *regret* for cell $(i,j)$ is the opportunity cost of not taking the best action:

$$R_{ij} = \max_{k} A_{kj} - A_{ij}$$

Regret matrix:

| | $s_1$ | $s_2$ | $s_3$ | max regret |
|--|--:|--:|--:|--:|
| $d_1$ | 10 | 20 | 25 | **25** ← best |
| $d_2$ | 0 | 30 | 0 | 30 |
| $d_3$ | 20 | 0 | 35 | 35 |

Minimax regret → $d_1$ (minimises the maximum regret of 25).

**Hurwicz criterion** (coefficient of optimism $\alpha \in [0,1]$)

$$H(d_i) = \alpha \cdot \max_{j} A_{ij} + (1-\alpha) \cdot \min_{j} A_{ij}$$

With $\alpha = 0.4$: $H(d_1)=36$, $H(d_2)=\mathbf{47}$, $H(d_3)=38$ → best is $d_2$.

!!! note "Criteria disagree"
    The same matrix gives $d_3$ (EMV, Maximax), $d_2$ (Maximin, Hurwicz), and $d_1$ (Minimax Regret). The choice of criterion encodes your attitude toward risk and regret.

```python
# pip install numpy
import numpy as np

decisions = ["d1", "d2", "d3"]
states    = ["s1", "s2", "s3"]
probs     = np.array([0.30, 0.50, 0.20])

A = np.array([
    [40, 60, 20],
    [50, 50, 45],
    [30, 80, 10],
])

# ── EMV ───────────────────────────────────────────────────────────────────────
emv = A @ probs
print("EMV:", {d: f"{v:.1f}" for d, v in zip(decisions, emv)})
print("  Best (EMV):", decisions[np.argmax(emv)])

# ── Maximin ───────────────────────────────────────────────────────────────────
print("  Best (Maximin):", decisions[np.argmax(A.min(axis=1))])

# ── Maximax ───────────────────────────────────────────────────────────────────
print("  Best (Maximax):", decisions[np.argmax(A.max(axis=1))])

# ── Minimax Regret ────────────────────────────────────────────────────────────
regret = A.max(axis=0) - A          # broadcast: subtract column max from each row
print("  Best (Minimax Regret):", decisions[np.argmin(regret.max(axis=1))])

# ── Hurwicz (α = 0.4) ─────────────────────────────────────────────────────────
alpha = 0.4
hurwicz = alpha * A.max(axis=1) + (1 - alpha) * A.min(axis=1)
print("  Best (Hurwicz α=0.4):", decisions[np.argmax(hurwicz)])

# ── EVPI ──────────────────────────────────────────────────────────────────────
ev_with_perfect_info = probs @ A.max(axis=0)
evpi = ev_with_perfect_info - emv.max()
print(f"\n  EV|PI = {ev_with_perfect_info:.1f}")
print(f"  EVPI  = {evpi:.1f}  (max worth paying for perfect information)")
```

!!! question "Check Your Understanding — Payoff Matrices"
    1. In the worked example, why does Maximin choose $d_2$ while EMV chooses $d_3$? What does this reveal about each criterion's implicit assumption?
    2. Regret is defined column-wise. Why does it make sense to compare regret only *within* a state of nature, not across states?
    3. As $\alpha \to 1$ in the Hurwicz criterion, it converges to which other criterion? As $\alpha \to 0$?

---

### 3. Decision Trees: Nodes and Backward Induction

When decisions are **sequential** — the outcome of an early choice determines what choices are available later — a payoff matrix is insufficient. A **decision tree** captures the full temporal structure.

#### Node types

| Symbol | Type | Meaning |
|--------|------|---------|
| □ | **Decision node** | The analyst chooses an action |
| ○ | **Chance node** | Nature resolves an uncertainty (probabilities on branches) |
| ◇ | **Terminal node** | The game ends; a payoff is received |

#### Backward induction (fold-back)

Work from right to left:

1. **At each terminal node** — record the payoff.
2. **At each chance node** — compute the expected value of its branches.
3. **At each decision node** — take the branch with the highest expected value (prune the rest).

#### Three-stage walkthrough: R&D investment decision

```
□ Invest in R&D (-$20K)           □ License technology (-$50K)
  │                                  │
  ○ Technical outcome                ◇ Net payoff = +$40K
  ├── Success (p=0.70)
  │     └── □ Choose next action
  │           ├── Launch product
  │           │     ○ Market outcome
  │           │     ├── Success (p=0.60)  → ◇ net = +$180K
  │           │     └── Failure (p=0.40)  → ◇ net = -$40K
  │           └── Sell patent             → ◇ net = +$60K
  └── Failure (p=0.30)
        └── ◇ Abandon                     → ◇ net = -$20K
```

**Stage 3 — Decision node after R&D success:**
- EV(Launch) = 0.60 × 180 + 0.40 × (−40) = 108 − 16 = **$92K**
- EV(Sell patent) = **$60K**
- → Best: **Launch** ($92K)

**Stage 2 — Chance node after R&D decision:**
- EV(R&D path) = 0.70 × 92 + 0.30 × (−20) = 64.4 − 6 = **$58.4K**

**Stage 1 — Root decision node:**
- EV(R&D) = $58.4K  
- EV(License) = $40K  
- → **Invest in R&D** ($58.4K > $40K) ✓

```python
# Recursive backward-induction implementation
from __future__ import annotations
from dataclasses import dataclass, field
from typing import Optional

@dataclass
class Node:
    kind: str            # 'decision', 'chance', or 'terminal'
    name: str
    payoff: float = 0.0  # used only for terminal nodes
    children: list = field(default_factory=list)   # list of (prob_or_label, Node)

def ev(node: Node) -> float:
    """Return the expected value of a node via backward induction."""
    if node.kind == "terminal":
        return node.payoff
    if node.kind == "chance":
        return sum(p * ev(child) for p, child in node.children)
    if node.kind == "decision":
        return max(ev(child) for _, child in node.children)
    raise ValueError(f"Unknown node kind: {node.kind}")

# ─── Build the R&D tree ───────────────────────────────────────────────────────
market_success = Node("chance", "Market outcome", children=[
    (0.60, Node("terminal", "Launch-Success",  payoff=180)),
    (0.40, Node("terminal", "Launch-Failure",  payoff=-40)),
])
post_rd_success = Node("decision", "Post-R&D decision", children=[
    ("Launch",       market_success),
    ("Sell patent",  Node("terminal", "Sell", payoff=60)),
])
rd_outcome = Node("chance", "Technical outcome", children=[
    (0.70, post_rd_success),
    (0.30, Node("terminal", "R&D Failure", payoff=-20)),
])
root = Node("decision", "Root", children=[
    ("R&D",     rd_outcome),
    ("License", Node("terminal", "License", payoff=40)),
])

print(f"EV(R&D path)   = ${ev(rd_outcome):.1f}K")
print(f"EV(License)    = $40.0K")
print(f"Optimal EV     = ${ev(root):.1f}K  → {'R&D' if ev(rd_outcome) > 40 else 'License'}")
```

!!! question "Check Your Understanding — Decision Trees"
    1. What happens to the optimal decision if the probability of technical success drops from 0.70 to 0.40? Recalculate by hand.
    2. Why must backward induction be performed right-to-left rather than left-to-right?
    3. A chance node with all branches leading to terminals is equivalent to what simpler object?

---

### 4. Expected Value of Perfect Information (EVPI)

**Perfect information** means you know the state of nature *before* making your decision. EVPI is the most you should ever pay for any information source.

$$\text{EV}|\text{PI} = \sum_{j=1}^{n} p_j \cdot \max_{i} A_{ij}$$

$$\boxed{\text{EVPI} = \text{EV}|\text{PI} - \max_i \text{EMV}(d_i)}$$

For our 3 × 3 example:

$$\text{EV}|\text{PI} = 0.30 \times 50 + 0.50 \times 80 + 0.20 \times 45 = 64$$

$$\text{EVPI} = 64 - 51 = \mathbf{13}$$

**Operational interpretation:** An intelligence asset that reveals the true state perfectly is worth at most 13 payoff units. If the asset costs more than 13, it is not worth acquiring regardless of any other consideration.

!!! note "EVPI is an upper bound"
    No real-world information source is perfect. EVPI therefore *bounds* the value of any intelligence, sensor, or study.

---

### 5. Expected Value of Sample Information (EVSI)

Real information sources are imperfect: they provide a *signal* $z$ that is correlated with, but not identical to, the true state $\theta_j$.

**Step-by-step Bayesian update:**

1. **Prior**: $P(\theta_j)$ — initial belief about each state of nature.
2. **Likelihood**: $P(z \mid \theta_j)$ — how likely is each signal given each state.
3. **Marginal probability of signal** (law of total probability):

$$P(z) = \sum_{j} P(z \mid \theta_j) \, P(\theta_j)$$

4. **Posterior** (Bayes' theorem):

$$P(\theta_j \mid z) = \frac{P(z \mid \theta_j) \, P(\theta_j)}{P(z)}$$

5. **Optimal decision after observing signal** $z$:

$$d^*(z) = \arg\max_i \sum_j P(\theta_j \mid z) \, A_{ij}$$

6. **EV with sample information** (average over all possible signals):

$$\text{EV}|\text{SI} = \sum_{z} P(z) \cdot \max_i \sum_j P(\theta_j \mid z) \, A_{ij}$$

$$\boxed{\text{EVSI} = \text{EV}|\text{SI} - \max_i \text{EMV}(d_i)}$$

Note: $0 \leq \text{EVSI} \leq \text{EVPI}$ always.

---

### 6. Utility Theory

EMV is only optimal when the decision-maker is **risk-neutral** — indifferent between a lottery and its expected value. In practice, most organisations and individuals are **risk-averse** for large payoffs.

#### Three risk attitudes

| Attitude | Utility shape | Example |
|----------|---------------|---------|
| **Risk-neutral** | Linear: $u(x) = x$ | Small stakes relative to wealth |
| **Risk-averse** | Concave: diminishing marginal utility | Healthcare, catastrophic risk |
| **Risk-seeking** | Convex: increasing marginal utility | Lottery tickets, desperate situations |

#### Exponential utility (most common in DA)

$$u(x) = 1 - e^{-x/R}, \quad R > 0$$

where $R$ is the **risk-tolerance** parameter (the point where the decision-maker is indifferent between $R$ for certain and a 50-50 gamble between $0$ and $2R$).

**Certainty Equivalent (CE)** — the guaranteed payoff worth the same as the risky prospect:

$$CE = u^{-1}(EU) = -R \ln(1 - EU)$$

**Risk premium** $\rho = \text{EMV} - CE \geq 0$ for risk-averse decision-makers.

```python
import numpy as np
import matplotlib.pyplot as plt

x = np.linspace(-50, 200, 400)
R_values = [50, 100, 200]   # lower R = more risk-averse

fig, ax = plt.subplots(figsize=(7, 4))
for R in R_values:
    ax.plot(x, 1 - np.exp(-x / R), label=f"R = {R} (exponential)")
ax.plot(x, x / 200, "k--", label="Risk-neutral (linear, scaled)")
ax.axhline(0, color="gray", linewidth=0.5)
ax.axvline(0, color="gray", linewidth=0.5)
ax.set_xlabel("Payoff x")
ax.set_ylabel("Utility u(x)")
ax.set_title("Risk-Averse Utility Functions")
ax.legend()
plt.tight_layout()
plt.savefig("utility_curves.png", dpi=120)
plt.show()
```

---

### 7. Multi-Attribute Utility Theory (MAUT)

When outcomes must be judged on several incommensurable dimensions (cost, speed, risk, political acceptability), a scalar payoff matrix is inadequate. MAUT constructs a composite utility function.

**Additive MAUT model** (valid when attributes are *mutually utility-independent*):

$$U(\mathbf{x}) = \sum_{i=1}^{k} w_i \, u_i(x_i), \quad \sum_i w_i = 1, \quad w_i \geq 0$$

where $u_i(x_i)$ is the single-attribute utility for attribute $i$, normalised to $[0,1]$, and $w_i$ is the swing weight.

**Weight elicitation (swing-weight method):**

1. Define $x_i^{-}$ (worst) and $x_i^{+}$ (best) for each attribute.
2. Ask: "Rank the attributes by the value of swinging each from worst to best while holding all others at worst."
3. Assign raw scores; normalise so weights sum to 1.

**Example attributes for a military COA:**

| Attribute | Weight |
|-----------|--------|
| Mission success probability | 0.45 |
| Friendly casualties (lower is better) | 0.30 |
| Collateral damage risk (lower is better) | 0.25 |

---

### 8. Sensitivity Analysis

A decision model is only as good as its inputs. Sensitivity analysis asks: *which assumptions drive the recommendation, and by how much?*

| Type | Description |
|------|-------------|
| **One-way** | Vary one parameter over a range; observe when the optimal decision changes |
| **Two-way** | Vary two parameters simultaneously; map the decision-region boundary |
| **Tornado diagram** | Rank all parameters by their single-parameter swing impact |

**Decision-flip threshold** — the critical value of a parameter at which the recommended action changes. In the payoff-matrix 3×3 example, we can find the probability $p$ at which EMV($d_3$) = EMV($d_2$):

$$30p_1 + 80p_2 + 10p_3 = 50p_1 + 50p_2 + 45p_3$$

$$-20p_1 + 30p_2 - 35p_3 = 0$$

Solving subject to $p_1 + p_2 + p_3 = 1$ traces a boundary in the probability simplex.

!!! question "Check Your Understanding — EVPI, Utility & Sensitivity"
    1. If EVPI = 0, what does that tell you about the structure of the payoff matrix?
    2. A decision-maker has $R = \$100\text{K}$. A project has EMV = $\$80\text{K}$ and CE = $\$55\text{K}$. What is the risk premium, and what does it represent?
    3. In a tornado diagram, a parameter appears very short (near the centre). Should you spend effort refining its estimate? Why or why not?

---

## Real-World Examples

### Example A — Military: Intelligence-Driven Strike Decision

!!! example "Scenario"
    A commander must decide to **Strike**, **Conduct ISR** (Intelligence, Surveillance & Reconnaissance), or **Hold** before acting. Intelligence suggests the enemy may or may not be present at the target. An ISR sortie provides an imperfect signal. All decisions have quantified payoffs on a 0–100 mission-value scale.

**Open-Source Tools:** Python, NumPy, Matplotlib

```python
# pip install numpy matplotlib
import numpy as np
import matplotlib.pyplot as plt

# ─── Problem Parameters ───────────────────────────────────────────────────────
P_ENEMY       = 0.60   # prior P(enemy present)
P_NO_ENEMY    = 0.40   # prior P(enemy absent)

# ISR sensor performance
P_DETECT      = 0.85   # P(positive signal | enemy present)  – hit rate
P_FALSE_ALARM = 0.10   # P(positive signal | enemy absent)   – false-alarm rate

# Payoffs (mission-value scale, 0–100)
V_STRIKE_PRESENT =  80   # successful strike
V_STRIKE_ABSENT  = -40   # strike on empty site / civilians
V_HOLD_PRESENT   = -20   # missed opportunity
V_HOLD_ABSENT    =  10   # correct restraint

ISR_COST = 5             # mission-value units lost to ISR delay

# ─── Without ISR: baseline decisions ─────────────────────────────────────────
EV_strike   = P_ENEMY * V_STRIKE_PRESENT + P_NO_ENEMY * V_STRIKE_ABSENT
EV_hold     = P_ENEMY * V_HOLD_PRESENT   + P_NO_ENEMY * V_HOLD_ABSENT
EV_no_info  = max(EV_strike, EV_hold)
best_no_isr = "Strike" if EV_strike >= EV_hold else "Hold"

# ─── EVPI ─────────────────────────────────────────────────────────────────────
best_if_present = max(V_STRIKE_PRESENT, V_HOLD_PRESENT)   # = 80  (Strike)
best_if_absent  = max(V_STRIKE_ABSENT,  V_HOLD_ABSENT)    # = 10  (Hold)
EV_perfect      = P_ENEMY * best_if_present + P_NO_ENEMY * best_if_absent
EVPI            = EV_perfect - EV_no_info

# ─── Bayesian update after ISR ───────────────────────────────────────────────
P_pos = P_DETECT * P_ENEMY + P_FALSE_ALARM * P_NO_ENEMY   # law of total probability
P_neg = 1 - P_pos

# Posterior via Bayes' theorem
P_enemy_given_pos = (P_DETECT * P_ENEMY)          / P_pos
P_enemy_given_neg = ((1 - P_DETECT) * P_ENEMY)    / P_neg

# Optimal decisions after each signal
def best_action(p_pres):
    ev_s = p_pres * V_STRIKE_PRESENT + (1 - p_pres) * V_STRIKE_ABSENT
    ev_h = p_pres * V_HOLD_PRESENT   + (1 - p_pres) * V_HOLD_ABSENT
    return ("Strike", ev_s) if ev_s >= ev_h else ("Hold", ev_h)

action_pos, ev_pos = best_action(P_enemy_given_pos)
action_neg, ev_neg = best_action(P_enemy_given_neg)

EV_ISR = P_pos * ev_pos + P_neg * ev_neg - ISR_COST
EVSI   = EV_ISR - EV_no_info

# ─── Print Results ────────────────────────────────────────────────────────────
print("═" * 55)
print("   INTELLIGENCE-DRIVEN STRIKE DECISION ANALYSIS")
print("═" * 55)
print(f"\n  Prior P(enemy present) = {P_ENEMY:.2f}")
print(f"\n── Without ISR ──────────────────────────────────────")
print(f"  EV(Strike) = {EV_strike:+.1f}")
print(f"  EV(Hold)   = {EV_hold:+.1f}")
print(f"  Best action: {best_no_isr}  (EV = {EV_no_info:.1f})")
print(f"\n── EVPI ─────────────────────────────────────────────")
print(f"  EV with perfect information = {EV_perfect:.1f}")
print(f"  EVPI = {EVPI:.1f}  → never pay more than {EVPI:.1f} units for certainty")
print(f"\n── After ISR signal (Bayesian update) ───────────────")
print(f"  P(positive signal)         = {P_pos:.3f}")
print(f"  P(enemy | positive signal) = {P_enemy_given_pos:.3f}  → {action_pos}")
print(f"  P(enemy | negative signal) = {P_enemy_given_neg:.3f}  → {action_neg}")
print(f"  EV(ISR path)  = {EV_ISR:.1f}")
print(f"\n── EVSI ─────────────────────────────────────────────")
print(f"  EVSI = {EVSI:.1f}")
print(f"  ISR cost = {ISR_COST}  →  ISR is {'worth it ✓' if EVSI >= ISR_COST else 'NOT worth it ✗'}")

# ─── One-Way Sensitivity: vary P(enemy present) ──────────────────────────────
p_range = np.linspace(0.05, 0.95, 300)

ev_s_range = p_range * V_STRIKE_PRESENT + (1 - p_range) * V_STRIKE_ABSENT
ev_h_range = p_range * V_HOLD_PRESENT   + (1 - p_range) * V_HOLD_ABSENT

# Analytic crossover: EV(Strike) = EV(Hold)
# p(V_SP - V_HP) + (1-p)(V_SA - V_HA) = 0
p_crossover = (V_HOLD_ABSENT - V_STRIKE_ABSENT) / (
    (V_STRIKE_PRESENT - V_HOLD_PRESENT) + (V_HOLD_ABSENT - V_STRIKE_ABSENT)
)

fig, ax = plt.subplots(figsize=(8, 4))
ax.plot(p_range, ev_s_range, color="firebrick",  linewidth=2, label="EV(Strike)")
ax.plot(p_range, ev_h_range, color="steelblue",  linewidth=2, label="EV(Hold)")
ax.axvline(p_crossover, color="gray",  linestyle="--",
           label=f"Crossover p = {p_crossover:.3f}")
ax.axvline(P_ENEMY,     color="black", linestyle=":",
           label=f"Base case p = {P_ENEMY:.2f}")
ax.fill_betweenx([-30, 50], 0.05,        p_crossover, alpha=0.07, color="steelblue")
ax.fill_betweenx([-30, 50], p_crossover, 0.95,        alpha=0.07, color="firebrick")
ax.set_xlabel("P(enemy present)")
ax.set_ylabel("Expected Value")
ax.set_title("One-Way Sensitivity: Optimal Action vs. P(enemy present)")
ax.legend(fontsize=9)
ax.grid(alpha=0.3)
plt.tight_layout()
plt.savefig("strike_sensitivity.png", dpi=120)
plt.show()

print(f"\n  Decision flips from Hold→Strike at P(enemy) = {p_crossover:.3f}")
```

---

### Example B — Civilian: Pharmaceutical Drug Development Decision

!!! example "Scenario"
    A biotech firm holds a drug candidate after Phase I. It can: **Walk Away** ($0), **Conduct Phase II** trials (cost $50M, success probability 40%), or if Phase II succeeds, choose to **License Out** ($30M guaranteed) or **Advance to Phase III** (cost $100M, approval probability 60%, approval revenue $500M). A risk-averse CFO wants the utility-optimal path, not just EMV.

**Open-Source Tools:** Python, NumPy, Matplotlib

```python
# pip install numpy matplotlib
import numpy as np
import matplotlib.pyplot as plt

# ─── Terminal net payoffs ($ millions, net of all upstream costs) ─────────────
C2          = -50.0    # Phase II cost
C3          = -100.0   # Phase III cost
REV_APPROVE =  500.0   # revenue if drug approved
REV_LICENSE =   30.0   # revenue from licensing after Phase II success

T_WALK    =  0.0                       #  0
T_P2FAIL  =  C2                        # -50
T_LICENSE =  C2 + REV_LICENSE          # -20
T_P3FAIL  =  C2 + C3                   # -150
T_APPROVE =  C2 + C3 + REV_APPROVE     # +350

p2 = 0.40    # P(Phase II success)
p3 = 0.60    # P(Phase III success | Phase II success)

print("Terminal net payoffs ($M):")
for name, val in [("Walk Away", T_WALK), ("P2 Fail", T_P2FAIL),
                  ("License",   T_LICENSE), ("P3 Fail", T_P3FAIL),
                  ("Approved",  T_APPROVE)]:
    print(f"  {name:12s}: ${val:+.0f}M")

# ─── EMV — backward induction ─────────────────────────────────────────────────
emv_p3_proceed = p3 * T_APPROVE  + (1 - p3) * T_P3FAIL     # $150M
emv_p3_license = T_LICENSE                                   # -$20M
emv_after_p2   = max(emv_p3_proceed, emv_p3_license)
p3_choice_emv  = "Proceed to P3" if emv_p3_proceed >= emv_p3_license else "License"

emv_p2_conduct = p2 * emv_after_p2 + (1 - p2) * T_P2FAIL   # +$30M
p2_choice_emv  = "Conduct P2" if emv_p2_conduct >= T_WALK else "Walk Away"

print(f"\n── EMV Analysis ──────────────────────────────────────")
print(f"  EMV(P3 proceed)        = ${emv_p3_proceed:+.1f}M")
print(f"  Decision after P2 win: {p3_choice_emv}")
print(f"  EMV(Conduct Phase II)  = ${emv_p2_conduct:+.1f}M")
print(f"  Overall EMV decision:  {p2_choice_emv}")

# ─── Risk-Averse Utility (Exponential, R = $100M) ────────────────────────────
R = 100.0    # risk-tolerance parameter ($M); lower R = more risk-averse

def u(x: float) -> float:
    """Exponential utility: u(x) = 1 - exp(-x / R)."""
    return 1.0 - np.exp(-x / R)

def ce(eu_val: float) -> float:
    """Certainty equivalent: inverse of exponential utility."""
    return -R * np.log(1.0 - eu_val)

# Fold back with utility
eu_p3_proceed = p3 * u(T_APPROVE) + (1 - p3) * u(T_P3FAIL)
eu_p3_license = u(T_LICENSE)
eu_best_p2    = max(eu_p3_proceed, eu_p3_license)
p3_choice_u   = "Proceed to P3" if eu_p3_proceed >= eu_p3_license else "License"

eu_p2_conduct = p2 * eu_best_p2 + (1 - p2) * u(T_P2FAIL)
eu_walk       = u(T_WALK)                       # = 0
p2_choice_u   = "Conduct P2" if eu_p2_conduct >= eu_walk else "Walk Away"

ce_p3_proceed = ce(eu_p3_proceed)
ce_p3_license = ce(eu_p3_license)
ce_full       = ce(eu_p2_conduct)

print(f"\n── Utility Analysis (exponential, R = ${R:.0f}M) ──────")
print(f"  CE(P3 proceed) = ${ce_p3_proceed:+.1f}M   EU = {eu_p3_proceed:+.4f}")
print(f"  CE(License)    = ${ce_p3_license:+.1f}M   EU = {eu_p3_license:+.4f}")
print(f"  Decision at P3 junction: {p3_choice_u}")
print(f"  CE(Conduct Phase II)    = ${ce_full:+.1f}M")
print(f"  CE(Walk Away)           = $0.0M")
print(f"  Overall utility decision: {p2_choice_u}")
print(f"\n  ⚠  EMV says '{p2_choice_emv}' (${emv_p2_conduct:.1f}M),")
print(f"      but utility (R=${R:.0f}M) says '{p2_choice_u}' — risk aversion flips the choice.")

# ─── Tornado Diagram: sensitivity of project EMV to ±20% parameter changes ───
def project_emv(p2_, p3_, rev_app, rev_lic, c3_abs):
    """EMV of the optimal 'Conduct Phase II' policy."""
    t_lic   = C2 + rev_lic
    t_p3f   = C2 - c3_abs
    t_app   = C2 - c3_abs + rev_app
    emv_p3_ = p3_ * t_app + (1 - p3_) * t_p3f
    return p2_ * max(emv_p3_, t_lic) + (1 - p2_) * C2

base_args = [p2, p3, REV_APPROVE, REV_LICENSE, abs(C3)]
base_emv  = project_emv(*base_args)

params = {
    "P(Phase II success)":  (p2,             0),
    "P(Phase III success)": (p3,             1),
    "Approval revenue":     (REV_APPROVE,    2),
    "License revenue":      (REV_LICENSE,    3),
    "Phase III cost":       (abs(C3),        4),
}

labels_, lo_swings, hi_swings = [], [], []
for name, (val, idx) in params.items():
    is_prob = "success" in name.lower()
    lo_val = max(val * 0.8, 0.05) if is_prob else val * 0.8
    hi_val = min(val * 1.2, 0.95) if is_prob else val * 1.2
    args_lo, args_hi = base_args.copy(), base_args.copy()
    args_lo[idx] = lo_val
    args_hi[idx] = hi_val
    ce_lo = project_emv(*args_lo)
    ce_hi = project_emv(*args_hi)
    labels_.append(name)
    lo_swings.append(min(ce_lo, ce_hi) - base_emv)
    hi_swings.append(max(ce_lo, ce_hi) - base_emv)

# Sort by total swing width
order     = sorted(range(len(labels_)), key=lambda i: hi_swings[i] - lo_swings[i], reverse=True)
labels_   = [labels_[i]    for i in order]
lo_swings = [lo_swings[i]  for i in order]
hi_swings = [hi_swings[i]  for i in order]

fig, ax = plt.subplots(figsize=(8, 4))
y = list(range(len(labels_)))
ax.barh(y, hi_swings, left=base_emv, color="steelblue", label="High (+20%)")
ax.barh(y, lo_swings, left=base_emv, color="firebrick", label="Low (−20%)")
ax.set_yticks(y)
ax.set_yticklabels(labels_)
ax.axvline(base_emv, color="black", linewidth=1.5, label=f"Base EMV = ${base_emv:.0f}M")
ax.set_xlabel("EMV of 'Conduct Phase II' path ($M)")
ax.set_title("Tornado Diagram: Sensitivity of Project EMV to ±20% Parameter Changes")
ax.legend(fontsize=9)
ax.grid(axis="x", alpha=0.3)
plt.tight_layout()
plt.savefig("pharma_tornado.png", dpi=120)
plt.show()
```

!!! question "Check Your Understanding — Examples"
    1. In Example A, EVSI < EVPI. Why must this always be true, and under what conditions would EVSI ≈ EVPI?
    2. In Example B, the EMV-optimal decision is "Conduct Phase II" but the utility-optimal is "Walk Away." What does this tell you about how the exponential utility function penalises the downside outcomes?
    3. In the tornado diagram, "License revenue" shows near-zero sensitivity. Explain analytically why varying the license revenue barely affects the EMV of the project.

---

## Interactive Exercises

### Exercise 1 — Guided (Warm-Up): Payoff Matrix Analysis

!!! question "Task"
    You are given a 3×4 payoff matrix (3 actions, 4 states of nature). Complete the TODO blocks to:
    (a) compute EMV for each action,
    (b) find the Maximin and Minimax Regret decisions,
    (c) compute EVPI.

```python
# pip install numpy
import numpy as np

actions = ["Action A1", "Action A2", "Action A3"]
states  = ["State S1", "State S2", "State S3", "State S4"]
probs   = np.array([0.25, 0.35, 0.30, 0.10])   # must sum to 1

payoff = np.array([
    [80, 50, 30, 10],   # A1
    [60, 65, 55, 20],   # A2
    [40, 45, 70, 90],   # A3
])

# ── (a) EMV ───────────────────────────────────────────────────────────────────
# TODO: compute the EMV vector (hint: matrix-vector product)
# emv = ...

# TODO: print each action's EMV and identify the best action
# for i, act in enumerate(actions):
#     print(f"  {act}: EMV = {emv[i]:.2f}")
# print(f"  Best (EMV): {actions[np.argmax(emv)]}")

# ── (b) Maximin ───────────────────────────────────────────────────────────────
# TODO: compute the worst-case payoff for each action
# row_min = ...
# print(f"  Best (Maximin): {actions[np.argmax(row_min)]}")

# ── (b) Minimax Regret ────────────────────────────────────────────────────────
# TODO: build the regret matrix (hint: subtract each element from its column max)
# regret = ...

# TODO: find the action with the smallest maximum regret
# print(f"  Best (Minimax Regret): {actions[np.argmin(regret.max(axis=1))]}")

# ── (c) EVPI ─────────────────────────────────────────────────────────────────
# TODO: compute EV|PI (expected value of the best action in each state)
# ev_pi = ...

# TODO: compute EVPI = EV|PI - best EMV
# evpi = ...
# print(f"\n  EVPI = {evpi:.2f}")
# print(f"  Interpretation: never pay more than {evpi:.2f} payoff units for perfect information.")
```

### Exercise 2 — Exploratory: COA Analysis with Sensitivity Sweep

!!! question "Task"
    A battalion commander evaluates three COAs against three possible enemy COAs (eCOA 1, 2, 3) with estimated probabilities $[0.50, 0.30, 0.20]$.

    1. Build the payoff matrix (mission success scores below).
    2. Evaluate all five decision criteria (EMV, Maximin, Maximax, Minimax Regret, Hurwicz $\alpha=0.4$).
    3. Perform a sensitivity sweep: vary $P(\text{eCOA 1})$ from 0.10 to 0.90 (keeping the ratio of the other two fixed). Plot EMV for each COA. Mark the **breakeven probabilities** where the recommended COA switches.

```python
import numpy as np
import matplotlib.pyplot as plt

coas  = ["COA Alpha", "COA Bravo", "COA Charlie"]
ecoas = ["eCOA 1", "eCOA 2", "eCOA 3"]
probs = np.array([0.50, 0.30, 0.20])

payoff = np.array([
    [70, 50, 40],   # COA Alpha
    [55, 75, 60],   # COA Bravo
    [45, 60, 85],   # COA Charlie
])

# TODO: Print results for all five criteria using functions from the Lecture section.

# TODO: Sensitivity sweep
# p1_range = np.linspace(0.10, 0.90, 200)
# For each p1, distribute the remainder [0.30, 0.20] proportionally:
#   p_rest = 1 - p1
#   probs_sweep = np.array([p1, p_rest * (0.30/0.50), p_rest * (0.20/0.50)])
#   # The ratios 0.30/0.50 and 0.20/0.50 keep the relative weights of eCOA 2 and 3
#   # fixed at their original proportions (60% and 40% of the non-eCOA-1 mass).
#
# TODO: compute EMV for each COA at each p1, plot, and annotate breakeven points.
```

### Exercise 3 — Challenge: Multi-Stage Decision Tree with Bayesian Updating and MAUT

!!! question "Task"
    An analyst can collect intelligence at two levels before recommending a strike:

    - **Tactical Recon** (cheap, noisy): costs 3 MV units; hit-rate 0.70, false-alarm 0.25.
    - **Strategic ISR** (expensive, reliable): costs 8 MV units; hit-rate 0.92, false-alarm 0.05.

    The analyst may choose any combination (none, recon only, ISR only, recon then ISR) before deciding Strike or Hold.

    Terminal payoffs are evaluated on **three attributes** using additive MAUT:
    | Attribute | Weight | Scale |
    |-----------|--------|-------|
    | Mission success | 0.50 | 0–100 |
    | Friendly casualties (inverted) | 0.30 | 0–100 |
    | Collateral damage (inverted) | 0.20 | 0–100 |

    **Your tasks:**
    1. Represent all decision and chance nodes as a recursive `Node` structure (extend the `Node` class from the Lecture).
    2. Implement backward induction using the **MAUT score** as the terminal payoff.
    3. Apply Bayes' theorem at each information node to update $P(\text{enemy present})$.
    4. Elicit weights interactively from the command line (or use the table above as defaults).
    5. Print the optimal policy and visualise the tree using `matplotlib` (nodes as shapes, branches as arrows, optimal path highlighted in gold).

    **Starter skeleton:**

```python
import numpy as np

# ─── Parameters ───────────────────────────────────────────────────────────────
P_PRIOR     = 0.60   # prior P(enemy present)
MAUT_WEIGHTS = {"mission": 0.50, "friendly_cas": 0.30, "collateral": 0.20}

# Raw attribute payoffs [enemy_present, enemy_absent] for Strike and Hold
RAW_PAYOFFS = {
    #              mission   friendly_cas   collateral
    "Strike": {"present": [90, 80, 30], "absent": [10, 70, 20]},
    "Hold":   {"present": [20, 95, 90], "absent": [70, 98, 95]},
}

def maut_score(action: str, enemy_present: bool) -> float:
    """Compute the MAUT score for a terminal node."""
    state = "present" if enemy_present else "absent"
    attrs = RAW_PAYOFFS[action][state]
    w = list(MAUT_WEIGHTS.values())
    return sum(wi * ai for wi, ai in zip(w, attrs))

def bayes_update(p_prior: float, hit_rate: float, false_alarm: float,
                 signal_positive: bool) -> float:
    """Return P(enemy present | signal) using Bayes' theorem.

    Hint:
      P(signal | present)  = hit_rate       if positive,  1 - hit_rate    if negative
      P(signal | absent)   = false_alarm    if positive,  1 - false_alarm if negative
      P(signal)            = P(s|pres)*p_prior + P(s|abs)*(1-p_prior)
      posterior            = P(s|pres) * p_prior / P(signal)
    """
    # TODO: implement using the hint above
    pass

# TODO: Build the full decision tree and run backward induction.
# TODO: Visualise the tree with matplotlib.
```

!!! question "Check Your Understanding — Exercises"
    1. In Exercise 1, does the Minimax Regret criterion always agree with the EMV criterion? Construct a simple 2×2 example where they disagree.
    2. In Exercise 2, what is the *mathematical* condition for a breakeven probability between two COAs? How would you solve it analytically for the 3-state case?
    3. In Exercise 3, adding a second intelligence source (ISR after Recon) can *lower* the expected value of the first source. Under what conditions does this happen?

---

!!! tip "Prompt Template"
    Use the [Agent Prompt](prompt.md) for this topic, or the [Generic Prompt Template](../prompt-template.md) to generate additional lectures, examples, and exercises.
