# OfflineLink Zambia — Software Testing and Experimental Validation Plan

**Project:** OfflineLink Zambia  
**Research title:** Design and Experimental Evaluation of an Offline-First Agricultural Data Collection Application for Unstable and Low-Bandwidth Network Conditions in Zambia  
**Document type:** Software Testing and Experimental Validation Plan  
**Status:** Testing strategy approved; test execution pending implementation  
**Version:** 0.2  
**Date:** 11 November 2026

---

## 1. Introduction

### 1.1 Purpose

This document defines the testing and experimental validation strategy for OfflineLink Zambia.

It establishes how the planned application, synchronization mechanisms and research environment will be tested to determine whether they satisfy the approved system requirements and research objectives.

The plan covers:

- Backend unit and integration testing.
- Frontend unit and component testing.
- Browser and end-to-end testing.
- Offline operation and local persistence testing.
- Synchronization correctness testing.
- Duplicate-prevention and retry testing.
- Experimental workload and network configuration validation.
- Measurement integrity and final-state verification.
- Acceptance gates and evidence management.

The purpose of testing is to demonstrate actual system behavior. The existence of source files, folders or a successful application startup is not sufficient evidence that a requirement has been satisfied.

### 1.2 Testing Objectives

The testing process must establish that:

1. Agricultural records can be created, viewed and updated according to the approved requirements.
2. Supported local operations remain available without a live server connection.
3. Local records and pending operations survive application reloads and connectivity interruptions.
4. Both synchronization mechanisms process equivalent logical workloads.
5. Repeated operation delivery does not produce unintended duplicate effects.
6. Retry and recovery behavior preserves pending work.
7. Processing acknowledgements and receipts correctly represent server-side processing.
8. Final server state matches the deterministic expected state.
9. Experimental measurements are collected consistently and accurately.
10. Experimental configurations and results can be reproduced and independently examined.

### 1.3 Testing Principles

Testing must follow these principles:

- Requirements-driven verification.
- Reproducible test configurations.
- Synthetic test data.
- Shared synchronization correctness criteria.
- Equivalent experimental conditions.
- Retention of raw evidence.
- Explicit handling of failed and interrupted tests.
- Traceability between requirements, tests and results.

---

## 2. Testing Scope

### 2.1 Included Testing

The testing scope includes:

- Agricultural record validation.
- Entity relationships.
- Local IndexedDB persistence.
- Offline record operations.
- Pending synchronization queue behavior.
- Full-record synchronization.
- Change-log synchronization.
- Stable operation identity.
- Duplicate prevention.
- Retry and recovery behavior.
- Processing acknowledgements and receipts.
- Final-state verification.
- Experimental workload generation.
- Network profile configuration.
- Request-body measurement.
- Total network traffic measurement.
- Verified synchronization time.
- Completion rate.
- Data consistency.
- Trial metadata and result export.
- Reproducibility.

### 2.2 Excluded Testing

Testing must remain within the approved research scope.

The initial plan does not include:

- Marketplace or payment functionality.
- AI crop diagnosis or recommendations.
- External weather or mapping integrations.
- Production-scale deployment testing.
- Unrestricted multi-user concurrent editing.
- Real farmer personal data testing.

Any proposed expansion requires review against the approved research scope.

---

## 3. Testing Levels

The project will use five principal testing levels.

### 3.1 Backend Unit Testing

Backend unit tests will validate individual backend components independently.

Areas include:

- Request and response schema validation.
- Agricultural data validation.
- Application service behavior.
- Repository behavior where practical.
- Operation identity handling.
- Synchronization strategy behavior.
- Duplicate-prevention logic.
- Retry classification.
- Processing receipt handling.
- Metric calculations.
- Expected-state verification logic.

**Planned testing tool:** pytest.

### 3.2 Backend Integration Testing

Backend integration tests will verify interactions between backend components and the central database.

Areas include:

- API endpoint behavior.
- Database connectivity.
- Database migrations.
- Agricultural record persistence.
- Relationship enforcement.
- Transactional processing.
- Repeated operation delivery.
- Idempotency behavior.
- Processing receipts.
- Final-state verification.

Integration tests must use an appropriate test database and controlled test data.

### 3.3 Frontend Unit and Component Testing

Frontend tests will validate individual interface components and client-side application behavior.

Areas include:

- Form validation.
- Agricultural record feature behavior.
- Local operation creation.
- IndexedDB data access.
- Queue state transitions.
- API payload construction.
- Synchronization status presentation.
- Receipt handling.
- Local persistence behavior.

### 3.4 Browser and End-to-End Testing

Browser tests will verify complete user workflows across the frontend and backend.

Areas include:

- Agricultural record creation.
- Agricultural record editing.
- Offline record capture.
- Local persistence after application reload.
- Connectivity restoration.
- Synchronization initiation.
- Server processing.
- Visible synchronization status.
- Correct final record state.

The final browser testing framework will be confirmed during the implementation foundation stage.

### 3.5 Experimental Verification

Experimental verification will establish that the research environment produces valid, reproducible measurements.

Areas include:

- Deterministic dataset generation.
- Workload generation.
- Expected-state generation.
- Network profile application.
- Trial configuration.
- Synchronization method selection.
- Traffic capture.
- Application-level measurement.
- Completion and consistency verification.
- Raw result persistence.
- Result export.
- Repeatability.

---

## 4. Agricultural Data and Validation Tests

The system must support four agricultural entities:

- Farmer.
- Farm.
- Crop.
- Observation.

The following conditions must be tested.

| Test ID | Test condition | Expected outcome |
|---|---|---|
| DATA-01 | Create a valid farmer record | Record is accepted and persisted |
| DATA-02 | Create a valid farm associated with a farmer | Record and relationship are accepted |
| DATA-03 | Create a valid crop associated with a farm | Record and relationship are accepted |
| DATA-04 | Create a valid observation associated with a crop | Record and relationship are accepted |
| DATA-05 | Submit a record with a missing required field | Invalid record is rejected |
| DATA-06 | Submit an invalid relationship reference | Invalid relationship is rejected |
| DATA-07 | Submit invalid date values | Invalid data is rejected according to the approved validation rules |
| DATA-08 | Submit a duplicate entity identifier | Duplicate handling follows the approved data contract |
| DATA-09 | Compare local and server validation | Validation behavior is consistent |
| DATA-10 | Verify synthetic test fixtures | Research tests use synthetic records only |

The final field-level validation conditions must follow the approved data dictionary and API schemas.

---

## 5. Offline Operation Tests

Offline behavior is a fundamental system requirement.

The client must remain usable for supported local record operations when the central API is unavailable.

### 5.1 Offline Test Procedure

The intended test sequence is:

1. Start the application with the backend unavailable.
2. Open the field application.
3. Create and update supported agricultural records.
4. Verify local persistence.
5. Reload the application.
6. Verify that records remain available.
7. Verify that pending synchronization operations remain stored.
8. Restore backend connectivity.
9. Verify that pending operations become eligible for synchronization.
10. Verify that the local operation state reflects valid server processing evidence.

### 5.2 Offline Test Cases

| Test ID | Test condition | Expected outcome |
|---|---|---|
| OFF-01 | API unavailable before record creation | Local record creation remains available |
| OFF-02 | Create and update records while offline | Changes are persisted locally |
| OFF-03 | Reload the client while offline | Previously stored records remain available |
| OFF-04 | Reload with pending operations | Pending operations remain stored |
| OFF-05 | Restore connectivity | Eligible pending operations can be synchronized |
| OFF-06 | Receive valid processing receipts | Local operation state reflects server processing |
| OFF-07 | Interrupt synchronization | Unprocessed operations are retained |
| OFF-08 | Attempt synchronization while server is unavailable | Client handles the failure without silently discarding pending work |

---

## 6. Synchronization Correctness Tests

Both synchronization methods must be tested against equivalent logical scenarios.

The tests must establish that the selected synchronization representation does not compromise operation identity, ordering, reliability or final-state correctness.

### 6.1 Core Synchronization Test Cases

| Test ID | Scenario | Expected outcome |
|---|---|---|
| SYNC-01 | Normal synchronization | All valid logical operations are processed and verified |
| SYNC-02 | Repeated operation ID | Duplicate delivery does not create a second logical effect |
| SYNC-03 | Interrupted connection | Pending work is retained and can recover |
| SYNC-04 | Response lost after server processing | Retry uses the same operation ID and does not duplicate effects |
| SYNC-05 | Repeated edits to one record | Required logical ordering is preserved |
| SYNC-06 | Operations distributed across many records | All valid operations are processed and the final state is correct |
| SYNC-07 | Receipt and queue verification | Client status agrees with server processing evidence |
| SYNC-08 | Invalid operation | Invalid data is rejected with structured error information |
| SYNC-09 | Fair method comparison | Both methods receive equivalent workloads and trial conditions |

