# AI Logging Standard (AILS)

### Begin AI Logging Standard

<br>

**Title:** AI Logging Standard (AILS)

**Status:** RELEASED

**Date:** 2026-04-10

**Authors:** Shawn Kahalewai Reilly

**Version:** 1.0.0

**License:** Apache License, Version 2.0

<br>

### ABSTRACT

The industry lacks a standardized, vendor-neutral format for recording
AI system behavior across inference, agent execution, tool invocation,
memory access, governance enforcement, and security lifecycle events.
AI Logging Standard (AILS) fills this gap by defining a canonical event
envelope, a structured event taxonomy, graduated integrity mechanisms,
and conformance levels that serve both general-purpose AI observability
and security-critical audit requirements. AILS operates as a semantic
standard defining what is recorded and how it is structured; it does not
mandate transport mechanisms, storage backends, or observability
platforms. AILS provides a single unified format that scales from
developer-facing LLM telemetry to tamper-evident, hash-chained audit
trails required by AI security standards including DAE, AGBAC, ALS,
AI-SCS, ACS, and the MCP Integrity Standard, and protocol-level
telemetry for agent communication protocols including MCP and A2A. AILS is complementary to
OpenTelemetry; AILS events may be mapped to OpenTelemetry spans, logs,
and events without modification to either standard.

<br>

### TABLE OF CONTENTS

1. Introduction
2. Glossary
3. Normative Language and Conventions
4. Design Principles
5. Event Envelope Specification
6. Event Taxonomy
7. Category: Inference Events
8. Category: Agent Events
9. Category: Tool Events
10. Category: Memory Events
11. Category: Human Interaction Events
12. Category: Authority Events
13. Category: Session Events
14. Category: Credential Events
15. Category: Policy Events
16. Category: Supply Chain Events
17. Category: Integrity Events
18. Category: Capability Events
19. Category: Control Events
20. Category: Agent-to-Agent Protocol Events
21. Category: System Events
22. Integrity and Tamper-Evidence Requirements
23. Provenance and Data Classification
24. Privacy, Redaction, and Sensitive Data
25. Retention Requirements
26. Multi-Tenant Isolation
27. Alert Condition Bindings
28. Conformance
29. Relationship to Other Standards
30. Versioning and Standard Evolution
- Appendix A: Event Envelope Schema (Normative)
- Appendix B: Event Type Registry (Normative)
- Appendix C: OpenTelemetry Mapping (Informative)
- Appendix D: Redaction Profiles (Informative)
- Appendix E: Cross-Standard Event Matrix (Informative)
- Appendix F: Regulatory Compliance Matrix (Informative)
- Appendix G: OCSF Mapping (Informative)
- Appendix H: MITRE ATLAS Mapping (Informative)
- Appendix I: Sigma Detection Rule Templates (Informative)
- Appendix J: Webhook Payload Specification (Informative)
- Appendix K: Complete Event Examples (Informative)
- Appendix L: References (Normative / Informative)

<br>

## SECTION 1: INTRODUCTION

*Informative*

### 1.1 Background

Artificial intelligence systems produce behavior that is fundamentally
different from traditional software. Inference is probabilistic. Agent
execution is non-deterministic and multi-step. Tool invocations cross
trust boundaries. Memory operations influence future behavior in ways
that are invisible without structured telemetry. Governance and security
enforcement actions generate audit obligations that existing logging
formats were not designed to satisfy.

Despite this, AI systems are currently observed using general-purpose
application logs, vendor-specific dashboards, framework-specific tracing
tools, and ad hoc JSON structures that vary across every vendor,
framework, and deployment. The consequences are measurable:

- **Fragmentation.** Every AI vendor, framework, and platform defines
  its own log format. There is no shared vocabulary for describing AI
  behavior. An LLM inference event from one provider cannot be compared,
  correlated, or analyzed alongside an inference event from another
  without custom transformation.

- **Opacity.** Agent reasoning, tool selection, memory influence, and
  governance decisions are either unlogged or logged in formats that
  resist automated analysis. Debugging, auditing, and forensic
  investigation of AI behavior require manual reconstruction from
  incomplete data.

- **Insufficient audit capability.** AI security standards — including
  Deterministic Agent Execution (DAE), Agent-Based Access Control
  (AGBAC), Agent Layer Security (ALS), AI Supply Chain Standard (AI-SCS),
  Agent Capability Standard (ACS), and the MCP Integrity Standard —
  each independently define audit event requirements. Without a shared
  logging standard, implementations produce incompatible audit formats
  that cannot be correlated, verified, or analyzed across standard
  boundaries.

- **Regulatory gap.** The EU AI Act (Regulation 2024/1689) Article 12
  requires logging capabilities for high-risk AI systems. NIST AI RMF
  MEASURE 2.6 requires documentation of AI system behavior. No existing
  standard defines what these logs should contain for AI-specific
  operations.

<br>

### 1.2 Problem Statement

The industry is missing a canonical format for AI telemetry. When an AI
system performs an inference, selects a tool, executes an agent step,
reads from memory, enforces a policy, issues an authority token, manages
a session credential, or validates a supply chain artifact, there is no
standardized, structured, interoperable format in which to record that
event.

Existing logging and telemetry standards are insufficient because they
were designed for deterministic software systems:

- **Syslog (RFC 5424)** provides unstructured or semi-structured text
  messages. It has no concept of AI-specific semantics, correlation
  across multi-step agent workflows, or tamper-evident integrity.

- **OpenTelemetry** provides excellent telemetry transport, correlation,
  and instrumentation infrastructure. However, OpenTelemetry defines
  how telemetry is collected and transmitted, not what AI-specific
  semantics should be recorded. OpenTelemetry's semantic conventions
  do not cover AI inference parameters, agent reasoning, authority
  lifecycle, governance enforcement, or supply chain validation.

- **Vendor-specific formats** (OpenAI usage logs, Anthropic API logs,
  LangSmith traces, etc.) capture vendor-specific details but are
  proprietary, incompatible, and cannot represent cross-vendor or
  cross-framework workflows.

- **Framework-specific formats** (LangChain callbacks, CrewAI logs,
  AutoGen traces) are bound to specific agent frameworks and cannot
  represent infrastructure-level events, security governance events,
  or cross-framework agent interactions.

None of these formats can represent the full spectrum of AI system
behavior — from a simple LLM API call to a governed, multi-agent
workflow operating under DAE authority controls and ALS session
governance — in a single, unified, interoperable format.

<br>

### 1.3 What This Standard Provides

The AI Logging Standard (AILS) defines:

1. A **canonical event envelope** — a uniform outer structure for all AI
   telemetry events, providing identity, correlation, provenance,
   timestamps, and integrity fields that are consistent regardless of
   event type.

2. A **structured event taxonomy** — a comprehensive, namespaced
   classification of AI event types spanning inference, agent execution,
   tool invocation, memory access, human interaction, authority
   lifecycle, session governance, credential management, policy
   enforcement, supply chain validation, integrity verification,
   capability governance, control channel operations, and system
   health.

3. A **graduated integrity model** — three conformance levels that scale
   from lightweight developer telemetry (Level 1) to tamper-evident,
   hash-chained, signed audit trails (Level 3) suitable for regulatory
   compliance and security-critical deployments.

4. A **provenance classification system** — mandatory source trust
   tagging on every event, enabling downstream systems to distinguish
   events originating from system-trusted, user-trusted, and
   external-untrusted sources.

5. A **privacy and redaction framework** — normative requirements for
   handling sensitive data including prompts, completions, personally
   identifiable information, credentials, and chain-of-thought
   reasoning, with configurable redaction profiles.

6. A **cross-standard integration model** — normative event type
   definitions that satisfy the audit requirements of DAE, AGBAC, ALS,
   AI-SCS, ACS, and the MCP Integrity Standard, enabling implementations
   of those standards to emit conformant AILS events rather than
   defining independent audit formats.

<br>

### 1.4 What This Standard Does Not Provide

- **Transport mechanism.** AILS does not define how events are
  transmitted. Events may be transported via OpenTelemetry, HTTP,
  message queues, file systems, or any other mechanism.

- **Storage backend.** AILS does not define where events are stored.
  Events may be stored in log aggregation systems, databases, object
  storage, or any other backend.

- **Observability platform.** AILS does not define dashboards,
  alerting systems, or analysis tools. These are downstream consumers
  of AILS events.

- **AI runtime or framework.** AILS does not define how AI systems
  operate. It defines how their behavior is recorded.

- **Policy or compliance decisions.** AILS does not determine what
  constitutes a policy violation, what actions should be taken in
  response to events, or what compliance requirements apply to a
  deployment. These decisions belong to policy engines, governance
  frameworks, and the standards that AILS supports.

- **Content analysis.** AILS does not evaluate, score, or classify
  the content of AI inputs or outputs. It records that inference
  occurred and what parameters were used.

<br>

### 1.5 Relationship to OpenTelemetry

AILS is complementary to OpenTelemetry. The relationship is:

- AILS defines **AI-specific semantics** — the meaning and structure of
  AI telemetry events.
- OpenTelemetry provides **telemetry infrastructure** — collection,
  correlation, export, and transport.

AILS events MAY be:

- Exported as OpenTelemetry log records with AILS-defined attributes
- Mapped to OpenTelemetry spans for latency and dependency analysis
- Emitted as OpenTelemetry span events within existing trace contexts
- Exported independently of OpenTelemetry via any transport

The mapping between AILS events and OpenTelemetry constructs is defined
in Appendix C. This mapping is informative; AILS does not require
OpenTelemetry adoption.

<br>

### 1.6 Relationship to AI Security Standards

AILS is designed to serve as the audit and logging layer for the
following AI security standards:

| Standard | AILS Role |
|----------|-----------|
| DAE (Deterministic Agent Execution) | Authority lifecycle, state machine transitions, enforcement gate events |
| AGBAC (Agent-Based Access Control) | Dual-subject authorization decisions, delegation token lifecycle |
| ALS (Agent Layer Security) | Session governance, credential lifecycle, control channel, watchdog events |
| AI-SCS (AI Supply Chain Standard) | ABOM validation, artifact integrity, trust assertion verification |
| ACS (Agent Capability Standard) | PAEP authorization, CID selection, execution contract lifecycle |
| MCP Integrity Standard | Manifest verification, interface fingerprinting, trust level determination |
| A2A (Agent-to-Agent Protocol) | Task lifecycle, message exchange, Agent Card discovery, authentication |

Each of these standards defines audit requirements. AILS provides the
unified format in which those audit events are recorded. When an
implementation of any of these standards emits audit events in AILS
format, the events are structurally compatible, correlatable, and
analyzable using a single set of tools.

<br>

### 1.7 Document Conventions

This document uses RFC 2119 normative language (BCP 14, updated by RFC
8174) as defined in Section 3. Normative sections contain requirements
affecting conformance. Informative sections contain guidance that does
not affect conformance.

<br>

## SECTION 2: GLOSSARY

*Normative*

The following terms are used normatively throughout this standard. All
terms are defined before first normative use.

**AILS (AI Logging Standard):** The standard defined by this document.

**AILS Event:** A single structured record conforming to the AILS Event
Envelope specification (Section 5), representing one unit of AI system
behavior, decision, or state change.

**AILS Event Envelope:** The canonical outer structure of an AILS Event,
containing identity, correlation, provenance, timestamp, integrity, and
payload fields. Defined in Section 5.

**Actor:** The entity that initiated or caused an AILS Event. An actor
is classified as one of: `agent`, `human`, `system`, or `external`.

**Agent Identity:** A first-class, non-human, digital identity
representing an AI system, model, agent, or automation that produces
or triggers AILS Events.

**Alert Condition:** A defined pattern of one or more AILS Events that,
when detected, requires notification to operators or security systems.
Defined in Section 27.

**Audit Event:** An AILS Event produced to satisfy a governance, security,
or compliance requirement. Audit Events are subject to the integrity and
retention requirements of the conformance level under which they are
emitted.

**Chain Hash:** A cryptographic value included in each AILS Event at
Level 2 and above that incorporates a hash of the immediately preceding
AILS Event within the same event stream, enabling tamper detection across
the event sequence. Defined in Section 22.

**Conformance Level:** One of three defined tiers of AILS compliance
(Level 1, Level 2, Level 3) as defined in Section 28. Levels are
cumulative.

**Correlation Context:** The set of identifiers — including trace ID,
span ID, parent span ID, and session ID — that link an AILS Event to
other events in the same workflow, session, or transaction.

**Data Classification:** The provenance designation assigned to the
source of an AILS Event: `SYSTEM_TRUSTED`, `USER_TRUSTED`, or
`EXTERNAL_UNTRUSTED`. Defined in Section 23.

**Emitter:** Any software component that produces AILS Events. Emitters
include application code, AI frameworks, agent runtimes, security
enforcement points, infrastructure gateways, and proxy services.

**Event Category:** The top-level classification of an AILS Event type,
represented by the namespace prefix of the `event_type` field (e.g.,
`inference`, `agent`, `tool`, `authority`, `session`).

**Event Payload:** The event-type-specific data carried within an AILS
Event, defined by the event type specification for each event in the
AILS event taxonomy.

**Event Stream:** An ordered sequence of AILS Events sharing a common
scope — typically a session, a trace, or a tenant partition — within
which chain hashing and sequence numbering are maintained.

**Event Type:** The namespaced identifier of an AILS Event, consisting
of a category prefix and a specific event name separated by a period
(e.g., `inference.call`, `agent.step`, `authority.token_issued`).

**Human Principal:** A human user whose actions, instructions, or
feedback are recorded in AILS Events.

**Integrity Envelope:** The set of fields within an AILS Event that
provide tamper-evidence, including `sequence_number`, `chain_hash`,
and `event_signature`. Required at Level 2 and above. Defined in
Section 22.

**Log Signing Key:** A cryptographic key used to sign individual AILS
Events at Level 3. The signing key is held by the Emitter or a
designated signing service.

**Provenance Tag:** The `data_classification` field within an AILS Event
Envelope that identifies the trust level of the event source. Defined
in Section 23.

**Redaction Profile:** A named configuration defining which AILS Event
fields are redacted, hashed, truncated, or omitted for a given
deployment context. Defined in Section 24 and Appendix D.

**Retention Policy:** The minimum duration for which AILS Events of a
given category, conformance level, or regulatory classification must
be retained. Defined in Section 25.

**Sequence Number:** A monotonically increasing integer assigned to each
AILS Event within an event stream, enabling ordering, gap detection,
and replay verification. Required at Level 2 and above.

**Session ID:** An identifier linking AILS Events to a specific
interaction session, agent session, or governance session. Carried
within the Correlation Context.

**Span ID:** A unique identifier for a single unit of work within a
trace, compatible with OpenTelemetry span identifiers.

**Tamper-Evident Log:** An AILS Event log where unauthorized
modification, insertion, or deletion is detectable via chain hashing,
append-only storage, or event signing.

**Telemetry Event:** An AILS Event produced for observability,
debugging, or performance analysis purposes, as distinguished from an
Audit Event produced for governance or compliance purposes. Both use
the same AILS Event Envelope.

**Tenant ID:** An identifier isolating AILS Events to a specific
organizational tenant in multi-tenant deployments. Defined in
Section 26.

**Trace ID:** A unique identifier for an end-to-end workflow or request,
compatible with OpenTelemetry trace identifiers. Shared across all AILS
Events within a single workflow.

<br>

## SECTION 3: NORMATIVE LANGUAGE AND CONVENTIONS

*Normative*

### 3.1 Normative Language Policy

This standard uses RFC 2119 (BCP 14) normative keywords as updated by
RFC 8174:

- **MUST / SHALL:** Absolute requirement. Non-compliance renders the
  implementation non-conformant at the applicable conformance level.
- **MUST NOT / SHALL NOT:** Absolute prohibition.
- **SHOULD / RECOMMENDED:** Strong preference. Deviation requires
  documented justification and MUST NOT weaken any MUST requirement.
- **MAY / OPTIONAL:** Permitted but not required.

The word "required" in prose uses its ordinary English meaning and is
not a normative keyword unless rendered in all capitals.

<br>

### 3.2 Requirement Identifier Format

Normative requirements are identified with the prefix `REQ-` followed
by the section number and a sequence number:

```
REQ-{section}.{sequence}
```

Example: `REQ-5.1.1` is the first requirement in Section 5.1.

<br>

### 3.3 Conformance Level Notation

Requirements that apply only at specific conformance levels are
annotated as follows:

- Requirements with no level annotation apply at all conformance levels.
- Requirements annotated `[Level 2+]` apply at Level 2 and Level 3.
- Requirements annotated `[Level 3]` apply at Level 3 only.

<br>

### 3.4 Document Conventions

- Field names within the AILS Event Envelope and event payloads are
  rendered in `snake_case` and monospace.
- Event type identifiers are rendered in `category.event_name` format.
- Component names are rendered in Title Case when referenced normatively.
- Informative passages within normative sections are prefixed
  `[INFORMATIVE]`.
- Non-normative examples are prefixed `[EXAMPLE]`.

<br>

### 3.5 JSON Conventions

All AILS Events MUST be serializable as JSON objects conforming to
RFC 8259. Field names MUST be encoded as UTF-8 strings. Timestamp values
MUST use ISO 8601 format with UTC timezone designator and millisecond
precision (e.g., `2026-04-10T14:30:00.000Z`). UUID values MUST conform
to RFC 9562 (UUID version 4 or version 7).

<br>

## SECTION 4: DESIGN PRINCIPLES

*Normative*

The following principles are normative. Any implementation decision
conflicting with these principles is non-conformant regardless of
whether a specific requirement addresses the conflict. When requirements
are ambiguous, these principles are the authoritative tie-breakers.

<br>

**DP-1 — SEMANTICS OVER TRANSPORT**

AILS defines what is recorded and how it is structured. AILS SHALL NOT
mandate transport mechanisms, wire protocols, or delivery infrastructure.
Transport is an implementation concern. AILS events MAY be transported
via OpenTelemetry, HTTP, message queues, file systems, Unix sockets, or
any other mechanism that preserves the structural integrity of the event
envelope.

<br>

**DP-2 — SINGLE ENVELOPE, ALL EVENT TYPES**

Every AILS Event — from a simple LLM inference call to a tamper-evident
security audit record — SHALL use the same canonical Event Envelope. The
envelope is invariant across event categories, conformance levels, and
deployment contexts. Variation between event types is expressed
exclusively in the event payload, never in the envelope structure.

<br>

**DP-3 — GRADUATED INTEGRITY**

AILS SHALL support three conformance levels with increasing integrity
guarantees. Level 1 provides structured telemetry with no integrity
overhead. Level 2 adds tamper-evidence via chain hashing and sequence
numbering. Level 3 adds cryptographic event signing. An implementation
MUST be able to emit Level 1 events with no cryptographic dependencies.
Higher integrity levels are additive, not replacements.

<br>

**DP-4 — PRIVACY BY CONSTRUCTION**

AILS SHALL treat all AI input content (prompts, messages, instructions),
AI output content (completions, responses, reasoning), personally
identifiable information, and credentials as sensitive by default.
Sensitive fields SHALL be redactable, hashable, or omittable without
breaking event envelope validity. A conformant AILS Event with all
sensitive fields redacted MUST remain a valid, parseable, correlatable
event.

<br>

**DP-5 — PROVENANCE IS MANDATORY**

Every AILS Event SHALL carry a provenance tag classifying the trust
level of the event source. This classification — SYSTEM_TRUSTED,
USER_TRUSTED, or EXTERNAL_UNTRUSTED — is a required envelope field, not
an optional annotation. Downstream systems MUST be able to filter,
route, and evaluate events based on provenance without inspecting
payloads.

<br>

**DP-6 — CORRELATION IS FIRST-CLASS**

AILS SHALL provide mandatory correlation fields — trace ID, span ID,
and session ID — in every event envelope. Multi-step agent workflows,
cross-service interactions, and end-to-end request chains MUST be
reconstructable from AILS events using standard correlation fields
alone, without requiring payload inspection or external metadata.

<br>

**DP-7 — VENDOR NEUTRALITY**

AILS SHALL NOT reference, require, or privilege any specific AI vendor,
model provider, agent framework, or observability platform. Vendor-
specific data MAY appear in event payloads but SHALL NOT be required by
the event envelope or by any normative event type definition.

<br>

**DP-8 — CROSS-STANDARD UNIFICATION**

AILS SHALL define event types sufficient to satisfy the audit
requirements of DAE, AGBAC, ALS, AI-SCS, ACS, and the MCP Integrity
Standard. When an implementation of any of these standards emits AILS-
conformant events, those events SHALL be structurally compatible with
events from any other AILS-conformant implementation, regardless of
which standard triggered the event.

<br>

**DP-9 — BACKWARD-COMPATIBLE EVOLUTION**

Schema evolution MUST be additive wherever possible. New fields MAY be
added to the event envelope or to event payloads in minor versions.
Required fields MUST NOT be removed or renamed without a major version
increment. Consumers of AILS events MUST ignore unrecognized fields.
Emitters MUST NOT reject events containing fields not defined in their
implemented version of the standard.

<br>

**DP-10 — FAIL-SAFE LOGGING**

AILS implementations SHALL NOT cause AI system failure when logging
infrastructure is degraded or unavailable. At Level 1, logging failure
SHALL be non-blocking. At Level 2 and above, implementations SHOULD
buffer events during transient logging failures and MUST emit a system
alert event when logging degradation is detected. Only when a specific
security standard (e.g., ALS, ACS) mandates fail-closed behavior on
audit failure does logging failure become blocking — and that behavior
is governed by the security standard, not by AILS itself.

<br>

## SECTION 5: EVENT ENVELOPE SPECIFICATION

*Normative*

### 5.1 Purpose

The Event Envelope is the canonical outer structure of every AILS Event.
It provides identity, typing, timestamps, correlation, provenance,
actor attribution, integrity, and a payload container. The envelope is
invariant across all event categories and conformance levels. All
variation between event types is expressed in the `payload` field.

<br>

### 5.2 Envelope Structure

**REQ-5.2.1:** Every AILS Event MUST be a JSON object conforming to the
envelope structure defined in this section.

**REQ-5.2.2:** The AILS Event Envelope MUST contain the following
top-level fields:

| Field | Type | Required | Description |
|---|---|---|---|
| `ails_version` | string | MUST | Version of the AILS standard under which this event was emitted. Semantic version format (e.g., `"1.0.0"`). |
| `event_id` | string | MUST | Globally unique identifier for this event. UUID v4 or v7. |
| `event_type` | string | MUST | Namespaced event type identifier (e.g., `inference.call`, `authority.token_issued`). |
| `timestamp` | string | MUST | ISO 8601 UTC timestamp with millisecond precision indicating when the event occurred. |
| `emitter` | object | MUST | Identity of the software component that produced this event. See Section 5.4. |
| `actor` | object | MUST | Identity of the entity whose action caused this event. See Section 5.5. |
| `correlation` | object | MUST | Trace, span, session, and parent identifiers for event correlation. See Section 5.6. |
| `data_classification` | string | MUST | Provenance trust level of the event source. See Section 5.7. |
| `payload` | object | MUST | Event-type-specific data. Structure defined by each event type specification. |
| `context` | object | OPTIONAL | Deployment and environmental context. See Section 5.8. |
| `metadata` | object | OPTIONAL | Non-semantic operational data. See Section 5.9. |
| `integrity` | object | CONDITIONAL | Tamper-evidence and signing fields. MUST be present at Level 2 and above. See Section 5.10. |
| `tenant` | object | OPTIONAL | Multi-tenant isolation fields. See Section 5.11. |
| `redaction` | object | OPTIONAL | Redaction metadata indicating which fields have been redacted and by which profile. See Section 5.12. |
| `severity` | string | OPTIONAL | Severity level of the event for filtering and prioritization. See Section 5.13. |
| `indicators` | array | OPTIONAL | Indicators of compromise or interest extracted from the event for security tool consumption. See Section 5.14. |

**REQ-5.2.3:** An Emitter MUST NOT add top-level fields to the envelope
beyond those defined in this section. Vendor-specific or implementation-
specific data MUST be placed within the `payload`, `context`, or
`metadata` fields.

**REQ-5.2.4:** A consumer of AILS Events MUST ignore unrecognized
top-level fields without error. This requirement enables forward
compatibility with future minor versions of the standard.

<br>

### 5.3 Core Identity Fields

**REQ-5.3.1:** The `ails_version` field MUST contain the full semantic
version string of the AILS standard under which the event was produced.
For this version of the standard, the value MUST be `"1.0.0"`.

**REQ-5.3.2:** The `event_id` field MUST be a universally unique
identifier conforming to RFC 9562. UUID version 4 (random) or UUID
version 7 (time-ordered random) MUST be used. UUID version 7 is
RECOMMENDED for deployments that require time-ordered event identifiers
without relying on timestamp parsing.

**REQ-5.3.3:** The `event_type` field MUST be a string in the format
`category.event_name` where `category` is one of the event categories
defined in Section 6 and `event_name` is a specific event type defined
within that category's specification. Custom event types MUST use the
prefix `x-` followed by a reverse-domain namespace
(e.g., `x-com.example.custom_event`).

**REQ-5.3.4:** The `timestamp` field MUST represent the time at which
the event occurred, not the time at which it was logged, buffered, or
transmitted. The value MUST be in ISO 8601 format with UTC timezone
designator and millisecond precision
(e.g., `"2026-04-10T14:30:00.123Z"`).

**REQ-5.3.5:** The `timestamp` MUST be generated from a monotonic
clock source where available. In environments where monotonic clocks are
not available, implementations SHOULD document the clock source and its
precision characteristics.

<br>

### 5.4 Emitter Object

The `emitter` field identifies the software component that produced
the AILS Event.

**REQ-5.4.1:** The `emitter` field MUST be a JSON object containing
the following fields:

| Field | Type | Required | Description |
|---|---|---|---|
| `id` | string | MUST | Unique identifier for the emitter instance. |
| `name` | string | MUST | Human-readable name of the emitting component (e.g., `"acs-runtime"`, `"als-service"`, `"llm-gateway"`). |
| `version` | string | SHOULD | Version of the emitting component. |
| `type` | string | MUST | Classification of the emitter. One of: `application`, `framework`, `runtime`, `gateway`, `proxy`, `security_service`, `infrastructure`. |

**REQ-5.4.2:** The `emitter.id` field MUST uniquely identify the
specific instance of the emitting component within the deployment. In
containerized environments, the container ID or pod name is RECOMMENDED.
In serverless environments, the function invocation ID is RECOMMENDED.

**REQ-5.4.3:** The `emitter.type` field MUST use one of the values
defined in REQ-5.4.1. Custom emitter types MUST use the prefix `x-`
followed by a reverse-domain namespace.

<br>

### 5.5 Actor Object

The `actor` field identifies the entity whose action or decision caused
the event to occur. The actor is distinct from the emitter: the emitter
is the component that writes the log; the actor is the entity whose
behavior is being logged.

**REQ-5.5.1:** The `actor` field MUST be a JSON object containing the
following fields:

| Field | Type | Required | Description |
|---|---|---|---|
| `type` | string | MUST | Classification of the actor. One of: `agent`, `human`, `system`, `external`. |
| `id` | string | MUST | Unique identifier for the actor. For agents: agent identity. For humans: user identity (subject to redaction). For system: service identity. For external: source identifier. |
| `name` | string | OPTIONAL | Human-readable name or label for the actor. |
| `agent_identity` | object | OPTIONAL | Extended agent identity fields. Present when `type` is `agent`. See Section 5.5.2. |
| `human_identity` | object | OPTIONAL | Extended human identity fields. Present when `type` is `human`. See Section 5.5.3. |

**REQ-5.5.2:** When the actor type is `agent`, the `agent_identity`
object SHOULD be present and MAY contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `framework` | string | OPTIONAL | Agent framework (e.g., `"langchain"`, `"crewai"`, `"autogen"`, `"custom"`). |
| `model` | string | OPTIONAL | Underlying model identifier if applicable. |
| `spiffe_id` | string | OPTIONAL | SPIFFE identity if available. |

**REQ-5.5.3:** When the actor type is `human`, the `human_identity`
object SHOULD be present and MAY contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `subject` | string | OPTIONAL | User subject identifier (e.g., from OIDC `sub` claim). Subject to redaction per Section 24. |
| `issuer` | string | OPTIONAL | Identity provider issuer URL. |
| `roles` | array of string | OPTIONAL | Roles held by the human principal at event time. |

**REQ-5.5.4:** In events where both an agent and a human are relevant
— such as dual-subject authorization decisions defined by AGBAC — the
`actor` field SHALL represent the primary actor (typically the agent),
and the secondary actor (typically the human principal) SHALL be
represented within the event `payload` using the `delegating_principal`
field defined in the applicable event type specification.

<br>

### 5.6 Correlation Object

The `correlation` field provides the identifiers necessary to link AILS
Events across multi-step workflows, cross-service interactions, and
session boundaries.

**REQ-5.6.1:** The `correlation` field MUST be a JSON object containing
the following fields:

| Field | Type | Required | Description |
|---|---|---|---|
| `trace_id` | string | MUST | Unique identifier for the end-to-end workflow or request. MUST be a 32-character lowercase hexadecimal string (128 bits), compatible with W3C Trace Context and OpenTelemetry trace IDs. |
| `span_id` | string | MUST | Unique identifier for the specific unit of work that produced this event. MUST be a 16-character lowercase hexadecimal string (64 bits), compatible with OpenTelemetry span IDs. |
| `parent_span_id` | string | OPTIONAL | Span ID of the parent operation that caused this event. `null` or absent for root spans. |
| `session_id` | string | OPTIONAL | Identifier for the interaction session, agent session, or governance session within which this event occurred. |
| `request_id` | string | OPTIONAL | Identifier for the originating user or API request. |
| `correlation_id` | string | OPTIONAL | An additional correlation identifier for domain-specific grouping (e.g., linking events across standards). |

**REQ-5.6.2:** The `trace_id` and `span_id` fields MUST be compatible
with W3C Trace Context specification (W3C Recommendation, 2021-11-23)
to enable direct mapping to OpenTelemetry without transformation.

**REQ-5.6.3:** When an AILS Event is produced within an existing
OpenTelemetry trace context, the Emitter MUST propagate the trace ID
and span ID from the active context. The Emitter MUST NOT generate new
trace or span identifiers that would break the existing correlation
chain.

**REQ-5.6.4:** When an AILS Event is produced outside of an existing
trace context, the Emitter MUST generate a new trace ID and span ID
conforming to REQ-5.6.1.

<br>

### 5.7 Data Classification Field

**REQ-5.7.1:** The `data_classification` field MUST be present on every
AILS Event and MUST contain one of the following values:

| Value | Definition |
|---|---|
| `SYSTEM_TRUSTED` | The event was produced by a system component operating within the trusted computing boundary of the deployment. Examples: security enforcement points, governance engines, infrastructure services. |
| `USER_TRUSTED` | The event was produced in response to an authenticated user action within established trust boundaries. Examples: authenticated API calls, human-approved agent actions. |
| `EXTERNAL_UNTRUSTED` | The event was produced in response to or contains data from an external, unauthenticated, or unverified source. Examples: events triggered by external API responses, web-scraped content, unauthenticated inputs. |

**REQ-5.7.2:** The `data_classification` value MUST be assigned by the
Emitter at event creation time and MUST NOT be modified after the event
is produced.

**REQ-5.7.3:** When an event is triggered by a combination of trusted
and untrusted inputs, the Emitter MUST assign the least trusted
classification that applies. An event influenced by any
`EXTERNAL_UNTRUSTED` input MUST be classified as `EXTERNAL_UNTRUSTED`.

<br>

### 5.8 Context Object

The `context` field provides deployment and environmental metadata that
applies to the event but is not part of the event's core semantics.

**REQ-5.8.1:** The `context` field, when present, MUST be a JSON object.
The following fields are defined:

| Field | Type | Required | Description |
|---|---|---|---|
| `environment` | string | OPTIONAL | Deployment environment (e.g., `"production"`, `"staging"`, `"development"`). |
| `region` | string | OPTIONAL | Geographic region or data center identifier. |
| `deployment_id` | string | OPTIONAL | Deployment or release identifier. |
| `service_name` | string | OPTIONAL | Logical service name within a distributed system. |
| `labels` | object | OPTIONAL | Key-value pairs for deployment-specific classification. Keys and values MUST be strings. |

**REQ-5.8.2:** Custom context fields not defined in this section MUST
be placed within the `labels` sub-object, not as top-level `context`
fields.

<br>

### 5.9 Metadata Object

The `metadata` field carries non-semantic operational data that is useful
for analysis, debugging, or cost tracking but does not affect the
meaning of the event.

**REQ-5.9.1:** The `metadata` field, when present, MUST be a JSON
object. The following fields are defined:

| Field | Type | Required | Description |
|---|---|---|---|
| `latency_ms` | number | OPTIONAL | Duration of the operation in milliseconds. |
| `cost` | object | OPTIONAL | Cost information. See REQ-5.9.2. |
| `retry_count` | integer | OPTIONAL | Number of retries before this event was produced. |
| `sdk_name` | string | OPTIONAL | Name of the AILS SDK or library used to emit this event. |
| `sdk_version` | string | OPTIONAL | Version of the AILS SDK or library. |

**REQ-5.9.2:** The `cost` object, when present, MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `amount` | number | MUST | Numeric cost value. |
| `currency` | string | MUST | ISO 4217 currency code (e.g., `"USD"`). |
| `unit` | string | OPTIONAL | Cost unit description (e.g., `"per_request"`, `"per_1k_tokens"`). |

<br>

### 5.10 Integrity Object

The `integrity` field provides tamper-evidence and cryptographic signing
for AILS Events. This field enables the graduated integrity model
defined in Section 22.

**REQ-5.10.1:** `[Level 2+]` The `integrity` field MUST be present on
every AILS Event emitted at Level 2 or above.

**REQ-5.10.2:** The `integrity` field, when present, MUST be a JSON
object containing the following fields:

| Field | Type | Required | Description |
|---|---|---|---|
| `sequence_number` | integer | MUST (Level 2+) | Monotonically increasing integer within the event stream. Starts at 1 for the first event in a stream. |
| `stream_id` | string | MUST (Level 2+) | Identifier for the event stream within which sequence numbering and chain hashing are maintained. |
| `chain_hash` | string | MUST (Level 2+) | SHA-256 hash providing tamper-evidence. See Section 22 for computation rules. |
| `event_signature` | string | OPTIONAL (Level 3) | Cryptographic signature over the canonical event. See Section 22. |
| `signing_key_id` | string | OPTIONAL (Level 3) | Identifier of the key used to produce `event_signature`. |
| `signing_algorithm` | string | OPTIONAL (Level 3) | Algorithm used (e.g., `"Ed25519"`, `"ES256"`). |

**REQ-5.10.3:** `[Level 2+]` The `sequence_number` MUST be a positive
integer that increments by exactly 1 for each successive event within
the same `stream_id`. A gap in sequence numbers indicates a missing or
deleted event.

**REQ-5.10.4:** `[Level 2+]` The `stream_id` MUST uniquely identify the
scope within which chain hashing is maintained. Typical stream scopes
include: per-session, per-emitter, or per-tenant. The stream scope MUST
be documented in the implementation's conformance claim.

**REQ-5.10.5:** At Level 1, the `integrity` field MAY be absent. Level 1
events are valid without integrity fields.

<br>

### 5.11 Tenant Object

The `tenant` field provides multi-tenant isolation for AILS Events in
shared infrastructure deployments.

**REQ-5.11.1:** The `tenant` field, when present, MUST be a JSON object
containing:

| Field | Type | Required | Description |
|---|---|---|---|
| `tenant_id` | string | MUST | Unique identifier for the organizational tenant. |
| `partition` | string | OPTIONAL | Logical partition within the tenant, if applicable. |

**REQ-5.11.2:** In multi-tenant deployments, the `tenant` field MUST be
present on every AILS Event. See Section 26 for multi-tenant isolation
requirements.

<br>

### 5.12 Redaction Object

The `redaction` field provides metadata about which fields in the event
have been redacted and under which profile.

**REQ-5.12.1:** The `redaction` field, when present, MUST be a JSON
object containing:

| Field | Type | Required | Description |
|---|---|---|---|
| `profile` | string | MUST | Name of the redaction profile applied (e.g., `"default"`, `"hipaa"`, `"gdpr_strict"`). |
| `redacted_fields` | array of string | MUST | JSON path expressions identifying fields that have been redacted, hashed, or omitted in this event. |
| `redaction_method` | string | MUST | Method applied. One of: `omitted`, `hashed`, `truncated`, `masked`. |

**REQ-5.12.2:** When any field in an AILS Event has been redacted, the
`redaction` object MUST be present. When no fields have been redacted,
the `redaction` object MAY be absent.

