# OfflineLink Zambia — System Requirements Specification

**Project:** OfflineLink Zambia  
**Research title:** Design and Experimental Evaluation of an Offline-First Agricultural Data Collection Application for Unstable and Low-Bandwidth Network Conditions in Zambia  
**Document type:** System Requirements Specification  
**Status:** Approved research baseline; requirements specification under refinement  
**Version:** 0.2  
**Date:** 11 November 2026

---

## 1. Introduction

### 1.1 Purpose

This document defines the functional and non-functional requirements for OfflineLink Zambia.

It establishes the expected system behavior, research boundaries, evaluation criteria and acceptance principles that will guide system design, implementation, testing and experimental evaluation.

The document serves as the authoritative reference for determining what the system is required to do. Implementation teams must use these requirements alongside the architecture, API contract, synchronization protocol and test plan.

### 1.2 Project Overview

OfflineLink Zambia is an offline-first agricultural data collection application intended to enable agricultural field officers to capture, view and update agricultural records without requiring continuous internet connectivity.

Changes made while offline must be retained locally until synchronization with a central server becomes possible.

The project also provides a controlled experimental environment for comparing full-record synchronization and change-log synchronization under emulated low-bandwidth and intermittent network conditions.

### 1.3 Intended Users

**Agricultural Field Officer**

The primary application user responsible for capturing, viewing and updating farmer, farm, crop and observation records.

**Research Operator**

The researcher responsible for configuring synthetic workloads and network profiles, executing experiments, monitoring trials, verifying results and exporting research data.

---

## 2. Research Objectives

The following objectives have been approved and form part of the fixed research baseline.

1. To design and develop an offline-first agricultural data collection application that enables agricultural field officers to capture, view and update farmer and crop records without continuous internet connectivity and retain changes locally until synchronization is possible.

2. To implement full-record and change-log synchronization mechanisms within the application to transfer locally stored agricultural data to a central server while applying consistent retry, duplicate-prevention and data-correctness rules.

3. To experimentally evaluate and compare the two synchronization mechanisms under controlled emulated low-bandwidth and intermittent network conditions using communication overhead, verified synchronization time, completion rate and data consistency as evaluation measures.

These objectives must not be silently rewritten or replaced during implementation.

---

## 3. Research Questions

The system and experimental evaluation must address the following approved research questions.

**RQ1:** How do full-record and change-log synchronization compare in communication overhead, verified synchronization time and completion rate under controlled low-bandwidth and intermittent network conditions?

**RQ2:** How does the distribution of record edits, involving many records compared with repeated edits to fewer records, affect the comparative performance of the two synchronization mechanisms?

**RQ3:** To what extent do the two synchronization mechanisms preserve correctness and achieve the expected final server state after synchronization attempts, retries and connectivity interruptions?

---

## 4. System Scope

### 4.1 Included Scope

The system shall cover:

- Farmer record management.
- Farm record management.
- Crop record management.
- Agricultural observation record management.
- Offline-capable data collection.
- Local data persistence.
- Local synchronization operation queuing.
- Full-record synchronization.
- Change-log synchronization.
- Retry handling and duplicate prevention.
- Central server persistence.
- Synchronization status and processing acknowledgements.
- Deterministic synthetic dataset generation.
- Research workload configuration.
- Controlled network profile configuration.
- Experimental execution and measurement.
- Final-state verification.
- Research result storage and export.
- Automated testing and reproducibility documentation.

### 4.2 Excluded Scope

The following capabilities are outside the approved research scope:

- Agricultural marketplace or trading functionality.
- Payment processing.
- AI-based crop diagnosis or recommendations.
- Satellite imagery, mapping or external weather APIs.
- Production-scale deployment.
- Unrestricted multi-user concurrent editing and conflict-resolution features.
- Collection of actual personally identifiable farmer data for experimental trials.

Any proposed addition outside this scope must undergo explicit review before implementation.

