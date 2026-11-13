# OfflineLink Zambia — Synchronization Protocol Specification

**Project:** OfflineLink Zambia  
**Research title:** Design and Experimental Evaluation of an Offline-First Agricultural Data Collection Application for Unstable and Low-Bandwidth Network Conditions in Zambia  
**Document type:** Synchronization Protocol Specification  
**Status:** Protocol baseline approved; implementation-critical parameters pending finalization  
**Version:** 0.2  
**Date:** 11 November 2026

---

## 1. Introduction

### 1.1 Purpose

This document defines the synchronization protocol for OfflineLink Zambia.

It establishes the behavior, rules and correctness requirements governing the transfer of locally stored agricultural data from the offline-first client to the central server.

The protocol supports two synchronization mechanisms:

1. Full-record synchronization.
2. Change-log synchronization.

Both mechanisms will be implemented and experimentally evaluated under controlled emulated low-bandwidth and intermittent network conditions.

The protocol provides a shared reference for frontend synchronization logic, backend processing, integration testing and experimental measurement.

### 1.2 Research Significance

Synchronization is a central component of this research.

The project aims to determine how full-record and change-log synchronization compare when transferring equivalent logical workloads under constrained network conditions.

The comparison must distinguish differences caused by the synchronization representation from differences caused by workload, retry behavior, transport conditions or verification procedures.

Consequently, both mechanisms must follow equivalent reliability and correctness requirements.

### 1.3 Scope

This protocol covers:

- Logical operation identity.
- Full-record synchronization.
- Change-log synchronization.
- Operation ordering.
- Duplicate prevention.
- Retry handling.
- Processing acknowledgements and receipts.
- Interruption and recovery behavior.
- Final-state verification.
- Experimental measurement.
- Trial metadata and reproducibility.
- Experimental fairness.

---

## 2. Protocol Design Principles

Both synchronization methods must operate under the following shared principles.

### 2.1 Equivalent Logical Workloads

Both methods must process the same logical workload.

A logical operation must represent the same intended user-level record creation or edit regardless of the selected synchronization representation.

### 2.2 Stable Operation Identity

Every logical operation must have a unique, stable identifier.

The identifier must remain unchanged across transmission attempts and retries.

### 2.3 Equivalent Ordering

Both methods must preserve the same logical operation ordering wherever ordering is required, particularly when multiple edits affect the same record.

### 2.4 Shared Reliability Rules

Both methods must use the same:

- Transport.
- Retry policy.
- Duplicate-prevention requirements.
- Processing acknowledgement requirements.
- Final-state verification criteria.

### 2.5 Measurement Consistency

Both methods must be measured using the same procedures and definitions.

Request-body bytes and total captured network traffic must be reported separately.

### 2.6 Research Integrity

The synchronization methods must differ in their intended data representation, not through accidental differences in reliability behavior or experimental configuration.

---

## 3. Protocol Terminology

The following terms have specific meanings within this project.

| Term | Definition |
|---|---|
| Logical operation | One intended user-level record creation or edit in the workload |
| Operation ID | Stable unique identifier assigned to a logical operation |
| Pending operation | Locally stored operation not yet acknowledged as processed |
| Transmission attempt | One attempt to send an operation or batch to the server |
| Retry | A subsequent transmission attempt using the original operation identity |
| Receipt | Server evidence of operation processing or duplicate recognition |
| Expected state | Deterministic final server state calculated from the initial dataset and logical workload |
| Duplicate delivery | Repeated transmission of an operation that has already been submitted |
| Idempotency | Processing behavior that prevents repeated delivery of the same logical operation from producing unintended duplicate effects |
| Verified completion | A trial outcome satisfying all defined processing, queue, receipt and final-state verification conditions |

### 3.1 Logical Operation Versus Transmission Attempt

A logical operation and its transmission attempts must be tracked separately.

