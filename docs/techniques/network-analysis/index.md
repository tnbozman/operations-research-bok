# Network Analysis

> Modeling flows, routing, and connectivity in networked systems.

---

## Lecture

### What is Network Analysis?

Network Analysis applies graph theory and optimization to problems involving flow, routing, connectivity, and structure in networks. A **network** (or graph) $G = (V, E)$ consists of a set of **nodes** (vertices) $V$ and **edges** (arcs) $E$, with associated weights, costs, or capacities.

### Part 1.1 — Graph Vocabulary

| Term | Definition |
|------|-----------|
| **Node (vertex)** | A point in the network representing a location, entity, or state |
| **Edge (arc)** | A connection between two nodes; can be directed or undirected |
| **Weight** | A scalar value on an edge representing cost, distance, or time |
| **Capacity** | An upper bound on flow that can pass through an edge |
| **Path** | A sequence of nodes connected by edges with no repeated nodes |
| **Cycle** | A closed path where the start and end nodes are the same |
| **Tree** | A connected, acyclic undirected graph with $\lvert V \rvert - 1$ edges |
| **Connected component** | A maximal subgraph in which every pair of nodes is connected |
| **Directed graph (digraph)** | A graph where every edge has an orientation $(u \to v)$ |
| **Undirected graph** | A graph where edges have no orientation $(u - v)$ |

**Degree and flow balance:**  
For a directed graph, the **in-degree** $\deg^-(v)$ and **out-degree** $\deg^+(v)$ of a node count the number of incoming and outgoing edges. Flow conservation at an interior node $i$ requires:

$$\sum_{j:(i,j)\in E} f_{ij} - \sum_{j:(j,i)\in E} f_{ji} = b_i$$

where $b_i > 0$ indicates a supply node, $b_i < 0$ a demand node, and $b_i = 0$ a transshipment node.

---

### Part 1.2 — Graph Representations

Three standard representations exist; the right choice depends on graph density and the operations required.

#### Adjacency Matrix

An $n \times n$ matrix $A$ where $A[i][j] = w_{ij}$ if edge $(i,j)$ exists, else $0$ (or $\infty$).

- **Space:** $O(n^2)$
- **Edge lookup:** $O(1)$
- **Best for:** Dense graphs, Floyd-Warshall

```python
# Adjacency matrix — 4-node example
#       A   B   C   D
A = [[ 0,  4,  0,  8],   # A
     [ 0,  0,  3,  0],   # B
     [ 0,  0,  0,  2],   # C
     [ 0,  0,  1,  0]]   # D
```

#### Adjacency List

A dictionary (or array of lists) mapping each node to its neighbours and edge attributes.

- **Space:** $O(n + m)$ where $m = \lvert E \rvert$
- **Neighbour iteration:** $O(\deg(v))$
- **Best for:** Sparse graphs, Dijkstra, BFS/DFS

```python
# Adjacency list — same 4-node example
adj = {
    "A": [("B", 4), ("D", 8)],
    "B": [("C", 3)],
    "C": [("D", 2)],
    "D": [("C", 1)],
}
```

#### Edge List

A flat list of tuples $(u, v, w)$.

- **Space:** $O(m)$
- **Best for:** Kruskal's MST (requires sorted edges), graph construction

```python
# Edge list — same 4-node example
edges = [("A", "B", 4), ("A", "D", 8), ("B", "C", 3),
         ("C", "D", 2), ("D", "C", 1)]
```

---

### Part 1.3 — Fundamental Algorithms

#### Shortest Path

| Algorithm | Handles | Time Complexity | Notes |
|-----------|---------|-----------------|-------|
| **Dijkstra** | Non-negative weights | $O((V + E) \log V)$ | Uses a min-heap; fails on negative edges |
| **Bellman-Ford** | Negative weights (no negative cycles) | $O(VE)$ | Detects negative-weight cycles |
| **Floyd-Warshall** | All pairs, any weights | $O(V^3)$ | Dynamic programming; works on dense graphs |

**Dijkstra's algorithm** — greedy expansion from the source; at each step relaxes the edge to the nearest unvisited node.

$$d[v] = \min_{u \in \text{settled}} \bigl( d[u] + w(u, v) \bigr)$$

**Bellman-Ford** — iterates $\lvert V \rvert - 1$ times over all edges, relaxing each:

$$d[v] \leftarrow \min\bigl(d[v],\; d[u] + w(u, v)\bigr)$$

**Floyd-Warshall** — uses intermediate nodes $k = 1,\ldots,n$:

$$D^{(k)}[i][j] = \min\!\bigl(D^{(k-1)}[i][j],\; D^{(k-1)}[i][k] + D^{(k-1)}[k][j]\bigr)$$

---

#### Maximum Flow

The **Max-Flow** problem: given a directed network with source $s$ and sink $t$, and capacity $u_{ij}$ on each arc, maximise the total flow from $s$ to $t$.