### 6.2 Detailed Correctness Expectations

**Operation identity**

A logical operation must retain the same operation ID throughout its transmission attempts.

**Duplicate prevention**

Repeated delivery of an already processed operation must not produce unintended duplicate effects.

**Ordering**

Where operations affect the same record, the required logical sequence must be preserved.

**Acknowledgements**

The client must not mark an operation processed based only on an attempted transmission or HTTP success.

**Final-state verification**

The actual central server state must be compared against the deterministic expected state.

**Pending queue**

An operation must remain pending until the applicable processing evidence has been received and the client has updated its state accordingly.

### 6.3 Method Coverage

All applicable synchronization correctness tests must be executed against both:

- Full-record synchronization.
- Change-log synchronization.

Any differences in test execution must be justified and documented.

---

## 7. Experimental Fairness Tests

The experimental comparison must isolate the intended difference between synchronization representations.

The following conditions must be validated before paired comparisons are accepted.

| Test ID | Fairness condition | Expected outcome |
|---|---|---|
| FAIR-01 | Same initial dataset | Both methods start from equivalent data |
| FAIR-02 | Same logical workload | Both methods receive equivalent operations |
| FAIR-03 | Same operation ordering | Required logical ordering is preserved |
| FAIR-04 | Same network profile | Equivalent network settings are applied |
| FAIR-05 | Same transport | Both methods use the same transport conditions |
| FAIR-06 | Same retry configuration | Both methods use the same retry policy |
| FAIR-07 | Same verification criteria | Both methods use identical completion and correctness rules |
| FAIR-08 | Recorded trial configuration | Each trial retains its relevant configuration |
| FAIR-09 | Method-specific representation | The intended synchronization representation remains the principal planned difference |

The fairness checks must be performed before interpreting experimental performance results.

Any environmental deviation must be recorded and assessed.

---

## 8. Measurement Validation Tests

Measurement tests must establish that experimental metrics are collected and calculated correctly.

### 8.1 Request-Body Measurement

The research tooling must measure serialized application request-body bytes.

Tests must verify that the reported value corresponds to the serialized payloads transmitted during the trial.

### 8.2 Total Captured Network Traffic

Total captured network traffic must be measured independently from request-body bytes.

The capture method and boundaries must be documented.

Depending on the capture method, the measurement may include headers, acknowledgements, retransmissions and other traffic.

### 8.3 Request and Attempt Counts

The research tooling must distinguish:

- Scheduled logical operations.
- Synchronization requests.
- Transmission attempts.
- Retries.

Repeated attempts must not inflate the logical operation count.

### 8.4 Verified Synchronization Time

The timing procedure must use clearly defined start and end events.

The end event must correspond to verified completion, not merely the receipt of an HTTP response.

### 8.5 Completion Rate

The completion-rate denominator must equal the number of logical operations scheduled for the trial.

Only operations satisfying the approved completion criterion may be counted as completed.

### 8.6 Consistency Verification

The consistency checker must detect:

- Missing operations.
- Unexpected operations or effects.
- Duplicate effects.
- Incorrect record values.
- Incorrect final relationships.

### 8.7 Measurement Test Cases

| Test ID | Test condition | Expected outcome |
|---|---|---|
| MET-01 | Calculate request-body bytes | Matches serialized application payload bytes |
| MET-02 | Capture total network traffic | Independent traffic measurement is retained |
| MET-03 | Count transmission attempts | All attempts and retries are counted consistently |
| MET-04 | Calculate verified synchronization time | Timing follows defined completion events |
| MET-05 | Calculate completion rate | Denominator equals scheduled logical operations |
| MET-06 | Introduce missing operations | Consistency checker detects missing operations |
| MET-07 | Introduce unexpected or duplicate effects | Consistency checker detects deviations |
| MET-08 | Recompute metrics from raw data | Derived metrics can be independently reproduced |

---

## 9. Research Infrastructure Tests

The research environment must be tested independently of the main experimental evaluation.

