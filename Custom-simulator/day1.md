# AERL-P1 Custom Simulator Technical Blueprint v1
## Week 1 - Day 1 (Final)

**Project:** AERL - Adaptive Environment Representation Layer  
**Problem:** P1 Urban Mobility Environment Representation  
**Status:** Day 1 Approved (Post-Mentor Review Corrections Applied)  
**Stage:** Engineering Foundation

---

# 1. Purpose

The Custom Simulator exists to provide a mathematically auditable environment for evaluating:

- Predictive Sufficiency (H1)
- Transferability (H2)
- Robustness (H3)
- Adaptation Efficiency (H4)
- Representation Advantage (H5)

The simulator is not intended to replace SUMO.

It is intended to:

- Verify AERL assumptions
- Support controlled experiments
- Enable hidden-state evaluation
- Provide reproducible trajectories
- Support transfer-learning studies

---

# 2. Simulator Philosophy

The development strategy follows:

```text
Custom Simulator
        ↓
AERL Validation
        ↓
SUMO Validation
```

Priority order:

```text
1. Correctness
2. Auditability
3. Interpretability
4. Realism
```

---

# 3. Initial Scale

Version 1:

```text
Intersections      : 10
Directed Roads     : 30
Demand Zones       : 5
Bus Routes         : 2
OD Pairs           : 20 – 50
```

This scale is intentionally small enough to debug while still allowing:

- Congestion
- Bottlenecks
- Route changes
- Incidents
- Observation failures
- Transfer experiments

---

# 4. Traffic Model

We adopt a:

```text
Mesoscopic Fluid Network Model
```

rather than a microscopic vehicle simulator.

Version 1 does NOT model:

- Lane changing
- Car-following
- Driver psychology
- Pedestrians
- Cyclists
- Parking systems

---

# 5. Simulation Clock

## Global Time Step

\[
\Delta t = 10s
\]

All state transitions occur every 10 seconds.

---

## State Convention

\[
s_t
\]

represents the state at the beginning of interval \(t\).

---

## Control Convention

At each interval:

```text
Observe o_t
        ↓
Update Belief
        ↓
Compute Action a_t
        ↓
Environment Evolves
        ↓
Receive Reward r_t
```

The action affects interval:

\[
[t,t+1)
\]

---

# 6. Units

| Quantity | Symbol | Unit |
|-----------|----------|--------|
| Vehicle Count | \(n_e\) | vehicles |
| Junction Storage | \(J_i\) | vehicles |
| Queue Length | \(Q_i\) | vehicles |
| Capacity | \(c_e\) | vehicles/hour |
| Flow Rate | \(r_e\) | vehicles/hour |
| Interval Flow | \(F_e\) | vehicles/step |
| Speed | \(v_e\) | km/hour |
| Travel Time | \(tt_e\) | seconds |
| Demand Arrival | \(\lambda_{od}\) | trips/step |

---

## Rate Conversion

For any flow rate:

\[
r_{e,t}
\]

the interval flow is:

\[
F_{e,t}
=
r_{e,t}
\frac{\Delta t}{3600}
\]

---

# 7. Simulator Boundary

## Inside Simulator

```text
Road Network

Intersections

Junction Storage

Demand Backlog

Transit System

Incidents

Weather

Demand Regimes

Sensor Failures
```

---

## Outside Simulator

```text
Pedestrians

Cyclists

Parking

Economic Activity

Detailed Driver Psychology

Microscopic Vehicle Dynamics
```

---

# 8. Network Schema

## Intersections

Node:

\[
i \in V_I
\]

Each intersection maintains:

```text
Signal Phase

Intersection Queue

Incoming Roads

Outgoing Roads

Junction Storage
```

---

## Roads

Road segment:

\[
e=(i,j)
\]

Each road maintains:

```text
Length

Capacity

Vehicle Count

Travel Time

Average Speed

Incident State
```

---

## Road Movements

Movement:

\[
e \rightarrow j
\]

represents a legal transition from one road segment to another through an intersection.

---

# 9. Environment State

The simulator state is:

\[
s_t
=
(G,X_t,D_t,W_t,T_t^{bus},H_t)
\]