---

## 5. System Users and Responsibilities

| User | Responsibilities |
|---|---|
| Field Officer | Create, view and update agricultural records; perform operations while offline; monitor synchronization status |
| Research Operator | Configure datasets, workloads and network profiles; execute trials; monitor experimental outcomes; verify and export results |

The research operator's experimental responsibilities must be distinguished from ordinary field-officer record-management activities.

---

## 6. Functional Requirements

Functional requirements define the behavior the system must provide.

Requirement identifiers are stable references and must be retained when the requirements are implemented, tested or revised.

### 6.1 Agricultural Record Management

| ID | Requirement |
|---|---|
| FR-001 | The client shall allow a field officer to create farmer records. |
| FR-002 | The client shall allow a field officer to view farmer records. |
| FR-003 | The client shall allow a field officer to update farmer records. |
| FR-004 | The client shall allow a field officer to create, view and update farm records associated with farmers. |
| FR-005 | The client shall allow a field officer to create, view and update crop records associated with farms. |
| FR-006 | The client shall allow a field officer to create, view and update agricultural observation records. |
| FR-007 | The application shall validate record data before accepting a local or server-side write. |

### 6.2 Offline Operation and Local Persistence

| ID | Requirement |
|---|---|
| FR-008 | The client shall persist supported records locally using IndexedDB. |
| FR-009 | The client shall permit supported record operations while the server is unreachable. |
| FR-010 | The client shall retain pending local operations until they are acknowledged as successfully processed. |
| FR-011 | The client shall show synchronization state, including pending, in-progress, completed and failed work. |
| FR-012 | The client shall not require continuous internet connectivity for local record capture and viewing. |

### 6.3 Data Synchronization

| ID | Requirement |
|---|---|
| FR-013 | The system shall implement full-record synchronization. |
| FR-014 | The system shall implement change-log synchronization. |
| FR-015 | Both synchronization methods shall use the same logical workload and equivalent correctness requirements. |
| FR-016 | The system shall assign a stable unique identifier to each logical operation. |
| FR-017 | The server shall prevent repeated delivery of an operation from causing unintended duplicate effects. |
| FR-018 | The system shall apply a documented retry policy to eligible failed or interrupted operations. |
| FR-019 | The system shall provide processing acknowledgements that allow the client to distinguish accepted work from verified completion. |
| FR-020 | The system shall support verification of the final server state against a deterministic expected state. |
| FR-021 | The system shall retain sufficient operation and synchronization metadata for experiment analysis. |

### 6.4 Research and Experimental Measurement

| ID | Requirement |
|---|---|
| FR-022 | The research tooling shall support deterministic synthetic datasets. |
| FR-023 | The research tooling shall support workload scenarios involving edits across many records and repeated edits to fewer records. |
| FR-024 | The research tooling shall associate each trial with a defined synchronization method, workload, network profile and configuration. |
| FR-025 | The research tooling shall record communication overhead, verified synchronization time, completion rate and data-consistency outcomes. |
| FR-026 | The research tooling shall distinguish request-body bytes from total captured network traffic. |
| FR-027 | The research tooling shall store raw trial results separately from derived analysis. |
| FR-028 | The research tooling shall export results in a documented machine-readable format. |

---

## 7. Non-Functional Requirements

Non-functional requirements define the quality attributes, constraints and operational expectations of the system.

