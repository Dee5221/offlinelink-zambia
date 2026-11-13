# OfflineLink Zambia

**An Offline-First Agricultural Data Collection Application for Unstable and Low-Bandwidth Network Conditions in Zambia**

## 1. Project Overview

OfflineLink Zambia is an academic research and software development project focused on designing and experimentally evaluating an offline-first agricultural data collection application for environments with unstable or limited internet connectivity.

The project addresses a practical challenge faced by agricultural field officers: the inability to reliably capture, access, and update agricultural records when internet connectivity is unavailable or inconsistent.

The proposed application will allow field officers to work locally, retain changes on their devices, and synchronize those changes with a central server when connectivity becomes available.

The research will experimentally compare two synchronization approaches:

1. **Full-record synchronization:** Transfers the complete current record associated with a logical change.
2. **Change-log synchronization:** Transfers individual recorded changes made to a record.

Both approaches will be evaluated under controlled network conditions to determine their relative performance and data-integrity characteristics.

## 2. Research Background

Connectivity limitations in rural and peri-urban environments can affect the reliability of digital agricultural information systems.

An application that depends entirely on continuous internet access may interrupt fieldwork, delay data submission, and make record management difficult.

OfflineLink Zambia investigates an alternative approach in which data collection can continue independently of network availability, with synchronization taking place when a connection becomes usable.

The project uses an agricultural data collection scenario as its research context.

## 3. Research Aim and Objectives

### Research aim

To design and experimentally evaluate an offline-first agricultural data collection application under unstable and low-bandwidth network conditions in Zambia.

### Specific objectives

1. To design and develop an offline-first agricultural data collection application that enables agricultural field officers to capture, view, and update farmer and crop records without continuous internet connectivity, while retaining changes locally until synchronization becomes possible.

2. To implement full-record and change-log synchronization mechanisms for transferring local agricultural data to a central server, incorporating consistent retry, duplicate-prevention, and data-correctness rules.

3. To experimentally compare both synchronization approaches under controlled emulated low-bandwidth and intermittent network conditions using communication overhead, verified synchronization time, completion rate, and data consistency.

## 4. Research Questions

The research will investigate:

1. How do full-record and change-log synchronization compare in terms of communication overhead, verified synchronization time, and synchronization completion rate?

2. How does the distribution of edits across many records, compared with repeated edits to fewer records, affect the performance of each synchronization approach?

3. To what extent do the synchronization approaches preserve data correctness and produce the expected final server state under retries and network interruptions?

## 5. Project Scope

### In scope

- Offline-first agricultural data collection.
- Local browser persistence.
- Farmer, farm, crop, and observation records.
- Local change queues.
- Full-record synchronization.
- Change-log synchronization.
- Stable operation identifiers and duplicate prevention.
- Retry and interruption recovery.
- Central server persistence.
- Controlled network emulation.
- Synthetic workload generation.
- Experimental measurement and analysis.
- Test evidence and reproducibility documentation.

### Out of scope

- Agricultural marketplace and e-commerce functionality.
- Payment processing.
- AI-based crop diagnosis.
- Weather forecasting.
- Mapping and geolocation services.
- Production-scale national deployment.
- Unrestricted concurrent editing and advanced conflict resolution.
- Real farmer personally identifiable information.

## 6. Planned System Architecture

The planned system follows a client-server architecture with an offline-first web application.

| Component | Planned technology | Responsibility |
|---|---|---|
| Frontend | React and TypeScript | User interface and agricultural record management |
| Offline persistence | IndexedDB | Local records and pending operations |
| Backend | Python and FastAPI | API, validation, and synchronization processing |
| Central database | PostgreSQL | Persistent server-side agricultural records |
| Network experimentation | Linux network namespaces, `tc`, and `netem` | Controlled network conditions |
| Research tooling | Python scripts and supporting tools | Workload generation, measurement, and analysis |
| Version control | Git and GitHub | Source and documentation management |

These are the selected technology directions. Their implementation and integration remain pending.

## 7. Synchronization Research

The application will implement two synchronization approaches using a shared set of research requirements.

