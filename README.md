AERL-P1: Adaptive Environment Representation Layer
Research Evolution README (Revision 1 → Revision 5)

Status: Architecture Frozen 
 Research Stage: Completed
 Current Stage: Simulator Engineering & Experimental Validation
 Domain: Urban Mobility, Transportation Networks, Transferable Environment Representations

1. Overview

AERL (Adaptive Environment Representation Layer) investigates the following research question:

Can an agent construct a structured representation of a partially observable environment that preserves decision-relevant predictive information while enabling transfer across environments with different topologies, capacities, demand patterns, and disturbances?

Problem 1 focuses on urban transportation systems consisting of:

Road networks
Intersections
Bus systems
Demand flows
Weather disruptions
Traffic incidents
Noisy and incomplete observations

The goal is not merely traffic optimization.

The goal is learning a representation:

Rt\mathcal R_t

that supports:

Prediction
Transfer Learning
Decision Making
Uncertainty Handling

across unseen cities.

2. Research Evolution
Revision 1
Initial Idea

Single latent representation:

ztz_t

Pipeline:

Traffic State
    ↓
GNN
    ↓
Temporal Encoder
    ↓
Causal Layer
    ↓
Invariant Layer
    ↓
RL Controller

Major Issues
Road variables incorrectly placed on intersections.
Single-vector representation discarded structure.
Causal claims unsupported.
World model deterministic.
Transferability weakly defined.
Mentor Verdict

Promising idea.

Not mathematically justified.

Revision 2
Major Changes

Introduced structured representation:

Rt=(Zt,Xt,Ct,Bt)\mathcal R_t = (Z_t,X_t,C_t,B_t)

where:

ZtZ_t: learned state
XtX_t: physical observations
CtC_t: constraints
BtB_t: uncertainty information
Contributions
Preserved physical variables.
Added local + global state.
Added stochastic world model.
Removed unsupported causal claims.
Mentor Verdict

Research direction approved.

Mathematical foundation still incomplete.

Revision 3
Focus

Formalizing:

Graph structure
Predictive sufficiency
Transferability
Belief state
Representation Principle
Rt=(Zt,Xt,Ct,Bt)\mathcal R_t = (Z_t,X_t,C_t,B_t)

became central.

Predictive Sufficiency Objective
P(Yt∣ht,at)≈P(Yt∣Rt,at)P(Y_t|h_t,a_t) \approx P(Y_t|\mathcal R_t,a_t)

where

Yt=(ot+1,rt)Y_t=(o_{t+1},r_t)
Transferability

Shared versus city-specific structure:

Zt=(Ztshared,Ztspecific)Z_t= (Z_t^{shared}, Z_t^{specific})
Mentor Verdict

First legitimate mathematical specification.

Not yet a complete environment model.

Revision 4
Focus

Close simulator mathematics.

Added:

Demand dynamics
Route assignment
Hidden-state transitions
Observation generation
Bayesian belief updates
Stochastic simulator model

Environment:

st=(G,Xt,Dt,Wt,Qt,Ht)s_t= (G,X_t,D_t,W_t,Q_t,H_t)
Observation Model
ot∼P(ot∣st)o_t \sim P(o_t|s_t)
Hidden-State Belief
Bt(H)=P(Ht∣ht)B_t(H) = P(H_t|h_t)
Mentor Verdict

Implementation-candidate research specification.

Simulator still incomplete.

Revision 5
Focus

Close dynamics and conservation.

Major corrections:

Demand Separation

Split:

λod,t\lambda_{od,t}

New demand

Uod,tU_{od,t}

Waiting demand

dod,tservedd^{served}_{od,t}

Served demand

dod,tcanceld^{cancel}_{od,t}

Cancelled demand

Vehicle Conservation

Road dynamics:

ne,t+1=ne,t+qe,tin−qe,toutn_{e,t+1} = n_{e,t} + q^{in}_{e,t} - q^{out}_{e,t}
Joint Belief State

Full latent state:

Lt=(Xt,Dt,Wt,Qt,Ht)L_t= (X_t,D_t,W_t,Q_t,H_t)

Belief:

bt(L)=P(Lt=L∣ht)b_t(L) = P(L_t=L|h_t)
Observation Generation

Separated:

Missingness
Noise
Delay

using

mt,τt,ytm_t, \tau_t, y_t
Mentor Verdict

Conditionally approved for implementation planning.

AERL architecture frozen.

3. Final AERL Representation
Representation Definition
Rt=(Zt,Xt,Ct,Bt)\boxed{ \mathcal R_t= (Z_t,X_t,C_t,B_t) }
Learned State
Zt=(ZtI,ZtR,ztg)Z_t= ( Z_t^I, Z_t^R, z_t^g )