### 9.1 Workload Generation

The workload generator must produce deterministic results for a fixed seed and configuration.

### 9.2 Network Profiles

Network profile configuration must be recorded and applied consistently.

### 9.3 Trial Metadata

Every trial must have a unique identifier and sufficient configuration metadata.

### 9.4 Result Storage

Raw trial results must be retained separately from derived analysis.

Failed and interrupted trials must be preserved and clearly labelled.

### 9.5 Export Validation

Exported result files must use a documented machine-readable format and be parseable by the intended analysis tooling.

### 9.6 Infrastructure Test Cases

| Test ID | Test condition | Expected outcome |
|---|---|---|
| EXP-01 | Generate workload using fixed seed/configuration | Equivalent deterministic workload is produced |
| EXP-02 | Apply a network profile | Configured profile is applied and recorded |
| EXP-03 | Create experimental trial | Unique trial ID is assigned |
| EXP-04 | Record trial metadata | Required metadata is retained |
| EXP-05 | Interrupt a trial | Trial is retained and labelled appropriately |
| EXP-06 | Store raw and derived results | The two result categories remain separate |
| EXP-07 | Export experimental results | Exported data can be parsed by analysis tooling |
| EXP-08 | Repeat a configured trial | The configuration and workload can be reproduced |
| EXP-09 | Freeze main-trial parameters | Approved parameters are retained for main evaluation |

---

## 10. Requirements Traceability

Tests must be traceable to the functional and non-functional requirements defined in `requirements.md`.

The following matrix establishes the initial traceability structure.

### 10.1 Functional Requirement Coverage

| Requirement group | Requirement IDs | Principal test coverage |
|---|---|---|
| Agricultural records | FR-001 to FR-007 | DATA-01 to DATA-10 |
| Offline operation | FR-008 to FR-012 | OFF-01 to OFF-08 |
| Synchronization | FR-013 to FR-021 | SYNC-01 to SYNC-09 |
| Research and measurement | FR-022 to FR-028 | FAIR-01 to FAIR-09, MET-01 to MET-08, EXP-01 to EXP-09 |

### 10.2 Non-Functional Requirement Coverage

| Requirement | Verification approach |
|---|---|
| NFR-001 — Correctness | Final-state comparison and synchronization correctness tests |
| NFR-002 — Offline usability | Offline operation and browser tests |
| NFR-003 — Reliability | Interruption, recovery and pending-operation retention tests |
| NFR-004 — Idempotency | Repeated operation delivery tests |
| NFR-005 — Reproducibility | Workload, configuration, metadata and repeated-trial verification |
| NFR-006 — Experimental fairness | Paired workload and configuration checks |
| NFR-007 — Measurement integrity | Independent payload and traffic measurement tests |
| NFR-008 — Data privacy | Synthetic dataset inspection and test-data controls |
| NFR-009 — Maintainability | Architecture, API and synchronization documentation review |
| NFR-010 — Testability | Automated test execution and independent component testing |
| NFR-011 — Configuration security | Repository and configuration inspection |
| NFR-012 — Scope control | Requirements and decision-record review |

The traceability matrix must be updated when requirements or test procedures change.

---

## 11. Acceptance Gates

Development and validation will proceed through defined acceptance gates.

A gate must not be considered passed solely because the corresponding application starts successfully.

### Gate A — Foundation

Expected verification:

- Backend and frontend development environments are configured.
- Application configuration works.
- Health check works.
- Test tooling runs.
- Database connection is verified.
- Database migration workflow is verified.

**Acceptance condition:** The development foundation can be configured and verified consistently.

### Gate B — Data Management

Expected verification:

- Agricultural CRUD behavior works.
- Entity relationships are valid.
- Data validation is enforced.
- Persistence tests pass.
- Invalid data is handled correctly.

**Acceptance condition:** Supported agricultural data operations satisfy the approved data requirements.

### Gate C — Offline Client

Expected verification:

- Local record operations work without server access.
- IndexedDB persistence works.
- Records survive application reload.
- Pending queue behavior is correct.
- Connectivity restoration allows pending work to be synchronized.

**Acceptance condition:** Supported offline operations remain available and pending work is retained reliably.

### Gate D — Synchronization

Expected verification:

- Both synchronization methods are implemented.
- Shared correctness scenarios pass.
- Duplicate delivery tests pass.
- Retry and interruption behavior is validated.
- Processing receipts are handled correctly.
- Final-state verification succeeds.

**Acceptance condition:** Both methods satisfy the shared synchronization correctness requirements.

### Gate E — Research Tooling

Expected verification:

- Workloads can be generated and reproduced.
- Network profiles can be configured and verified.
- Trial metadata is recorded.
- Raw metrics are captured.
- Expected-state verification works.
- Result export is validated.

**Acceptance condition:** The research environment can execute and record reproducible experimental trials.

### Gate F — Main Experiments

Expected verification:

- Pilot results have been reviewed.
- Main-trial parameters have been frozen.
- The experimental procedure is documented.
- Trial configurations are reproducible.
- Main trials can be executed and analysed.
- Raw evidence is retained.

**Acceptance condition:** The main experimental evaluation can be conducted using the approved procedure and measurement definitions.

---

## 12. Defect Management

Defects identified during development or testing must be documented and tracked.

Each defect record must include:

- Defect ID.
- Affected component or layer.
- Reproduction steps.
- Expected behavior.
- Actual behavior.
- Severity.
- Relevant evidence or logs.
- Resolution or fix.
- Regression test.

### 12.1 Defect Handling Principles

- Defects must be reproducible where possible.
- Correctness-related defects must receive appropriate priority.
- Fixes must include relevant regression tests.
- Failed tests must not be silently ignored.
- Defects affecting research validity must be resolved or explicitly assessed before the affected evaluation proceeds.

A gate must not be passed while an unresolved defect undermines the correctness or research validity required by that gate.

---

## 13. Test Evidence and Reporting

Testing must produce evidence that can be associated with the software revision and configuration being evaluated.

### 13.1 Required Evidence

Retain the following where applicable:

- Test command.
- Test environment.
- Software revision.
- Test configuration.
- Pass/fail output.
- Test data or fixture identity.
- Relevant application logs.
- Database or verification evidence.
- Screenshots where visual evidence adds value.
- Experimental raw files.
- Network profile configuration.
- Measurement output.
- Defect records.

### 13.2 Evidence Traceability

Evidence must be traceable to:

- The requirement or test case.
- The code revision.
- The test configuration.
- The execution date.
- The result.

Experimental evidence must retain enough metadata to allow later analysis and verification.

### 13.3 Failed and Interrupted Tests

Failed, incomplete and interrupted tests must be retained and clearly identified.

They must not be silently removed from the testing record.

---

## 14. Test Execution Management

Testing must be performed incrementally as the application and research environment are developed.

The planned sequence is:

1. Establish the development and testing foundation.
2. Validate agricultural data models and persistence.
3. Validate offline client behavior.
4. Validate synchronization operation identity and processing.
5. Validate both synchronization strategies.
6. Validate experimental workload and network tooling.
7. Conduct pilot trials.
8. Freeze main-trial parameters.
9. Execute main experimental trials.
10. Analyse and report the results.

Tests must be executed against the relevant implementation and documented acceptance criteria.

A planned test is not considered passed until its execution evidence has been reviewed.

---

## 15. Test Plan Change Control

Any change to the testing strategy must be reviewed against the approved requirements and research objectives.

A material change must:

1. Identify the affected requirement or test.
2. Explain the reason for the change.
3. Assess its effect on correctness and experimental validity.
4. Update the relevant test procedures.
5. Update the traceability matrix.
6. Record the change where appropriate.

Changes to synchronization correctness tests or experimental measurement procedures must also be reviewed against `sync-protocol.md`.

---

## 16. Document Status

This document establishes the planned testing and experimental validation approach for OfflineLink Zambia.

The test levels, principal scenarios, measurement checks, traceability structure, acceptance gates and evidence requirements are defined as the testing baseline.

Test execution has not yet commenced because application source code and research tooling have not yet been created.

The acceptance gates must be supported by actual verification evidence before being marked as passed.

**Implementation status:** Not implemented.  
**Test execution status:** Pending implementation.

**Related documents:**

- `requirements.md`
- `architecture.md`
- `api-contract.md`
- `sync-protocol.md`
- `README-stage0.md`

---

**OfflineLink Zambia — Software Testing and Experimental Validation Plan**

*Verification must demonstrate correctness, reliability, reproducibility and experimental integrity.*