### 7.1 Full-record synchronization

The full-record method will transmit the complete current record associated with each queued logical change.

The implementation must preserve logical operation identity and prevent repeated transmission from creating unintended duplicate effects.

### 7.2 Change-log synchronization

The change-log method will transmit individual changes recorded by the client.

The implementation must preserve operation ordering and support safe processing when requests are retried or interrupted.

### 7.3 Fair comparison

Both approaches will be evaluated using equivalent:

- Initial datasets.
- Logical workloads.
- Operation ordering.
- Network profiles.
- Retry policies.
- Transport conditions.
- Verification requirements.

### 7.4 Evaluation metrics

| Metric | Purpose |
|---|---|
| Communication overhead | Measures the data transferred during synchronization |
| Verified synchronization time | Measures the time required to establish successful, verified completion |
| Completion rate | Measures verified completed logical operations against scheduled logical operations |
| Data consistency | Measures whether the final server state matches the expected state |

Request-body size and total captured network traffic will be treated as separate measurements.

## 8. Repository Structure

The repository is organized to separate application components, research tooling, and project documentation.

```text
offlinelink-zambia/
│
├── backend/
│   ├── app/
│   │   ├── api/
│   │   ├── core/
│   │   ├── models/
│   │   ├── repositories/
│   │   ├── schemas/
│   │   ├── services/
│   │   └── synchronization/
│   ├── migrations/
│   └── tests/
│
├── frontend/
│   ├── public/
│   ├── src/
│   │   ├── app/
│   │   ├── components/
│   │   ├── db/
│   │   ├── features/
│   │   │   ├── crops/
│   │   │   ├── farmers/
│   │   │   ├── farms/
│   │   │   ├── observations/
│   │   │   └── synchronization/
│   │   ├── services/
│   │   └── styles/
│   └── tests/
│
├── research/
│   ├── analysis/
│   ├── network-profiles/
│   ├── protocol/
│   ├── raw-results/
│   ├── scripts/
│   └── workloads/
│
├── docs/
│   ├── decisions/
│   │   ├── 0001-stage-zero-baseline.md
│   │   └── README.md
│   ├── README-stage0.md
│   ├── api-contract.md
│   ├── architecture.md
│   ├── requirements.md
│   ├── sync-protocol.md
│   └── test-plan.md
│
├── .env.example
├── .gitignore
├── .vscode/
│   └── settings.json
├── README.md
└── SECURITY.md
```

This structure represents the prepared project layout. The presence of a directory does not imply that its application code has been implemented.

## 9. Documentation Guide

The following documents provide the project's detailed specifications and governance.

| Document | Description |
|---|---|
| `docs/README-stage0.md` | Stage 0 progress, governance, and readiness |
| `docs/requirements.md` | Research objectives, questions, scope, and requirements |
| `docs/architecture.md` | Planned system architecture and component responsibilities |
| `docs/api-contract.md` | API structure, endpoints, and data contracts |
| `docs/sync-protocol.md` | Synchronization behavior, operation identity, retries, and verification |
| `docs/test-plan.md` | Functional, synchronization, and experimental test strategy |
| `docs/decisions/README.md` | Architecture Decision Record guidelines |
| `docs/decisions/0001-stage-zero-baseline.md` | Stage 0 research and architecture baseline |
| `SECURITY.md` | Security requirements and vulnerability-reporting policy |

Architecture Decision Records are used to document significant decisions, their reasoning, and their consequences.

## 10. Development and Collaboration Approach

The project will use a controlled, documentation-led development process.

Planned AI-assisted responsibilities:

| Tool | Planned responsibility |
|---|---|
| Qwen | Backend implementation support |
| DeepSeek | Frontend implementation support |
| ChatGPT | Architecture coordination, technical review, prompt preparation, integration guidance, and documentation consistency |

All generated code must be reviewed, integrated, and tested against the approved requirements.

AI-generated output is not automatically accepted as working or verified functionality.

## 11. Testing and Experimental Methodology

Testing will cover the following areas:

- Data validation and relationship integrity.
- Local persistence and offline behavior.
- Synchronization correctness.
- Duplicate-operation prevention.
- Retry and interrupted-connection recovery.
- Processing receipts and queue resolution.
- Fairness between synchronization approaches.
- Measurement accuracy.
- Experimental reproducibility.

Network conditions will be emulated using Linux networking tools, including `tc` and `netem`, with controlled profiles for bandwidth, latency, packet loss, and interruptions.

The main experimental parameters will be established during pilot testing and frozen before the main comparison.

Raw experimental results, configuration details, software revisions, and derived measurements must be preserved separately.

Refer to `docs/test-plan.md` for the detailed verification and acceptance strategy.

## 12. Security and Data Protection

Security requirements are documented in `SECURITY.md`.

The research implementation will use synthetic agricultural records. Real farmer personally identifiable information is outside the approved research scope.

The planned implementation must address:

- API input validation.
- Authentication and authorization.
- Secure configuration and secret management.
- Database access restrictions.
- Local browser storage limitations.
- Synchronization integrity.
- Duplicate prevention.
- Safe error handling and logging.

Security requirements must be verified through implementation evidence and testing before the system is treated as deployment-ready.

## 13. Current Project Status

<badge color="success">Documentation baseline established</badge>

<badge color="warning">Implementation pending</badge>

The project has an initial Git repository structure and a documented Stage 0 technical and research foundation.

| Area | Current status |
|---|---|
| Research title and objectives | Approved |
| Research questions and scope | Established |
| Repository structure | Prepared |
| Requirements documentation | Reviewed |
| Architecture documentation | Reviewed |
| API contract | Reviewed; detailed specifications remain open |
| Synchronization protocol | Reviewed; protocol parameters remain open |
| Test plan | Drafted for review |
| ADR governance | Documented |
| Security policy | Reviewed |
| Frontend implementation | Not started |
| Backend implementation | Not started |
| Database implementation | Not started |
| Synchronization implementation | Not started |
| Experimental execution | Not started |

**Important:** No application source code or experimental results are currently claimed by this README. Progress must be updated as implementation and verification evidence becomes available.

## 14. Development Roadmap

The planned development sequence is:

1. **Stage 0 – Research and technical foundation:** Establish requirements, architecture, API contracts, synchronization protocol, testing strategy, security requirements, and decision records.
2. **Stage 1 – Backend foundation:** Establish the backend application, database integration, data models, validation, and API foundation.
3. **Stage 2 – Frontend and offline persistence:** Develop the user interface, local data management, and offline-first workflows.
4. **Stage 3 – Synchronization implementation:** Implement full-record and change-log synchronization, operation identity, retry handling, receipts, and verification.
5. **Stage 4 – Integration and verification:** Integrate the application components and execute the required functional and synchronization tests.
6. **Stage 5 – Experimental preparation:** Develop workloads, configure network profiles, conduct pilot tests, and freeze experimental parameters.
7. **Stage 6 – Main experiments and analysis:** Execute the controlled comparison, preserve results, analyze findings, and prepare research outputs.

Each stage must meet its documented acceptance criteria before the project advances.

## 15. Repository Setup

The following setup steps will apply when implementation begins:

```bash
git clone https://github.com/Dee5221/offlinelink-zambia.git

cd offlinelink-zambia

git status
```

The exact installation and execution instructions for the frontend, backend, database, and research tools will be added when their implementation and configuration files are established.

Do not assume that application services can currently be started from this repository.

## 16. Project Governance

The project will maintain a clear distinction between:

- Approved research and architectural decisions.
- Proposed or unresolved technical specifications.
- Implemented functionality.
- Verified functionality supported by test evidence.
- Experimental findings supported by recorded results.

Material changes to research objectives, system architecture, synchronization behavior, or experimental methodology must be documented and reviewed.

The repository documentation is the shared reference for implementation and research activities.

## 17. Project Repository

**GitHub:** https://github.com/Dee5221/offlinelink-zambia

---

**OfflineLink Zambia**

*Reliable agricultural data collection, even when connectivity is not.*