For example, one logical operation may be transmitted three times because the first two attempts encounter connectivity failures.

This must still count as one scheduled logical operation, not three separate operations.

The same operation ID must be used for all three attempts.

---

## 4. Operation Identity and Structure

### 4.1 Entity Identifiers

Supported agricultural entities use client-generated UUIDv4 identifiers.

The initial entity identifiers are:

- `farmer_id`
- `farm_id`
- `crop_id`
- `observation_id`

These identifiers must remain consistent between local persistence and central synchronization.

### 4.2 Operation Identifiers

Every logical operation must receive a client-generated UUIDv4 identifier.

The operation ID must:

1. Be generated once when the logical operation is created.
2. Be persisted locally.
3. Remain unchanged during retries.
4. Be included in synchronization submissions.
5. Be recognized by the server.
6. Be associated with processing evidence or a receipt.

A retry must never generate a replacement operation ID.

### 4.3 Operation Metadata

Each logical operation must retain sufficient information to support synchronization and experimental analysis.

The required information includes:

- Operation ID.
- Operation type.
- Entity type.
- Entity ID.
- Relevant operation payload.
- Client creation time or sequence metadata where required.
- Protocol version.
- Processing status.
- Relevant transmission attempt information.

The exact operation schema remains subject to the final protocol contract.

---

## 5. Full-Record Synchronization

### 5.1 Definition

Full-record synchronization transmits the complete current record for each queued logical edit according to the approved workload semantics.

The strategy represents each logical edit through the full-record representation rather than transmitting only the individual field changes.

### 5.2 Processing Requirements

The full-record strategy must:

- Transmit the complete record representation required by the protocol.
- Preserve the logical operation ID.
- Preserve the defined logical ordering.
- Record logical operations separately from transmission attempts.
- Apply server-side duplicate-prevention rules.
- Provide processing acknowledgement or receipt information.
- Record payload size and attempt outcomes.
- Support final-state verification.

### 5.3 Repeated Edits

The strategy must not silently collapse multiple logical edits unless the workload specification explicitly defines that behavior.

The treatment of repeated edits to the same entity must be consistent with the logical workload and expected-state calculation.

This is particularly important when evaluating the communication overhead associated with repeated changes to fewer records.

---

## 6. Change-Log Synchronization

### 6.1 Definition

Change-log synchronization transmits individual change entries representing logical record edits.

Each entry represents an intended change and must be associated with its corresponding logical operation identity.

### 6.2 Processing Requirements

The change-log strategy must:

- Represent individual logical edits as change entries.
- Assign a stable operation ID to each logical change.
- Preserve the defined operation ordering.
- Associate changes with the correct entity and entity identifier.
- Apply changes according to the approved operation semantics.
- Prevent repeated delivery from applying the same logical operation twice.
- Provide processing acknowledgement or receipt information.
- Record payload size and attempt outcomes.
- Support final-state verification.

### 6.3 Repeated Edits

Multiple changes to the same record must remain distinguishable as separate logical operations where the workload defines them as separate edits.

The change-log strategy must preserve the ordering required to produce the deterministic expected final state.

---

## 7. Operation Processing Lifecycle

The protocol must distinguish between local creation, transmission, server processing and verified completion.

### 7.1 Intended Lifecycle

The logical lifecycle is:

1. **Created:** The user performs a supported record operation.
2. **Persisted locally:** The record and corresponding logical operation are stored locally.
3. **Pending:** The operation awaits synchronization.
4. **In progress:** The operation is included in a transmission attempt.
5. **Processed:** The server has successfully applied or recognized the operation.
6. **Acknowledged:** The client receives valid processing evidence.
7. **Verified:** The operation and trial outcome satisfy the applicable correctness checks.

An operation may also encounter a rejection or failure condition.

The exact status values and transition rules must be defined in the final implementation contract.

### 7.2 Local Persistence

The client must persist the agricultural record and its corresponding synchronization operation.

