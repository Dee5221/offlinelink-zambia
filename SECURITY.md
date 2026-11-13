# Security Policy

## 1. Overview

OfflineLink Zambia is an academic research and software development project investigating an offline-first agricultural data collection application under unstable and low-bandwidth network conditions in Zambia.

Security is an important consideration because the planned application will handle agricultural records locally and synchronize them with a central backend when connectivity becomes available.

This document establishes the project's security principles, expected development practices, data-handling requirements, and vulnerability-reporting process.

**Project status:** The application is currently at the documentation and design stage. Security controls described here are requirements or planned safeguards, not confirmation of implemented functionality.

## 2. Supported Versions

During development, security fixes and reviews will target the active project branch.

| Version | Security support |
|---|---|
| Main development branch | Active development and review |
| Released versions | None currently |
| Archived versions | No formal security support |

Once releases are introduced, this section will be updated to identify supported versions.

## 3. Security Principles

The project will follow these principles:

### 3.1 Data minimization

Only information necessary for the approved research objectives and application functionality should be collected.

The experimental system will use synthetic agricultural data. Real farmer personally identifiable information is outside the approved research scope.

### 3.2 Least privilege

Users and system components should receive only the permissions required for their responsibilities.

Field officers and research operators should have clearly separated responsibilities where access control is implemented.

### 3.3 Secure communication

Communication between the frontend and backend should use HTTPS in deployment environments.

Authentication credentials, authorization tokens, and sensitive information must not be transmitted through insecure channels.

### 3.4 Data integrity

Agricultural records and synchronization operations must be validated before being accepted by the backend.

The synchronization implementation must protect against duplicate processing, invalid relationships, malformed operations, and inconsistent record states.

### 3.5 Secure failure handling

Errors must not expose passwords, tokens, database credentials, internal stack traces, or other sensitive implementation details.

Failure recovery must preserve synchronization integrity and must not silently discard pending operations.

## 4. Data Protection Requirements

### 4.1 Data classification

The project will use the following data categories:

| Category | Examples | Handling |
|---|---|---|
| Research data | Synthetic farmer, farm, crop, and observation records | Permitted for experiments |
| Application data | Local records, queued operations, synchronization receipts | Validate and protect |
| Configuration secrets | Database passwords, API secrets, signing keys | Never commit to Git |
| Operational logs | Errors, synchronization events, experiment metadata | Avoid sensitive values |
| Research results | Traffic measurements, timing results, completion rates | Preserve integrity and reproducibility |

### 4.2 Personally identifiable information

The research implementation must not use real farmer personal information unless the research scope is formally revised and the necessary ethical and data-protection approvals are obtained.

Avoid collecting or storing:

- National registration numbers.
- Personal telephone numbers.
- Residential addresses.
- Personal identification documents.
- Precise personal location information.
- Other unnecessary identifying details.

Synthetic records should be used for testing, workload generation, and experimental evaluation.

### 4.3 Local browser storage

The planned frontend will use IndexedDB for offline persistence.

Implementation must consider:

- Restricting access to application data through appropriate application controls.
- Validating data before local persistence and synchronization.
- Avoiding unnecessary storage of authentication secrets.
- Handling browser storage failures without silently losing queued operations.
- Clearing or invalidating locally stored data when a user signs out, where appropriate.
- Recognizing that browser-side storage is not inherently encrypted or protected from someone with access to the device or browser profile.

Offline functionality must not be presented as equivalent to secure storage on a fully managed device.

## 5. Authentication and Authorization

The backend must implement an appropriate authentication and authorization mechanism before deployment beyond controlled development and testing environments.

Security requirements include:

1. Authentication credentials must not be stored in plaintext.
2. Passwords, if used, must be protected using an appropriate password-hashing algorithm.
3. Access to protected endpoints must be authorized.
4. Field officer and research operator responsibilities must be clearly defined.
5. Authentication tokens must be handled securely and must not be exposed in logs or source control.
6. Authentication and authorization failures must produce safe, structured error responses.

The exact authentication method and role-permission model remain subject to a separate technical decision.

## 6. API Security

The planned backend will use Python and FastAPI.

API implementation must include:

- Request validation using defined schemas.
- Validation of UUID identifiers and timestamps.
- Validation of relationships between Farmer, Farm, Crop, and Observation records.
- Appropriate HTTP status codes and structured error responses.
- Protection against unauthorized access.
- Sensible request-size and input limits.
- Safe database access through parameterized queries or appropriate ORM mechanisms.
- No exposure of internal exception details to clients.
- Rate limiting or equivalent abuse controls where appropriate for the deployment environment.

The API must not trust client-submitted data merely because it originates from the offline application.

## 7. Synchronization Security and Integrity

Synchronization is a critical component of OfflineLink Zambia.

The implementation must preserve data integrity when connectivity is interrupted, requests are retried, or server responses are lost.

Required safeguards include:

### 7.1 Operation identity

Each logical synchronization operation must have a stable, unique operation identifier.

Retries must reuse the original operation identifier rather than create a new logical operation.

### 7.2 Duplicate prevention

The backend must prevent repeated delivery of the same logical operation from causing unintended duplicate effects.

Idempotency checks must also consider whether a previously used operation identifier is being reused with a different payload.

### 7.3 Validation

The backend must validate incoming operations, including:

- Operation identifiers.
- Entity identifiers.
- Required fields.
- Data types and formats.
- Parent-child relationships.
- Operation structure and supported operation types.

### 7.4 Atomic processing

Where supported by the database and processing design, operation acceptance, resulting data changes, and processing receipts should be handled atomically.

