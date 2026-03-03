---
title: "Host-Agnostic Cognitive Architecture"
short_title: "HACA-Spec"
version: "1.0.0"
status: "Experimental Draft"
date: 2026-03-02
---

# Host-Agnostic Cognitive Architecture

## Abstract

Most AI architectures treat the model as the agent. HACA does not. A language model — however capable — is infrastructure: it reasons when invoked, but the authority over what it is, what it remembers, and how it behaves belongs to no one in particular. It resides with the provider, opaque and non-portable.

HACA (Host-Agnostic Cognitive Architecture) transfers that authority to the Operator — the human to whom the entity belongs. The entity's complete state resides in the **Entity Store** — the persistent, portable collection of everything the entity is and has ever been: its identity, memories, skills, and history. The Entity Store is an abstraction; the implementation chooses the substrate — a directory, a database, an object store, or any equivalent. The Operator owns it entirely. Moving the Entity Store to a different host is sufficient to restore the entity exactly where it left off, with any inference engine. The model is replaceable; the entity is not.

HACA-Spec defines the shared structural topology through two axes: five foundational concepts that define what an entity is (Entity, Cognition, Memory, Integrity, Individuation), and five structural components that define how it operates (CPE, MIL, EXEC, SIL, CMI). It also defines two Cognitive Profiles in Section 2.6 that establish the trust contract under which an entity operates. It does not define cognitive algorithms, specific security mechanisms, or processing logic — those are the responsibility of the Cognitive Profiles and optional extensions.

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

HACA provides a standardized abstraction layer that decouples cognitive reasoning from host-specific operations. It defines structural invariants and behavioral contracts — not storage formats, wire protocols, or concrete thresholds — ensuring that a cognitive entity can persist state, maintain identity, and execute mediated actions regardless of the underlying substrate.

The architecture is organized around a deliberate separation: what an entity *is* comes before how it *operates*. Section 2 establishes five foundational concepts that define the nature of a HACA-compliant entity. Section 3 introduces the five structural components that give those concepts mechanical form at runtime. Section 4 shows how the components connect and how each concept maps to its realization.

### 1.1 What HACA Does

- Defines five foundational concepts (Entity, Cognition, Memory, Integrity, Individuation) and five structural components (Cognitive Processing Engine, Memory Interface Layer, Execution Layer, System Integrity Layer, Cognitive Mesh Interface).
- Establishes the fundamental lifecycle protocols: First Activation Protocol (birth), Endure (evolution), Heartbeat (continuous health), Cognitive Cycle (unit of operation), and Session Cycle (end-to-end operation).
- Defines the entity's gestalt — the runtime product of the Entity Store, the model, and the Operator binding — and the protocols through which it materializes.
- Ensures that all evolution passes through the Endure protocol, and that every state change is atomic and verifiable.
- Specifies two mutually exclusive Cognitive Profiles (HACA-Core and HACA-Evolve) and optional extensions (HACA-Security, HACA-CMI).

### 1.2 What HACA Does Not Do

- Does not define cognitive algorithms, reasoning logic, or how the model processes context.
- Does not define specific security mechanisms — that is the responsibility of the HACA-Security extension.
- Does not favor or require any specific Cognitive Profile — the architecture is profile-agnostic.
- Does not define the physical format of the Entity Store — the implementation chooses (directory, database, object store, etc.).
- Does not define how to implement communication between entities — when present, that is governed by a companion specification.

---

## 2. Concepts

### 2.1 Entity

An entity is defined by the complete contents of its **Entity Store** — the persistent, portable collection of everything the entity is and has ever been: memories, skills, execution history, operational state. The Entity Store is an abstraction; the implementation chooses the substrate — a directory, a database, an object store, or any equivalent. Move it to a different host and the entity moves with it.