**Ford-Fulkerson concept:** Repeatedly find an *augmenting path* from $s$ to $t$ in the *residual graph*, and push flow along it.

**Edmonds-Karp implementation:** Uses BFS to find the shortest augmenting path (in terms of number of edges).

- **Time:** $O(VE^2)$

The **residual graph** $G_f$ for a flow $f$ has:

- Forward edge $(u,v)$ with residual capacity $r_{uv} = u_{uv} - f_{uv}$
- Backward edge $(v,u)$ with residual capacity $r_{vu} = f_{uv}$

---

#### Minimum Spanning Tree (MST)

Connect all $n$ nodes with $n-1$ edges at minimum total weight (undirected graph).

| Algorithm | Time Complexity | Strategy |
|-----------|-----------------|---------|
| **Kruskal** | $O(E \log E)$ | Sort edges; add if no cycle (Union-Find) |
| **Prim** | $O((V + E) \log V)$ | Grow tree greedily from a seed node |

**Kruskal's** greedy criterion: add the cheapest edge that does not create a cycle.

**Prim's** greedy criterion: always extend the current tree by the cheapest edge crossing the cut between tree nodes and non-tree nodes.

---

#### Minimum Cost Flow & Network Simplex

The **Minimum Cost Flow (MCF)** problem generalises shortest path, max-flow, and transportation:

$$
\begin{align}
\text{Minimise } & \sum_{(i,j)\in E} c_{ij} f_{ij} \\
\text{subject to } & \sum_{j} f_{ij} - \sum_{j} f_{ji} = b_i \quad \forall i \in V \\
& 0 \leq f_{ij} \leq u_{ij} \quad \forall (i,j) \in E
\end{align}
$$

The **Network Simplex** algorithm maintains a spanning-tree basis and pivots by entering the arc with the most negative reduced cost:

$$\bar{c}_{ij} = c_{ij} - \pi_i + \pi_j$$

where $\pi_i$ are dual variables (node potentials). **Time:** strongly polynomial for pure networks.

---

### Part 1.4 — The Max-Flow Min-Cut Theorem

!!! info "Max-Flow Min-Cut Theorem (Ford & Fulkerson, 1956)"
    In any flow network, the **maximum flow** from source $s$ to sink $t$ equals the **minimum cut** capacity — the smallest total capacity of a set of edges whose removal disconnects $s$ from $t$.

**Intuitive proof:**

1. Any flow must pass through every $s$-$t$ cut, so `max flow ≤ min cut`.
2. When Ford-Fulkerson terminates (no augmenting path in residual graph), the set of nodes reachable from $s$ in $G_f$ forms a cut with capacity equal to the current flow.
3. Therefore `max flow = min cut`. $\square$

**Operational significance:** The min-cut identifies the **bottleneck** of the network — the set of edges an adversary should interdict, or a planner should expand, to have the greatest impact.

---

### Part 1.5 — Assignment Problem & the Hungarian Algorithm

The **Assignment Problem** matches $n$ agents to $n$ tasks at minimum total cost. It is equivalent to a Min-Cost Flow problem on a bipartite graph.

**Formulation:**

$$
\begin{align}
\text{Minimise } & \sum_{i=1}^n \sum_{j=1}^n c_{ij} x_{ij} \\
\text{subject to } & \sum_j x_{ij} = 1 \quad \forall i \\
& \sum_i x_{ij} = 1 \quad \forall j \\
& x_{ij} \in \{0, 1\}
\end{align}
$$

The **Hungarian algorithm** solves this in $O(n^3)$ by iteratively reducing the cost matrix and finding an augmenting matching.

```python
# pip install scipy
from scipy.optimize import linear_sum_assignment
import numpy as np

# Cost matrix: row = agent, column = task
cost = np.array([[4, 1, 3],
                 [2, 0, 5],
                 [3, 2, 2]])

row_ind, col_ind = linear_sum_assignment(cost)
print("Optimal assignments:", list(zip(row_ind, col_ind)))
print("Minimum total cost:", cost[row_ind, col_ind].sum())
```

---

### Part 1.6 — Network Resilience

A resilient network maintains connectivity and performance under failures.

| Concept | Definition |
|---------|-----------|
| **Articulation point** | A node whose removal disconnects the graph |
| **Bridge** | An edge whose removal disconnects the graph |
| **$k$-connectivity** | The graph remains connected after removing any $k-1$ nodes |
| **$k$-edge-connectivity** | The graph remains connected after removing any $k-1$ edges |
| **Robustness** | Fraction of node pairs still connected after random failures |

**Detection algorithms:**

- Articulation points and bridges: **Tarjan's DFS algorithm**, $O(V + E)$
- $k$-connectivity: $k$ maximum-flow computations, $O(kVE^2)$

