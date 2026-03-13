# Military Operations — Overview

## Introduction

Military operations are actions conducted by armed forces to achieve strategic, operational, or tactical objectives. Force commanders — those responsible for employing military forces to produce operational outcomes — organize these activities into recognized **operation types** that provide a common language for planning, tasking, and assessment.

This overview introduces the taxonomy of military operation types across all warfighting domains: Army, Navy, Air Force, Space, Cyber, and Multi-Domain.

## The Joint Operations Framework

Joint military doctrine (e.g., U.S. Joint Publication JP 3-0, *Joint Operations*) organizes military activities along a spectrum from competition short of conflict through large-scale combat operations (LSCO) and post-conflict stabilization. At each level, commanders employ forces to achieve **operational outcomes** — measurable effects that contribute to campaign objectives.

```
Strategic Objectives
        │
        ▼
  Operational Outcomes  ← Force Commanders task these
        │
        ▼
  Tactical Tasks / Mission Types
```

## Common Concepts Across Domains

### Phases of a Military Campaign

| Phase | Description |
|-------|-------------|
| **Shape** | Set conditions for future operations; deter adversaries |
| **Deter** | Demonstrate capability and will to discourage adversary action |
| **Seize Initiative** | Gain and maintain the initiative; transition to offensive operations |
| **Dominate** | Defeat/destroy adversary forces; achieve operational objectives |
| **Stabilize** | Establish security and governance conditions |
| **Enable Civil Authority** | Transition to civilian or partner control |

### Operation Types vs. Mission Types

- **Operation Types** (this BoK): High-level, force-level activities that commanders task to achieve campaign objectives. Examples: *Submarine Barrier Operations*, *Humanitarian Assistance/Disaster Relief*, *Air Superiority Operations*.
- **Mission Types**: More granular tasks assigned to specific units. Examples: *Conduct a Strike*, *Establish a Combat Outpost*, *Execute a Combat Air Patrol*.

### Key Planning Constructs

| Construct | Description |
|-----------|-------------|
| **Commander's Intent** | The desired end state and purpose of an operation |
| **Lines of Effort (LOEs)** | Conceptual groupings of tasks linked to achieving conditions |
| **Main and Supporting Efforts** | Concentration of mass and supporting activities |
| **Branch Plans** | Contingency plans for anticipated events |
| **Sequel Plans** | Follow-on operations after current operations conclude |

## Domain Overview

### Army (Land Domain)
Land forces conduct the full spectrum of military operations including offensive, defensive, stability, and civil support missions. Large-scale combat operations represent the most demanding context. See [Army Operations](army-operations.md).

### Navy (Maritime Domain)
Naval forces operate in the surface, subsurface, and littoral environments to achieve sea control, power projection, and deterrence. See [Navy Operations](navy-operations.md).

### Air (Aerospace Domain)
Air forces conduct operations across the air-to-ground and air-to-air spectrum, including strike, interdiction, airlift, and ISR missions. See [Air Operations](air-operations.md).

### Space (Space Domain)
Space forces provide enabling capabilities (PNT, communications, ISR) and conduct offensive and defensive operations in the orbital environment. See [Space Operations](space-operations.md).

### Cyber (Cyberspace Domain)
Cyber forces defend networks, exploit adversary systems, and support joint operations through offensive and defensive cyberspace operations. See [Cyber Operations](cyber-operations.md).

### Multi-Domain (Cross-Domain Integration)
Joint force commanders integrate capabilities across all domains to achieve convergence effects that no single domain can produce alone. See [Multi-Domain Operations](multi-domain-operations.md).

## OR Applications at the Operational Level

Operations research at the force commander level typically addresses:

!!! example "Representative OR Problems"
    - **Force allocation**: How many submarines are needed to achieve a given detection probability in a barrier operation?
    - **Logistics optimization**: How should ammunition and fuel be pre-positioned to sustain a maneuver campaign?
    - **Targeting sequencing**: In what order should targets be struck to maximize disruption to enemy command and control?
    - **Risk assessment**: What is the probability of mission success given force attrition and environmental uncertainty?
    - **Scheduling**: How should air tasking orders be built to satisfy competing fires, ISR, and airlift demands?

These problems span linear programming, network flows, stochastic simulation, search theory, and game-theoretic models — all covered in depth in this BoK.
