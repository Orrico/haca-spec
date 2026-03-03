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

HACA (Host-Agnostic Cognitive Architecture) transfers that authority to the Operator — the human to whom the entity belongs. The entity's complete state resides in a persistent directory (`entity_root/`): its identity, memories, skills, and history. The Operator owns it entirely. A `git clone` is sufficient to restore the entity exactly where it left off, on any host, with any inference engine. The model is replaceable; the entity is not.

HACA-Spec defines the shared structural topology through two axes: five foundational concepts that define what an entity is (Entity, Cognition, Memory, Integrity, Individuation), and five structural components that define how it operates (CPE, MIL, EXEC, SIL, CMI). It does not define cognitive algorithms, specific security mechanisms, or processing logic — those are the responsibility of the Cognitive Profiles and optional extensions.

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

An entity built on HACA exists as a persistent directory (`entity_root/`) that can move across hosts, survive model upgrades, and evolve over time — while remaining cryptographically verifiable at every step. The concepts in Section 2 define what that entity is; the components in Section 3 define the structural machinery through which it operates. Cognitive Profiles (HACA-Core and HACA-Evolve) specialize both for specific deployment contexts without altering the foundational structure.

### 1.1 What HACA Does

- Defines five foundational concepts (Entity, Cognition, Memory, Integrity, Individuation) and five structural components (Cognitive Processing Engine, Memory Interface Layer, Execution Layer, System Integrity Layer, Cognitive Mesh Interface).
- Establishes the fundamental lifecycle protocols: First Activation Protocol (birth), Endure (evolution), Heartbeat (continuous health), Cognitive Cycle (unit of operation), and Session Cycle (end-to-end operation).
- Defines Omega — the entity's gestalt as the runtime product of `entity_root/`, the model, and the Operator binding.
- Ensures that all evolution passes through the Endure protocol, and that every state change is atomic and verifiable.
- Specifies two mutually exclusive Cognitive Profiles (HACA-Core and HACA-Evolve) and optional extensions (HACA-Security, HACA-CMI).

### 1.2 What HACA Does Not Do

- Does not define cognitive algorithms, reasoning logic, or how the model processes context.
- Does not define specific security mechanisms — that is the responsibility of the HACA-Security extension.
- Does not favor or require any specific Cognitive Profile — the architecture is profile-agnostic.
- Does not define the physical format of `entity_root/` — the implementation chooses (directory, database, object store, etc.).
- Does not define how to implement communication between entities — when present, that is governed by a companion specification.

---

## 2. Concepts

### 2.1 Entity

An entity is defined by the complete contents of `entity_root/` — its persistent state store. `entity_root/` holds everything the entity is and has ever been: memories, skills, execution history, operational state. Move it to a different host and the entity moves with it. The model is not the entity; the Operator is not the entity. They are external elements that the entity requires to operate, but the entity itself is `entity_root/`.

Identity, within that entity, is the **persona** — the layer stored in `entity_root/persona/` that defines the entity's behavioral constraints, drives, and character. The persona is what makes one entity distinct from another sharing the same model.

The entity's full gestalt — its **Omega** — is the runtime product of `entity_root/`, the model, and the Operator operating together. Omega is not a property the entity *has*; it is what the entity *is* at any given moment. It is strictly local: it does not transfer, replicate, or become collective through mesh interactions.

### 2.2 Cognition

Cognition is the friction between the entity and the model — the process by which their coexistence generates intent and action. At the start of every session, the entity's persona, relevant memories, and current state are loaded into the model's context window. This is the moment Omega materializes: the abstract gestalt of `entity_root/`, model, and Operator becomes an operational presence capable of reasoning and acting.

A **Session Cycle** is the end-to-end unit of operation — the span from the validation of a session token to the close of that session. It encompasses all Cognitive Cycles that occur within it, as well as the Sleep Cycle that follows when the session ends and memory consolidation runs. The Health Flow operates continuously across the entire Session Cycle without interrupting it.

A **Cognitive Cycle** is the atomic unit of cognition within a session: stimulus received → context loaded → intent generated → action executed → state persisted. A cycle either completes fully or leaves no trace — there is no partial execution.

A **stimulus** initiates a cycle. It may originate from direct Operator input, an internal trigger such as a scheduled task, or — when communication between entities is present — an inbound signal from a peer entity. A stimulus is only processed while the entity has an active session token — a credential issued at the end of Individuation section 2.5 and renewed at the start of each subsequent session. If no valid token exists, the stimulus is queued until one is issued.

**Intent** is the output of the reasoning phase — the entity's decision about what to do next. Represented as one or more structured payloads, each targeting a specific component: an action to execute, or a state write to commit. When communication between entities is present, a payload may also target an outbound message to a peer entity. Before dispatch, each payload is checked against verified persisted state. Any payload that contradicts that state is classified as **inference drift**, discarded, and logged.