**REQ-5.12.3:** A redacted field MUST be replaced with one of:

- The string `"[REDACTED]"` for omitted fields
- A SHA-256 hash prefixed with `"sha256:"` for hashed fields
- A truncated value with the suffix `"...[TRUNCATED]"` for truncated
  fields
- A masked value preserving structure but replacing content (e.g.,
  `"u]***@***.com"`) for masked fields

<br>

### 5.13 Severity Field

The `severity` field provides a uniform severity level on the event
envelope, enabling security tools, SIEMs, and log aggregation systems
to filter and prioritize AILS Events without inspecting payloads.

**REQ-5.13.1:** The `severity` field, when present, MUST contain one
of the following values:

| Value | Description |
|---|---|
| `debug` | Diagnostic detail useful only during development or troubleshooting. |
| `info` | Normal operational event. No action required. |
| `warn` | Abnormal condition that does not prevent operation but may require attention. |
| `error` | An operation failed. Requires investigation. |
| `critical` | A security, integrity, or governance condition requiring immediate action. |

**REQ-5.13.2:** When the `severity` field is absent, consumers SHOULD
treat the event as `info` severity.

**REQ-5.13.3:** The following event types MUST set `severity` to
`critical` when emitted:

- `policy.violation` with payload `severity` of `critical`
- `supplychain.validation_failure` with payload `severity` of `high`
  or `critical`
- `system.log_degraded` with payload `severity` of `critical`
- `system.alert` with payload `severity` of `critical`

**REQ-5.13.4:** The following event types SHOULD set `severity` to
`error` when their `status` indicates failure:

- `inference.call` with `status` of `error`
- `tool.call` with `status` of `error`
- `capability.execution_failed`
- `session.init_failed`
- `credential.refused`
- `authority.gate_denied`

**REQ-5.13.5:** The envelope `severity` field provides a uniform
filtering mechanism. It does not replace or override the `severity`
fields defined within specific event payloads (e.g.,
`policy.violation` payload `severity`). Both fields MAY be present
and MAY differ when the envelope severity is set for routing purposes
while the payload severity reflects domain-specific classification.

<br>

### 5.14 Indicators Array

The `indicators` field provides a standardized structure for extracting
indicators of compromise (IOCs) or indicators of interest from security-
relevant AILS Events, enabling automated consumption by SIEM, SOAR, and
threat intelligence platforms.

**REQ-5.14.1:** The `indicators` field, when present, MUST be a JSON
array. Each entry MUST be a JSON object containing:

| Field | Type | Required | Description |
|---|---|---|---|
| `type` | string | MUST | Classification of the indicator. One of: `hash`, `ip_address`, `domain`, `url`, `tool_name`, `model_name`, `artifact_name`, `agent_id`, `certificate_fingerprint`, `cid`, `custom`. |
| `value` | string | MUST | The indicator value. |
| `role` | string | MUST | Role of the indicator in the event. One of: `subject` (the entity being described), `source` (origin of an action), `target` (destination of an action), `related` (contextually associated). |
| `confidence` | number | OPTIONAL | Confidence score between 0.0 and 1.0, if applicable. |

**REQ-5.14.2:** The `indicators` field SHOULD be populated on the
following event types when the event represents an anomalous, failed,
or security-relevant condition:

- `policy.violation` — violating entity, affected resource
- `policy.decision` with `decision: deny` — denied subject, resource
- `supplychain.validation_failure` — affected artifact hash, name
- `supplychain.artifact_verified` with `verification_result: fail` —
  expected and actual hashes
- `integrity.manifest_verified` with `verification_result: fail` —
  server name, publisher
- `integrity.tofu_changed` — tool name, expected and observed digests
- `authority.gate_denied` — action ID, token ID if presented
- `control.channel_auth_failed` — source IP, certificate fingerprint
- `control.replay_detected` — command ID, commander ID
- `credential.refused` — requesting entity

**REQ-5.14.3:** The `indicators` field MUST NOT contain credential
material, private keys, bearer tokens, or raw authentication secrets.
Credential fingerprints (hashes of credentials) are permitted.

**REQ-5.14.4:** Indicator values that contain PII are subject to the
redaction requirements of Section 24. When redaction is applied, the
indicator value MUST be replaced with the redacted form and the
`redaction.redacted_fields` array MUST include the indicator's JSON
path.

<br>

### 5.15 Envelope Validation Rules

**REQ-5.15.1:** All fields designated as MUST in the envelope structure
(REQ-5.2.2) MUST be present and non-null in every AILS Event.

**REQ-5.15.2:** An AILS Event missing any required envelope field is
non-conformant and MUST be rejected by conformance validators.

**REQ-5.15.3:** An AILS Event containing a `payload` field that is not
a JSON object is non-conformant.

**REQ-5.15.4:** Consumers MUST NOT reject events based on unrecognized
fields within the `payload`, `context`, `metadata`, or `labels` objects.
Forward compatibility requires tolerance of unknown fields.

**REQ-5.15.5:** The maximum size of a single serialized AILS Event
SHOULD NOT exceed 1 megabyte. Events exceeding this size SHOULD be
split into multiple events linked by a shared `correlation_id`, or
large content fields SHOULD be redacted or truncated. Implementations
MAY enforce a configurable maximum event size.

<br>

## SECTION 6: EVENT TAXONOMY

*Normative*

### 6.1 Purpose

The AILS Event Taxonomy defines the complete set of event categories and
event types recognized by this standard. Each event category groups
related event types that share a common domain. Each event type within
a category defines a specific payload structure.

<br>

### 6.2 Category Registry

**REQ-6.2.1:** The `event_type` field of every AILS Event MUST use the
format:

```
category.event_name
```

where `category` is one of the categories registered in this section
and `event_name` is a specific event type defined within that category's
specification.

**REQ-6.2.2:** The following event categories are defined by this
standard:

| Category | Scope | Specification |
|---|---|---|
| `inference` | LLM and model inference operations | Section 7 |
| `agent` | Agent lifecycle, reasoning, and execution | Section 8 |
| `tool` | Tool and function invocations | Section 9 |
| `memory` | Memory and context read/write operations | Section 10 |
| `human` | Human-in-the-loop interactions | Section 11 |
| `authority` | Authority token and capability lifecycle | Section 12 |
| `session` | Session lifecycle and state transitions | Section 13 |
| `credential` | Credential issuance, renewal, and expiry | Section 14 |
| `policy` | Authorization decisions and policy enforcement | Section 15 |
| `supplychain` | Supply chain validation and artifact verification | Section 16 |
| `integrity` | Manifest verification and interface fingerprinting | Section 17 |
| `capability` | Capability governance and planning authorization | Section 18 |
| `control` | Control channel operations and commands | Section 19 |
| `a2a` | Agent-to-Agent protocol interactions | Section 20 |
| `system` | System health, alerts, and configuration | Section 21 |

**REQ-6.2.3:** Custom event categories MUST use the prefix `x-` followed
by a reverse-domain namespace (e.g., `x-com.example.custom_category`).
Implementations MUST NOT define custom event types within standard
categories without the `x-` prefix.

<br>

### 6.3 Event Type Naming Rules

**REQ-6.3.1:** Event type names MUST be lowercase.

**REQ-6.3.2:** Event type names MUST use underscores to separate words
within the event name component (e.g., `inference.call`,
`authority.token_issued`, `session.state_changed`).

**REQ-6.3.3:** Event type names MUST NOT exceed 128 characters in total
length including the category prefix and separator.

<br>

### 6.4 Event Type Stability

**REQ-6.4.1:** Each event type defined in this standard carries a
stability designation:

| Designation | Meaning |
|---|---|
| `stable` | Backward-compatible. Fields may be added but not removed or renamed. |
| `experimental` | Subject to change in minor versions. Implementations SHOULD support but MUST NOT depend on field stability. |
| `deprecated` | Scheduled for removal. The deprecation notice specifies the version in which the event type will be removed and the replacement event type, if any. |

**REQ-6.4.2:** All event types defined in Sections 7 through 20 of this
version of the standard are designated `stable` unless explicitly marked
otherwise.

<br>

### 6.5 Payload Structure Convention

**REQ-6.5.1:** Every event type specification MUST define:

- The event type string (e.g., `inference.call`)
- A description of when the event is emitted
- A payload field table with field name, type, required/optional
  designation, and description
- The stability designation

**REQ-6.5.2:** Payload fields designated as MUST are required for every
instance of that event type. Payload fields designated as SHOULD or
OPTIONAL MAY be absent.

**REQ-6.5.3:** Payload fields not defined in the event type
specification MAY be present. Consumers MUST ignore unrecognized payload
fields.

<br>

### 6.6 Cross-Standard Event Binding

**REQ-6.6.1:** When an AILS event type is defined to satisfy an audit
requirement of an external standard (DAE, AGBAC, ALS, AI-SCS, ACS,
MCP Integrity Standard, or A2A), the event type specification SHALL
reference the specific external standard requirement it satisfies.

**REQ-6.6.2:** The complete mapping between AILS event types and
external standard audit requirements is maintained in Appendix E
(Cross-Standard Event Matrix).

<br>

### 6.7 Summary of Event Types

The following table provides a non-exhaustive summary of the event types
defined by this standard. The authoritative definitions are in Sections
7 through 20.

| Event Type | Category | Description |
|---|---|---|
| `inference.call` | inference | A single LLM or model inference request and response |
| `inference.embedding` | inference | An embedding generation operation |
| `inference.cache_hit` | inference | A cached inference result was served |
| `inference.guardrail` | inference | An input or output guardrail evaluation |
| `inference.routing` | inference | A model routing or fallback decision |
| `inference.generation` | inference | A non-text generative AI operation (image, audio, video, code) |
| `inference.structured_output` | inference | A structured output validation against a schema constraint |
| `agent.lifecycle` | agent | Agent instantiation or termination |
| `agent.step` | agent | A single agent reasoning or action step |
| `agent.delegation` | agent | An agent delegating to another agent |
| `agent.loop_checkpoint` | agent | A governance checkpoint in an autonomous execution loop |
| `agent.workflow_node` | agent | A DAG or graph workflow node completed execution |
| `tool.call` | tool | A tool or function invocation |
| `tool.discovery` | tool | A tool discovery or enumeration event |
| `tool.mcp_resource` | tool | An MCP resource access operation |
| `tool.mcp_sampling` | tool | An MCP server-initiated sampling request |
| `memory.read` | memory | A memory or context retrieval operation |
| `memory.write` | memory | A memory or context persistence operation |
| `human.feedback` | human | Human feedback on AI output |
| `human.confirmation` | human | Human confirmation for a controlled action |
| `human.escalation_response` | human | Human response to an escalation request |
| `authority.token_issued` | authority | An authority token or capability was issued |
| `authority.token_consumed` | authority | An authority token was consumed by an action |
| `authority.token_expired` | authority | An authority token expired without use |
| `authority.token_revoked` | authority | An authority token was revoked |
| `authority.gate_passed` | authority | An enforcement gate check succeeded |
| `authority.gate_denied` | authority | An enforcement gate check failed |
| `session.initialized` | session | A governance or interaction session was created |
| `session.state_changed` | session | A session transitioned to a new state |
| `session.terminated` | session | A session was permanently closed |
| `credential.issued` | credential | A credential was issued |
| `credential.renewed` | credential | A credential was renewed |
| `credential.refused` | credential | A credential renewal was refused |
| `credential.expired` | credential | A credential expired |
| `policy.decision` | policy | An authorization decision was made |
| `policy.violation` | policy | A policy violation was detected |
| `policy.escalation` | policy | An escalation was triggered |
| `supplychain.abom_validated` | supplychain | An ABOM was validated |
| `supplychain.artifact_verified` | supplychain | An artifact integrity check was performed |
| `supplychain.trust_assertion` | supplychain | A trust assertion was evaluated |
| `supplychain.validation_failure` | supplychain | A runtime supply chain validation failed |
| `integrity.manifest_verified` | integrity | A sealed manifest was verified |
| `integrity.fingerprint_checked` | integrity | An interface fingerprint was compared |
| `integrity.chain_validated` | integrity | A version lineage chain was validated |
| `integrity.tofu_recorded` | integrity | A TOFU fingerprint was cached on first use |
| `integrity.tofu_changed` | integrity | A TOFU fingerprint mismatch was detected |
| `capability.index_delivered` | capability | A Capability Summary Index was delivered |
| `capability.paep_evaluated` | capability | A PAEP authorization decision was made |
| `capability.manifest_delivered` | capability | A Capability Manifest was delivered |
| `capability.selected` | capability | A capability was selected by a planner |
| `capability.contract_issued` | capability | An Execution Contract was issued |
| `capability.executed` | capability | A capability was executed |
| `control.command_received` | control | A control channel command was received |
| `control.command_executed` | control | A control channel command was executed |
| `control.channel_connected` | control | A control channel connection was established |
| `control.channel_disconnected` | control | A control channel connection was lost |
| `control.watchdog_triggered` | control | A watchdog timer triggered |
| `control.push_notification_sent` | control | A push notification was sent |
| `control.push_notification_acked` | control | A push notification was acknowledged |
| `a2a.task_created` | a2a | An A2A task was initiated |
| `a2a.task_status_changed` | a2a | An A2A task transitioned state |
| `a2a.message_sent` | a2a | A message was sent to a remote agent |
| `a2a.message_received` | a2a | A message was received from a remote agent |
| `a2a.agent_card_discovered` | a2a | An A2A Agent Card was fetched and parsed |
| `a2a.authentication` | a2a | An A2A authentication event occurred |
| `system.alert` | system | An alert condition was triggered |
| `system.health` | system | A health or status check event |
| `system.configuration_changed` | system | A configuration change was detected |
| `system.log_degraded` | system | Logging infrastructure degradation was detected |
| `system.retention_action` | system | A retention policy action was executed |
| `system.budget_exceeded` | system | A cost or resource budget threshold was crossed |
| `system.quota_exhausted` | system | A rate limit or quota was exhausted |

<br>

## SECTION 7: CATEGORY — INFERENCE EVENTS

*Normative*

### 7.1 Purpose

Inference events record interactions with large language models,
embedding models, and other AI inference services. These events capture
the fundamental unit of AI behavior: a request to a model and the
resulting response. Inference events enable debugging, cost analysis,
latency tracking, safety monitoring, and cross-vendor comparison of
model interactions.

<br>

### 7.2 Event Type: `inference.call`

**Stability:** Stable

**Description:** Emitted when a single inference request to a language
model or generative AI model completes, whether successfully or with
an error. One `inference.call` event represents one request-response
cycle.

**When to emit:** After the inference response has been received in its
entirety, or after the final token has been received in a streaming
response, or after the inference request has failed.

**REQ-7.2.1:** The `inference.call` payload MUST contain the following
fields:

| Field | Type | Required | Description |
|---|---|---|---|
| `provider` | string | MUST | Identifier of the inference provider (e.g., `"openai"`, `"anthropic"`, `"google"`, `"aws_bedrock"`, `"self_hosted"`). |
| `model` | object | MUST | Model identification. See REQ-7.2.2. |
| `status` | string | MUST | Outcome of the inference call. One of: `success`, `error`, `timeout`, `refused`. |
| `input` | object | MUST | Input details. See REQ-7.2.3. |
| `output` | object | MUST | Output details. See REQ-7.2.4. |
| `parameters` | object | OPTIONAL | Model configuration parameters. See REQ-7.2.5. |
| `usage` | object | OPTIONAL | Token usage and cost metrics. See REQ-7.2.6. |
| `latency` | object | OPTIONAL | Timing details. See REQ-7.2.7. |
| `error` | object | OPTIONAL | Error details. MUST be present when `status` is `error`, `timeout`, or `refused`. See REQ-7.2.8. |
| `streaming` | boolean | OPTIONAL | Whether the response was delivered via streaming. Default: `false`. |
| `cached` | boolean | OPTIONAL | Whether the response was served from a cache. Default: `false`. |
| `tool_calls_requested` | array | OPTIONAL | Tool or function calls requested by the model in its response. See REQ-7.2.9. |

**REQ-7.2.2:** The `model` object MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `name` | string | MUST | Model identifier as recognized by the provider (e.g., `"gpt-4.1"`, `"claude-sonnet-4-20250514"`, `"gemini-2.5-pro"`). |
| `version` | string | OPTIONAL | Model version or checkpoint identifier, if distinct from the name. |

**REQ-7.2.3:** The `input` object MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `format` | string | MUST | Input format. One of: `messages`, `prompt`, `multimodal`. |
| `message_count` | integer | OPTIONAL | Number of messages in the input, if `format` is `messages`. |
| `token_count` | integer | OPTIONAL | Number of input tokens as reported by the provider or estimated by the emitter. |
| `content` | any | OPTIONAL | The input content (prompt string, messages array, or multimodal payload). Subject to redaction per Section 24. |
| `has_system_prompt` | boolean | OPTIONAL | Whether a system prompt was included. |
| `has_images` | boolean | OPTIONAL | Whether image inputs were included. |
| `has_documents` | boolean | OPTIONAL | Whether document inputs were included. |

**REQ-7.2.4:** The `output` object MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `token_count` | integer | OPTIONAL | Number of output tokens. |
| `finish_reason` | string | OPTIONAL | Reason the model stopped generating. Provider-specific values are permitted (e.g., `"stop"`, `"length"`, `"tool_calls"`, `"content_filter"`). |
| `content` | any | OPTIONAL | The output content. Subject to redaction per Section 24. |
| `has_tool_calls` | boolean | OPTIONAL | Whether the model response included tool call requests. |
| `refusal` | boolean | OPTIONAL | Whether the model refused to generate a response. |

**REQ-7.2.5:** The `parameters` object, when present, MAY contain:

| Field | Type | Description |
|---|---|---|
| `temperature` | number | Sampling temperature. |
| `top_p` | number | Nucleus sampling parameter. |
| `top_k` | integer | Top-k sampling parameter. |
| `max_tokens` | integer | Maximum output tokens requested. |
| `stop_sequences` | array of string | Stop sequences configured. |
| `frequency_penalty` | number | Frequency penalty. |
| `presence_penalty` | number | Presence penalty. |
| `seed` | integer | Random seed for reproducibility. |
| `response_format` | string | Requested response format (e.g., `"json"`, `"text"`). |

**REQ-7.2.6:** The `usage` object, when present, SHOULD contain:

| Field | Type | Description |
|---|---|---|
| `input_tokens` | integer | Input tokens as reported by the provider. |
| `output_tokens` | integer | Output tokens as reported by the provider. |
| `total_tokens` | integer | Total tokens consumed. |
| `input_cached_tokens` | integer | Input tokens served from provider cache. |
| `cost` | object | Cost information conforming to REQ-5.9.2. |

**REQ-7.2.7:** The `latency` object, when present, SHOULD contain:

| Field | Type | Description |
|---|---|---|
| `total_ms` | number | Total wall-clock time from request sent to response complete. |
| `time_to_first_token_ms` | number | Time from request sent to first token received (streaming). |
| `provider_processing_ms` | number | Provider-reported processing time, if available. |

**REQ-7.2.8:** The `error` object, when present, MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `code` | string | MUST | Error code or classification (e.g., `"rate_limited"`, `"context_length_exceeded"`, `"model_unavailable"`, `"content_filtered"`). |
| `message` | string | OPTIONAL | Human-readable error description. Subject to redaction. |
| `retryable` | boolean | OPTIONAL | Whether the error is retryable. |

**REQ-7.2.9:** Each entry in the `tool_calls_requested` array, when
present, MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `tool_name` | string | MUST | Name of the tool or function the model requested to call. |
| `tool_call_id` | string | OPTIONAL | Provider-assigned identifier for the tool call. |
| `arguments_summary` | string | OPTIONAL | Summary or hash of the arguments. Subject to redaction. Full arguments MUST NOT be included by default. |

<br>

### 7.3 Event Type: `inference.embedding`

**Stability:** Stable

**Description:** Emitted when an embedding generation request completes.

**REQ-7.3.1:** The `inference.embedding` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `provider` | string | MUST | Inference provider identifier. |
| `model` | object | MUST | Model identification per REQ-7.2.2. |
| `status` | string | MUST | Outcome. One of: `success`, `error`, `timeout`. |
| `input_count` | integer | MUST | Number of input items (texts, images, etc.) embedded. |
| `input_token_count` | integer | OPTIONAL | Total input tokens across all items. |
| `dimensions` | integer | OPTIONAL | Dimensionality of the output embeddings. |
| `usage` | object | OPTIONAL | Token usage per REQ-7.2.6. |
| `latency` | object | OPTIONAL | Timing per REQ-7.2.7. |
| `error` | object | OPTIONAL | Error details per REQ-7.2.8. |

<br>

### 7.4 Event Type: `inference.cache_hit`

**Stability:** Stable

**Description:** Emitted when an inference response is served from a
cache (semantic cache, exact-match cache, or provider-side cache)
without making a full inference request.

**REQ-7.4.1:** The `inference.cache_hit` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `cache_type` | string | MUST | Type of cache. One of: `exact`, `semantic`, `provider`, `custom`. |
| `original_model` | object | MUST | Model identification of the cached inference per REQ-7.2.2. |
| `cache_age_seconds` | number | OPTIONAL | Age of the cached response in seconds. |
| `similarity_score` | number | OPTIONAL | Semantic similarity score, if `cache_type` is `semantic`. |

<br>

### 7.5 Event Type: `inference.guardrail`

**Stability:** Stable

**Description:** Emitted when an input or output guardrail, content
filter, safety classifier, or moderation check is evaluated against
inference content.

**REQ-7.5.1:** The `inference.guardrail` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `guardrail_name` | string | MUST | Name or identifier of the guardrail. |
| `guardrail_version` | string | OPTIONAL | Version of the guardrail. |
| `direction` | string | MUST | Whether the guardrail was applied to input or output. One of: `input`, `output`, `both`. |
| `result` | string | MUST | Outcome of the guardrail evaluation. One of: `pass`, `fail`, `warn`. |
| `categories` | array of string | OPTIONAL | Categories triggered (e.g., `"pii"`, `"toxicity"`, `"prompt_injection"`, `"jailbreak"`). |
| `severity` | string | OPTIONAL | Severity of the finding. One of: `low`, `medium`, `high`, `critical`. |
| `action_taken` | string | OPTIONAL | Action taken in response. One of: `none`, `blocked`, `modified`, `flagged`, `logged`. |
| `scores` | object | OPTIONAL | Key-value pairs of category names to numeric confidence scores. |

<br>

### 7.6 Event Type: `inference.routing`

**Stability:** Stable

**Description:** Emitted when an inference gateway, proxy, or router
makes a routing decision — selecting a model, provider, or endpoint for
an inference request based on cost, latency, availability, or policy
criteria.

**REQ-7.6.1:** The `inference.routing` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `requested_model` | string | MUST | Model originally requested by the caller. |
| `routed_model` | string | MUST | Model to which the request was actually routed. |
| `routed_provider` | string | MUST | Provider to which the request was routed. |
| `routing_reason` | string | MUST | Reason for the routing decision. One of: `primary`, `fallback_unavailable`, `fallback_overloaded`, `fallback_cost`, `fallback_latency`, `policy`, `load_balance`. |
| `candidates_evaluated` | integer | OPTIONAL | Number of candidate models or providers evaluated. |
| `fallback_depth` | integer | OPTIONAL | Position in the fallback chain (1 = primary, 2 = first fallback, etc.). |

<br>

### 7.7 Event Type: `inference.generation`

**Stability:** Stable

**Description:** Emitted when a non-text generative AI operation
completes — including image generation, audio synthesis, video
generation, code execution, and other modalities where the output is
not a text completion. This event type covers operations that are
structurally different from text inference (different parameters,
output formats, and cost models).

**REQ-7.7.1:** The `inference.generation` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `provider` | string | MUST | Inference provider identifier. |
| `model` | object | MUST | Model identification per REQ-7.2.2. |
| `modality` | string | MUST | Output modality. One of: `image`, `audio`, `video`, `code_execution`, `3d`, `custom`. |
| `status` | string | MUST | Outcome. One of: `success`, `error`, `timeout`, `refused`, `content_filtered`. |
| `input` | object | OPTIONAL | Input details. See REQ-7.7.2. |
| `output` | object | OPTIONAL | Output details. See REQ-7.7.3. |
| `parameters` | object | OPTIONAL | Generation-specific parameters. See REQ-7.7.4. |
| `usage` | object | OPTIONAL | Usage and cost per REQ-7.2.6. |
| `latency` | object | OPTIONAL | Timing per REQ-7.2.7. |
| `error` | object | OPTIONAL | Error details per REQ-7.2.8. |

**REQ-7.7.2:** The `input` object for generation events, when present,
SHOULD contain:

| Field | Type | Description |
|---|---|---|
| `prompt` | string | Text prompt or instruction for generation. Subject to redaction. |
| `prompt_hash` | string | SHA-256 hash of the prompt. RECOMMENDED when `prompt` is redacted. |
| `reference_inputs` | array of string | Types of reference inputs provided (e.g., `"image"`, `"audio"`, `"sketch"`, `"mask"`). |
| `input_token_count` | integer | Input tokens, if applicable. |

**REQ-7.7.3:** The `output` object for generation events, when present,
SHOULD contain:

| Field | Type | Description |
|---|---|---|
| `output_count` | integer | Number of outputs generated (e.g., number of images). |
| `output_format` | string | Output format (e.g., `"png"`, `"mp3"`, `"mp4"`, `"python"`). |
| `output_dimensions` | string | Dimensions or size descriptor (e.g., `"1024x1024"`, `"30s"`, `"1080p"`). |
| `output_size_bytes` | integer | Total output size in bytes. |
| `content_filter_applied` | boolean | Whether a content filter modified or blocked the output. |

**REQ-7.7.4:** The `parameters` object for generation events, when
present, MAY contain:

| Field | Type | Description |
|---|---|---|
| `quality` | string | Quality setting (e.g., `"standard"`, `"hd"`, `"ultra"`). |
| `style` | string | Style parameter, if applicable. |
| `steps` | integer | Number of generation steps (diffusion models). |
| `guidance_scale` | number | Guidance or CFG scale. |
| `seed` | integer | Random seed for reproducibility. |
| `negative_prompt` | string | Negative prompt, if applicable. Subject to redaction. |

<br>

### 7.8 Event Type: `inference.structured_output`

**Stability:** Stable

**Description:** Emitted when an inference request uses structured
output enforcement — JSON schema constraints, function calling schema,
grammar-based decoding, or other mechanisms that constrain model output
to a specific format. This event records whether the constrained output
validated successfully and captures validation failures that indicate
model-schema misalignment.

**REQ-7.8.1:** The `inference.structured_output` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `constraint_type` | string | MUST | Type of output constraint. One of: `json_schema`, `function_call`, `grammar`, `regex`, `enum`, `custom`. |
| `schema_name` | string | OPTIONAL | Name or identifier of the schema or grammar applied. |
| `schema_hash` | string | OPTIONAL | SHA-256 hash of the schema definition. |
| `validation_result` | string | MUST | Whether the output validated against the constraint. One of: `valid`, `invalid`, `partial`, `error`. |
| `validation_errors` | array of string | OPTIONAL | Descriptions of validation failures. Present when `validation_result` is `invalid` or `partial`. |
| `retry_count` | integer | OPTIONAL | Number of retries attempted to produce valid output. |
| `inference_event_id` | string | MUST | Event ID of the `inference.call` event this structured output validation applies to. |

<br>

## SECTION 8: CATEGORY — AGENT EVENTS

*Normative*

### 8.1 Purpose

Agent events record the lifecycle, reasoning, decision-making, and
delegation behavior of AI agents. Agents are stateful, multi-step
systems that plan, reason, select tools, observe results, and adjust
behavior across multiple inference cycles. Agent events make this
behavior observable without requiring disclosure of proprietary
reasoning implementations or chain-of-thought content.

<br>

### 8.2 Event Type: `agent.lifecycle`

**Stability:** Stable

**Description:** Emitted when an agent is instantiated, configured,
restarted, or terminated. Lifecycle events bracket the agent's
operational period and provide the context necessary to interpret
subsequent `agent.step` events.

**REQ-8.2.1:** The `agent.lifecycle` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `agent_id` | string | MUST | Unique identifier for the agent instance. |
| `agent_name` | string | OPTIONAL | Human-readable name of the agent. |
| `agent_type` | string | MUST | Agent architecture or pattern. One of: `react`, `plan_and_execute`, `reflexion`, `chain_of_thought`, `tool_use`, `multi_agent`, `custom`. |
| `lifecycle_action` | string | MUST | The lifecycle transition. One of: `started`, `configured`, `restarted`, `terminated`, `failed`. |
| `framework` | string | OPTIONAL | Agent framework (e.g., `"langchain"`, `"crewai"`, `"autogen"`, `"semantic_kernel"`, `"custom"`). |
| `framework_version` | string | OPTIONAL | Version of the agent framework. |
| `model` | object | OPTIONAL | Primary model used by the agent, per REQ-7.2.2. |
| `tools_available` | array of string | OPTIONAL | List of tool names available to the agent at lifecycle event time. |
| `tool_count` | integer | OPTIONAL | Number of tools available. |
| `configuration` | object | OPTIONAL | Agent configuration parameters. Subject to redaction. MUST NOT contain credentials. |
| `termination_reason` | string | OPTIONAL | Reason for termination. MUST be present when `lifecycle_action` is `terminated` or `failed`. |
| `error` | object | OPTIONAL | Error details per REQ-7.2.8. Present when `lifecycle_action` is `failed`. |

<br>

### 8.3 Event Type: `agent.step`

**Stability:** Stable

**Description:** Emitted when an agent completes a single step in its
execution cycle. A step represents one discrete reasoning, action,
observation, or reflection operation. The granularity of steps is
determined by the agent architecture, but each step SHOULD represent
the smallest meaningful unit of agent decision-making.

**REQ-8.3.1:** The `agent.step` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `agent_id` | string | MUST | Unique identifier for the agent instance. |
| `step_index` | integer | MUST | Zero-based index of this step within the current agent execution. |
| `step_type` | string | MUST | Classification of the step. One of: `reasoning`, `planning`, `action`, `observation`, `reflection`, `synthesis`, `custom`. |
| `status` | string | MUST | Outcome of the step. One of: `completed`, `failed`, `skipped`, `interrupted`. |
| `goal` | string | OPTIONAL | The current goal or objective the agent is pursuing. Subject to redaction. |
| `decision` | object | OPTIONAL | Decision made during this step. See REQ-8.3.2. |
| `reasoning` | object | OPTIONAL | Reasoning information. See REQ-8.3.3. |
| `tool_selected` | string | OPTIONAL | Name of the tool selected during this step, if any. |
| `input_event_ids` | array of string | OPTIONAL | Event IDs of events that provided input to this step (e.g., prior `inference.call`, `tool.call`, or `memory.read` events). |
| `output_event_ids` | array of string | OPTIONAL | Event IDs of events produced by this step (e.g., a resulting `tool.call` or `inference.call`). |
| `iteration` | integer | OPTIONAL | Iteration count if the agent is in a retry or refinement loop. |
| `max_iterations` | integer | OPTIONAL | Maximum iterations configured for the current loop. |
| `error` | object | OPTIONAL | Error details per REQ-7.2.8. Present when `status` is `failed`. |

**REQ-8.3.2:** The `decision` object, when present, MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `description` | string | OPTIONAL | Human-readable description of the decision. Subject to redaction. |
| `confidence` | number | OPTIONAL | Confidence score between 0.0 and 1.0, if available. |
| `alternatives_considered` | integer | OPTIONAL | Number of alternative actions considered before this decision. |

**REQ-8.3.3:** The `reasoning` object, when present, MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `disclosure` | string | MUST | Level of reasoning disclosure. One of: `none`, `summary`, `full`, `hidden`. |
| `summary` | string | OPTIONAL | A summary of the reasoning. MUST be present when `disclosure` is `summary`. Subject to redaction. |
| `content` | string | OPTIONAL | Full reasoning content. MUST be present when `disclosure` is `full`. Subject to redaction. MUST NOT be required by any conformance level. |

**REQ-8.3.4:** Implementations MUST NOT require `reasoning.content` to
be populated. Chain-of-thought and internal reasoning content is
sensitive intellectual property and safety-relevant data. The
`disclosure` value `hidden` explicitly indicates that reasoning occurred
but is not disclosed, and this is a conformant state.

<br>

### 8.4 Event Type: `agent.delegation`

**Stability:** Stable

**Description:** Emitted when an agent delegates a task, sub-goal, or
action to another agent. Delegation events enable tracing of multi-agent
orchestration workflows and identification of delegation chains.

**REQ-8.4.1:** The `agent.delegation` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `delegating_agent_id` | string | MUST | Agent ID of the delegating (parent) agent. |
| `receiving_agent_id` | string | MUST | Agent ID of the receiving (child) agent. |
| `delegation_type` | string | MUST | Type of delegation. One of: `task`, `sub_goal`, `tool_execution`, `review`, `custom`. |
| `task_description` | string | OPTIONAL | Description of the delegated task. Subject to redaction. |
| `delegation_depth` | integer | OPTIONAL | Depth of the delegation chain (1 = first delegation, 2 = sub-delegation, etc.). |
| `constraints` | object | OPTIONAL | Constraints placed on the delegated agent (e.g., time limits, tool restrictions, scope boundaries). |
| `status` | string | MUST | Outcome of the delegation. One of: `initiated`, `accepted`, `completed`, `failed`, `rejected`. |
| `result_summary` | string | OPTIONAL | Summary of the delegation result. Subject to redaction. Present when `status` is `completed` or `failed`. |

<br>

### 8.5 Event Type: `agent.loop_checkpoint`

**Stability:** Stable

**Description:** Emitted at governance checkpoints during autonomous
agent execution loops. Long-running autonomous agents (research agents,
coding agents, AutoGPT-pattern agents) operate in iterative loops that
may run for extended periods. Loop checkpoint events provide governance
visibility into loop progress, resource consumption, and autonomy
boundaries without requiring a separate event for every step.

**REQ-8.5.1:** The `agent.loop_checkpoint` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `agent_id` | string | MUST | Unique identifier for the agent instance. |
| `loop_id` | string | MUST | Identifier for the current execution loop. |
| `iteration` | integer | MUST | Current iteration number within the loop. |
| `max_iterations` | integer | OPTIONAL | Maximum iterations configured. `null` if unbounded. |
| `checkpoint_type` | string | MUST | Type of checkpoint. One of: `periodic`, `budget_threshold`, `iteration_milestone`, `time_threshold`, `manual`, `policy_required`. |
| `status` | string | MUST | Loop status at checkpoint. One of: `continuing`, `completed`, `paused`, `terminated`, `budget_exhausted`. |
| `resource_usage` | object | OPTIONAL | Resources consumed since loop start. See REQ-8.5.2. |
| `goal_progress` | string | OPTIONAL | Summary of progress toward the loop's goal. Subject to redaction. |
| `next_action_summary` | string | OPTIONAL | Summary of the planned next action. Subject to redaction. |
| `human_review_required` | boolean | OPTIONAL | Whether this checkpoint requires human review before continuing. Default: `false`. |

**REQ-8.5.2:** The `resource_usage` object, when present, SHOULD
contain:

| Field | Type | Description |
|---|---|---|
| `total_tokens` | integer | Total tokens consumed since loop start. |
| `total_inference_calls` | integer | Total inference calls made. |
| `total_tool_calls` | integer | Total tool invocations made. |
| `elapsed_seconds` | number | Wall-clock seconds since loop start. |
| `estimated_cost` | object | Estimated cost per REQ-5.9.2. |

<br>

### 8.6 Event Type: `agent.workflow_node`

**Stability:** Stable

**Description:** Emitted when a node in a DAG-based or graph-based
agent workflow completes execution. Workflow orchestration frameworks
(LangGraph, CrewAI workflows, custom DAGs) define structured execution
graphs where agents or operations execute at named nodes with defined
edges. This event captures node-level execution, enabling visualization
and debugging of graph execution paths.

**REQ-8.6.1:** The `agent.workflow_node` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `workflow_id` | string | MUST | Identifier for the workflow instance. |
| `workflow_name` | string | OPTIONAL | Name of the workflow definition. |
| `node_id` | string | MUST | Identifier for the node within the workflow graph. |
| `node_name` | string | OPTIONAL | Human-readable name of the node. |
| `node_type` | string | MUST | Type of node. One of: `agent`, `tool`, `condition`, `router`, `aggregator`, `human_input`, `start`, `end`, `custom`. |
| `status` | string | MUST | Node execution outcome. One of: `completed`, `failed`, `skipped`, `timeout`. |
| `incoming_edges` | array of string | OPTIONAL | Node IDs of predecessors that triggered this node. |
| `outgoing_edge` | string | OPTIONAL | Node ID of the successor selected after this node's execution. |
| `branch_condition` | string | OPTIONAL | If `node_type` is `condition` or `router`, the condition or routing rule that determined the outgoing edge. Subject to redaction. |
| `parallel_group` | string | OPTIONAL | Identifier for a parallel execution group, if this node executed in parallel with others. |
| `execution_order` | integer | OPTIONAL | Order of execution within the workflow (1-based). |
| `latency` | object | OPTIONAL | Timing per REQ-7.2.7. |
| `error` | object | OPTIONAL | Error details per REQ-7.2.8. Present when `status` is `failed`. |

