# OfflineLink Zambia — System Requirements

**Project:** OfflineLink Zambia  
**Research title:** Design and Experimental Evaluation of an Offline-First Agricultural Data Collection Application for Unstable and Low-Bandwidth Network Conditions in Zambia  
**Status:** Stage 0 baseline — approved proposal  
**Version:** 0.1  
**Date:** 2026-11-07

## 1. Purpose

OfflineLink Zambia is an offline-first agricultural data collection application intended to enable agricultural field officers to capture, view, and update farmer and crop records when continuous internet connectivity is unavailable. Local changes are retained until synchronization is possible.

The application also provides a controlled research environment for experimentally comparing full-record synchronization and change-log synchronization under emulated low-bandwidth and intermittent network conditions.

## 2. Research objectives

The following approved specific objectives are fixed and must not be silently rewritten:

1. To design and develop an offline-first agricultural data collection application that enables agricultural field officers to capture, view and update farmer and crop records without continuous internet connectivity and retain changes locally until synchronization is possible.
2. To implement full-record and change-log synchronization mechanisms within the application to transfer locally stored agricultural data to a central server while applying consistent retry, duplicate-prevention and data-correctness rules.
3. To experimentally evaluate and compare the two synchronization mechanisms under controlled emulated low-bandwidth and intermittent network conditions using communication overhead, verified synchronization time, completion rate and data consistency as evaluation measures.

## 3. Research questions

1. How do full-record and change-log synchronization compare in communication overhead, verified synchronization time and completion rate under controlled low-bandwidth and intermittent network conditions?
2. How does the distribution of record edits, involving many records compared with repeated edits to fewer records, affect the comparative performance of the two synchronization mechanisms?
3. To what extent do the two synchronization mechanisms preserve correctness and achieve the expected final server state after synchronization attempts, retries and connectivity interruptions?

## 4. Users and system boundary

### 4.1 Intended application user
Agricultural field officer who captures and maintains farmer, farm, crop and observation records.

### 4.2 Research operator
Researcher who configures workloads and network profiles, runs experiments, monitors execution and exports results.

### 4.3 In scope
- Farmer, farm, crop and observation record management.
- Offline-capable client application.
- Local persistence and queued local operations.
- Full-record and change-log synchronization.
- Retry handling and duplicate-prevention.
- Server-side persistence.
- Research workload and network-profile configuration.
- Experiment execution, measurement, result storage and export.
- Automated tests and reproducibility documentation.

### 4.4 Out of scope
- Marketplace or trading functionality.
- Payments.
- AI crop diagnosis or recommendations.
- Satellite imagery, maps or external weather APIs.
- Production-scale deployment.
- Unrestricted multi-user concurrent editing and conflict-resolution features.
- Collection of actual personally identifiable farmer data for experiments.

## 5. Functional requirements

IDs are stable references for implementation and testing.

### Agricultural records
- **FR-001:** The client shall allow a field officer to create farmer records.
- **FR-002:** The client shall allow a field officer to view farmer records.
- **FR-003:** The client shall allow a field officer to update farmer records.
- **FR-004:** The client shall allow a field officer to create, view and update farm records associated with farmers.
- **FR-005:** The client shall allow a field officer to create, view and update crop records associated with farms.
- **FR-006:** The client shall allow a field officer to create, view and update agricultural observation records.
- **FR-007:** The application shall validate record data before accepting a local or server-side write.

### Offline behavior
- **FR-008:** The client shall persist supported records locally using IndexedDB.
- **FR-009:** The client shall permit supported record operations while the server is unreachable.
- **FR-010:** The client shall retain pending local operations until they are acknowledged as successfully processed.
- **FR-011:** The client shall show synchronization state, including pending, in-progress, completed and failed work.
- **FR-012:** The client shall not require continuous internet connectivity for local record capture and viewing.

