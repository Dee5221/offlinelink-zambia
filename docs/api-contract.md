# OfflineLink Zambia — API Contract

**Status:** Stage 0 draft baseline  
**Version:** 0.1  
**Date:** 2026-11-07

> This is a contract scaffold, not a claim that endpoints already exist. Endpoint paths and payloads below are proposed for implementation and must be confirmed/frozen before Stage 2 coding. Backend and frontend prompts must use the same approved version.

## 1. Contract principles

- JSON over HTTP for the application API.
- API versioning should be explicit; initial prefix proposed: `/api/v1`.
- Request and response schemas must be documented and validated.
- IDs must be stable and represented consistently.
- Dates/timestamps must use ISO 8601 with timezone-aware UTC timestamps where a timestamp is required.
- Errors must use a consistent JSON structure.
- Client-generated operation IDs must remain stable across retries.
- Frontend types must match backend schemas; do not independently invent payload fields.

## 2. Proposed endpoint inventory

The following endpoints are proposed as the initial API surface. Confirm during Stage 0 review before implementation.

| Method | Proposed path | Purpose |
|---|---|---|
| GET | `/health` | Basic service health |
| GET | `/api/v1/health` | Versioned API health, if desired |
| POST | `/api/v1/farmers` | Create farmer |
| GET | `/api/v1/farmers` | List farmers |
| GET | `/api/v1/farmers/{farmer_id}` | Retrieve farmer |
| PATCH | `/api/v1/farmers/{farmer_id}` | Update farmer |
| POST | `/api/v1/farms` | Create farm |
| GET | `/api/v1/farms` | List farms |
| GET | `/api/v1/farms/{farm_id}` | Retrieve farm |
| PATCH | `/api/v1/farms/{farm_id}` | Update farm |
| POST | `/api/v1/crops` | Create crop record |
| GET | `/api/v1/crops` | List crop records |
| GET | `/api/v1/crops/{crop_id}` | Retrieve crop record |
| PATCH | `/api/v1/crops/{crop_id}` | Update crop record |
| POST | `/api/v1/observations` | Create observation |
| GET | `/api/v1/observations` | List observations |
| GET | `/api/v1/observations/{observation_id}` | Retrieve observation |
| PATCH | `/api/v1/observations/{observation_id}` | Update observation |
| POST | `/api/v1/sync/full-record` | Submit full-record synchronization batch |
| POST | `/api/v1/sync/change-log` | Submit change-log synchronization batch |
| GET | `/api/v1/sync/operations/{operation_id}` | Retrieve operation processing status/receipt |

Do not implement endpoints not needed by the approved requirements without review.

## 3. Common data conventions

- Entity IDs: UUID strings are proposed; finalize before model/migration creation.
- Operation ID: client-generated stable UUID for each logical operation.
- `created_at` / `updated_at`: UTC ISO 8601 timestamps.
- Optional fields: explicitly nullable in schema and API documentation.
- Pagination: if list endpoints need pagination, use one consistent documented convention.
- Validation: invalid data must return a structured client error.

## 4. Proposed error envelope

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Request validation failed",
    "details": []
  }
}
```

The exact fields and status-code mapping must be finalized before implementation. Do not expose stack traces or secrets to clients.

## 5. Agricultural entity contract

The domain includes:
- Farmer
- Farm, associated with a farmer
- Crop record, associated with a farm
- Observation, associated with an applicable agricultural record

The exact field list must be approved before Stage 2. Do not invent demographic, sensitive or unnecessary farmer fields. Use synthetic records for research.

## 6. Synchronization contract requirements

Each submitted operation must carry:
- stable operation ID;
- operation type;
- entity type and entity ID;
- operation payload or change payload, depending on strategy;
- client creation time or sequence metadata needed by the protocol;
- protocol/version identifier.

Server response must provide enough information for the client to determine:
- which operation IDs were received;
- which were accepted/processed;
- which were rejected and why;
- whether a repeated operation was recognized;
- whether the operation has a durable processing receipt.

The exact request and response schemas are to be specified in `sync-protocol.md` and frozen before implementation. Do not infer completion from HTTP status alone.

## 7. Compatibility rules

- Any change to field names, requiredness, ID type, operation semantics or response meaning requires updating this document.
- Backend and frontend changes must be coordinated.
- A contract change requires relevant schema, service, client and integration tests.
- Keep the contract version or protocol version visible in trial metadata.

## 8. Open items to resolve before coding

- Confirm UUID usage for entity and operation IDs.
- Finalize agricultural record fields and relationships.
- Finalize list pagination and filtering needs.
- Finalize exact synchronization batch and receipt schemas.
- Finalize API error status mapping.
- Decide whether health endpoint is versioned or unversioned.
- Confirm whether direct CRUD API is used by the client during normal online operation or whether all writes are locally queued first.

These items must be resolved in Stage 0 or the relevant design stage before dependent code is generated.