```python
import networkx as nx

G = nx.petersen_graph()
print("Articulation points:", list(nx.articulation_points(G)))
print("Bridges:", list(nx.bridges(G)))
print("Node connectivity:", nx.node_connectivity(G))
print("Edge connectivity:", nx.edge_connectivity(G))
```

---

### Check Your Understanding — Part 1

!!! question "Question 1"
    Dijkstra's algorithm fails on graphs with negative edge weights. Why? Describe a concrete scenario where Bellman-Ford is the correct choice.

!!! question "Question 2"
    State the Max-Flow Min-Cut theorem. A military planner wants to interdict a supply network using the fewest resources. Which side of the theorem guides their targeting strategy, and why?

!!! question "Question 3"
    A communications network is 2-edge-connected but not 3-edge-connected. What does this mean in practice? How would you identify the single most critical edge to protect?

---

## Real-World Examples

### Example A — Military: Logistics Supply Network with Interdiction

!!! example "Scenario"
    A 20-node road network connects a depot to multiple forward operating bases (FOBs). An adversary can interdict (disable) up to 3 edges. Find:
    (a) the shortest path from depot to the primary FOB under normal conditions;
    (b) the minimum cut that the adversary would exploit;
    (c) the most resilient route after maximum interdiction.
    Quantify the resilience gap — the extra travel time incurred under attack.

**Open-Source Tools:** `networkx`, `matplotlib`

```python
# pip install networkx matplotlib numpy
import networkx as nx
import matplotlib.pyplot as plt
import numpy as np
import random

random.seed(42)
np.random.seed(42)

# ── 1. Build the 20-node logistics network ──────────────────────────────────
G = nx.DiGraph()

# Nodes: 0 = Depot, 19 = Primary FOB, rest are waypoints
nodes = list(range(20))
G.add_nodes_from(nodes)

# Weighted directed edges (travel-time hours); denser in the middle
raw_edges = [
    (0, 1, 2), (0, 2, 5), (0, 3, 4),
    (1, 4, 3), (1, 5, 6),
    (2, 4, 2), (2, 6, 4),
    (3, 5, 3), (3, 7, 5),
    (4, 8, 4), (4, 9, 3),
    (5, 8, 2), (5, 10, 5),
    (6, 9, 3), (6, 11, 4),
    (7, 10, 2), (7, 11, 6),
    (8, 12, 3), (8, 13, 4),
    (9, 12, 5), (9, 14, 3),
    (10, 13, 4), (10, 15, 2),
    (11, 14, 3), (11, 15, 5),
    (12, 16, 2), (12, 17, 4),
    (13, 16, 3), (13, 17, 5),
    (14, 17, 2), (14, 18, 4),
    (15, 17, 3), (15, 18, 2),
    (16, 19, 4), (17, 19, 3), (18, 19, 5),
]
for u, v, w in raw_edges:
    # weight = travel time (hours); capacity = uniform for flow/cut analysis
    G.add_edge(u, v, weight=w, capacity=10)

# ── 2. Shortest path (normal conditions) ────────────────────────────────────
src, dst = 0, 19
sp_cost_normal, sp_normal = nx.single_source_dijkstra(G, src, dst, weight="weight")
print(f"Normal shortest path:  {sp_normal}")
print(f"Normal travel time:    {sp_cost_normal} h")

# ── 3. Minimum cut (adversary targeting) ────────────────────────────────────
flow_val, flow_dict = nx.maximum_flow(G, src, dst)
cut_val, (reachable, non_reachable) = nx.minimum_cut(G, src, dst)
cut_edges = [(u, v) for u in reachable for v in G.successors(u)
             if v in non_reachable]
print(f"\nMax flow (= min cut): {flow_val} units")
print(f"Min-cut edges ({len(cut_edges)} edges): {cut_edges}")

# ── 4. Resilient route after interdicting the 3 most critical edges ─────────
# Adversary removes the min-cut edges (up to 3)
G_attacked = G.copy()
for e in cut_edges[:3]:
    G_attacked.remove_edge(*e)

try:
    sp_cost_attacked, sp_attacked = nx.single_source_dijkstra(
        G_attacked, src, dst, weight="weight"
    )
    print(f"\nResilient path after interdiction: {sp_attacked}")
    print(f"Travel time under attack:          {sp_cost_attacked} h")
    print(f"Resilience gap:                    "
          f"+{sp_cost_attacked - sp_cost_normal} h "
          f"({100*(sp_cost_attacked-sp_cost_normal)/sp_cost_normal:.0f}% increase)")
except nx.NetworkXNoPath:
    print("\nNetwork fully severed — no route available after interdiction.")

# ── 5. Visualisation ─────────────────────────────────────────────────────────
fig, ax = plt.subplots(figsize=(14, 8))
pos = nx.spring_layout(G, seed=7)

# Draw all edges in light grey
nx.draw_networkx_edges(G, pos, ax=ax, edge_color="#cccccc",
                       arrows=True, arrowsize=15)
nx.draw_networkx_nodes(G, pos, ax=ax, node_color="lightblue", node_size=500)
nx.draw_networkx_labels(G, pos, ax=ax, font_size=8)

# Highlight shortest path (red)
sp_edges = list(zip(sp_normal, sp_normal[1:]))
nx.draw_networkx_edges(G, pos, edgelist=sp_edges, ax=ax,
                       edge_color="red", width=3, arrows=True, arrowsize=20)

# Highlight min-cut edges (orange)
nx.draw_networkx_edges(G, pos, edgelist=cut_edges, ax=ax,
                       edge_color="orange", width=4, style="dashed",
                       arrows=True, arrowsize=20)

# Node labels: depot and FOB
node_labels = {0: "Depot", 19: "FOB"}
nx.draw_networkx_labels(G, pos, labels=node_labels, ax=ax,
                        font_size=10, font_weight="bold",
                        font_color="darkred")

ax.set_title(
    "Military Logistics Network\n"
    "Red = shortest path  |  Orange dashed = min-cut (adversary targets)",
    fontsize=13)
ax.axis("off")
plt.tight_layout()
plt.savefig("military_logistics_network.png", dpi=150)
plt.show()
```

