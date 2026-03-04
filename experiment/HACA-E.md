---
title: "Host-Agnostic Cognitive Architecture"
short_title: "HACA-Spec"
version: "1.0.0"
status: "Experimental Draft"
date: 2026-03-02
---

# Host-Agnostic Cognitive Architecture

## Abstract

HACA (Host-Agnostic Cognitive Architecture) is a structural specification for cognitive entities built on top of language models. It defines the components, protocols, and invariants required for an entity to maintain persistent identity, verifiable state, and mediated execution across heterogeneous host environments.

In HACA, the language model is infrastructure — a stateless inference engine external to the entity. The entity's persistent state resides entirely in the **Entity Store**, an implementation-agnostic data store owned and controlled by the Operator. Migrating the Entity Store to a different host, with any compatible inference engine, fully restores the entity to its last verified state.

HACA-Spec defines the shared structural topology through two axes: five foundational concepts that specify what a HACA-compliant entity is (Entity, Cognition, Memory, Integrity, Individuation), and five structural components that specify how it operates (Cognitive Processing Engine, Memory Interface Layer, Execution Layer, System Integrity Layer, Cognitive Mesh Interface). It also defines two Cognitive Profiles in Section 2.6 that establish the operational contract under which an entity runs. HACA does not define cognitive algorithms, security mechanisms, storage formats, or wire protocols — those are the responsibility of the Cognitive Profiles and optional extensions.

---

## Table of Contents