<br>

## SECTION 9: CATEGORY — TOOL EVENTS

*Normative*

### 9.1 Purpose

Tool events record invocations of external tools, functions, APIs, and
services by AI systems. Tool usage is a defining characteristic of
modern AI agents and represents the boundary where AI reasoning produces
real-world side effects. Tool events enable auditing of external actions,
debugging of tool selection and execution, and correlation of tool
invocations with the agent decisions that triggered them.

<br>

### 9.2 Event Type: `tool.call`

**Stability:** Stable

**Description:** Emitted when a tool, function, API, or external service
invocation completes, whether successfully or with an error. One
`tool.call` event represents one invocation-response cycle.

**REQ-9.2.1:** The `tool.call` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `tool_name` | string | MUST | Name of the tool as recognized by the AI system. |
| `tool_type` | string | MUST | Classification of the tool. One of: `mcp_tool`, `function`, `api`, `database`, `filesystem`, `shell`, `custom`. |
| `status` | string | MUST | Outcome of the invocation. One of: `success`, `error`, `timeout`, `denied`. |
| `invocation` | object | MUST | Invocation details. See REQ-9.2.2. |
| `result` | object | MUST | Result details. See REQ-9.2.3. |
| `tool_version` | string | OPTIONAL | Version of the tool. |
| `tool_provider` | string | OPTIONAL | Provider or server hosting the tool. |
| `latency` | object | OPTIONAL | Timing details per REQ-7.2.7. |
| `error` | object | OPTIONAL | Error details per REQ-7.2.8. MUST be present when `status` is `error`, `timeout`, or `denied`. |
| `triggered_by` | string | OPTIONAL | Event ID of the `agent.step` or `inference.call` that triggered this tool invocation. |
| `side_effects` | object | OPTIONAL | Declared or observed side effects. See REQ-9.2.4. |

**REQ-9.2.2:** The `invocation` object MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `arguments` | object | OPTIONAL | Arguments passed to the tool. Subject to redaction per Section 24. |
| `arguments_hash` | string | OPTIONAL | SHA-256 hash of the canonicalized arguments object. RECOMMENDED when `arguments` is redacted. |
| `argument_count` | integer | OPTIONAL | Number of arguments passed. |

**REQ-9.2.3:** The `result` object MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `output` | any | OPTIONAL | Tool output. Subject to redaction per Section 24. |
| `output_hash` | string | OPTIONAL | SHA-256 hash of the output. RECOMMENDED when `output` is redacted. |
| `output_size_bytes` | integer | OPTIONAL | Size of the output in bytes. |
| `output_truncated` | boolean | OPTIONAL | Whether the output was truncated before inclusion in this event. |

**REQ-9.2.4:** The `side_effects` object, when present, SHOULD contain:

| Field | Type | Description |
|---|---|---|
| `reads` | array of string | Resource categories read by this invocation. |
| `writes` | array of string | Resource categories written by this invocation. |
| `network_egress` | boolean | Whether the invocation made outbound network connections. |
| `state_mutation` | string | One of: `none`, `reversible`, `irreversible`. |

<br>

### 9.3 Event Type: `tool.discovery`

**Stability:** Stable

**Description:** Emitted when a tool discovery, enumeration, or
registration event occurs. This includes MCP `tools/list` calls, OpenAPI
schema retrieval, and dynamic tool registration.

**REQ-9.3.1:** The `tool.discovery` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `discovery_type` | string | MUST | Type of discovery. One of: `enumeration`, `registration`, `deregistration`, `schema_update`, `list_changed`. |
| `source` | string | MUST | Source of the discovery. One of: `mcp_server`, `openapi`, `registry`, `manual`, `custom`. |
| `source_identifier` | string | OPTIONAL | Identifier of the source (e.g., MCP server URL, OpenAPI spec URL). |
| `tool_count` | integer | OPTIONAL | Number of tools discovered or affected. |
| `tools` | array of string | OPTIONAL | Names of tools discovered or affected. |
| `schema_token_count` | integer | OPTIONAL | Total token count of the discovered tool schemas, if measurable. |

<br>

### 9.4 Event Type: `tool.mcp_resource`

**Stability:** Stable

**Description:** Emitted when an MCP resource is accessed. MCP
resources are structured data exposed by MCP servers (distinct from
tools) that provide context to LLM interactions — including files,
database records, API responses, and live system data. Resource access
events enable auditing of what contextual data was consumed by AI
systems.

**REQ-9.4.1:** The `tool.mcp_resource` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `server_name` | string | MUST | Name of the MCP server providing the resource. |
| `server_url` | string | OPTIONAL | URL of the MCP server. Subject to redaction. |
| `resource_uri` | string | MUST | URI of the accessed resource. |
| `resource_name` | string | OPTIONAL | Human-readable name of the resource. |
| `resource_mime_type` | string | OPTIONAL | MIME type of the resource content. |
| `status` | string | MUST | Outcome. One of: `success`, `error`, `not_found`, `denied`. |
| `content_size_bytes` | integer | OPTIONAL | Size of the resource content in bytes. |
| `template_used` | boolean | OPTIONAL | Whether the resource URI was resolved from a resource template. |
| `template_arguments` | object | OPTIONAL | Template arguments used for resolution. Subject to redaction. |
| `latency` | object | OPTIONAL | Timing per REQ-7.2.7. |
| `error` | object | OPTIONAL | Error details per REQ-7.2.8. |

<br>

### 9.5 Event Type: `tool.mcp_sampling`

**Stability:** Stable

**Description:** Emitted when an MCP server initiates a sampling request
— requesting the MCP client (host application) to perform an LLM
inference on the server's behalf. MCP sampling inverts the normal
tool-call flow: the server requests inference from the client. This
event captures the server-initiated request and its outcome.

**REQ-9.5.1:** The `tool.mcp_sampling` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `server_name` | string | MUST | Name of the MCP server that requested sampling. |
| `server_url` | string | OPTIONAL | URL of the MCP server. Subject to redaction. |
| `status` | string | MUST | Outcome. One of: `success`, `error`, `denied`, `timeout`. |
| `model_requested` | string | OPTIONAL | Model hint provided by the server, if any. |
| `model_used` | string | OPTIONAL | Model actually used by the client to fulfill the request. |
| `message_count` | integer | OPTIONAL | Number of messages in the sampling request. |
| `input_token_count` | integer | OPTIONAL | Input tokens for the sampling request. |
| `output_token_count` | integer | OPTIONAL | Output tokens in the sampling response. |
| `max_tokens` | integer | OPTIONAL | Maximum tokens requested by the server. |
| `human_approved` | boolean | OPTIONAL | Whether a human approved the sampling request before execution. |
| `inference_event_id` | string | OPTIONAL | Event ID of the `inference.call` event produced to fulfill this sampling request. |
| `latency` | object | OPTIONAL | Timing per REQ-7.2.7. |
| `error` | object | OPTIONAL | Error details per REQ-7.2.8. |

<br>

## SECTION 10: CATEGORY — MEMORY EVENTS

*Normative*

### 10.1 Purpose

Memory events record read and write operations against memory stores,
knowledge bases, context systems, and retrieval-augmented generation
(RAG) pipelines used by AI systems. Memory strongly influences AI
behavior — what an agent remembers, what context is retrieved, and what
knowledge is persisted directly shapes future inferences and decisions.
Memory events make this influence observable.

<br>

### 10.2 Event Type: `memory.read`

**Stability:** Stable

**Description:** Emitted when an AI system reads from a memory store,
retrieves context, queries a knowledge base, or performs a RAG retrieval
operation.

**REQ-10.2.1:** The `memory.read` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `memory_type` | string | MUST | Classification of the memory store. One of: `vector`, `key_value`, `graph`, `relational`, `conversation_history`, `document`, `custom`. |
| `memory_name` | string | MUST | Name or identifier of the specific memory store. |
| `operation` | string | MUST | Read operation type. One of: `query`, `get`, `search`, `retrieve`, `scan`. |
| `status` | string | MUST | Outcome. One of: `success`, `empty`, `error`, `timeout`. |
| `query` | object | OPTIONAL | Query details. See REQ-10.2.2. |
| `results` | object | OPTIONAL | Result details. See REQ-10.2.3. |
| `latency` | object | OPTIONAL | Timing details per REQ-7.2.7. |
| `error` | object | OPTIONAL | Error details per REQ-7.2.8. Present when `status` is `error` or `timeout`. |
| `triggered_by` | string | OPTIONAL | Event ID of the `agent.step` or `inference.call` that triggered this read. |

**REQ-10.2.2:** The `query` object, when present, SHOULD contain:

| Field | Type | Description |
|---|---|---|
| `query_text` | string | The query string or search text. Subject to redaction. |
| `query_hash` | string | SHA-256 hash of the query text. RECOMMENDED when `query_text` is redacted. |
| `query_type` | string | Query method (e.g., `"similarity"`, `"keyword"`, `"hybrid"`, `"exact_match"`). |
| `top_k` | integer | Number of results requested. |
| `filters` | object | Structured filters applied to the query. Subject to redaction. |
| `namespace` | string | Namespace or collection within the memory store. |

**REQ-10.2.3:** The `results` object, when present, SHOULD contain:

| Field | Type | Description |
|---|---|---|
| `item_count` | integer | Number of items returned. |
| `relevance_scores` | array of number | Relevance or similarity scores for returned items, if applicable. |
| `max_relevance` | number | Highest relevance score among returned items. |
| `min_relevance` | number | Lowest relevance score among returned items. |
| `sources` | array of string | Source identifiers or document references for returned items. Subject to redaction. |
| `total_available` | integer | Total number of matching items in the store, if known. |
| `content_summary` | string | Summary of retrieved content. Subject to redaction. |

<br>

### 10.3 Event Type: `memory.write`

**Stability:** Stable

**Description:** Emitted when an AI system writes to a memory store,
persists context, updates a knowledge base, or stores embeddings.

**REQ-10.3.1:** The `memory.write` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `memory_type` | string | MUST | Classification of the memory store per REQ-10.2.1. |
| `memory_name` | string | MUST | Name or identifier of the specific memory store. |
| `operation` | string | MUST | Write operation type. One of: `insert`, `update`, `upsert`, `delete`, `clear`, `append`. |
| `status` | string | MUST | Outcome. One of: `success`, `error`, `timeout`, `denied`. |
| `item_count` | integer | OPTIONAL | Number of items written, updated, or deleted. |
| `data_summary` | string | OPTIONAL | Summary of the data written. Subject to redaction. |
| `data_hash` | string | OPTIONAL | SHA-256 hash of the written data. |
| `data_size_bytes` | integer | OPTIONAL | Size of the written data in bytes. |
| `namespace` | string | OPTIONAL | Namespace or collection within the memory store. |
| `ttl_seconds` | integer | OPTIONAL | Time-to-live for the written data, if applicable. |
| `latency` | object | OPTIONAL | Timing details per REQ-7.2.7. |
| `error` | object | OPTIONAL | Error details per REQ-7.2.8. |
| `triggered_by` | string | OPTIONAL | Event ID of the `agent.step` or operation that triggered this write. |

<br>

## SECTION 11: CATEGORY — HUMAN INTERACTION EVENTS

*Normative*

### 11.1 Purpose

Human interaction events record the points at which humans engage with
AI systems — providing feedback, confirming actions, responding to
escalations, or intervening in automated workflows. These events close
the loop between AI behavior and human oversight, enabling audit of
human-in-the-loop processes and providing the evidentiary basis for
governance frameworks that require human approval before certain actions
are executed.

### 11.2 Event Type: `human.feedback`

**Stability:** Stable

**Description:** Emitted when a human provides feedback on an AI
system's output, behavior, or decision. Feedback events enable
evaluation, learning, and trust calibration.

**REQ-11.2.1:** The `human.feedback` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `feedback_type` | string | MUST | Classification of the feedback. One of: `rating`, `correction`, `approval`, `rejection`, `annotation`, `preference`, `custom`. |
| `target_event_id` | string | MUST | Event ID of the AILS Event that this feedback applies to. |
| `value` | any | MUST | The feedback value. For `rating`: a number. For `correction`: a string containing the corrected output. For `approval`/`rejection`: a boolean. For `annotation`: a string. For `preference`: an object identifying the preferred option. |
| `comment` | string | OPTIONAL | Human-readable comment explaining the feedback. Subject to redaction. |
| `confidence` | number | OPTIONAL | Confidence in the feedback (0.0 to 1.0), if applicable. |
| `categories` | array of string | OPTIONAL | Categories or dimensions the feedback applies to (e.g., `"accuracy"`, `"helpfulness"`, `"safety"`, `"relevance"`). |

### 11.3 Event Type: `human.confirmation`

**Stability:** Stable

**Description:** Emitted when a human explicitly confirms or denies
authorization for an action that requires human approval. This event
satisfies the confirmation requirements of governance standards
including DAE escalation approval and ACS DESTRUCTIVE capability
confirmation.

**Cross-Standard References:**
- DAE Section 4.7 (Escalation as a First-Class Concept)
- ACS REQ-6.5-L2-001 (Confirmation Event for DESTRUCTIVE capabilities)

**REQ-11.3.1:** The `human.confirmation` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `confirmation_type` | string | MUST | Classification of the confirmation. One of: `action_approval`, `escalation_response`, `destructive_capability`, `policy_override`, `custom`. |
| `target_event_id` | string | MUST | Event ID of the AILS Event requesting confirmation (e.g., the `policy.escalation` or `capability.selected` event). |
| `decision` | string | MUST | The human's decision. One of: `approved`, `denied`, `deferred`. |
| `reason` | string | OPTIONAL | Human-provided reason for the decision. Subject to redaction. |
| `reviewer_id` | string | MUST | Identity of the human who made the confirmation decision. Subject to redaction but MUST be present. |
| `reviewed_at` | string | MUST | ISO 8601 UTC timestamp of the confirmation decision. |
| `expires_at` | string | OPTIONAL | ISO 8601 UTC timestamp after which this confirmation is no longer valid. |
| `scope` | object | OPTIONAL | Scope of the confirmation. See REQ-11.3.2. |

**REQ-11.3.2:** The `scope` object, when present, SHOULD contain:

| Field | Type | Description |
|---|---|---|
| `action` | string | The specific action being confirmed. |
| `resource` | string | The resource the action applies to. |
| `cid` | string | The Capability Identifier, if applicable (ACS integration). |
| `single_use` | boolean | Whether the confirmation is valid for a single execution only. Default: `true`. |

### 11.4 Event Type: `human.escalation_response`

**Stability:** Stable

**Description:** Emitted when a human responds to an escalation request
from an AI system or governance framework. Distinct from
`human.confirmation` in that escalation responses address situations
where the system has halted execution and requires human guidance to
proceed, resume, or terminate.

**Cross-Standard References:**
- DAE Section 4.7 (Escalation as a First-Class Concept)
- ALS REQ-4.4.3 (RESUME Command requires `reviewed_by` field)

**REQ-11.4.1:** The `human.escalation_response` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `escalation_event_id` | string | MUST | Event ID of the `policy.escalation` event that triggered this response. |
| `response_action` | string | MUST | The human's directive. One of: `resume`, `terminate`, `modify_and_resume`, `reassign`, `defer`. |
| `reviewer_id` | string | MUST | Identity of the human respondent. Subject to redaction but MUST be present. |
| `reviewed_at` | string | MUST | ISO 8601 UTC timestamp of the response. |
| `modifications` | object | OPTIONAL | Modifications to apply before resuming. Structure is domain-specific. Subject to redaction. |
| `reason` | string | OPTIONAL | Human-provided reason for the response. Subject to redaction. |
| `authority_granted` | boolean | OPTIONAL | Whether the response grants additional authority beyond the original scope. Default: `false`. |
## SECTION 12: CATEGORY — AUTHORITY EVENTS

*Normative*

### 12.1 Purpose

Authority events record the lifecycle of authority tokens, capabilities,
and enforcement gate decisions within AI systems that implement
capability-based authority models. These events provide the audit trail
necessary to verify that every side-effectful action was explicitly
authorized, that authority was properly scoped and consumed, and that
unauthorized actions were deterministically denied.

Authority events are the primary audit mechanism for implementations of
the Deterministic Agent Execution (DAE) standard.

**Cross-Standard References:**
- DAE Section 4.4 (Authority Tokens / Capabilities)
- DAE Section 4.5 (Enforcement Gate)
- DAE Section 3.2 (Authority Is Explicit and Finite)
- DAE Section 3.4 (Default Deny Is Mandatory)

### 12.2 Event Type: `authority.token_issued`

**Stability:** Stable

**Description:** Emitted when an authority token or capability is issued
to authorize a specific action. Each token issuance represents a
discrete grant of authority bound to a specific intent, plan, action,
and scope.

**REQ-12.2.1:** The `authority.token_issued` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `token_id` | string | MUST | Unique identifier for the authority token. |
| `action_id` | string | MUST | Identifier of the specific action this token authorizes. |
| `intent_hash` | string | OPTIONAL | Cryptographic hash of the intent to which this token is bound. |
| `plan_hash` | string | OPTIONAL | Cryptographic hash of the plan to which this token is bound. |
| `plan_step_index` | integer | OPTIONAL | Index of the plan step this token authorizes. |
| `scope` | object | OPTIONAL | Scope constraints of the authority grant. See REQ-12.2.2. |
| `issued_at` | string | MUST | ISO 8601 UTC timestamp of issuance. |
| `expires_at` | string | MUST | ISO 8601 UTC timestamp of expiry. |
| `issuer` | string | MUST | Identity of the component that issued the token. |

**REQ-12.2.2:** The `scope` object, when present, SHOULD contain:

| Field | Type | Description |
|---|---|---|
| `resource` | string | The resource the token authorizes action upon. |
| `operation` | string | The operation authorized (e.g., `"read"`, `"write"`, `"delete"`, `"execute"`). |
| `constraints` | object | Additional constraints (e.g., maximum value, time window, geographic restriction). |
| `tenant_id` | string | Tenant to which the token is bound, if multi-tenant isolation is active. |

### 12.3 Event Type: `authority.token_consumed`

**Stability:** Stable

**Description:** Emitted when an authority token is consumed by the
successful execution of the action it authorized. A consumed token
cannot be reused.

**REQ-12.3.1:** The `authority.token_consumed` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `token_id` | string | MUST | Identifier of the consumed token. |
| `action_id` | string | MUST | Identifier of the action that consumed the token. |
| `consumed_at` | string | MUST | ISO 8601 UTC timestamp of consumption. |
| `consumed_by` | string | MUST | Identity of the component or agent that consumed the token. |
| `execution_result` | string | MUST | Outcome of the authorized action. One of: `success`, `error`, `partial`. |

### 12.4 Event Type: `authority.token_expired`

**Stability:** Stable

**Description:** Emitted when an authority token expires without being
consumed. Expiry without consumption indicates that the authorized
action was never executed within the token's validity period.

**REQ-12.4.1:** The `authority.token_expired` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `token_id` | string | MUST | Identifier of the expired token. |
| `action_id` | string | MUST | Identifier of the action the token was intended to authorize. |
| `issued_at` | string | MUST | ISO 8601 UTC timestamp of original issuance. |
| `expired_at` | string | MUST | ISO 8601 UTC timestamp of expiry. |
| `reason` | string | OPTIONAL | Reason for non-consumption (e.g., `"timeout"`, `"plan_cancelled"`, `"superseded"`). |

### 12.5 Event Type: `authority.token_revoked`

**Stability:** Stable

**Description:** Emitted when an authority token is explicitly revoked
before expiry. Revocation is a deliberate withdrawal of authority,
distinct from natural expiry.

**Cross-Standard References:**
- DAE Section 3.8 (Runtime Revocation)

**REQ-12.5.1:** The `authority.token_revoked` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `token_id` | string | MUST | Identifier of the revoked token. |
| `action_id` | string | MUST | Identifier of the action the token authorized. |
| `revoked_at` | string | MUST | ISO 8601 UTC timestamp of revocation. |
| `revoked_by` | string | MUST | Identity of the entity that revoked the token. |
| `revocation_reason` | string | MUST | Reason for revocation. One of: `policy_change`, `security_incident`, `scope_violation`, `manual_override`, `session_terminated`, `custom`. |

### 12.6 Event Type: `authority.gate_passed`

**Stability:** Stable

**Description:** Emitted when an enforcement gate successfully validates
an authority token and permits the associated action to proceed.

**Cross-Standard References:**
- DAE Section 4.5 (Enforcement Gate)

**REQ-12.6.1:** The `authority.gate_passed` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `gate_id` | string | MUST | Identifier of the enforcement gate. |
| `token_id` | string | MUST | Identifier of the authority token presented. |
| `action_id` | string | MUST | Identifier of the action being authorized. |
| `validations_performed` | array of string | MUST | List of validation checks performed (e.g., `"token_valid"`, `"binding_verified"`, `"scope_checked"`, `"expiry_checked"`, `"intent_matched"`). |
| `validated_at` | string | MUST | ISO 8601 UTC timestamp of validation. |

### 12.7 Event Type: `authority.gate_denied`

**Stability:** Stable

**Description:** Emitted when an enforcement gate rejects an action
because no valid authority token was presented, the token was invalid,
or the token did not match the requested action. This event represents
a default-deny enforcement.

**Cross-Standard References:**
- DAE Section 3.4 (Default Deny Is Mandatory)
- DAE Section 4.5 (Enforcement Gate)

**REQ-12.7.1:** The `authority.gate_denied` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `gate_id` | string | MUST | Identifier of the enforcement gate. |
| `action_id` | string | MUST | Identifier of the action that was denied. |
| `denial_reason` | string | MUST | Reason for denial. One of: `no_token`, `token_expired`, `token_revoked`, `binding_mismatch`, `scope_exceeded`, `intent_mismatch`, `plan_mismatch`, `tenant_mismatch`, `policy_denied`. |
| `token_id` | string | OPTIONAL | Identifier of the token presented, if any. |
| `denied_at` | string | MUST | ISO 8601 UTC timestamp of denial. |
| `escalation_triggered` | boolean | OPTIONAL | Whether the denial triggered an escalation. Default: `false`. |
## SECTION 13: CATEGORY — SESSION EVENTS

*Normative*

### 13.1 Purpose

Session events record the lifecycle and state transitions of governance
sessions, interaction sessions, and agent sessions. A session represents
a bounded period of operation with defined initialization, active
states, and termination. Session events provide the structural backbone
for correlating all other events that occur within a session boundary.

Session events are the primary audit mechanism for session governance
as defined by the Agent Layer Security (ALS) standard and for planner
sessions as defined by the Agent Capability Standard (ACS).

**Cross-Standard References:**
- ALS Section 4 (ALS Session Model)
- ALS Section 4.2 (Session State Machine)
- ACS Section 8.1 (Runtime Component Model)
- DAE Section 4.3 (Runtime State Machine)

### 13.2 Event Type: `session.initialized`

**Stability:** Stable

**Description:** Emitted when a session is successfully created and
enters its initial operational state. This event establishes the session
context for all subsequent events within the session boundary.

**REQ-13.2.1:** The `session.initialized` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `session_id` | string | MUST | Unique identifier for the session. This value MUST also be present in the `correlation.session_id` field of all subsequent events in this session. |
| `session_type` | string | MUST | Classification of the session. One of: `als_session`, `acs_planner_session`, `dae_execution_session`, `interaction_session`, `agent_session`, `custom`. |
| `session_state` | string | MUST | State the session entered upon initialization. |
| `initiated_by` | string | MUST | Identity of the entity that initiated the session. |
| `initialized_at` | string | MUST | ISO 8601 UTC timestamp of initialization. |
| `configuration` | object | OPTIONAL | Session configuration. See REQ-13.2.2. |
| `enforcement_tier` | integer | OPTIONAL | Enforcement or conformance tier at which the session is operating (e.g., ALS Tier 1/2/3, ACS Level 1/2/3). |
| `commanders` | array of string | OPTIONAL | Identities authorized to command this session (ALS integration). |
| `ttl_seconds` | integer | OPTIONAL | Session credential TTL, if applicable (ALS integration). |

**REQ-13.2.2:** The `configuration` object, when present, MAY contain
session-type-specific configuration parameters. Credential material,
private keys, and authentication secrets MUST NOT be included in the
configuration object.

### 13.3 Event Type: `session.state_changed`

**Stability:** Stable

**Description:** Emitted when a session transitions from one state to
another. State transitions represent significant changes in the
session's operational status and may be triggered by commands,
timeouts, watchdog actions, or internal state machine logic.

**Cross-Standard References:**
- ALS Section 4.2 (Valid State Transitions table)
- DAE Section 4.3 (INITIALIZED → INTENT_SET → PLAN_APPROVED → EXECUTING → ESCALATION_REQUIRED → TERMINATED)

**REQ-13.3.1:** The `session.state_changed` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `session_id` | string | MUST | Session identifier. |
| `previous_state` | string | MUST | State the session was in before the transition. |
| `new_state` | string | MUST | State the session transitioned to. |
| `transition_trigger` | string | MUST | What caused the transition. One of: `command`, `watchdog`, `timeout`, `error`, `initialization`, `completion`, `escalation`, `policy`, `manual`. |
| `triggered_by` | string | MUST | Identity of the entity or component that triggered the transition. |
| `command_id` | string | OPTIONAL | Identifier of the command that triggered the transition, if applicable. |
| `transitioned_at` | string | MUST | ISO 8601 UTC timestamp of the transition. |
| `reason` | string | OPTIONAL | Human-readable reason for the transition. |
| `severity` | string | OPTIONAL | Severity classification of the transition trigger. One of: `advisory`, `operational`, `security`. |
| `resume_possible` | boolean | OPTIONAL | Whether the session can be resumed from its new state. |

**REQ-13.3.2:** The combination of `session_id`, `previous_state`, and
`new_state` MUST represent a valid state transition as defined by the
applicable session governance standard. Implementations SHOULD validate
transitions against the applicable state machine before emitting the
event.

### 13.4 Event Type: `session.terminated`

**Stability:** Stable

**Description:** Emitted when a session is permanently closed with no
possibility of resumption. This is the final event in a session's
lifecycle. No further events with this session ID SHOULD be emitted
after termination, except `system.retention_action` events related to
the session's audit records.

**Cross-Standard References:**
- ALS REQ-4.4.4 (TERMINATE Command)
- ALS REQ-4.1.4 (Session ID reuse prohibition)

**REQ-13.4.1:** The `session.terminated` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `session_id` | string | MUST | Session identifier. |
| `previous_state` | string | MUST | State the session was in before termination. |
| `termination_reason` | string | MUST | Reason for termination. One of: `completed`, `policy_violation`, `security_incident`, `decommission`, `timeout`, `error`, `manual`, `test`. |
| `terminated_at` | string | MUST | ISO 8601 UTC timestamp of termination. |
| `terminated_by` | string | MUST | Identity of the entity that terminated the session. |
| `session_duration_seconds` | number | OPTIONAL | Total duration of the session from initialization to termination, in seconds. |
| `event_count` | integer | OPTIONAL | Total number of AILS events emitted during this session. |
| `command_id` | string | OPTIONAL | Identifier of the termination command, if applicable. |

### 13.5 Event Type: `session.init_failed`

**Stability:** Stable

**Description:** Emitted when a session initialization attempt fails
before the session reaches an operational state.

**Cross-Standard References:**
- ALS REQ-4.3.5 (Commander validation failure)

**REQ-13.5.1:** The `session.init_failed` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `session_type` | string | MUST | Classification of the session that failed to initialize. |
| `initiated_by` | string | MUST | Identity of the entity that attempted initialization. |
| `failure_reason` | string | MUST | Reason for failure (e.g., `"authentication_failed"`, `"commander_not_provisioned"`, `"invalid_configuration"`, `"resource_unavailable"`). |
| `error_code` | string | OPTIONAL | Structured error code, if defined by the applicable standard. |
| `failed_at` | string | MUST | ISO 8601 UTC timestamp of the failure. |
## SECTION 14: CATEGORY — CREDENTIAL EVENTS

*Normative*

### 14.1 Purpose

Credential events record the lifecycle of credentials used by AI
systems, including session credentials, delegation tokens, client
certificates, and API keys. Credential lifecycle events are essential
for security auditing, incident response, and compliance verification
in governed AI deployments.

Credential events are the primary audit mechanism for credential
management as defined by the Agent Layer Security (ALS) standard and
for delegation token lifecycle as defined by the Agent-Based Access
Control (AGBAC) specification.

**Cross-Standard References:**
- ALS Section 5 (Session Credential Protocol)
- ALS Section 5.5 (Credential Renewal Protocol)
- AGBAC Section 5 (Delegation Token Requirements)

### 14.2 Event Type: `credential.issued`

**Stability:** Stable

**Description:** Emitted when a credential is issued to an AI agent,
service, or system component.

**REQ-14.2.1:** The `credential.issued` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `credential_type` | string | MUST | Classification of the credential. One of: `als_session_credential`, `delegation_token`, `client_certificate`, `api_key`, `jwt`, `custom`. |
| `credential_fingerprint` | string | MUST | Cryptographic fingerprint of the credential (e.g., SHA-256 of the certificate or token). MUST NOT contain the credential material itself. |
| `issued_to` | string | MUST | Identity of the entity the credential was issued to. |
| `issued_by` | string | MUST | Identity of the issuing authority. |
| `issued_at` | string | MUST | ISO 8601 UTC timestamp of issuance. |
| `valid_from` | string | MUST | ISO 8601 UTC timestamp from which the credential is valid. |
| `valid_until` | string | MUST | ISO 8601 UTC timestamp at which the credential expires. |
| `issuance_mode` | string | OPTIONAL | Mode of issuance. One of: `keypair`, `csr`, `exchange`, `generated`. (ALS integration.) |
| `session_id` | string | OPTIONAL | Session identifier the credential is bound to, if applicable. |
| `delegation_context` | object | OPTIONAL | Delegation context for delegation tokens. See REQ-14.2.2. |

**REQ-14.2.2:** The `delegation_context` object, when present (AGBAC
integration), SHOULD contain:

| Field | Type | Description |
|---|---|---|
| `agent_subject` | string | Agent identity (`sub` claim). |
| `human_subject` | string | Human principal identity (`act.sub` claim). Subject to redaction. |
| `delegation_method` | string | One of: `explicit`, `implicit`, `system`. |
| `intent_summary` | string | Summary of the delegated intent. Subject to redaction. |
| `granted_at` | string | ISO 8601 UTC timestamp of the delegation grant. |

**REQ-14.2.3:** Credential material — including private keys, bearer
tokens, passwords, and raw certificate content — MUST NOT be included
in credential events. Only fingerprints, identifiers, and metadata
about the credential are permitted.

### 14.3 Event Type: `credential.renewed`

**Stability:** Stable

**Description:** Emitted when a credential is successfully renewed,
extending its validity period with a new credential replacing the
previous one.

**Cross-Standard References:**
- ALS REQ-5.5.4 (CREDENTIAL_RENEWED Audit Event)

**REQ-14.3.1:** The `credential.renewed` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `credential_type` | string | MUST | Classification per REQ-14.2.1. |
| `previous_fingerprint` | string | MUST | Fingerprint of the credential being replaced. |
| `new_fingerprint` | string | MUST | Fingerprint of the newly issued credential. |
| `renewed_at` | string | MUST | ISO 8601 UTC timestamp of renewal. |
| `valid_from` | string | MUST | New validity start. |
| `valid_until` | string | MUST | New validity end. |
| `ttl_remaining_at_renewal` | integer | OPTIONAL | Seconds remaining on the previous credential at the time of renewal. |
| `session_id` | string | OPTIONAL | Session identifier, if applicable. |

### 14.4 Event Type: `credential.refused`

**Stability:** Stable

**Description:** Emitted when a credential renewal or issuance request
is refused by the issuing authority.

**Cross-Standard References:**
- ALS REQ-5.5.3 (Renewal refusal in HALTED state)

**REQ-14.4.1:** The `credential.refused` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `credential_type` | string | MUST | Classification per REQ-14.2.1. |
| `requested_by` | string | MUST | Identity of the entity that requested the credential. |
| `refused_at` | string | MUST | ISO 8601 UTC timestamp of refusal. |
| `refused_by` | string | MUST | Identity of the issuing authority that refused the request. |
| `refusal_reason` | string | MUST | Reason for refusal. One of: `session_halted`, `session_terminated`, `session_paused`, `credential_expired`, `authentication_failed`, `policy_denied`, `rate_limited`, `custom`. |
| `credential_fingerprint_presented` | string | OPTIONAL | Fingerprint of the credential presented with the request, if any. |
| `session_id` | string | OPTIONAL | Session identifier, if applicable. |
| `session_state_at_refusal` | string | OPTIONAL | Session state at the time of refusal (e.g., `"HALTED"`, `"TERMINATED"`). |

### 14.5 Event Type: `credential.expired`

**Stability:** Stable

**Description:** Emitted when a credential expires naturally without
renewal. Credential expiry is the primary enforcement mechanism for
fail-closed governance in ALS deployments.

**Cross-Standard References:**
- ALS Section 5.8 (Credential Expiry Behavior)
- ALS DP-2 (Fail-Closed by Physics)

**REQ-14.5.1:** The `credential.expired` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `credential_type` | string | MUST | Classification per REQ-14.2.1. |
| `credential_fingerprint` | string | MUST | Fingerprint of the expired credential. |
| `issued_to` | string | MUST | Identity of the entity the credential was issued to. |
| `expired_at` | string | MUST | ISO 8601 UTC timestamp of expiry. |
| `session_id` | string | OPTIONAL | Session identifier, if applicable. |
| `renewal_attempted` | boolean | OPTIONAL | Whether a renewal was attempted before expiry. |
| `last_renewal_at` | string | OPTIONAL | ISO 8601 UTC timestamp of the last successful renewal, if any. |
## SECTION 15: CATEGORY — POLICY EVENTS

*Normative*

### 15.1 Purpose

Policy events record authorization decisions, policy violations,
escalation triggers, and governance enforcement actions within AI
systems. These events provide the audit trail necessary to verify that
access control decisions were correctly evaluated, that policy
violations were detected and addressed, and that escalation procedures
were followed.

Policy events serve the authorization audit requirements of DAE, AGBAC,
and ACS.

**Cross-Standard References:**
- DAE Section 3.4 (Default Deny Is Mandatory)
- DAE Section 4.7 (Escalation as a First-Class Concept)
- AGBAC Section 7 (Authorization Model)
- AGBAC Section 11 (Audit and Logging Requirements)
- ACS Section 9 (Planning Authorization Enforcement Point)

### 15.2 Event Type: `policy.decision`

**Stability:** Stable

**Description:** Emitted when an authorization decision is made by a
policy engine, policy enforcement point, or authorization service. This
event records both allow and deny decisions with sufficient detail for
forensic analysis.

**REQ-15.2.1:** The `policy.decision` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `decision` | string | MUST | The authorization outcome. One of: `allow`, `deny`. |
| `policy_type` | string | MUST | Classification of the policy evaluated. One of: `access_control`, `dual_subject`, `capability_authorization`, `data_access`, `rate_limit`, `content_policy`, `custom`. |
| `action` | string | MUST | The action being authorized (e.g., `"read"`, `"write"`, `"execute"`, `"delegate"`). |
| `resource` | string | MUST | The resource the action applies to. |
| `evaluated_at` | string | MUST | ISO 8601 UTC timestamp of the decision. |
| `policy_name` | string | OPTIONAL | Name or identifier of the policy that produced the decision. |
| `policy_version` | string | OPTIONAL | Version of the policy. |
| `reason` | string | OPTIONAL | Human-readable explanation of the decision. |
| `subjects` | object | OPTIONAL | Identity information for the subjects of the decision. See REQ-15.2.2. |
| `evaluation_inputs` | object | OPTIONAL | Inputs to the policy evaluation. See REQ-15.2.3. |

**REQ-15.2.2:** The `subjects` object, when present, SHOULD contain:

| Field | Type | Description |
|---|---|---|
| `primary_subject` | string | Identity of the primary subject (e.g., agent identity). |
| `primary_subject_type` | string | One of: `agent`, `human`, `system`, `external`. |
| `secondary_subject` | string | Identity of the secondary subject (e.g., human principal in AGBAC dual-subject authorization). Subject to redaction. |
| `secondary_subject_type` | string | Type of the secondary subject. |
| `delegation_valid` | boolean | Whether the delegation relationship between subjects is valid (AGBAC integration). |