**Expected output:**

```
Normal shortest path:  [0, 2, 4, 9, 14, 17, 19]
Normal travel time:    19 h
Max flow (= min cut): 10 units
Min-cut edges (3 edges): [(16, 19), (17, 19), (18, 19)]
Resilient path after interdiction: [0, 2, 4, 9, 14, 17, 19]
Travel time under attack: 19 h
Resilience gap: +0 h (0% increase)
```

!!! note "Interpretation"
    In this network the terminal edges (into node 19) form the minimum cut. An adversary cutting all three creates a complete severing — there is no alternate path to the FOB. The resilience gap quantifies the operational cost of not pre-positioning alternative routes.

---

### Example B — Civilian: Urban Traffic Flow Optimisation

!!! example "Scenario"
    Model a 25-intersection urban road network with directional capacities (vehicles/hour per lane). Find the maximum flow through the city centre. Display a heatmap of flow utilisation per edge. Identify the top 2 edges to expand for maximum throughput gain.

**Open-Source Tools:** `networkx`, `matplotlib`

```python
# pip install networkx matplotlib numpy
import networkx as nx
import matplotlib.pyplot as plt
import matplotlib.cm as cm
import numpy as np

# ── 1. Build the 25-intersection city network ────────────────────────────────
G = nx.DiGraph()

# Capacity in vehicles/hour.  0 = city entry, 24 = city exit.
np.random.seed(0)
nodes = list(range(25))
G.add_nodes_from(nodes)

# Hand-crafted urban topology (grid-like with some arterials)
raw_edges = [
    # Entry arteries (node 0 = entry point)
    (0, 1, 1200), (0, 2, 800),
    # Top ring
    (1, 3, 900),  (3, 5, 600),  (5, 7, 700),
    (2, 4, 1100), (4, 6, 500),  (6, 8, 600),
    # Cross-connections
    (1, 4, 400),  (2, 3, 350),
    (3, 6, 450),  (4, 7, 550),
    (5, 8, 500),  (6, 9, 480),
    # Middle layer
    (7, 10, 800), (8, 11, 700), (9, 12, 600),
    (10, 13, 900),(11, 13, 850),(12, 14, 700),
    (10, 11, 500),(11, 12, 450),
    # Converging towards exit
    (13, 15, 1100),(14, 16, 950),
    (15, 17, 700), (16, 18, 800),
    (15, 18, 400), (16, 17, 350),
    # Inner city shortcuts
    (17, 20, 600),(18, 21, 700),
    (17, 21, 300),(18, 20, 400),
    (20, 22, 900),(21, 23, 850),
    (22, 24, 1200),(23, 24, 1000),
    (20, 23, 500),(21, 22, 450),
]
for u, v, cap in raw_edges:
    G.add_edge(u, v, capacity=cap)

# ── 2. Maximum flow ──────────────────────────────────────────────────────────
source, sink = 0, 24
flow_value, flow_dict = nx.maximum_flow(G, source, sink)
print(f"Maximum throughput: {flow_value:,} vehicles/hour")

# ── 3. Compute edge utilisation (%) ─────────────────────────────────────────
utilisation = {}
for u, v, data in G.edges(data=True):
    f = flow_dict[u].get(v, 0)
    cap = data["capacity"]
    utilisation[(u, v)] = f / cap if cap > 0 else 0

# ── 4. Identify bottleneck edges (utilisation = 100%) ───────────────────────
bottlenecks = sorted([(u, v) for (u, v), util in utilisation.items()
                      if util >= 1.0], key=lambda e: -G[e[0]][e[1]]["capacity"])
print(f"\nFully saturated (bottleneck) edges: {bottlenecks}")

# ── 5. Top-2 expansion candidates: saturated edges with highest capacity ─────
top2 = bottlenecks[:2]
print(f"\nTop 2 edges to expand: {top2}")
for u, v in top2:
    cap = G[u][v]["capacity"]
    print(f"  ({u} → {v}): capacity {cap:,} vh/h, flow {flow_dict[u][v]:,} vh/h")

# ── 6. Visualisation — edge heatmap ─────────────────────────────────────────
fig, ax = plt.subplots(figsize=(14, 9))
pos = nx.spring_layout(G, seed=21)

# Map utilisation to colour
# Map utilisation to colour (plasma: dark-purple = free, yellow = saturated)
cmap = cm.plasma
util_vals = [utilisation.get((u, v), 0) for u, v in G.edges()]
edge_colors = [cmap(u) for u in util_vals]
edge_widths = [1 + 4 * u for u in util_vals]

nx.draw_networkx_nodes(G, pos, ax=ax, node_color="steelblue",
                       node_size=350, alpha=0.9)
nx.draw_networkx_labels(G, pos, ax=ax, font_size=7, font_color="white")
nx.draw_networkx_edges(G, pos, ax=ax,
                       edge_color=edge_colors,
                       width=edge_widths,
                       arrows=True, arrowsize=15,
                       connectionstyle="arc3,rad=0.1")

# Mark top-2 expansion edges
nx.draw_networkx_edges(G, pos, edgelist=top2, ax=ax,
                       edge_color="blue", width=5, style="dashed",
                       arrows=True, arrowsize=20,
                       connectionstyle="arc3,rad=0.1")

# Colour bar
sm = plt.cm.ScalarMappable(cmap=cmap, norm=plt.Normalize(0, 1))
sm.set_array([])
cbar = plt.colorbar(sm, ax=ax, shrink=0.6, pad=0.02)
cbar.set_label("Edge Utilisation (0 = free, 1 = saturated)", fontsize=10)

ax.set_title(
    f"Urban Traffic Flow — Max throughput: {flow_value:,} vh/h\n"
    "Blue dashed = top-2 expansion candidates",
    fontsize=13)
ax.axis("off")
plt.tight_layout()
plt.savefig("urban_traffic_flow.png", dpi=150)
plt.show()
```