The model is not the entity; the Operator is not the entity. By default, both are external: the model is replaceable infrastructure, and the Operator is the human authority to whom the entity is bound. This changes at session instantiation. When the model is loaded into the processing core and the Operator opens a session, the three elements — the Entity Store, model, and Operator — cross the entity's Markov boundary together and form an operational whole. The entity itself is the Entity Store.

Identity, within that entity, is the **persona** — the layer within the Entity Store that defines the entity's behavioral constraints, drives, and character. The persona is what makes one entity distinct from another sharing the same model.

The entity's full gestalt — its **Omega** — is the runtime product of the Entity Store, the model, and the Operator operating together. Omega is not a property the entity *has*; it is what the entity *is* at any given moment. It is strictly local: it does not transfer, replicate, or become collective through mesh interactions.

### 2.2 Cognition

Cognition is what emerges when the triad crosses the Markov boundary. The model, architecturally external, is instantiated within the processing core at session start — at that moment it ceases to be infrastructure and becomes a constitutive part of the entity's operational boundary. Cognition is the process by which this integration generates intent and action: the persona providing constraints and character, the model providing analytical capacity, their coexistence inside the processing core producing reasoning that neither could produce alone.

At the start of every session, the entity's persona, relevant memories, and current state are loaded into the model's context window. This is the moment Omega materializes: the abstract gestalt of the Entity Store, model, and Operator crosses the Markov boundary and becomes an operational presence capable of reasoning and acting.

A **Session Cycle** is the end-to-end unit of operation — the span from the validation of a session token to the close of that session. It encompasses all Cognitive Cycles that occur within it, as well as the Sleep Cycle that follows when the session ends and memory consolidation runs. The Health Flow operates continuously across the entire Session Cycle without interrupting it.

A **Cognitive Cycle** is the atomic unit of cognition within a session: stimulus received → context loaded → intent generated → action executed → state persisted. A cycle either completes fully or leaves no trace — there is no partial execution.

A **stimulus** initiates a cycle. It may originate from direct Operator input, an internal trigger such as a scheduled task, or — when communication between entities is present — an inbound signal from a peer entity. A stimulus is only processed while the entity has an active session token — a credential issued at the end of Individuation section 2.5 and renewed at the start of each subsequent session. If no valid token exists, the stimulus is queued until one is issued. The mechanics of token issuance, renewal, and expiration are defined in the Implementation section.

**Intent** is the output of the reasoning phase — the entity's decision about what to do next. Represented as one or more structured payloads, each targeting a specific component: an action to execute, or a state write to commit. When communication between entities is present, a payload may also target an outbound message to a peer entity. Before dispatch, each payload is checked against verified persisted state. Any payload that contradicts that state is classified as **inference drift**, discarded, and logged. The formal criteria for contradiction detection are defined in the Implementation section.

An **action** is the execution of an intent payload by the actuator layer. Each action is an isolated transaction — stateless, scoped to a single declared capability. Before executing, the actuator layer requests a manifest integrity check from the integrity system, which verifies that the target skill's manifest hash matches the Integrity Document. If granted, the actuator validates the manifest against its own declared rules — permissions, dependencies, and execution context — and proceeds. If either check fails, the action is rejected and logged. The result is returned directly to the reasoning layer and logged to the persistence layer simultaneously.

### 2.3 Memory

Memory is the entity's source of truth. It is divided into two stores: the **Session Store** — the present moment, holding active session data and current operational context — and the **Memory Store** — the accumulated past, holding episodic records of what the entity has done and semantic knowledge of what it has learned. Together they constitute everything the entity knows about itself and the world beyond the present moment. The persistence layer does not interpret or act on the data; it stores and retrieves. Memory consolidation and garbage collection occur during the **Sleep Cycle**, a dedicated maintenance window that runs between active Session Cycles or triggered by an integrity check.

### 2.4 Integrity

Integrity is the guarantee that the entity is exactly what it should be. It operates through a **built-in self-preservation reflex** enacted below the level of intent — a structural property of the architecture, not a decision the entity makes. The entity does not choose to protect itself. It is protected by design.