### Synchronization
- **FR-013:** The system shall implement full-record synchronization.
- **FR-014:** The system shall implement change-log synchronization.
- **FR-015:** Both synchronization methods shall use the same logical workload and equivalent correctness requirements.
- **FR-016:** The system shall assign a stable unique identifier to each logical operation.
- **FR-017:** The server shall prevent repeated delivery of an operation from causing unintended duplicate effects.
- **FR-018:** The system shall apply a documented retry policy to eligible failed or interrupted operations.
- **FR-019:** The system shall provide processing acknowledgements that allow the client to distinguish accepted work from verified completion.
- **FR-020:** The system shall support verification of the final server state against a deterministic expected state.
- **FR-021:** The system shall retain sufficient operation and synchronization metadata for experiment analysis.

### Research and measurement
- **FR-022:** The research tooling shall support deterministic synthetic datasets.
- **FR-023:** The research tooling shall support workload scenarios involving edits across many records and repeated edits to fewer records.
- **FR-024:** The research tooling shall associate each trial with a defined synchronization method, workload, network profile and configuration.
- **FR-025:** The research tooling shall record communication overhead, verified synchronization time, completion rate and data-consistency outcomes.
- **FR-026:** The research tooling shall distinguish request-body bytes from total captured network traffic.
- **FR-027:** The research tooling shall store raw trial results separately from derived analysis.
- **FR-028:** The research tooling shall export results in a documented machine-readable format.

## 6. Non-functional requirements

- **NFR-001 — Correctness:** A completed trial must be evaluated against its deterministic expected server state; HTTP success alone is not proof of correctness.
- **NFR-002 — Offline usability:** Local record capture and viewing must remain available without a live server connection.
- **NFR-003 — Reliability:** Interrupted synchronization must not silently lose pending operations.
- **NFR-004 — Idempotency:** Re-delivery of an operation must not create unintended duplicate effects.
- **NFR-005 — Reproducibility:** Trial configuration, workload seed or identity, network profile, software version and result files must be recorded.
- **NFR-006 — Fair comparison:** Synchronization methods must share equivalent logical operations, ordering, transport, retry policy and verification rules.
- **NFR-007 — Measurement integrity:** Request-body bytes and total captured traffic must be reported as separate measures.
- **NFR-008 — Data privacy:** Experimental datasets must be synthetic and must not contain real farmer personal data.
- **NFR-009 — Maintainability:** Backend and frontend responsibilities, API contracts and synchronization protocol must be documented.
- **NFR-010 — Testability:** Core logic must be testable independently, with automated backend, frontend and integration tests.
- **NFR-011 — Configuration:** Secrets and environment-specific configuration must not be committed to source control.
- **NFR-012 — Scope control:** New features outside the approved research scope require explicit review before implementation.

## 7. Evaluation definitions

### Communication overhead
Report application request-body bytes and total captured network traffic separately. Total captured traffic may include headers, acknowledgements, retransmissions and other transport overhead depending on the capture method.

### Verified synchronization time
Elapsed time from the defined synchronization start until:
1. intended operations have been processed;
2. the pending queue is empty;
3. the final server state matches the deterministic expected state; and
4. operation receipts or equivalent evidence show no unintended duplicate effects.

An HTTP 200 response alone does not satisfy this definition.

### Completion rate
Completed logical operations divided by the number of logical operations scheduled for the trial, reported with the exact denominator and completion criterion.

### Data consistency
Whether the final server state matches the deterministic expected state and whether operation processing produced unintended duplicates, omissions or incorrect values.

## 8. Constraints and assumptions

- The initial development environment is Linux Mint on the researcher's laptop.
- Backend technology: Python and FastAPI.
- Central database: PostgreSQL.
- Frontend: React and TypeScript.
- Client local storage: IndexedDB.
- Network emulation: Linux network namespaces and `tc/netem`.
- Synthetic data is used for experimental trials.
- Network profiles are experimental settings, not claims about measured real-world Zambian connectivity.
- Practical profile values are established during pilot testing and frozen before main trials.
- The current repository has folders prepared, but implementation code has not yet been created.

## 9. Acceptance baseline

A requirement is considered implemented only when its behavior is demonstrated by a relevant test or recorded verification. Folder presence, generated code or a successful application startup alone is not evidence that all requirements are satisfied.

## 10. Change control

Any proposed change to objectives, research questions, synchronization semantics, experimental metrics or scope must be raised for review before implementation. Requirements may be clarified, but approved research objectives must not be silently altered.