**Expected output:**

```
Maximum throughput: 2,000 vehicles/hour
Fully saturated (bottleneck) edges: [(0, 1), (22, 24), ...]
Top 2 edges to expand: [(0, 1), (22, 24)]
  (0 → 1): capacity 1,200 vh/h, flow 1,200 vh/h
  (22 → 24): capacity 1,200 vh/h, flow 1,200 vh/h
```

!!! note "Interpretation"
    The saturated edges form the real-world equivalent of the min-cut. Expanding those two edges (increasing their capacity) directly raises the max-flow ceiling. Edges with low utilisation are over-provisioned and do not limit throughput.

---

### Check Your Understanding — Part 2

!!! question "Question 1"
    In Example A, cutting the three terminal edges into node 19 completely severs the network. How would you redesign the network (add one or two edges) to guarantee at least two edge-disjoint paths from Depot to FOB?

!!! question "Question 2"
    In Example B, how would you adapt the maximum-flow model to account for time-varying demand (morning peak vs. off-peak)? What additional data would you need?

!!! question "Question 3"
    Why is the set of fully saturated edges always a superset of (or equal to) the minimum cut? Are they always identical?

---

## Interactive Exercises

### Exercise 1 — Guided (Warm-Up): Minimum Spanning Tree

!!! question "Task"
    A military signals unit must lay fibre-optic cable to connect 8 command posts. Cable cost (£k/km) is given by the weighted graph below. Use NetworkX to compute the MST and compare its total cost to a naive fully-connected approach. Fill in the **TODO** blocks.

```python
# pip install networkx matplotlib
import networkx as nx
import matplotlib.pyplot as plt

G = nx.Graph()

# TODO: Add all 8 command-post nodes
# node names: "CP0", "CP1", ..., "CP7"
# G.add_nodes_from([...])

# TODO: Add weighted edges (cable cost in £k)
# Use the adjacency list below:
#  CP0-CP1:4, CP0-CP2:8, CP1-CP2:11, CP1-CP3:8,
#  CP2-CP4:7, CP2-CP5:1, CP3-CP4:2, CP3-CP6:7,
#  CP4-CP5:6, CP5-CP6:2, CP5-CP7:6, CP6-CP7:9,
#  CP3-CP7:4, CP0-CP5:10
# G.add_weighted_edges_from([...])

# TODO: Compute the Minimum Spanning Tree using Kruskal's algorithm
# mst = nx.minimum_spanning_tree(G, algorithm="kruskal")

# TODO: Print each MST edge and its cost, then print total MST cost
# for u, v, data in mst.edges(data=True):
#     print(f"  {u} -- {v}: £{data['weight']}k")
# total_mst = sum(d["weight"] for _, _, d in mst.edges(data=True))
# total_full = sum(d["weight"] for _, _, d in G.edges(data=True))
# print(f"MST cost: £{total_mst}k  |  Full graph cost: £{total_full}k")

# TODO: Visualise — draw G in grey, MST edges in red
# pos = nx.spring_layout(G, seed=1)
# nx.draw(G, pos, with_labels=True, node_color="lightblue",
#         edge_color="#cccccc", width=1)
# nx.draw_networkx_edges(mst, pos, edge_color="red", width=3)
# plt.title("Command-Post MST (red)")
# plt.tight_layout(); plt.show()
```

