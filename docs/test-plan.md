# OfflineLink Zambia — Test Plan

**Status:** Stage 0 baseline  
**Version:** 0.1  
**Date:** 2026-11-07

## 1. Purpose

This plan defines how implementation correctness and experimental reliability will be validated. Tests must establish behavior, not merely confirm that files exist or applications start.

## 2. Test levels

### 2.1 Backend unit tests
Test schemas, validation, services, repositories where practical, operation identity, synchronization strategy behavior, retry classification and metric calculations.

### 2.2 Backend integration tests
Test API behavior against a test database, migrations, transactional processing, duplicate delivery, receipts and final-state verification.

### 2.3 Frontend unit/component tests
Test form validation, feature behavior, local operation creation, queue state transitions, API payload construction and synchronization status.

### 2.4 Browser/end-to-end tests
Test record creation and editing, offline persistence, connectivity restoration, synchronization and visible status.

### 2.5 Experimental verification
Test workload determinism, network profile application, trial metadata, measurement capture, expected-state generation, result persistence and repeatability.

## 3. Agricultural data tests

- Valid farmer/farm/crop/observation records are accepted.
- Invalid required fields are rejected.
- Relationships are validated.
- Local writes remain available without API connectivity.
- Records persist across application reload.
- Synthetic test fixtures are used.
- No real farmer personal data is used in research tests.

## 4. Offline tests

- Start with API unavailable.
- Create and update records locally.
- Reload the client.
- Verify local records and pending operations remain.
- Restore connectivity.
- Verify pending operations are eligible for synchronization.
- Verify local status reflects server receipts.

## 5. Synchronization correctness tests

Run equivalent scenarios against both methods:
- one operation;
- multiple operations on different records;
- repeated edits to the same record;
- repeated delivery of the same operation ID;
- retry after a transient failure;
- response lost after server processing;
- interruption and recovery;
- invalid operation;
- duplicate batch submission;
- server state verification.

For each scenario, compare actual server state with deterministic expected state and inspect operation receipts. A successful HTTP status is not sufficient.

## 6. Fairness tests

- Same logical workload is supplied to both methods.
- Workload order is preserved where required.
- Same network profile is applied.
- Same retry configuration is applied.
- Same verification criteria are applied.
- Trial configuration is recorded.
- Method-specific payload representation is the only intended synchronization difference.

## 7. Measurement tests

- Request-body byte count matches serialized payload.
- Total captured traffic is measured independently.
- Attempts and retries are counted consistently.
- Verified synchronization time starts and stops at defined events.
- Completion rate denominator equals scheduled logical operations.
- Consistency checker detects missing operations.
- Consistency checker detects unexpected or duplicate effects.
- Raw results are retained unchanged.
- Derived metrics can be recomputed from raw data.

## 8. Research infrastructure tests

- Workload generation is deterministic for a fixed seed/configuration.
- Network profile configuration is recorded.
- Trial IDs are unique.
- Trial metadata is complete.
- Failed and interrupted trials are retained and labeled, not silently removed.
- Raw and analysis outputs are stored separately.
- Exported result files can be parsed by analysis scripts.
- Main-trial parameters are frozen before main evaluation.

## 9. Acceptance gates

### Gate A — Foundation
Backend and frontend start; configuration works; health check works; test tooling runs; database connection and migration workflow are verified.

### Gate B — Data management
CRUD and relationships work; validation and persistence tests pass.

### Gate C — Offline client
Local record operations work without server access; persistence survives reload; pending queue behavior is tested.

### Gate D — Synchronization
Both methods pass shared correctness scenarios, duplicate delivery tests and final-state verification.

### Gate E — Research tooling
Workload and network profile can be reproduced; trial metadata and raw metrics are recorded.

### Gate F — Main experiments
Pilot results reviewed; parameters frozen; trial procedure documented; main trials can be repeated and analyzed.

## 10. Defect handling

For each defect record:
- ID;
- affected layer;
- reproduction steps;
- expected behavior;
- actual behavior;
- severity;
- evidence/logs;
- fix;
- regression test.

Do not proceed past a gate while a defect undermines the correctness or validity required by that gate.

## 11. Test evidence

Retain:
- test command;
- environment and software revision;
- pass/fail output;
- test data or fixture identity;
- relevant logs;
- screenshots only where UI evidence adds value;
- experimental raw files and configuration.

Evidence must be traceable to the code revision that produced it.