Contains:

Local intersection memory
Local road memory
Global network memory
Structured State
XtX_t

Observed transportation quantities.

Examples:

speed
occupancy
queues
travel time
Constraint State
Ct={gk(Xt,at)≤0}C_t = \{ g_k(X_t,a_t)\le0 \}

Examples:

signal constraints
fleet limits
safety requirements
Belief State
Bt=P(Lt∣ht)B_t = P(L_t|h_t)

Represents uncertainty over:

incidents
demand regimes
sensor failures
hidden states
4. Transportation Environment
True Simulator State
st=(G,Xt,Dt,Wt,Sttransit,Ht)s_t= ( G, X_t, D_t, W_t, S_t^{transit}, H_t )
Hidden State
Ht=(It,St,Rt)H_t= ( I_t, S_t, R_t )

where:

Incident State
ItI_t
Sensor State
StS_t
Demand Regime
RtR_t
5. Heterogeneous Graph
Intersections
VIV_I
Roads
VRV_R
Directed Relationships
EIR\mathcal E_{IR}

Intersection → Road

ERI\mathcal E_{RI}

Road → Intersection

Graph
Gt=(VI,VR,EIR,ERI)\mathcal G_t= (V_I,V_R,\mathcal E_{IR},\mathcal E_{RI})
6. Core Research Objectives
H1 Predictive Sufficiency

AERL should preserve predictive information.

Theoretical measure:

EPS=E[DKL(P(Yt∣ht,at)∥P(Yt∣Rt,at))]E_{PS} = \mathbb E \left[ D_{KL} \Big( P(Y_t|h_t,a_t) \| P(Y_t|\mathcal R_t,a_t) \Big) \right]

Interpretation:

How much predictive information is lost by using the representation instead of the full history.

H2 Transferability

Transfer from City A to City B.

Measure:

TG=Lscratch−LtransferLscratchTG = \frac {L_{scratch}-L_{transfer}} {L_{scratch}}
H3 Robustness

Evaluate under:

Noise
Delays
Missing sensors
H4 Adaptation Efficiency

Measure interactions required to reach a target performance threshold.

H5 Representation Advantage

Compare:

A

Raw State

B

Single Latent

C

Local Representation

D

Local + Global

E

Full AERL

(Zt,Xt,Ct,Bt)(Z_t,X_t,C_t,B_t)
7. Engineering Plan
Stage 1
Custom Simulator

Initial scale:

10 Intersections
30 Directed Roads


Features:

Demand
Route Choice
Incidents
Sensor Failures
Signal Control
Observation Noise
Simulator Validation Tests
Test 1

Mass Conservation

Test 2

Observation Correctness

Test 3

Belief Calibration

Test 4

Delay Handling

Test 5

Reproducibility

Identical seed ⇒ identical trajectory.

Stage 2
AERL Experiments

Run:

H1
H2
H3
H4
H5
Stage 3
SUMO Validation

Evaluate whether AERL transfers beyond the custom simulator.

Experiments:

Custom Simulator
    ↓
SUMO


Separate from:

City A
    ↓
City B


within the custom simulator.

8. Frozen AERL Architecture
Environment
      ↓
Observation Model
      ↓
Belief State
      ↓
AERL Representation
      ↓
Predictive Model
      ↓
Planner / Policy
      ↓
Actions


or mathematically:

st→ot→Bt→Rt→Prediction→Decision\boxed{ s_t \rightarrow o_t \rightarrow B_t \rightarrow \mathcal R_t \rightarrow Prediction \rightarrow Decision }
9. Current Status
Component	StatusAERL Representation	 Frozen
Research Formulation	 Complete
Predictive Sufficiency Objective	 Defined
Transfer Objective	 Defined
Heterogeneous Graph	 Defined
Belief Framework	 Defined
Simulator Mathematics	 Sufficient for engineering
Custom Simulator Blueprint	 Next
Implementation	 Pending
Experimental Validation	 Pending
SUMO Validation	 Pending
10. Final Research Statement

AERL is a structured environment representation framework that combines learned spatial-temporal embeddings, explicit physical observations, operational constraints, and uncertainty beliefs. Rather than compressing the environment into a single latent vector, AERL seeks to preserve predictive information necessary for decision-making while enabling transfer across transportation environments with different topologies, capacities, disruptions, and observation conditions. The central hypothesis is that structured representations improve predictive sufficiency, transfer efficiency, and decision quality compared with purely latent representations.

Status: Research formulation complete. Ready for engineering implementation and experimental validation. 