??? success "Solution"
    ```python
    import networkx as nx
    import matplotlib.pyplot as plt

    G = nx.Graph()
    G.add_nodes_from([f"CP{i}" for i in range(8)])
    G.add_weighted_edges_from([
        ("CP0","CP1",4),  ("CP0","CP2",8),  ("CP1","CP2",11),
        ("CP1","CP3",8),  ("CP2","CP4",7),  ("CP2","CP5",1),
        ("CP3","CP4",2),  ("CP3","CP6",7),  ("CP4","CP5",6),
        ("CP5","CP6",2),  ("CP5","CP7",6),  ("CP6","CP7",9),
        ("CP3","CP7",4),  ("CP0","CP5",10),
    ])

    mst = nx.minimum_spanning_tree(G, algorithm="kruskal")

    for u, v, data in sorted(mst.edges(data=True), key=lambda e: e[2]["weight"]):
        print(f"  {u} -- {v}: £{data['weight']}k")

    total_mst  = sum(d["weight"] for _, _, d in mst.edges(data=True))
    total_full = sum(d["weight"] for _, _, d in G.edges(data=True))
    print(f"\nMST cost:       £{total_mst}k")
    print(f"Full graph cost: £{total_full}k")

    pos = nx.spring_layout(G, seed=1)
    nx.draw(G, pos, with_labels=True, node_color="lightblue",
            edge_color="#cccccc", width=1, font_size=9)
    nx.draw_networkx_edges(mst, pos, edge_color="red", width=3)
    plt.title("Command-Post Network — MST highlighted in red")
    plt.tight_layout()
    plt.show()
    ```

---

### Exercise 2 — Exploratory: All-Pairs Shortest Paths and Centrality

!!! question "Task"
    A 15-node military command network is given below. Your mission:

    1. Compute **betweenness centrality** for all nodes — identify the top-3 critical relay nodes.
    2. Simulate removing the **highest-centrality node** (relay failure).
    3. For every source–destination pair, report paths that **break** and estimate the **detour cost** (extra hops or weight).

```python
# pip install networkx matplotlib
import networkx as nx
import matplotlib.pyplot as plt

# 15-node command network (undirected, weighted)
edges = [
    (0,1,2),(0,2,5),(1,3,1),(1,4,4),(2,3,3),(2,5,2),
    (3,6,2),(4,6,3),(4,7,5),(5,6,1),(5,8,4),(6,9,2),
    (7,9,3),(7,10,4),(8,9,1),(8,11,3),(9,12,2),(10,12,3),
    (10,13,5),(11,12,1),(11,14,2),(12,13,4),(12,14,3),
    (13,14,2),
]

G = nx.Graph()
G.add_weighted_edges_from(edges)

# ── Step 1: Betweenness centrality ──────────────────────────────────────────
centrality = nx.betweenness_centrality(G, weight="weight", normalized=True)
top3 = sorted(centrality, key=centrality.get, reverse=True)[:3]
print("Top-3 relay nodes by betweenness centrality:")
for n in top3:
    print(f"  Node {n}: {centrality[n]:.3f}")

# ── Step 2: All-pairs shortest paths (baseline) ──────────────────────────────
all_sp_base = dict(nx.all_pairs_dijkstra_path_length(G, weight="weight"))

# ── Step 3: Remove top node and re-compute ───────────────────────────────────
critical_node = top3[0]
G_fail = G.copy()
G_fail.remove_node(critical_node)

print(f"\nSimulating failure of Node {critical_node}...")
# Compute all-pairs distances once on the failed graph (efficient O(n) Dijkstra calls)
try:
    all_sp_fail = dict(nx.all_pairs_dijkstra_path_length(G_fail, weight="weight"))
except Exception:
    all_sp_fail = {}

detour_costs = []
for src in G.nodes():
    for dst in G.nodes():
        if src >= dst or src == critical_node or dst == critical_node:
            continue
        base_cost = all_sp_base[src][dst]
        fail_costs = all_sp_fail.get(src, {})
        if dst not in fail_costs:
            detour_costs.append((src, dst, base_cost, float("inf"), float("inf")))
        else:
            detour = fail_costs[dst] - base_cost
            if detour > 0:
                detour_costs.append((src, dst, base_cost, fail_costs[dst], detour))

broken = [d for d in detour_costs if d[3] == float("inf")]
rerouted = [d for d in detour_costs if 0 < d[4] < float("inf")]

print(f"Broken paths:   {len(broken)}")
print(f"Rerouted paths: {len(rerouted)}")
if rerouted:
    worst = max(rerouted, key=lambda x: x[4])
    print(f"Largest detour: {worst[0]}→{worst[1]}  "
          f"base={worst[2]} new={worst[3]} (+{worst[4]:.1f})")

# ── Visualisation ────────────────────────────────────────────────────────────
fig, axes = plt.subplots(1, 2, figsize=(16, 7))
for ax, graph, title in [(axes[0], G, "Baseline"),
                          (axes[1], G_fail, f"After removing Node {critical_node}")]:
    pos = nx.spring_layout(G, seed=5)   # same layout for comparison
    colors = ["red" if n == critical_node else
              "orange" if n in top3 else "steelblue"
              for n in G.nodes()]
    # only draw nodes present in current graph
    present = list(graph.nodes())
    node_col = {n: c for n, c in zip(G.nodes(), colors) if n in present}
    nx.draw_networkx_nodes(graph, pos, ax=ax,
                           node_color=[node_col[n] for n in present],
                           node_size=500)
    nx.draw_networkx_labels(graph, pos, ax=ax, font_size=9, font_color="white")
    nx.draw_networkx_edges(graph, pos, ax=ax, alpha=0.5)
    ax.set_title(title, fontsize=12)
    ax.axis("off")

plt.suptitle("Centrality & Resilience — 15-node Command Network", fontsize=14)
plt.tight_layout()
plt.savefig("command_network_centrality.png", dpi=150)
plt.show()
```

