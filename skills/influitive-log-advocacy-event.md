---
name: Log an event and award advocacy points
description: >-
  Log an event or act of advocacy for a contact in an Influitive AdvocateHub and award points, optionally
  advancing an API-integrated challenge stage.
api: openapi/influitive-events-openapi.json
operations:
- events
generated: '2026-07-19'
method: generated
---

# Log an event and award advocacy points

Use the Influitive Events API to record an action a customer took outside of Influitive and award them
points inside their AdvocateHub. This is the only public write operation; it can also advance a challenge
stage and record an act of advocacy.

## Prerequisites

- An **API token** and your hub's **organization id** (`X_ORG_ID`).
- A **custom event** created in the AdvocateHub whose API code you pass as `type`. See
  https://support.influitive.com/article/142-api-setting-up-custom-event-types
- (Optional) An API-integrated **challenge stage** if the event should progress a challenge. See
  https://support.influitive.com/article/439-challenge-stage-api-integration

## Authentication

Send two headers (both required):

- `Auhtorization Token: <api-token>` — note the header name is spelled exactly this way in the provider's
  published spec (a vendor typo); verify against influitive.readme.io before use.
- `X_ORG_ID: <organization-id>`

## Steps

1. **`events`** — `POST https://api.influitive.com/events` with `Content-Type: application/json`.
   - Required body fields: `type` (the custom event API code) and `contact` (one of `id`, `email`, or
     `crm_contact_id`). If the email does not exist in the hub, a profile is created.
   - Optional: `points` (points to award), `stage.code` (advance a challenge stage — do not send both
     `stage` and `advocacy`), and `advocacy` (`advocacy_type`, `content`, `source_title`, `source_url`,
     `occurred_at`) to record an act of advocacy.
   - Success returns **201** with the event `id`, `points`, and the affected `contact` profile.

## Conventions and error handling

- The operation is **not idempotent** — retries re-award points; guard against duplicate sends yourself
  (see `conventions/influitive-conventions.yml`).
- A **401 Unauthorized** (`WWW-Authenticate: Token realm=Api`) means the token or `X_ORG_ID` is missing or
  invalid. Errors are `text/plain`, not problem+json (see `errors/influitive-problem-types.yml`).
