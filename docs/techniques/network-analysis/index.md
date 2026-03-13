# Network Analysis

> Modeling flows, routing, and connectivity in networked systems.

---

## Lecture

### What is Network Analysis?

Network Analysis applies graph theory and optimization to problems involving flow, routing, connectivity, and structure in networks. A network consists of **nodes** (vertices) and **edges** (arcs), with associated capacities, costs, or weights.

### Core Problem Types

| Problem | Description |
|---------|-------------|
| **Shortest Path** | Find the minimum-cost route between two nodes |
| **Maximum Flow** | Maximize flow through a network from source to sink |
| **Minimum Cost Flow** | Send a required flow at minimum total cost |
| **Minimum Spanning Tree** | Connect all nodes at minimum total edge cost |
| **Assignment Problem** | Optimally match elements from two sets |
| **Network Design** | Determine which edges to build or upgrade |

### Mathematical Foundation

A network $G = (V, E)$ with flow variables $f_{ij}$ on each arc $(i,j) \in E$:

$$
\begin{align}
\text{Minimize } & \sum_{(i,j) \in E} c_{ij} f_{ij} \\
\text{subject to } & \sum_j f_{ij} - \sum_j f_{ji} = b_i \quad \forall i \in V \\
& 0 \leq f_{ij} \leq u_{ij} \quad \forall (i,j) \in E
\end{align}
$$

Where $b_i$ is the net supply/demand at node $i$, and $u_{ij}$ is the arc capacity.

### Key Concepts

- **Graph representations** — adjacency matrix, adjacency list, edge list
- **Dijkstra's algorithm** — shortest path in non-negative weighted graphs
- **Ford-Fulkerson / Edmonds-Karp** — maximum flow algorithms
- **Network Simplex** — specialized simplex for network flow problems
- **Connectivity and resilience** — cut vertices, bridges, network reliability

### Historical Context

Network flow theory emerged in the 1950s from military logistics problems (RAND Corporation). The max-flow min-cut theorem (Ford & Fulkerson, 1956) is a cornerstone result linking optimization to combinatorics.

### Relationship to Other OR Techniques

- Network flow problems are special cases of **Linear Programming**
- Discrete network decisions use **Integer Programming**
- Stochastic networks connect to **Simulation** and **Stochastic Processes**

---

## Real-World Examples

### Example A — Military: Supply Route Optimization

!!! example "Scenario"
    Find the shortest supply route from a rear logistics base to a forward operating base through a road network where edge weights represent travel time (accounting for terrain and threat).

**Open-Source Tools:** NetworkX, Python

```python
# pip install networkx matplotlib
import networkx as nx
import matplotlib.pyplot as plt

G = nx.DiGraph()
edges = [
    ("Depot", "NodeA", 4), ("Depot", "NodeB", 2),
    ("NodeA", "NodeC", 5), ("NodeA", "NodeD", 10),
    ("NodeB", "NodeA", 1), ("NodeB", "NodeD", 8),
    ("NodeC", "FOB", 3),
    ("NodeD", "NodeC", 2), ("NodeD", "FOB", 6),
]
G.add_weighted_edges_from(edges)

# Find shortest path
path = nx.shortest_path(G, "Depot", "FOB", weight="weight")
cost = nx.shortest_path_length(G, "Depot", "FOB", weight="weight")

print(f"Optimal Route: {' -> '.join(path)}")
print(f"Total Travel Time: {cost} hours")

# Visualize
pos = nx.spring_layout(G, seed=42)
nx.draw(G, pos, with_labels=True, node_color="lightblue", node_size=700)
edge_labels = nx.get_edge_attributes(G, "weight")
nx.draw_networkx_edge_labels(G, pos, edge_labels=edge_labels)
path_edges = list(zip(path, path[1:]))
nx.draw_networkx_edges(G, pos, edgelist=path_edges, edge_color="red", width=3)
plt.title("Supply Route Network")
plt.tight_layout()
plt.savefig("supply_route.png", dpi=150)
plt.show()
```

### Example B — Civilian: Maximum Flow in a Distribution Network

!!! example "Scenario"
    Determine the maximum throughput from a factory to a retail hub through a distribution network with capacity constraints on each link.

**Open-Source Tools:** NetworkX, Python

```python
# pip install networkx
import networkx as nx

G = nx.DiGraph()
edges = [
    ("Factory", "DC1", 15), ("Factory", "DC2", 20),
    ("DC1", "Hub1", 10), ("DC1", "Hub2", 8),
    ("DC2", "Hub1", 12), ("DC2", "Hub2", 15),
    ("Hub1", "Retail", 18), ("Hub2", "Retail", 14),
]
for u, v, cap in edges:
    G.add_edge(u, v, capacity=cap)

flow_value, flow_dict = nx.maximum_flow(G, "Factory", "Retail")
print(f"Maximum Flow: {flow_value} units")
print("\nFlow on each arc:")
for u in flow_dict:
    for v in flow_dict[u]:
        if flow_dict[u][v] > 0:
            print(f"  {u} -> {v}: {flow_dict[u][v]} units")
```

---

## Interactive Exercises

### Exercise 1 — Guided (Warm-Up)

!!! question "Task"
    Build a graph representing a military communications network. Use NetworkX to find the minimum spanning tree — the lowest-cost way to connect all nodes. Fill in the TODO blocks.

```python
import networkx as nx

G = nx.Graph()
# TODO: Add at least 6 nodes and 10 weighted edges
# G.add_weighted_edges_from([...])

# TODO: Compute the minimum spanning tree
# mst = ...

# TODO: Print MST edges and total cost
# for u, v, data in mst.edges(data=True):
#     print(f"  {u} -- {v}: cost {data['weight']}")
```

### Exercise 2 — Exploratory (Intermediate)

!!! question "Task"
    Model a convoy routing problem with multiple origin-destination pairs. Use a minimum cost flow formulation. Explore: What happens if a key bridge (edge) is removed? Quantify the impact on total cost.

### Exercise 3 — Challenge (Advanced)

!!! question "Task"
    Build a network resilience analyzer. Given a transportation network, identify the critical edges (bridges) and nodes (articulation points). Simulate random edge failures and plot how connectivity degrades. Use NetworkX for analysis and matplotlib/plotly for visualization.

---

## Check Your Understanding

1. What does the max-flow min-cut theorem state, and why is it important?
2. When is Dijkstra's algorithm insufficient? What alternatives exist?
3. How does network structure affect the solvability of flow problems?

---

!!! tip "Prompt Template"
    Use the [Agent Prompt](prompt.md) for this topic, or the [Generic Prompt Template](../prompt-template.md) to generate additional lectures, examples, and exercises.
