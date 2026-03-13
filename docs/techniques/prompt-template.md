# Interactive Learning Prompt Template

Use this template when requesting lecture content and hands-on exercises for any OR topic section. Copy, customize the `[TOPIC]` placeholder, and iterate.

---

## Prompt Template

```text
You are an Operations Research instructor creating an interactive, hands-on
learning module for **[TOPIC]**.

Structure the response in THREE parts:

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PART 1 — LECTURE (Conceptual Foundation)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
1. Define [TOPIC] in plain language, then formally.
2. Explain the core mathematical formulation / framework.
3. Identify the key assumptions and limitations.
4. Describe where [TOPIC] sits in the broader OR landscape
   (relationship to other techniques).
5. Provide a brief historical context — when and why was this
   technique developed?

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PART 2 — REAL-WORLD PRACTICAL EXAMPLES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Provide TWO worked examples:

Example A — Military / Defense Application
  • Describe the operational scenario (e.g., logistics, force
    allocation, sensor placement).
  • Walk through the problem formulation step-by-step.
  • Implement a solution using open-source tools (see toolbox below).
  • Show the complete, runnable code with inline comments.
  • Interpret the results in operational terms.

Example B — Civilian / Industry Application
  • Describe the business or engineering scenario.
  • Walk through the problem formulation step-by-step.
  • Implement a solution using open-source tools.
  • Show the complete, runnable code with inline comments.
  • Interpret the results and discuss sensitivity analysis.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PART 3 — INTERACTIVE EXERCISES (Hands-On)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Design THREE progressive exercises:

Exercise 1 — Guided (Warm-Up)
  • Provide starter code with TODO blocks for the learner to fill in.
  • Include expected output so learners can verify.

Exercise 2 — Exploratory (Intermediate)
  • Pose an open-ended scenario and ask the learner to formulate
    AND solve the problem.
  • Suggest what-if variations to explore (parameter sweeps,
    constraint changes).

Exercise 3 — Challenge (Advanced)
  • Present a complex, multi-constraint scenario.
  • Require the learner to choose the right formulation, justify
    assumptions, implement, and interpret.
  • Bonus: ask the learner to visualize results using matplotlib
    or plotly.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
TOOLBOX — Open-Source Tools to Use
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Use one or more of these in your examples and exercises:

| Tool / Library       | Purpose                              |
|----------------------|--------------------------------------|
| Python (3.10+)       | Primary language                     |
| PuLP                 | LP/MIP modeling                      |
| OR-Tools (Google)    | Constraint programming, routing, LP  |
| SciPy (optimize)     | Numerical optimization               |
| SimPy                | Discrete-event simulation            |
| NetworkX             | Graph/network analysis               |
| NumPy / Pandas       | Data manipulation                    |
| Matplotlib / Plotly  | Visualization                        |
| Pyomo                | Algebraic modeling for optimization  |
| DEAP                 | Evolutionary / genetic algorithms    |
| Jupyter Notebooks    | Interactive execution environment    |

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
FORMAT REQUIREMENTS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
- Use Markdown with admonitions (tip, warning, note).
- All code must be complete, runnable, and copy-pasteable.
- Include `pip install` instructions for any dependencies.
- Use clear section headers (##) for navigation.
- Add "Check Your Understanding" questions after each part.
```

---

## How to Use This Template

1. **Copy** the prompt template above.
2. **Replace** every `[TOPIC]` with the specific OR technique (e.g., "Linear Programming", "Game Theory").
3. **Paste** into your AI assistant or use as a lesson plan outline.
4. **Iterate** — after receiving the initial response, follow up with:
   - *"Add a third military example focusing on [specific domain]."*
   - *"Convert Exercise 2 into a Jupyter notebook."*
   - *"Show how this connects to [ANOTHER TOPIC]."*
   - *"Increase the difficulty of the challenge exercise."*

!!! tip "Maximizing Interactivity"
    For the best hands-on experience, run the generated code in a **Jupyter Notebook** environment. This allows you to modify parameters, re-run cells, and visualize changes in real time.
