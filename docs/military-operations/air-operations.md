# Air Operations

## Introduction

Air forces operate across the full aerospace domain — from the surface to the edge of space — to achieve **air superiority**, **strike**, **strategic attack**, **airlift**, **ISR**, and **close support** missions. U.S. Air Force doctrine is codified in **AFPD 3-0 (Operations and Force Employment)** and joint doctrine in **JP 3-30 (Command and Control of Joint Air Operations)**. The Joint Forces Air Component Commander (JFACC) and Combined Air Operations Center (CAOC) coordinate and task air forces via the **Air Tasking Order (ATO)**.

---

## Air Superiority Operations

Air superiority is the degree of dominance in the air battle that permits the conduct of friendly land, sea, space, and air operations without prohibitive interference from air and missile threats.

| Operation Type | Description |
|----------------|-------------|
| **Offensive Counter-Air (OCA)** | Attack enemy air forces, airfields, air defense systems, and production facilities to destroy adversary air power at its source |
| **Defensive Counter-Air (DCA)** | Protect friendly forces and assets from air attack through combat air patrols (CAP), intercepts, and airspace management |
| **Suppression of Enemy Air Defenses (SEAD)** | Degrade, neutralize, or destroy enemy surface-based air defenses to enable freedom of action for friendly air operations |
| **Destruction of Enemy Air Defenses (DEAD)** | Permanently destroy (rather than temporarily suppress) integrated air defense system (IADS) components |

!!! note "OR Lens — Air Superiority"
    OCA strike planning is a **target assignment problem** (integer programming). SEAD sequencing must account for radar re-activation and can be modeled as a **dynamic programming** problem. CAP positioning optimizes coverage against threat corridors using geometric and probabilistic models.

---

## Strike Operations

Strike operations are attacks against fixed or mobile targets to destroy, degrade, or disrupt adversary capabilities.

| Operation Type | Description |
|----------------|-------------|
| **Strategic Attack** | Direct application of air power against the adversary's leadership, war-making capacity, or will; targeting critical nodes rather than fielded military forces |
| **Air Interdiction (AI)** | Attack targets in the adversary rear (logistics, reserves, infrastructure) to delay, disrupt, or destroy before they can be employed against friendly forces |
| **Close Air Support (CAS)** | Air action against hostile targets near friendly forces; requires detailed integration with ground forces and careful deconfliction |
| **Air Strikes / Precision Strike** | Employment of precision-guided munitions (PGMs) against high-value targets with minimal collateral damage |
| **Time-Sensitive Targeting (TST)** | Strike planning cycle executed rapidly against fleeting, high-payoff targets that require near-real-time sensor-to-shooter coordination |

!!! note "OR Lens — Strike"
    AI mission planning is a **routing** and **targeting** optimization problem. CAS deconfliction involves real-time scheduling under dynamic constraints. TST is amenable to **queueing** and **optimal stopping** models. Collateral damage estimation uses probabilistic blast and fragmentation models.

---

## Airlift and Air Mobility Operations

Airlift provides the joint force with strategic and intra-theater mobility to deploy, sustain, and redeploy forces and materiel.

| Operation Type | Description |
|----------------|-------------|
| **Strategic Airlift** | Move forces and materiel over intercontinental distances (C-17, C-5 aircraft) |
| **Intra-Theater Airlift** | Move forces and supplies within a theater of operations |
| **Airdrop and Air-Land Operations** | Deliver personnel, equipment, or supplies by parachute or landing in austere locations |
| **Aeromedical Evacuation (AE)** | Move patients from forward areas to definitive care facilities using dedicated medical transport aircraft |
| **Air Refueling** | Extend the range, endurance, and payload of receiver aircraft using tanker aircraft (KC-135, KC-46) |

!!! note "OR Lens — Airlift"
    Strategic airlift scheduling is a classic **vehicle routing and scheduling** problem. Air refueling track placement and tanker allocation is amenable to **assignment** and **network flow** models. Aeromedical evacuation prioritization uses **multi-criteria decision analysis**.

---

## Intelligence, Surveillance, and Reconnaissance (ISR) Operations

ISR operations collect and process information to support decision-making and targeting.

| Operation Type | Description |
|----------------|-------------|
| **Airborne ISR** | Collect imagery, signals, and other intelligence using manned and unmanned aircraft (U-2, RQ-4 Global Hawk, MQ-9) |
| **Maritime Patrol and Reconnaissance** | Conduct ocean surveillance and anti-submarine reconnaissance from aircraft (P-8 Poseidon) |
| **Battle Damage Assessment (BDA)** | Post-strike reconnaissance to assess effectiveness of fires against target sets |

!!! note "OR Lens — ISR"
    ISR asset scheduling and routing is a **multi-mission planning** problem with sensor coverage objectives. Search area prioritization uses **Bayesian search theory**. BDA interpretation involves statistical inference models.

---

## Special Operations Aviation

| Operation Type | Description |
|----------------|-------------|
| **Direct Action Air Support** | Provide precision air support to special operations forces (SOF) conducting raids and direct action missions |
| **Personnel Recovery (PR) / Combat Search and Rescue (CSAR)** | Recover isolated personnel (downed aircrew, special operators) from denied or contested environments |
| **Psychological Operations (PSYOP) Support** | Employ aircraft for leaflet drops, loudspeaker broadcasts, or radio transmission in support of information operations |

---

## Humanitarian and Disaster Relief Airlift

| Operation Type | Description |
|----------------|-------------|
| **Humanitarian Airlift** | Deliver food, water, medicine, and supplies to populations affected by natural disaster or conflict |
| **Airdrop to Encircled Populations** | Supply isolated civilian or military populations unable to be reached by ground or sea |

---

## Air Operations Summary

| Operation Type | Primary Objective | Key Platforms |
|----------------|-------------------|---------------|
| OCA | Destroy enemy air power at source | F-22, F-35, B-2, B-52 |
| DCA / CAP | Protect friendly forces from air attack | F-22, F-35, F-15 |
| SEAD / DEAD | Defeat enemy air defenses | F-16CJ (Wild Weasel), EA-18G, HARM |
| Strategic Attack | Destroy adversary war-making capacity | B-2, B-21, cruise missiles |
| Air Interdiction | Disrupt adversary rear area | F-15E, F-35, A-10 |
| Close Air Support | Support ground forces in contact | A-10, F-35, rotary wing |
| Strategic Airlift | Intercontinental force projection | C-17, C-5 |
| Intra-Theater Airlift | In-theater logistics | C-130, C-17 |
| Air Refueling | Extend range/endurance | KC-135, KC-46 |
| Airborne ISR | Intelligence collection | U-2, RQ-4, MQ-9 |
| CSAR | Recover isolated personnel | HC-130, HH-60 |
| Humanitarian Airlift | Deliver relief supplies | C-17, C-130 |