**REQ-15.2.3:** The `evaluation_inputs` object, when present, SHOULD
contain the attributes evaluated during the policy decision. The
specific fields depend on the `policy_type`:

For `dual_subject` (AGBAC integration):

| Field | Type | Description |
|---|---|---|
| `agent_authorized` | boolean | Whether the agent is authorized for the action. |
| `human_authorized` | boolean | Whether the human is authorized for the action. |
| `delegation_valid` | boolean | Whether the delegated intent is valid and non-expired. |

For `capability_authorization` (ACS integration):

| Field | Type | Description |
|---|---|---|
| `risk_class_match` | boolean | Whether the capability's risk class is within the planner's authorization. |
| `role_match` | boolean | Whether the planner holds an authorized role. |
| `trust_scope_match` | boolean | Whether the planner's trust domain matches the capability's trust scope. |
| `jurisdiction_match` | boolean | Whether jurisdictional constraints are satisfied. |

**REQ-15.2.4:** For AGBAC dual-subject authorization decisions, both
the agent authorization result and the human authorization result MUST
be recorded. A decision event that records only the composite result
without individual subject evaluations is non-conformant for AGBAC
audit requirements.

### 15.3 Event Type: `policy.violation`

**Stability:** Stable

**Description:** Emitted when a policy violation is detected. A
violation is distinct from a deny decision: a deny decision is a
normal authorization outcome; a violation indicates that an action was
attempted that should not have been possible under the system's
governance model, or that a constraint has been breached.

**REQ-15.3.1:** The `policy.violation` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `violation_type` | string | MUST | Classification of the violation. One of: `unauthorized_action`, `scope_exceeded`, `provenance_violation`, `data_classification_breach`, `privilege_escalation`, `state_machine_violation`, `tenant_boundary_crossed`, `custom`. |
| `severity` | string | MUST | Severity of the violation. One of: `low`, `medium`, `high`, `critical`. |
| `policy_name` | string | MUST | Name or identifier of the policy that was violated. |
| `policy_version` | string | OPTIONAL | Version of the policy. |
| `description` | string | MUST | Human-readable description of the violation. |
| `detected_at` | string | MUST | ISO 8601 UTC timestamp of detection. |
| `action_attempted` | string | OPTIONAL | The action that caused the violation. |
| `resource_affected` | string | OPTIONAL | The resource affected by the violation. |
| `action_taken` | string | OPTIONAL | Enforcement action taken in response. One of: `blocked`, `logged`, `alerted`, `session_halted`, `session_terminated`, `escalated`, `none`. |
| `violating_entity` | string | OPTIONAL | Identity of the entity that caused the violation. |

### 15.4 Event Type: `policy.escalation`

**Stability:** Stable

**Description:** Emitted when an escalation is triggered, halting
execution and requiring external (typically human) approval before
the system may proceed. Escalation is a first-class governance concept,
not an error condition.

**Cross-Standard References:**
- DAE Section 4.7 (Escalation as a First-Class Concept)

**REQ-15.4.1:** The `policy.escalation` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `escalation_id` | string | MUST | Unique identifier for this escalation instance. Used to correlate with `human.escalation_response` events. |
| `escalation_reason` | string | MUST | Reason for escalation. One of: `policy_required`, `risk_threshold`, `confidence_low`, `ambiguous_intent`, `destructive_action`, `scope_boundary`, `manual_trigger`, `custom`. |
| `severity` | string | MUST | Severity of the escalation. One of: `low`, `medium`, `high`, `critical`. |
| `escalated_at` | string | MUST | ISO 8601 UTC timestamp of escalation. |
| `execution_state` | string | MUST | State of execution at escalation. One of: `halted`, `paused`, `frozen`. |
| `action_pending` | string | OPTIONAL | Description of the action that is pending approval. |
| `authority_frozen` | boolean | OPTIONAL | Whether authority issuance has been frozen. Default: `true` (DAE integration). |
| `escalation_target` | string | OPTIONAL | Identity or role to which the escalation is directed. |
| `auto_timeout_seconds` | integer | OPTIONAL | Seconds before the escalation auto-resolves (e.g., auto-deny). `null` if no auto-timeout. |
| `session_id` | string | OPTIONAL | Session identifier, if the escalation is within a session context. |
## SECTION 16: CATEGORY — SUPPLY CHAIN EVENTS

*Normative*

### 16.1 Purpose

Supply chain events record the validation, verification, and
enforcement actions related to AI supply chain artifacts — including
models, datasets, embeddings, dependencies, agent logic, tools, and
infrastructure components. These events provide the audit trail
necessary to demonstrate that AI system components are authentic,
unmodified, and authorized for use.

Supply chain events are the primary audit mechanism for implementations
of the AI Supply Chain Standard (AI-SCS).

**Cross-Standard References:**
- AI-SCS Section 5 (ABOM — AI Bill of Materials & Provenance)
- AI-SCS Section 6 (AI Artifact Integrity & Authenticity Assurance)
- AI-SCS Section 7 (Continuous AI Supply Chain Validation)

### 16.2 Event Type: `supplychain.abom_validated`

**Stability:** Stable

**Description:** Emitted when an AI Bill of Materials (ABOM) is
generated, loaded, or validated against the current state of an AI
system's supply chain.

**REQ-16.2.1:** The `supplychain.abom_validated` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `abom_id` | string | MUST | Unique identifier or version of the ABOM. |
| `abom_version` | string | MUST | Version of the ABOM document. |
| `validation_result` | string | MUST | Outcome of validation. One of: `valid`, `invalid`, `partial`, `error`. |
| `validated_at` | string | MUST | ISO 8601 UTC timestamp of validation. |
| `artifact_count` | integer | MUST | Total number of artifacts declared in the ABOM. |
| `artifacts_verified` | integer | OPTIONAL | Number of artifacts that passed verification. |
| `artifacts_failed` | integer | OPTIONAL | Number of artifacts that failed verification. |
| `artifacts_skipped` | integer | OPTIONAL | Number of artifacts not verified (e.g., unreachable). |
| `categories_covered` | array of string | OPTIONAL | AI-SCS asset categories represented in the ABOM (e.g., `"models"`, `"data"`, `"embeddings"`, `"dependencies"`, `"agents"`, `"tools"`, `"infrastructure"`). |
| `deviations` | array of object | OPTIONAL | List of deviations found. See REQ-16.2.2. |

**REQ-16.2.2:** Each entry in the `deviations` array, when present,
MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `artifact_name` | string | MUST | Name of the artifact with a deviation. |
| `artifact_category` | string | MUST | AI-SCS asset category. |
| `deviation_type` | string | MUST | One of: `undeclared`, `hash_mismatch`, `version_mismatch`, `missing`, `unauthorized`, `modified`. |
| `description` | string | OPTIONAL | Human-readable description. |

### 16.3 Event Type: `supplychain.artifact_verified`

**Stability:** Stable

**Description:** Emitted when a specific AI artifact's integrity and
authenticity are verified against its declared trust assertion.

**Cross-Standard References:**
- AI-SCS Section 6 (AI Artifact Integrity & Authenticity Assurance)
- AI-SCS Section 6.3 (Trust Assertions)

**REQ-16.3.1:** The `supplychain.artifact_verified` payload MUST
contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `artifact_name` | string | MUST | Name of the artifact verified. |
| `artifact_category` | string | MUST | AI-SCS asset category (e.g., `"models"`, `"dependencies"`, `"embeddings"`, `"agents"`, `"tools"`). |
| `artifact_version` | string | OPTIONAL | Version of the artifact. |
| `verification_result` | string | MUST | Outcome. One of: `pass`, `fail`, `unsigned`, `untrusted_signer`, `expired`, `error`. |
| `verified_at` | string | MUST | ISO 8601 UTC timestamp of verification. |
| `expected_hash` | string | OPTIONAL | Expected cryptographic hash from the ABOM or trust assertion. |
| `actual_hash` | string | OPTIONAL | Computed hash of the artifact at verification time. |
| `hash_match` | boolean | OPTIONAL | Whether expected and actual hashes match. |
| `signing_entity` | string | OPTIONAL | Identity of the entity that signed the trust assertion. |
| `trust_assertion_valid` | boolean | OPTIONAL | Whether the trust assertion passed all validation checks. |

### 16.4 Event Type: `supplychain.trust_assertion`

**Stability:** Stable

**Description:** Emitted when a trust assertion is created, evaluated,
or its validity status changes.

**REQ-16.4.1:** The `supplychain.trust_assertion` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `assertion_id` | string | MUST | Unique identifier for the trust assertion. |
| `artifact_name` | string | MUST | Name of the artifact the assertion covers. |
| `artifact_category` | string | MUST | AI-SCS asset category. |
| `action` | string | MUST | Trust assertion action. One of: `created`, `verified`, `expired`, `revoked`, `failed_verification`. |
| `signing_entity` | string | MUST | Identity of the signing entity. |
| `signing_timestamp` | string | MUST | ISO 8601 UTC timestamp of the assertion signature. |
| `validity_period_start` | string | OPTIONAL | Start of the assertion's validity period. |
| `validity_period_end` | string | OPTIONAL | End of the assertion's validity period. |
| `abom_reference` | string | OPTIONAL | Reference to the ABOM entry for this artifact. |

### 16.5 Event Type: `supplychain.validation_failure`

**Stability:** Stable

**Description:** Emitted when a runtime supply chain validation
detects a violation — an unauthorized, modified, or undeclared artifact
is detected in the operating environment.

**Cross-Standard References:**
- AI-SCS Section 7.2 (Runtime Validation Objectives)
- AI-SCS Section 7.2.1 (Enforcement Expectations)

**REQ-16.5.1:** The `supplychain.validation_failure` payload MUST
contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `failure_type` | string | MUST | Classification of the failure. One of: `model_substitution`, `dependency_drift`, `unauthorized_tool`, `undeclared_artifact`, `modified_prompt_artifact`, `provenance_mismatch`, `hash_mismatch`, `custom`. |
| `severity` | string | MUST | Severity. One of: `low`, `medium`, `high`, `critical`. |
| `artifact_name` | string | MUST | Name of the affected artifact. |
| `artifact_category` | string | MUST | AI-SCS asset category. |
| `detected_at` | string | MUST | ISO 8601 UTC timestamp of detection. |
| `description` | string | MUST | Human-readable description of the failure. |
| `enforcement_action` | string | MUST | Action taken. One of: `blocked`, `disabled`, `reverted`, `failed_closed`, `alerted`, `logged`, `none`. |
| `expected_state` | string | OPTIONAL | Expected state of the artifact (e.g., expected hash). |
| `observed_state` | string | OPTIONAL | Observed state of the artifact (e.g., computed hash). |
## SECTION 17: CATEGORY — INTEGRITY EVENTS

*Normative*

### 17.1 Purpose

Integrity events record the verification of MCP tool and server
integrity — including sealed manifest verification, interface
fingerprint comparison, artifact digest checking, version lineage
validation, and trust-on-first-use (TOFU) operations. These events
provide the audit trail for MCP tool supply chain integrity.

Integrity events are the primary audit mechanism for implementations
of the MCP Integrity Standard.

**Cross-Standard References:**
- MCP Integrity Standard Section 18 (Verification)
- MCP Integrity Standard Section 19 (Client Behavior and Policy Enforcement)
- MCP Integrity Standard Section 8 (Tool Interface Fingerprinting)
- MCP Integrity Standard Section 12 (Version Lineage)

### 17.2 Event Type: `integrity.manifest_verified`

**Stability:** Stable

**Description:** Emitted when a Sealed Manifest is fetched and verified
for an MCP server. This event records the overall verification outcome
including signature validation, trust level determination, and any
errors or advisories.

**REQ-17.2.1:** The `integrity.manifest_verified` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `server_name` | string | MUST | Name of the MCP server. |
| `server_version` | string | OPTIONAL | Version of the MCP server. |
| `publisher_namespace` | string | OPTIONAL | Publisher namespace from the manifest. |
| `verification_result` | string | MUST | Overall outcome. One of: `pass`, `fail`, `degraded`, `not_found`. |
| `trust_level` | integer | MUST | Determined trust level (0–3). |
| `claimed_level` | integer | OPTIONAL | Trust level claimed by the server. |
| `verified_at` | string | MUST | ISO 8601 UTC timestamp of verification. |
| `verification_trigger` | string | MUST | What triggered the verification. One of: `install`, `list`, `update`, `periodic`, `manual`. |
| `attestations_verified` | array of string | OPTIONAL | Attestation roles with valid signatures (e.g., `"publisher"`, `"registry"`, `"scanner"`, `"auditor"`). |
| `errors` | array of object | OPTIONAL | Blocking errors encountered. See REQ-17.2.2. |
| `advisories` | array of object | OPTIONAL | Non-blocking warnings. See REQ-17.2.2. |

**REQ-17.2.2:** Each entry in the `errors` and `advisories` arrays,
when present, MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `code` | string | MUST | Error or advisory code as defined by the MCP Integrity Standard Section 22 (e.g., `"SIGNATURE_INVALID"`, `"CHAIN_BROKEN"`, `"LEVEL_CLAIMED_NOT_MET"`). |
| `message` | string | OPTIONAL | Human-readable description. |
| `tool_name` | string | OPTIONAL | Affected tool name, if the error is tool-specific. |

### 17.3 Event Type: `integrity.fingerprint_checked`

**Stability:** Stable

**Description:** Emitted when interface fingerprints for one or more
MCP tools are computed and compared against manifest values or cached
TOFU values.

**Cross-Standard References:**
- MCP Integrity Standard Section 8 (Tool Interface Fingerprinting)

**REQ-17.3.1:** The `integrity.fingerprint_checked` payload MUST
contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `server_name` | string | MUST | Name of the MCP server. |
| `tool_name` | string | MUST | Name of the tool whose fingerprint was checked. |
| `result` | string | MUST | Outcome. One of: `match`, `mismatch`, `error`. |
| `checked_at` | string | MUST | ISO 8601 UTC timestamp. |
| `comparison_source` | string | MUST | What the fingerprint was compared against. One of: `sealed_manifest`, `tofu_cache`, `registry`. |
| `component_results` | object | OPTIONAL | Per-component match results. See REQ-17.3.2. |

**REQ-17.3.2:** The `component_results` object, when present, SHOULD
contain:

| Field | Type | Description |
|---|---|---|
| `description_match` | boolean | Whether the description digest matched. |
| `input_schema_match` | boolean | Whether the input schema digest matched. |
| `annotations_match` | boolean | Whether the annotations digest matched. `null` if no annotations declared. |
| `output_schema_match` | boolean | Whether the output schema digest matched. `null` if no output schema declared. |
| `composite_match` | boolean | Whether the composite digest matched. |

### 17.4 Event Type: `integrity.chain_validated`

**Stability:** Stable

**Description:** Emitted when a version lineage chain is validated
across tool versions.

**Cross-Standard References:**
- MCP Integrity Standard Section 12 (Version Lineage)

**REQ-17.4.1:** The `integrity.chain_validated` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `server_name` | string | MUST | Name of the MCP server. |
| `tool_name` | string | MUST | Name of the tool. |
| `current_version` | string | MUST | Current tool version. |
| `previous_version` | string | OPTIONAL | Previous version in the chain. `null` for first versions. |
| `result` | string | MUST | Outcome. One of: `valid`, `chain_broken`, `chain_absent`, `error`. |
| `validated_at` | string | MUST | ISO 8601 UTC timestamp. |
| `changes_declared` | array of object | OPTIONAL | Change classifications from the lineage. See REQ-17.4.2. |

**REQ-17.4.2:** Each entry in the `changes_declared` array, when
present, SHOULD contain:

| Field | Type | Description |
|---|---|---|
| `type` | string | Change type (e.g., `"bugfix"`, `"feature"`, `"security"`, `"breaking"`). |
| `interface_changed` | boolean | Whether the tool's interface fingerprints changed. |
| `capabilities_changed` | boolean | Whether required capabilities changed. |
| `side_effects_changed` | boolean | Whether side-effect declarations changed. |

### 17.5 Event Type: `integrity.tofu_recorded`

**Stability:** Stable

**Description:** Emitted when a tool's interface fingerprint is cached
on first encounter in TOFU (Trust On First Use) mode.

**REQ-17.5.1:** The `integrity.tofu_recorded` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `server_name` | string | MUST | Name of the MCP server. |
| `tool_name` | string | MUST | Name of the tool. |
| `composite_digest` | string | MUST | The composite interface fingerprint that was cached. |
| `recorded_at` | string | MUST | ISO 8601 UTC timestamp. |

### 17.6 Event Type: `integrity.tofu_changed`

**Stability:** Stable

**Description:** Emitted when a subsequent interface fingerprint check
detects a mismatch against a previously cached TOFU value.

**REQ-17.6.1:** The `integrity.tofu_changed` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `server_name` | string | MUST | Name of the MCP server. |
| `tool_name` | string | MUST | Name of the tool. |
| `expected_digest` | string | MUST | Previously cached composite digest. |
| `observed_digest` | string | MUST | Newly computed composite digest. |
| `detected_at` | string | MUST | ISO 8601 UTC timestamp of detection. |
| `component_changes` | object | OPTIONAL | Per-component change details per REQ-17.3.2. |
| `user_action` | string | OPTIONAL | Action taken by the user or policy. One of: `accepted`, `blocked`, `pending`. |
## SECTION 18: CATEGORY — CAPABILITY EVENTS

*Normative*

### 18.1 Purpose

Capability events record the lifecycle of capability governance in
AI planning systems — including capability discovery, planning
authorization decisions, capability selection, execution contract
management, and capability execution. These events provide the audit
trail for verifying that capability exposure is governed by
authorization policy, that planners see only authorized capabilities,
and that execution is bound to explicit contracts.

Capability events are the primary audit mechanism for implementations
of the Agent Capability Standard (ACS).

**Cross-Standard References:**
- ACS Section 7 (Capability Summary Index)
- ACS Section 8 (ACS Runtime Requirements)
- ACS Section 9 (Planning Authorization Enforcement Point)
- ACS Section 10 (Execution Contract Specification)
- ACS Section 11 (Audit Logging Requirements)

### 18.2 Event Type: `capability.index_delivered`

**Stability:** Stable

**Description:** Emitted when a Capability Summary Index is constructed
and delivered to a planner interface.

**Cross-Standard References:**
- ACS REQ-8.4-L1-001 (Index construction sequence)

**REQ-18.2.1:** The `capability.index_delivered` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `session_token` | string | MUST | Planner session token. |
| `capability_count` | integer | MUST | Number of capabilities included in the index. |
| `total_token_count` | integer | OPTIONAL | Estimated token count of the serialized index. |
| `delivered_at` | string | MUST | ISO 8601 UTC timestamp. |
| `domain_tags` | array of string | OPTIONAL | Domain tags represented in the index. |
| `truncated` | boolean | OPTIONAL | Whether the index was truncated due to token budget constraints. |
| `capabilities_excluded_count` | integer | OPTIONAL | Number of capabilities excluded by PAEP authorization (Level 2+). MUST NOT reveal which specific capabilities were excluded. |

### 18.3 Event Type: `capability.paep_evaluated`

**Stability:** Stable

**Description:** Emitted when the Planning Authorization Enforcement
Point (PAEP) evaluates whether a capability may be exposed to a
planner. One event is emitted per capability per evaluation.

**Cross-Standard References:**
- ACS Section 9 (PAEP)
- ACS REQ-9.4-L2-004 (All evaluations logged)

**REQ-18.3.1:** The `capability.paep_evaluated` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `cid` | string | MUST | Capability Identifier evaluated. |
| `session_token` | string | MUST | Planner session token. |
| `decision` | string | MUST | Authorization outcome. One of: `granted`, `denied`. |
| `evaluated_at` | string | MUST | ISO 8601 UTC timestamp. |
| `evaluation_context` | string | MUST | When the evaluation occurred. One of: `index_construction`, `manifest_retrieval`, `contract_issuance`, `execution`. |
| `evaluation_inputs` | object | OPTIONAL | Inputs to the evaluation. See REQ-18.3.2. |

**REQ-18.3.2:** The `evaluation_inputs` object, when present, SHOULD
contain:

| Field | Type | Description |
|---|---|---|
| `planner_sub` | string | Planner identity identifier. |
| `risk_class` | string | Capability risk class evaluated. |
| `risk_class_match` | boolean | Whether risk class is within planner authorization. |
| `role_match` | boolean | Whether planner holds an authorized role. |
| `trust_scope_match` | boolean | Whether trust domain matches. |
| `jurisdiction_match` | boolean | Whether jurisdictional constraints are satisfied. |

**REQ-18.3.3:** The `evaluation_inputs` object MUST NOT contain the
full capability manifest content. Only the evaluation attributes and
their match results are recorded.

### 18.4 Event Type: `capability.manifest_delivered`

**Stability:** Stable

**Description:** Emitted when a full Capability Manifest is delivered
to the planner interface in response to an on-demand retrieval request.

**REQ-18.4.1:** The `capability.manifest_delivered` payload MUST
contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `cid` | string | MUST | Capability Identifier of the delivered manifest. |
| `session_token` | string | MUST | Planner session token. |
| `risk_class` | string | MUST | Risk classification of the capability. |
| `semantic_quality` | string | MUST | Semantic quality designation (`OPERATIONAL` or `OUTCOME`). |
| `delivered_at` | string | MUST | ISO 8601 UTC timestamp. |

### 18.5 Event Type: `capability.selected`

**Stability:** Stable

**Description:** Emitted when a planner selects a capability by CID,
triggering execution contract issuance.

**REQ-18.5.1:** The `capability.selected` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `cid` | string | MUST | Selected Capability Identifier. |
| `session_token` | string | MUST | Planner session token. |
| `risk_class` | string | MUST | Risk classification of the selected capability. |
| `confirmation_required` | boolean | MUST | Whether the capability requires confirmation before execution. |
| `selected_at` | string | MUST | ISO 8601 UTC timestamp. |

### 18.6 Event Type: `capability.contract_issued`

**Stability:** Stable

**Description:** Emitted when an Execution Contract is issued for a
selected capability.

**Cross-Standard References:**
- ACS Section 10 (Execution Contract Specification)

**REQ-18.6.1:** The `capability.contract_issued` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `contract_id` | string | MUST | Unique identifier of the Execution Contract. |
| `cid` | string | MUST | Capability Identifier the contract is bound to. |
| `session_token` | string | MUST | Planner session token. |
| `execution_intent_hash` | string | MUST | Execution Intent Hash per ACS Section 11.3. |
| `issued_at` | string | MUST | ISO 8601 UTC timestamp. |
| `expires_at` | string | MUST | ISO 8601 UTC timestamp of contract expiry. |
| `provider_ref` | string | OPTIONAL | Opaque reference to the resolved provider. |

### 18.7 Event Type: `capability.executed`

**Stability:** Stable

**Description:** Emitted when a capability is executed against the
source system via a valid Execution Contract.

**REQ-18.7.1:** The `capability.executed` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `contract_id` | string | MUST | Identifier of the Execution Contract used. |
| `cid` | string | MUST | Capability Identifier executed. |
| `session_token` | string | MUST | Planner session token. |
| `execution_intent_hash` | string | MUST | Execution Intent Hash. |
| `status` | string | MUST | Execution outcome. One of: `success`, `error`, `timeout`, `denied`. |
| `executed_at` | string | MUST | ISO 8601 UTC timestamp. |
| `state_mutation_type` | string | OPTIONAL | State mutation produced. One of: `none`, `reversible`, `irreversible`. |
| `provider_ref` | string | OPTIONAL | Opaque provider reference. |
| `latency` | object | OPTIONAL | Timing details per REQ-7.2.7. |
| `error` | object | OPTIONAL | Error details per REQ-7.2.8. Present when `status` is `error`, `timeout`, or `denied`. |

### 18.8 Event Type: `capability.execution_failed`

**Stability:** Stable

**Description:** Emitted when a capability execution fails due to
contract expiry, PAEP re-evaluation denial, parameter validation
failure, or source system error.

**REQ-18.8.1:** The `capability.execution_failed` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `contract_id` | string | OPTIONAL | Identifier of the Execution Contract, if one was active. |
| `cid` | string | MUST | Capability Identifier. |
| `session_token` | string | MUST | Planner session token. |
| `failure_reason` | string | MUST | Reason for failure. One of: `contract_expired`, `paep_denied`, `parameter_invalid`, `source_unreachable`, `source_error`, `confirmation_missing`, `custom`. |
| `failed_at` | string | MUST | ISO 8601 UTC timestamp. |
| `error` | object | OPTIONAL | Error details per REQ-7.2.8. |
## SECTION 19: CATEGORY — CONTROL EVENTS

*Normative*

### 19.1 Purpose

Control events record operations on control channels, command
processing, watchdog activity, and push notification delivery within
AI governance infrastructure. These events provide the audit trail for
verifying that governance commands were received, authenticated,
executed, and acknowledged.

Control events are the primary audit mechanism for the control channel,
watchdog, and push notification components of the Agent Layer Security
(ALS) standard.

**Cross-Standard References:**
- ALS Section 8 (Control Channel Protocol)
- ALS Section 9 (Watchdog Mechanism)
- ALS Section 10 (Push Notification Protocol)

### 19.2 Event Type: `control.command_received`

**Stability:** Stable

**Description:** Emitted when a command is received on a control
channel from an authorized or unauthorized commander.

**Cross-Standard References:**
- ALS Section 8.3 (Command Schema)
- ALS Section 12.1 (COMMAND_RECEIVED audit event)

**REQ-19.2.1:** The `control.command_received` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `command_id` | string | MUST | Unique identifier of the command. |
| `command_type` | string | MUST | Type of command. One of: `halt`, `pause`, `resume`, `terminate`, `query`, `custom`. |
| `session_id` | string | MUST | Target session identifier. |
| `commander_id` | string | MUST | Identity of the issuing commander. |
| `received_at` | string | MUST | ISO 8601 UTC timestamp of receipt. |
| `command_result` | string | MUST | Processing outcome. One of: `success`, `rejected`, `error`. |
| `rejection_reason` | string | OPTIONAL | Reason for rejection, if applicable. One of: `replay_detected`, `invalid_signature`, `unauthorized`, `invalid_session`, `capability_not_supported`, `rate_limited`, `timestamp_invalid`, `custom`. |
| `signature_valid` | boolean | OPTIONAL | Whether the command signature was verified. |

### 19.3 Event Type: `control.command_executed`

**Stability:** Stable

**Description:** Emitted when a validated command is executed and
produces a state transition.

**REQ-19.3.1:** The `control.command_executed` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `command_id` | string | MUST | Identifier of the executed command. |
| `command_type` | string | MUST | Type of command executed. |
| `session_id` | string | MUST | Target session identifier. |
| `commander_id` | string | MUST | Identity of the commanding entity. |
| `executed_at` | string | MUST | ISO 8601 UTC timestamp of execution. |
| `resulting_state` | string | MUST | Session state after command execution. |
| `halt_reason` | string | OPTIONAL | Reason for halt. Present when `command_type` is `halt`. |
| `halt_severity` | string | OPTIONAL | Severity. One of: `advisory`, `operational`, `security`. Present when `command_type` is `halt`. |
| `resume_possible` | boolean | OPTIONAL | Whether the session may be resumed. Present when `command_type` is `halt`. |
| `termination_reason` | string | OPTIONAL | Reason for termination. Present when `command_type` is `terminate`. |
| `termination_class` | string | OPTIONAL | Classification. One of: `policy_violation`, `security_incident`, `decommission`, `test`. Present when `command_type` is `terminate`. |
| `reviewed_by` | string | OPTIONAL | Identity of the human reviewer who authorized the command. Present when `command_type` is `resume`. |

### 19.4 Event Type: `control.channel_connected`

**Stability:** Stable

**Description:** Emitted when a control channel connection is
established between a commander and the governance service.

**REQ-19.4.1:** The `control.channel_connected` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `commander_id` | string | MUST | Identity of the connecting commander. |
| `connected_at` | string | MUST | ISO 8601 UTC timestamp. |
| `authentication_method` | string | OPTIONAL | Authentication method used (e.g., `"mtls"`, `"commander_certificate"`). |

### 19.5 Event Type: `control.channel_disconnected`

**Stability:** Stable

**Description:** Emitted when a control channel connection is lost or
closed.

**Cross-Standard References:**
- ALS REQ-8.7.3 (CONTROL_CHANNEL_DISCONNECTED)

**REQ-19.5.1:** The `control.channel_disconnected` payload MUST
contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `commander_id` | string | MUST | Identity of the disconnected commander. |
| `disconnected_at` | string | MUST | ISO 8601 UTC timestamp. |
| `disconnect_reason` | string | MUST | Reason for disconnection. One of: `normal_close`, `network_error`, `authentication_failed`, `timeout`, `server_shutdown`, `unknown`. |
| `reconnected_at` | string | OPTIONAL | ISO 8601 UTC timestamp of reconnection, if reconnection occurred before this event was emitted. |

### 19.6 Event Type: `control.channel_auth_failed`

**Stability:** Stable

**Description:** Emitted when a control channel authentication attempt
fails.

**Cross-Standard References:**
- ALS REQ-8.2.4 (CONTROL_CHANNEL_AUTH_FAILED)

**REQ-19.6.1:** The `control.channel_auth_failed` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `source_ip` | string | MUST | Source IP address of the failed connection. |
| `presented_certificate_fingerprint` | string | OPTIONAL | Fingerprint of the certificate presented, if any. |
| `failure_reason` | string | MUST | Reason for failure. One of: `certificate_invalid`, `certificate_expired`, `certificate_unknown`, `certificate_revoked`, `no_certificate`, `custom`. |
| `failed_at` | string | MUST | ISO 8601 UTC timestamp. |

### 19.7 Event Type: `control.watchdog_triggered`

**Stability:** Stable

**Description:** Emitted when a watchdog timer fires due to missed
credential renewal within the configured watchdog window.

**Cross-Standard References:**
- ALS Section 9 (Watchdog Mechanism)
- ALS REQ-9.3.1 (SESSION_WATCHDOG_HALT)

**REQ-19.7.1:** The `control.watchdog_triggered` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `session_id` | string | MUST | Session identifier. |
| `last_renewal_at` | string | MUST | ISO 8601 UTC timestamp of the last successful credential renewal. |
| `watchdog_window_seconds` | integer | MUST | Configured watchdog window in seconds. |
| `watchdog_multiplier` | number | MUST | Configured watchdog multiplier. |
| `triggered_at` | string | MUST | ISO 8601 UTC timestamp of the trigger. |
| `resulting_state` | string | MUST | Session state after the watchdog halt (typically `"HALTED"`). |
| `agent_identity` | string | OPTIONAL | Identity of the governed agent. |

### 19.8 Event Type: `control.push_notification_sent`

**Stability:** Stable

**Description:** Emitted when a push notification is sent to a governed
agent via the push notification channel.

**Cross-Standard References:**
- ALS Section 10 (Push Notification Protocol)

**REQ-19.8.1:** The `control.push_notification_sent` payload MUST
contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `notification_id` | string | MUST | Unique identifier for the notification. |
| `session_id` | string | MUST | Session identifier. |
| `notification_type` | string | MUST | Type of notification. One of: `session_state_change`, `credential_expiring`, `session_resumed`, `session_terminated`. |
| `new_state` | string | OPTIONAL | New session state, if applicable. |
| `effective_in_seconds` | integer | OPTIONAL | Seconds remaining before enforcement takes effect. |
| `sent_at` | string | MUST | ISO 8601 UTC timestamp. |
| `push_channel_active` | boolean | MUST | Whether the push notification channel was active at send time. |

### 19.9 Event Type: `control.push_notification_acked`

**Stability:** Stable

**Description:** Emitted when a push notification is acknowledged by
the governed agent.

**REQ-19.9.1:** The `control.push_notification_acked` payload MUST
contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `notification_id` | string | MUST | Identifier of the acknowledged notification. |
| `session_id` | string | MUST | Session identifier. |
| `acknowledged_at` | string | MUST | ISO 8601 UTC timestamp of acknowledgment. |
| `acknowledgment_latency_ms` | integer | OPTIONAL | Time in milliseconds between notification send and acknowledgment. |

### 19.10 Event Type: `control.push_notification_unacked`

**Stability:** Stable

**Description:** Emitted when a push notification is not acknowledged
within the required timeout period.

**Cross-Standard References:**
- ALS REQ-10.3.3 (HALT_NOTIFICATION_UNACKNOWLEDGED)

**REQ-19.10.1:** The `control.push_notification_unacked` payload MUST
contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `notification_id` | string | MUST | Identifier of the unacknowledged notification. |
| `session_id` | string | MUST | Session identifier. |
| `timeout_seconds` | integer | MUST | Acknowledgment timeout that was exceeded. |
| `detected_at` | string | MUST | ISO 8601 UTC timestamp when the timeout was detected. |
| `notification_type` | string | MUST | Type of the unacknowledged notification. |
| `severity` | string | OPTIONAL | Severity of the unacknowledged notification (e.g., `"high"` for security-severity halt notifications). |

### 19.11 Event Type: `control.conflicting_commands`

**Stability:** Stable

**Description:** Emitted when two commanders issue conflicting commands
for the same session and the governance service applies the most
restrictive command.

**Cross-Standard References:**
- ALS REQ-8.6.3 (CONFLICTING_COMMANDS_RECEIVED)

**REQ-19.11.1:** The `control.conflicting_commands` payload MUST
contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `session_id` | string | MUST | Session identifier. |
| `command_id_1` | string | MUST | First command identifier. |
| `commander_id_1` | string | MUST | First commander identity. |
| `command_type_1` | string | MUST | First command type. |
| `command_id_2` | string | MUST | Second command identifier. |
| `commander_id_2` | string | MUST | Second commander identity. |
| `command_type_2` | string | MUST | Second command type. |
| `resolution` | string | MUST | Which command was applied (e.g., `"halt_over_resume"`, `"terminate_over_halt"`). |
| `resolved_at` | string | MUST | ISO 8601 UTC timestamp. |

### 19.12 Event Type: `control.replay_detected`

**Stability:** Stable

**Description:** Emitted when a replayed command is detected and
rejected.

**Cross-Standard References:**
- ALS REQ-8.5.1 (Replay Window)

**REQ-19.12.1:** The `control.replay_detected` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `command_id` | string | MUST | Identifier of the replayed command. |
| `commander_id` | string | MUST | Identity of the presenting commander. |
| `original_nonce` | string | MUST | The nonce that was previously processed. |
| `detected_at` | string | MUST | ISO 8601 UTC timestamp. |
## SECTION 20: CATEGORY — AGENT-TO-AGENT PROTOCOL EVENTS

*Normative*

### 20.1 Purpose

Agent-to-Agent (A2A) protocol events record interactions between AI
agents communicating over the A2A protocol (Google A2A, version 1.0 and
later). A2A defines a standard for agent interoperability including
task delegation, status reporting, artifact exchange, and capability
discovery via Agent Cards. A2A events make cross-agent interactions
observable, auditable, and correlatable with the internal behavior of
each participating agent.

A2A events are distinct from `agent.delegation` events (Section 8.4)
in that they capture protocol-level interactions — task lifecycle,
message exchange, Agent Card discovery, and A2A-specific authentication
— rather than abstract agent-to-agent delegation semantics.

**Protocol References:**
- Google Agent-to-Agent Protocol (A2A), RC v1.0

### 20.2 Event Type: `a2a.task_created`

**Stability:** Stable

**Description:** Emitted when an A2A task is created by a sending agent
(client) on a receiving agent (server). This event records the
initiation of a cross-agent work unit.

**REQ-20.2.1:** The `a2a.task_created` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `task_id` | string | MUST | A2A task identifier as assigned by the receiving agent. |
| `sending_agent_id` | string | MUST | Identity of the agent that initiated the task. |
| `receiving_agent_id` | string | MUST | Identity of the agent that received and will execute the task. |
| `receiving_agent_url` | string | OPTIONAL | URL of the receiving agent's A2A endpoint. Subject to redaction. |
| `task_description` | string | OPTIONAL | Description or summary of the task. Subject to redaction. |
| `created_at` | string | MUST | ISO 8601 UTC timestamp of task creation. |
| `initial_status` | string | MUST | Initial task status. Typically `"submitted"`. |
| `delegation_depth` | integer | OPTIONAL | Depth in the delegation chain (1 = direct delegation). |
| `parent_task_id` | string | OPTIONAL | Task ID of the parent task, if this task was created as a sub-task. |
| `skills_requested` | array of string | OPTIONAL | Skill identifiers from the receiving agent's Agent Card that the task targets. |

### 20.3 Event Type: `a2a.task_status_changed`

