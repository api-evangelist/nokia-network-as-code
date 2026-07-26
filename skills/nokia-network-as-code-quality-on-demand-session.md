---
name: Boost network quality for a session with Quality on Demand
description: Create, inspect, extend and tear down a CAMARA Quality on Demand session so a specific device gets a named quality tier for a bounded period.
api: openapi/nokia-network-as-code-platform-openapi.json
generated: '2026-07-25'
method: generated
operations:
  - createSession-QoD-V1
  - getSession-QoD-V1
  - retrieveSessions-QoD-V1
  - extendQosSessionDuration-QoD-V1
  - deleteSession-QoD-V1
---

# Boost network quality for a session

Quality on Demand is the capability that made this category interesting: for a bounded window, a named device gets a named quality tier — the live-video upload that must not stall, the remote-control session that must not jitter. It is also the most consequential thing in the catalogue, because it changes the network's behaviour rather than reading it.

## Before you start

- Base URL `https://network-as-code.p-eu.rapidapi.com`, `x-rapidapi-key` on every request.
- Two generations run side by side. Use the v1 operations (`/quality-on-demand/v1/...`); `/qod/v0/sessions` is the older surface and is *not* flagged deprecated in the contract, so pin v1 explicitly.
- QoS profiles are **operator-defined**. The profile names that work on one network are not guaranteed on another. Do not hard-code a profile across markets.
- Sessions are billable and time-bounded. Always set the shortest duration that does the job.

## Steps

1. **Create the session.** `createSession-QoD-V1` (`POST /quality-on-demand/v1/sessions`) with the device, the QoS profile, the duration and, where the flow needs them, the application server ports. Returns a `sessionId` and a status. Some responses come back `202 Accepted` — the session is being established, not yet established.
2. **Reconcile before you retry.** There is no idempotency key. If the create call times out, call `retrieveSessions-QoD-V1` (`POST /quality-on-demand/v1/retrieve-sessions`) for the device and check whether a session already exists before creating another. Duplicate sessions cost money and may conflict.
3. **Poll or subscribe for status.** `getSession-QoD-V1` (`GET /quality-on-demand/v1/sessions/{sessionId}`) reads current state. The platform also emits `org.camaraproject.quality-on-demand.v1.qos-status-changed` CloudEvents to a sink — prefer the event over a polling loop where you already have a sink (`asyncapi/nokia-network-as-code-webhooks.yml`).
4. **Extend rather than recreate.** `extendQosSessionDuration-QoD-V1` (`POST /quality-on-demand/v1/sessions/{sessionId}/extend`) adds time to a live session. Recreating instead leaves an orphan.
5. **Always tear down.** `deleteSession-QoD-V1` (`DELETE /quality-on-demand/v1/sessions/{sessionId}`) in a `finally` block. A session left running keeps consuming the boost and the budget until its duration expires.

## Errors that matter

- `400 QUALITY_ON_DEMAND.DURATION_OUT_OF_RANGE` — the requested duration is outside what the operator permits.
- `400 OUT_OF_RANGE` — commonly an invalid port on the device public port range.
- `409 ABORTED` / `409 ALREADY_EXISTS` — a conflicting session exists for that device. Reconcile with `retrieveSessions-QoD-V1`.
- `422 SERVICE_NOT_APPLICABLE` — the serving operator does not offer QoD for this subscriber. Not retryable.
- `422 DEVICE_NOT_APPLICABLE` — the device cannot be given this profile.
- `404 IDENTIFIER_NOT_FOUND` — the session or device is unknown.
- `503 UNAVAILABLE` / `504 TIMEOUT` — the downstream operator network is unreachable. Back off; there is no `Retry-After` header.

## Do not

- Do not retry a create on timeout without reconciling — this is the single most expensive mistake available on this platform.
- Do not assume a `200` on create means the boost is active. Check the session status; `202` means in progress.
- Do not carry a profile name from one operator's market to another without checking it exists there.