---

## Physical State

\[
X_t
\]

Road-level variables:

\[
n_{e,t}
\]

\[
v_{e,t}
\]

\[
tt_{e,t}
\]

Intersection-level variables:

\[
Q_{i,t}
\]

\[
phase_{i,t}
\]

\[
J_{i,t}
\]

---

## Demand State

\[
D_t
\]

contains:

### New Demand

\[
\lambda_{od,t}
\]

### Backlog

\[
U_{od,t}
\]

### Served Demand

\[
d^{served}_{od,t}
\]

### Cancelled Demand

\[
d^{cancel}_{od,t}
\]

---

## Weather State

\[
W_t
\]

Version 1:

```text
Clear

Rain

Heavy Rain
```

---

## Transit State

\[
T_t^{bus}
\]

contains:

```text
Bus Position

Passenger Queue

Route Status
```

---

## Hidden State

\[
H_t
=
(I_t,H_t^{sensor},R_t)
\]

where

### Incident State

\[
I_t
\]

### Sensor State

\[
H_t^{sensor}
\]

### Demand Regime

\[
R_t
\]

---

# 10. Agent–Simulator Separation

The simulator and agent maintain separate information.

---

## Simulator Access

Simulator maintains:

```text
True State

Oracle Hidden Variables

Observation Generator

Evaluation Logs
```

---

## Agent Access

Agent receives only:

\[
o_t
\]

The agent internally constructs:

Belief:

\[
b_t(L)
\]

Representation:

\[
\mathcal R_t
=
(Z_t,X_t,C_t,B_t)
\]

Action:

\[
a_t
\]

No hidden-state information may leak from simulator to agent.

---

# 11. Conservation Accounting

Vehicles can exist in:

1. Roads
2. Junction storage
3. Demand backlog

---

## Network Population

\[
N_t^{network}
=
\sum_e n_{e,t}
+
\sum_i J_{i,t}
\]

---

## Total Population

\[
N_t^{total}
=
N_t^{network}
+
\sum_{od} U_{od,t}
\]

This definition will be used for global conservation tests.

---

# 12. Required Invariants

The following conditions must hold at every simulation step.

---

## Road Occupancy

\[
0
\le
n_{e,t}
\le
N_e^{max}
\]

---

## Junction Occupancy

\[
J_{i,t}
\ge
0
\]

---

## Queue Size

\[
Q_{i,t}
\ge
0
\]

---

## Demand Backlog

\[
U_{od,t}
\ge
0
\]

---

## Outflow Validity

\[
0
\le
F^{out}_{e,t}
\le
n_{e,t}
\]

---

## Route Assignment Conservation

\[
\sum_p
f_{p,od,t}
=
d^{served}_{od,t}
\]

---

## Reproducibility

Identical:

```text
Seed
Network
Actions
```

must generate identical trajectories.

---

# 13. Validation Targets

The simulator is considered valid only if it passes:

### Test A

Mass Conservation

---

### Test B

Observation Correctness

---

### Test C

Delay Correctness

---

### Test D

Belief Calibration

---

### Test E

Reproducibility

---

# 14. Week 1 Roadmap

## Day 1 ✅

Scope

State Definitions

Units

Boundaries

Invariants

---

## Day 2

Demand System

Admission Rules

Route Assignment

---

## Day 3

Traffic Flow Engine

Movement Constraints

Vehicle Conservation

---

## Day 4

Incident Dynamics

Weather Dynamics

Demand Regimes

---

## Day 5

Observation System

Noise

Missingness

Delay Buffer

---

## Day 6

Belief Approximation

Posterior Tracking

Uncertainty Summaries

---

## Day 7

Verification Suite

Invariant Checks

Reproducibility Testing

---

# Day 1 Exit Criteria

The Day 1 milestone is complete when:

- Simulator scope is defined
- State schema is specified
- Units are standardized
- Agent/simulator separation is enforced
- Junction storage is included
- Invariants are documented
- Week 1 subsystem roadmap is approved

---

**Status:**  Day 1 Approved Baseline (Mentor Corrections Applied)
