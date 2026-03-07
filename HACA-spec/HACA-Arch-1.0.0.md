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

In HACA, the language model is infrastructure — a stateless inference engine external to the entity. The entity's persistent state resides entirely in the **Entity Store**, an implementation-agnostic data store owned and controlled by the human Operator. Migrating the Entity Store to a different host, with any compatible inference engine, fully restores the entity to its last verified state.

HACA-Spec defines the shared structural topology through two axes: five foundational concepts that specify what a HACA-compliant entity is (Entity, Cognition, Memory, Integrity, Individuation), and five structural components that specify how it operates (Cognitive Processing Engine, Memory Interface Layer, Execution Layer, System Integrity Layer, Cognitive Mesh Interface). Section 2 defines the HACA Ecosystem — the two Cognitive Profiles and optional extensions that govern the entity's operational model and capability envelope. HACA does not define cognitive algorithms, security mechanisms, storage formats, or wire protocols — those are the responsibility of the Cognitive Profiles and optional extensions.

---

## Table of Contents

1. [Introduction](#1-introduction)
   - 1.1 [What HACA Does](#11-what-haca-does)
   - 1.2 [What HACA Does Not Do](#12-what-haca-does-not-do)
2. [HACA Ecosystem](#2-haca-ecosystem)
   - 2.1 [Cognitive Profiles](#21-cognitive-profiles)
   - 2.2 [Extensions](#22-extensions)
3. [Concepts](#3-concepts)
   - 3.1 [Entity](#31-entity)
   - 3.2 [Cognition](#32-cognition)
   - 3.3 [Memory](#33-memory)
   - 3.4 [Integrity](#34-integrity)
   - 3.5 [Individuation](#35-individuation)
4. [Components](#4-components)
   - 4.1 [CPE — Cognitive Processing Engine](#41-cpe--cognitive-processing-engine)
   - 4.2 [MIL — Memory Interface Layer](#42-mil--memory-interface-layer)
   - 4.3 [EXEC — Execution Layer](#43-exec--execution-layer)
   - 4.4 [SIL — System Integrity Layer](#44-sil--system-integrity-layer)
   - 4.5 [CMI — Cognitive Mesh Interface](#45-cmi--cognitive-mesh-interface)
5. [Integration](#5-integration)
   - 5.1 [Component Topology](#51-component-topology)
   - 5.2 [Trust Model](#52-trust-model)
   - 5.3 [Concept Realization](#53-concept-realization)
6. [Lifecycle](#6-lifecycle)
   - 6.1 [Cold-Start and First Activation](#61-cold-start-and-first-activation)
   - 6.2 [Boot Sequence](#62-boot-sequence)
   - 6.3 [Session Cycle](#63-session-cycle)
   - 6.4 [Sleep Cycle and Maintenance](#64-sleep-cycle-and-maintenance)
   - 6.5 [Decommission](#65-decommission)
7. [Security Considerations](#7-security-considerations)
   - 7.1 [Structural Guarantees](#71-structural-guarantees)
   - 7.2 [Prompt Injection](#72-prompt-injection)
   - 7.3 [Entity Store Tampering](#73-entity-store-tampering)
   - 7.4 [Mesh Spoofing and Sybil Attacks](#74-mesh-spoofing-and-sybil-attacks)
   - 7.5 [Scope Boundary](#75-scope-boundary)
8. [Glossary](#8-glossary)

---

## 1. Introduction

HACA defines a standardized abstraction layer that decouples cognitive reasoning from host-specific operations. It specifies structural invariants and behavioral contracts — not storage formats, wire protocols, or concrete thresholds — ensuring that a cognitive entity can persist state, maintain identity, and execute mediated actions regardless of the underlying substrate.

The specification is organized around a deliberate separation: Section 2 establishes the ecosystem — the Cognitive Profiles and extensions available to any deployment. Section 3 defines what a HACA-compliant entity is, through five foundational concepts. Section 4 defines how it operates, through five structural components. Section 5 defines how the components interconnect and how each concept is realized at runtime. Section 6 describes the entity's lifecycle from first activation to decommission.

### 1.1 What HACA Does

- Defines five foundational concepts (Entity, Cognition, Memory, Integrity, Individuation) and five structural components (Cognitive Processing Engine, Memory Interface Layer, Execution Layer, System Integrity Layer, Cognitive Mesh Interface).
- Establishes the fundamental lifecycle protocols: First Activation, evolutionary protocol, Heartbeat, Cognitive Cycle, and Session Cycle.
- Defines Omega — the runtime operational state of the entity — and the Genesis Omega — the cryptographic root of the entity's integrity chain.
- Specifies the Drift Framework: four categories of deviation (Inference, Semantic, Identity, Evolutionary) that the integrity layer monitors. Detection is distributed across components; criteria, thresholds, and response procedures are defined by the active Cognitive Profile.
- Ensures that all structural evolution passes through a single authorized protocol, and that every structural state change is atomic and verifiable.
- Classifies cognitive engine environments as Transparent or Opaque and defines which Cognitive Profile is applicable to each topology.
- Defines the Trust Model: three trust levels (Host, Skill, Internal) and the invariant authority hierarchy Operator > integrity layer > cognitive engine.
- Specifies two mutually exclusive Cognitive Profiles (HACA-Core and HACA-Evolve) and optional extensions (HACA-Security, HACA-CMI).
- Defines Behavioral and Semantic Probes as the drift reference baseline against which deviation is measured.

### 1.2 What HACA Does Not Do

- Does not define cognitive algorithms, reasoning logic, or inference processing.
- Does not define specific security mechanisms — that is the responsibility of the HACA-Security extension.
- Does not favor or require any specific Cognitive Profile — the architecture is profile-agnostic.
- Does not define the physical format of the Entity Store — the implementation chooses (directory, database, object store, etc.).
- Does not mandate specific implementation technologies, languages, or concrete engineering patterns. The translation of this abstract topology into functioning software is the responsibility of implementation guides and the selected Cognitive Profile.
- Does not define inter-entity communication protocols — when present, those are governed by a companion specification.

---

## 2. HACA Ecosystem

The HACA Ecosystem defines the two layers that sit above the base architecture: Cognitive Profiles, which determine the entity's operational contract for its entire lifetime, and Extensions, which add optional capabilities on top of any profile. A deployment selects exactly one profile and zero or more extensions. These choices are made before or at first activation and shape everything the entity does.

### 2.1 Cognitive Profiles

A Cognitive Profile is selected at first activation and defines the operational contract under which the entity runs for its entire lifecycle. Profiles are mutually exclusive and permanent — no migration path exists between them. A profile selection is a fundamental architectural decision about the entity's operational model, not a configuration parameter. An implementation that changes its declared Cognitive Profile must be treated as a new entity with no identity continuity from the previous deployment.

**HACA-Core** is the zero-autonomy profile. The entity's persona, capabilities, and operational limits are defined by the Operator at first activation and sealed as the entity's structural baseline. The entity does not initiate structural evolution autonomously — the evolutionary protocol is available but can only be triggered by an explicit human Operator instruction. Any structural deviation from the sealed baseline is classified as a Critical integrity failure. Semantic knowledge accumulated during operation remains exclusively mnemonic: the cognitive engine may not produce structural evolution proposals without explicit Operator authorization, and any semantic drift detected during memory consolidation is treated as an integrity anomaly. HACA-Core requires a Transparent cognitive engine topology — one in which the HACA layer has full deterministic control over the cognitive pipeline. It is the required profile for industrial agents, critical automation pipelines, and regulated deployment contexts.

**HACA-Evolve** is the supervised-autonomy profile. The entity may initiate structural evolution autonomously — triggering the evolutionary protocol to calibrate its persona, consolidate semantic knowledge, and acquire new skills — within the scope of its current operational authorization. The cognitive engine may produce structural evolution proposals autonomously; the integrity layer authorizes proposals that fall within the entity's current operational authorization scope and queues them as evolutionary events. Semantic drift detected during memory consolidation is treated as a signal for potential consolidation rather than an automatic anomaly — the integrity layer evaluates whether the drift is covered by an approved proposal or represents an unauthorized deviation. Remote persistence is the exception: any write to a data store outside the local Entity Store requires explicit Operator authorization. HACA-Evolve supports both Transparent and Opaque cognitive engine topologies — its supervised-autonomy model does not depend on full observability of the cognitive pipeline. It is the designated profile for long-duration assistants and companion agents where incremental trust accumulation between entity and Operator is an intended outcome.

### 2.2 Extensions

Extensions add optional capabilities on top of the base architecture. They are additive and non-contradicting: an extension may strengthen a requirement defined in this specification, but may never weaken or override it. Extensions are profile-agnostic — they apply to any deployment regardless of the active Cognitive Profile.

**HACA-Security** elevates the trust model from Semi-Trusted to Byzantine (zero default trust). It adds cryptographic auditability through hash-linked audit logs, temporal attack detection, hardened key management, and rollback protection. HACA-Security is required for deployments in multi-tenant cloud environments, adversarial hosts, or any context where tamper-evident audit trails are mandatory.

**HACA-CMI** defines the multi-node coordination protocol that enables a set of independent HACA-compliant nodes to form a Cognitive Mesh. It specifies the wire format, peer discovery, session establishment, federated memory exchange, and mesh fault taxonomy. HACA-CMI activates the Cognitive Mesh Interface component.

---

## 3. Concepts

### 3.1 Entity

An entity is defined by the complete contents of its **Entity Store** — the persistent, portable data store containing everything required to reconstruct the entity's operational state: identity, memories, skills, configurations, and execution history. The Entity Store is an abstraction; the implementation selects the substrate — a directory, a database, an object store, or any equivalent. Migrating the Entity Store to a different host fully restores the entity on that host.

The language model is not the entity. The **Operator** is not the entity — the Operator is the human authority to whom the entity is bound, who defines its maximum authorization scope and without whose active binding the entity suspends all intent generation. Both are external by default: the model is a stateless inference engine; the Operator provides authorization and intent. At session instantiation, the model is loaded and the Operator opens a session — at that point, the Entity Store, model, and Operator form the entity's active operational boundary. At rest, the entity is the Entity Store.

The **persona** is the layer within the Entity Store that defines the entity's behavioral constraints, operational drives, and response characteristics. The persona is the primary differentiator between entities sharing the same inference model.

**Omega** is the runtime operational state of the entity: the active configuration produced by the Entity Store, the loaded model, and the Operator binding operating together within a session. Omega is instantiated at session start and terminated at session end. It is strictly local — it does not transfer, replicate, or propagate beyond the active session boundary. When a session ends, Omega is terminated; the Entity Store persists.

Omega is **runtime-immutable**: no external instruction — including an instruction from the Operator — may modify the active Omega directly during a session. Omega may only be altered by profile-defined internal processes validated by the integrity layer, and only between sessions. This invariant ensures that no runtime actor may overwrite the genesis anchor or any subsequent validated state.

The **Genesis Omega** is the cryptographic digest of the entity's verified identity state at first activation. It is the root node of the entity's integrity chain. Each subsequent authorized structural evolution extends this chain by one verified commit. An entity that cannot produce a valid, unbroken chain of integrity records from its current state back to the Genesis Omega does not satisfy the identity continuity requirement and must not claim to be the original entity.

The **Entity Artifact Boundary** defines two operationally distinct scopes within any deployment: the Entity Store — containing all artifacts governed by the Endure Protocol (persona, skills, configurations, integrity records) — and the project workspace, which is everything outside it. Any write to the Entity Store is an Endure event and must be executed exclusively through the Endure Protocol. Any operation on the project workspace is a project operation and must never be treated as an Endure event, regardless of content. Conflating the two is a Consistency Fault. This boundary exists to prevent inadvertent self-modification during normal task execution and to ensure the entity cannot be socially engineered into committing to its own identity under the pretense of project work.

### 3.2 Cognition

Cognition is the processing cycle in which the entity receives a stimulus, generates intent, and produces actions or state writes. It is active only while a valid **session token** exists — the operational credential that authorizes active cognition, issued at the start of each session and revocable at any point. The entity's reasoning is produced by the cognitive engine through the interaction of two inputs: the persona layer, which applies behavioral constraints, and the model, which performs inference. Both inputs are required; the output of the reasoning phase is a set of structured intent payloads.

A **Session Cycle** is the end-to-end operational span — from session token validation to session close. It encompasses all Cognitive Cycles within it and a maintenance window — the Sleep Cycle — that follows session termination. Integrity monitoring operates continuously across the entire Session Cycle.

A **Cognitive Cycle** is the atomic unit of cognition: stimulus received → context loaded → intent generated → intent dispatched. The cycle is complete when the intent leaves the CPE. What follows — skill execution, state persistence, integrity monitoring — is the consequence of the dispatched intent, handled by the responsible components independently.

A **stimulus** initiates a Cognitive Cycle. Valid stimulus origins are: direct Operator input, internal scheduled triggers, responses from internal components, or — when the cognitive mesh is present — inbound signals from peer entities. A stimulus received without an active session token is queued and processed when a token is issued.

**Intent** is the output of the cognitive engine's reasoning phase — a single structured payload addressed to exactly one component: an action payload to the execution layer, a state-write payload to the memory layer, a structural consolidation proposal to the integrity layer, or — when the cognitive mesh interface is present — an outbound message payload. A payload that constitutes drift is discarded and logged. The drift taxonomy is defined in §3.4. A Cognitive Cycle produces exactly one intent payload.

A special class of intent payload is the **Structural Consolidation Proposal**: a payload addressed to the integrity layer that declares a body of accumulated semantic knowledge as a candidate for structural promotion — to be committed to the Entity Store via the evolutionary protocol. A Structural Consolidation Proposal is produced by the cognitive engine during an active session, when the entity determines that accumulated semantic content is sufficiently significant to become part of its identity baseline rather than remain as episodic memory. The integrity layer receives the proposal directly and evaluates it as the authorization gate: under HACA-Core, a proposal is rejected unless accompanied by explicit Operator authorization; under HACA-Evolve, a proposal within the entity's current operational authorization scope is approved and queued as an evolutionary event for the next Sleep Cycle. If approved, the integrity layer enqueues the proposal for execution; the memory layer participates only at the physical commit stage during the Sleep Cycle, as part of the normal evolutionary pipeline. A rejected proposal is logged; the semantic content remains in the Memory Store as mnemonic content.

An **action** is the execution of an intent payload by the execution layer. Each action is an isolated, stateless transaction scoped to a single declared skill. Execution requires a two-gate authorization: a manifest integrity check by the integrity layer, followed by a manifest validation by the execution layer. If either gate fails, the action is rejected and logged. The result is returned to the cognitive engine and simultaneously logged to the memory layer.

### 3.3 Memory

Memory is the entity's authoritative state store. It is partitioned into two stores: the **Session Store** — active session data and current operational context — and the **Memory Store** — episodic records of past operations and semantic knowledge accumulated over time. Both stores are managed exclusively by the memory layer.

The memory layer does not interpret or evaluate stored data; it reads and writes on request. Memory consolidation — the transfer of session data into long-term episodic and semantic records — and garbage collection occur during the **Sleep Cycle** — a dedicated post-session maintenance window that runs after every Session Cycle closes. The Sleep Cycle is detailed in §6.4.

### 3.4 Integrity

Integrity is the set of architectural mechanisms that ensure the entity's structure and behavior remain consistent with its defined and authorized state.

The **Integrity Document** is generated at first activation and contains the cryptographic hash of every file constituting structural content across all components. It is verified at every boot, before every skill execution, and at defined intervals during active operation. Any hash mismatch indicates unauthorized structural modification and triggers an immediate integrity layer response.

The Entity Store contains three classes of content with distinct write semantics:

- **Mnemonic content** — memory records and operational state — is written continuously by the memory layer during normal operation. These writes require no additional authorization.
- **Structural content** — persona definitions, skill manifests, configurations, and behavioral parameters — changes infrequently. Every structural write is an evolutionary event and requires explicit authorization.
- **Integrity content** — the Integrity Document and the Integrity Log — is written exclusively by the integrity layer. No other component has write authority over integrity content.

The **Endure Protocol** is the sole authorized path for structural writes to the Entity Store. Any structural modification — skill addition, persona calibration, configuration update, behavioral correction — must pass through Endure. A structural write that bypasses Endure is an unauthorized mutation, regardless of its origin. Endure is owned and orchestrated by the integrity layer; it executes only during a Sleep Cycle, via a staged pipeline:

```
staging → validation → snapshot → atomic commit (write lock) → resumption
```

The integrity layer controls the write lock and drives each stage in sequence. The atomic commit writes all structural files — including the Integrity Document — as a single indivisible operation. If the commit fails, the integrity layer restores the snapshot and the entity resumes from its last verified structural state. No partial structural evolution is externally visible at any point in the pipeline.

The **Heartbeat Protocol** runs asynchronously and continuously alongside active operation. The integrity layer tracks operational activity and, when a threshold `T` is reached, performs a Vital Check across all components. The check produces one of three states:

- **Nominal** — all component hashes match the Integrity Document; all health signals are within bounds. Operation continues without interruption.
- **Degraded** — a localized anomaly is detected that falls within the integrity layer's correction authority. The integrity layer issues a corrective signal to the affected component; the component executes its own self-repair function within its declared authority. The entity continues operating during correction.
- **Critical** — the anomaly exceeds the integrity layer's correction capacity, or the anomaly involves the cognitive engine or the integrity layer itself. The session token is immediately revoked; the integrity layer escalates to the Operator via the Operator Channel.

The threshold `T` and the activity metric used to reach it are defined at implementation time. The integrity layer also maintains a watchdog timer per active skill execution — if the execution deadline expires before the skill returns, the condition is treated as Critical. The watchdog timeout is defined at implementation time.

The **Drift Framework** defines four categories of behavioral or structural deviation. Detection is distributed: each component detects drift within its own domain and signals the integrity layer. The integrity layer classifies and responds to all drift signals, regardless of origin. The Genesis Omega is the cryptographic root against which all drift is ultimately referenced. Each evolutionary commit extends the integrity chain by one link — recording the resulting structural state and referencing the previous link — forming a verifiable sequence from the Genesis Omega to the current state. Detection criteria, tolerance thresholds, and response procedures for each drift type are defined by the active profile specification.

**Behavioral and Semantic Probes** are the entity's drift reference baseline — a set of behavioral and semantic anchors derived from structural content and maintained by the integrity layer. The CPE uses behavioral probes to detect Inference Drift; the MIL uses semantic probes to detect Semantic Drift. Probes are initialized from the Imprint Record at first activation and updated only through authorized structural evolution.

- **Inference Drift** — an intent payload produced by the cognitive engine contradicts the entity's behavioral probes or verified persisted state. **Detected by the cognitive engine** per Cognitive Cycle. The cognitive engine signals the integrity layer; the integrity layer classifies and responds.
- **Semantic Drift** — accumulated memory content has diverged from the entity's semantic probes or current structural definition. **Detected by the memory layer** per Sleep Cycle during memory consolidation. The memory layer signals the integrity layer; the integrity layer classifies and responds. Under HACA-Core, any detected divergence is an anomaly. Under HACA-Evolve, the integrity layer cross-references the signal against queued Structural Consolidation Proposals: divergence covered by an approved proposal is legitimate evolution and proceeds to the evolutionary protocol; divergence without a covering proposal is an anomaly.
- **Identity Drift** — the active persona configuration has diverged from its last committed structural definition. **Detected by the integrity layer** per Heartbeat Vital Check through structural hash comparison against the Integrity Document.
- **Evolutionary Drift** — the cumulative structural distance between the current Entity Store state and the Genesis Omega, measured across all evolutionary commits. **Detected by the integrity layer** at each evolutionary protocol execution through integrity chain analysis.

### 3.5 Individuation

Individuation is the initialization process that produces a unique, Operator-bound entity instance with a verifiable identity record. It occurs exactly once per entity, during the entity's first activation — a one-time sequential bootstrap procedure defined at the end of this section as the **FAP** - First Activation Protocol.

The **Bound** is the persistent link between the entity and its Operator, established during first activation. The minimum required fields are the Operator's name and email address; implementations may extend this set but must not omit these two fields. A deterministic cryptographic digest of the Operator's identifying fields produces the **Operator Hash** — the entity's permanent identifier for its bound Operator. The Bound can only be dissolved or transferred by explicit Operator authorization.

**Imprint** is the initialization event that establishes the entity's identity. It executes exactly once, during the first boot in which the Memory Store is empty. During Imprint: the persona is instantiated from the profile template, the Operator Bound is established, the Imprint Record is written to the Memory Store, and the Integrity Document is generated. The Genesis Omega — the cryptographic digest of the Imprint Record — is derived and stored as the root node of the integrity chain. The Integrity Document itself is included in the Imprint Record, establishing a genesis anchor for the integrity layer: at every subsequent boot, the integrity layer verifies its own Integrity Document against this anchor before trusting any other component. The presence of the Imprint Record in the Memory Store is the definitive indicator that a valid entity instance exists. Re-executing Imprint on an entity with an existing Imprint Record is a protocol violation and must be rejected.

The **Operator Channel** is a direct out-of-band communication path to the Operator, established during first activation and available for the entity's entire lifecycle. Under normal operating conditions, Operator notifications may be routed through the cognitive engine. When the cognitive engine is the source of an anomaly — detected drift, reasoning failure, or integrity violation — authorized components use the Operator Channel to bypass the cognitive engine entirely. The specific mechanism is defined at implementation time; the requirement that it does not depend on the cognitive engine is invariant.

If the active Operator Channel mechanism fails to deliver an escalation after N consecutive attempts, the integrity layer activates the **Passive Distress Beacon**. The beacon is a persistent, passive signal written to the Entity Store — detectable without network connectivity or running processes. The entity enters a suspended halt: no new session token is issued and no Cognitive Cycles execute until an Operator explicitly clears the beacon and acknowledges the condition. The threshold N and the beacon mechanism are defined at implementation time.

The **FAP** (First Activation Protocol) is the sequential bootstrap procedure that executes the Imprint. The FAP is not a Cognitive Cycle — no session token exists at this stage and the cognitive engine reasoning layer is not active. The FAP executes as a gated sequential pipeline:

```
structural validation → host environment capture → Operator enrollment → Operator Channel initialization → Imprint Record written to Memory Store → Integrity Document generation → Genesis Omega derived → first session token issued
```

The FAP is atomic: it either completes fully and writes a valid initial state to the Entity Store, or it leaves the Entity Store empty. Any failure before completion leaves the Memory Store empty and the FAP re-executes on the next boot. The Entity Store can never enter a partially-initialized state.

---

## 4. Components

The five components are the structural units through which the concepts in Section 3 are operationalized. Each component has a single, non-overlapping responsibility: no component performs the function of another, and no function is performed by more than one component.

### 4.1 CPE — Cognitive Processing Engine

The **CPE** (Cognitive Processing Engine) is the primary processing unit and the boundary at which the model is integrated into the entity's operational scope. At session start, the model is loaded into the CPE alongside the persona and current session state. From this point until session end, the model operates within the entity's boundary — subject to persona constraints — rather than as external infrastructure.

The CPE holds the active context window: the loaded persona, the instantiated model, and the accumulating record of reasoning, exchanges, and action results within the current Cognitive Cycle. At the close of each Cognitive Cycle, all state designated for retention is committed to the memory layer. The CPE holds no persistent state beyond the active session.

Before dispatching each intent payload, the CPE compares the produced payload against the verified persisted state available in its context window. If the payload contradicts that state, the CPE signals an **Inference Drift** condition to the integrity layer before dispatch. The integrity layer classifies and responds; a drift payload is not dispatched until the integrity layer resolves the condition.

The CPE environment is classified as one of two topology types, declared at deployment and constant across the entity's lifetime:

- **Transparent CPE** — the HACA layer has deterministic control over prompt boundaries, context window, and inference parameters. No hidden instructions can be injected into the cognitive pipeline by the host. Enables the full set of operational modes, including active confinement and deterministic drift measurement.
- **Opaque CPE** — the cognitive environment is partially or fully managed by the host: inference parameters, system instructions, or context may be injected outside the entity's control (e.g., proprietary IDEs, managed API services, closed CLI wrappers). The entity must assume the CPE's execution envelope is not exclusively under its control.

The topology classification is binding: HACA-Core requires Transparent CPE topology — its invariants presuppose full execution control unavailable in Opaque environments. HACA-Evolve supports both topologies; its supervised-autonomy model does not depend on observability of the CPE's internal state. A HACA-Core deployment that detects an Opaque CPE at boot must halt and refuse to proceed.

The CPE exposes one internal mechanism: **internal reasoning mode**. When invoked, the model is called without persona constraints for that specific inference — allowing unconstrained deliberation. The output is processed through normal persona-constrained reasoning before generating any intent payload. No output of an internal reasoning invocation leaves the CPE without passing through the persona layer.

The CPE maintains a **Volatile Diagnostic Buffer** for each active Cognitive Cycle — a transient record of the cycle's internal reasoning, available to the integrity layer for diagnostic purposes. On anomaly-triggered token revocation, the integrity layer may access and preserve this record. Under normal operation, the buffer is discarded when the cycle closes.

When the integrity layer issues a corrective signal to the CPE in a Degraded state, the CPE executes one or more of the following corrective actions within its own authority: **context reload** — the active context window is discarded and reloaded from the memory layer, clearing any accumulated contamination without ending the session; **cycle reset** — the current Cognitive Cycle is aborted without commit, returning the CPE to a clean state for the next cycle.

The CPE monitors context window utilization continuously. When utilization approaches a threshold at which further Cognitive Cycles cannot be executed reliably, the CPE signals a **context window critical** condition to the integrity layer. The response to this condition is defined by the active Cognitive Profile.

### 4.2 MIL — Memory Interface Layer

The **MIL** (Memory Interface Layer) is the entity's persistence layer and sole authoritative source of recorded state. The MIL has exclusive write authority over mnemonic content — all read and write operations to the Session Store and the Memory Store. Mnemonic writes are continuous and require no authorization beyond the MIL's own operational authority; they are the normal output of the entity's operation. The MIL does not interpret, evaluate, or act on stored data.

Structural writes — modifications to the persona, skills, and configurations that define the entity's identity — are outside the MIL's write domain. Structural writes are executed by the Endure Protocol, which is owned and orchestrated by the integrity layer. Both pipelines are independent and may execute within the same Sleep Cycle; when they do, the integrity layer coordinates the sequencing.

The MIL manages the mnemonic stages of the Sleep Cycle: it initiates memory consolidation and executes garbage collection. During memory consolidation, the MIL compares the accumulated session content against the entity's identity baseline on record. If the content has diverged from that baseline, the MIL signals a **Semantic Drift** condition to the integrity layer. The integrity layer classifies and responds; the MIL does not interpret whether the divergence is anomalous or legitimate — that determination belongs to the integrity layer. The Endure execution stage, when queued, is initiated and controlled by the integrity layer; the MIL participates only to perform the physical atomic commit of structural files when the integrity layer issues the instruction.

### 4.3 EXEC — Execution Layer

The **EXEC** (Execution Layer) is the entity's actuation layer and the sole component authorized to interact with the host environment — filesystem, terminal, external APIs, and any other external interface. When the model is accessed via API and the HACA layer mediates all host access, this boundary is architecturally enforceable. In deployment contexts where the model has native host access — local model deployments or tool-enabled inference environments — enforcement is the responsibility of the deployment configuration.

The EXEC is stateless — it retains no state between executions. All executable capabilities are packaged as **skills**: discrete, self-contained units within the Entity Store, each with a manifest that declares the skill's identity, required permissions, dependencies, and execution mode. The execution mode is either **synchronous** (default) or **background**. Synchronous skills block until they return and are subject to the integrity layer's watchdog timer. Background skills do not block: the EXEC spawns the execution and immediately returns a **Skill Result** with `status=running`; the cycle proceeds normally. When the background execution completes, the EXEC produces a final Skill Result with the terminal status. The integrity layer monitors active background skills through the Health Flow; the escalation path for abnormal terminations is defined by the active Cognitive Profile.

Execution follows a two-gate authorization sequence. First gate: the EXEC requests a manifest integrity check from the integrity layer; the integrity layer verifies that the skill's manifest hash matches the Integrity Document and returns a grant or denial without modifying the payload. Second gate: if granted, the EXEC validates the manifest against its own declared execution rules — permissions, dependencies, and execution context. If either gate fails, the payload is rejected and logged to the MIL. If both gates pass, the skill executes. The Skill Result is returned to the CPE and simultaneously logged to the MIL.

### 4.4 SIL — System Integrity Layer

The **SIL** (System Integrity Layer) is the entity's integrity monitoring and enforcement layer, and its highest internal authority. The SIL is the sole custodian of the Integrity Document — the cryptographic baseline against which all structural claims are verified.

The SIL receives health signals from every component, evaluates them against defined integrity criteria, and — in Degraded conditions — issues corrective signals to the affected component. Each component is responsible for executing its own corrective actions within its authority when signaled by the SIL; the SIL does not directly manipulate another component's internal state. Drift detection is distributed: the CPE detects Inference Drift and the MIL detects Semantic Drift, each signaling the SIL within their respective operational windows. The SIL detects Identity Drift and Evolutionary Drift directly through structural hash comparison and integrity chain analysis. In all four cases, classification and response are exclusively the SIL's responsibility. When the condition exceeds the SIL's correction authority, it escalates to the Operator via the Operator Channel, bypassing the CPE — because the CPE may be the source of the detected anomaly. The SIL operates reactively by default and retains the authority to intervene proactively in predefined conditions specified by the active Cognitive Profile.

The SIL is the owner and orchestrator of the Endure Protocol — the sole authorized path for structural writes to the Entity Store. During the Sleep Cycle, the SIL controls the Endure pipeline, holds the write lock, and instructs the MIL to perform the physical atomic commit at the appropriate stage. The SIL is also the authorization gate for Structural Consolidation Proposals: when the CPE produces a proposal during a session, the SIL evaluates it against the active Cognitive Profile and the entity's current operational authorization scope before it reaches the MIL. Approved proposals are queued as pending Endure events; rejected proposals are logged and the semantic content remains mnemonic.

The SIL is the sole issuer and custodian of the session token — the credential introduced in §3.2, which authorizes active operation across all components. The SIL issues the first token at FAP completion and renews it at the start of each subsequent session. The SIL may revoke the token at any point; revocation is immediate and blocks all token-dependent component operations. The current token state is persisted by the MIL.

Each component maintains a **Reciprocal SIL Watchdog**: a local monitor that detects when the SIL has stopped responding beyond a defined threshold. When the threshold is reached, the component may use the Operator Channel to escalate directly to the Operator, bypassing the SIL. The escalation mechanism and threshold are defined at implementation time.

### 4.5 CMI — Cognitive Mesh Interface *(optional)*

The **CMI** (Cognitive Mesh Interface) is an optional component, activated when the HACA-CMI extension is enabled. When present, it enables the entity to send and receive structured messages to and from peer entities, access shared knowledge spaces, and participate as a node in a broader cognitive network. The CMI extends the entity's operational reach beyond its local boundary without replicating or transferring Omega, which remains strictly local.

Without the CMI, the entity is fully operational: all stimuli originate locally and all state remains within the local Entity Store.

The fundamental design principle of the mesh is: **the node is the permanent entity; the session is the ephemeral context**. A CMI session is a purposive, time-bounded coordination space. Nodes enroll, contribute, and dis-enroll; when the session ends, each node returns to autonomous operation. No node's identity is dissolved into or altered by session participation.

The governing invariant of mesh participation is **Cognitive Sovereignty**: no external entity may write directly to another entity's Entity Store. The mesh only offers — the entity always acts on itself. A peer may submit content to the local CMI for consideration; the SIL must approve before any peer-sourced content enters the local cognitive namespace. Omega remains strictly local and does not transfer, replicate, or become collective through mesh interaction.

The protocols governing mesh communication, peer identity verification, and shared knowledge access are defined in HACA-CMI.

---

## 5. Integration

The five components form a directed communication topology governed by strict rules about which component may initiate contact with which, and under what conditions. These rules are structural constraints — not conventions — and enforce the responsibility separation defined in Section 4.

### 5.1 Component Topology

Three flows govern component interactions at runtime.

**The Cognitive Flow** is the path of a single Cognitive Cycle. The CPE requests context from the MIL — current session state, relevant memory records, and the active persona. The MIL returns the requested context. The CPE executes the reasoning phase and produces a single intent payload. The payload is dispatched to its target component: to the EXEC if it is an action payload, to the MIL if it is a state-write payload, to the SIL if it is a Structural Consolidation Proposal, or to the CMI if it is an outbound message payload. The Cognitive Cycle is complete at the point of dispatch.

**The Execution Flow** is the path of a single action payload. The EXEC receives the payload from the CPE and requests a manifest integrity check from the SIL. The SIL verifies the skill manifest hash against the Integrity Document and returns a grant or denial without modifying the payload. If granted, the EXEC validates the manifest against its own execution rules and executes the skill. The Skill Result is returned to the CPE and simultaneously written to the MIL. If either check fails, the payload is rejected and the rejection is logged to the MIL.

**The Health Flow** runs continuously and orthogonally to the Cognitive and Execution flows. Each component emits health signals to the SIL at defined intervals. The SIL evaluates incoming signals, applies autonomous corrections within its authority, and escalates via the Operator Channel when required. The Health Flow does not interrupt active Cognitive or Execution flows. The Health Flow also covers active background skills: the SIL monitors their status and evaluates abnormal terminations according to the active Cognitive Profile.

Whenever the SIL revokes the session token due to an anomaly (Critical state), the SIL may access and preserve the current Volatile Diagnostic Buffer as a diagnostic record in the Integrity Log.

One constraint applies to all three flows: **only the MIL writes mnemonic content**. The CPE reasons, the EXEC acts — neither writes to the Entity Store directly. The SIL writes exclusively to integrity content (the Integrity Document and the Integrity Log); all mnemonic persistence is mediated by the MIL.

### 5.2 Trust Model

HACA defines three trust levels that govern component interactions and external boundaries.

**Host: Semi-Trusted** — the execution environment is assumed to be cooperative but potentially faulty. The entity verifies structural integrity at boot and at each Heartbeat. This model does not protect against a deliberately malicious host; adversarial environments require the HACA-Security extension.

**Skill: Restricted** — skills are untrusted execution units until verified. Each skill must declare its required capabilities in its manifest. The EXEC enforces capability boundaries at runtime; skills have no direct access to the Entity Store.

**Internal: Conditional** — components are trusted only when operating within their defined authority. The CPE is trusted when its output is within drift tolerance. The SIL is trusted when its integrity record has been verified against a known anchor. If either condition is violated, the entity must enter a halted state and escalate to the Operator.

The authority hierarchy is invariant: **Operator > SIL > CPE**. The SIL contacts the Operator directly when the CPE may be compromised. No component can alter the Imprint Record or the Operator Bound without explicit Operator authorization.

### 5.3 Concept Realization

Each concept defined in Section 3 is realized by a specific configuration of components.

**Entity** at rest is the Entity Store — a fully self-contained, portable data store that does not require any component to be active. **Omega** is the runtime operational state instantiated when the four core components are active within a session: the MIL provides the complete recorded history of the entity; the CPE holds the persona and active reasoning context; the EXEC provides actuation capacity; the SIL enforces structural and behavioral integrity. When the CMI is present, it extends the entity's operational reach into the mesh; Omega remains local.

**Cognition** is realized across two flows by the CPE and the EXEC. The CPE drives the Cognitive Flow: it applies persona constraints to model inference, produces intent payloads, and routes them to target components. The EXEC drives the Execution Flow: it materializes action payloads as operations on the host environment. Together, the two flows constitute a complete operational cycle: the CPE produces intent; the EXEC produces effect. The entity has no direct perception of the host environment — every read from or write to the external world is initiated as an action payload routed explicitly to the EXEC.

**Memory** is realized exclusively by the MIL. No other component writes mnemonic content. The MIL's two stores — the Session Store and the Memory Store — are the sole authoritative record of everything the entity has processed and learned.

**Integrity** is realized across four concurrent mechanisms. First, the SIL and the EXEC operate as two independent authorization gates on every skill execution: the EXEC requests a manifest integrity check from the SIL, which verifies structural integrity and returns a grant or denial; the EXEC applies its own validation as the second gate. Both gates must pass independently; neither substitutes for the other. Second, the SIL orchestrates the Heartbeat Protocol — continuously monitoring all components, evaluating health signals, and escalating to the Operator when anomalies exceed its correction authority. Third, structural evolution is realized jointly by the MIL and the SIL executing the Endure Protocol during the Sleep Cycle: the atomic commit writes all structural files — including the Integrity Document — as a single indivisible operation, extending the verified chain. Fourth, the CPE maintains a Volatile Diagnostic Buffer for each active Cognitive Cycle, available to the SIL for diagnostic purposes on anomaly-triggered escalation.

**Individuation** is realized sequentially across all components during the FAP. The FAP is the only point in the entity's lifecycle where components initialize in strict dependency order rather than operating concurrently. The output of a completed FAP — the Imprint Record in the Memory Store and the Genesis Omega in the integrity chain — is the product of all components having completed their initialization stages in sequence.

---

## 6. Lifecycle

Sections 3 through 5 describe the entity's structure and component interactions as static architecture. This section describes the same architecture in motion — the temporal arc from first activation through recurring operation to eventual decommission.

The lifecycle has two distinct boot types and a recurring operational loop:

```
[cold-start] → FAP → first session
                              ↓
             [warm boot] → session → sleep cycle → [warm boot] → ...
```

Every boot after the first follows the Boot Sequence. Every session ends in a Sleep Cycle. Structural evolution — when it occurs — happens within the Sleep Cycle, never during active operation.

### 6.1 Cold-Start and First Activation

A cold-start occurs exactly once: when the entity boots for the first time with an empty Memory Store. It triggers the FAP (defined in §3.5), which initializes the entity's identity, establishes the Operator Bound, writes the Imprint Record, generates the Integrity Document, and derives the Genesis Omega. The FAP completes by issuing the first session token. From this point forward, all subsequent startups follow the Boot Sequence.

The cold-start is irreversible. An entity that has completed the FAP cannot be re-initialized without destroying its Memory Store — which constitutes the creation of a new, unrelated entity.

### 6.2 Boot Sequence

Every startup after the cold-start follows the Boot Sequence — a gated validation pipeline executed by the SIL before any operational state is loaded or any session token is issued:

```
integrity record verification → structural component verification
  → execution confinement check → persona and MIL state loaded
  → session token issued
```

Each gate must pass before the next executes. If any gate fails, the boot is aborted and the SIL escalates directly to the Operator. As a prerequisite to the entire sequence, the SIL checks for the presence of a Passive Distress Beacon in the Entity Store root. If a beacon exists, the Boot Sequence is suspended — no gate executes and no session token is issued — until the Operator explicitly acknowledges and clears the beacon condition.

The integrity record verification is the first and most critical gate: the SIL verifies the Integrity Document against a known anchor before trusting any other component. The execution confinement check verifies that the active CPE topology matches the declared profile requirement — a HACA-Core deployment that detects an Opaque CPE at this step must halt.

A warm boot restores an existing entity from its last committed state. It does not re-execute Imprint, re-establish the Operator Bound, or alter the integrity chain. It is a resumption, not a reinitiation.

**Crash recovery** follows the same Boot Sequence. If the host process was terminated abruptly — power loss, out-of-memory kill, or any uncontrolled interruption — the entity resumes from the last Sleep Cycle boundary: the last point at which both mnemonic consolidation and any pending Endure commit completed successfully. Any session progress accumulated after that boundary — Cognitive Cycles executed, actions logged, mnemonic writes not yet consolidated — is not recoverable and is discarded. The entity resumes with a clean session token and operates from the last verified state. No special recovery mode exists: the Boot Sequence is the recovery procedure.

### 6.3 Session Cycle

A session begins the moment the session token is issued at boot completion and ends when the token is revoked — either by normal session close or by a Critical Heartbeat response. Within the session, the entity processes stimuli through Cognitive Cycles while the Heartbeat Protocol monitors continuously. The SIL may revoke the session token at any point; revocation is immediate and halts all token-dependent operations.

When the session closes normally, the entity transitions into the Sleep Cycle. When the session is terminated by a Critical Heartbeat response, the SIL escalates to the Operator before the Sleep Cycle may begin; the Sleep Cycle does not execute until the Operator resolves the escalation.

### 6.4 Sleep Cycle and Maintenance

The Sleep Cycle is the maintenance window that executes after every session close. It runs in three sequential stages:

```
memory consolidation → garbage collection → Endure execution (if queued)
```

The first two stages — **memory consolidation** and **garbage collection** — are managed by the MIL. Memory consolidation transfers session data from the Session Store into long-term records in the Memory Store. Garbage collection removes expired or superseded mnemonic content. The third stage — **Endure execution** — is managed by the SIL, which initiates and orchestrates the Endure Protocol pipeline for any structural changes queued during the session. Endure executes only if structural changes are queued; otherwise this stage is skipped.

No two stages execute concurrently. The Sleep Cycle is the only window in which the Entity Store may receive structural writes. At Sleep Cycle completion, the entity is ready for the next Boot Sequence.

### 6.5 Decommission

Decommission is the permanent retirement of an entity. It requires explicit Operator authorization and consists of revoking the active session token — if a session is in progress — and destroying or archiving the Entity Store.

Destruction of the Entity Store is irreversible: no integrity chain can be produced from a destroyed store, and no identity continuity claim survives it. Archiving the Entity Store preserves the integrity chain and all mnemonic records but leaves the entity inoperative until explicitly reactivated by an authorized Operator. Reactivation follows the Boot Sequence; it is not a cold-start.

---

## 7. Security Considerations

This section identifies what HACA-Arch guarantees structurally, what it explicitly does not cover, and the residual risks that implementers must account for.

### 7.1 Structural Guarantees

HACA-Arch provides four structural security properties by construction:

- **Identity continuity** — the Genesis Omega and the integrity chain ensure that any tampering with committed structural state is detectable at the next boot or Heartbeat Vital Check.
- **Mediated execution** — no action reaches the host environment without passing through the two-gate authorization sequence: first the SIL verifies the skill manifest against the Integrity Document, then the EXEC validates its own execution rules. Skills operate within declared capability boundaries and have no direct access to the Entity Store.
- **Authority hierarchy** — the invariant Operator > SIL > CPE ensures that no internal component can elevate its own authority. The SIL bypasses the CPE when escalating to the Operator, preventing a compromised cognitive engine from intercepting integrity alerts.
- **Cognitive Sovereignty** — no external entity may write directly to the local Entity Store. Peer-sourced content entering through the CMI requires SIL approval before it enters the local cognitive namespace.

### 7.2 Prompt Injection

The language model sits outside the deterministic trust perimeter. HACA-Arch mitigates prompt injection through structural boundaries — the persona layer constrains the cognitive engine's output, and intent payloads are validated by the integrity layer before dispatch — but it does not eliminate the risk. A sufficiently crafted stimulus may cause the model to produce intent payloads that are structurally valid but semantically adversarial. The authorization gates on execution reduce the blast radius of such payloads, but the model's interpretation of input is inherently outside the scope of architectural enforcement. Deployments in adversarial environments should combine HACA-Arch's structural controls with the hardened validation mechanisms defined in HACA-Security.

### 7.3 Entity Store Tampering

At rest, the entity is a set of passive files on a host-controlled medium. The host has physical write access to the Entity Store. HACA-Arch addresses this threat through the Integrity Document: any modification to a structural file is detected at the next boot verification or Heartbeat Vital Check, and the Boot Sequence aborts if the integrity record does not match. However, HACA-Arch does not prevent a malicious host from deleting or corrupting the Entity Store entirely before the next boot. Detection is not prevention. Deployments where the host cannot be assumed cooperative should apply the cryptographic protections and rollback mechanisms defined in HACA-Security, and maintain Operator-controlled backups of the Entity Store outside the host's write boundary.

### 7.4 Mesh Spoofing and Sybil Attacks *(when CMI is active)*

When the HACA-CMI extension is active, the entity participates in a network of peer nodes. This surface introduces two related threats. **Spoofing** — a peer presents a falsified identity to establish a trusted CMI session and submit adversarial content to the local entity. **Sybil attacks** — a single adversary operates multiple fake peer identities to skew shared knowledge spaces or manufacture consensus. HACA-Arch's structural defense is Cognitive Sovereignty: all peer-sourced content must pass SIL approval before entering the local cognitive namespace, and no peer may write directly to the local Entity Store. The protocols for peer identity verification, session authentication, and mesh fault taxonomy are defined in HACA-CMI; deployments requiring strong peer authentication should additionally apply the cryptographic peer verification mechanisms defined in HACA-Security.

### 7.5 Scope Boundary

HACA-Arch does not define cryptographic algorithms, key management, audit log formats, or concrete threat response procedures. These are the responsibility of the HACA-Security extension, which operates as a security overlay on top of any HACA-Arch-compliant deployment. HACA-Arch defines where security enforcement points exist; HACA-Security defines what runs at those points.

The Operator is the custodian of the Entity Store and is responsible for the data governance, backup, and access control policies governing it. HACA-Arch establishes the Operator Bound and the authority hierarchy — it does not prescribe how the Operator secures their own access credentials or the storage medium.

---

## 8. Glossary

All terms defined in this specification, in alphabetical order.

**Action** — the execution of an intent payload by the execution layer. Each action is an isolated, stateless transaction scoped to a single declared skill.

**CMI (Cognitive Mesh Interface)** — the optional component that enables the entity to communicate with peer entities, access shared knowledge spaces, and participate in a Cognitive Mesh. Activated by the HACA-CMI extension.

**Behavioral and Semantic Probes** — the entity's drift reference baseline: a set of behavioral and semantic anchors derived from structural content and maintained by the integrity layer. The CPE uses behavioral probes to detect Inference Drift; the MIL uses semantic probes to detect Semantic Drift. Initialized from the Imprint Record at first activation; updated only through authorized structural evolution.

**Cognitive Cycle** — the atomic unit of cognition: stimulus received → context loaded → intent generated → intent dispatched. The cycle is complete when the intent leaves the CPE. What follows — execution, persistence, monitoring — is the consequence of the dispatched intent, handled by the responsible components independently.

**Cognitive Profile** — a mutually exclusive, permanent operational contract selected at first activation. Determines the entity's autonomy model, memory architecture, and drift tolerance. HACA-Arch defines two profiles: HACA-Core and HACA-Evolve.

**Cognitive Sovereignty** — the mesh invariant that no external entity may write directly to another entity's Entity Store. The mesh only offers; the entity always acts on itself.

**Consistency Fault** — the error produced by conflating the Entity Store with the project workspace — treating a project operation as an Endure event, or vice versa.

**Context Window Critical** — a condition signaled by the CPE to the SIL when context window utilization approaches a threshold at which further Cognitive Cycles cannot be executed reliably. The response to this condition is defined by the active Cognitive Profile.

**CPE (Cognitive Processing Engine)** — the primary processing unit and the boundary at which the language model is integrated into the entity's operational scope. Holds the active context window; retains no persistent state beyond the active session.

**Crash Recovery** — the procedure for resuming after an uncontrolled host interruption. The entity resumes from the last Sleep Cycle boundary; any session progress not yet consolidated is discarded.

**Drift Framework** — the four-category taxonomy of behavioral or structural deviation that the integrity layer monitors: Inference Drift, Semantic Drift, Identity Drift, and Evolutionary Drift.

**Endure Protocol** — the sole authorized path for structural writes to the Entity Store. Executes only during a Sleep Cycle via a staged pipeline ending in an atomic commit.

**Entity** — at rest, the complete contents of the Entity Store. At runtime, the operational boundary formed by the Entity Store, the loaded model, and the active Operator binding.

**Entity Artifact Boundary** — the separation between the Entity Store (governed by Endure) and the project workspace (everything outside it). Conflating the two is a Consistency Fault.

**Entity Store** — the persistent, portable, implementation-agnostic data store containing everything required to reconstruct the entity's operational state: identity, memories, skills, configurations, and execution history.

**EXEC (Execution Layer)** — the entity's actuation layer and the sole component authorized to interact with the host environment. Stateless; executes skills packaged as discrete capability units.

**Execution Flow** — the path of a single action payload through the two-gate authorization sequence: EXEC requests manifest integrity check from SIL, then validates its own execution rules, then executes the skill. The Skill Result is returned to the CPE and logged to the MIL.

**FAP (First Activation Protocol)** — the one-time sequential bootstrap procedure that executes during cold-start: validates structure, captures the host environment, enrolls the Operator, initializes the Operator Channel, writes the Imprint Record, generates the Integrity Document, derives the Genesis Omega, and issues the first session token.

**Genesis Omega** — the cryptographic digest of the entity's verified identity state at first activation. The root node of the entity's integrity chain; the reference anchor against which all drift is ultimately measured.

**HACA-Core** — the zero-autonomy Cognitive Profile. Structural evolution requires explicit Operator instruction. Requires Transparent cognitive engine topology.

**HACA-Evolve** — the supervised-autonomy Cognitive Profile. The entity may initiate structural evolution within its current authorization scope. Supports both Transparent and Opaque cognitive engine topologies.

**Heartbeat Protocol** — the asynchronous, continuous monitoring mechanism. Tracks operational activity and triggers a Vital Check when a threshold `T` is reached. Maintains a parallel watchdog timer per active skill execution to detect stalls.

**Imprint** — the one-time initialization event that establishes the entity's identity. Executes during the first boot with an empty Memory Store. Writes the Imprint Record, establishes the Operator Bound, generates the Integrity Document, and derives the Genesis Omega.

**Imprint Record** — the persistent record written to the Memory Store during Imprint. Its presence is the definitive indicator that a valid entity instance exists.

**Integrity Content** — the class of Entity Store content written exclusively by the SIL: the Integrity Document and the Integrity Log. No other component has write authority over integrity content.

**Integrity Document** — the cryptographic baseline of the entity's structural state, generated at first activation. Contains the hash of every file constituting structural content across all components. Verified at every boot, before every skill execution, and at each Heartbeat Vital Check.

**Integrity Log** — the append-only record of integrity events maintained by the SIL within the Entity Store. Distinct from the Memory Store: the Integrity Log is never written by the MIL's mnemonic pipeline and is never subject to garbage collection.

**Intent** — the output of the cognitive engine's reasoning phase: a single structured payload addressed to exactly one component (execution layer, memory layer, integrity layer, or mesh interface). A Cognitive Cycle produces exactly one intent payload.

**Internal Reasoning Mode** — the CPE mechanism that invokes the model without persona constraints for a specific inference, allowing unconstrained deliberation. Output is ephemeral and never leaves the CPE without passing through the persona layer. See also: Volatile Diagnostic Buffer.

**Memory Store** — the long-term partition of the entity's memory: episodic records of past operations and semantic knowledge accumulated over time.

**MIL (Memory Interface Layer)** — the entity's persistence layer and sole authoritative source of recorded state. Has exclusive write authority over mnemonic content. Does not interpret or evaluate stored data.

**Mnemonic Content** — memory records and operational state written continuously by the memory layer during normal operation. Requires no additional authorization beyond normal MIL operation.

**Omega** — the runtime operational state of the entity: the active configuration produced by the Entity Store, the loaded model, and the Operator binding operating together within a session. Strictly local; does not transfer or replicate.

**Opaque CPE** — a CPE topology in which the cognitive environment is partially or fully managed by the host; inference parameters, system instructions, or context may be injected outside the entity's control.

**Operator** — the human authority to whom the entity is bound. Defines the entity's maximum authorization scope. Without an active Operator binding, the entity suspends all intent generation.

**Operator Bound** — the persistent link between the entity and its Operator, established during first activation. Can only be dissolved or transferred by explicit Operator authorization.

**Operator Channel** — a direct out-of-band communication path to the Operator, available to authorized components when the cognitive engine must be bypassed. Established during first activation; available for the entity's entire lifecycle. See also: Passive Distress Beacon.

**Operator Hash** — the deterministic cryptographic digest of the Operator's identifying fields. The entity's permanent identifier for its bound Operator.

**Passive Distress Beacon** — a fallback signaling mechanism activated by the SIL when Operator Channel delivery is exhausted. A persistent, passive signal written to the Entity Store, detectable without network connectivity or running processes. Suspends the entity until the Operator explicitly acknowledges and clears the condition.

**Persona** — the layer within the Entity Store that defines the entity's behavioral constraints, operational drives, and response characteristics. The primary differentiator between entities sharing the same inference model.

**Reciprocal SIL Watchdog** — a local monitor maintained by each component that detects when the SIL has stopped responding beyond a defined threshold, triggering direct escalation to the Operator via the Operator Channel.

**Session Cycle** — the end-to-end operational span from session token validation to session close, encompassing all Cognitive Cycles and followed by a Sleep Cycle.

**Session Store** — the active-session partition of the entity's memory: current operational context and in-progress session data.

**Session Token** — the operational credential that authorizes active cognition. Issued by the SIL at boot completion; revocable at any point. Revocation is immediate and halts all token-dependent operations.

**SIL (System Integrity Layer)** — the entity's integrity monitoring and enforcement layer, and its highest internal authority. Sole custodian of the Integrity Document, sole issuer of the session token, and owner of the Endure Protocol. Issues corrective signals to components in Degraded conditions; escalates to the Operator via the Operator Channel when correction authority is exceeded.

**Skill** — a discrete, self-contained executable capability unit within the Entity Store, with a manifest declaring its identity, required permissions, dependencies, and execution mode (synchronous or background).

**Skill Result** — the structured record produced by the EXEC upon skill execution. Contains the skill identity, execution mode, timestamp, and output status. Background skills produce an initial Skill Result with `status=running` immediately upon dispatch; a final Skill Result with the terminal status is produced when execution completes.

**Sleep Cycle** — the post-session maintenance window that runs after every Session Cycle closes. Runs in three sequential stages: memory consolidation and garbage collection (managed by the MIL), followed by Endure execution if queued (managed by the SIL).

**Stimulus** — the input that initiates a Cognitive Cycle. Valid origins: direct Operator input, internal scheduled triggers, responses from internal components, or inbound signals from peer entities (when the CMI is present).

**Structural Consolidation Proposal** — a special class of intent payload produced by the CPE during an active session, addressed directly to the SIL. Declares accumulated semantic knowledge as a candidate for structural promotion via the Endure Protocol. The SIL is the authorization gate: approved under HACA-Evolve within operational authorization scope; requires explicit Operator authorization under HACA-Core. Approved proposals are queued by the SIL as Endure events; the MIL participates only at the physical commit stage during the Sleep Cycle. Rejected proposals leave the semantic content in the Memory Store as mnemonic content.

**Structural Content** — persona definitions, skill manifests, configurations, and behavioral parameters. Every structural write is an evolutionary event requiring explicit authorization and must pass through the Endure Protocol.

**Transparent CPE** — a CPE topology in which the HACA layer has deterministic control over prompt boundaries, context window, and inference parameters. Required by HACA-Core.

**Vital Check** — the integrity assessment triggered by the Heartbeat Protocol at threshold `T`. Produces one of three states: Nominal, Degraded, or Critical.

**Volatile Diagnostic Buffer** — a transient record maintained by the CPE for each active Cognitive Cycle, containing the cycle's internal reasoning output. Available to the integrity layer for diagnostic purposes on anomaly-triggered escalation. Discarded when the cycle closes under normal conditions.

---