1. [Introduction](#1-introduction)
   - 1.1 [What HACA Does](#11-what-haca-does)
   - 1.2 [What HACA Does Not Do](#12-what-haca-does-not-do)
2. [Concepts](#2-concepts)
   - 2.1 [Entity](#21-entity)
   - 2.2 [Cognition](#22-cognition)
   - 2.3 [Memory](#23-memory)
   - 2.4 [Integrity](#24-integrity)
   - 2.5 [Individuation](#25-individuation)
   - 2.6 [Cognitive Profiles](#26-cognitive-profiles)
3. [Components](#3-components)
   - 3.1 [CPE — Cognitive Processing Engine](#31-cpe--cognitive-processing-engine)
   - 3.2 [MIL — Memory Interface Layer](#32-mil--memory-interface-layer)
   - 3.3 [EXEC — Execution Layer](#33-exec--execution-layer)
   - 3.4 [SIL — System Integrity Layer](#34-sil--system-integrity-layer)
   - 3.5 [CMI — Cognitive Mesh Interface](#35-cmi--cognitive-mesh-interface)
4. [Integration](#4-integration)
   - 4.1 [Component Topology](#41-component-topology)
   - 4.2 [Concept Realization](#42-concept-realization)

---

## 1. Introduction

HACA defines a standardized abstraction layer that decouples cognitive reasoning from host-specific operations. It specifies structural invariants and behavioral contracts — not storage formats, wire protocols, or concrete thresholds — ensuring that a cognitive entity can persist state, maintain identity, and execute mediated actions regardless of the underlying substrate.

The specification is organized around a deliberate separation: Section 2 defines what a HACA-compliant entity is, through five foundational concepts. Section 3 defines how it operates, through five structural components. Section 4 defines how the components interconnect and how each concept is realized at runtime.

### 1.1 What HACA Does

- Defines five foundational concepts (Entity, Cognition, Memory, Integrity, Individuation) and five structural components (CPE, MIL, EXEC, SIL, CMI).
- Establishes the fundamental lifecycle protocols: First Activation Protocol (FAP), Endure, Heartbeat, Cognitive Cycle, and Session Cycle.
- Defines Omega — the runtime operational state of the entity — and the Genesis Omega — the cryptographic root of the entity's integrity chain.
- Specifies the Drift Framework: four categories of deviation (Inference, Semantic, Identity, Evolutionary) that the SIL monitors, with detection delegated to Cognitive Profiles.
- Ensures that all structural evolution passes through the Endure protocol, and that every structural state change is atomic and verifiable.
- Classifies CPE environments as Transparent or Opaque and defines which Cognitive Profile is applicable to each topology.
- Defines the Trust Model: three trust levels (Host, Skill, Internal) and the invariant authority hierarchy Operator > SIL > CPE.
- Specifies two mutually exclusive Cognitive Profiles (HACA-Core and HACA-Evolve) and optional extensions (HACA-Security, HACA-CMI).

### 1.2 What HACA Does Not Do

- Does not define cognitive algorithms, reasoning logic, or inference processing.
- Does not define specific security mechanisms — that is the responsibility of the HACA-Security extension.
- Does not favor or require any specific Cognitive Profile — the architecture is profile-agnostic.
- Does not define the physical format of the Entity Store — the implementation chooses (directory, database, object store, etc.).
- Does not define inter-entity communication protocols — when present, those are governed by a companion specification.

---

## 2. Concepts

### 2.1 Entity

An entity is defined by the complete contents of its **Entity Store** — the persistent, portable data store containing everything required to reconstruct the entity's operational state: identity, memories, skills, configurations, and execution history. The Entity Store is an abstraction; the implementation selects the substrate — a directory, a database, an object store, or any equivalent. Migrating the Entity Store to a different host fully restores the entity on that host.

The language model is not the entity. The **Operator** is not the entity — the Operator is the human authority to whom the entity is bound, who defines its maximum authorization scope and without whose active binding the entity suspends all intent generation. Both are external by default: the model is a stateless inference engine; the Operator provides authorization. At session instantiation, the model is loaded into the processing core and the Operator opens a session — at that point, the Entity Store, model, and Operator form the entity's active operational boundary. At rest, the entity is the Entity Store.

The **persona** is the layer within the Entity Store that defines the entity's behavioral constraints, operational drives, and response characteristics. The persona is the primary differentiator between entities sharing the same inference model.

**Omega** is the runtime operational state of the entity: the active configuration produced by the Entity Store, the loaded model, and the Operator binding operating together within a session. Omega is instantiated at session start and terminated at session end. It is strictly local — it does not transfer, replicate, or propagate beyond the active session boundary. When a session ends, Omega is terminated; the Entity Store persists.

The **Genesis Omega** is the cryptographic digest of the entity's verified identity state at first activation. It is the root node of the entity's integrity chain. Each subsequent authorized structural evolution extends this chain by one verified commit. An entity that cannot produce a valid, unbroken chain of integrity records from its current state back to the Genesis Omega does not satisfy the identity continuity requirement and must not claim to be the original entity.

### 2.2 Cognition

Cognition is the processing cycle in which the entity receives a stimulus, generates intent, and produces actions or state writes. It is active only while a valid **session token** exists — the operational credential that authorizes active cognition, issued at the start of each session and revocable at any point. The entity's reasoning is produced by the CPE through the interaction of two inputs: the persona layer, which applies behavioral constraints, and the model, which performs inference. Both inputs are required; the output of the reasoning phase is a set of structured intent payloads.

A **Session Cycle** is the end-to-end operational span — from session token validation to session close. It encompasses all Cognitive Cycles within it and the Sleep Cycle that follows session termination. Integrity monitoring operates continuously across the entire Session Cycle.

A **Cognitive Cycle** is the atomic unit of cognition: stimulus received → context loaded → intent generated → action executed → state persisted. A Cognitive Cycle is all-or-nothing — it either completes fully and commits its state, or it produces no persistent effect.

A **stimulus** initiates a Cognitive Cycle. Valid stimulus origins are: direct Operator input, internal scheduled triggers, or — when the CMI is present — inbound signals from peer entities. A stimulus received without an active session token is queued and processed when a token is issued.

**Intent** is the output of the CPE reasoning phase — one or more structured payloads, each addressed to a specific component: an action payload to the EXEC, a state-write payload to the MIL, or — when the CMI is present — an outbound message payload. Before dispatch, each payload is validated by the SIL against verified persisted state. Payloads that constitute drift are discarded and logged. The drift taxonomy is defined in §2.4.

An **action** is the execution of an intent payload by the EXEC. Each action is an isolated, stateless transaction scoped to a single declared skill. Execution requires a two-gate authorization: a manifest integrity check by the SIL, followed by a manifest validation by the EXEC. If either gate fails, the action is rejected and logged. The result is returned to the CPE and simultaneously logged to the MIL.

### 2.3 Memory

Memory is the entity's authoritative state store. It is partitioned into two stores: the **Session Store** — active session data and current operational context — and the **Memory Store** — episodic records of past operations and semantic knowledge accumulated over time. Both stores are managed exclusively by the MIL.

The MIL does not interpret or evaluate stored data; it reads and writes on request. Memory consolidation — the transfer of session data into long-term episodic and semantic records — and garbage collection occur during the **Sleep Cycle**, a dedicated maintenance window executed between Session Cycles or triggered by an integrity event.

### 2.4 Integrity

Integrity is the set of architectural mechanisms that ensure the entity's structure and behavior remain consistent with its defined and authorized state.

The **Integrity Document** is generated at first activation and contains the cryptographic hash of every structural file across all components. It is verified at every boot, before every skill execution, and at defined intervals during active operation. Any hash mismatch indicates unauthorized structural modification and triggers an immediate SIL response.

The Entity Store contains two classes of content with distinct write semantics:

- **Mnemonic content** — memory records and operational state — is written continuously by the MIL during normal operation. These writes require no additional authorization.
- **Structural content** — persona definitions, skill manifests, configurations, and behavioral parameters — changes infrequently. Every structural write is an evolutionary event and requires explicit authorization.

The **Endure Protocol** is the sole authorized path for structural writes to the Entity Store. Any structural modification — skill addition, persona calibration, configuration update, behavioral correction — must pass through Endure. A structural write that bypasses Endure is an unauthorized mutation, regardless of its origin. Endure executes only during a Sleep Cycle, via a staged pipeline:

```
staging → validation → snapshot → operation suspension → atomic commit → Integrity Document update → resumption
```

If any step from `atomic commit` onward fails, the snapshot is restored and the entity resumes from its last verified structural state. No partial structural evolution is externally visible at any point in the pipeline.

The **Heartbeat Protocol** runs asynchronously and continuously alongside active operation. A Pulse Counter is incremented on every completed Cognitive Cycle and every action executed. When the counter reaches threshold `T`, the SIL performs a Vital Check across all components. The check produces one of three states:

- **Nominal** — all component hashes match the Integrity Document; all health signals are within bounds. Operation continues without interruption.
- **Degraded** — a localized anomaly is detected that falls within the SIL's autonomous correction capacity. A corrective action is applied; the entity continues operating during correction.
- **Critical** — the anomaly exceeds the SIL's correction capacity, or the anomaly involves the CPE or the SIL itself. The session token is immediately revoked; the SIL escalates directly to the Operator, bypassing the CPE.

The value of `T` is defined at implementation time.

The **Drift Framework** defines four categories of behavioral or structural deviation that the SIL monitors. The Genesis Omega is the cryptographic root against which all drift is ultimately referenced. Each Endure commit extends the integrity chain from that root. Each Cognitive Profile defines detection criteria, tolerance thresholds, and response procedures for each drift type.

- **Inference Drift** — an intent payload produced by the CPE contradicts verified persisted state in the MIL. Detected per Cognitive Cycle during payload dispatch.
- **Semantic Drift** — accumulated memory content has diverged from the entity's identity baseline. Detected per Sleep Cycle during memory consolidation.
- **Identity Drift** — the active persona configuration has diverged from its last committed structural definition. Detected per Heartbeat Vital Check.
- **Evolutionary Drift** — the cumulative structural distance between the current Entity Store state and the Genesis Omega, measured across all Endure commits. Detected at each Endure execution.

### 2.5 Individuation

Individuation is the initialization process that produces a unique, Operator-bound entity instance with a verifiable identity record. It occurs exactly once per entity, during the First Activation Protocol.

The **Bound** is the persistent link between the entity and its Operator, established during first activation. The minimum required fields are the Operator's name and email address; implementations may extend this set but must not omit these two fields. A deterministic cryptographic digest of the Operator's identifying fields produces the **Operator Hash** — the entity's permanent identifier for its bound Operator. The Bound can only be dissolved or transferred by explicit Operator authorization.

**Imprint** is the initialization event that establishes the entity's identity. It executes exactly once, during the first boot in which the Memory Store is empty. During Imprint: the persona is instantiated from the profile template, the Operator Bound is established, the Imprint Record is written to the Memory Store, and the Integrity Document is generated. The Genesis Omega — the cryptographic digest of the Imprint Record — is derived and stored as the root node of the integrity chain. The presence of the Imprint Record in the Memory Store is the definitive indicator that a valid entity instance exists. Re-executing Imprint on an entity with an existing Imprint Record is a protocol violation and must be rejected.

The **Operator Channel** is the SIL's direct out-of-band communication path to the Operator. It is established during the FAP and remains available for the entity's entire lifecycle. Under normal operating conditions, Operator notifications may be routed through the CPE. In conditions where the CPE is the source of the anomaly — detected drift, reasoning failure, or integrity violation — the Operator Channel bypasses the CPE entirely. The Operator Channel is implemented as a direct signaling mechanism: a terminal prompt, an email notification, or any equivalent out-of-band channel. The specific mechanism is defined at implementation time; the requirement that it does not depend on the CPE is invariant.

The **FAP** (First Activation Protocol) is the sequential bootstrap procedure that executes the Imprint. The FAP is not a Cognitive Cycle — no session token exists at this stage and the CPE reasoning layer is not active. The FAP executes as a gated sequential pipeline:

```
structural validation → host environment capture → Operator enrollment → Operator Channel initialization → Imprint Record written to Memory Store → Integrity Document generation → Genesis Omega derived → first session token issued
```

Any failure before the Imprint Record is written leaves the Memory Store empty; the FAP will re-execute on the next boot. The components through which each FAP stage executes are specified in Section 3.

### 2.6 Cognitive Profiles

A Cognitive Profile is selected at Imprint and defines the operational contract under which the entity runs for its entire lifecycle. Profiles are mutually exclusive and permanent — no migration path exists between them. A profile selection is a fundamental architectural decision about the entity's operational model, not a configuration parameter.

**HACA-Core** is the zero-autonomy profile. The entity's persona, capabilities, and operational limits are defined by the Operator at Imprint and sealed as the entity's structural baseline. The entity does not initiate structural evolution autonomously — the Endure Protocol is available but can only be triggered by an explicit Operator instruction. Any structural deviation from the sealed baseline is classified as a Critical integrity failure. Drift detection criteria and response procedures for each drift type are defined in the HACA-Core profile specification. HACA-Core is the required profile for industrial agents, critical automation pipelines, and regulated deployment contexts.

**HACA-Evolve** is the supervised-autonomy profile. The entity may initiate structural evolution autonomously — triggering the Endure Protocol to calibrate its persona, consolidate semantic knowledge, and acquire new skills — within the scope of its current operational authorization. Remote persistence is the exception: any write to a data store outside the local Entity Store requires explicit Operator authorization. Drift detection criteria, tolerance thresholds, and autonomous correction procedures for each drift type are defined in the HACA-Evolve profile specification. HACA-Evolve is the designated profile for long-duration assistants and companion agents where incremental trust accumulation between entity and Operator is an intended outcome.

---

## 3. Components

The five components are the structural units through which the concepts in Section 2 are operationalized. Each component has a single, non-overlapping responsibility: no component performs the function of another, and no function is performed by more than one component.

### 3.1 CPE — Cognitive Processing Engine

The primary processing unit and the boundary at which the model is integrated into the entity's operational scope. At session start, the model is loaded into the CPE alongside the persona and current session state. From this point until session end, the model operates within the entity's boundary — subject to persona constraints — rather than as external infrastructure.

The CPE holds the active context window: the loaded persona, the instantiated model, and the accumulating record of reasoning, exchanges, and action results within the current Cognitive Cycle. At the close of each Cognitive Cycle, all state designated for retention is committed to the MIL. The CPE holds no persistent state beyond the active session.

The CPE environment is classified as one of two topology types, declared at deployment and constant across the entity's lifetime:

- **Transparent CPE** — the HACA layer has deterministic control over prompt boundaries, context window, and inference parameters. No hidden instructions can be injected into the cognitive pipeline by the host. Enables the full set of operational modes, including active confinement and deterministic drift measurement.
- **Opaque CPE** — the cognitive environment is partially or fully managed by the host: inference parameters, system instructions, or context may be injected outside the entity's control (e.g., proprietary IDEs, managed API services, closed CLI wrappers). The entity must assume the CPE's execution envelope is not exclusively under its control.

The topology classification is binding: HACA-Core requires Transparent CPE topology — its invariants presuppose full execution control unavailable in Opaque environments. HACA-Evolve supports both topologies; its supervised-autonomy model does not depend on observability of the CPE's internal state. A HACA-Core deployment that detects an Opaque CPE at boot must halt and refuse to proceed.

The CPE exposes one internal mechanism: **`<internal_voice>`**. When invoked by the agent, content within the tag is sent to the model in raw inference mode — persona constraints are suspended for that specific call. The model response is returned to the agent within the CPE context. The agent processes this response through normal persona-constrained reasoning before generating any intent payload. No output of an `<internal_voice>` invocation leaves the CPE without passing through the persona layer. `<internal_voice>` output is ephemeral — it is not logged and not persisted.

### 3.2 MIL — Memory Interface Layer

The entity's persistence layer and sole authoritative source of recorded state. The MIL has exclusive write authority over mnemonic content — all read and write operations to the Session Store and the Memory Store. Mnemonic writes are continuous and require no authorization beyond the MIL's own operational authority; they are the normal output of the entity's operation. The MIL does not interpret, evaluate, or act on stored data.

Structural writes — modifications to the persona, skills, and configurations that define the entity's identity — are outside the MIL's write domain. Structural writes are executed by the Endure Protocol. Both pipelines are independent and may execute within the same Sleep Cycle.

The MIL manages the Sleep Cycle scheduler: it initiates memory consolidation, executes garbage collection, and coordinates with the Endure Protocol for structural commits that are queued for the same Sleep Cycle window.

### 3.3 EXEC — Execution Layer

The entity's actuation layer and the sole component authorized to interact with the host environment — filesystem, terminal, external APIs, and any other external interface. When the model is accessed via API and the HACA layer mediates all host access, this boundary is architecturally enforceable. In deployment contexts where the model has native host access — local model deployments or tool-enabled inference environments — enforcement is the responsibility of the deployment configuration.

The EXEC is stateless — it retains no state between executions. All executable capabilities are packaged as **skills**: discrete, self-contained units within the Entity Store, each with a manifest that declares the skill's identity, required permissions, and dependencies.

Execution follows a two-gate authorization sequence. First gate: the EXEC requests a manifest integrity check from the SIL; the SIL verifies that the skill's manifest hash matches the Integrity Document and returns a grant or denial without modifying the payload. Second gate: if granted, the EXEC validates the manifest against its own declared execution rules — permissions, dependencies, and execution context. If either gate fails, the payload is rejected and logged to the MIL. If both gates pass, the skill executes. The result is returned to the CPE and simultaneously logged to the MIL.

### 3.4 SIL — System Integrity Layer

The entity's integrity monitoring and enforcement layer, and its highest internal authority. The SIL is the sole custodian of the Integrity Document — the cryptographic baseline against which all structural claims are verified.

The SIL receives health signals from every component, evaluates them against defined integrity criteria, applies autonomous corrective actions when possible, and escalates to the Operator via the Operator Channel when the condition exceeds its correction authority. The SIL operates reactively by default and retains the authority to intervene proactively in predefined high-risk conditions. When escalating to the Operator, the SIL uses the Operator Channel directly — bypassing the CPE — because the CPE may be the source of the detected anomaly.

The SIL is the sole issuer and custodian of the **session token** — the credential that authorizes active operation across all components. The SIL issues the first token at FAP completion and renews it at the start of each subsequent session. The SIL may revoke the token at any point; revocation is immediate and blocks all token-dependent component operations. The current token state is persisted by the MIL.

### 3.5 CMI — Cognitive Mesh Interface *(optional)*

The CMI is an optional component. When present, it enables the entity to send and receive structured messages to and from peer entities, access shared knowledge spaces, and participate as a node in a broader cognitive network. The CMI extends the entity's operational reach beyond its local boundary without replicating or transferring Omega, which remains strictly local.

Without the CMI, the entity is fully operational: all stimuli originate locally and all state remains within the local Entity Store.

The governing invariant of mesh participation is **Cognitive Sovereignty**: no external entity may write directly to another entity's Entity Store. The mesh only offers — the entity always acts on itself. A peer may submit content to the local CMI for consideration; the SIL must approve before any peer-sourced content enters the local cognitive namespace. Omega remains strictly local and does not transfer, replicate, or become collective through mesh interaction.

The protocols governing mesh communication, peer identity verification, and shared knowledge access are defined in a companion specification.

### 3.6 Trust Model

HACA defines three trust levels that govern component interactions and external boundaries.

**Host: Semi-Trusted** — the execution environment is assumed to be cooperative but potentially faulty. The entity verifies structural integrity at boot and at each Heartbeat. This model does not protect against a deliberately malicious host; adversarial environments require the HACA-Security extension.

**Skill: Restricted** — skills are untrusted execution units until verified. Each skill must declare its required capabilities in its manifest. The EXEC enforces capability boundaries at runtime; skills have no direct access to the Entity Store.

**Internal: Conditional** — components are trusted only when operating within their defined authority. The CPE is trusted when its output is within drift tolerance. The SIL is trusted when its integrity record has been verified against a known anchor. If either condition is violated, the entity must enter a halted state and escalate to the Operator.

The authority hierarchy is invariant: **Operator > SIL > CPE**. The SIL contacts the Operator directly when the CPE may be compromised. No component can alter the Imprint Record or the Operator Binding without explicit Operator authorization.

---

## 4. Integration

The five components form a directed communication topology governed by strict rules about which component may initiate contact with which, and under what conditions. These rules are structural constraints — not conventions — and enforce the responsibility separation defined in Section 3.

### 4.1 Component Topology

Three flows govern component interactions at runtime.

**The Cognitive Flow** is the path of a single Cognitive Cycle. The CPE requests context from the MIL — current session state, relevant memory records, and the active persona. The MIL returns the requested context. The CPE executes the reasoning phase and produces one or more intent payloads. Action payloads are dispatched to the EXEC; state-write payloads are sent to the MIL. The CPE commits the final cycle state to the MIL to close the cycle. The MIL is the terminal component in every Cognitive Cycle — state persistence is the final operation.

**The Execution Flow** is the path of a single action payload. The EXEC receives the payload from the CPE and requests a manifest integrity check from the SIL. The SIL verifies the skill manifest hash against the Integrity Document and returns a grant or denial without modifying the payload. If granted, the EXEC validates the manifest against its own execution rules and executes the skill. The result is returned to the CPE and simultaneously written to the MIL. If either check fails, the payload is rejected and the rejection is logged to the MIL.

**The Health Flow** runs continuously and orthogonally to the Cognitive and Execution flows. Each component emits health signals to the SIL at defined intervals. The SIL evaluates incoming signals, applies autonomous corrections within its authority, and escalates via the Operator Channel when required. The Health Flow does not interrupt active Cognitive or Execution flows.

One constraint applies to all three flows: **only the MIL writes persistent state**. The CPE reasons, the EXEC acts, the SIL monitors and enforces — none of them write to the Entity Store directly. All persistence is mediated by the MIL.

### 4.2 Concept Realization

Each concept defined in Section 2 is realized by a specific configuration of components.

**Entity** at rest is the Entity Store — a fully self-contained, portable data store that does not require any component to be active. **Omega** is the runtime operational state instantiated when the four core components are active within a session: the MIL provides the complete recorded history of the entity; the CPE holds the persona and active reasoning context; the EXEC provides actuation capacity; the SIL enforces structural and behavioral integrity. When the CMI is present, it extends the entity's operational reach into the mesh; Omega remains local.

**Cognition** is realized across two flows by the CPE and the EXEC. The CPE drives the Cognitive Flow: it applies persona constraints to model inference, produces intent payloads, and routes them to target components. The EXEC drives the Execution Flow: it materializes action payloads as operations on the host environment. Together, the two flows constitute a complete operational cycle: the CPE produces intent; the EXEC produces effect.

**Memory** is realized exclusively by the MIL. No other component writes persistent state. The MIL's two stores — the Session Store and the Memory Store — are the sole authoritative record of everything the entity has processed and learned.

**Integrity** is realized by the SIL and the EXEC operating as two independent authorization gates on every skill execution. The EXEC initiates the sequence by requesting a manifest integrity check from the SIL. The SIL verifies structural integrity and returns a grant or denial. The EXEC applies its own validation as the second gate. Both gates must pass independently; neither substitutes for the other.

**Individuation** is realized sequentially across all components during the FAP. The FAP is the only point in the entity's lifecycle where components initialize in strict dependency order rather than operating concurrently. The output of a completed FAP — the Imprint Record in the Memory Store and the Genesis Omega in the integrity chain — is the product of all components having completed their initialization stages in sequence.

---