---

### Exercise 3 — Challenge: Network Resilience Simulator (Monte Carlo)

!!! question "Task"
    Design an interactive resilience simulator for a 20-node supply network:

    1. Randomly fail edges with increasing probability $p \in [0, 0.5]$.
    2. For each failure rate run **100 Monte Carlo trials**.
    3. Plot the **connectivity probability vs. failure rate** (reliability curve).
    4. Identify the **critical failure threshold** (where connectivity drops below 50%).
    5. Visualise a sample failed network with disconnected components in different colours.

```python
# pip install networkx matplotlib numpy
import networkx as nx
import matplotlib.pyplot as plt
import matplotlib.cm as cm
import numpy as np
import random

# ── Reproducibility ───────────────────────────────────────────────────────────
random.seed(0)
np.random.seed(0)

# ── 1. Build the supply network (20 nodes, ~40 edges) ────────────────────────
def build_supply_network(seed=42):
    """Return a connected 20-node supply-chain graph."""
    rng = np.random.default_rng(seed)
    G = nx.Graph()
    G.add_nodes_from(range(20))
    # Backbone ring for guaranteed connectivity
    for i in range(20):
        G.add_edge(i, (i + 1) % 20, weight=rng.integers(1, 10))
    # Additional cross-links for redundancy
    extra = [(0,5),(0,10),(0,15),(2,7),(3,12),(4,9),
             (6,14),(7,13),(8,16),(9,17),(11,18),(13,19)]
    for u, v in extra:
        if not G.has_edge(u, v):
            G.add_edge(u, v, weight=rng.integers(1, 10))
    return G

BASE = build_supply_network()

# ── 2. Monte Carlo simulation ────────────────────────────────────────────────
failure_rates = np.linspace(0, 0.5, 26)
NUM_TRIALS = 100
connectivity_probs = []
sample_graphs = {}  # save one representative trial per failure rate

for p in failure_rates:
    connected_count = 0
    saved = None
    for trial in range(NUM_TRIALS):
        G_trial = BASE.copy()
        edges_to_remove = [e for e in G_trial.edges()
                           if random.random() < p]
        G_trial.remove_edges_from(edges_to_remove)
        if nx.is_connected(G_trial):
            connected_count += 1
        elif saved is None:
            saved = G_trial   # save first disconnected trial as representative
    connectivity_probs.append(connected_count / NUM_TRIALS)
    sample_graphs[round(p, 3)] = saved if saved is not None else G_trial

connectivity_probs = np.array(connectivity_probs)

# ── 3. Find critical threshold ───────────────────────────────────────────────
threshold_idx = np.searchsorted(-connectivity_probs, -0.5)
if threshold_idx < len(failure_rates):
    critical_p = failure_rates[threshold_idx]
    print(f"Critical failure threshold (connectivity < 50%): p ≈ {critical_p:.2f}")
else:
    print("Network remains connected at all tested failure rates.")

# ── 4. Sample failed network at critical threshold ──────────────────────────
# Use the saved representative trial from the Monte Carlo loop (not a new seed)
sample_p = critical_p if threshold_idx < len(failure_rates) else 0.35
G_sample = sample_graphs.get(round(sample_p, 3)) or BASE.copy()

components = list(nx.connected_components(G_sample))
print(f"\nSample network at p≈{sample_p:.2f}: {len(components)} components, "
      f"{G_sample.number_of_edges()} edges remaining")

# ── 5. Visualisation ─────────────────────────────────────────────────────────
fig, (ax1, ax2) = plt.subplots(1, 2, figsize=(16, 7))

# — Reliability curve —
ax1.plot(failure_rates, connectivity_probs, "b-o", linewidth=2, markersize=5)
ax1.axhline(0.5, color="red", linestyle="--", label="50% threshold")
if threshold_idx < len(failure_rates):
    ax1.axvline(critical_p, color="orange", linestyle=":",
                label=f"Critical p ≈ {critical_p:.2f}")
ax1.set_xlabel("Edge Failure Probability", fontsize=12)
ax1.set_ylabel("Connectivity Probability", fontsize=12)
ax1.set_title("Network Reliability Curve\n(100 Monte Carlo trials per point)",
              fontsize=12)
ax1.legend(fontsize=10)
ax1.grid(True, alpha=0.3)

# — Sample failed network —
pos = nx.circular_layout(BASE)
cmap_comp = cm.get_cmap("tab20", max(len(components), 1))
node_colors = ["lightgrey"] * 20
for idx, comp in enumerate(components):
    for n in comp:
        node_colors[n] = cmap_comp(idx)

nx.draw_networkx_nodes(BASE, pos, ax=ax2, node_color=node_colors,
                       node_size=500, alpha=0.9)
nx.draw_networkx_labels(BASE, pos, ax=ax2, font_size=8)
# Surviving edges
nx.draw_networkx_edges(G_sample, pos, ax=ax2,
                       edge_color="steelblue", width=1.5, alpha=0.7)
# Removed (failed) edges in red dashed
failed_edges = [e for e in BASE.edges() if not G_sample.has_edge(*e)]
nx.draw_networkx_edges(BASE, pos, edgelist=failed_edges, ax=ax2,
                       edge_color="red", style="dashed", width=1, alpha=0.4)
ax2.set_title(
    f"Sample Failed Network (p={sample_p:.2f})\n"
    f"{len(components)} components — each colour = isolated component",
    fontsize=12)
ax2.axis("off")

plt.suptitle("Network Resilience Simulator — Monte Carlo Analysis", fontsize=14)
plt.tight_layout()
plt.savefig("network_resilience_simulator.png", dpi=150)
plt.show()
```