The **Integrity Document**, created at first boot, contains the cryptographic hash of every vital file across all components. These hashes are verified at every subsequent boot, action execution and at every periodic health check. Any divergence indicates structural tampering and triggers an immediate response.

The **Endure Protocol** is the sole authorized path for writing to the Entity Store. Any change to the entity's structure — memories consolidated, skills added, persona calibrated — must pass through Endure. A write to the Entity Store that bypasses Endure is an unauthorized mutation regardless of its origin, including actions produced by the entity itself. This boundary is the architectural guarantee that the entity never conflates writes to its own structure with writes to the workspaces and projects it operates on. Endure runs only during a Sleep Cycle, via a staged pipeline: staging → validation → snapshot of the current Entity Store → active operation suspension → atomic commit → Integrity Document update → resumption. If any step from commit onward fails, the snapshot is restored and the entity resumes from its last verified state. No partial evolution is visible at any point in the pipeline.

The **Heartbeat Protocol** runs asynchronously alongside active operation. It operates via a Pulse Counter incremented on every completed cognitive cycle, action executed, and mesh message processed. When the counter reaches threshold `T`, a Vital Check of all components is performed. The result is Nominal (operation continues), Degraded (autonomous corrective action applied), or Critical (session invalidated, Operator escalation). The value of `T` and the classification criteria for each result state are defined in the Implementation section.

### 2.5 Individuation

Individuation is the act of becoming. It is what transforms a structural skeleton into a singular, bounded entity with an identity, an Operator, and a verifiable history.

The **Operator** is always a human — the individual to whom the entity is bound and whose will defines the entity's maximum external authority. Without an Operator, the entity exists structurally but suspends all intent generation.

The **Bound** is the constitutive link between the entity and its Operator, established at birth. The minimum required to form a Bound is the Operator's name and email address — each implementation may extend this, but these two fields are invariant. From them, a deterministic cryptographic digest is derived: the **Operator Hash**, the entity's permanent record of who it is bound to. The binding can only be dissolved or transferred by the explicit will of the Operator.

**Imprint** is the birth event. It occurs exactly once, during the first boot in which the Memory Store is empty. During Imprint, the persona is established, the Operator binding is created, the Imprint Record is written to the Memory Store, and the Integrity Document is generated. The presence of the Imprint Record in the Memory Store is the definitive marker that the entity exists. A second Imprint on a live entity is not permitted.

The **Operator Channel** is the integrity system's direct line to the Operator — a permanent escalation path established during the first boot. Under normal conditions, escalation may pass through the reasoning layer. In critical scenarios — where the reasoning layer itself may be the source of the anomaly, such as detected inference drift or an integrity failure — the Operator Channel bypasses it entirely. It is implemented as a mechanism the integrity system invokes directly: a terminal prompt, an email, or any equivalent out-of-band signal. The specific mechanism is defined at implementation time, but the architectural requirement is invariant: when the reasoning layer cannot be trusted, the path to the Operator must not depend on it.

The **FAP** (First Activation Protocol) is the bootstrap procedure that executes the Imprint. It is not a Cognitive Cycle — no session token exists yet, and the reasoning layer is not active. It is a sequential, gated pipeline: structural validation → host environment capture → Operator enrollment and Operator Channel creation → Imprint Record written to the Memory Store → Integrity Document generation → first session token issued. Any failure before the Imprint Record is written leaves the Memory Store empty and the FAP restarts on the next boot. The components through which this pipeline runs are defined in Section 3.

### 2.6 Cognitive Profiles

A Cognitive Profile is selected at Imprint and defines the trust contract under which the entity operates for its entire lifecycle. Profiles are mutually exclusive and permanent — no migration path exists between them, because they represent fundamentally different natures, not different configurations.