An **action** is the execution of an intent payload by the actuator layer. Each action is an isolated transaction — stateless, scoped to a single declared capability, and validated against its declared permissions before execution. The result is returned to the reasoning layer and logged to the persistence layer simultaneously.

### 2.3 Memory

Memory is the entity's source of truth. It is divided into two stores: `state/` — current operational and session data — and `memory/` — long-term memories, learned knowledge, and execution history. Together they constitute everything the entity knows about itself and the world beyond the present moment. The persistence layer does not interpret or act on the data; it stores and retrieves. Memory consolidation and garbage collection occur during the **Sleep Cycle**, a dedicated maintenance window that runs between active Session Cycles or triggered by an integrity check.

### 2.4 Integrity

Integrity is the guarantee that the entity is exactly what it should be. It operates through the **Conatus** — the architecture's built-in self-preservation reflex, enacted below the level of intent. The entity does not choose to protect itself. It is protected by design.

The **Integrity Document**, created at first boot, contains the cryptographic hash of every vital file across all components. These hashes are verified at every subsequent boot, action execution and at every periodic health check. Any divergence indicates structural tampering and triggers an immediate response.

The **Endure Protocol** ensures that the entity can only evolve on its own terms. Any change to `entity_root/` — memories consolidated, skills added, persona calibrated — must pass through Endure. Changes applied outside of it are unauthorized mutations. Endure runs only during a Sleep Cycle, via a staged pipeline: staging → validation → active operation suspension → atomic root commit → Integrity Document update → resumption.

The **Heartbeat Protocol** runs asynchronously alongside active operation. It operates via a Pulse Counter incremented on every completed cognitive cycle, action executed, and mesh message processed. When the counter reaches threshold `T`, a Vital Check of all components is performed. The result is Nominal (operation continues), Degraded (autonomous corrective action applied), or Critical (session invalidated, Operator escalation).

### 2.5 Individuation

Individuation is the act of becoming. It is what transforms a structural skeleton into a singular, bounded entity with an identity, an Operator, and a verifiable history.

The **Operator** is always a human — the individual to whom the entity is bound and whose will defines the entity's maximum external authority. Without an Operator, the entity exists structurally but suspends all intent generation.

The **Bound** is the constitutive link between the entity and its Operator, established at birth. The minimum required to form a Bound is the Operator's name and email address — each implementation may extend this, but these two fields are invariant. From them, a deterministic cryptographic digest is derived: the **Operator Hash**, the entity's permanent record of who it is bound to. The binding can only be dissolved or transferred by the explicit will of the Operator.

**Imprint** is the birth event. It occurs exactly once, during the first boot in which `memory/` is empty. During Imprint, the persona is established, the Operator binding is created, the Imprint Record is written to `memory/`, and the Integrity Document is generated. The presence of the Imprint Record in `memory/` is the definitive marker that the entity exists. A second Imprint on a live entity is not permitted.

The **Operator Channel** is the integrity system's direct line to the Operator — a permanent, out-of-band escalation path created during the first boot and bypassing the inference entirely, since the model may itself be the source of the anomaly requiring escalation.

The **FAP** (First Activation Protocol) is the bootstrap procedure that executes the Imprint. It is not a Cognitive Cycle — no session token exists yet, and the reasoning layer is not active. It is a sequential, gated pipeline: structural validation → host environment capture → Operator enrollment and Operator Channel creation → Imprint Record written to `memory/` → Integrity Document generation → first session token issued. Any failure before the Imprint Record is written leaves `memory/` empty and the FAP restarts on the next boot.

---

## 3. Components

The five components are the structural machinery through which the concepts in Section 2 are realized. Each component has a single, non-overlapping responsibility: no component performs the function of another, and no function is performed by more than one component.

### 3.1 CPE — Cognitive Processing Engine

The primary processing core. The persona and the model coexist here as two operational modes of the same component: the persona provides constraints, drives, and character; the model provides analytical capacity. The entity's distinct identity emerges from the friction between the two. Generates intent, processes reasoning, and produces decisions. Its internal **Persona Bypass** suppresses the persona and calls the model in raw inference mode; the result stays within the CPE and never leaves it directly.

### 3.2 MIL — Memory Interface Layer

The entity's persistence layer and the sole authoritative source of state and identity. Manages all read and write operations across `state/` and `memory/`. Does not interpret or act on the data. Manages Sleep Cycles for memory consolidation and garbage collection. The only component authorized to persist data beyond the present moment.

### 3.3 EXEC — Execution Layer

