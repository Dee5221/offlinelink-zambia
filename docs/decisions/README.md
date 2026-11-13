# Architecture Decision Records (ADRs)

This directory contains Architecture Decision Records (ADRs) for OfflineLink Zambia.

ADRs document significant technical and architectural decisions, the reasoning behind them, their consequences, and any alternatives considered. They provide a traceable record of why the project follows a particular direction.

## 1. Purpose

ADRs help ensure that:

- Architectural decisions are documented rather than kept only in conversations.
- Implementation remains consistent with the approved research requirements.
- Technical changes have a recorded rationale.
- AI-assisted development uses the same agreed project direction.
- Future reviewers can understand the reasoning behind the system design.

An ADR records a decision and its context. It does not replace implementation documentation, API specifications, test plans, or research results.

## 2. ADR Naming Convention

Use sequential numbering and descriptive filenames:

`NNNN-short-decision-title.md`

Examples:

- `0001-stage-zero-baseline.md`
- `0002-database-technology.md`
- `0003-synchronization-idempotency.md`

Once an ADR has been accepted, do not reuse its number for another decision.

## 3. ADR Status

Every ADR must have one of the following statuses:

| Status | Meaning |
|---|---|
| Proposed | Submitted for review; not yet accepted |
| Accepted | Approved by the project owner |
| Rejected | Reviewed but not approved |
| Superseded | Replaced by a later decision |

A proposed decision must not be treated as an approved implementation requirement.

An accepted ADR remains part of the project's decision history. If a decision changes, create a new ADR that references the earlier record and explains the change.

## 4. When to Create an ADR

Create an ADR when a decision materially affects one or more of the following:

- System architecture or component boundaries.
- Frontend, backend, database, or infrastructure technology.
- Offline persistence and synchronization behavior.
- Data integrity, idempotency, retry, or conflict-handling rules.
- API design or integration contracts.
- Security, privacy, or access control.
- Experimental methodology or fairness of the comparison.
- Deployment assumptions or significant project constraints.

Routine implementation details that do not materially affect the architecture do not require separate ADRs.

## 5. Required ADR Structure

Each ADR should use the following structure.

### Title

A short, descriptive title identifying the decision.

### Metadata

- **Status**
- **Date**
- **Decision owners**
- **Decision scope**

### 1. Context

Explain the problem, constraints, research requirements, and circumstances that make the decision necessary.

### 2. Decision

State the selected approach clearly and precisely.

### 3. Alternatives Considered

List relevant alternatives and explain why they were not selected. If alternatives have not yet been evaluated, state that explicitly rather than inventing a comparison.

### 4. Consequences

Document the benefits, limitations, risks, and trade-offs resulting from the decision.

### 5. Implementation Implications

Explain which documents, components, tests, or project activities are affected.

Distinguish planned work from functionality that has actually been implemented.

### 6. Review and Change Control

Explain how the decision can be reviewed, amended, or superseded.

## 6. Decision-Making Rules

The following rules apply to all ADRs:

1. The project owner is responsible for approving or rejecting proposed decisions.
2. Technical recommendations must be consistent with the approved research objectives and scope.
3. Decisions must be supported by clear reasoning.
4. Unresolved questions must remain explicitly documented.
5. An ADR must not claim that a feature, test, or experiment is complete without evidence.
6. Material changes to an accepted decision must be recorded in a new ADR.
7. Related documents must be updated when an accepted decision changes their requirements.

## 7. Relationship with Other Project Documents

ADRs complement the main project documentation.

| Document | Relationship to ADRs |
|---|---|
| `docs/requirements.md` | Defines what the system and research must achieve |
| `docs/architecture.md` | Describes the planned system structure |
| `docs/api-contract.md` | Defines API interfaces and expected behavior |
| `docs/sync-protocol.md` | Defines synchronization rules and invariants |
| `docs/test-plan.md` | Defines verification and experimental tests |
| `docs/decisions/` | Records why significant decisions were made |

When an ADR conflicts with a detailed technical specification, the project owner must resolve the inconsistency and update the affected documents. Do not silently assume that one document overrides another.

## 8. ADR Template

Use the following template when creating a new ADR:

```markdown
# ADR-NNNN: Decision Title

- **Status:** Proposed
- **Date:** YYYY-MM-DD
- **Decision owners:** Project owner and relevant contributors
- **Decision scope:** Short description

## 1. Context

Describe the problem, requirements, and constraints.

## 2. Decision

State the selected approach.

## 3. Alternatives Considered

Describe the alternatives and reasons for selecting or rejecting them.

## 4. Consequences

Document benefits, limitations, risks, and trade-offs.

## 5. Implementation Implications

Identify affected documents, components, tests, and implementation work.

## 6. Review and Change Control

Describe approval, review, and supersession arrangements.
```

## 9. Current ADR Register

| ADR | Title | Status |
|---|---|---|
| ADR-0001 | Stage 0 Research and Architecture Baseline | Proposed |

Update this register whenever an ADR is created or its status changes.

## 10. Project Governance Principle

**Document the decision, preserve its reasoning, and verify its implementation.**

An approved architectural direction is not proof of implementation. Implementation status must be supported by repository evidence, test results, and documented verification.