| ID | Category | Requirement |
|---|---|---|
| NFR-001 | Correctness | A completed trial must be evaluated against its deterministic expected server state; HTTP success alone is not proof of correctness. |
| NFR-002 | Offline usability | Local record capture and viewing must remain available without a live server connection. |
| NFR-003 | Reliability | Interrupted synchronization must not silently lose pending operations. |
| NFR-004 | Idempotency | Re-delivery of an operation must not create unintended duplicate effects. |
| NFR-005 | Reproducibility | Trial configuration, workload seed or identity, network profile, software version and result files must be recorded. |
| NFR-006 | Experimental fairness | Synchronization methods must share equivalent logical operations, ordering, transport, retry policy and verification rules. |
| NFR-007 | Measurement integrity | Request-body bytes and total captured traffic must be reported as separate measures. |
| NFR-008 | Data privacy | Experimental datasets must be synthetic and must not contain real farmer personal data. |
| NFR-009 | Maintainability | Backend and frontend responsibilities, API contracts and synchronization protocol must be documented. |
| NFR-010 | Testability | Core logic must be testable independently, with automated backend, frontend and integration tests. |
| NFR-011 | Configuration security | Secrets and environment-specific configuration must not be committed to source control. |
| NFR-012 | Scope control | New features outside the approved research scope require explicit review before implementation. |

---

## 8. Agricultural Data Requirements

### 8.1 Supported Entities

The system shall support the following four agricultural data entities:

1. **Farmer** — represents a farmer record.
2. **Farm** — represents a farm associated with a farmer.
3. **Crop** — represents a crop record associated with a farm.
4. **Observation** — represents an agricultural observation associated with an applicable agricultural record.

### 8.2 Proposed Initial Data Fields

The approved initial data dictionary direction identifies the following fields.

| Entity | Proposed fields |
|---|---|
| Farmer | `farmer_id`, `first_name`, `last_name`, `created_at`, `updated_at` |
| Farm | `farm_id`, `farmer_id`, `farm_name`, `district`, `created_at`, `updated_at` |
| Crop | `crop_id`, `farm_id`, `crop_name`, `season`, `planting_date`, `created_at`, `updated_at` |
| Observation | `observation_id`, `crop_id`, `observation_date`, `observation_type`, `notes`, `created_at`, `updated_at` |

These fields represent the current proposed data dictionary. Exact field types, requiredness, optionality, validation constraints and relationship enforcement must be finalized in the data-model specification before implementation.

### 8.3 Data Validation Direction

The initial validation direction requires:

- Unique identifiers for supported entities.
- Valid references between related records.
- Consistent date values.
- Nonblank values for required fields.
- Explicit handling of optional or nullable fields.
- Consistent validation rules on the client and server.
- Synthetic data for experimental workloads.
- Avoidance of unnecessary sensitive farmer information.

The precise validation rules must be documented before the relevant data models and API schemas are implemented.

---

## 9. Evaluation Requirements and Definitions

The experimental evaluation must use the same core definitions for both synchronization approaches.

### 9.1 Communication Overhead

Communication overhead must distinguish between:

**Request-body bytes:** The serialized application request payload bytes transmitted during synchronization.

**Total captured network traffic:** Traffic measured through the defined network capture procedure. Depending on the capture method, this may include headers, acknowledgements, retransmissions and other transport-level traffic.

These measures must be reported separately.

### 9.2 Verified Synchronization Time

Verified synchronization time is the elapsed time from the defined synchronization start until all of the following conditions are satisfied:

1. All intended logical operations have been processed.
2. The pending operation queue is empty.
3. The final server state matches the deterministic expected state.
4. Operation receipts or equivalent evidence show no unintended duplicate effects.

An HTTP 200 response alone does not satisfy the completion definition.

The precise timing start event and measurement procedure must be documented in the synchronization protocol.

### 9.3 Completion Rate

Completion rate is calculated as:

**Completion rate = Verified completed logical operations / Scheduled logical operations**

The exact denominator and completion criteria must be recorded for every trial.

A logical operation must not be counted as completed merely because it was transmitted or received an HTTP response.

### 9.4 Data Consistency

Data consistency is determined by comparing the final server state with the deterministic expected state generated from the initial dataset and logical workload.

The verification must identify relevant:

- Missing operations.
- Unexpected operations or effects.
- Duplicate effects.
- Incorrect record values.
- Incorrect final relationships or state.

---

