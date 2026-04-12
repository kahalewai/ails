<div align="center">

<img width="359" height="496" alt="ails-logo2" src="https://github.com/user-attachments/assets/cc811c86-ea83-4657-ae8f-d3e289019a18" />

![Status: Released](https://img.shields.io/badge/Status-Released-brightgreen)
![Version: 1.0.0](https://img.shields.io/badge/Version-1.0.0-blue)
![License: Apache 2.0](https://img.shields.io/badge/License-Apache%202.0-green)

<br>

</div>

<br>

## Intro

AI systems produce behavior that is fundamentally different from traditional software. Inference is probabilistic. Agent execution is non-deterministic and multi-step. Tool invocations cross trust boundaries. Memory operations influence future behavior in ways that are invisible without structured telemetry. Governance and security enforcement actions generate audit obligations that existing logging formats were never designed to satisfy.

Despite this, the industry has no standardized format for recording AI system behavior. Every vendor, framework, and platform defines its own log format. There is no shared vocabulary. An inference event from one provider cannot be compared, correlated, or analyzed alongside an inference event from another without custom transformation. Agent reasoning, tool selection, memory influence, and governance decisions are either unlogged or logged in formats that resist automated analysis.

<br>

> AILS is the canonical event format for AI telemetry, from developer-facing observability to tamper-evident, hash-chained audit trails

<br>

AILS (AI Logging Standard) defines a single, unified, vendor-neutral format that records what AI systems do and how that behavior is structured. It covers inference, agent execution, tool invocation, memory access, human interaction, authority lifecycle, session governance, credential management, policy enforcement, supply chain validation, integrity verification, capability governance, control channel operations, agent-to-agent protocol interactions, and system health, all in one interoperable format.

<br>

AILS scales from lightweight developer telemetry to tamper-evident, cryptographically signed audit trails required by AI security standards and regulatory frameworks:

* EU AI Act Article 12 - provides the logging capabilities required for high-risk AI systems, including automatic event recording and retention
* NIST AI RMF MEASURE 2.6 - provides the documentation of AI system behavior required by the risk management framework
* NIST CSF 2.0 - maps to governance, detection, response, and recovery functions
* GDPR / HIPAA / SOX - provides privacy-by-construction redaction profiles and tamper-evident audit trails

<br>

AILS is complementary to OpenTelemetry. AILS defines the AI-specific semantics, what is recorded and how it is structured. OpenTelemetry provides the telemetry infrastructure, collection, correlation, and transport. AILS events map directly to OpenTelemetry spans, logs, and events without modification to either standard.

<br>
<br>

## Why AILS?

Existing logging and telemetry standards were designed for deterministic software systems and are insufficient for AI:

* **Syslog (RFC 5424)** provides unstructured text messages with no concept of AI-specific semantics, multi-step agent correlation, or tamper-evident integrity.
* **OpenTelemetry** provides excellent telemetry transport and correlation infrastructure, but its semantic conventions do not cover AI inference parameters, agent reasoning, authority lifecycle, governance enforcement, or supply chain validation.
* **Vendor-specific formats** (OpenAI usage logs, Anthropic API logs, LangSmith traces, etc.) are proprietary, incompatible, and cannot represent cross-vendor workflows.
* **Framework-specific formats** (LangChain callbacks, CrewAI logs, AutoGen traces) are bound to specific agent frameworks and cannot represent infrastructure-level or cross-framework events.

No existing format can represent the full spectrum of AI system behavior, from a simple LLM API call to a governed, multi-agent workflow operating under authority controls and session governance, in a single, unified, interoperable format.

AILS fills this gap.

<br>
<br>

## How Does AILS Work?

**Every AI event uses the same canonical envelope**

```json
{
  "ails_version": "1.0.0",
  "event_id": "a1b2c3d4-e5f6-4a7b-8c9d-0e1f2a3b4c5d",
  "event_type": "inference.call",
  "timestamp": "2026-04-11T14:30:00.123Z",
  "emitter": { "id": "llm-gateway-pod-7f8a9b", "name": "llm-gateway", "type": "gateway" },
  "actor": { "type": "agent", "id": "agent-research-001" },
  "correlation": { "trace_id": "4bf92f3577b34da6a3ce929d0e0e4736", "span_id": "00f067aa0ba902b7" },
  "data_classification": "USER_TRUSTED",
  "payload": { ... }
}
```

The envelope is invariant. Every event, inference, agent step, tool call, policy decision, security alert, carries the same identity, correlation, provenance, and integrity fields. Variation between event types lives exclusively in the `payload`.

<br>

**Provenance is mandatory on every event**

Every AILS event carries a `data_classification` tag: `SYSTEM_TRUSTED`, `USER_TRUSTED`, or `EXTERNAL_UNTRUSTED`. Downstream systems can filter, route, and evaluate events based on trust level without inspecting payloads. When inputs of mixed trust contribute to an event, the least trusted classification wins.

<br>

**Correlation is first-class**

Every event carries W3C Trace Context-compatible `trace_id` and `span_id` fields. Multi-step agent workflows, cross-service interactions, and end-to-end request chains are reconstructable from AILS events using standard correlation fields alone, no payload inspection required.

<br>

**Integrity is graduated**

| Level | Mechanism | Use Case |
|---|---|---|
| **Level 1** | Structured telemetry, no integrity overhead | Developer observability, debugging, cost tracking |
| **Level 2** | Sequence numbering + chain hashing | Enterprise audit trails, compliance logging, governance records |
| **Level 3** | Level 2 + cryptographic event signing (Ed25519 / ES256) | Regulatory compliance, forensic evidence, high-risk AI system audit |

A Level 1 event requires zero cryptographic dependencies. Level 2 adds tamper-evidence. Level 3 adds non-repudiation. Levels are cumulative.

<br>

**Privacy is built in by construction**

All prompt content, completion content, reasoning chains, PII, and tool arguments are classified as sensitive by default and are redactable. A fully redacted AILS event remains valid, parseable, and correlatable. Credential material is absolutely prohibited — there is no configuration option that enables it.

<br>
<br>

## What AILS Defines

The AILS specification defines:

* Canonical event envelope - a uniform outer structure for all AI telemetry events providing identity, correlation, provenance, timestamps, and integrity fields consistent across all event types
* 73 structured event types across 15 categories spanning inference, agent execution, tool invocation, memory access, human interaction, authority lifecycle, session governance, credential management, policy enforcement, supply chain validation, integrity verification, capability governance, control channel operations, agent-to-agent protocol interactions, and system health
* Graduated integrity model - three conformance levels scaling from lightweight telemetry (Level 1) to tamper-evident hash-chained audit trails (Level 2) to cryptographically signed events (Level 3)
* Provenance classification system - mandatory source trust tagging on every event (`SYSTEM_TRUSTED`, `USER_TRUSTED`, `EXTERNAL_UNTRUSTED`)
* Privacy and redaction framework - four standard redaction profiles (`default`, `minimal`, `strict`, `regulatory`) with configurable field-level redaction
* Retention requirements - three retention tiers (Standard: 90 days, Governance: 3 years, Regulatory: 10 years)
* Multi-tenant isolation - tenant boundary enforcement for shared infrastructure deployments
* Mandatory alert conditions - 18 defined alert patterns that implementations must detect and surface
* Cross-standard integration - event types satisfying the audit requirements of DAE, AGBAC, ALS, AI-SCS, ACS, the MCP Integrity Standard, and A2A
* OpenTelemetry mapping - informative mapping of AILS events to OpenTelemetry spans, logs, and metrics
* OCSF mapping - informative mapping to the Open Cybersecurity Schema Framework for SIEM integration
* MITRE ATLAS mapping - detection patterns for AI-specific adversary techniques
* Sigma rule templates - ready-to-convert detection rules for all mandatory alert conditions
* Webhook payload specification - standardized alert notification format for incident management platforms

<br>
<br>

## What AILS Does NOT Do

Understanding the boundary is as important as understanding the capability:

* AILS does not define transport. Events may be transported via OpenTelemetry, HTTP, message queues, file systems, or any other mechanism.
* AILS does not define storage. Events may be stored in log aggregation systems, databases, object storage, or any other backend.
* AILS does not define dashboards or alerting platforms. These are downstream consumers of AILS events.
* AILS does not define how AI systems operate. It defines how their behavior is recorded.
* AILS does not make policy or compliance decisions. Those belong to policy engines and governance frameworks.
* AILS does not evaluate or classify AI content. It records that inference occurred and what parameters were used.

<br>
<br>

## Event Taxonomy

AILS defines 73 event types across 15 categories:

| Category | Events | Scope |
|---|---|---|
| `inference.*` | 7 | LLM and model inference, embeddings, guardrails, routing, structured output |
| `agent.*` | 5 | Agent lifecycle, reasoning steps, delegation, loop checkpoints, workflow nodes |
| `tool.*` | 4 | Tool invocations, discovery, MCP resources, MCP sampling |
| `memory.*` | 2 | Memory/context reads and writes, RAG retrieval |
| `human.*` | 3 | Human feedback, confirmation, escalation response |
| `authority.*` | 6 | Authority token lifecycle, enforcement gate pass/deny |
| `session.*` | 4 | Session initialization, state transitions, termination |
| `credential.*` | 4 | Credential issuance, renewal, refusal, expiry |
| `policy.*` | 3 | Authorization decisions, policy violations, escalations |
| `supplychain.*` | 4 | ABOM validation, artifact verification, trust assertions |
| `integrity.*` | 5 | Manifest verification, fingerprint checking, TOFU operations |
| `capability.*` | 7 | Capability index delivery, PAEP evaluation, execution contracts |
| `control.*` | 11 | Control channel commands, watchdog, push notifications, replay detection |
| `a2a.*` | 6 | A2A task lifecycle, message exchange, Agent Card discovery, authentication |
| `system.*` | 7 | Alerts, health checks, configuration changes, budget/quota tracking |

All event types are designated `stable` in this version. Custom events use the `x-` prefix with reverse-domain namespace.

<br>
<br>

## Conformance Levels

AILS defines three cumulative conformance levels:

<br>

**Level 1 - Structured Telemetry**

Emit valid AILS events with correct envelopes, provenance classification, default redaction, and 90-day retention. No integrity overhead required. No cryptographic dependencies.

> *Permitted claim: "This implementation emits AILS-conformant structured AI telemetry (Level 1)"*

<br>

**Level 2 - Governed Audit**

Level 1 plus: tamper-evident event streams via chain hashing and sequence numbering, 3-year retention for audit events, all 18 mandatory alert conditions, multi-tenant isolation, append-only or hash-chain-verified storage, and on-demand integrity verification.

> *Permitted claim: "AILS tamper-evident audit logging is in effect. This implementation satisfies the audit logging requirements of [DAE/AGBAC/ALS/AI-SCS/ACS/MCP Integrity Standard]."*

<br>

**Level 3 - Certified Audit**

Level 2 plus: cryptographic event signing (Ed25519 or ES256), 10-year regulatory retention, all four standard redaction profiles, and signature verification on demand.

> *Permitted claim: "AILS events are cryptographically signed. This implementation satisfies EU AI Act Article 12 logging requirements via AILS."*

<br>
<br>

## Cross-Standard Integration

AILS serves as the unified audit logging layer for an ecosystem of AI security standards. When an implementation of any of these standards emits AILS-conformant events, those events are structurally compatible, correlatable, and analyzable using a single set of tools.

| Standard | AILS Role | Event Types Required |
|---|---|---|
| **DAE** (Deterministic Agent Execution) | Authority lifecycle, state machine transitions, enforcement gate events | 11 |
| **AGBAC** (Agent-Based Access Control) | Dual-subject authorization decisions, delegation token lifecycle | 4 |
| **ALS** (Agent Layer Security) | Session governance, credential lifecycle, control channel, watchdog events | 16 |
| **AI-SCS** (AI Supply Chain Standard) | ABOM validation, artifact integrity, trust assertion verification | 4 |
| **ACS** (Agent Capability Standard) | PAEP authorization, capability selection, execution contract lifecycle | 10 |
| **MCP Integrity Standard** | Manifest verification, interface fingerprinting, trust level determination | 5 |
| **A2A** (Agent-to-Agent Protocol) | Task lifecycle, message exchange, Agent Card discovery, authentication | 6 |

**All standards combined: 42 event types** (with deduplication of shared types).

Cross-standard conformance claims specify both the AILS level and the external standards satisfied:

```
"AILS v1.0.0 Level 2 Conformant; satisfies audit requirements of ALS v2.0.0 and ACS v1.0.0"
```

<br>
<br>

## Regulatory Compliance

AILS was designed to satisfy the logging and audit requirements of current AI regulation:

<br>

**EU AI Act (Regulation 2024/1689)**

| AILS Element | EU AI Act Requirement |
|---|---|
| Event Envelope (§5) | Article 12 - logging capabilities |
| Conformance Level 3 (§28.4) | Article 12 - automatic recording of events |
| Retention - Regulatory tier (§25) | Article 12 - retention for the period appropriate to the intended purpose |
| Provenance classification (§23) | Article 14 - human oversight, understanding of system behavior |
| Redaction profiles (§24) | Article 10 - data governance, privacy by design |
| Alert conditions (§27) | Article 9 - risk management, anomaly detection |

<br>

**NIST AI Risk Management Framework (AI RMF 1.0)**

| AILS Element | NIST AI RMF Function |
|---|---|
| Event taxonomy (§6) | MEASURE 2.6 - AI system performance and behavior documentation |
| Provenance (§23) | MAP 5.1 - data provenance |
| Alert conditions (§27) | MANAGE 2.2 - response mechanisms |
| Integrity (§22) | GOVERN 2.1 - accountability mechanisms |
| Retention (§25) | MEASURE 4.1 - post-deployment monitoring |

<br>

**NIST Cybersecurity Framework (CSF) 2.0**

| AILS Element | CSF Function |
|---|---|
| Supply chain events (§16), Tool discovery (§9.3) | IDENTIFY - Asset Management |
| Policy events (§15), Authority events (§12), Credential events (§14) | PROTECT - Access Control |
| Alert conditions (§27), Policy violations (§15.3) | DETECT - Adverse Event Analysis |
| Control events (§19), Session events (§13) | RESPOND - Mitigation |

<br>

**Additional Frameworks**

AILS also maps to GDPR (Articles 5, 25, 30, 35), SOX (Sections 302, 404, 802), HIPAA (Security Rule §164.312), and ISO/IEC 42001 (AI Management System). A full regulatory compliance matrix is available in Appendix F of the specification.

<br>
<br>

## Security Tool Integration

AILS events are designed for consumption by security operations infrastructure:

<br>

**OCSF (Open Cybersecurity Schema Framework)**

AILS events map to OCSF categories and classes, enabling native ingestion by OCSF-aware platforms including AWS Security Lake, Splunk, CrowdStrike, IBM QRadar, and others. Mapping defined in Appendix G.

<br>

**MITRE ATLAS**

AILS event patterns map to MITRE ATLAS adversary techniques for AI systems, enabling detection of prompt injection (AML.T0051), ML supply chain compromise (AML.T0010), LLM plugin compromise (AML.T0056), jailbreak attempts (AML.T0054), and other AI-specific attack techniques. Mapping defined in Appendix H.

<br>

**Sigma Detection Rules**

AILS provides ready-to-convert Sigma rule templates for all mandatory alert conditions and key ATLAS-mapped detection patterns. Convert to Splunk SPL, Elastic KQL, Microsoft Sentinel KQL, or Chronicle YARA-L using standard Sigma tooling. Templates defined in Appendix I.

<br>

**Webhook Notifications**

AILS defines a standardized webhook payload format for alert delivery to PagerDuty, OpsGenie, Slack, Microsoft Teams, and custom endpoints. Specification in Appendix J.

<br>
<br>

## Relationship to OpenTelemetry

AILS is complementary to OpenTelemetry, not a replacement:

* **AILS** defines AI-specific semantics, the meaning and structure of AI telemetry events
* **OpenTelemetry** provides telemetry infrastructure, collection, correlation, export, and transport

AILS events may be:

* Exported as OpenTelemetry log records with AILS-defined attributes
* Mapped to OpenTelemetry spans for latency and dependency analysis
* Emitted as OpenTelemetry span events within existing trace contexts
* Exported independently of OpenTelemetry via any transport

AILS correlation fields (`trace_id`, `span_id`) are format-compatible with W3C Trace Context and OpenTelemetry identifiers, ensuring zero-transformation interoperability. The complete mapping is defined in Appendix C of the specification.

<br>
<br>

## View the AILS Standard

For the complete formal specification, including all 73 event type definitions, JSON Schema, conformance requirements, and appendices:

[AILS-Standard-v1.0.0.md](https://github.com/kahalewai/ails/blob/main/spec/ails_specification.md)

**Status:** Released

**Version:** v1.0.0

**License:** Apache License 2.0

**Date:** 2026-04-10

**Author:** Shawn Kahalewai Reilly

<br>
<br>

## Design Principles

AILS is built on ten normative design principles that serve as authoritative tie-breakers when requirements are ambiguous:

| Principle | Summary |
|---|---|
| **DP-1 - Semantics Over Transport** | AILS defines what is recorded, not how it is transmitted |
| **DP-2 - Single Envelope, All Event Types** | One canonical envelope for every event, from inference calls to security audit records |
| **DP-3 - Graduated Integrity** | Three levels: structured telemetry → tamper-evident → cryptographically signed |
| **DP-4 - Privacy By Construction** | All AI content is sensitive by default and redactable without breaking event validity |
| **DP-5 - Provenance Is Mandatory** | Every event carries a trust classification; no exceptions |
| **DP-6 - Correlation Is First-Class** | Trace ID, span ID, and session ID on every event; workflows reconstructable without payload inspection |
| **DP-7 - Vendor Neutrality** | No vendor, model provider, framework, or platform is referenced, required, or privileged |
| **DP-8 - Cross-Standard Unification** | Event types satisfy audit requirements across DAE, AGBAC, ALS, AI-SCS, ACS, and MCP Integrity |
| **DP-9 - Backward-Compatible Evolution** | Additive schema changes; consumers ignore unrecognized fields; no breaking changes without major version |
| **DP-10 - Fail-Safe Logging** | Logging failure never causes AI system failure at Level 1; buffering and alerting at Level 2+ |

<br>
<br>

## Versioning

AILS follows Semantic Versioning 2.0.0 (`MAJOR.MINOR.PATCH`). The current version is `1.0.0`.

* **MAJOR** - breaking changes (removing required fields, renaming stable event types, changing hash algorithms)
* **MINOR** - backward-compatible additions (new event types, new categories, new optional fields)
* **PATCH** - clarifications, typographical corrections, informative reference updates

Consumers must ignore unrecognized fields and must accept events from later minor versions within the same major version. Event types follow a lifecycle: Experimental → Stable → Deprecated → Removed (only on major increment).

<br>
<br>

## Licensing

The AILS specification and all reference materials are released under the **Apache License, Version 2.0**. The standard is open, royalty-free, and vendor-neutral. Organizations are encouraged to adopt, implement, reference, and build upon it.

<br>
<br>

## Community and Collaboration

AILS is an open initiative. The community's input will shape its evolution.

The project welcomes:

* **Feedback on the specification** - technical review, edge case analysis, implementation experience
* **Cross-standard integration testing** - implementations of DAE, AGBAC, ALS, AI-SCS, ACS, and MCP Integrity emitting AILS events
* **Reference implementations** - AILS SDKs, emitter libraries, conformance validators
* **Conformance test contributions** - help build and extend the test suite across all 14 test categories
* **SIEM and security platform integrations** - OCSF transformers, Sigma rule repositories, SOAR playbooks
* **OpenTelemetry integration** - AILS-to-OTEL exporters and semantic convention proposals
* **Regulatory and compliance analysis** - additional framework mappings, jurisdiction-specific guidance
* **Ecosystem tooling** - dashboards, visualization tools, chain hash verifiers, event stream analyzers

The goal is to establish AILS as the canonical event format for AI system observability and audit, so that every AI vendor, framework, security tool, and compliance platform speaks the same structured language for recording AI behavior.

<br>
