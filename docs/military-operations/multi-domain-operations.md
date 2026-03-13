# Multi-Domain Operations

## Introduction

**Multi-Domain Operations (MDO)** is the Army's operational concept for how joint forces defeat a peer adversary that employs layered anti-access/area denial (A2/AD) capabilities across all domains simultaneously. At the joint level, the analogous concept is **Joint All-Domain Operations (JADO)**, enabled by **Joint All-Domain Command and Control (JADC2)**. These concepts reflect the reality that modern adversaries have made all domains — land, sea, air, space, cyberspace, and the electromagnetic spectrum (EMS) — simultaneously contested.

The core idea: **Convergence** — rapidly and continuously combining capabilities from multiple domains to overwhelm an adversary with simultaneous dilemmas they cannot solve in time.

---

## Foundational Concepts

### The Multi-Domain Problem

Modern peer adversaries (e.g., China, Russia) have developed layered systems that deny U.S. freedom of action within each domain:

- **Land**: Long-range precision fires, area denial
- **Maritime**: Anti-ship missiles, submarines, mines
- **Air**: Integrated air defense systems (IADS), long-range SAMs
- **Space**: Anti-satellite weapons, GPS jamming
- **Cyber**: Offensive cyber operations, network intrusion
- **EMS**: Electronic warfare, jamming, spoofing

No single domain can overcome these layered defenses. MDO/JADO requires simultaneous pressure across all layers.

### Convergence

> "Convergence is the rapid and continuous integration of capabilities in all domains, the electromagnetic spectrum, and the information environment that presents multiple, simultaneous dilemmas to an adversary."
> — *TRADOC Pamphlet 525-3-1, The U.S. Army in Multi-Domain Operations 2028*

!!! note "OR Lens — Convergence"
    Convergence planning is fundamentally a **synchronization and scheduling** problem. OR methods include **multi-objective optimization** to balance effects across domains, **game theory** to model adversary responses, and **stochastic simulation** to evaluate convergence timing under uncertainty.

---

## Multi-Domain Operation Types

### Penetrate, Dis-Integrate, and Exploit

The MDO framework describes three sequential operational actions against A2/AD systems:

| Operational Action | Description |
|--------------------|-------------|
| **Penetrate** | Create windows of superiority within the adversary's A2/AD system by employing cross-domain fires, cyber attacks, EW, and space effects to degrade key nodes |
| **Dis-integrate** | Separate adversary systems (command and control, fires, maneuver forces) from each other so they cannot mutually support, and destroy them in detail |
| **Exploit** | Exploit the penetration and dis-integration to achieve strategic objectives; advance land forces, project power, and prevent adversary reconstitution |

---

### Joint All-Domain Command and Control (JADC2) Operations

JADC2 is the operational framework for connecting sensors from all services into a single network and enabling commanders to direct effects from any domain against any target.

| Operation Type | Description |
|----------------|-------------|
| **Cross-Domain Fires** | Employ fires capabilities from one domain to produce effects in another (e.g., cyber to enable air strike; naval fires to support ground maneuver) |
| **Joint Targeting** | Collaboratively develop, validate, and execute target lists that integrate air, land, maritime, space, and cyber effects against a common set of targets |
| **All-Domain ISR Fusion** | Integrate ISR feeds from space-based sensors, airborne platforms, ground sensors, and cyber sources into a common operating picture |
| **Effects Synchronization** | Coordinate the timing and sequencing of kinetic and non-kinetic effects across domains to achieve convergence at the decisive point |

!!! note "OR Lens — JADC2"
    Joint targeting is a **multi-layer assignment problem** — matching effects from multiple domains to targets to achieve campaign objectives. ISR fusion uses **data fusion**, **Kalman filtering**, and **Bayesian tracking**. Effects synchronization is a **multi-resource scheduling** problem.

---

### Integrated Air and Missile Defense (IAMD)

IAMD provides joint force protection against ballistic missile, cruise missile, air-breathing, and hypersonic threats through layered interception capabilities.

