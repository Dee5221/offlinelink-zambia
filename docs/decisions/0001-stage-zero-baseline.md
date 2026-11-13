# ADR-0001: Stage 0 Research and Architecture Baseline

- **Status:** Proposed for project-owner approval
- **Date:** 2026-11-11
- **Decision owners:** Project owner and research team
- **Decision scope:** Research direction, system architecture, technology direction, and documentation baseline

## 1. Context

OfflineLink Zambia is a research and software development project investigating offline-first agricultural data collection under unstable and low-bandwidth network conditions in Zambia.

The approved research title is:

**Design and Experimental Evaluation of an Offline-First Agricultural Data Collection Application for Unstable and Low-Bandwidth Network Conditions in Zambia**

The research will design and evaluate an application that enables agricultural field officers to capture and manage farmer, farm, crop, and observation records without continuous internet connectivity.

The project will experimentally compare two synchronization approaches:

1. Full-record synchronization.
2. Change-log synchronization.

The comparison will use controlled network conditions and consistent workloads to evaluate communication overhead, verified synchronization time, completion rate, and data consistency.

At the time of this decision record, the project has an initial repository structure and documentation baseline. Application source code has not yet been implemented, and no experimental results have been produced.

A shared technical and research baseline is therefore necessary before implementation begins.

## 2. Decision

The project will use the following Stage 0 baseline.

### 2.1 Research baseline

The approved research objectives and research questions in `docs/requirements.md` will serve as the authoritative research direction.

The system will focus on four agricultural data entities:

- Farmer
- Farm
- Crop
- Observation

The application will support field officers and a research operator. Experimental data will be synthetic; real farmer personally identifiable information is outside the research scope.

### 2.2 Architectural baseline

The planned system will use a client-server architecture with an offline-first web application.

The principal components are:

- **Frontend:** Web application with Progressive Web App capabilities.
- **Local persistence:** IndexedDB for browser-side storage.
- **Backend:** Python with FastAPI.
- **Central database:** PostgreSQL.
- **Synchronization:** Separate full-record and change-log synchronization paths.
- **Network experimentation:** Linux network namespaces and traffic-control tools, including `tc` and `netem`.
- **Research tooling:** Scripts and supporting tools for workload generation, experiment execution, measurement, analysis, and result export.

The frontend will support local-first data capture and modification. Changes will be retained locally and synchronized when connectivity becomes available.

The backend will provide central persistence, operation processing, synchronization receipts, and the interfaces needed by the research tooling.

### 2.3 Synchronization research baseline

Both synchronization methods will be evaluated under equivalent experimental conditions.

The comparison must preserve:

- The same logical workload.
- Stable operation identifiers across retries.
- Equivalent operation ordering.
- The same transport and retry policy.
- Duplicate-prevention rules.
- Processing receipts and final-state verification.
- Comparable measurement boundaries.

A logical user operation must remain distinguishable from its individual transmission attempts.

A synchronization operation will only count as verified complete when the required processing evidence is available, the local queue is appropriately resolved, and the resulting server state matches the expected state.

The detailed protocol parameters remain subject to the protocol-freeze process in `docs/sync-protocol.md`.

### 2.4 API baseline

The API will use `/api/v1` as its canonical versioned prefix.

The API contract will define the resource structures, validation rules, synchronization endpoints, operation receipts, and error responses.

Client-generated UUIDv4 identifiers will be used for records and logical synchronization operations. Timestamps will use UTC ISO 8601 representation.

Field-officer changes will follow the local-first workflow rather than bypassing the synchronization mechanism through direct online mutations.

The detailed API contract remains documented in `docs/api-contract.md`.

### 2.5 Documentation baseline

The repository documentation will act as the shared source of truth for research requirements, architecture, API design, synchronization behavior, and testing.

The following documents define the current baseline:

| Document | Responsibility |
|---|---|
| `README.md` | Project overview and navigation |
| `docs/README-stage0.md` | Stage 0 status and governance |
| `docs/requirements.md` | Research requirements and scope |
| `docs/architecture.md` | Planned system architecture |
| `docs/api-contract.md` | API design and endpoint contract |
| `docs/sync-protocol.md` | Synchronization protocol |
| `docs/test-plan.md` | Verification and experimental test plan |
| `docs/decisions/` | Architecture decision records |

The documentation must distinguish approved decisions, proposed decisions, unresolved parameters, and implemented functionality.

## 3. Technology Direction

The following technologies are selected as the planned implementation baseline:

| Area | Selected technology |
|---|---|
| Frontend | React with TypeScript |
| Offline storage | IndexedDB |
| Backend | Python with FastAPI |
| Central database | PostgreSQL |
| Network emulation | Linux network namespaces, `tc`, and `netem` |
| Version control | Git and GitHub |

These selections establish the intended direction. They do not imply that the application or its dependencies have already been implemented or tested.

Any material technology change must be recorded and justified.

## 4. Implementation and Collaboration Approach

Implementation will proceed in controlled stages after the documentation baseline has been reviewed.

The planned division of responsibilities is:

- **Qwen:** Backend implementation support.
- **DeepSeek:** Frontend implementation support.
- **ChatGPT:** Architecture coordination, technical review, prompt preparation, integration guidance, and documentation consistency.

These are intended collaboration responsibilities, not evidence of completed implementation.

All generated code must be reviewed, integrated, and tested against the repository requirements and acceptance criteria before being treated as part of the working application.

## 5. Consequences

### 5.1 Positive consequences

- Establishes a common research and technical direction.
- Reduces inconsistencies between implementation and dissertation documentation.
- Provides a shared reference for AI-assisted development.
- Makes synchronization comparisons more reproducible.
- Separates research decisions from implementation progress.
- Provides a basis for traceability between requirements, tests, and experimental results.

### 5.2 Constraints and trade-offs

- IndexedDB introduces browser-side storage and transaction considerations.
- Offline-first operation requires careful queue and retry management.
- Full-record and change-log synchronization require comparable operation identity and verification.
- PostgreSQL and FastAPI introduce backend deployment and integration responsibilities.
- Network emulation and experiment tooling must be configured consistently to support valid comparisons.

### 5.3 Outstanding decisions

This ADR does not freeze every implementation parameter. The following remain to be specified and approved in their respective documents:

- Exact field types, requiredness, nullability, and validation constraints.
- Complete API request and response schemas.
- Retryable and non-retryable failure classifications.
- Retry limits and backoff behavior.
- Ambiguous-outcome recovery behavior.
- Idempotency scope and retention.
- Receipt schema and batch-processing behavior.
- Final-state verification algorithm.
- Measurement boundaries and traffic-capture method.
- Experimental workload parameters and network profiles.

These items must be resolved before the relevant implementation or main experimental stage depends on them.

## 6. Review and Change Control

This decision record is subject to project-owner review.

Once accepted, it establishes the Stage 0 research and architectural baseline. It does not automatically approve unresolved protocol details or establish that implementation acceptance gates have been passed.

Any subsequent change that materially affects the research objectives, architecture, technology direction, or experimental fairness must be documented.

Where necessary, a new ADR should be created rather than silently rewriting a previously accepted decision.

## 7. Acceptance Criteria

This ADR may be marked **Accepted** when the project owner confirms that:

1. The research and architecture direction accurately reflects the approved project.
2. The selected technology direction is acceptable.
3. The documentation set is the shared project baseline.
4. The distinction between agreed direction and unresolved technical parameters is clear.
5. The implementation and collaboration approach is acceptable.

**Current implementation status:** Documentation baseline only. Application source code and experimental results have not yet been produced.