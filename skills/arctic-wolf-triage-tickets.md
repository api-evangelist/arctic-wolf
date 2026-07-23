---
name: Triage Arctic Wolf security tickets
description: List open Arctic Wolf security tickets for an organization, review a ticket, add a comment, and close it — using the Ticket API.
api: openapi/arctic-wolf-ticket-api-openapi.json
operations:
- listTickets
- getTicketById
- addCommentToTicket
- closeTicket
- getAttachment
---

# Triage Arctic Wolf security tickets

Operate the Arctic Wolf **Ticket API** to review and act on security tickets for an
organization. All calls are bearer-authenticated and scoped to a regional pod.

## Auth & setup
- Generate a **Personal API Key** in the Unified Portal (portal.arcticwolf.com):
  Organization Profile > Personal API Keys > Create an API Key.
- Send it as a bearer token: `Authorization: Bearer <token>` (JWT).
- Pick the **pod host** matching your organization, e.g.
  `https://ticket-api.managedgw.us001-prod.arcticwolf.net` (also US002/US003, EU001, AU001, CA001).
- You need the `organizationUuid` for every call.

## Steps

1. **List open tickets** — call `listTickets`
   (`GET /api/v1/organizations/{organizationUuid}/tickets`). Filter with query params:
   `status`, `priority`, `type`, `assigneeByEmail`, `createdAfter`/`createdBefore`,
   `updatedAfter`/`updatedBefore`, `includeComments`. Page with `offset`/`limit`
   (limit 1–1000); read `meta.total` for the full count.

2. **Inspect a ticket** — call `getTicketById`
   (`GET /api/v1/organizations/{organizationUuid}/tickets/{ticketId}`) to read the
   ticket detail, status, priority, and comments.

3. **Download evidence (optional)** — call `getAttachment`
   (`GET /api/v1/organizations/{organizationUuid}/tickets/{ticketId}/attachments/{attachmentId}`).

4. **Add a comment** — call `addCommentToTicket`
   (`POST /api/v1/organizations/{organizationUuid}/tickets/{ticketId}/comments`) with the
   comment body.

5. **Close the ticket** — call `closeTicket`
   (`POST /api/v1/organizations/{organizationUuid}/tickets/{ticketId}/close`) once resolved.

## Error handling
Errors return a custom `{ code, description }` envelope (not RFC 9457). Handle:
`400` bad request, `401` invalid/missing token, `403` no access to the org,
`404` ticket/attachment not found, `500` server error. Re-auth on `401`; do not retry `4xx`.
No idempotency key is supported, so avoid blindly retrying `POST` close/comment calls.
