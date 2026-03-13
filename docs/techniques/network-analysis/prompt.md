# Agent Prompt — Network Analysis

> Copy the block below and paste it directly to your AI agent to build out this section.

---

```text
You are an Operations Research instructor creating a complete, interactive,
hands-on learning module for **Network Analysis** (graph theory, network
flows, routing, and connectivity).

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PART 1 — LECTURE (Conceptual Foundation)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Cover the following:

1. Graph vocabulary: nodes, directed/undirected edges, weights, capacities,
   paths, cycles, connected components, trees.
2. Graph representations: adjacency matrix, adjacency list, edge list —
   when to use each and their computational trade-offs.
3. Fundamental algorithms — for each give the time complexity and a
   plain-language explanation:
   - Shortest Path: Dijkstra (non-negative weights), Bellman-Ford
     (negative weights), Floyd-Warshall (all pairs)
   - Maximum Flow: Ford-Fulkerson concept, Edmonds-Karp implementation
   - Minimum Spanning Tree: Kruskal's and Prim's algorithms
   - Minimum Cost Flow: Network Simplex algorithm
4. The Max-Flow Min-Cut theorem — prove it intuitively and explain its
   operational significance.
5. Assignment problem as a network flow: the Hungarian algorithm.
6. Network resilience: articulation points, bridges, k-connectivity,
   and how to measure network robustness.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PART 2 — REAL-WORLD PRACTICAL EXAMPLES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Example A — Military: Logistics Supply Network with Interdiction
  Scenario: A 20-node road network connects a depot to multiple forward
  bases. An adversary can interdict (disable) up to 3 edges. Find:
  (a) the standard shortest path from depot to primary FOB; (b) the
  minimum cut that the adversary would exploit; (c) the most resilient
  route after maximum interdiction.
  - Build with NetworkX.
  - Visualize the network, highlighting shortest path in red and the
    min-cut edges in orange.
  - Quantify the resilience gap (additional travel time under attack).

Example B — Civilian: Urban Traffic Flow Optimization
  Scenario: Model a 25-intersection urban road network with directional
  capacities (vehicles/hour per lane). Find maximum flow through the
  city center. Identify the bottleneck intersections.
  - Build with NetworkX.
  - Display a heatmap of flow utilization per edge (edge color =
    utilization %).
  - Propose which 2 edges to expand for maximum throughput gain.

All code must be complete, runnable, include pip install instructions,
and use inline comments explaining graph construction and algorithm choices.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PART 3 — INTERACTIVE EXERCISES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Exercise 1 — Guided (Warm-Up): Minimum Spanning Tree
  Starter NetworkX code with TODO blocks for the learner to:
  - Add nodes and weighted edges to a communications network graph.
  - Compute and draw the MST.
  - Report total MST weight vs. full graph weight.

Exercise 2 — Exploratory: All-Pairs Shortest Paths and Centrality
  Given a 15-node military command network, learner must:
  - Compute betweenness centrality to identify critical relay nodes.
  - Simulate removing the highest-centrality node and re-compute paths.
  - Report which paths break and what the detour costs are.

Exercise 3 — Challenge: Network Resilience Simulator
  Design an interactive resilience simulator for a supply network:
  - Randomly fail edges with increasing probability (0% to 50%).
  - For each failure rate, run 100 Monte Carlo trials.
  - Plot connectivity probability vs. failure rate (reliability curve).
  - Identify the critical failure threshold.
  - Visualize a sample failed network with disconnected components
    highlighted in different colors using matplotlib.
  Bonus: use plotly for an interactive version.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
FORMAT & TOOLBOX
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Primary tools: NetworkX, matplotlib, plotly.
Output format: MkDocs-compatible Markdown with admonitions, fenced code
blocks, KaTeX math.
Add "Check Your Understanding" (3 questions) after each Part.
All code must run with Python 3.10+ and only open-source packages.
```