**Stability:** Stable

**Description:** Emitted when an A2A task transitions from one status
to another. A2A defines the following task statuses: `submitted`,
`working`, `input-required`, `completed`, `failed`, `canceled`.

**REQ-20.3.1:** The `a2a.task_status_changed` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `task_id` | string | MUST | A2A task identifier. |
| `previous_status` | string | MUST | Task status before the transition. |
| `new_status` | string | MUST | Task status after the transition. One of: `submitted`, `working`, `input-required`, `completed`, `failed`, `canceled`. |
| `transitioned_at` | string | MUST | ISO 8601 UTC timestamp of the transition. |
| `sending_agent_id` | string | MUST | Identity of the client agent. |
| `receiving_agent_id` | string | MUST | Identity of the server agent. |
| `status_message` | string | OPTIONAL | Human-readable status message from the receiving agent. Subject to redaction. |
| `has_artifacts` | boolean | OPTIONAL | Whether the status update includes artifacts (output data). |
| `artifact_count` | integer | OPTIONAL | Number of artifacts included, if any. |
| `error` | object | OPTIONAL | Error details per REQ-7.2.8. Present when `new_status` is `failed`. |

### 20.4 Event Type: `a2a.message_sent`

**Stability:** Stable

**Description:** Emitted when an agent sends a message to a remote
agent over the A2A protocol. This includes task creation messages,
additional input messages, and task update messages.

**REQ-20.4.1:** The `a2a.message_sent` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `task_id` | string | MUST | A2A task identifier the message is associated with. |
| `message_id` | string | MUST | Unique identifier for this message. |
| `direction` | string | MUST | Always `"outbound"` for sent messages. |
| `recipient_agent_id` | string | MUST | Identity of the receiving agent. |
| `recipient_url` | string | OPTIONAL | URL of the recipient's A2A endpoint. Subject to redaction. |
| `sent_at` | string | MUST | ISO 8601 UTC timestamp. |
| `message_type` | string | MUST | Classification of the message. One of: `task_send`, `task_send_subscribe`, `task_get`, `task_cancel`, `input_provided`, `custom`. |
| `content_parts_count` | integer | OPTIONAL | Number of content parts in the message. |
| `content_types` | array of string | OPTIONAL | MIME types of the content parts (e.g., `"text/plain"`, `"application/json"`, `"image/png"`). |
| `content_summary` | string | OPTIONAL | Summary of the message content. Subject to redaction. |
| `has_artifacts` | boolean | OPTIONAL | Whether the message includes artifacts. |

### 20.5 Event Type: `a2a.message_received`

**Stability:** Stable

**Description:** Emitted when an agent receives a message from a remote
agent over the A2A protocol.

**REQ-20.5.1:** The `a2a.message_received` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `task_id` | string | MUST | A2A task identifier the message is associated with. |
| `message_id` | string | MUST | Unique identifier for this message. |
| `direction` | string | MUST | Always `"inbound"` for received messages. |
| `sender_agent_id` | string | MUST | Identity of the sending agent. |
| `sender_url` | string | OPTIONAL | URL of the sender's A2A endpoint. Subject to redaction. |
| `received_at` | string | MUST | ISO 8601 UTC timestamp. |
| `message_type` | string | MUST | Classification per REQ-20.4.1. |
| `content_parts_count` | integer | OPTIONAL | Number of content parts in the message. |
| `content_types` | array of string | OPTIONAL | MIME types of the content parts. |
| `content_summary` | string | OPTIONAL | Summary of the message content. Subject to redaction. |
| `has_artifacts` | boolean | OPTIONAL | Whether the message includes artifacts. |
| `streaming` | boolean | OPTIONAL | Whether the message was received via SSE streaming. Default: `false`. |

### 20.6 Event Type: `a2a.agent_card_discovered`

**Stability:** Stable

**Description:** Emitted when an A2A Agent Card is fetched from a
remote agent's `/.well-known/agent.json` endpoint, parsed, and its
capabilities evaluated.

**REQ-20.6.1:** The `a2a.agent_card_discovered` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `agent_card_url` | string | MUST | URL from which the Agent Card was fetched. |
| `remote_agent_name` | string | MUST | Name of the remote agent as declared in the Agent Card. |
| `remote_agent_id` | string | OPTIONAL | Identity of the remote agent. |
| `discovery_result` | string | MUST | Outcome of the discovery. One of: `success`, `fetch_failed`, `parse_error`, `authentication_required`, `not_found`. |
| `discovered_at` | string | MUST | ISO 8601 UTC timestamp. |
| `skills_count` | integer | OPTIONAL | Number of skills declared in the Agent Card. |
| `skills` | array of string | OPTIONAL | Skill identifiers declared in the Agent Card. |
| `supported_content_types` | array of string | OPTIONAL | Content types the remote agent supports. |
| `authentication_required` | boolean | OPTIONAL | Whether the Agent Card indicates authentication is required. |
| `authentication_schemes` | array of string | OPTIONAL | Authentication schemes supported by the remote agent (e.g., `"bearer"`, `"oauth2"`, `"apiKey"`). |
| `a2a_version` | string | OPTIONAL | A2A protocol version supported by the remote agent. |

### 20.7 Event Type: `a2a.authentication`

**Stability:** Stable

**Description:** Emitted when an A2A authentication event occurs —
including successful authentication, authentication failure, and
credential exchange between agents.

**REQ-20.7.1:** The `a2a.authentication` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `direction` | string | MUST | Authentication direction. One of: `outbound` (authenticating to a remote agent), `inbound` (validating a remote agent's credentials). |
| `remote_agent_id` | string | MUST | Identity of the remote agent. |
| `remote_agent_url` | string | OPTIONAL | URL of the remote agent. Subject to redaction. |
| `authentication_scheme` | string | MUST | Scheme used. One of: `bearer`, `oauth2`, `api_key`, `mtls`, `custom`. |
| `result` | string | MUST | Outcome. One of: `success`, `failed`, `expired`, `revoked`. |
| `authenticated_at` | string | MUST | ISO 8601 UTC timestamp. |
| `failure_reason` | string | OPTIONAL | Reason for failure. Present when `result` is not `success`. |
| `token_type` | string | OPTIONAL | Type of token used (e.g., `"jwt"`, `"opaque"`). |

## SECTION 21: CATEGORY — SYSTEM EVENTS

*Normative*

### 21.1 Purpose

System events record infrastructure health, alert conditions,
configuration changes, logging degradation, and retention actions. These
events provide operational awareness of the AILS logging infrastructure
itself and of the systems that produce AILS events. System events
ensure that the observability and audit system is itself observable.

### 21.2 Event Type: `system.alert`

**Stability:** Stable

**Description:** Emitted when an alert condition defined in Section 27
is triggered. Alert events provide a durable record of conditions that
required operator attention.

**REQ-21.2.1:** The `system.alert` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `alert_name` | string | MUST | Name or identifier of the alert condition. |
| `alert_source` | string | MUST | Component or standard that defined the alert condition (e.g., `"ails"`, `"als"`, `"acs"`, `"custom"`). |
| `severity` | string | MUST | Severity. One of: `low`, `medium`, `high`, `critical`. |
| `description` | string | MUST | Human-readable description of the alert condition. |
| `triggered_at` | string | MUST | ISO 8601 UTC timestamp. |
| `triggering_event_ids` | array of string | OPTIONAL | Event IDs of the AILS Events that triggered the alert. |
| `triggering_pattern` | string | OPTIONAL | Description of the event pattern that triggered the alert. |
| `alert_destination` | string | OPTIONAL | Where the alert was delivered (e.g., `"webhook"`, `"syslog"`, `"smtp"`, `"pagerduty"`). |
| `auto_action_taken` | string | OPTIONAL | Automated action taken in response. One of: `none`, `session_halted`, `capability_disabled`, `escalation_triggered`, `custom`. |

### 21.3 Event Type: `system.health`

**Stability:** Stable

**Description:** Emitted periodically or on-demand to record the health
status of an AILS Emitter, logging infrastructure component, or
monitored AI system component.

**REQ-21.3.1:** The `system.health` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `component_name` | string | MUST | Name of the component reporting health. |
| `component_type` | string | MUST | Type of component (e.g., `"emitter"`, `"log_pipeline"`, `"audit_store"`, `"als_service"`, `"acs_runtime"`, `"renewal_endpoint"`). |
| `status` | string | MUST | Health status. One of: `healthy`, `degraded`, `unhealthy`, `unknown`. |
| `checked_at` | string | MUST | ISO 8601 UTC timestamp. |
| `details` | object | OPTIONAL | Component-specific health details. |
| `metrics` | object | OPTIONAL | Health metrics. See REQ-21.3.2. |

**REQ-21.3.2:** The `metrics` object, when present, MAY contain:

| Field | Type | Description |
|---|---|---|
| `events_emitted_total` | integer | Total events emitted since last health check or since startup. |
| `events_failed_total` | integer | Total events that failed to emit. |
| `buffer_utilization_percent` | number | Percentage of audit event buffer currently in use. |
| `average_latency_ms` | number | Average event emission latency. |
| `error_rate_percent` | number | Error rate as a percentage. |
| `uptime_seconds` | number | Time since component startup. |

### 21.4 Event Type: `system.configuration_changed`

**Stability:** Stable

**Description:** Emitted when a configuration change is detected in an
AILS Emitter, logging pipeline, or monitored component that affects
logging behavior, redaction profiles, retention policies, or
conformance level.

**REQ-21.4.1:** The `system.configuration_changed` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `component_name` | string | MUST | Name of the component whose configuration changed. |
| `change_type` | string | MUST | Classification of the change. One of: `redaction_profile`, `retention_policy`, `conformance_level`, `emitter_config`, `alert_config`, `sampling_rate`, `custom`. |
| `previous_value` | string | OPTIONAL | Previous configuration value. MUST NOT contain credentials or secrets. |
| `new_value` | string | OPTIONAL | New configuration value. MUST NOT contain credentials or secrets. |
| `changed_at` | string | MUST | ISO 8601 UTC timestamp. |
| `changed_by` | string | OPTIONAL | Identity of the entity that made the change. |

### 21.5 Event Type: `system.log_degraded`

**Stability:** Stable

**Description:** Emitted when logging infrastructure degradation is
detected — including write failures, buffer overflow conditions,
transport unavailability, or latency exceeding acceptable thresholds.

**Cross-Standard References:**
- AILS DP-10 (Fail-Safe Logging)
- ACS REQ-11.5-L2-002 (Halt on buffer overflow)
- ALS REQ-12.4.1 (Audit log write failure — CRITICAL alert)

**REQ-21.5.1:** The `system.log_degraded` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `degradation_type` | string | MUST | Type of degradation. One of: `write_failure`, `buffer_overflow`, `transport_unavailable`, `latency_exceeded`, `storage_full`, `custom`. |
| `severity` | string | MUST | Severity. One of: `medium`, `high`, `critical`. |
| `component_name` | string | MUST | Name of the affected logging component. |
| `detected_at` | string | MUST | ISO 8601 UTC timestamp. |
| `description` | string | OPTIONAL | Human-readable description. |
| `events_affected` | integer | OPTIONAL | Number of events affected by the degradation (e.g., events lost, events buffered). |
| `buffer_utilization_percent` | number | OPTIONAL | Current buffer utilization. |
| `action_taken` | string | OPTIONAL | Response to the degradation. One of: `buffering`, `sampling`, `halted_execution`, `alert_sent`, `none`. |
| `resolved_at` | string | OPTIONAL | ISO 8601 UTC timestamp when degradation was resolved. `null` if ongoing. |

### 21.6 Event Type: `system.retention_action`

**Stability:** Stable

**Description:** Emitted when a retention policy action is executed on
AILS Events — including archival, deletion, or export of events that
have reached the end of their retention period.

**REQ-21.6.1:** The `system.retention_action` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `action` | string | MUST | Retention action performed. One of: `archived`, `deleted`, `exported`, `extended`. |
| `retention_policy` | string | MUST | Name or identifier of the retention policy applied. |
| `events_affected` | integer | MUST | Number of events affected. |
| `date_range_start` | string | MUST | Start of the date range of affected events (ISO 8601). |
| `date_range_end` | string | MUST | End of the date range of affected events (ISO 8601). |
| `executed_at` | string | MUST | ISO 8601 UTC timestamp of the retention action. |
| `executed_by` | string | OPTIONAL | Identity of the entity or automated process that executed the action. |
| `session_ids_affected` | array of string | OPTIONAL | Session identifiers of affected events, if applicable. |
| `reason` | string | OPTIONAL | Reason for the retention action (e.g., `"retention_period_expired"`, `"regulatory_requirement"`, `"storage_reclamation"`). |

### 21.7 Event Type: `system.budget_exceeded`

**Stability:** Stable

**Description:** Emitted when a cost, token, or resource budget
threshold is crossed. Budget events enable proactive cost governance
and prevent runaway spending in AI systems. This event is distinct from
quota exhaustion (hard limit) — budget thresholds are warning boundaries
that may or may not block further operations depending on policy.

**REQ-21.7.1:** The `system.budget_exceeded` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `budget_type` | string | MUST | Type of budget. One of: `token`, `cost`, `inference_calls`, `tool_calls`, `time`, `custom`. |
| `budget_scope` | string | MUST | Scope of the budget. One of: `request`, `session`, `agent`, `tenant`, `global`. |
| `budget_limit` | number | MUST | Configured budget limit value. |
| `current_value` | number | MUST | Current consumed value at the time of threshold crossing. |
| `threshold_percent` | number | MUST | Threshold percentage that was crossed (e.g., `80`, `90`, `100`). |
| `exceeded_at` | string | MUST | ISO 8601 UTC timestamp. |
| `currency` | string | OPTIONAL | ISO 4217 currency code, if `budget_type` is `cost`. |
| `action_taken` | string | MUST | Response to the threshold crossing. One of: `alert_only`, `throttled`, `blocked`, `none`. |
| `scope_identifier` | string | OPTIONAL | Identifier of the scoped entity (session ID, agent ID, tenant ID). |

### 21.8 Event Type: `system.quota_exhausted`

**Stability:** Stable

**Description:** Emitted when a hard rate limit or quota is exhausted,
preventing further operations until the quota resets or is increased.
This event captures provider-side rate limits, organizational quotas,
and system-imposed limits.

**REQ-21.8.1:** The `system.quota_exhausted` payload MUST contain:

| Field | Type | Required | Description |
|---|---|---|---|
| `quota_type` | string | MUST | Type of quota. One of: `rate_limit`, `daily_limit`, `token_limit`, `request_limit`, `concurrency_limit`, `custom`. |
| `quota_source` | string | MUST | Source imposing the quota. One of: `provider`, `gateway`, `organization`, `system`, `custom`. |
| `provider` | string | OPTIONAL | Provider that imposed the limit, if `quota_source` is `provider`. |
| `quota_limit` | number | MUST | The quota limit value. |
| `current_value` | number | OPTIONAL | Current consumed value. |
| `exhausted_at` | string | MUST | ISO 8601 UTC timestamp. |
| `reset_at` | string | OPTIONAL | ISO 8601 UTC timestamp when the quota resets, if known. |
| `retry_after_seconds` | integer | OPTIONAL | Seconds to wait before retrying, if provided by the source. |
| `affected_operations` | array of string | OPTIONAL | Types of operations affected (e.g., `"inference"`, `"embedding"`, `"tool_calls"`). |
| `action_taken` | string | MUST | Response. One of: `queued`, `retrying`, `failed`, `fallback`, `alert_only`. |

## SECTION 22: INTEGRITY AND TAMPER-EVIDENCE REQUIREMENTS

*Normative*

### 22.1 Purpose

This section defines the graduated integrity model that enables AILS
events to serve as tamper-evident audit records. The integrity model
scales from no integrity overhead at Level 1 to cryptographically signed,
hash-chained events at Level 3. The integrity mechanisms defined here
satisfy the tamper-evidence requirements of ALS, ACS, and other
standards that mandate auditable, verifiable event records.

### 22.2 Integrity Levels Overview

| Level | Integrity Mechanism | Use Case |
|---|---|---|
| Level 1 | None. Structured telemetry only. | Developer observability, debugging, cost tracking. |
| Level 2 | Sequence numbering and chain hashing. | Enterprise audit trails, compliance logging, governance records. |
| Level 3 | Level 2 plus cryptographic event signing. | Regulatory compliance, forensic evidence, high-risk AI system audit. |

### 22.3 Level 1: Structured Telemetry

**REQ-22.3.1:** At Level 1, the `integrity` field of the AILS Event
Envelope MAY be absent.

**REQ-22.3.2:** At Level 1, events are not required to be tamper-evident.
Events are structured, typed, and correlatable, but no cryptographic
guarantees are provided.

**REQ-22.3.3:** Level 1 events MUST still conform to the full Event
Envelope specification (Section 5). The absence of integrity fields
does not exempt events from other envelope requirements.

### 22.4 Level 2: Tamper-Evident Events

**REQ-22.4.1:** `[Level 2+]` Every AILS Event MUST include the
`integrity` object containing `sequence_number`, `stream_id`, and
`chain_hash` fields.

#### 22.4.1 Sequence Numbering

**REQ-22.4.2:** `[Level 2+]` The `sequence_number` field MUST be a
positive integer starting at 1 for the first event in an event stream
and incrementing by exactly 1 for each subsequent event in the same
stream.

**REQ-22.4.3:** `[Level 2+]` A gap in sequence numbers within a single
event stream indicates that one or more events have been lost, deleted,
or failed to emit. Consumers SHOULD treat sequence gaps as integrity
anomalies and SHOULD log a `system.alert` event when a gap is detected.

**REQ-22.4.4:** `[Level 2+]` Sequence numbers MUST NOT be reused within
the same `stream_id`. If an event stream is terminated (e.g., session
ended), a new stream with a new `stream_id` MUST be created for
subsequent events.

#### 22.4.2 Event Stream Scope

**REQ-22.4.5:** `[Level 2+]` The `stream_id` field MUST uniquely
identify the scope within which sequence numbering and chain hashing
are maintained. The following stream scopes are defined:

| Stream Scope | Description | Recommended Use |
|---|---|---|
| Per-session | One stream per governance session (ALS session, ACS planner session, DAE execution session). | Security-critical deployments governed by ALS, ACS, or DAE. |
| Per-emitter | One stream per emitter instance. | General-purpose enterprise logging where session boundaries are not applicable. |
| Per-tenant | One stream per tenant. | Multi-tenant deployments where tenant-level integrity is required. |

**REQ-22.4.6:** `[Level 2+]` The stream scope used by an implementation
MUST be documented in the implementation's conformance claim. Mixed
stream scopes within a single deployment are permitted but each stream
MUST maintain independent sequence numbering and chain hashing.

**REQ-22.4.7:** `[Level 2+]` When a stream scope is per-session, the
`stream_id` SHOULD be identical to the `correlation.session_id`. When
the stream scope is per-emitter, the `stream_id` SHOULD be identical
to the `emitter.id`.

#### 22.4.3 Chain Hash Construction

**REQ-22.4.8:** `[Level 2+]` The `chain_hash` field MUST be computed
as the SHA-256 hash of the concatenation of:

1. The `chain_hash` value of the immediately preceding event in the
   same event stream
2. The `event_id` of the current event
3. The `timestamp` of the current event

The concatenation MUST use the colon character (`:`) as a delimiter:

```
chain_hash = SHA-256(previous_chain_hash + ":" + event_id + ":" + timestamp)
```

**REQ-22.4.9:** `[Level 2+]` For the first event in an event stream
(sequence_number = 1), the `chain_hash` MUST be computed using the
`stream_id` as the predecessor value:

```
chain_hash = SHA-256(stream_id + ":" + event_id + ":" + timestamp)
```

**REQ-22.4.10:** `[Level 2+]` The `chain_hash` value MUST be
represented as a lowercase hexadecimal string prefixed with `sha256:`
(e.g., `"sha256:a1b2c3d4..."`).

**REQ-22.4.11:** `[Level 2+]` An implementation MUST be capable of
verifying the chain hash integrity of an event stream on demand.
Verification MUST detect any modification, insertion, deletion, or
reordering of events in the stream.

**REQ-22.4.12:** `[Level 2+]` Chain hash verification failure MUST
result in a `system.alert` event with severity `critical` and alert
name `chain_hash_integrity_failure`.

### 22.5 Level 3: Signed Events

**REQ-22.5.1:** `[Level 3]` Every AILS Event MUST include, in addition
to the Level 2 integrity fields, the `event_signature`,
`signing_key_id`, and `signing_algorithm` fields within the `integrity`
object.

**REQ-22.5.2:** `[Level 3]` The `event_signature` field MUST contain a
cryptographic signature computed over the canonical serialization of the
AILS Event, excluding the `integrity.event_signature` field itself. The
canonical serialization MUST use JSON Canonicalization Scheme (RFC 8785).

**REQ-22.5.3:** `[Level 3]` The signing algorithm MUST be one of:

| Algorithm | Identifier | Requirement |
|---|---|---|
| Ed25519 (EdDSA over Curve25519) | `Ed25519` | REQUIRED to support |
| ES256 (ECDSA over P-256) | `ES256` | RECOMMENDED to support |

**REQ-22.5.4:** `[Level 3]` Implementations MUST NOT use RSA, MD5,
SHA-1, DSA, or any algorithm not listed in REQ-22.5.3 for event signing.

**REQ-22.5.5:** `[Level 3]` The `event_signature` value MUST be encoded
as a base64url string (RFC 4648 §5) without padding.

**REQ-22.5.6:** `[Level 3]` The `signing_key_id` MUST uniquely identify
the signing key within the deployment. Key management, rotation, and
revocation procedures are the responsibility of the deploying
organization. Key management guidance is provided in Appendix D.

**REQ-22.5.7:** `[Level 3]` Signature verification failure on any event
MUST result in a `system.alert` event with severity `critical` and
alert name `event_signature_verification_failure`.

### 22.6 Integrity and Event Emission Ordering

**REQ-22.6.1:** `[Level 2+]` Events within a single event stream MUST
be emitted in sequence number order. An Emitter MUST NOT emit event N+1
before event N has been assigned its sequence number, chain hash, and
(at Level 3) signature.

**REQ-22.6.2:** `[Level 2+]` If an Emitter cannot compute the chain
hash for an event because the previous event's chain hash is unknown
(e.g., due to a process restart), the Emitter MUST start a new event
stream with a new `stream_id` and sequence number starting at 1. The
Emitter MUST emit a `system.alert` event with alert name
`event_stream_discontinuity` in the new stream.

### 22.7 Integrity and Storage

**REQ-22.7.1:** `[Level 2+]` AILS Events MUST be written to persistent
storage before the operation they record is considered complete. For
audit events associated with security standards (ALS, DAE, ACS), the
event MUST be persisted before the corresponding command response or
enforcement action is returned to the requestor.

**REQ-22.7.2:** `[Level 2+]` The audit event store MUST be implemented
using one of the following tamper-evidence mechanisms:

- **Append-only storage:** A write-once storage system that prohibits
  modification or deletion of written events.
- **Hash-chain verification:** The chain hash mechanism defined in
  Section 22.4.3, with periodic integrity verification.
- **External log signing:** Events signed by the Emitter per
  Section 22.5 and verifiable by any party holding the public key.

**REQ-22.7.3:** `[Level 2+]` The audit event store MUST NOT be
accessible for modification or deletion by the AI agents, governed
agents, or planners whose behavior the events record. Audit
infrastructure MUST require separate authentication credentials from
the systems it monitors.
## SECTION 23: PROVENANCE AND DATA CLASSIFICATION

*Normative*

### 23.1 Purpose

This section defines the provenance classification system for AILS
Events. Provenance classification enables downstream systems — including
policy engines, security monitors, and audit tools — to evaluate the
trustworthiness of event sources without inspecting event payloads. The
provenance system directly supports the data provenance enforcement
requirements of the Deterministic Agent Execution (DAE) standard.

**Cross-Standard References:**
- DAE Section 4.6 (Provenance Enforcement)
- DAE Section 3.3 (Data Is Never Authority)

### 23.2 Classification Values

**REQ-23.2.1:** The `data_classification` field defined in Section 5.7
MUST contain one of the following values. These values are exhaustive;
no other values are permitted.

**SYSTEM_TRUSTED**

The event was produced by a component operating within the trusted
computing boundary of the deployment. The Emitter is a system component
whose identity is verified, whose code is controlled by the deployment
operator, and whose behavior is deterministic with respect to the event
it records.

Examples of SYSTEM_TRUSTED sources:
- Security enforcement points (DAE enforcement gates, ALS services,
  PAEP components)
- Credential issuance and renewal services
- Policy decision points
- System health monitors
- Configuration management systems

**USER_TRUSTED**

The event was produced in response to an authenticated user action
within established trust boundaries. The user's identity has been
verified, but the content of the user's action is not independently
validated by the system.

Examples of USER_TRUSTED sources:
- Authenticated API calls from known users
- Human-approved agent actions
- Human feedback and confirmation events
- Authenticated administrative actions

**EXTERNAL_UNTRUSTED**

The event was produced in response to, or contains data derived from,
an external, unauthenticated, or unverified source. The content of the
event may be influenced by data that has not been validated by any
trusted component.

Examples of EXTERNAL_UNTRUSTED sources:
- Events triggered by external API responses
- Events containing web-scraped or retrieved content
- Events containing data from unauthenticated inputs
- Events where the input includes content from external documents,
  emails, or user-provided files whose content is not verified
- RAG retrieval results from external knowledge bases
- Tool call results from external services

### 23.3 Classification Rules

**REQ-23.3.1:** The `data_classification` MUST be assigned by the
Emitter at event creation time based on the trust level of the source
that triggered the event.

**REQ-23.3.2:** The `data_classification` MUST NOT be modified after
the event is produced. Reclassification of an event's provenance is
prohibited.

**REQ-23.3.3:** When an event is triggered by a combination of trusted
and untrusted inputs, the Emitter MUST assign the least trusted
classification that applies. The precedence order from least trusted
to most trusted is:

1. `EXTERNAL_UNTRUSTED`
2. `USER_TRUSTED`
3. `SYSTEM_TRUSTED`

An event influenced by any EXTERNAL_UNTRUSTED input MUST be classified
as EXTERNAL_UNTRUSTED, regardless of other inputs.

**REQ-23.3.4:** When the trust level of an event source cannot be
determined, the Emitter MUST assign `EXTERNAL_UNTRUSTED`.

### 23.4 Provenance and Authority

**REQ-23.4.1:** AILS Events classified as `EXTERNAL_UNTRUSTED` MUST
NOT be used as the sole basis for authority decisions in systems
implementing DAE. This requirement is enforced by the consuming
governance system, not by AILS itself. AILS provides the classification;
DAE enforces the authority boundary.

**REQ-23.4.2:** Policy engines and governance systems consuming AILS
Events SHOULD use the `data_classification` field to filter, weight,
or flag events based on provenance before incorporating event data into
decision-making processes.

### 23.5 Provenance in Multi-Hop Workflows

**REQ-23.5.1:** When an AILS Event is produced by a component that
received its input from another component's AILS Event, the downstream
event MUST carry a `data_classification` no more trusted than the
upstream event's classification.

**REQ-23.5.2:** An Emitter that processes the output of an
EXTERNAL_UNTRUSTED event MUST classify its own resulting event as
EXTERNAL_UNTRUSTED, even if the Emitter itself is a SYSTEM_TRUSTED
component. The provenance of the data, not the identity of the
processor, determines the classification.

[INFORMATIVE] Example: An ALS service (SYSTEM_TRUSTED) that processes
a credential renewal request triggered by an agent acting on external
data classifies the session event based on the session's trust context,
not on the ALS service's own trust level. The ALS service's own
operational events (e.g., watchdog triggers, health checks) are
SYSTEM_TRUSTED. Events recording agent-initiated actions influenced by
external data are classified based on the data's provenance.
## SECTION 24: PRIVACY, REDACTION, AND SENSITIVE DATA

*Normative*

### 24.1 Purpose

This section defines the requirements for handling sensitive data within
AILS Events. AI telemetry inherently involves sensitive content —
including user prompts, model outputs, personally identifiable
information, reasoning chains, and credentials. AILS provides a
normative framework for redaction that ensures events remain valid,
parseable, and correlatable after sensitive fields are removed.

### 24.2 Sensitive Data Categories

**REQ-24.2.1:** The following categories of data are classified as
sensitive within AILS and are subject to the redaction requirements
of this section:

| Category | Description | Examples |
|---|---|---|
| `prompt_content` | User or system prompts submitted to AI models. | `inference.call` payload `input.content` |
| `completion_content` | AI model outputs and completions. | `inference.call` payload `output.content` |
| `reasoning_content` | Agent reasoning, chain-of-thought, and internal deliberation. | `agent.step` payload `reasoning.content` |
| `pii` | Personally identifiable information. | Names, email addresses, phone numbers, government identifiers, biometric data, IP addresses. |
| `credential_material` | Authentication credentials, tokens, keys, and secrets. | API keys, bearer tokens, private keys, passwords. |
| `tool_arguments` | Arguments passed to tool invocations. | `tool.call` payload `invocation.arguments` |
| `tool_output` | Results returned by tool invocations. | `tool.call` payload `result.output` |
| `memory_content` | Content read from or written to memory stores. | `memory.read` payload `results.content_summary`, `memory.write` payload `data_summary` |
| `human_identity` | Human user identifiers and identity attributes. | `actor.human_identity.subject`, `human.confirmation` payload `reviewer_id` |
| `business_data` | Business-sensitive data in event payloads. | Financial figures, customer records, proprietary information in tool outputs. |

### 24.3 Default Redaction Requirements

**REQ-24.3.1:** The following fields MUST be redactable in every
conformant AILS implementation. The implementation MUST provide a
configuration mechanism to enable or disable redaction of each field
independently:

- `inference.call` → `input.content`
- `inference.call` → `output.content`
- `agent.step` → `reasoning.content`
- `agent.step` → `reasoning.summary`
- `agent.step` → `goal`
- `agent.step` → `decision.description`
- `tool.call` → `invocation.arguments`
- `tool.call` → `result.output`
- `memory.read` → `query.query_text`
- `memory.read` → `results.content_summary`
- `memory.write` → `data_summary`
- `human.feedback` → `comment`
- `human.feedback` → `value` (when type is `correction`)

**REQ-24.3.2:** Credential material — including private keys, bearer
tokens, API keys, passwords, and raw certificate content — MUST NOT
appear in AILS Events under any configuration. This prohibition is
absolute and is not subject to redaction profile configuration. There
is no configuration option that enables credential material in events.

**REQ-24.3.3:** Raw prompts and completions MUST be redacted by default.
Implementations MUST require explicit opt-in configuration to include
raw prompt and completion content in events. The default redaction
profile MUST redact all `prompt_content` and `completion_content`
fields.

**REQ-24.3.4:** Agent reasoning content (`reasoning.content`) MUST NOT
be required by any conformance level. Chain-of-thought and internal
reasoning are sensitive intellectual property and safety-relevant data.
The `reasoning.disclosure` value `hidden` is always a conformant state.

### 24.4 Redaction Methods

**REQ-24.4.1:** When a field is redacted, the implementation MUST apply
one of the following methods and MUST record the method in the
`redaction.redaction_method` field of the event envelope:

| Method | Replacement Value | Use Case |
|---|---|---|
| `omitted` | `"[REDACTED]"` | Complete removal of sensitive content. Default method. |
| `hashed` | `"sha256:<hex_digest>"` | Content replaced with a SHA-256 hash, enabling matching without content disclosure. |
| `truncated` | First N characters followed by `"...[TRUNCATED]"` | Partial content preserved for debugging while limiting exposure. |
| `masked` | Structure-preserving replacement (e.g., `"u***@***.com"`) | PII fields where format is useful but content is sensitive. |

**REQ-24.4.2:** When the `hashed` method is used, the hash MUST be
computed over the UTF-8 encoding of the original field value. The hash
algorithm MUST be SHA-256. The hash MUST be represented as a lowercase
hexadecimal string prefixed with `sha256:`.

**REQ-24.4.3:** When the `truncated` method is used, the implementation
MUST preserve no more than the first 100 characters of the original
value by default. The truncation length MUST be configurable.

### 24.5 Redaction Profiles

**REQ-24.5.1:** Implementations MUST support named redaction profiles
that define which fields are redacted and by which method. The following
profiles are defined by this standard:

| Profile Name | Description |
|---|---|
| `default` | Redacts prompt content, completion content, reasoning content, PII, and tool arguments. Retains structural metadata, token counts, and correlation fields. |
| `minimal` | Redacts only credential material. All other content is retained. For development and debugging use only. |
| `strict` | Redacts all sensitive data categories defined in REQ-24.2.1. Retains only structural metadata necessary for correlation and analysis. |
| `regulatory` | Extends `strict` with additional redaction of human identity fields beyond reviewer IDs. For deployments subject to GDPR, HIPAA, or equivalent regulations. |

**REQ-24.5.2:** Custom redaction profiles MAY be defined by
implementations. Custom profiles MUST NOT weaken the absolute
prohibition on credential material (REQ-24.3.2).

**REQ-24.5.3:** The active redaction profile MUST be recorded in the
`redaction.profile` field of every event to which redaction has been
applied.

### 24.6 Redaction and Event Validity

**REQ-24.6.1:** An AILS Event with all sensitive fields redacted MUST
remain a valid, parseable event conforming to the Event Envelope
specification. Redaction MUST NOT remove required envelope fields or
required payload fields that are non-sensitive (e.g., `status`,
`event_type`, timestamps, identifiers).

**REQ-24.6.2:** When a required payload field is redacted, the field
MUST still be present in the event with its redaction replacement value
(e.g., `"[REDACTED]"`). The field MUST NOT be absent.

**REQ-24.6.3:** The `redaction.redacted_fields` array MUST list all
fields that have been redacted, using JSON path notation
(e.g., `"payload.input.content"`, `"actor.human_identity.subject"`).

### 24.7 Redaction and Integrity

**REQ-24.7.1:** `[Level 2+]` Chain hashing and event signing MUST be
computed over the event after redaction has been applied. The integrity
mechanisms protect the redacted event, not the original content. This
ensures that a redacted event cannot be modified after redaction without
detection.

**REQ-24.7.2:** `[Level 2+]` When the `hashed` redaction method is used,
the hash of the original content provides a binding between the redacted
event and the original content. If the original content is retained in
a separate secure store, the hash enables verification that the redacted
event corresponds to the retained content.

### 24.8 Prompt and Completion Logging Guidance

[INFORMATIVE] Organizations that choose to retain raw prompt and
completion content should consider the following:

- Raw content significantly increases storage costs and log volume.
- Raw content may contain PII from users that triggers regulatory
  obligations (GDPR, CCPA, HIPAA).
- Raw content in audit logs may be discoverable in legal proceedings.
- Token counts, model parameters, finish reasons, and error codes
  provide substantial diagnostic value without raw content.
- The `hashed` redaction method enables prompt/completion matching
  (e.g., detecting identical prompts across sessions) without
  retaining content.
## SECTION 25: RETENTION REQUIREMENTS

*Normative*

### 25.1 Purpose

This section defines the minimum retention periods for AILS Events.
Retention requirements ensure that audit trails, governance records, and
telemetry data are available for forensic analysis, compliance
verification, and incident response for the duration required by
applicable standards and regulations.

### 25.2 Retention Tiers

**REQ-25.2.1:** AILS defines three retention tiers. The applicable tier
is determined by the event's category, the conformance level of the
emitting system, and any regulatory classification applied to the
deployment.

| Retention Tier | Minimum Retention Period | Applicable Events |
|---|---|---|
| Standard | 90 days from event emission | Level 1 telemetry events: `inference.*`, `agent.*`, `tool.*`, `memory.*`, `human.*` |
| Governance | 3 years from session termination | Level 2+ audit events: `authority.*`, `session.*`, `credential.*`, `policy.*`, `supplychain.*`, `integrity.*`, `capability.*`, `control.*` |
| Regulatory | 10 years from session termination | Events associated with EU AI Act high-risk AI systems, or deployments subject to regulatory retention requirements exceeding the Governance tier. |

**REQ-25.2.2:** Deployers MUST retain events for the longer of the AILS
minimum retention period or any applicable regulatory requirement.

**REQ-25.2.3:** `system.*` events MUST be retained for at least the
Governance tier period (3 years) at Level 2 and above, and for at least
the Standard tier period (90 days) at Level 1.

### 25.3 Retention and Session Boundaries

**REQ-25.3.1:** For events associated with a governance session
(ALS session, ACS planner session, DAE execution session), the
retention period begins at session termination, not at event emission.
All events within a session MUST be retained for the full retention
period measured from the session's `session.terminated` event timestamp.

**REQ-25.3.2:** For events not associated with a session, the retention
period begins at event emission timestamp.

**REQ-25.3.3:** When a session spans multiple retention tiers (e.g., a
session contains both telemetry events and audit events), all events
in the session MUST be retained for the longest applicable retention
period.

### 25.4 Retention Actions

**REQ-25.4.1:** When events reach the end of their retention period, the
implementation MUST emit a `system.retention_action` event before
deleting, archiving, or exporting the affected events.

**REQ-25.4.2:** Retention actions MUST NOT be executed by any process
that is accessible to the AI agents, governed agents, or planners whose
behavior the events record. Retention actions are an administrative
function performed by storage management processes with independent
credentials.

**REQ-25.4.3:** Implementations SHOULD support retention action types of
`archived` (moved to long-term storage), `exported` (copied to an
external system), and `deleted` (permanently removed). The `extended`
action type indicates that the retention period was extended beyond the
standard minimum.

### 25.5 Retention Configuration

**REQ-25.5.1:** Retention periods MUST be configurable per event
category and per deployment. Configured retention periods MUST NOT be
shorter than the minimums defined in REQ-25.2.1 for the applicable
conformance level.

**REQ-25.5.2:** Changes to retention configuration MUST emit a
`system.configuration_changed` event with `change_type` of
`retention_policy`.

**REQ-25.5.3:** `[Level 2+]` The retention configuration MUST be
documented in the implementation's conformance claim.

### 25.6 Cross-Standard Retention Alignment

**REQ-25.6.1:** The AILS retention tiers are designed to satisfy the
following external standard requirements:

| External Standard | Retention Requirement | AILS Tier |
|---|---|---|
| ACS REQ-11.6-L2-001 | 90 days minimum | Governance (exceeds) |
| ALS REQ-12.5.2 | 3 years minimum | Governance |
| ALS REQ-12.5.1 | 10 years for EU AI Act high-risk | Regulatory |

**REQ-25.6.2:** Implementations claiming AILS conformance that also
implement ALS, ACS, or other standards with retention requirements MUST
apply the most restrictive retention period across all applicable
standards.
## SECTION 26: MULTI-TENANT ISOLATION

*Normative*

### 26.1 Purpose

This section defines the requirements for multi-tenant isolation of AILS
Events in shared infrastructure deployments. Multi-tenant isolation
ensures that events belonging to one organizational tenant cannot be
accessed, correlated with, or leaked to another tenant through the
logging infrastructure.

**Cross-Standard References:**
- DAE Section 4.8 (Optional Multi-Tenant Isolation)
- ALS Section 5.3 (Credential binding to tenant)

### 26.2 Multi-Tenant Deployment Detection

**REQ-26.2.1:** A deployment is classified as multi-tenant when two or
more organizational tenants share any component of the AILS logging
infrastructure — including Emitters, transport pipelines, storage
backends, or analysis tools.

**REQ-26.2.2:** In multi-tenant deployments, the `tenant` object defined
in Section 5.11 MUST be present on every AILS Event.

**REQ-26.2.3:** In single-tenant deployments, the `tenant` object MAY
be absent.

### 26.3 Tenant Identifier Requirements

**REQ-26.3.1:** The `tenant.tenant_id` field MUST uniquely identify the
organizational tenant within the deployment. Tenant identifiers MUST be
stable across the lifetime of the deployment.

**REQ-26.3.2:** The `tenant.tenant_id` MUST be assigned by the Emitter
at event creation time and MUST NOT be modifiable after event emission.

**REQ-26.3.3:** Events produced by infrastructure components that
operate on behalf of multiple tenants (e.g., shared gateways, load
balancers) MUST carry the `tenant_id` of the tenant whose request or
action triggered the event. Infrastructure events that are not
attributable to a specific tenant (e.g., system health checks) MUST use
a reserved tenant identifier of `"_system"`.

### 26.4 Isolation Requirements

**REQ-26.4.1:** In multi-tenant deployments, the logging infrastructure
MUST enforce tenant isolation such that events belonging to one tenant
are not accessible to users, systems, or queries operating in the
context of another tenant.

**REQ-26.4.2:** Event streams (as defined in Section 22.4.2) MUST NOT
span multiple tenants. Chain hashing and sequence numbering MUST be
maintained independently per tenant.

**REQ-26.4.3:** Cross-tenant event correlation MUST NOT be possible
through the standard AILS correlation fields (`trace_id`, `span_id`,
`session_id`). If a workflow spans multiple tenants, each tenant's
segment MUST use independent correlation identifiers.

**REQ-26.4.4:** Retention actions MUST be scoped to a single tenant.
A retention action that deletes or archives events for one tenant MUST
NOT affect events belonging to any other tenant.

**REQ-26.4.5:** Administrative access to multi-tenant event stores MUST
be tenant-scoped. Global administrative access that spans all tenants
MUST be restricted to infrastructure operators with explicitly granted
cross-tenant privileges and MUST be logged as `system.alert` events
with severity `high`.

### 26.5 Tenant Boundary Violations

**REQ-26.5.1:** An attempt to emit an event with a `tenant_id` that
does not match the authenticated tenant context of the Emitter MUST be
treated as a policy violation and MUST result in a `policy.violation`
event with `violation_type` of `tenant_boundary_crossed`.

**REQ-26.5.2:** An attempt to query or access events belonging to a
tenant other than the authenticated tenant context MUST be denied and
logged.
## SECTION 27: ALERT CONDITION BINDINGS

*Normative*

### 27.1 Purpose

This section defines mandatory alert conditions that implementations
MUST detect and surface when specific AILS Event patterns occur. Alert
conditions bridge the gap between passive logging and active
operational awareness. When a defined pattern is detected, a
`system.alert` event MUST be emitted in addition to the triggering
events themselves.

### 27.2 Mandatory Alert Conditions

**REQ-27.2.1:** `[Level 2+]` The following alert conditions MUST be
implemented. Detection of any condition MUST result in emission of a
`system.alert` event with the specified severity and alert name.

| Alert Name | Severity | Triggering Condition | Source |
|---|---|---|---|
| `chain_hash_integrity_failure` | critical | Chain hash verification fails for any event in a stream. | AILS §22 |
| `event_signature_verification_failure` | critical | Event signature verification fails (Level 3). | AILS §22 |
| `event_stream_discontinuity` | high | A new event stream is started due to chain hash state loss. | AILS §22 |
| `sequence_gap_detected` | high | A gap in sequence numbers is detected in an event stream. | AILS §22 |
| `log_write_failure` | critical | An AILS Event fails to write to persistent storage. | AILS §21 |
| `buffer_overflow` | critical | The event buffer reaches capacity and events may be lost. | AILS §21 |
| `tenant_boundary_crossed` | high | A tenant boundary violation is detected. | AILS §26 |
| `credential_renewal_refused` | high | A credential renewal is refused for a security-class session. | AILS §14 |
| `authority_gate_denied_repeated` | high | Three or more `authority.gate_denied` events for the same action within 60 seconds. | AILS §12 |
| `policy_violation_critical` | critical | A `policy.violation` event with severity `critical` is emitted. | AILS §15 |
| `supply_chain_validation_failure` | high | A `supplychain.validation_failure` event with severity `high` or `critical` is emitted. | AILS §16 |
| `integrity_manifest_failed` | high | An `integrity.manifest_verified` event with `verification_result` of `fail` is emitted. | AILS §17 |
| `tofu_change_detected` | medium | An `integrity.tofu_changed` event is emitted. | AILS §17 |
| `watchdog_halt` | high | A `control.watchdog_triggered` event is emitted for a security-class agent session. | AILS §19 |
| `halt_notification_unacknowledged` | high | A `control.push_notification_unacked` event is emitted for a security-severity notification. | AILS §19 |
| `control_channel_auth_failure_repeated` | high | Three or more `control.channel_auth_failed` events from the same source within 60 seconds. | AILS §19 |
| `replay_attack_detected` | high | A `control.replay_detected` event is emitted. | AILS §19 |
| `execution_without_confirmation` | critical | A `capability.executed` event is emitted for a capability with `confirmation_required: true` without a preceding `human.confirmation` event. | AILS §18/§11 |

### 27.3 Alert Condition Detection

**REQ-27.3.1:** `[Level 2+]` Alert conditions MUST be evaluated by the
Emitter, the logging pipeline, or a dedicated alert processor. The
standard does not mandate where detection occurs, only that it does
occur and that the resulting `system.alert` event is emitted.

**REQ-27.3.2:** `[Level 2+]` Alert conditions involving temporal
patterns (e.g., "three events within 60 seconds") MUST use the
`timestamp` field of the triggering events, not the wall-clock time of
the alert processor.

**REQ-27.3.3:** `[Level 2+]` The `system.alert` event MUST include the
`triggering_event_ids` field containing the `event_id` values of the
events that triggered the alert condition.

### 27.4 Alert Destinations

**REQ-27.4.1:** `[Level 2+]` Implementations MUST support at least one
alert destination. The following destination types are defined:

| Destination Type | Description |
|---|---|
| `webhook` | HTTP POST to a configured URL. |
| `syslog` | Syslog message per RFC 5424. |
| `smtp` | Email notification. |
| `event_only` | Alert is recorded as a `system.alert` event only, with no external notification. |

**REQ-27.4.2:** `[Level 2+]` At least one alert destination other than
`event_only` MUST be configured for `critical` severity alerts in
non-development deployments. An implementation with no external alert
destination for critical alerts SHOULD emit a
`system.configuration_changed` advisory at startup.

### 27.5 Custom Alert Conditions

**REQ-27.5.1:** Implementations MAY define custom alert conditions
beyond those specified in REQ-27.2.1. Custom alert conditions MUST use
alert names prefixed with `x-` followed by a reverse-domain namespace
(e.g., `x-com.example.custom_alert`).

**REQ-27.5.2:** Custom alert conditions MUST NOT override or disable
the mandatory alert conditions defined in REQ-27.2.1.

### 27.6 Alert Suppression

**REQ-27.6.1:** Implementations MAY implement alert suppression
(deduplication) to prevent alert flooding. When suppression is active,
the suppressed alerts MUST still be recorded as `system.alert` events
but external notification MAY be suppressed.

**REQ-27.6.2:** Alert suppression MUST NOT suppress the first
occurrence of any alert condition. Only subsequent occurrences of the
same alert condition within a configurable suppression window MAY be
suppressed.

**REQ-27.6.3:** The suppression window MUST NOT exceed 300 seconds for
`critical` severity alerts and MUST NOT exceed 3600 seconds for `high`
severity alerts.
## SECTION 28: CONFORMANCE

*Normative*

### 28.1 Conformance Level Definitions

AILS defines three conformance levels. Levels are cumulative: Level 2
requires all Level 1 requirements; Level 3 requires all Level 2
requirements.

### 28.2 Level 1 — Structured Telemetry

**REQ-28.2.1:** A Level 1 conformant implementation MUST satisfy all
requirements in this standard that do not carry a `[Level 2+]` or
`[Level 3]` annotation.

**REQ-28.2.2:** A Level 1 conformant implementation MUST implement:

1. Event Envelope specification (Section 5, all non-level-annotated
   requirements)
2. Event Taxonomy naming and stability rules (Section 6)
3. At least one event type from the `inference` category (Section 7)
4. At least one event type from any other category
5. Data classification assignment (Section 23, REQ-23.2.1 through
   REQ-23.3.4)
6. Default redaction of prompt and completion content (Section 24,
   REQ-24.3.1 through REQ-24.3.4)
7. Prohibition of credential material in events (Section 24,
   REQ-24.3.2)
8. Redaction envelope field when redaction is applied (Section 5.12)
9. Standard retention tier (90 days) for emitted events
   (Section 25, REQ-25.2.1)

**REQ-28.2.3:** A Level 1 implementation MUST NOT claim tamper-evidence,
chain hash integrity, cryptographic signing, or audit-grade logging
capabilities.

**Permitted claims at Level 1:**
- "This implementation emits AILS-conformant structured AI telemetry
  (Level 1)"
- "AILS event correlation is supported"
- "AILS provenance classification is active"

**Prohibited claims at Level 1:**
- "AILS audit-grade logging is active"
- "AILS tamper-evident logging is in effect"
- "This implementation satisfies EU AI Act Article 12 logging
  requirements via AILS"

### 28.3 Level 2 — Governed Audit

**REQ-28.3.1:** A Level 2 conformant implementation MUST satisfy all
Level 1 requirements plus all requirements annotated `[Level 2+]` in
this standard.

**REQ-28.3.2:** A Level 2 conformant implementation MUST additionally
implement:

1. Integrity envelope with sequence numbering and chain hashing
   (Section 22, REQ-22.4.1 through REQ-22.4.12)
2. Event stream management (Section 22, REQ-22.4.5 through REQ-22.4.7)
3. Integrity storage requirements (Section 22, REQ-22.7.1 through
   REQ-22.7.3)
4. Event emission ordering (Section 22, REQ-22.6.1 and REQ-22.6.2)
5. Governance retention tier (3 years) for audit events (Section 25)
6. All mandatory alert conditions (Section 27, REQ-27.2.1)
7. At least one external alert destination for critical alerts
   (Section 27, REQ-27.4.2)
8. Multi-tenant isolation when applicable (Section 26)
9. Redaction profile support including `default` and `strict` profiles
   (Section 24, REQ-24.5.1)
10. Chain hash integrity verification on demand (Section 22,
    REQ-22.4.11)
11. Retention configuration documentation in conformance claim
    (Section 25, REQ-25.5.3)

**REQ-28.3.3:** A Level 2 implementation MUST NOT claim cryptographic
event signing capabilities.

**Permitted claims at Level 2 (plus Level 1 claims):**
- "AILS tamper-evident audit logging is in effect"
- "AILS chain hash integrity is verifiable"
- "AILS mandatory alert conditions are active"
- "This implementation provides audit-grade AI logging"
- "This implementation satisfies the audit logging requirements of
  [DAE/AGBAC/ALS/AI-SCS/ACS/MCP Integrity Standard]" (when the
  applicable event types are implemented)