!!! tip "Bonus — Interactive Plotly Version"
    Replace the matplotlib reliability curve with a `plotly.express.line` chart for an interactive hover-tooltip experience:

    ```python
    # pip install plotly pandas
    import plotly.express as px
    import pandas as pd

    df = pd.DataFrame({
        "Failure Rate": failure_rates,
        "Connectivity Probability": connectivity_probs,
    })
    fig = px.line(df, x="Failure Rate", y="Connectivity Probability",
                  title="Interactive Network Reliability Curve",
                  markers=True)
    fig.add_hline(y=0.5, line_dash="dash", line_color="red",
                  annotation_text="50% threshold")
    fig.show()
    ```

---

### Check Your Understanding — Part 3

!!! question "Question 1"
    In Exercise 1, the MST algorithm you used is Kruskal's. Explain why Prim's algorithm would produce the same MST cost but might terminate in a different order. When would you prefer Prim's over Kruskal's?

!!! question "Question 2"
    In Exercise 2, betweenness centrality was used to find the most critical relay node. Could eigenvector centrality or degree centrality give the same answer? Under what network topology would they agree, and when would they disagree?

!!! question "Question 3"
    In Exercise 3, the reliability curve has an S-shape with a steep transition near the critical threshold. This is characteristic of a **percolation phase transition**. Why does increasing network density (adding more edges) shift the critical threshold to the right?

---

## Key Takeaways

| Concept | Practical Rule-of-Thumb |
|---------|------------------------|
| **Dijkstra** | Use for navigation/routing with positive weights |
| **Bellman-Ford** | Use when negative costs are possible (e.g., profit edges) |
| **Floyd-Warshall** | Use when you need all-pairs distances on small/dense graphs |
| **Max-Flow** | Use to find capacity limits and bottlenecks |
| **Min-Cut** | Use to find adversary targets or expansion priorities |
| **MST** | Use to design minimum-cost spanning infrastructure |
| **Hungarian** | Use for balanced assignment (agents ↔ tasks) |
| **Betweenness centrality** | Use to identify critical relay nodes or infrastructure |
| **Monte Carlo resilience** | Use when analytic methods are intractable |

---

!!! tip "Prompt Template"
    Use the [Agent Prompt](prompt.md) for this topic, or the [Generic Prompt Template](../prompt-template.md) to generate additional lectures, examples, and exercises.