The operation must remain available after:

- Application reload.
- Temporary loss of connectivity.
- Interrupted synchronization.
- Server unavailability.

Pending work must not be silently discarded.

### 7.3 Server Processing

The server must:

1. Receive the submitted operation.
2. Validate its structure and contents.
3. Evaluate its operation identity.
4. Determine whether it is a new or previously processed operation.
5. Apply the appropriate processing behavior.
6. Persist the resulting effect and processing metadata.
7. Provide the appropriate acknowledgement or receipt.

Operation processing and the corresponding data mutation should be atomic where supported by the database design.

---

## 8. Duplicate Prevention and Idempotency

### 8.1 Purpose

Intermittent connectivity creates situations where the server may process an operation but the client does not receive the corresponding response.

The client may consequently submit the same operation again.

The protocol must prevent this repeated delivery from producing unintended duplicate effects.

### 8.2 Required Behavior

The client must reuse the original operation ID when retrying.

The server must:

- Recognize previously processed operation IDs.
- Prevent repeated processing from producing unintended duplicate effects.
- Provide appropriate evidence when a previously processed operation is submitted again.
- Preserve consistency between operation processing metadata and the resulting record state.

### 8.3 Atomic Processing

Operation identity and its resulting data mutation should be persisted transactionally where supported.

This reduces the possibility of recording an operation as processed without persisting its corresponding effect, or persisting an effect without recording its processing identity.

### 8.4 Outstanding Idempotency Decisions

The following details must be finalized:

- Operation identity scope.
- Database uniqueness constraints.
- Idempotency retention period.
- Behavior when an existing operation ID is submitted with a different payload.
- Response behavior for repeated operation delivery.
- Handling of expired or unavailable operation-processing records.

These details must be agreed before synchronization implementation begins.

---

## 9. Retry Policy

### 9.1 General Principle

Both synchronization methods must use the same retry policy.

The policy must distinguish between failures that may be retried and failures that require rejection or other handling.

### 9.2 Retryable Failure Classes

The final implementation must define the retry behavior for conditions such as:

- Server unavailability.
- Connection interruption.
- Request timeout.
- Connection loss during transmission.
- Lost response after server processing.
- Temporary network failure.

### 9.3 Non-Retryable Failure Classes

Validation failures and other permanent rejection conditions must not be treated as ordinary transient connectivity failures.

The client must receive sufficient information to distinguish these conditions.

### 9.4 Retry Configuration

The retry policy must define:

- Eligible failure classes.
- Non-retryable failure classes.
- Maximum transmission attempts or trial-level stopping rule.
- Retry delay and backoff policy.
- Behavior after timeouts.
- Handling of ambiguous outcomes.
- Conditions for marking an operation failed.
- Conditions for retaining an operation as pending.

### 9.5 Experimental Consistency

Retry configuration must be:

- Identical for both synchronization methods.
- Configurable during testing and pilot experiments.
- Fixed and recorded during main experimental trials.

Retry parameters must not be silently changed between methods.

The actual retry limits and backoff values remain to be approved.

---

## 10. Acknowledgements and Processing Receipts

### 10.1 Acknowledgement Principle

An HTTP response indicates that the server returned an API response. It does not independently establish that the intended logical operation has been correctly processed.

The client must rely on valid processing evidence defined by the protocol.

### 10.2 Receipt Requirements

The server must provide sufficient information to establish:

- The operation ID associated with the result.
- Whether the operation was processed.
- Whether the operation was recognized as a repeated submission.
- Whether the operation was rejected.
- The reason for rejection where applicable.
- Evidence that the processing result has been durably recorded.

### 10.3 Client Handling

The client must only treat an operation as processed after receiving valid processing evidence or equivalent contract-defined confirmation.

A missing or ambiguous response must not automatically cause the client to discard the operation.

### 10.4 Receipt Schema