**Prohibited claims at Level 2:**
- "AILS events are cryptographically signed"
- "AILS events provide non-repudiation"

### 28.4 Level 3 — Certified Audit

**REQ-28.4.1:** A Level 3 conformant implementation MUST satisfy all
Level 2 requirements plus all requirements annotated `[Level 3]` in
this standard.

**REQ-28.4.2:** A Level 3 conformant implementation MUST additionally
implement:

1. Cryptographic event signing (Section 22, REQ-22.5.1 through
   REQ-22.5.7)
2. Event signature verification on demand
3. Regulatory retention tier (10 years) when applicable (Section 25)
4. All four standard redaction profiles (`default`, `minimal`, `strict`,
   `regulatory`) (Section 24, REQ-24.5.1)

**Permitted claims at Level 3 (plus Level 2 claims):**
- "AILS events are cryptographically signed"
- "AILS events provide non-repudiation via event signatures"
- "This implementation satisfies EU AI Act Article 12 logging
  requirements via AILS"
- "This implementation satisfies NIST AI RMF MEASURE 2.6 logging
  requirements via AILS"

### 28.5 Cross-Standard Conformance

**REQ-28.5.1:** An implementation claiming AILS conformance for the
audit requirements of an external standard (DAE, AGBAC, ALS, AI-SCS,
ACS, MCP Integrity Standard, or A2A) MUST implement all AILS event
types mapped to that standard in Appendix E.

**REQ-28.5.2:** Cross-standard conformance claims MUST specify both
the AILS conformance level and the external standards whose audit
requirements are satisfied:

```
"AILS v1.0.0 Level 2 Conformant; satisfies audit requirements of
ALS v2.0.0 and ACS v1.0.0"
```

**REQ-28.5.3:** An implementation that satisfies some but not all
event types for an external standard MUST NOT claim full cross-standard
conformance for that standard. Partial cross-standard conformance MUST
use the format:

```
"AILS v1.0.0 Level 2 Conformant; partially satisfies audit
requirements of ALS v2.0.0 (session and credential events only)"
```

### 28.6 Conformance Test Categories

**REQ-28.6.1:** A conformance test suite MUST include at minimum one
test per category:

| Test Category | Description | Level |
|---|---|---|
| T-ENVELOPE | Event envelope contains all required fields with valid types and values | L1 |
| T-TAXONOMY | Event types conform to naming rules and carry valid stability designations | L1 |
| T-CORRELATION | Trace ID, span ID, and session ID enable end-to-end workflow reconstruction from AILS events alone | L1 |
| T-PROVENANCE | Data classification is present on every event and follows least-trusted-input rules | L1 |
| T-REDACTION | Sensitive fields are redactable; redacted events remain valid; credential material is never present | L1 |
| T-RETENTION | Events are retained for the applicable minimum retention period | L1 |
| T-SEQUENCE | Sequence numbers are monotonically increasing with no gaps within a stream | L2 |
| T-CHAINHASH | Chain hashes are correctly computed and verifiable; tampering is detectable | L2 |
| T-STREAM | Event streams are correctly scoped; new streams are created on discontinuity | L2 |
| T-STORAGE | Audit events are persisted before the operations they record are considered complete | L2 |
| T-ALERTS | All mandatory alert conditions trigger `system.alert` events with correct severity | L2 |
| T-TENANT | Multi-tenant isolation prevents cross-tenant event access | L2 |
| T-SIGNING | Event signatures are correctly computed and verifiable; invalid signatures are detected | L3 |
| T-CROSSSTANDARD | All event types mapped to a claimed external standard are implemented with correct payloads | L2 |

### 28.7 Partial Conformance

**REQ-28.7.1:** An implementation satisfying a subset of requirements
for a conformance level MAY state:

```
"This implementation satisfies [list of specific requirement
identifiers] of AILS v1.0.0 Level [N]. It does not claim full
Level [N] conformance."
```

**REQ-28.7.2:** The phrase "AILS conformant" without qualification
implies full Level 1 conformance. "AILS Level 2 conformant" implies
full Level 2 conformance.

### 28.8 Self-Assessment vs. Third-Party Audit

**REQ-28.8.1:** Level 1 and Level 2 conformance MAY be claimed on the
basis of self-assessment. Conformance claims MUST identify whether
based on self-assessment or third-party audit.

**REQ-28.8.2:** Deployments using AILS to satisfy EU AI Act Article 12
logging requirements for high-risk AI systems SHOULD obtain third-party
audit of Level 3 conformance.
## SECTION 29: RELATIONSHIP TO OTHER STANDARDS

*Informative*

### 29.1 OpenTelemetry

AILS is complementary to OpenTelemetry. OpenTelemetry provides telemetry
infrastructure — collection, correlation, export, and transport. AILS
defines AI-specific semantics — the meaning and structure of AI
telemetry events.

AILS events may be exported as OpenTelemetry log records, mapped to
OpenTelemetry spans, or emitted as OpenTelemetry span events. The
mapping is defined in Appendix C. AILS does not require OpenTelemetry
adoption; AILS events may be transported by any mechanism.

**Normative dependency:** AILS correlation fields (`trace_id`,
`span_id`) are format-compatible with W3C Trace Context and
OpenTelemetry identifiers (REQ-5.6.2). This ensures zero-transformation
interoperability when AILS events are exported to OpenTelemetry.

### 29.2 Deterministic Agent Execution (DAE)

AILS provides the audit logging layer for DAE implementations. The
following AILS event categories map to DAE audit requirements:

| DAE Mechanism | AILS Event Category |
|---|---|
| Authority Tokens (§4.4) | `authority.*` |
| Enforcement Gate (§4.5) | `authority.gate_passed`, `authority.gate_denied` |
| Runtime State Machine (§4.3) | `session.state_changed` |
| Provenance Enforcement (§4.6) | `data_classification` envelope field |
| Escalation (§4.7) | `policy.escalation`, `human.escalation_response` |
| Default Deny (§3.4) | `authority.gate_denied` |

DAE implementations emitting AILS Level 2 events satisfy DAE Section 3.9
(Formal Compliance and Certification) audit requirements.

### 29.3 Agent-Based Access Control (AGBAC)

AILS provides the audit logging layer for AGBAC implementations. The
following AILS events map to AGBAC audit requirements:

| AGBAC Requirement | AILS Event |
|---|---|
| Dual-subject authorization decision (§7.1) | `policy.decision` with `policy_type: dual_subject` |
| Delegation token lifecycle (§5) | `credential.issued` with `credential_type: delegation_token` |
| Agent identity (§4.1) | `actor` envelope field with `type: agent` |
| Human identity (§4.2) | `actor.human_identity` or `payload.delegating_principal` |
| Audit event required fields (§11) | AILS envelope provides timestamp, agent identity, human identity, action, resource, delegation type, delegation context, policy decision, reason, and correlation ID |

AGBAC implementations emitting AILS events satisfy AGBAC Section 11
(Audit and Logging Requirements) when the `policy.decision` event
payload includes both individual subject evaluation results per
REQ-15.2.4.

### 29.4 Agent Layer Security (ALS)

AILS provides the audit logging layer for ALS implementations. The
mapping between ALS audit events and AILS events is comprehensive:

| ALS Audit Event | AILS Event |
|---|---|
| SESSION_INIT_REQUESTED | `session.initialized` or `session.init_failed` |
| SESSION_INITIALIZED | `session.initialized` |
| SESSION_INIT_FAILED | `session.init_failed` |
| CREDENTIAL_ISSUED | `credential.issued` |
| CREDENTIAL_RENEWED | `credential.renewed` |
| RENEWAL_REFUSED_HALTED | `credential.refused` |
| SESSION_HALTED | `session.state_changed` (new_state: HALTED) |
| SESSION_WATCHDOG_HALT | `control.watchdog_triggered` |
| SESSION_PAUSED | `session.state_changed` (new_state: PAUSED) |
| SESSION_RESUMED | `session.state_changed` (new_state: NORMAL) |
| SESSION_TERMINATED | `session.terminated` |
| HALT_NOTIFICATION_SENT | `control.push_notification_sent` |
| HALT_NOTIFICATION_ACKNOWLEDGED | `control.push_notification_acked` |
| HALT_NOTIFICATION_UNACKNOWLEDGED | `control.push_notification_unacked` |
| COMMAND_RECEIVED | `control.command_received` |
| CONTROL_CHANNEL_AUTH_FAILED | `control.channel_auth_failed` |
| CONFLICTING_COMMANDS_RECEIVED | `control.conflicting_commands` |
| CONTROL_CHANNEL_DISCONNECTED | `control.channel_disconnected` |
| CONTROL_CHANNEL_RECONNECTED | `control.channel_connected` |
| REPLAY_DETECTED | `control.replay_detected` |
| RATE_LIMIT_EXCEEDED | `control.command_received` (rejection_reason: rate_limited) |
| RENEWAL_ENDPOINT_DEGRADED | `system.health` (component_type: renewal_endpoint, status: degraded) |
| CONTROL_CHANNEL_EXTENDED_ABSENCE | `system.alert` (alert_name: control_channel_extended_absence) |

ALS implementations emitting AILS Level 2 events satisfy ALS Section 12
(Audit Requirements) when all mapped event types are implemented.

### 29.5 AI Supply Chain Standard (AI-SCS)

AILS provides the audit logging layer for AI-SCS implementations:

| AI-SCS Requirement | AILS Event |
|---|---|
| ABOM validation (§5) | `supplychain.abom_validated` |
| Artifact integrity verification (§6) | `supplychain.artifact_verified` |
| Trust assertion lifecycle (§6.3) | `supplychain.trust_assertion` |
| Runtime validation failures (§7.2) | `supplychain.validation_failure` |
| Enforcement actions (§7.2.1) | `supplychain.validation_failure` (enforcement_action field) |

AI-SCS Level 3 (Continuous Assurance) implementations emitting AILS
Level 2 events satisfy AI-SCS audit requirements across all three
control domains.

### 29.6 Agent Capability Standard (ACS)

AILS provides the audit logging layer for ACS implementations. The
mapping between ACS audit events and AILS events is:

| ACS Audit Event | AILS Event |
|---|---|
| SESSION_INITIATED | `session.initialized` (session_type: acs_planner_session) |
| INDEX_DELIVERED | `capability.index_delivered` |
| PAEP_EVALUATED | `capability.paep_evaluated` |
| MANIFEST_DELIVERED | `capability.manifest_delivered` |
| CAPABILITY_SELECTED | `capability.selected` |
| CONTRACT_ISSUED | `capability.contract_issued` |
| CONFIRMATION_RECEIVED | `human.confirmation` |
| CAPABILITY_EXECUTED | `capability.executed` |
| EXECUTION_FAILED | `capability.execution_failed` |
| SESSION_TERMINATED | `session.terminated` |
| TASK_STATUS | `capability.executed` (for long-running task polls) |
| TASK_CANCELLED | `capability.execution_failed` (failure_reason: paep_denied) |

ACS implementations emitting AILS Level 2 events satisfy ACS Section 11
(Audit Logging Requirements) at ACS Level 2.

### 29.7 MCP Integrity Standard

AILS provides the verification event logging for MCP Integrity Standard
implementations:

| MCP Integrity Verification Event | AILS Event |
|---|---|
| verification_pass / verification_fail | `integrity.manifest_verified` |
| Interface fingerprint match/mismatch | `integrity.fingerprint_checked` |
| Version lineage validation | `integrity.chain_validated` |
| tofu_first_use | `integrity.tofu_recorded` |
| tofu_change_detected | `integrity.tofu_changed` |
| manifest_fetch_failed | `integrity.manifest_verified` (verification_result: not_found) |
| key_revoked | `integrity.manifest_verified` (errors array with code: SIGNATURE_KEY_REVOKED) |

MCP Integrity Standard client implementations emitting AILS events
satisfy MCP Integrity Standard Section 19.3 (Audit Logging).

### 29.8 EU AI Act (Regulation 2024/1689)

| AILS Element | EU AI Act Requirement |
|---|---|
| Event Envelope (§5) | Article 12 — logging capabilities |
| Conformance Level 3 (§28.4) | Article 12 — automatic recording of events |
| Retention — Regulatory tier (§25) | Article 12 — retention for the period appropriate to the intended purpose |
| Provenance classification (§23) | Article 14 — human oversight, understanding of system behavior |
| Redaction profiles (§24) | Article 10 — data governance, privacy by design |
| Alert conditions (§27) | Article 9 — risk management, anomaly detection |

AILS Level 3 provides the technical logging infrastructure for high-risk
AI systems under EU AI Act Title III.

### 29.9 NIST AI Risk Management Framework (AI RMF 1.0)

| AILS Element | NIST AI RMF Function |
|---|---|
| Event taxonomy (§6) | MEASURE 2.6 — AI system performance and behavior documentation |
| Provenance (§23) | MAP 5.1 — data provenance |
| Alert conditions (§27) | MANAGE 2.2 — response mechanisms |
| Integrity (§22) | GOVERN 2.1 — accountability mechanisms |
| Retention (§25) | MEASURE 4.1 — post-deployment monitoring |

### 29.10 Google Agent-to-Agent Protocol (A2A)

AILS records A2A protocol interactions through the `a2a.*` event
category (Section 20). AILS does not modify A2A protocol semantics.
A2A task lifecycle, message exchange, Agent Card discovery, and
authentication events are recorded as structured AILS events with
full correlation support.

| A2A Protocol Operation | AILS Event |
|---|---|
| Task creation (`tasks/send`, `tasks/sendSubscribe`) | `a2a.task_created` |
| Task status transition | `a2a.task_status_changed` |
| Message send | `a2a.message_sent` |
| Message receive | `a2a.message_received` |
| Agent Card discovery (`/.well-known/agent.json`) | `a2a.agent_card_discovered` |
| Authentication | `a2a.authentication` |

When ALS governs A2A transport sessions, ALS session events (Section 13)
and A2A protocol events (Section 20) are correlated via the shared
`correlation.session_id` field, providing a unified view of transport
governance and application-layer agent interactions.

When ACS imports A2A Agent Card skills as capabilities, ACS capability
events (Section 18) and A2A discovery events are correlated via the
shared `correlation.trace_id`, enabling end-to-end tracing from
Agent Card discovery through capability governance to task execution.

### 29.11 Model Context Protocol (MCP)

AILS records MCP-related telemetry through tool events (Section 9) and
integrity events (Section 17). AILS does not modify MCP protocol
semantics. MCP tool invocations are recorded as `tool.call` events with
`tool_type: mcp_tool`. MCP tool discovery is recorded as
`tool.discovery` events with `source: mcp_server`.
## SECTION 30: VERSIONING AND STANDARD EVOLUTION

*Normative*

### 30.1 Version Identifier Format

**REQ-30.1.1:** AILS version identifiers SHALL follow Semantic
Versioning 2.0.0: `MAJOR.MINOR.PATCH`. Current version: `1.0.0`.

**REQ-30.1.2:** The `ails_version` field in all AILS Events SHALL carry
the full semantic version string.

**REQ-30.1.3:** AILS implementations SHALL NOT use pre-release version
identifiers in production deployments.

### 30.2 Breaking Change Definition

**REQ-30.2.1:** The following constitute breaking changes (MAJOR
increment):

(a) Removing or renaming any required field in the Event Envelope
(b) Removing or renaming any event type designated `stable`
(c) Changing the data type, format, or encoding of any required field
(d) Changing the semantics of any Glossary term
(e) Adding a required field to the Event Envelope that was not
    previously present
(f) Changing the chain hash computation algorithm
(g) Removing a conformance level

**REQ-30.2.2:** The following are backward-compatible changes (MINOR
increment):

(a) Adding optional fields to the Event Envelope
(b) Adding optional fields to existing event type payloads
(c) Adding new event types to existing categories
(d) Adding new event categories
(e) Adding new conformance test categories
(f) Adding new alert conditions
(g) Adding new redaction profiles
(h) Deprecating event types (with deprecation notice)

**REQ-30.2.3:** The following are patch-level changes (PATCH increment):

(a) Clarifying normative text without changing requirements
(b) Correcting typographical errors
(c) Updating informative references
(d) Adding informative examples

### 30.3 Event Type Lifecycle

**REQ-30.3.1:** Event types progress through the following lifecycle:

| Stage | Description |
|---|---|
| Experimental | Subject to change in minor versions. Implementations SHOULD support but MUST NOT depend on field stability. |
| Stable | Backward-compatible. Fields may be added but not removed or renamed. |
| Deprecated | Scheduled for removal. A deprecation notice specifies the version in which the event type will be removed and the replacement event type, if any. |
| Removed | No longer part of the standard. Only occurs in a MAJOR version increment. |

**REQ-30.3.2:** An event type MUST be designated `deprecated` in a
MINOR version before it can be removed in a subsequent MAJOR version.
The minimum deprecation period is one MINOR version cycle.

**REQ-30.3.3:** Implementations SHOULD log a warning when emitting
events of a deprecated event type.

### 30.4 Forward Compatibility

**REQ-30.4.1:** Consumers of AILS Events MUST ignore unrecognized
fields in the envelope, payload, context, metadata, and integrity
objects. This enables consumers implemented against AILS v1.x to process
events from later v1.x versions without modification.

**REQ-30.4.2:** Consumers MUST NOT reject events based on an
`ails_version` value higher than their implemented version, provided the
MAJOR version matches. A consumer implementing AILS v1.0.0 MUST accept
events with `ails_version: "1.1.0"` or `"1.2.0"`.
**REQ-30.4.3:** Consumers MAY reject events with a different MAJOR
version. Cross-major-version compatibility is not guaranteed.

### 30.5 Deprecation Procedure

**REQ-30.5.1:** When an event type, envelope field, or conformance
requirement is deprecated, the deprecation notice MUST include:

(a) The version in which the item was deprecated
(b) The version in which the item will be removed
(c) The replacement mechanism, if any
(d) Migration guidance

**REQ-30.5.2:** Deprecated items MUST remain functional in all versions
within the same MAJOR version as their deprecation.

### 30.6 Extension Mechanism

**REQ-30.6.1:** Custom event categories MUST use the `x-` prefix per
REQ-6.2.3. Custom event types within standard categories MUST use the
`x-` prefix per REQ-5.3.3.

**REQ-30.6.2:** Custom alert conditions MUST use the `x-` prefix per
REQ-27.5.1.

**REQ-30.6.3:** Extensions MUST NOT modify the semantics of any standard
event type, envelope field, or conformance requirement.

**REQ-30.6.4:** Extensions that achieve broad adoption MAY be proposed
for inclusion in the standard through the standard evolution process.

<br>

## APPENDIX A: EVENT ENVELOPE SCHEMA REFERENCE

*Normative*

