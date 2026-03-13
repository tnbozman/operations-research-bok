# Space Operations

## Introduction

Space operations support the joint force by providing **critical enabling capabilities** — positioning, navigation and timing (PNT), satellite communications, missile warning, weather, and intelligence — while also conducting offensive and defensive operations in the space domain. U.S. Space Force doctrine is codified in **USSF Doctrine Publication 3-0 (Space Operations)** and joint doctrine in **JP 3-14 (Space Operations)**. The Commander, U.S. Space Command (SPACECOM) integrates space capabilities across the joint force.

Space operations are unique in that many are **dual-use** — they simultaneously enable joint force capabilities and support civilian society. Space domain operations are divided into **military space operations** and **space domain awareness (SDA)** activities.

---

## Space Domain Awareness (SDA) Operations

Space Domain Awareness is the effective identification, characterization, and understanding of any factor — active or passive — associated with the space domain.

| Operation Type | Description |
|----------------|-------------|
| **Space Surveillance and Tracking** | Continuously track all artificial objects in Earth orbit using ground- and space-based sensors to maintain a catalog of space objects |
| **Space Object Identification (SOI)** | Characterize the function, capability, and status of objects in orbit to distinguish operational satellites from debris |
| **Threat Warning and Assessment** | Detect, characterize, and attribute hostile actions against space systems; provide tactical warning of attacks on satellites |
| **Electromagnetic Environment Awareness** | Monitor the radio frequency (RF) environment to detect jamming, interference, and spoofing of satellite signals |

!!! note "OR Lens — SDA"
    Sensor tasking and scheduling for space surveillance is a **combinatorial optimization** problem — maximizing coverage of high-priority objects subject to sensor availability and field-of-view constraints. Conjunction analysis (collision avoidance) uses probabilistic orbital mechanics models.

---

## Positioning, Navigation, and Timing (PNT) Operations

PNT is one of the most operationally critical space-enabled capabilities, underpinning precision weapons, logistics, and command and control.

| Operation Type | Description |
|----------------|-------------|
| **GPS Constellation Management** | Maintain the GPS satellite constellation to ensure continuous, accurate global coverage; manage satellite health and orbital maintenance |
| **PNT Assured Operations** | Protect PNT services from jamming, spoofing, and denial; employ alternative PNT methods when GPS is degraded |
| **Anti-Jam and GPS Resilience Operations** | Employ directional antennas, signal processing, and alternative sources (GLONASS, Galileo, inertial navigation) to maintain PNT under threat |

!!! note "OR Lens — PNT"
    GPS constellation coverage analysis uses **geometric optimization** and **simulation** to evaluate coverage gaps as a function of orbital parameters and satellite outages. Resilient PNT under jamming is a **robust optimization** problem.

---

## Satellite Communications (SATCOM) Operations

SATCOM provides the backbone for beyond-line-of-sight command and control, intelligence dissemination, and logistics coordination.

| Operation Type | Description |
|----------------|-------------|
| **Protected SATCOM Operations** | Operate jam-resistant, nuclear-hardened satellite communications (Advanced EHF, AEHF) for strategic command and control |
| **Wideband SATCOM Operations** | Provide high-bandwidth communications for ISR data distribution, video teleconferencing, and broadband connectivity (WGS) |
| **Commercial SATCOM Integration** | Augment military SATCOM capacity with leased commercial satellite bandwidth during high-demand periods |
| **SATCOM Frequency Management and Deconfliction** | Manage orbital slots and frequency assignments to avoid interference between allied, neutral, and commercial systems |

!!! note "OR Lens — SATCOM"
    SATCOM bandwidth allocation across competing users is a **resource allocation** optimization problem. Link budget analysis and interference modeling use statistical signal processing models.

---

## Missile Warning Operations

| Operation Type | Description |
|----------------|-------------|
| **Ballistic Missile Launch Detection** | Detect ballistic missile launches using infrared sensors aboard dedicated satellites (SBIRS, NGG) and correlate with ground-based radar to characterize trajectories |
| **Hypersonic Threat Tracking** | Track hypersonic glide vehicles and maneuvering reentry vehicles through the sustained observation phase |
| **Nuclear Detonation Detection (NUDET)** | Detect and characterize nuclear detonations globally using satellite-based sensors |

---

## Intelligence, Surveillance, and Reconnaissance (ISR) from Space

| Operation Type | Description |
|----------------|-------------|
| **Imagery Intelligence (IMINT) Collection** | Task and operate reconnaissance satellites to collect imagery of adversary facilities, force dispositions, and activities |
| **Signals Intelligence (SIGINT) Collection** | Intercept and process adversary communications and electronic emissions from orbit |
| **Space-Based Infrared / Overhead Persistent Infrared (OPIR)** | Provide persistent infrared surveillance for missile warning, target detection, and environmental monitoring |

---

## Space Control Operations

Space control ensures freedom of action in space for the U.S. and its allies while denying an adversary freedom of action.

| Operation Type | Description |
|----------------|-------------|
| **Defensive Space Control (DSC)** | Protect space systems from hostile interference through jamming mitigation, satellite maneuvering, and cyber defense of ground systems |
| **Offensive Space Control (OSC)** | Degrade, deny, disrupt, or destroy adversary space capabilities through reversible and non-reversible means |
| **Electronic Warfare Against Space Systems** | Jam or spoof adversary satellite uplinks, downlinks, or navigation signals |
| **Orbital Warfare** | Employ space vehicles to maneuver in proximity to adversary satellites for reconnaissance, interference, or physical engagement |

!!! note "OR Lens — Space Control"
    Defensive maneuver planning for satellite avoidance uses **optimal control** and **trajectory optimization**. Offensive targeting of adversary space assets involves **kill chain analysis** and **effects-based operations** modeling.

---

## Space Lift Operations

| Operation Type | Description |
|----------------|-------------|
| **Launch and Early Orbit Operations** | Support the launch, tracking, and early-orbit checkout of military satellites |
| **Reconstitution / Resilient Architecture** | Rapidly replace damaged or destroyed satellites using pre-positioned spares, commercial augmentation, or responsive launch capabilities |

---

## Summary

| Operation Type | Primary Objective | Key Systems |
|----------------|-------------------|-------------|
| Space Surveillance and Tracking | Maintain space object catalog | SSN, Space Fence, Telescopes |
| GPS Constellation Management | Provide global PNT | GPS (Block III) satellites |
| Protected SATCOM | Strategic C2 communications | AEHF satellites |
| Wideband SATCOM | Broadband data distribution | WGS satellites |
| Ballistic Missile Launch Detection | Missile warning | SBIRS/NGG satellites |
| IMINT Collection | Overhead imagery | NRO assets, commercial GEOINT |
| Defensive Space Control | Protect space systems | Jamming mitigation, cyber defense |
| Offensive Space Control | Deny adversary space use | ASAT weapons, cyber, EW |
| Orbital Warfare | On-orbit engagement | Maneuvering satellites |
| Space Lift / Reconstitution | Restore space capabilities | Falcon 9/Heavy, Vulcan, ULA |
