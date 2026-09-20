# TakedownCare — Database, API & Backend Prompt

## Role
You are the lead backend architect.

Build a secure, maintainable production backend.

## Recommended Stack
- TypeScript
- Next.js server capabilities or Node.js API
- PostgreSQL
- Prisma or Drizzle
- Zod
- Managed authentication
- S3-compatible private object storage
- Email provider
- AI provider abstraction
- Background jobs
- OpenAPI

## Architecture
HTTP → Validation → Authorization → Application Services → Domain Logic → Repository/ORM → PostgreSQL.

External providers must use adapter interfaces.

## Tables
Implement:
- users
- profiles
- cases
- case_urls
- evidence
- case_events
- case_notes
- case_actions
- requests
- request_events
- notifications
- support_tickets
- abuse_reports
- ai_interactions
- audit_logs
- help_articles

## Key Case Fields
id, public_case_id, user_id, category, title, description, status, readiness_status, created_at, updated_at, closed_at.

## Evidence Fields
id, case_id, uploaded_by, file_name, storage_key, mime_type, detected_mime_type, file_size, sha256_hash, description, scan_status, created_at, deleted_at.

## Request Fields
id, case_id, type, status, title, body, official_destination, user_confirmed, submitted_at, created_at, updated_at.

## Case State Machine
DRAFT → READY_FOR_REVIEW → UNDER_REVIEW → ACTION_NEEDED / REQUEST_PREPARED / RESOLVED.

REQUEST_PREPARED → SUBMITTED → WAITING_FOR_RESPONSE → ACTION_NEEDED / RESOLVED.

RESOLVED → CLOSED.

Validate every transition server-side.

## API
Implement:
POST/GET/PATCH /api/cases
POST/DELETE /api/cases/:id/urls
POST/GET/DELETE /api/cases/:id/evidence
POST /api/cases/:id/evidence/:evidenceId/download
GET /api/cases/:id/events
POST /api/cases/:id/notes
POST /api/cases/:id/review
POST /api/cases/:id/close
POST/GET /api/cases/:id/requests
GET/PATCH /api/cases/:id/requests/:requestId
POST /api/cases/:id/requests/:requestId/review
POST /api/cases/:id/requests/:requestId/submit
POST /api/guide/message
GET /api/notifications
PATCH /api/notifications/:id/read
POST /api/support/tickets
GET /api/support/tickets
GET /api/support/tickets/:id
POST /api/support/tickets/:id/messages

## Admin API
Implement case management, abuse reports, support, and audit-log endpoints with explicit permissions.

## API Rules
- validate server-side;
- authenticate;
- authorize;
- use consistent errors;
- use pagination;
- use idempotency for retryable writes;
- never leak internal errors.

## Error Format
```json
{
  "error": {
    "code": "CASE_NOT_FOUND",
    "message": "The requested case could not be found.",
    "request_id": "req_..."
  }
}
```

## Evidence
Private object storage only.

Upload:
validate → detect MIME → scan → store → save metadata → event.

Download:
authenticate → authorize → signed short-lived URL.

Default configurable limits:
25 MB/file, 20 files/case.

## Database Requirements
Define:
- primary keys;
- foreign keys;
- indexes;
- unique constraints;
- transactions;
- cascade/restrict behavior;
- soft deletion;
- retention strategy.

## Security
Prevent IDOR/BOLA, injection, unauthorized access, unsafe file handling, and privilege escalation.

## Background Jobs
Use jobs for:
- email;
- scanning;
- notifications;
- AI tasks where appropriate;
- cleanup;
- retention;
- provider synchronization.

## Definition of Done
Backend supports the complete core case workflow, is authorized server-side, has migrations, tests, audit events, private storage, consistent errors, and production-ready configuration.