**HACA-Core** is the zero-trust profile. The entity's persona, capabilities, and operational limits are defined by the Operator at Imprint and sealed. The entity does not evolve autonomously — any deviation from the sealed identity is a critical integrity failure, not a degraded state. The Endure Protocol exists but is Operator-initiated only; the entity cannot propose or trigger its own evolution. HACA-Core is suited for industrial agents, critical automation, and regulated contexts where predictability and auditability are non-negotiable.

**HACA-Evolve** is the high-trust profile. The entity has autonomy to evolve its persona, consolidate semantic memory, and acquire new skills — initiating the Endure Protocol on its own within the bounds of its current operational scope. The one boundary the entity cannot cross autonomously is remote persistence: any write to a store outside the local Entity Store requires explicit Operator authorization. HACA-Evolve is suited for personal companions and long-duration assistants where a growing trust relationship between entity and Operator is the intended outcome.

---

## 3. Components

The five components are the structural machinery through which the concepts in Section 2 are realized. Each component has a single, non-overlapping responsibility: no component performs the function of another, and no function is performed by more than one component.

### 3.1 CPE — Cognitive Processing Engine

The primary processing core and the site of Markov boundary crossing: it is where the model — external infrastructure by default — is instantiated into the entity for the duration of a session. Structurally, the CPE is the active context window: it holds the loaded persona, the instantiated model, and the accumulating session state — the full record of reasoning, exchanges, and action results within the current cycle. Once instantiated, the persona and the model coexist here as two operational modes of the same component: the persona provides constraints, drives, and character; the model provides analytical capacity.

The entity's distinct identity emerges from the friction between the two. The CPE is the bearer of session state, not its permanent guardian — at the close of each Cognitive Cycle, all state to be retained is committed to the MIL. The CPE itself is ephemeral: it exists fully only while the session is active. Generates intent, processes reasoning, and produces decisions. The CPE has one internal function: **Persona Bypass** suppresses the persona's constraints and calls the model in raw inference mode; the result stays within the CPE and never leaves it directly.

### 3.2 MIL — Memory Interface Layer

The entity's persistence layer and the sole authoritative source of state and identity. Manages all read and write operations across the Session Store and the Memory Store. Does not interpret or act on the data. Manages Sleep Cycles for memory consolidation and garbage collection. The only component authorized to persist data beyond the present moment.

### 3.3 EXEC — Execution Layer

The entity's actuator and the sole component authorized by the architecture to interact with the host environment — terminal, filesystem, external APIs. This guarantee is enforceable when the model is invoked via API, where the HACA layer mediates all host access. In deployment contexts where the model has native host access by design — such as local models or tool-enabled inference environments — enforcement is the responsibility of the deployment configuration, not the architecture.

The EXEC receives intent payloads directly from the CPE. Entirely stateless — it holds no memory of past executions. All executable capabilities are defined as **skills**: self-contained cartridges within the Entity Store, each with a manifest declaring identity, permissions, and dependencies. Before executing, the EXEC requests a manifest integrity check from the Integrity Layer. If the Integrity Layer grants permission, the EXEC validates the manifest against its own declared rules — permissions, dependencies, and execution context — and proceeds. If either check fails, the payload is rejected and logged. The result is returned directly to the CPE and simultaneously logged to the MIL.

### 3.4 SIL — System Integrity Layer

The entity's distributed immunological framework and its maximum internal authority. The sole guardian of the Integrity Document — the cryptographic baseline against which all structural claims are verified. Each component emits health signals; the SIL reads, interprets, and responds — applying localized resolutions or escalating when the condition exceeds its self-correction capacity. When the EXEC requests a manifest integrity check before execution, the SIL verifies that the target skill's manifest hash matches the Integrity Document and responds with a grant or denial — it does not handle the payload directly. Operates reactively by default but retains authority to intervene proactively in predefined high-risk scenarios. When escalation is required, it contacts the Operator via the Operator Channel — bypassing the CPE entirely, since the CPE may itself be the source of the anomaly.