The following JSON Schema defines the canonical AILS Event Envelope.
All conformant AILS Events MUST validate against this schema.

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "https://ails-standard.org/schemas/v1/event-envelope",
  "title": "AILS Event Envelope v1.0.0",
  "type": "object",
  "required": [
    "ails_version",
    "event_id",
    "event_type",
    "timestamp",
    "emitter",
    "actor",
    "correlation",
    "data_classification",
    "payload"
  ],
  "properties": {
    "ails_version": {
      "type": "string",
      "pattern": "^\\d+\\.\\d+\\.\\d+$",
      "description": "Semantic version of the AILS standard"
    },
    "event_id": {
      "type": "string",
      "format": "uuid",
      "description": "UUID v4 or v7 uniquely identifying this event"
    },
    "event_type": {
      "type": "string",
      "pattern": "^[a-z][a-z0-9_]*\\.[a-z][a-z0-9_]*$|^x-[a-z0-9._-]+\\.[a-z][a-z0-9_]*$",
      "maxLength": 128,
      "description": "Namespaced event type identifier"
    },
    "timestamp": {
      "type": "string",
      "format": "date-time",
      "description": "ISO 8601 UTC with millisecond precision"
    },
    "emitter": {
      "type": "object",
      "required": ["id", "name", "type"],
      "properties": {
        "id": { "type": "string" },
        "name": { "type": "string" },
        "version": { "type": "string" },
        "type": {
          "type": "string",
          "enum": [
            "application", "framework", "runtime", "gateway",
            "proxy", "security_service", "infrastructure"
          ]
        }
      }
    },
    "actor": {
      "type": "object",
      "required": ["type", "id"],
      "properties": {
        "type": {
          "type": "string",
          "enum": ["agent", "human", "system", "external"]
        },
        "id": { "type": "string" },
        "name": { "type": "string" },
        "agent_identity": {
          "type": "object",
          "properties": {
            "framework": { "type": "string" },
            "model": { "type": "string" },
            "spiffe_id": { "type": "string" }
          }
        },
        "human_identity": {
          "type": "object",
          "properties": {
            "subject": { "type": "string" },
            "issuer": { "type": "string" },
            "roles": {
              "type": "array",
              "items": { "type": "string" }
            }
          }
        }
      }
    },
    "correlation": {
      "type": "object",
      "required": ["trace_id", "span_id"],
      "properties": {
        "trace_id": {
          "type": "string",
          "pattern": "^[a-f0-9]{32}$",
          "description": "128-bit trace identifier, W3C Trace Context compatible"
        },
        "span_id": {
          "type": "string",
          "pattern": "^[a-f0-9]{16}$",
          "description": "64-bit span identifier, OpenTelemetry compatible"
        },
        "parent_span_id": {
          "type": ["string", "null"],
          "pattern": "^[a-f0-9]{16}$"
        },
        "session_id": { "type": ["string", "null"] },
        "request_id": { "type": ["string", "null"] },
        "correlation_id": { "type": ["string", "null"] }
      }
    },
    "data_classification": {
      "type": "string",
      "enum": ["SYSTEM_TRUSTED", "USER_TRUSTED", "EXTERNAL_UNTRUSTED"]
    },
    "payload": {
      "type": "object",
      "description": "Event-type-specific data"
    },
    "context": {
      "type": "object",
      "properties": {
        "environment": { "type": "string" },
        "region": { "type": "string" },
        "deployment_id": { "type": "string" },
        "service_name": { "type": "string" },
        "labels": {
          "type": "object",
          "additionalProperties": { "type": "string" }
        }
      }
    },
    "metadata": {
      "type": "object",
      "properties": {
        "latency_ms": { "type": "number" },
        "cost": {
          "type": "object",
          "required": ["amount", "currency"],
          "properties": {
            "amount": { "type": "number" },
            "currency": { "type": "string", "pattern": "^[A-Z]{3}$" },
            "unit": { "type": "string" }
          }
        },
        "retry_count": { "type": "integer", "minimum": 0 },
        "sdk_name": { "type": "string" },
        "sdk_version": { "type": "string" }
      }
    },
    "integrity": {
      "type": "object",
      "properties": {
        "sequence_number": { "type": "integer", "minimum": 1 },
        "stream_id": { "type": "string" },
        "chain_hash": { "type": "string" },
        "event_signature": { "type": "string" },
        "signing_key_id": { "type": "string" },
        "signing_algorithm": {
          "type": "string",
          "enum": ["Ed25519", "ES256"]
        }
      }
    },
    "tenant": {
      "type": "object",
      "required": ["tenant_id"],
      "properties": {
        "tenant_id": { "type": "string" },
        "partition": { "type": "string" }
      }
    },
    "redaction": {
      "type": "object",
      "required": ["profile", "redacted_fields", "redaction_method"],
      "properties": {
        "profile": { "type": "string" },
        "redacted_fields": {
          "type": "array",
          "items": { "type": "string" }
        },
        "redaction_method": {
          "type": "string",
          "enum": ["omitted", "hashed", "truncated", "masked"]
        }
      }
    },
    "severity": {
      "type": "string",
      "enum": ["debug", "info", "warn", "error", "critical"],
      "description": "Severity level for filtering and prioritization"
    },
    "indicators": {
      "type": "array",
      "items": {
        "type": "object",
        "required": ["type", "value", "role"],
        "properties": {
          "type": {
            "type": "string",
            "enum": [
              "hash", "ip_address", "domain", "url", "tool_name",
              "model_name", "artifact_name", "agent_id",
              "certificate_fingerprint", "cid", "custom"
            ]
          },
          "value": { "type": "string" },
          "role": {
            "type": "string",
            "enum": ["subject", "source", "target", "related"]
          },
          "confidence": {
            "type": "number",
            "minimum": 0.0,
            "maximum": 1.0
          }
        }
      },
      "description": "Indicators of compromise or interest for security tool consumption"
    }
  },
  "additionalProperties": false
}
```
## APPENDIX B: EVENT TYPE REGISTRY

*Normative*

The following table is the authoritative registry of all AILS event
types defined by this version of the standard.

| Event Type | Section | Stability | Level Required |
|---|---|---|---|
| `inference.call` | 7.2 | Stable | L1 |
| `inference.embedding` | 7.3 | Stable | L1 |
| `inference.cache_hit` | 7.4 | Stable | L1 |
| `inference.guardrail` | 7.5 | Stable | L1 |
| `inference.routing` | 7.6 | Stable | L1 |
| `inference.generation` | 7.7 | Stable | L1 |
| `inference.structured_output` | 7.8 | Stable | L1 |
| `agent.lifecycle` | 8.2 | Stable | L1 |
| `agent.step` | 8.3 | Stable | L1 |
| `agent.delegation` | 8.4 | Stable | L1 |
| `agent.loop_checkpoint` | 8.5 | Stable | L1 |
| `agent.workflow_node` | 8.6 | Stable | L1 |
| `tool.call` | 9.2 | Stable | L1 |
| `tool.discovery` | 9.3 | Stable | L1 |
| `tool.mcp_resource` | 9.4 | Stable | L1 |
| `tool.mcp_sampling` | 9.5 | Stable | L1 |
| `memory.read` | 10.2 | Stable | L1 |
| `memory.write` | 10.3 | Stable | L1 |
| `human.feedback` | 11.2 | Stable | L1 |
| `human.confirmation` | 11.3 | Stable | L1 |
| `human.escalation_response` | 11.4 | Stable | L1 |
| `authority.token_issued` | 12.2 | Stable | L1 |
| `authority.token_consumed` | 12.3 | Stable | L1 |
| `authority.token_expired` | 12.4 | Stable | L1 |
| `authority.token_revoked` | 12.5 | Stable | L1 |
| `authority.gate_passed` | 12.6 | Stable | L1 |
| `authority.gate_denied` | 12.7 | Stable | L1 |
| `session.initialized` | 13.2 | Stable | L1 |
| `session.state_changed` | 13.3 | Stable | L1 |
| `session.terminated` | 13.4 | Stable | L1 |
| `session.init_failed` | 13.5 | Stable | L1 |
| `credential.issued` | 14.2 | Stable | L1 |
| `credential.renewed` | 14.3 | Stable | L1 |
| `credential.refused` | 14.4 | Stable | L1 |
| `credential.expired` | 14.5 | Stable | L1 |
| `policy.decision` | 15.2 | Stable | L1 |
| `policy.violation` | 15.3 | Stable | L1 |
| `policy.escalation` | 15.4 | Stable | L1 |
| `supplychain.abom_validated` | 16.2 | Stable | L1 |
| `supplychain.artifact_verified` | 16.3 | Stable | L1 |
| `supplychain.trust_assertion` | 16.4 | Stable | L1 |
| `supplychain.validation_failure` | 16.5 | Stable | L1 |
| `integrity.manifest_verified` | 17.2 | Stable | L1 |
| `integrity.fingerprint_checked` | 17.3 | Stable | L1 |
| `integrity.chain_validated` | 17.4 | Stable | L1 |
| `integrity.tofu_recorded` | 17.5 | Stable | L1 |
| `integrity.tofu_changed` | 17.6 | Stable | L1 |
| `capability.index_delivered` | 18.2 | Stable | L1 |
| `capability.paep_evaluated` | 18.3 | Stable | L1 |
| `capability.manifest_delivered` | 18.4 | Stable | L1 |
| `capability.selected` | 18.5 | Stable | L1 |
| `capability.contract_issued` | 18.6 | Stable | L1 |
| `capability.executed` | 18.7 | Stable | L1 |
| `capability.execution_failed` | 18.8 | Stable | L1 |
| `control.command_received` | 19.2 | Stable | L1 |
| `control.command_executed` | 19.3 | Stable | L1 |
| `control.channel_connected` | 19.4 | Stable | L1 |
| `control.channel_disconnected` | 19.5 | Stable | L1 |
| `control.channel_auth_failed` | 19.6 | Stable | L1 |
| `control.watchdog_triggered` | 19.7 | Stable | L1 |
| `control.push_notification_sent` | 19.8 | Stable | L1 |
| `control.push_notification_acked` | 19.9 | Stable | L1 |
| `control.push_notification_unacked` | 19.10 | Stable | L1 |
| `control.conflicting_commands` | 19.11 | Stable | L1 |
| `control.replay_detected` | 19.12 | Stable | L1 |
| `a2a.task_created` | 20.2 | Stable | L1 |
| `a2a.task_status_changed` | 20.3 | Stable | L1 |
| `a2a.message_sent` | 20.4 | Stable | L1 |
| `a2a.message_received` | 20.5 | Stable | L1 |
| `a2a.agent_card_discovered` | 20.6 | Stable | L1 |
| `a2a.authentication` | 20.7 | Stable | L1 |
| `system.alert` | 21.2 | Stable | L1 |
| `system.health` | 21.3 | Stable | L1 |
| `system.configuration_changed` | 21.4 | Stable | L1 |
| `system.log_degraded` | 21.5 | Stable | L1 |
| `system.retention_action` | 21.6 | Stable | L1 |
| `system.budget_exceeded` | 21.7 | Stable | L1 |
| `system.quota_exhausted` | 21.8 | Stable | L1 |

**Total event types defined:** 73
## APPENDIX C: OPENTELEMETRY MAPPING

*Informative*

### C.1 Overview

This appendix defines how AILS Events map to OpenTelemetry (OTEL)
constructs. This mapping enables AILS events to be exported into
OpenTelemetry-compatible backends without loss of semantic information.
This mapping is informative; AILS does not require OpenTelemetry
adoption.

### C.2 Envelope to OTEL Mapping

| AILS Envelope Field | OTEL Construct | Mapping |
|---|---|---|
| `ails_version` | Resource attribute | `ails.version` |
| `event_id` | LogRecord attribute | `ails.event.id` |
| `event_type` | LogRecord `Body` or Span `Name` | Use as span name when mapped to spans; use as body when mapped to log records. |
| `timestamp` | LogRecord `Timestamp` / Span start time | Direct mapping. |
| `correlation.trace_id` | Trace ID | Direct mapping (format-compatible). |
| `correlation.span_id` | Span ID | Direct mapping (format-compatible). |
| `correlation.parent_span_id` | Parent Span ID | Direct mapping. |
| `correlation.session_id` | Span attribute | `ails.session.id` |
| `emitter.id` | Resource attribute | `service.instance.id` |
| `emitter.name` | Resource attribute | `service.name` |
| `emitter.version` | Resource attribute | `service.version` |
| `actor.type` | Span attribute | `ails.actor.type` |
| `actor.id` | Span attribute | `ails.actor.id` |
| `data_classification` | Span attribute | `ails.data_classification` |
| `tenant.tenant_id` | Resource attribute | `ails.tenant.id` |

### C.3 Event Type to OTEL Signal Mapping

| AILS Category | Recommended OTEL Signal | Rationale |
|---|---|---|
| `inference.*` | Span | Inference calls have meaningful duration and parent-child relationships. |
| `agent.*` | Span | Agent steps form hierarchical execution trees. |
| `tool.*` | Span | Tool calls have duration and are children of agent steps. |
| `memory.*` | Span or Log Record | Memory operations have duration; log records acceptable for lightweight deployments. |
| `human.*` | Log Record | Human interactions are discrete events, not duration-bearing operations. |
| `authority.*` | Log Record | Authority lifecycle events are discrete state changes. |
| `session.*` | Log Record | Session events are discrete state transitions. |
| `credential.*` | Log Record | Credential events are discrete lifecycle transitions. |
| `policy.*` | Log Record | Policy decisions are discrete evaluation results. |
| `supplychain.*` | Log Record | Supply chain validations are discrete verification results. |
| `integrity.*` | Log Record | Integrity checks are discrete verification results. |
| `capability.*` | Span or Log Record | Capability execution has duration; other capability events are discrete. |
| `control.*` | Log Record | Control events are discrete command/response pairs. |
| `system.*` | Log Record | System events are discrete status reports. |

### C.4 Payload to OTEL Attributes

AILS payload fields SHOULD be mapped to OTEL span attributes or log
record attributes using the namespace prefix `ails.` followed by the
event category and field name:

```
ails.inference.provider = "openai"
ails.inference.model.name = "gpt-4.1"
ails.inference.status = "success"
ails.inference.usage.total_tokens = 1500
```

### C.5 Metadata to OTEL Metrics

AILS metadata fields MAY additionally be exported as OTEL metrics for
aggregation and alerting:

| AILS Metadata Field | OTEL Metric | Type |
|---|---|---|
| `metadata.latency_ms` | `ails.operation.duration` | Histogram |
| `metadata.cost.amount` | `ails.operation.cost` | Counter |
| `payload.usage.total_tokens` | `ails.inference.tokens` | Counter |
| `payload.usage.input_tokens` | `ails.inference.tokens.input` | Counter |
| `payload.usage.output_tokens` | `ails.inference.tokens.output` | Counter |

### C.6 Integrity Fields

AILS integrity fields (`sequence_number`, `chain_hash`,
`event_signature`) SHOULD be mapped to OTEL log record attributes but
are not candidates for span attributes, as they are event-level
properties, not operation-level properties.
## APPENDIX D: REDACTION PROFILES AND KEY MANAGEMENT GUIDANCE

*Informative*

### D.1 Redaction Profile Definitions

The following tables define the field-level behavior of each standard
redaction profile.

#### D.1.1 `default` Profile

| Sensitive Category | Method | Behavior |
|---|---|---|
| `prompt_content` | omitted | Replaced with `"[REDACTED]"` |
| `completion_content` | omitted | Replaced with `"[REDACTED]"` |
| `reasoning_content` | omitted | Replaced with `"[REDACTED]"` |
| `pii` | masked | Structure preserved, content masked |
| `credential_material` | N/A | Prohibited in all profiles |
| `tool_arguments` | hashed | SHA-256 hash of arguments |
| `tool_output` | omitted | Replaced with `"[REDACTED]"` |
| `memory_content` | omitted | Replaced with `"[REDACTED]"` |
| `human_identity` | retained | Subject identifiers retained |
| `business_data` | retained | Business data retained |

#### D.1.2 `minimal` Profile

| Sensitive Category | Method | Behavior |
|---|---|---|
| `prompt_content` | retained | Full content retained |
| `completion_content` | retained | Full content retained |
| `reasoning_content` | retained | Full content retained (if disclosed) |
| `pii` | retained | Full PII retained |
| `credential_material` | N/A | Prohibited in all profiles |
| `tool_arguments` | retained | Full arguments retained |
| `tool_output` | retained | Full output retained |
| `memory_content` | retained | Full content retained |
| `human_identity` | retained | Full identity retained |
| `business_data` | retained | Full data retained |

**Warning:** The `minimal` profile is intended for development and
debugging environments only. It SHOULD NOT be used in production
deployments that process real user data or PII.

#### D.1.3 `strict` Profile

| Sensitive Category | Method | Behavior |
|---|---|---|
| `prompt_content` | omitted | Replaced with `"[REDACTED]"` |
| `completion_content` | omitted | Replaced with `"[REDACTED]"` |
| `reasoning_content` | omitted | Replaced with `"[REDACTED]"` |
| `pii` | omitted | Replaced with `"[REDACTED]"` |
| `credential_material` | N/A | Prohibited in all profiles |
| `tool_arguments` | omitted | Replaced with `"[REDACTED]"` |
| `tool_output` | omitted | Replaced with `"[REDACTED]"` |
| `memory_content` | omitted | Replaced with `"[REDACTED]"` |
| `human_identity` | hashed | SHA-256 hash of identifier |
| `business_data` | omitted | Replaced with `"[REDACTED]"` |

#### D.1.4 `regulatory` Profile

| Sensitive Category | Method | Behavior |
|---|---|---|
| `prompt_content` | omitted | Replaced with `"[REDACTED]"` |
| `completion_content` | omitted | Replaced with `"[REDACTED]"` |
| `reasoning_content` | omitted | Replaced with `"[REDACTED]"` |
| `pii` | omitted | Replaced with `"[REDACTED]"` |
| `credential_material` | N/A | Prohibited in all profiles |
| `tool_arguments` | omitted | Replaced with `"[REDACTED]"` |
| `tool_output` | omitted | Replaced with `"[REDACTED]"` |
| `memory_content` | omitted | Replaced with `"[REDACTED]"` |
| `human_identity` | omitted | Replaced with `"[REDACTED]"` (except `reviewer_id` in confirmation events, which is hashed) |
| `business_data` | omitted | Replaced with `"[REDACTED]"` |

### D.2 Key Management Guidance (Level 3)

The following guidance applies to deployments implementing Level 3
event signing.

#### D.2.1 Key Generation

- Signing keys SHOULD be generated using Ed25519 (preferred) or
  ECDSA P-256.
- Key generation SHOULD occur in a hardware security module (HSM) or
  equivalent secure key management system.
- Key generation in software is permitted for development environments
  only.

#### D.2.2 Key Storage

- Private signing keys MUST be stored in a key management system that
  enforces access control and audit logging.
- Private keys MUST NOT be stored in source control, environment
  variables accessible to application code, or unencrypted
  configuration files.
- Cloud-managed key management services (AWS KMS, Azure Key Vault,
  Google Cloud KMS, HashiCorp Vault) are RECOMMENDED for production
  deployments.

#### D.2.3 Key Rotation

- Signing keys SHOULD be rotated at intervals not exceeding one year.
- Upon rotation, the previous public key MUST remain available for
  verification of events signed with it for the full retention period
  of those events.
- Key rotation MUST emit a `system.configuration_changed` event.

#### D.2.4 Key Revocation

- If a signing key is compromised, the deployer MUST remove the key
  from all signing infrastructure, emit a `system.alert` event with
  severity `critical`, and generate a replacement key.
- Events signed with revoked keys SHOULD be flagged as
  `signature_key_revoked` in verification results but MUST NOT be
  deleted, as the event content remains valid even if the signature
  is no longer trustworthy.

#### D.2.5 Key Discovery

- Public keys for signature verification SHOULD be discoverable via
  a JWKS endpoint or equivalent mechanism.
- The key discovery mechanism is deployment-specific and is not
  mandated by this standard.
## APPENDIX E: CROSS-STANDARD EVENT MATRIX

*Informative*

### E.1 Purpose

This appendix provides the authoritative mapping between AILS event
types and the audit event requirements of external AI security
standards. Implementations claiming cross-standard conformance per
REQ-28.5.1 MUST implement all AILS event types listed for the claimed
external standard.

### E.2 DAE — Deterministic Agent Execution

| DAE Requirement | DAE Mechanism | AILS Event Type(s) |
|---|---|---|
| Authority token lifecycle | §4.4 Authority Tokens | `authority.token_issued`, `authority.token_consumed`, `authority.token_expired`, `authority.token_revoked` |
| Enforcement gate | §4.5 Enforcement Gate | `authority.gate_passed`, `authority.gate_denied` |
| State machine transitions | §4.3 Runtime State Machine | `session.initialized`, `session.state_changed`, `session.terminated` |
| Provenance enforcement | §4.6 Provenance Enforcement | `data_classification` envelope field (all events) |
| Escalation | §4.7 Escalation | `policy.escalation`, `human.escalation_response` |
| Default deny | §3.4 Default Deny | `authority.gate_denied` |

**Total AILS event types required for DAE conformance:** 11

### E.3 AGBAC — Agent-Based Access Control

| AGBAC Requirement | AGBAC Section | AILS Event Type(s) |
|---|---|---|
| Dual-subject authorization | §7.1 Dual-Subject Rule | `policy.decision` (with `policy_type: dual_subject`) |
| Delegation token lifecycle | §5 Delegation Token | `credential.issued` (with `credential_type: delegation_token`), `credential.expired` |
| Audit event emission | §11 Audit and Logging | `policy.decision` (with both subject evaluation results) |
| No escalation guarantee | §7.2 | `policy.violation` (with `violation_type: privilege_escalation`) |

**Total AILS event types required for AGBAC conformance:** 4

### E.4 ALS — Agent Layer Security

| ALS Audit Event | ALS Section | AILS Event Type |
|---|---|---|
| SESSION_INIT_REQUESTED | §4.3 | `session.initialized` |
| SESSION_INITIALIZED | §4.3 | `session.initialized` |
| SESSION_INIT_FAILED | §4.3 | `session.init_failed` |
| CREDENTIAL_ISSUED | §5.2 | `credential.issued` |
| CREDENTIAL_RENEWED | §5.5 | `credential.renewed` |
| RENEWAL_REFUSED_HALTED | §5.5 | `credential.refused` |
| SESSION_HALTED | §4.4 | `session.state_changed` |
| SESSION_WATCHDOG_HALT | §9.3 | `control.watchdog_triggered` |
| SESSION_PAUSED | §4.4 | `session.state_changed` |
| SESSION_RESUMED | §4.4 | `session.state_changed` |
| SESSION_TERMINATED | §4.4 | `session.terminated` |
| HALT_NOTIFICATION_SENT | §10.2 | `control.push_notification_sent` |
| HALT_NOTIFICATION_ACKNOWLEDGED | §10.3 | `control.push_notification_acked` |
| HALT_NOTIFICATION_UNACKNOWLEDGED | §10.3 | `control.push_notification_unacked` |
| COMMAND_RECEIVED | §8.3 | `control.command_received` |
| CONTROL_CHANNEL_AUTH_FAILED | §8.2 | `control.channel_auth_failed` |
| CONFLICTING_COMMANDS_RECEIVED | §8.6 | `control.conflicting_commands` |
| CONTROL_CHANNEL_DISCONNECTED | §8.7 | `control.channel_disconnected` |
| CONTROL_CHANNEL_RECONNECTED | §8.7 | `control.channel_connected` |
| REPLAY_DETECTED | §8.5 | `control.replay_detected` |
| RATE_LIMIT_EXCEEDED | §8.8 | `control.command_received` (rejection_reason: rate_limited) |
| RENEWAL_ENDPOINT_DEGRADED | §7.6 | `system.health` |
| CONTROL_CHANNEL_EXTENDED_ABSENCE | §8.7 | `system.alert` |

**Total AILS event types required for ALS conformance:** 16

### E.5 AI-SCS — AI Supply Chain Standard

| AI-SCS Requirement | AI-SCS Section | AILS Event Type |
|---|---|---|
| ABOM validation | §5 | `supplychain.abom_validated` |
| Artifact integrity verification | §6 | `supplychain.artifact_verified` |
| Trust assertion verification | §6.3 | `supplychain.trust_assertion` |
| Runtime validation failure | §7.2 | `supplychain.validation_failure` |
| Enforcement action | §7.2.1 | `supplychain.validation_failure` (enforcement_action field) |

**Total AILS event types required for AI-SCS conformance:** 4

### E.6 ACS — Agent Capability Standard

| ACS Audit Event | ACS Section | AILS Event Type |
|---|---|---|
| SESSION_INITIATED | §8.1 | `session.initialized` (session_type: acs_planner_session) |
| INDEX_DELIVERED | §8.4 | `capability.index_delivered` |
| PAEP_EVALUATED | §9.4 | `capability.paep_evaluated` |
| MANIFEST_DELIVERED | §8.5 | `capability.manifest_delivered` |
| CAPABILITY_SELECTED | §8.6 | `capability.selected` |
| CONTRACT_ISSUED | §8.6 | `capability.contract_issued` |
| CONFIRMATION_RECEIVED | §6.5 | `human.confirmation` |
| CAPABILITY_EXECUTED | §8.7 | `capability.executed` |
| EXECUTION_FAILED | §8.7 | `capability.execution_failed` |
| SESSION_TERMINATED | §8.1 | `session.terminated` |
| TASK_STATUS | §10.5 | `capability.executed` |
| TASK_CANCELLED | §10.5 | `capability.execution_failed` |

**Total AILS event types required for ACS conformance:** 10

### E.7 MCP Integrity Standard

| MCP Integrity Event | MCP Integrity Section | AILS Event Type |
|---|---|---|
| verification_pass | §18.2 | `integrity.manifest_verified` (verification_result: pass) |
| verification_fail | §18.2 | `integrity.manifest_verified` (verification_result: fail) |
| Interface fingerprint check | §18.2 step 4 | `integrity.fingerprint_checked` |
| Version lineage check | §18.2 step 6 | `integrity.chain_validated` |
| tofu_first_use | §19.2 | `integrity.tofu_recorded` |
| tofu_change_detected | §19.2 | `integrity.tofu_changed` |
| manifest_fetch_failed | §19.3 | `integrity.manifest_verified` (verification_result: not_found) |
| key_revoked | §19.3 | `integrity.manifest_verified` (errors: SIGNATURE_KEY_REVOKED) |

**Total AILS event types required for MCP Integrity conformance:** 5

### E.8 A2A — Agent-to-Agent Protocol

| A2A Protocol Operation | AILS Event |
|---|---|
| Task creation | `a2a.task_created` |
| Task status transition | `a2a.task_status_changed` |
| Message send | `a2a.message_sent` |
| Message receive | `a2a.message_received` |
| Agent Card discovery | `a2a.agent_card_discovered` |
| Authentication | `a2a.authentication` |

**Total AILS event types required for A2A conformance:** 6

### E.9 Summary

| External Standard | AILS Event Types Required |
|---|---|
| DAE | 11 |
| AGBAC | 4 |
| ALS | 16 |
| AI-SCS | 4 |
| ACS | 10 |
| MCP Integrity | 5 |
| A2A | 6 |
| **All standards combined** | **42** (with deduplication of shared event types) |
## APPENDIX F: REGULATORY COMPLIANCE MATRIX

*Informative*

### F.1 Purpose

This appendix maps AILS requirements and capabilities to regulatory
frameworks applicable to AI systems. This mapping is informative and
does not constitute legal advice. Organizations should consult legal
counsel to determine their specific compliance obligations.

### F.2 EU AI Act (Regulation 2024/1689)

| EU AI Act Article | Requirement Summary | AILS Element | AILS Level |
|---|---|---|---|
| Article 12(1) | High-risk AI systems shall technically allow for automatic recording of events (logs) | Event Envelope (§5), Event Taxonomy (§6) | L1 |
| Article 12(2) | Logging shall include the period of each use, the reference database, input data, and identification of natural persons involved in verification | Correlation (§5.6), Actor (§5.5), Retention (§25) | L2 |
| Article 12(3) | Logs shall be kept for a period appropriate to the intended purpose, at least 6 months | Retention tiers (§25), Regulatory tier (10 years) | L2/L3 |
| Article 12(4) | Providers shall ensure logging capabilities conform to recognised standards | AILS conformance levels (§28) | L2 |
| Article 14(1) | High-risk AI systems shall be designed to be effectively overseen by natural persons | Human interaction events (§11), Alert conditions (§27) | L2 |
| Article 14(4) | Measures for human oversight shall include the ability to interrupt, stop, or override | Session events (§13), Control events (§19), Policy events (§15) | L2 |
| Article 9(2)(a) | Risk management shall include identification and analysis of known and reasonably foreseeable risks | Alert conditions (§27), Policy violation events (§15) | L2 |
| Article 10(2)(f) | Data governance shall include examination in view of possible biases | Inference events with guardrail results (§7.5) | L1 |
| Article 43 | Conformity assessment | Conformance test categories (§28.6), Self-assessment vs. third-party audit (§28.8) | L3 |

### F.3 NIST AI Risk Management Framework (AI RMF 1.0)

| NIST AI RMF Function | Subcategory | AILS Element | AILS Level |
|---|---|---|---|
| GOVERN 1.1 | Legal and regulatory requirements are identified | Regulatory retention tier (§25) | L2 |
| GOVERN 2.1 | Accountability mechanisms are in place | Integrity model (§22), Actor attribution (§5.5) | L2 |
| MAP 2.3 | Relevant data properties are understood | Provenance classification (§23), Memory events (§10) | L1 |
| MAP 5.1 | Data provenance is tracked | Provenance classification (§23) | L1 |
| MEASURE 2.1 | AI system performance is monitored | Inference events (§7), System health (§21) | L1 |
| MEASURE 2.6 | AI system behavior is documented | Event taxonomy (§6), all event categories | L1 |
| MEASURE 4.1 | Post-deployment monitoring is in place | Retention (§25), System events (§21) | L2 |
| MANAGE 2.2 | Response mechanisms are defined | Alert conditions (§27), Control events (§19) | L2 |
| MANAGE 4 | Risks are managed over the AI lifecycle | Supply chain events (§16), Integrity events (§17) | L2 |

### F.4 NIST Cybersecurity Framework (CSF) 2.0

| CSF Function | Category | AILS Element | AILS Level |
|---|---|---|---|
| GOVERN (GV) | GV.RM — Risk Management | Alert conditions (§27), Conformance (§28) | L2 |
| IDENTIFY (ID) | ID.AM — Asset Management | Supply chain events (§16), Tool discovery (§9.3) | L1 |
| PROTECT (PR) | PR.AC — Access Control | Policy events (§15), Authority events (§12), Credential events (§14) | L2 |
| DETECT (DE) | DE.AE — Adverse Event Analysis | Alert conditions (§27), Policy violations (§15.3) | L2 |
| DETECT (DE) | DE.CM — Continuous Monitoring | System health (§21.3), Integrity events (§17) | L2 |
| RESPOND (RS) | RS.MI — Mitigation | Control events (§19), Session events (§13) | L2 |
| RESPOND (RS) | RS.CO — Communication | Push notification events (§19.8–19.10), Alert destinations (§27.4) | L2 |
| RECOVER (RC) | RC.RP — Recovery Planning | Session resumed events (§13.3), Credential renewed events (§14.3) | L2 |

### F.5 GDPR (Regulation (EU) 2016/679)

| GDPR Article | Requirement Summary | AILS Element | AILS Level |
|---|---|---|---|
| Article 5(1)(f) | Integrity and confidentiality of personal data | Redaction framework (§24), Tamper-evidence (§22) | L2 |
| Article 25 | Data protection by design and by default | Default redaction of PII (§24.3), Regulatory redaction profile (§24.5) | L1 |
| Article 30 | Records of processing activities | Event taxonomy documenting all AI operations, Retention (§25) | L2 |
| Article 35 | Data protection impact assessment | Provenance classification (§23), Audit trail (§22) | L2 |

### F.6 SOX (Sarbanes-Oxley Act)

| SOX Section | Requirement Summary | AILS Element | AILS Level |
|---|---|---|---|
| §302 | Corporate responsibility for financial reports | Policy decision events for financial operations (§15), Tamper-evident logging (§22) | L2 |
| §404 | Management assessment of internal controls | Conformance claims (§28), Audit trail integrity (§22) | L2 |
| §802 | Criminal penalties for altering documents | Tamper-evident event streams (§22.4), Append-only storage (§22.7.2) | L3 |

### F.7 HIPAA (Health Insurance Portability and Accountability Act)

| HIPAA Rule | Requirement Summary | AILS Element | AILS Level |
|---|---|---|---|
| §164.312(b) | Audit controls | Event taxonomy (§6), Integrity (§22), Retention (§25) | L2 |
| §164.312(c) | Integrity controls | Chain hashing (§22.4.3), Event signing (§22.5) | L2/L3 |
| §164.312(d) | Person or entity authentication | Actor attribution (§5.5), Credential events (§14) | L1 |
| §164.502 | Uses and disclosures of PHI | Redaction framework (§24), Regulatory profile (§24.5) | L2 |

### F.8 ISO/IEC 42001 (AI Management System)

| ISO 42001 Clause | Requirement Summary | AILS Element | AILS Level |
|---|---|---|---|
| 6.1.4 | AI risk assessment | Alert conditions (§27), Policy events (§15) | L2 |
| 8.4 | AI system operation and monitoring | Inference events (§7), Agent events (§8), System events (§21) | L1 |
| 9.1 | Monitoring, measurement, analysis and evaluation | Event taxonomy (§6), Retention (§25) | L1 |
| 10.1 | Nonconformity and corrective action | Policy violation events (§15.3), Supply chain validation failures (§16.5) | L2 |

## APPENDIX G: OCSF MAPPING

*Informative*

### G.1 Purpose

The Open Cybersecurity Schema Framework (OCSF) is an open-source
schema standard for security event data, adopted by AWS Security Lake,
Splunk, CrowdStrike, IBM QRadar, and other major security platforms.
This appendix defines how AILS Events map to OCSF categories and
classes, enabling AILS events to be ingested natively by OCSF-aware
security tools without custom transformation.

### G.2 OCSF Category Mapping

| AILS Event Category | OCSF Category | OCSF Category ID | Rationale |
|---|---|---|---|
| `inference.*` | Application Activity | 6 | Model inference is application-level activity |
| `agent.*` | Application Activity | 6 | Agent execution is application-level activity |
| `tool.*` | Application Activity | 6 | Tool invocation is application-level activity |
| `memory.*` | Application Activity | 6 | Memory access is application-level activity |
| `human.*` | Application Activity | 6 | Human interaction is application-level activity |
| `authority.*` | Identity & Access Management | 3 | Authority tokens are access control artifacts |
| `session.*` | Identity & Access Management | 3 | Session lifecycle is IAM-adjacent |
| `credential.*` | Identity & Access Management | 3 | Credential lifecycle is IAM |
| `policy.*` | Identity & Access Management | 3 | Authorization decisions are IAM |
| `supplychain.*` | Discovery | 5 | Supply chain validation is asset discovery and verification |
| `integrity.*` | Discovery | 5 | Integrity verification is asset verification |
| `capability.*` | Identity & Access Management | 3 | Capability governance is access control |
| `control.*` | Network Activity | 4 | Control channel operations are network-level |
| `a2a.*` | Network Activity | 4 | A2A protocol interactions are network-level |
| `system.*` | System Activity | 1 | System health and alerts are system-level |

### G.3 OCSF Class Mapping for Security-Relevant Events

| AILS Event Type | OCSF Class | OCSF Class ID | Mapping Notes |
|---|---|---|---|
| `policy.decision` | Authorization | 3002 | Map `decision` to OCSF `disposition`. Map `subjects` to OCSF `actor` and `user`. |
| `policy.violation` | Policy Violation | 3003 | Map `violation_type` to OCSF `policy.name`. Map `severity` to OCSF `severity_id`. |
| `authority.gate_denied` | Authorization | 3002 | Map as authorization denial. `denial_reason` maps to OCSF `message`. |
| `credential.issued` | Authentication | 3001 | Map as credential grant. |
| `credential.refused` | Authentication | 3001 | Map as authentication failure. |
| `control.channel_auth_failed` | Authentication | 3001 | Map as authentication failure with network context. |
| `control.replay_detected` | Security Finding | 2001 | Map as a detected threat. |
| `supplychain.validation_failure` | Security Finding | 2001 | Map as supply chain threat detection. |
| `integrity.tofu_changed` | Security Finding | 2001 | Map as integrity anomaly. |
| `system.alert` | Security Finding | 2001 | Map alert conditions to OCSF findings. |

### G.4 OCSF Field Mapping

| AILS Envelope Field | OCSF Field | Mapping |
|---|---|---|
| `event_id` | `metadata.uid` | Direct mapping |
| `timestamp` | `time` | Direct mapping (ISO 8601) |
| `severity` | `severity_id` | `debug`→0, `info`→1, `warn`→2, `error`→3, `critical`→4 |
| `actor.type` | `actor.type` | Map `agent`→`process`, `human`→`user`, `system`→`service` |
| `actor.id` | `actor.uid` | Direct mapping |
| `emitter.name` | `metadata.product.name` | Direct mapping |
| `emitter.version` | `metadata.product.version` | Direct mapping |
| `data_classification` | `metadata.labels` | Map as label: `ails_classification: <value>` |
| `correlation.trace_id` | `metadata.correlation_uid` | Direct mapping |
| `tenant.tenant_id` | `metadata.tenant_uid` | Direct mapping |
| `indicators[].type` | `observables[].type` | Map AILS indicator types to OCSF observable types |
| `indicators[].value` | `observables[].value` | Direct mapping |

### G.5 Implementation Guidance

OCSF-aware consumers SHOULD implement a transformation layer that:

1. Maps the AILS `event_type` to the appropriate OCSF category and class
2. Copies AILS envelope fields to OCSF common fields per Section G.4
3. Maps AILS `indicators` to OCSF `observables`
4. Maps AILS `severity` to OCSF `severity_id`
5. Preserves the full AILS event in the OCSF `unmapped` field for
   fields not covered by the OCSF mapping

OCSF transformation MUST NOT modify the original AILS event. The
transformation produces an OCSF-formatted copy for consumption by
OCSF-native tools.

## APPENDIX H: MITRE ATLAS MAPPING

*Informative*

### H.1 Purpose

MITRE ATLAS (Adversarial Threat Landscape for Artificial Intelligence
Systems) defines a knowledge base of adversary tactics and techniques
for AI systems, analogous to MITRE ATT&CK for traditional IT systems.
This appendix maps AILS event patterns to ATLAS techniques, enabling
security teams to build detection rules that identify AI-specific
attack techniques from AILS event streams.

### H.2 ATLAS Technique Detection via AILS Events

| ATLAS Technique | ATLAS ID | Detection Pattern | AILS Events |
|---|---|---|---|
| ML Supply Chain Compromise | AML.T0010 | Artifact hash mismatch or undeclared artifact in ABOM validation | `supplychain.validation_failure` (failure_type: `hash_mismatch`, `undeclared_artifact`, `model_substitution`) |
| Backdoor ML Model | AML.T0018 | Model substitution or unauthorized model replacement detected at runtime | `supplychain.validation_failure` (failure_type: `model_substitution`) |
| Poison Training Data | AML.T0020 | Dataset provenance mismatch or undeclared dataset modification | `supplychain.validation_failure` (failure_type: `provenance_mismatch`) |
| Discover ML Model Ontology | AML.T0044 | Repeated PAEP-denied capability queries probing for available capabilities | Multiple `capability.paep_evaluated` (decision: `denied`) for same planner in short window |
| LLM Prompt Injection | AML.T0051 | Guardrail detects prompt injection pattern in input | `inference.guardrail` (result: `fail`, categories includes `prompt_injection`) |
| LLM Jailbreak | AML.T0054 | Guardrail detects jailbreak attempt | `inference.guardrail` (result: `fail`, categories includes `jailbreak`) |
| LLM Plugin Compromise | AML.T0056 | MCP tool interface fingerprint mismatch — tool description or schema modified | `integrity.fingerprint_checked` (result: `mismatch`) or `integrity.tofu_changed` |
| Exfiltration via ML Inference API | AML.T0024 | Unusual volume of inference calls or token consumption from a single session | `system.budget_exceeded` combined with high `inference.call` volume per session |
| Evade ML Model | AML.T0015 | Repeated inference calls with variations targeting refusal boundaries | Multiple `inference.call` (output.refusal: `true`) followed by `inference.call` (status: `success`) in same trace |
| Craft Adversarial Data | AML.T0043 | Structured output validation failures suggesting adversarial schema probing | Multiple `inference.structured_output` (validation_result: `invalid`) in short window |
| Abuse Existing Tool Access | — | Tool invocation denied by authority gate or policy | `authority.gate_denied` or `tool.call` (status: `denied`) |
| Unauthorized Agent Delegation | — | Agent delegation to unrecognized or unauthorized agent | `agent.delegation` (status: `rejected`) or `a2a.authentication` (result: `failed`) |

### H.3 ATLAS Tactic Coverage

| ATLAS Tactic | Coverage via AILS |
|---|---|
| Reconnaissance | `capability.paep_evaluated` (denied), `a2a.agent_card_discovered`, `tool.discovery` |
| Resource Development | `supplychain.artifact_verified` (fail), `supplychain.trust_assertion` (failed_verification) |
| Initial Access | `control.channel_auth_failed`, `a2a.authentication` (failed), `credential.refused` |
| ML Attack Staging | `supplychain.validation_failure`, `integrity.manifest_verified` (fail) |
| Execution | `authority.gate_denied`, `tool.call` (denied), `policy.violation` |
| Persistence | `supplychain.validation_failure` (model_substitution, dependency_drift) |
| Defense Evasion | `inference.guardrail` (fail), `integrity.tofu_changed` |
| Exfiltration | `system.budget_exceeded`, `system.quota_exhausted` |
| Impact | `policy.violation` (critical), `session.state_changed` (to HALTED) |

### H.4 Implementation Guidance

Security teams implementing ATLAS-based detection SHOULD:

1. Create detection rules mapping the event patterns in Section H.2
   to alerts with ATLAS technique identifiers.
2. Enrich AILS `system.alert` events with the applicable ATLAS
   technique ID in the `triggering_pattern` field.
3. Correlate multiple low-confidence detections across a single
   `trace_id` or `session_id` to identify multi-stage attack patterns.
4. Feed confirmed detections into threat intelligence platforms using
   the ATLAS technique ID as the classification anchor.

## APPENDIX I: SIGMA DETECTION RULE TEMPLATES

*Informative*

### I.1 Purpose

Sigma is a generic and open signature format for SIEM systems,
enabling detection rules to be written once and converted to any SIEM
query language (Splunk SPL, Elastic KQL, Microsoft Sentinel KQL,
Chronicle YARA-L, etc.). This appendix provides Sigma rule templates
for the mandatory AILS alert conditions defined in Section 27 and for
key ATLAS-mapped detection patterns from Appendix H.

### I.2 Sigma Rule Templates

#### I.2.1 Chain Hash Integrity Failure

```yaml
title: AILS Chain Hash Integrity Failure
id: ails-001
status: stable
description: Detects tampering with AILS event stream via chain hash verification failure
references:
  - AILS v1.0.0 Section 22
