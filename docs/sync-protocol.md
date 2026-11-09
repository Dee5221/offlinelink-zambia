# OfflineLink Zambia — Synchronization Protocol

**Status:** Stage 0 draft baseline  
**Version:** 0.1  
**Date:** 2026-11-07

> This protocol defines research-critical behavior. Details marked as proposed must be reviewed and frozen before synchronization implementation begins.

## 1. Purpose

Implement and experimentally compare:
- full-record synchronization; and
- change-log synchronization.

Both methods transfer locally stored agricultural data to a central server under controlled low-bandwidth and intermittent connectivity conditions.

## 2. Shared invariants

Both methods must:
1. process the same logical workload;
2. preserve a stable unique operation ID;
3. use equivalent operation ordering;
4. use the same transport;
5. use the same retry policy;
6. apply duplicate-prevention rules;
7. provide processing receipts;
8. use the same final-state verification;
9. record comparable metrics.

Any intentional difference must be intrinsic to the synchronization representation, not an accidental difference in workload or reliability behavior.

## 3. Terminology

- **Logical operation:** one intended user-level record creation or edit in the workload.
- **Operation ID:** stable unique identifier for a logical operation, reused on retries.
- **Pending operation:** locally stored operation not yet acknowledged as processed.
- **Attempt:** one transmission attempt for an operation or batch.
- **Receipt:** server evidence of operation processing or duplicate recognition.
- **Expected state:** deterministic final state calculated from the workload and initial dataset.
- **Verified completion:** all intended operations processed, queue empty, final state matches expected state, and no unintended duplicate effects are detected.

## 4. Full-record synchronization

Full-record synchronization transmits the complete current record for each queued logical edit, according to the approved workload semantics.

Requirements:
- Preserve the operation ID across retry attempts.
- Record the logical operation and attempt separately.
- Server must apply duplicate-prevention rules.
- Record payload size and attempt outcomes.
- Do not silently collapse logical edits unless the workload specification explicitly defines that behavior.

## 5. Change-log synchronization

Change-log synchronization transmits individual change entries representing logical edits.

Requirements:
- Each change entry has a stable operation ID.
- Entries preserve the defined logical ordering.
- Server applies changes according to the approved operation semantics.
- Repeated delivery must not apply the same logical operation twice.
- Record payload size and attempt outcomes.

## 6. Operation identity and duplicate prevention

Proposed baseline:
- The client creates a UUID operation ID once for each logical operation.
- Retries reuse that same ID.
- Server stores operation processing identity and result/receipt.
- A repeated operation ID is recognized and does not produce a second logical effect.
- Operation processing and the resulting data mutation should be atomic where supported by the chosen database design.

The final idempotency key scope, retention policy and conflict response must be frozen before implementation.

## 7. Retry policy

The retry policy must be identical for both synchronization methods.

The implementation must define and record:
- retryable failure classes;
- non-retryable validation failures;
- maximum attempts or trial-level stopping rule;
- delay/backoff policy;
- behavior after connection loss or timeout;
- how a client handles an ambiguous outcome where the server may have processed the request;
- when an operation is marked failed versus left pending.

Retry parameters must be configurable for tests but fixed and recorded during a main experimental trial. Do not silently change retry policy between methods.

## 8. Acknowledgement and completion

An HTTP response indicates transport/API response, not necessarily verified trial completion.

The client may mark an operation processed only when it receives a valid processing receipt or equivalent contract-defined evidence.

A trial is verified complete only when:
1. all scheduled logical operations have been processed;
2. no pending operations remain;
3. the final central server state matches the expected state;
4. operation receipts are consistent;
5. no unintended duplicate effects are found.

## 9. Ordering and workload semantics

- Workloads must define the logical operation sequence.
- The same sequence is supplied to both methods.
- Ordering requirements must be explicit for repeated edits to the same record.
- Workload scenarios must include edits spread across many records and repeated edits to fewer records.
- The expected final state must be generated deterministically before the trial.
- The workload seed or exact generated workload must be stored with trial metadata.

## 10. Failure and interruption behavior

Test scenarios should include:
- server unavailable before transmission;
- connection interruption during transmission;
- response lost after server processing;
- retry after ambiguous outcome;
- duplicate operation delivery;
- partial batch acceptance, if batching is supported;
- invalid operation;
- network recovery after offline edits.

The protocol must ensure pending work is not silently discarded and repeated delivery does not create unintended duplicate effects.

## 11. Metrics

### Communication overhead
Measure:
- serialized request-body bytes;
- number of requests and attempts;
- total captured traffic using the defined capture method.

Report request-body bytes separately from total captured traffic, which may include headers, acknowledgements, retransmissions and other traffic.

### Verified synchronization time
Measure from the defined synchronization start until verified completion as defined in `requirements.md`.

### Completion rate
Report successfully verified logical operations divided by scheduled logical operations. State the denominator and failure/stop rules.

### Data consistency
Compare final server state with the deterministic expected state. Record missing, unexpected, duplicated or incorrect effects.

## 12. Trial metadata

Every trial must record at least:
- unique trial ID;
- timestamp;
- synchronization method;
- workload identifier and parameters;
- exact logical operation count;
- network profile identifier and parameters;
- software revision;
- protocol version;
- retry configuration;
- batch configuration, if applicable;
- start/end times;
- request-body bytes;
- total captured traffic;
- request and attempt counts;
- completion rate;
- verified synchronization time;
- consistency result;
- failure details;
- raw result file references.

## 13. Experimental fairness

- Use the same initial dataset and logical workload for paired comparisons.
- Use the same network profile and trial procedure.
- Use the same retry policy and verification criteria.
- Record any environmental deviations.
- Pilot trials are used to establish practical profile/workload parameters.
- Freeze parameters before main trials.
- Do not select or modify parameters after seeing main results merely to favor a method.

## 14. Protocol freeze checklist

Before implementation, resolve:
- operation ID format and scope;
- entity ID format;
- operation ordering rules;
- full-record behavior for repeated edits;
- change-log payload semantics;
- batch size and partial acceptance behavior;
- retry classes and limits;
- backoff policy;
- receipt schema;
- idempotency retention;
- conflict/error semantics;
- final-state verification algorithm;
- measurement capture method.

Record approved decisions in `docs/decisions/`. Both frontend and backend prompts must cite this version.