The exact receipt structure, status values, response codes and retrieval behavior must be finalized before implementation.

---

## 11. Operation Ordering and Workload Semantics

### 11.1 Logical Ordering

Every workload must define the logical operation sequence.

Both synchronization methods must receive equivalent operation sequences.

### 11.2 Repeated Record Edits

Where multiple logical operations affect the same entity, the required ordering must be preserved.

The ordering mechanism must be defined consistently for both methods.

### 11.3 Deterministic Expected State

The research environment must generate the expected final state from:

- The initial synthetic dataset.
- The logical operation sequence.
- The approved operation semantics.

Expected-state generation must be independent of the synchronization result being evaluated.

This allows the actual server state to be compared against a predefined expected outcome.

### 11.4 Workload Scenarios

The protocol must support the two approved workload patterns:

**Scenario A — Many Records**

Operations are distributed across a larger number of agricultural records.

**Scenario B — Fewer Records with Repeated Edits**

Multiple logical operations affect a smaller number of records.

Both scenarios must be supplied to both synchronization methods using equivalent logical operations.

### 11.5 Workload Reproducibility

Each workload must retain its identifier, parameters and seed or exact generated operation sequence.

The information must be associated with the relevant trial metadata.

---

## 12. Failure and Interruption Handling

The protocol must support reliable recovery from network and processing failures.

The following scenarios must be tested.

| Scenario | Expected protocol behavior |
|---|---|
| Server unavailable before transmission | Local operations remain pending |
| Connection interrupted during transmission | Client retains the operation and applies the agreed retry policy |
| Response lost after server processing | Client retries using the same operation ID |
| Retry after ambiguous outcome | Server recognizes the existing operation and prevents duplicate effects |
| Duplicate operation delivery | Repeated delivery does not produce an unintended second effect |
| Partial batch acceptance | Accepted and rejected operations are distinguishable, if batching is supported |
| Invalid operation | Server rejects invalid data with structured error information |
| Network recovery after offline edits | Pending operations become eligible for synchronization |
| Application reload during pending work | Persisted records and pending operations remain available |

### 12.1 No Silent Data Loss

An operation must not disappear from the local queue merely because:

- A transmission was attempted.
- A connection was interrupted.
- A timeout occurred.
- The client did not receive a response.

The final operation status must be based on the protocol's processing and acknowledgement rules.

---

## 13. Batch Processing

Batching may be used to submit multiple logical operations in one request.

However, its exact behavior must be finalized before implementation.

The design must establish:

- Whether both methods use batching.
- Whether batch sizes are identical or method-specific.
- How operations are identified within a batch.
- How partial acceptance is represented.
- How failed operations are retried.
- How duplicate operations within a batch are handled.
- How operation receipts are returned.
- How request and attempt counts are measured.

Any batch configuration used during a main trial must be fixed and recorded.

Batch behavior must not introduce an unfair advantage or reliability difference between synchronization methods.

---

## 14. Synchronization Completion and Verification

### 14.1 Verified Completion Definition

A trial is considered verified complete only when all the following conditions are satisfied:

1. All scheduled logical operations have been processed.
2. No pending logical operations remain.
3. The final central server state matches the deterministic expected state.
4. Processing receipts are consistent with the submitted workload.
5. No unintended duplicate effects are detected.

### 14.2 Final-State Verification

The verification process must compare the actual server state with the expected state generated before the trial.

The comparison must identify:

- Missing records or operations.
- Unexpected records or effects.
- Duplicate effects.
- Incorrect field values.
- Incorrect record relationships.
- Other deviations from the expected state.

### 14.3 HTTP Response Limitation

A successful HTTP status code must not independently establish trial completion.

Transport success, operation processing and verified trial completion must remain distinct concepts.

---

## 15. Experimental Metrics

Both synchronization methods must be evaluated using the same metric definitions.

### 15.1 Communication Overhead

Communication overhead must distinguish between two measurements.