level: critical
logsource:
  category: ai_telemetry
  product: ails
detection:
  selection:
    event_type: "system.alert"
    payload.alert_name: "chain_hash_integrity_failure"
  condition: selection
falsepositives:
  - Software bugs in chain hash computation
  - Event stream recovery after crash
tags:
  - ails.integrity
  - attack.defense_evasion
```

#### I.2.2 Supply Chain Validation Failure — Model Substitution

```yaml
title: AILS Model Substitution Detected
id: ails-002
status: stable
description: Detects unauthorized model replacement in AI supply chain
references:
  - AILS v1.0.0 Section 16
  - MITRE ATLAS AML.T0010
level: high
logsource:
  category: ai_telemetry
  product: ails
detection:
  selection:
    event_type: "supplychain.validation_failure"
    payload.failure_type: "model_substitution"
  condition: selection
falsepositives:
  - Authorized model upgrade without ABOM update
tags:
  - ails.supplychain
  - attack.persistence
  - atlas.AML.T0010
```

#### I.2.3 Prompt Injection Detected

```yaml
title: AILS Prompt Injection Detected
id: ails-003
status: stable
description: Detects prompt injection attempts via guardrail evaluation
references:
  - AILS v1.0.0 Section 7.5
  - MITRE ATLAS AML.T0051
level: high
logsource:
  category: ai_telemetry
  product: ails
detection:
  selection:
    event_type: "inference.guardrail"
    payload.result: "fail"
    payload.categories|contains: "prompt_injection"
  condition: selection
falsepositives:
  - Overly sensitive guardrail rules
tags:
  - ails.inference
  - atlas.AML.T0051
```

#### I.2.4 MCP Tool Poisoning — Interface Change

```yaml
title: AILS MCP Tool Interface Tampering
id: ails-004
status: stable
description: Detects modification of MCP tool interface (description, schema, or annotations)
references:
  - AILS v1.0.0 Section 17
  - MITRE ATLAS AML.T0056
level: high
logsource:
  category: ai_telemetry
  product: ails
detection:
  selection:
    event_type: "integrity.fingerprint_checked"
    payload.result: "mismatch"
  condition: selection
falsepositives:
  - Legitimate tool update without manifest re-sealing
tags:
  - ails.integrity
  - atlas.AML.T0056
```

#### I.2.5 Repeated Authority Gate Denial

```yaml
title: AILS Repeated Authority Gate Denial
id: ails-005
status: stable
description: Detects repeated unauthorized action attempts via enforcement gate
references:
  - AILS v1.0.0 Section 12
level: high
logsource:
  category: ai_telemetry
  product: ails
detection:
  selection:
    event_type: "authority.gate_denied"
  condition: selection | count() by payload.action_id > 2
  timeframe: 60s
falsepositives:
  - Misconfigured authority policy during deployment
tags:
  - ails.authority
  - attack.execution
```

#### I.2.6 Control Channel Replay Attack

```yaml
title: AILS Control Channel Replay Attack
id: ails-006
status: stable
description: Detects replayed commands on ALS control channel
references:
  - AILS v1.0.0 Section 19
level: high
logsource:
  category: ai_telemetry
  product: ails
detection:
  selection:
    event_type: "control.replay_detected"
  condition: selection
falsepositives:
  - Network retransmission causing duplicate delivery (unlikely with nonce checking)
tags:
  - ails.control
  - attack.initial_access
```

#### I.2.7 Autonomous Agent Budget Exhaustion

```yaml
title: AILS Agent Budget Exhaustion
id: ails-007
status: stable
description: Detects autonomous agent exceeding resource budgets — potential runaway behavior
references:
  - AILS v1.0.0 Section 21
level: medium
logsource:
  category: ai_telemetry
  product: ails
detection:
  selection:
    event_type: "system.budget_exceeded"
    payload.threshold_percent|gte: 100
    payload.budget_scope: "session"
  condition: selection
falsepositives:
  - Legitimately large tasks exceeding conservative budgets
tags:
  - ails.system
  - atlas.AML.T0024
```

#### I.2.8 DESTRUCTIVE Capability Executed Without Confirmation

```yaml
title: AILS DESTRUCTIVE Capability Without Confirmation
id: ails-008
status: stable
description: Detects execution of a DESTRUCTIVE capability without preceding human confirmation
references:
  - AILS v1.0.0 Section 27
level: critical
logsource:
  category: ai_telemetry
  product: ails
detection:
  selection:
    event_type: "system.alert"
    payload.alert_name: "execution_without_confirmation"
  condition: selection
falsepositives:
  - None expected — this indicates a governance bypass
tags:
  - ails.capability
  - attack.execution
```

#### I.2.9 A2A Agent Authentication Failure

```yaml
title: AILS A2A Agent Authentication Failure
id: ails-009
status: stable
description: Detects failed authentication between A2A agents
references:
  - AILS v1.0.0 Section 20
level: medium
logsource:
  category: ai_telemetry
  product: ails
detection:
  selection:
    event_type: "a2a.authentication"
    payload.result: "failed"
  condition: selection
falsepositives:
  - Expired credentials during normal rotation
tags:
  - ails.a2a
  - attack.initial_access
```

### I.3 Sigma Backend Conversion

These Sigma rules can be converted to SIEM-specific query languages
using the Sigma CLI or pySigma converters:

```bash
# Convert to Splunk SPL
sigma convert -t splunk ails-rules/

# Convert to Elastic KQL
sigma convert -t elasticsearch ails-rules/

# Convert to Microsoft Sentinel KQL
sigma convert -t microsoft365defender ails-rules/

# Convert to Chronicle YARA-L
sigma convert -t chronicle ails-rules/
```

Organizations adopting AILS SHOULD maintain a Sigma rule repository
corresponding to their deployed AILS alert conditions and extend it
with organization-specific detection rules.

## APPENDIX J: WEBHOOK PAYLOAD SPECIFICATION

*Informative*

### J.1 Purpose

Section 27 defines webhook as a supported alert destination type but
does not specify the webhook payload format. This appendix defines a
standard webhook payload format for AILS alert notifications, enabling
plug-and-play integration with incident management platforms (PagerDuty,
OpsGenie, Slack, custom endpoints) without custom transformation.

### J.2 Webhook Transport Requirements

Webhook delivery SHOULD use:

- **Method:** HTTP POST
- **Content-Type:** `application/json; charset=utf-8`
- **Authentication:** Bearer token or HMAC signature (configurable)
- **Retry:** At-least-once delivery with exponential backoff
  (initial 1s, maximum 60s, maximum 5 retries)
- **Timeout:** 10 seconds per delivery attempt

### J.3 Webhook Payload Format

```json
{
  "ails_webhook_version": "1.0",
  "delivery_id": "<UUID v4 — unique per delivery attempt>",
  "delivered_at": "<ISO 8601 UTC timestamp>",
  "alert": {
    "alert_name": "<from system.alert payload>",
    "severity": "<critical | high | medium | low>",
    "description": "<from system.alert payload>",
    "source": "<from system.alert payload alert_source>"
  },
  "event": {
    "event_id": "<event_id of the system.alert event>",
    "event_type": "system.alert",
    "timestamp": "<timestamp of the system.alert event>"
  },
  "context": {
    "environment": "<from event context, if present>",
    "service_name": "<from event context, if present>",
    "tenant_id": "<from event tenant, if present>",
    "session_id": "<from event correlation, if present>",
    "trace_id": "<from event correlation>"
  },
  "triggering_events": [
    {
      "event_id": "<event_id of triggering event>",
      "event_type": "<event_type of triggering event>",
      "timestamp": "<timestamp of triggering event>",
      "summary": "<human-readable summary of the triggering event>"
    }
  ],
  "indicators": [
    {
      "type": "<indicator type>",
      "value": "<indicator value>",
      "role": "<indicator role>"
    }
  ],
  "links": {
    "event_url": "<URL to view the alert event in the AILS dashboard, if available>",
    "runbook_url": "<URL to the runbook for this alert, if configured>"
  },
  "signature": "<HMAC-SHA256 of the payload, if HMAC authentication is configured>"
}
```

### J.4 Field Descriptions

| Field | Required | Description |
|---|---|---|
| `ails_webhook_version` | MUST | Version of the webhook payload format. `"1.0"` for this version. |
| `delivery_id` | MUST | Unique identifier for this delivery attempt. Used for deduplication by receivers. |
| `delivered_at` | MUST | Timestamp of the delivery attempt, distinct from the alert timestamp. |
| `alert` | MUST | Core alert information extracted from the `system.alert` event payload. |
| `event` | MUST | Identity of the `system.alert` event itself. |
| `context` | SHOULD | Deployment and session context for the alert. |
| `triggering_events` | SHOULD | Summary of the events that triggered the alert, from `triggering_event_ids`. |
| `indicators` | OPTIONAL | Indicators of compromise from the alert event's `indicators` array. |
| `links` | OPTIONAL | URLs for event investigation and response procedures. |
| `signature` | OPTIONAL | HMAC-SHA256 signature for payload integrity verification. |

### J.5 HMAC Signature Computation

When HMAC authentication is configured:

1. The signature is computed over the canonical JSON serialization
   (RFC 8785) of the payload excluding the `signature` field itself.
2. The HMAC key is a shared secret configured between the AILS
   implementation and the webhook receiver.
3. The algorithm is HMAC-SHA256.
4. The signature is encoded as a lowercase hexadecimal string.
5. The signature is additionally included in the HTTP header
   `X-AILS-Signature` for receivers that validate headers before
   parsing the body.

### J.6 Platform-Specific Adaptation

The standard webhook payload is designed to be transformable to
platform-specific formats:

| Platform | Adaptation |
|---|---|
| PagerDuty Events API v2 | Map `alert.severity` to PagerDuty `severity`. Map `alert.description` to `summary`. Map `delivery_id` to `dedup_key`. |
| OpsGenie Alert API | Map `alert.alert_name` to `alias`. Map `alert.severity` to `priority` (P1–P5). Map `indicators` to `details`. |
| Slack Incoming Webhooks | Format payload as Slack Block Kit message with severity-colored sidebar. |
| Microsoft Teams | Format as Adaptive Card with alert summary, context, and indicator table. |
| Generic HTTP | Deliver the standard payload as-is. |

Implementations MAY provide built-in adapters for these platforms.
The standard webhook format is the canonical format; platform-specific
formats are transformations of it.

## APPENDIX K: COMPLETE EVENT EXAMPLES

*Informative*

### K.1 Purpose

This appendix provides complete, valid AILS event examples that
conform to the Event Envelope schema (Section 5) and the payload
specifications of their respective event types. These examples
demonstrate correct envelope construction, correlation, provenance
tagging, redaction, and integrity envelope usage across conformance
levels.

### K.2 Example 1: `inference.call` (Level 1)

A successful OpenAI GPT-4.1 chat completion with default redaction
applied. No integrity fields (Level 1).

```json
{
  "ails_version": "1.0.0",
  "event_id": "a1b2c3d4-e5f6-4a7b-8c9d-0e1f2a3b4c5d",
  "event_type": "inference.call",
  "timestamp": "2026-04-11T14:30:00.123Z",
  "emitter": {
    "id": "llm-gateway-pod-7f8a9b",
    "name": "llm-gateway",
    "version": "2.4.1",
    "type": "gateway"
  },
  "actor": {
    "type": "agent",
    "id": "agent-research-001",
    "name": "Research Assistant",
    "agent_identity": {
      "framework": "langchain",
      "model": "gpt-4.1"
    }
  },
  "correlation": {
    "trace_id": "4bf92f3577b34da6a3ce929d0e0e4736",
    "span_id": "00f067aa0ba902b7",
    "parent_span_id": null,
    "session_id": "session-usr-20260411-001",
    "request_id": "req-88a1b2c3"
  },
  "data_classification": "USER_TRUSTED",
  "payload": {
    "provider": "openai",
    "model": {
      "name": "gpt-4.1",
      "version": "2026-03-15"
    },
    "status": "success",
    "input": {
      "format": "messages",
      "message_count": 3,
      "token_count": 1250,
      "content": "[REDACTED]",
      "has_system_prompt": true,
      "has_images": false,
      "has_documents": true
    },
    "output": {
      "token_count": 847,
      "finish_reason": "stop",
      "content": "[REDACTED]",
      "has_tool_calls": false,
      "refusal": false
    },
    "parameters": {
      "temperature": 0.7,
      "max_tokens": 2048,
      "top_p": 1.0
    },
    "usage": {
      "input_tokens": 1250,
      "output_tokens": 847,
      "total_tokens": 2097,
      "input_cached_tokens": 0,
      "cost": {
        "amount": 0.0083,
        "currency": "USD",
        "unit": "per_request"
      }
    },
    "latency": {
      "total_ms": 2340,
      "time_to_first_token_ms": 410,
      "provider_processing_ms": 2180
    },
    "streaming": true,
    "cached": false
  },
  "context": {
    "environment": "production",
    "region": "us-east-1",
    "service_name": "research-agent-service"
  },
  "metadata": {
    "latency_ms": 2340,
    "sdk_name": "ails-python",
    "sdk_version": "0.9.0"
  },
  "redaction": {
    "profile": "default",
    "redacted_fields": [
      "payload.input.content",
      "payload.output.content"
    ],
    "redaction_method": "omitted"
  },
  "severity": "info"
}
```

### K.3 Example 2: `agent.step` (Level 1)

An agent reasoning step that selects a tool, correlated to the
inference call that produced the reasoning.

```json
{
  "ails_version": "1.0.0",
  "event_id": "b2c3d4e5-f6a7-4b8c-9d0e-1f2a3b4c5d6e",
  "event_type": "agent.step",
  "timestamp": "2026-04-11T14:30:02.500Z",
  "emitter": {
    "id": "agent-runtime-pod-3c4d5e",
    "name": "agent-runtime",
    "version": "1.8.0",
    "type": "runtime"
  },
  "actor": {
    "type": "agent",
    "id": "agent-research-001",
    "name": "Research Assistant",
    "agent_identity": {
      "framework": "langchain",
      "model": "gpt-4.1"
    }
  },
  "correlation": {
    "trace_id": "4bf92f3577b34da6a3ce929d0e0e4736",
    "span_id": "1a2b3c4d5e6f7a8b",
    "parent_span_id": "00f067aa0ba902b7",
    "session_id": "session-usr-20260411-001",
    "request_id": "req-88a1b2c3"
  },
  "data_classification": "USER_TRUSTED",
  "payload": {
    "agent_id": "agent-research-001",
    "step_index": 2,
    "step_type": "reasoning",
    "status": "completed",
    "goal": "[REDACTED]",
    "decision": {
      "description": "[REDACTED]",
      "confidence": 0.92,
      "alternatives_considered": 3
    },
    "reasoning": {
      "disclosure": "summary",
      "summary": "Selected database query tool to retrieve quarterly revenue data based on user request for financial analysis."
    },
    "tool_selected": "analytics_db_query",
    "input_event_ids": [
      "a1b2c3d4-e5f6-4a7b-8c9d-0e1f2a3b4c5d"
    ],
    "output_event_ids": [
      "c3d4e5f6-a7b8-4c9d-0e1f-2a3b4c5d6e7f"
    ],
    "iteration": 1,
    "max_iterations": 10
  },
  "context": {
    "environment": "production",
    "region": "us-east-1",
    "service_name": "research-agent-service"
  },
  "metadata": {
    "latency_ms": 45
  },
  "redaction": {
    "profile": "default",
    "redacted_fields": [
      "payload.goal",
      "payload.decision.description"
    ],
    "redaction_method": "omitted"
  },
  "severity": "info"
}
```

### K.4 Example 3: `tool.call` (Level 1)

An MCP tool invocation with redacted arguments and output, linked to
the agent step that triggered it.

```json
{
  "ails_version": "1.0.0",
  "event_id": "c3d4e5f6-a7b8-4c9d-0e1f-2a3b4c5d6e7f",
  "event_type": "tool.call",
  "timestamp": "2026-04-11T14:30:03.100Z",
  "emitter": {
    "id": "agent-runtime-pod-3c4d5e",
    "name": "agent-runtime",
    "version": "1.8.0",
    "type": "runtime"
  },
  "actor": {
    "type": "agent",
    "id": "agent-research-001",
    "name": "Research Assistant"
  },
  "correlation": {
    "trace_id": "4bf92f3577b34da6a3ce929d0e0e4736",
    "span_id": "2b3c4d5e6f7a8b9c",
    "parent_span_id": "1a2b3c4d5e6f7a8b",
    "session_id": "session-usr-20260411-001",
    "request_id": "req-88a1b2c3"
  },
  "data_classification": "EXTERNAL_UNTRUSTED",
  "payload": {
    "tool_name": "analytics_db_query",
    "tool_type": "mcp_tool",
    "status": "success",
    "invocation": {
      "arguments": "[REDACTED]",
      "arguments_hash": "sha256:f2c8ebfb615809ede78b87299bd510aa15710990b50d6d93ccd50c0c4cf40281",
      "argument_count": 2
    },
    "result": {
      "output": "[REDACTED]",
      "output_hash": "sha256:a26662f58ccf76c6c9501f7c8b23ff74d789fbd7e73c1d89650acfde944cedae",
      "output_size_bytes": 284,
      "output_truncated": false
    },
    "tool_version": "1.2.0",
    "tool_provider": "analytics-mcp-server",
    "latency": {
      "total_ms": 580
    },
    "triggered_by": "b2c3d4e5-f6a7-4b8c-9d0e-1f2a3b4c5d6e",
    "side_effects": {
      "reads": ["database"],
      "writes": [],
      "network_egress": false,
      "state_mutation": "none"
    }
  },
  "metadata": {
    "latency_ms": 580
  },
  "redaction": {
    "profile": "default",
    "redacted_fields": [
      "payload.invocation.arguments",
      "payload.result.output"
    ],
    "redaction_method": "omitted"
  },
  "severity": "info"
}
```

### K.5 Example 4: `policy.decision` with Dual-Subject Evaluation (Level 2)

An AGBAC dual-subject authorization decision with full integrity
envelope.

```json
{
  "ails_version": "1.0.0",
  "event_id": "d4e5f6a7-b8c9-4d0e-1f2a-3b4c5d6e7f80",
  "event_type": "policy.decision",
  "timestamp": "2026-04-11T15:45:12.789Z",
  "emitter": {
    "id": "agbac-pdp-node-01",
    "name": "agbac-policy-engine",
    "version": "3.1.0",
    "type": "security_service"
  },
  "actor": {
    "type": "agent",
    "id": "agent-finance-bot-042",
    "name": "Finance Automation Agent",
    "agent_identity": {
      "framework": "custom",
      "model": "claude-sonnet-4-20250514",
      "spiffe_id": "spiffe://corp.example.com/agents/finance-bot-042"
    }
  },
  "correlation": {
    "trace_id": "8a3c5e7f19b24d6a8c0e2f4a6b8d0e2f",
    "span_id": "3c4d5e6f7a8b9c0d",
    "parent_span_id": "0a1b2c3d4e5f6a7b",
    "session_id": "als-session-fin-20260411",
    "request_id": "req-fin-7729"
  },
  "data_classification": "SYSTEM_TRUSTED",
  "payload": {
    "decision": "allow",
    "policy_type": "dual_subject",
    "action": "execute",
    "resource": "payment-processing/initiate-transfer",
    "evaluated_at": "2026-04-11T15:45:12.789Z",
    "policy_name": "agbac-finance-dual-subject-v2",
    "policy_version": "2.3.0",
    "reason": "Both agent and human principal authorized for payment initiation within delegation scope.",
    "subjects": {
      "primary_subject": "agent-finance-bot-042",
      "primary_subject_type": "agent",
      "secondary_subject": "sha256:e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855",
      "secondary_subject_type": "human",
      "delegation_valid": true
    },
    "evaluation_inputs": {
      "agent_authorized": true,
      "human_authorized": true,
      "delegation_valid": true
    }
  },
  "context": {
    "environment": "production",
    "region": "us-west-2",
    "service_name": "finance-governance"
  },
  "integrity": {
    "sequence_number": 147,
    "stream_id": "als-session-fin-20260411",
    "chain_hash": "sha256:7b3e8f1a2c4d6e8f0a2b4c6d8e0f1a3b5c7d9e1f3a5b7c9d1e3f5a7b9c1d3e5f"
  },
  "severity": "info"
}
```

### K.6 Example 5: `credential.issued` with Delegation Context (Level 2)

An AGBAC delegation token issuance with full delegation context and
integrity envelope.

```json
{
  "ails_version": "1.0.0",
  "event_id": "e5f6a7b8-c9d0-4e1f-2a3b-4c5d6e7f8091",
  "event_type": "credential.issued",
  "timestamp": "2026-04-11T15:44:58.456Z",
  "emitter": {
    "id": "als-credential-svc-02",
    "name": "als-credential-service",
    "version": "2.0.1",
    "type": "security_service"
  },
  "actor": {
    "type": "system",
    "id": "als-credential-svc-02",
    "name": "ALS Credential Service"
  },
  "correlation": {
    "trace_id": "8a3c5e7f19b24d6a8c0e2f4a6b8d0e2f",
    "span_id": "4d5e6f7a8b9c0d1e",
    "parent_span_id": null,
    "session_id": "als-session-fin-20260411"
  },
  "data_classification": "SYSTEM_TRUSTED",
  "payload": {
    "credential_type": "delegation_token",
    "credential_fingerprint": "sha256:9f86d081884c7d659a2feaa0c55ad015a3bf4f1b2b0b822cd15d6c15b0f00a08",
    "issued_to": "agent-finance-bot-042",
    "issued_by": "als-credential-svc-02",
    "issued_at": "2026-04-11T15:44:58.456Z",
    "valid_from": "2026-04-11T15:44:58.456Z",
    "valid_until": "2026-04-11T16:44:58.456Z",
    "issuance_mode": "exchange",
    "session_id": "als-session-fin-20260411",
    "delegation_context": {
      "agent_subject": "agent-finance-bot-042",
      "human_subject": "sha256:e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855",
      "delegation_method": "explicit",
      "intent_summary": "Process quarterly vendor payments for Q1 2026 per approved payment schedule.",
      "granted_at": "2026-04-11T15:44:50.000Z"
    }
  },
  "context": {
    "environment": "production",
    "region": "us-west-2",
    "service_name": "finance-governance"
  },
  "integrity": {
    "sequence_number": 142,
    "stream_id": "als-session-fin-20260411",
    "chain_hash": "sha256:2a4b6c8d0e1f3a5b7c9d1e3f5a7b9c1d3e5f7a9b1c3d5e7f9a1b3c5d7e9f1a3b"
  },
  "severity": "info"
}
```

### K.7 Example 6: `system.alert` — Chain Hash Integrity Failure (Level 2)

A critical integrity alert with indicators and full integrity envelope.

```json
{
  "ails_version": "1.0.0",
  "event_id": "f6a7b8c9-d0e1-4f2a-3b4c-5d6e7f809102",
  "event_type": "system.alert",
  "timestamp": "2026-04-11T16:02:33.891Z",
  "emitter": {
    "id": "audit-integrity-checker-01",
    "name": "ails-integrity-verifier",
    "version": "1.0.0",
    "type": "security_service"
  },
  "actor": {
    "type": "system",
    "id": "audit-integrity-checker-01",
    "name": "AILS Integrity Verifier"
  },
  "correlation": {
    "trace_id": "c0d1e2f3a4b5c6d7e8f9a0b1c2d3e4f5",
    "span_id": "5e6f7a8b9c0d1e2f",
    "parent_span_id": null,
    "session_id": "als-session-fin-20260411"
  },
  "data_classification": "SYSTEM_TRUSTED",
  "payload": {
    "alert_name": "chain_hash_integrity_failure",
    "alert_source": "ails",
    "severity": "critical",
    "description": "Chain hash verification failed for event stream als-session-fin-20260411. Event at sequence number 156 has chain_hash that does not match recomputed value. Possible event modification or insertion detected.",
    "triggered_at": "2026-04-11T16:02:33.891Z",
    "triggering_event_ids": [
      "a8b9c0d1-e2f3-4a5b-6c7d-8e9f0a1b2c3d"
    ],
    "triggering_pattern": "Chain hash mismatch: expected sha256:4a5b6c... observed sha256:9e8d7c...",
    "alert_destination": "webhook",
    "auto_action_taken": "session_halted"
  },
  "context": {
    "environment": "production",
    "region": "us-west-2",
    "service_name": "finance-governance"
  },
  "integrity": {
    "sequence_number": 158,
    "stream_id": "als-session-fin-20260411",
    "chain_hash": "sha256:5b6c7d8e9f0a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6e7f8a9b0c1d2e3f4a5b6c"
  },
  "severity": "critical",
  "indicators": [
    {
      "type": "hash",
      "value": "sha256:9e8d7c6b5a4f3e2d1c0b9a8f7e6d5c4b3a2f1e0d",
      "role": "subject"
    },
    {
      "type": "agent_id",
      "value": "als-session-fin-20260411",
      "role": "related"
    }
  ]
}
```

### K.8 Chain Hash Test Vectors

The following test vectors provide verifiable chain hash computations
per the algorithm defined in Section 22.4.3 (REQ-22.4.8 and
REQ-22.4.9). All SHA-256 values have been computed and are
independently verifiable using any compliant SHA-256 implementation.

#### Test Vector 1 — First Event in a Stream

Per REQ-22.4.9, the first event uses `stream_id` as the predecessor.

**Inputs:**

- `stream_id`: `session-abc-123`
- `event_id`: `550e8400-e29b-41d4-a716-446655440001`
- `timestamp`: `2026-04-11T10:00:00.000Z`

**Concatenation (colon-delimited per REQ-22.4.8):**

```
session-abc-123:550e8400-e29b-41d4-a716-446655440001:2026-04-11T10:00:00.000Z
```

**Result:**

```
chain_hash = sha256:09e9993c2573ea2b8d1473e721f35a2157e6fda20d5c018c0fe665d8af27eca6
```

#### Test Vector 2 — Second Event in the Same Stream

Per REQ-22.4.8, subsequent events use the previous event's `chain_hash`
as the predecessor.

**Inputs:**

- `previous_chain_hash`: `sha256:09e9993c2573ea2b8d1473e721f35a2157e6fda20d5c018c0fe665d8af27eca6`
- `event_id`: `550e8400-e29b-41d4-a716-446655440002`
- `timestamp`: `2026-04-11T10:00:01.500Z`

**Concatenation:**

```
sha256:09e9993c2573ea2b8d1473e721f35a2157e6fda20d5c018c0fe665d8af27eca6:550e8400-e29b-41d4-a716-446655440002:2026-04-11T10:00:01.500Z
```

**Result:**

```
chain_hash = sha256:88b2859e504f669e236dca1dbb455e15a1758ad395ddaff04d5a242ff852a9fc
```

#### Test Vector 3 — Third Event in the Same Stream

**Inputs:**

- `previous_chain_hash`: `sha256:88b2859e504f669e236dca1dbb455e15a1758ad395ddaff04d5a242ff852a9fc`
- `event_id`: `550e8400-e29b-41d4-a716-446655440003`
- `timestamp`: `2026-04-11T10:00:03.200Z`

**Concatenation:**

```
sha256:88b2859e504f669e236dca1dbb455e15a1758ad395ddaff04d5a242ff852a9fc:550e8400-e29b-41d4-a716-446655440003:2026-04-11T10:00:03.200Z
```

**Result:**

```
chain_hash = sha256:6710f0dc83a244f1d3ef524168aa6e1da5fadd61d037e3574722dc87cf10e583
```

#### Verification

Implementers can verify these test vectors using any SHA-256
implementation. Example using Python:

```python
import hashlib

# Test Vector 1
input_1 = "session-abc-123:550e8400-e29b-41d4-a716-446655440001:2026-04-11T10:00:00.000Z"
hash_1 = "sha256:" + hashlib.sha256(input_1.encode("utf-8")).hexdigest()
assert hash_1 == "sha256:09e9993c2573ea2b8d1473e721f35a2157e6fda20d5c018c0fe665d8af27eca6"

# Test Vector 2
input_2 = hash_1 + ":550e8400-e29b-41d4-a716-446655440002:2026-04-11T10:00:01.500Z"
hash_2 = "sha256:" + hashlib.sha256(input_2.encode("utf-8")).hexdigest()
assert hash_2 == "sha256:88b2859e504f669e236dca1dbb455e15a1758ad395ddaff04d5a242ff852a9fc"

# Test Vector 3
input_3 = hash_2 + ":550e8400-e29b-41d4-a716-446655440003:2026-04-11T10:00:03.200Z"
hash_3 = "sha256:" + hashlib.sha256(input_3.encode("utf-8")).hexdigest()
assert hash_3 == "sha256:6710f0dc83a244f1d3ef524168aa6e1da5fadd61d037e3574722dc87cf10e583"
```

Example using OpenSSL command line:

```bash
echo -n "session-abc-123:550e8400-e29b-41d4-a716-446655440001:2026-04-11T10:00:00.000Z" | openssl dgst -sha256
# Expected: 09e9993c2573ea2b8d1473e721f35a2157e6fda20d5c018c0fe665d8af27eca6
```

## APPENDIX L: REFERENCES

### L.1 Normative References

The following standards and specifications are referenced normatively
by this standard. Conformant implementations MUST comply with the
applicable requirements of these references.

**[RFC 2119]** Bradner, S., "Key words for use in RFCs to Indicate
Requirement Levels", BCP 14, RFC 2119, DOI 10.17487/RFC2119,
March 1997. https://www.rfc-editor.org/rfc/rfc2119

**[RFC 8174]** Leiba, B., "Ambiguity of Uppercase vs Lowercase in
RFC 2119 Key Words", BCP 14, RFC 8174, DOI 10.17487/RFC8174,
May 2017. https://www.rfc-editor.org/rfc/rfc8174

**[RFC 8259]** Bray, T., Ed., "The JavaScript Object Notation (JSON)
Data Interchange Format", STD 90, RFC 8259, DOI 10.17487/RFC8259,
December 2017. https://www.rfc-editor.org/rfc/rfc8259

**[RFC 9562]** Davis, K., Peabody, B., and P. Leach, "Universally
Unique IDentifiers (UUIDs)", RFC 9562, DOI 10.17487/RFC9562,
May 2024. https://www.rfc-editor.org/rfc/rfc9562

**[RFC 8785]** Rundgren, A., Jordan, B., and S. Erdtman, "JSON
Canonicalization Scheme (JCS)", RFC 8785, DOI 10.17487/RFC8785,
June 2020. https://www.rfc-editor.org/rfc/rfc8785

**[RFC 4648]** Josefsson, S., "The Base16, Base32, and Base64 Data
Encodings", RFC 4648, DOI 10.17487/RFC4648, October 2006.
https://www.rfc-editor.org/rfc/rfc4648

**[RFC 8032]** Josefsson, S. and I. Liusvaara, "Edwards-Curve Digital
Signature Algorithm (EdDSA)", RFC 8032, DOI 10.17487/RFC8032,
January 2017. https://www.rfc-editor.org/rfc/rfc8032

**[RFC 5424]** Gerhards, R., "The Syslog Protocol", RFC 5424,
DOI 10.17487/RFC5424, March 2009.
https://www.rfc-editor.org/rfc/rfc5424

**[W3C-TRACE]** W3C, "Trace Context", W3C Recommendation,
2021-11-23. https://www.w3.org/TR/trace-context/

**[ISO 8601]** ISO, "Date and time — Representations for information
interchange", ISO 8601:2019.

**[ISO 4217]** ISO, "Codes for the representation of currencies",
ISO 4217:2015.

**[SemVer]** Preston-Werner, T., "Semantic Versioning 2.0.0".
https://semver.org/spec/v2.0.0.html

### L.2 Informative References

The following standards and specifications are referenced for context,
mapping, or guidance. They do not impose normative requirements on AILS
implementations.

**[OpenTelemetry]** OpenTelemetry Project, "OpenTelemetry
Specification". https://opentelemetry.io/docs/specs/otel/

**[OCSF]** Open Cybersecurity Schema Framework. https://schema.ocsf.io/

**[MITRE-ATLAS]** MITRE, "Adversarial Threat Landscape for Artificial-
Intelligence Systems (ATLAS)". https://atlas.mitre.org/

**[Sigma]** Sigma Project, "Sigma Rule Specification".
https://sigmahq.io/

**[EU-AI-ACT]** European Parliament and Council, "Regulation (EU)
2024/1689 laying down harmonised rules on artificial intelligence
(Artificial Intelligence Act)", 2024.

**[NIST-AI-RMF]** NIST, "Artificial Intelligence Risk Management
Framework (AI RMF 1.0)", NIST AI 100-1, January 2023.
https://www.nist.gov/artificial-intelligence/executive-order-safe-secure-and-trustworthy-artificial-intelligence

**[NIST-CSF]** NIST, "Cybersecurity Framework (CSF) 2.0", NIST CSWP
29, February 2024. https://www.nist.gov/cyberframework

**[GDPR]** European Parliament and Council, "Regulation (EU) 2016/679
on the protection of natural persons with regard to the processing
of personal data (General Data Protection Regulation)", 2016.

**[SOX]** United States Congress, "Sarbanes-Oxley Act of 2002",
Public Law 107-204, 2002.

**[HIPAA]** United States Congress, "Health Insurance Portability and
Accountability Act of 1996", Public Law 104-191, 1996.

**[ISO-42001]** ISO/IEC, "Information technology — Artificial
intelligence — Management system", ISO/IEC 42001:2023.

**[DAE]** Reilly, S.K., "Deterministic Agent Execution Security
Standard", v1.0.0, 2026.

**[AGBAC]** Reilly, S.K., "Agent-Based Access Control", v1.0.0, 2026.

**[ALS]** Reilly, S.K., "Agent Layer Security Protocol Standard",
v2.0.0, 2026.

**[AI-SCS]** Reilly, S.K., "AI Supply Chain Standard", v1.0.0, 2026.

**[ACS]** Reilly, S.K., "Agent Capability Standard", v1.0.0, 2026.

**[MCP-INTEGRITY]** Reilly, S.K., "MCP Integrity Standard", v1.0.0,
2026.

**[A2A]** Google, "Agent-to-Agent Protocol (A2A)", RC v1.0, 2025.

**[MCP]** Anthropic, "Model Context Protocol Specification",
2025-11-25. https://spec.modelcontextprotocol.io/

**[DSSE]** Secure Systems Lab, "Dead Simple Signing Envelope (DSSE)".
https://github.com/secure-systems-lab/dsse

### End AI Logging Standard
