# ADR 0001: Stage 0 Architecture and Contract Baseline

- **Status:** Proposed for review
- **Date:** 2026-11-07

## Context

OfflineLink Zambia is approved as a dissertation research project comparing full-record and change-log synchronization under controlled emulated network conditions. The repository directories exist, but implementation code has not yet been created.

The project will be implemented incrementally using separate AI coding responsibilities:
- Qwen: backend
- DeepSeek: frontend
- ChatGPT: architecture, prompt preparation, integration review and validation

## Decision

Use the Stage 0 documents as the shared baseline:
- `requirements.md`
- `architecture.md`
- `api-contract.md`
- `sync-protocol.md`
- `test-plan.md`

The proposed technology baseline is React + TypeScript, IndexedDB, FastAPI, PostgreSQL and Linux network namespaces with `tc/netem`.

The backend and frontend must follow the same API and synchronization contracts. Material changes require review and an updated decision record.

## Consequences

- Implementation prompts can be scoped by layer and responsibility.
- Contract changes must be coordinated across backend and frontend.
- Research-critical semantics are defined before synchronization implementation.
- This record remains proposed until the project owner reviews and accepts it.