**A. Request-body bytes**

The total serialized application request-body bytes transmitted during the trial.

**B. Total captured network traffic**

The total traffic measured through the defined network capture procedure.

Depending on the capture boundary, this may include:

- Application payloads.
- HTTP headers.
- Acknowledgements.
- Retransmissions.
- Other transport-level traffic.

The two measurements must be reported separately.

### 15.2 Request and Attempt Counts

The research environment must record:

- Number of logical operations scheduled.
- Number of synchronization requests.
- Number of transmission attempts.
- Number of retries.

A logical operation must not be counted as a new operation merely because it is transmitted again.

### 15.3 Verified Synchronization Time

Verified synchronization time is the elapsed time between the defined synchronization start and the moment all verified completion conditions are satisfied.

The start event, end event and timing procedure must be consistent across both methods.

### 15.4 Completion Rate

Completion rate is calculated as:

**Completion rate = Verified completed logical operations / Scheduled logical operations**

The denominator must be recorded for each trial.

The exact completion criterion and any trial stopping rules must also be recorded.

### 15.5 Data Consistency

Data consistency is evaluated by comparing the final central server state with the deterministic expected state.

The result must identify missing, unexpected, duplicated or incorrect effects.

### 15.6 Measurement Integrity

Measurement procedures must be documented and applied consistently.

Request-body bytes must not be presented as total network traffic.

Raw measurement evidence must be retained to allow derived results to be checked.

---

## 16. Trial Metadata

Each experimental trial must retain sufficient information for reproducibility and later analysis.

At minimum, record:

| Metadata | Description |
|---|---|
| Trial ID | Unique trial identifier |
| Timestamp | Trial creation or execution timestamp |
| Synchronization method | Full-record or change-log |
| Workload ID | Workload identifier |
| Workload parameters | Scenario and workload configuration |
| Operation count | Exact scheduled logical operation count |
| Network profile | Profile identifier and configuration |
| Software revision | Code revision used |
| Protocol version | Synchronization protocol version |
| Retry configuration | Retry classes, limits and backoff settings |
| Batch configuration | Batch settings, if applicable |
| Start time | Defined synchronization start |
| End time | Verified completion or trial termination |
| Request-body bytes | Application payload measurement |
| Captured traffic | Total traffic under the defined capture procedure |
| Request count | Number of synchronization requests |
| Attempt count | Total transmission attempts |
| Completion rate | Verified completed operations divided by scheduled operations |
| Verified sync time | Time to verified completion, where achieved |
| Consistency result | Final-state verification outcome |
| Failure details | Relevant interruption, rejection or failure information |
| Raw result references | Locations or identifiers of raw evidence |

Failed, interrupted and incomplete trials must also be retained and appropriately labelled.

---

## 17. Experimental Fairness

The comparison must ensure that differences in results are attributable to the synchronization mechanisms rather than uncontrolled experimental conditions.

### 17.1 Shared Experimental Conditions

Paired comparisons must use:

- The same initial dataset.
- Equivalent logical workloads.
- The same logical operation sequence.
- The same network profile.
- The same transport.
- The same retry policy.
- The same verification criteria.
- Equivalent trial procedures.

### 17.2 Intended Method Difference

The intended difference between the two methods is their synchronization representation and corresponding payload construction.

Other differences must be justified, documented and assessed for their effect on research validity.

### 17.3 Pilot Trials

Pilot trials will be used to establish practical workload and network-profile parameters.

Pilot results must be reviewed before the main experimental parameters are frozen.

### 17.4 Main Trial Configuration

Before main trials:

- Workload parameters must be finalized.
- Network profiles must be finalized.
- Retry settings must be fixed.
- Batch settings must be fixed.
- Measurement procedures must be documented.
- Verification rules must be fixed.

These parameters must not be changed after reviewing main results merely to favor one synchronization method.

### 17.5 Environmental Deviations

