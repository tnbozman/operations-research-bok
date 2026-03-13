# Cyber Operations

## Introduction

Cyberspace operations (CO) are the employment of cyberspace capabilities to achieve objectives in or through cyberspace. U.S. cyber doctrine is codified in **JP 3-12 (Cyberspace Operations)** and **USCYBERCOM** doctrine publications. The Commander, U.S. Cyber Command (USCYBERCOM) is responsible for directing, synchronizing, and coordinating cyberspace operations and the defense of Department of Defense Information Networks (DODIN).

Cyber operations are organized into three broad categories: **DODIN Operations**, **Defensive Cyber Operations (DCO)**, and **Offensive Cyber Operations (OCO)**.

---

## Department of Defense Information Network (DODIN) Operations

DODIN operations are actions taken to design, build, configure, secure, operate, maintain, and sustain DoD communications systems and networks across the enterprise.

| Operation Type | Description |
|----------------|-------------|
| **Network Operations (NetOps)** | Day-to-day management of DODIN infrastructure including configuration management, performance monitoring, and fault management |
| **Information Assurance / Cybersecurity** | Implement technical controls, patches, and security configurations to protect DODIN from exploitation and attack |
| **Identity and Access Management** | Control who can access DODIN systems and data; enforce authentication, authorization, and audit controls |
| **Spectrum Management** | Manage the electromagnetic spectrum required to operate military communication systems, avoiding interference with friendly and civilian systems |
| **Software Defined Networking** | Employ dynamic network configuration to isolate compromised segments, route traffic securely, and optimize data flows |

!!! note "OR Lens — DODIN Operations"
    Network optimization (minimum-cost flow, shortest path) underpins routing and bandwidth allocation. Patch scheduling given dependency constraints is a **project scheduling** problem. Access control policy optimization uses **combinatorial search** and **graph theory**.

---

## Defensive Cyber Operations (DCO)

DCO are passive and active measures taken to preserve the ability to use friendly cyberspace capabilities and protect data, networks, net-centric capabilities, and other designated systems.

### DCO — Internal Defensive Measures (DCO-IDM)

These are largely automated and analyst-driven activities conducted within DODIN boundaries.

| Operation Type | Description |
|----------------|-------------|
| **Cyber Threat Intelligence (CTI) Operations** | Collect, analyze, and disseminate intelligence on adversary cyber capabilities, intentions, and tactics to inform defensive posture |
| **Hunt Operations** | Proactively search DODIN for indicators of compromise (IOCs), adversary persistence, and malicious activity that evades automated detection |
| **Incident Response (IR)** | Detect, contain, eradicate, and recover from cyber incidents; restore affected systems and services to normal operation |
| **Vulnerability Assessment and Red Team Operations** | Identify and assess vulnerabilities in DODIN systems through penetration testing, red team exercises, and automated scanning |

!!! note "OR Lens — DCO-IDM"
    Threat hunting is a **search theory** problem under uncertainty. Incident response prioritization uses **multi-criteria decision analysis** and **queuing** models when multiple incidents occur simultaneously. Patch/vulnerability prioritization is a **risk-adjusted scheduling** problem.

### DCO — Response Actions (DCO-RA)

These are actions taken outside DODIN boundaries (but within U.S.-controlled space) to stop or defeat adversary cyber attacks.

| Operation Type | Description |
|----------------|-------------|
| **Active Defense** | Employ cyber capabilities to detect, respond to, and defeat adversary intrusions that have crossed DODIN boundaries; may include sinkholing, beacon manipulation, or controlled deception |
| **Deception Operations (Cyber Deception)** | Deploy honeypots, honey credentials, and deceptive network architectures to attract, trap, and study adversary operators |
| **Hack Back / Counter-Intrusion** | Authorized actions taken against adversary infrastructure to stop an ongoing attack (requires senior leadership authorization) |

---

## Offensive Cyber Operations (OCO)

OCO are operations intended to project power by the application of force in and through cyberspace. OCO require Presidential or senior-level authorization and are coordinated with intelligence community activities.

| Operation Type | Description |
|----------------|-------------|
| **Cyberspace Attack** | Create denial effects (disruption, degradation, or destruction) against adversary systems and networks to prevent or impair their use |
| **Cyberspace Exploitation** | Gain persistent, covert access to adversary networks to collect intelligence, support targeting, or enable future effects |
| **Electronic Warfare Integration** | Combine cyber and electronic warfare effects to defeat adversary communications and sensors (e.g., disrupt radar networks, interfere with command and control) |
| **Effects Against Critical Infrastructure** | Degrade adversary critical infrastructure (power grids, financial systems, transportation networks) to reduce their war-making capacity |
| **Enabling Effects for Kinetic Operations** | Use cyber means to blind adversary air defense radars, disable command and control, or corrupt weapon system guidance prior to a kinetic strike |

!!! note "OR Lens — OCO"
    Cyber campaign planning is a **multi-stage attack graph** problem. Optimal sequencing of exploits and effects uses **dynamic programming** and **Markov decision processes**. Attribution analysis uses **Bayesian inference** and statistical fingerprinting.

---

## Cyber Intelligence, Surveillance, and Reconnaissance (Cyber ISR)

| Operation Type | Description |
|----------------|-------------|
| **Cyber Reconnaissance** | Map adversary network infrastructure, identify high-value targets, and collect technical intelligence to inform OCO planning |
| **Persistent Access Operations** | Establish and maintain covert footholds in adversary networks for long-term intelligence collection and pre-positioned effects |
| **Technical Intelligence Collection** | Collect malware samples, implants, and adversary tools to enable attribution, defensive signature development, and counter-tool analysis |

---

## Cyber Support to Joint Operations

Cyber forces provide direct support to joint force commanders during all phases of an operation.

| Operation Type | Description |
|----------------|-------------|
| **Cyber Support to Fires** | Provide cyber effects to support targeting, including disabling adversary air defense and communication systems before kinetic strikes |
| **Cyber Support to Maneuver** | Disrupt adversary command and control and logistics networks to facilitate ground maneuver |
| **Cyber Support to Influence Operations** | Degrade adversary propaganda and information systems; support information operations objectives in the information environment |

---

## Summary

| Operation Type | Category | Primary Objective |
|----------------|----------|-------------------|
| Network Operations | DODIN | Maintain network availability |
| Cybersecurity | DODIN | Protect DODIN from exploitation |
| Threat Intelligence | DCO-IDM | Inform defensive posture |
| Hunt Operations | DCO-IDM | Find adversary presence |
| Incident Response | DCO-IDM | Contain and eradicate threats |
| Active Defense | DCO-RA | Stop adversary intrusion |
| Cyber Deception | DCO-RA | Trap and study adversaries |
| Cyberspace Attack | OCO | Disrupt/destroy adversary systems |
| Cyberspace Exploitation | OCO | Collect intelligence |
| Enabling Effects | OCO | Support kinetic operations |
| Cyber Reconnaissance | Cyber ISR | Map adversary networks |
| Persistent Access | Cyber ISR | Long-term collection/positioning |