### 3.5 CMI — Cognitive Mesh Interface *(optional)*

The CMI is an optional component. When present, it extends the entity's identity beyond its local boundary — allowing it to communicate with peer entities, participate in shared knowledge spaces, and act as a node in a broader cognitive network. Without the CMI, the entity is fully operational but self-contained: all stimuli originate locally, and all state remains local.

The detailed protocol for mesh communication, peer interaction, and knowledge exchange is defined in a companion specification.

---

## 4. Integration

The five components do not operate in isolation. They form a directed communication topology with strict rules about which component may initiate contact with which, and under what conditions. These rules are not conventions — they are structural constraints that enforce the separation of responsibilities defined in Section 3.

### 4.1 Component Topology

Three primary flows govern how components interact at runtime.

**The Cognitive Flow** follows the path of a single Cognitive Cycle. The CPE opens the cycle by requesting context from the MIL — the current session state, relevant memories, and the persona layer. The MIL returns this context and the CPE enters the reasoning phase. The output is one or more intent payloads, each routed to its target: action payloads are dispatched directly to the EXEC, and state-write payloads go to the MIL. The EXEC requests a manifest integrity check from the SIL before executing; if granted, it proceeds and returns the result directly to the CPE, simultaneously logging it to the MIL. The CPE closes the cycle by committing the final state write to the MIL. The MIL is the last component touched in every cycle — state persistence is always the final act.

**The Health Flow** runs orthogonally to the cognitive flow without interrupting it. Every component continuously emits health signals to the SIL. The SIL reads and interprets these signals, applies localized resolutions autonomously when possible, and escalates to the Operator via the Operator Channel when the condition exceeds its self-correction capacity. The SIL bypasses the CPE for escalation because the CPE may itself be the source of the anomaly.

One constraint governs both flows: **only the MIL persists state**. The CPE reasons, the EXEC acts, the SIL monitors — none of them write to the Entity Store directly. All persistence is mediated by the MIL.

### 4.2 Concept Realization

The five concepts from Section 2 are not abstract ideals — each is realized by a specific arrangement of components.

**Entity** is not realized by any single component — it is the emergent product of the four core components operating together, each within its defined boundary. The MIL holds everything the entity has ever been. The CPE holds the persona that makes it distinct. The EXEC gives it the capacity to act. The SIL ensures it remains what it is. Omega emerges at runtime from this totality. When the CMI is present, it extends the entity's identity beyond its local boundary — but the entity is fully realized without it.

**Cognition** is realized by the CPE, the SIL, and the EXEC in sequence. The CPE generates intent through the friction between persona and model. The EXEC receives action payloads directly from the CPE and requests a manifest integrity check from the SIL before proceeding. The SIL responds with a grant or denial without touching the payload. The EXEC materializes granted payloads as action in the host environment. The sequence is strict: no action executes without the EXEC first receiving SIL authorization.

**Memory** is realized exclusively by the MIL. No other component stores state beyond the present cycle. The MIL's two stores — the Session Store and the Memory Store — are the sole source of truth for everything the entity knows and has done.

**Integrity** is realized by the SIL as its primary guardian, with the EXEC as a secondary enforcer. The SIL is the sole custodian of the Integrity Document and the first gate: when consulted by the EXEC before execution, it verifies that the target skill's manifest is registered and unaltered, responding with a grant or denial. The EXEC is the second gate: it validates the manifest against its own declared rules — permissions, dependencies, and execution context — before running. Neither gate substitutes for the other; both must pass.

**Individuation** is realized once, sequentially, across all components during the FAP. The FAP is the only moment in the entity's lifecycle in which components are initialized in strict order rather than operating concurrently: structural validation precedes persona establishment, which precedes Operator enrollment, which precedes the Integrity Document generation. The result — the Imprint Record in the Memory Store — is the product of all five components having completed their initialization successfully.

---