### 7.5 Safe recovery

A network interruption or ambiguous response must not cause the client to assume that an operation failed when the server may already have processed it.

The client should use the agreed receipt and retry mechanisms to establish the actual processing outcome.

### 7.6 Experimental integrity

Security and integrity checks must apply consistently to both full-record and change-log synchronization methods.

No synchronization method should receive an advantage through weaker validation, duplicate prevention, or verification requirements.

Detailed synchronization behavior is defined in `docs/sync-protocol.md`.

## 8. Database Security

The planned central database is PostgreSQL.

Implementation and deployment must consider:

- Restricting database access to authorized application components.
- Using dedicated database credentials rather than administrative accounts for normal application operations.
- Protecting database credentials through environment configuration or an appropriate secrets-management mechanism.
- Avoiding public database exposure.
- Applying database migrations through controlled procedures.
- Validating and constraining stored data.
- Backing up important development and research data where necessary.
- Avoiding sensitive values in database logs.

Database access must not be exposed directly to the frontend.

## 9. Secrets and Configuration Management

Sensitive configuration values must never be committed to the repository.

Examples include:

- Database usernames and passwords.
- API secrets.
- Authentication signing keys.
- Access tokens.
- Deployment credentials.

Development configuration should use environment variables and a local `.env` file excluded from version control.

The repository may contain `.env.example` with placeholder values and descriptions of required configuration variables.

If a secret is accidentally committed:

1. Treat it as compromised.
2. Revoke or rotate the affected credential.
3. Review repository history and exposure.
4. Remove the secret from active files and address historical exposure appropriately.
5. Document the incident and corrective action.

Deleting a secret from the latest commit alone does not guarantee that it has been removed from Git history or external copies.

## 10. Dependency and Development Security

All contributors must follow responsible dependency-management practices.

Requirements include:

- Use trusted package sources.
- Avoid unnecessary dependencies.
- Review dependency updates before integration.
- Investigate known vulnerabilities in dependencies.
- Avoid installing untrusted scripts or packages.
- Keep development tools and supported runtimes reasonably up to date.
- Review generated code before committing it.
- Do not execute unreviewed AI-generated code in environments containing sensitive credentials.

AI-assisted development does not remove the responsibility to review, test, and validate code.

## 11. Logging and Monitoring

Application logs should support troubleshooting, synchronization verification, and experimental reproducibility without unnecessarily exposing sensitive information.

Logs may capture:

- Operation identifiers.
- Entity identifiers where appropriate.
- Synchronization method.
- Attempt number.
- Processing outcome.
- Error category.
- Timestamps.
- Experiment and workload identifiers.

Logs must not contain:

- Passwords.
- Authentication tokens.
- Database credentials.
- Secret keys.
- Unnecessary personal information.

Research logs and experimental results should be stored separately from application secrets and protected against accidental modification or loss.

## 12. Network and Deployment Security

Network experiments may intentionally emulate:

- Low bandwidth.
- High latency.
- Packet loss.
- Temporary disconnections.
- Intermittent connectivity.

These conditions are experimental network profiles, not permission to disable application security controls.

Development and deployment environments should:

- Restrict access to experimental services.
- Avoid exposing development databases or administrative interfaces publicly.
- Use HTTPS where appropriate.
- Separate experimental network configuration from ordinary host networking.
- Avoid storing real credentials in experiment scripts or raw results.
- Restore network settings after controlled experiments.
- Document network configuration changes that could affect the host system.

## 13. Vulnerability Reporting

Security vulnerabilities should be reported privately to the project owner or designated project maintainer.

Reports should include:

- A description of the suspected vulnerability.
- The affected component or file.
- Steps to reproduce the issue, where safe.
- Potential impact.
- Supporting logs or evidence with secrets and personal information removed.

Please do not publicly disclose an exploitable vulnerability before the project maintainers have had a reasonable opportunity to investigate it.

This is an academic project and does not currently provide a formal bug-bounty programme or guaranteed response-time commitment.

## 14. Security Review Checklist

Security-related implementation work should be reviewed against the following checklist:

- [ ] No real farmer personally identifiable information is used in research fixtures.
- [ ] Secrets are excluded from source control.
- [ ] API inputs are validated.
- [ ] Database access is restricted.
- [ ] Authentication and authorization requirements are addressed.
- [ ] Local storage limitations are documented.
- [ ] Synchronization operations use stable identifiers.
- [ ] Duplicate processing is prevented.
- [ ] Ambiguous synchronization outcomes are handled safely.
- [ ] Logs do not expose credentials or unnecessary personal information.
- [ ] Dependencies are reviewed.
- [ ] Experimental network configuration does not unnecessarily expose services.
- [ ] Security-relevant tests and findings are documented.

A checked item should only be marked complete when implementation evidence or test evidence supports it.

## 15. Related Documentation

- `README.md` — Project overview.
- `docs/requirements.md` — Research scope and requirements.
- `docs/architecture.md` — Planned system architecture.
- `docs/api-contract.md` — API design.
- `docs/sync-protocol.md` — Synchronization protocol.
- `docs/test-plan.md` — Verification and experimental test plan.
- `docs/decisions/` — Architecture decision records.

## 16. Policy Review

This policy will be reviewed when:

- Authentication or authorization is introduced.
- The application begins handling real user data.
- The deployment architecture changes.
- New external services or integrations are introduced.
- A significant security vulnerability is discovered.
- Research scope or data-handling requirements change.

**Last updated:** 11 November 2026

**Current status:** Security requirements documented; implementation and security verification pending.