## 10. Experimental Workload Requirements

The research environment must support controlled workloads that allow the two synchronization approaches to be compared fairly.

### 10.1 Workload Scenarios

The workload design must include:

**Scenario A — Distributed Record Edits**

Operations are distributed across many agricultural records.

**Scenario B — Repeated Record Edits**

Multiple operations target fewer records, allowing the effect of repeated edits to be evaluated.

### 10.2 Workload Consistency

For a fair comparison:

- Both synchronization methods must receive equivalent logical operations.
- The initial dataset must be equivalent.
- Logical operation ordering must be preserved where required.
- Workload identity or seed must be recorded.
- The deterministic expected final state must be generated before evaluation.
- Method-specific payload representation must be distinguished from the underlying logical workload.

### 10.3 Network Conditions

Network conditions must be established using controlled emulation.

Network profiles will be experimental configurations rather than claims that the selected settings represent measured real-world Zambian connectivity.

Practical workload and network-profile parameters will be established during pilot testing and frozen before the main trials.

---

## 11. Technical Constraints and Assumptions

The initial project environment and technology direction are:

| Area | Technology or constraint |
|---|---|
| Development host | Linux Mint |
| Frontend | React and TypeScript |
| Local persistence | IndexedDB |
| Backend | Python and FastAPI |
| Central database | PostgreSQL |
| Network emulation | Linux network namespaces and `tc/netem` |
| Experimental data | Synthetic datasets |
| Development approach | Incremental, contract-driven implementation |

Additional assumptions:

1. Local record capture and viewing must remain available when the central server cannot be reached.
2. Pending operations must remain available for subsequent synchronization attempts.
3. The two synchronization methods must operate under equivalent reliability and verification requirements.
4. Network profiles are controlled experimental settings.
5. The initial repository structure exists, but application source code has not yet been created.

---

## 12. Requirements Traceability and Acceptance

Every functional and non-functional requirement must be traceable to an appropriate implementation component and verification activity.

A requirement shall only be considered implemented when its expected behavior has been demonstrated through a relevant test or recorded verification.

The existence of a directory, source file, generated code or successful application startup is not sufficient evidence that a requirement has been satisfied.

The test plan defines the validation levels, scenarios, evidence and acceptance gates.

### 12.1 Acceptance Principles

- Requirements must be validated against their stated behavior.
- Synchronization correctness must be verified against deterministic expected state.
- Duplicate-prevention behavior must be tested through repeated delivery.
- Offline behavior must be tested while the API is unavailable.
- Experimental metrics must be checked against their defined measurement procedures.
- Failed and interrupted trials must be retained and identified rather than silently discarded.
- Evidence must be associated with the software revision and configuration that produced it.

---

## 13. Change Control

The approved research objectives, research questions, project scope, synchronization semantics and experimental evaluation measures form the research baseline.

Any proposed change affecting these elements must be raised for explicit review before implementation.

Technical clarifications may be introduced where necessary, provided they do not silently alter the approved research direction.

Material changes must:

1. Identify the requirement or decision affected.
2. Explain the reason for the proposed change.
3. Assess the impact on architecture, API compatibility and research validity.
4. Receive approval before implementation.
5. Be reflected in the relevant documentation.
6. Be recorded in the appropriate decision record where material.

---

## 14. Document Status

This specification establishes the approved research requirements and the current system requirements baseline.

The research objectives, questions, scope, principal functional requirements, non-functional requirements and evaluation definitions are retained as approved project requirements.

Detailed data-model constraints, API schemas and synchronization implementation mechanics remain subject to their respective design specifications and approval processes.

**Implementation status:** Not implemented.

**Next related documents:** `architecture.md`, `api-contract.md`, `sync-protocol.md` and `test-plan.md`.

---

**OfflineLink Zambia — System Requirements Specification**

*This document defines what the system must achieve and provides the foundation for implementation and experimental verification.*