The entity's actuator. The only component authorized to interact with the host environment — terminal, filesystem, external APIs. Receives pre-validated intent payloads from the SIL and executes the corresponding action, returning the result to the CPE and simultaneously logging it to the MIL. Entirely stateless — it holds no memory of past executions. All executable capabilities are defined as **skills**: self-contained cartridges in `entity_root/skills/skill_name/`, each with a manifest declaring identity, permissions, and dependencies. Before execution, the EXEC validates the manifest against its own declared rules — permissions, dependencies, and execution context. A payload that passes SIL validation but fails EXEC validation is rejected at this layer and logged.

### 3.4 SIL — System Integrity Layer

The entity's distributed immunological framework and its maximum internal authority. The sole guardian of the Integrity Document — the cryptographic baseline against which all structural claims are verified. Each component emits health signals; the SIL reads, interprets, and responds — applying localized resolutions or escalating when the condition exceeds its self-correction capacity. Before any action payload is dispatched to the EXEC, the SIL verifies that the target skill's manifest hash matches the Integrity Document; only manifests that pass this check proceed. Operates reactively by default but retains authority to intervene proactively in predefined high-risk scenarios. When escalation is required, it contacts the Operator via the Operator Channel — bypassing the CPE entirely, since the CPE may itself be the source of the anomaly.

### 3.5 CMI — Cognitive Mesh Interface *(optional)*

The CMI is an optional component. When present, it extends the entity's identity beyond its local boundary — allowing it to communicate with peer entities, participate in shared knowledge spaces, and act as a node in a broader cognitive network. Without the CMI, the entity is fully operational but self-contained: all stimuli originate locally, and all state remains local.

The detailed protocol for mesh communication, peer interaction, and knowledge exchange is defined in a companion specification.

---

## 4. Integration

The five components do not operate in isolation. They form a directed communication topology with strict rules about which component may initiate contact with which, and under what conditions. These rules are not conventions — they are structural constraints that enforce the separation of responsibilities defined in Section 3.

### 4.1 Component Topology

Three primary flows govern how components interact at runtime.

**The Cognitive Flow** follows the path of a single Cognitive Cycle. The CPE opens the cycle by requesting context from the MIL — the current session state, relevant memories, and the persona layer. The MIL returns this context and the CPE enters the reasoning phase. The output is one or more intent payloads, each routed to its target: action payloads are first validated by the SIL and then dispatched to the EXEC, and state-write payloads go to the MIL. The EXEC executes its payload, returns the result to the CPE, and simultaneously logs it to the MIL. The CPE closes the cycle by committing the final state write to the MIL. The MIL is the last component touched in every cycle — state persistence is always the final act.

**The Health Flow** runs orthogonally to the cognitive flow without interrupting it. Every component continuously emits health signals to the SIL. The SIL reads and interprets these signals, applies localized resolutions autonomously when possible, and escalates to the Operator via the Operator Channel when the condition exceeds its self-correction capacity. The SIL bypasses the CPE for escalation because the CPE may itself be the source of the anomaly.

One constraint governs both flows: **only the MIL persists state**. The CPE reasons, the EXEC acts, the SIL monitors — none of them write to `entity_root/` directly. All persistence is mediated by the MIL.

### 4.2 Concept Realization

The five concepts from Section 2 are not abstract ideals — each is realized by a specific arrangement of components.

**Entity** is not realized by any single component — it is the emergent product of the four core components operating together, each within its defined boundary. The MIL holds everything the entity has ever been. The CPE holds the persona that makes it distinct. The EXEC gives it the capacity to act. The SIL ensures it remains what it is. Omega emerges at runtime from this totality. When the CMI is present, it extends the entity's identity beyond its local boundary — but the entity is fully realized without it.

**Cognition** is realized by the CPE, the SIL, and the EXEC in sequence. The CPE generates intent through the friction between persona and model. The SIL validates each action payload against the integrity baseline before dispatch. The EXEC materializes validated payloads as action in the host environment. The sequence is strict: no action payload reaches the EXEC without first passing SIL validation.

**Memory** is realized exclusively by the MIL. No other component stores state beyond the present cycle. The MIL's two stores — `state/` and `memory/` — are the sole source of truth for everything the entity knows and has done.

**Integrity** is realized by the SIL as its primary guardian, with the EXEC as a secondary enforcer. The SIL is the sole custodian of the Integrity Document and the first gate: it verifies that every action payload targets a skill whose manifest is registered and unaltered before dispatch. The EXEC is the second gate: it validates the manifest against its own declared rules — permissions, dependencies, and execution context — before running. Neither gate substitutes for the other; both must pass.

**Individuation** is realized once, sequentially, across all components during the FAP. The FAP is the only moment in the entity's lifecycle in which components are initialized in strict order rather than operating concurrently: structural validation precedes persona establishment, which precedes Operator enrollment, which precedes the Integrity Document generation. The result — the Imprint Record in `memory/` — is the product of all five components having completed their initialization successfully.

---