| Operation Type | Description |
|----------------|-------------|
| **Ballistic Missile Defense (BMD)** | Intercept and destroy ballistic missiles using Patriot, THAAD, Aegis, and ground-based midcourse interceptors |
| **Cruise Missile Defense** | Detect and engage low-flying cruise missiles using radar networks, fighter aircraft, and short-range interceptors |
| **Counter-Rocket, Artillery, and Mortar (C-RAM)** | Protect fixed sites and forward operating bases from indirect fire using automated gun systems (Phalanx, CIWS) and radar cueing |
| **Counter-Hypersonic Defense** | Develop and field capabilities to track and intercept hypersonic glide vehicles — an emerging operational challenge |
| **Airspace Management** | Coordinate the use of airspace to prevent fratricide, deconflict fires, and maximize freedom of movement for friendly forces |

!!! note "OR Lens — IAMD"
    Missile defense is a foundational OR problem. **Optimal assignment** of interceptors to targets, **weapon-to-target assignment (WTA)**, and **shoot-look-shoot** vs. **shoot-shoot-look** strategies are all classical operations research problems addressed with integer programming, dynamic programming, and stochastic models.

---

### Joint Fires

Joint fires are fires delivered by joint force components to produce effects against a target in support of the joint force commander's objectives.

| Operation Type | Description |
|----------------|-------------|
| **Deep Fires** | Long-range fires (ATACMS, PrSM, HIMARS, naval strike missiles, air-launched weapons) against enemy forces, C2 nodes, and logistics in the adversary's deep area |
| **Close Fires** | Artillery, mortars, and close air support coordinated to support engaged maneuver forces |
| **Counter-Battery Fires** | Detect and engage adversary artillery and rocket systems before they can displace after firing |
| **Multi-Domain Strike** | Coordinate and sequence strikes from land, sea, air, space, and cyber to overwhelm adversary defenses and achieve convergent effects |
| **Electronic Warfare (EW) Integration** | Use jamming, spoofing, and directed energy to complement kinetic fires, defeating adversary sensors and communication links |

---

### Joint Logistics Over the Shore (JLOTS) and Sustainment

Multi-domain sustainment provides the logistics backbone for sustained joint operations.

| Operation Type | Description |
|----------------|-------------|
| **Joint Logistics Over the Shore (JLOTS)** | Transfer cargo from sea to shore without fixed port infrastructure using lighterage, causeways, and naval support |
| **Air Line of Communications (ALOC)** | Sustain forward forces via air delivery when surface lines of communication are threatened or severed |
| **Contested Logistics** | Maintain logistical flow under adversary interdiction using dispersion, deception, and alternate supply routes |
| **Forward Area Refueling and Rearming Points (FARPs)** | Rapidly refuel and rearm rotary-wing and tiltrotor aircraft in forward areas to extend operational reach |

!!! note "OR Lens — Joint Logistics"
    JLOTS throughput and beach logistics are classic **throughput** and **queuing** problems. Contested logistics under interdiction uses **robust optimization** and **stochastic network flow** models. ALOC scheduling is a **vehicle routing** problem under capacity constraints.

---

### Information Operations (IO) and Multi-Domain Influence

| Operation Type | Description |
|----------------|-------------|
| **Military Information Support Operations (MISO)** | Influence adversary decision-makers and populations through tailored messages delivered via multiple channels |
| **Deception Operations (MILDEC)** | Mislead adversary commanders through false indicators and the manipulation of information, including through cyber and electronic means |
| **Operations Security (OPSEC)** | Protect friendly indicators and signatures to prevent adversary intelligence collection that could compromise operational plans |
| **Electromagnetic Spectrum Operations (EMSO)** | Integrate electronic warfare, spectrum management, and cyberspace operations to achieve EMS superiority |

---

## Summary

| Operation Type | Domain Integration | Key OR Methods |
|----------------|-------------------|----------------|
| Penetrate A2/AD | All domains | Network flow, combinatorial optimization |
| JADC2 / Cross-Domain Fires | All domains | Assignment, scheduling |
| Joint Targeting | Air, Land, Sea, Space, Cyber | Multi-attribute targeting, MIP |
| Integrated Air/Missile Defense | Air, Land, Sea, Space | WTA, dynamic programming |
| Deep Fires / Multi-Domain Strike | Land, Sea, Air | Routing, target scheduling |
| Joint Logistics (JLOTS, ALOC) | Land, Sea, Air | Queueing, network flow, VRP |
| Information Operations / MISO | Cyber, Information | Decision analysis, influence modeling |
| EMSO / Electronic Warfare | EMS, Cyber, Air | Signal optimization, frequency assignment |