Any deviation from the defined trial configuration must be recorded.

Trials affected by environmental problems must not be silently excluded from the research evidence.

---

## 18. Protocol Testing Requirements

The synchronization protocol must be validated through the following core scenarios.

| Test ID | Scenario | Expected outcome |
|---|---|---|
| SYNC-01 | Normal synchronization | All valid logical operations are processed and verified |
| SYNC-02 | Repeated operation ID | Duplicate delivery does not create a second logical effect |
| SYNC-03 | Interrupted connection | Pending work is retained and can recover |
| SYNC-04 | Response lost after processing | Retry uses the same operation ID and does not duplicate effects |
| SYNC-05 | Repeated edits to one record | Required logical ordering is preserved |
| SYNC-06 | Operations across many records | All operations are processed and final state is correct |
| SYNC-07 | Receipt and queue verification | Client status agrees with server processing evidence |
| SYNC-08 | Invalid operation | Invalid data is rejected with structured error information |
| SYNC-09 | Fair method comparison | Both methods receive equivalent workloads and trial conditions |

Both synchronization methods must pass the applicable shared correctness tests.

Detailed test procedures and acceptance evidence are defined in `test-plan.md`.

---

## 19. Protocol Freeze Checklist

The following decisions must be finalized and approved before synchronization implementation begins.

| ID | Decision | Status |
|---|---|---|
| SP-01 | Operation ID format | Approved: client-generated UUIDv4 |
| SP-02 | Entity ID format | Approved: client-generated UUIDv4 |
| SP-03 | Operation identity across retries | Approved: reuse the original ID |
| SP-04 | Logical workload equivalence | Approved |
| SP-05 | Operation ordering principles | Approved; exact sequencing mechanism pending |
| SP-06 | Full-record representation semantics | Defined at baseline; detailed payload schema pending |
| SP-07 | Change-log representation semantics | Defined at baseline; detailed payload schema pending |
| SP-08 | Batch size and partial acceptance | Pending |
| SP-09 | Retryable failure classification | Pending |
| SP-10 | Non-retryable failure classification | Pending |
| SP-11 | Maximum attempts or stopping rule | Pending |
| SP-12 | Backoff policy | Pending |
| SP-13 | Receipt schema | Pending |
| SP-14 | Idempotency scope and retention | Pending |
| SP-15 | Existing ID with different payload behavior | Pending |
| SP-16 | Conflict and error semantics | Pending |
| SP-17 | Final-state verification algorithm | Pending |
| SP-18 | Network capture method and boundaries | Pending |
| SP-19 | Verified completion timing boundaries | Pending |

No synchronization implementation should begin with unresolved decisions that directly affect its correctness or experimental validity.

---

## 20. Protocol Change Management

Any proposed protocol change must:

1. Identify the affected protocol rule.
2. Explain the reason for the proposed change.
3. Assess its effect on both synchronization methods.
4. Assess its effect on API compatibility.
5. Assess its effect on research fairness and validity.
6. Receive approval before implementation.
7. Update the API contract, test plan and other affected documents.
8. Be recorded in `docs/decisions/` where material.

The protocol must remain consistent across frontend, backend and experimental tooling.

---

## 21. Document Status

This document establishes the approved synchronization principles and the baseline behavior required for the research comparison.

The shared logical workload, stable operation identity, equivalent correctness requirements, duplicate-prevention direction, processing evidence and final-state verification criteria form the protocol foundation.

Exact retry parameters, batch behavior, receipt schemas, idempotency retention, conflict semantics and measurement boundaries remain subject to final approval.

**Implementation status:** Not implemented.

**Related documents:**

- `requirements.md`
- `architecture.md`
- `api-contract.md`
- `test-plan.md`
- `decisions/`

---

**OfflineLink Zambia — Synchronization Protocol Specification**

*A common reliability and correctness protocol for experimentally comparing full-record and change-log synchronization.*