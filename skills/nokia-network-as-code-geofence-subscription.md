---
name: Subscribe to geofence entry and exit events
description: Create a CAMARA geofencing subscription that delivers CloudEvents to your own sink when a device enters or leaves an area, and manage its full lifecycle.
api: openapi/nokia-network-as-code-platform-openapi.json
generated: '2026-07-25'
method: generated
operations:
  - createSubscription
  - retrieveGeofencingSubscription
  - retrieveGeofencingSubscriptionList
  - deleteGeofencingSubscription
scopes:
  - geofencing-subscriptions:org.camaraproject.geofencing-subscriptions.v0.area-entered:create
  - geofencing-subscriptions:org.camaraproject.geofencing-subscriptions.v0.area-left:create
  - geofencing-subscriptions:read
  - geofencing-subscriptions:delete
---

# Subscribe to geofence entry and exit events

This is the platform's asynchronous shape, and the one place its consent model is genuinely fine-grained: the scope you need names the exact CloudEvent type you want. Asking for `area-entered` does not get you `area-left`.

## Before you start

- Base URL `https://network-as-code.p-eu.rapidapi.com`, `x-rapidapi-key` on every request.
- You must host the sink. Nokia POSTs CloudEvents to a URL you supply and authenticates to it with a `sinkCredential` you also supply (`credentialType: ACCESSTOKEN`). There is **no published request-signing scheme**, so your sink's only proof of origin is the credential it issued — treat that credential as a secret and validate it on every delivery.
- Event types available on this surface: `org.camaraproject.geofencing-subscriptions.v0.area-entered`, `...v0.area-left`, and the lifecycle event `...v0.subscription-ends`. Full catalogue in `asyncapi/nokia-network-as-code-webhooks.yml`.

## Steps

1. **Stand up the sink first.** It must accept a CloudEvents POST, verify the credential, return quickly, and be idempotent — you may receive the same event more than once and there is no delivery-guarantee statement in the contract.
2. **Create the subscription.** `createSubscription` (`POST /geofencing-subscriptions/v0.3/subscriptions`) with the device, the `Area` (a `Circle`: centre `Point` plus radius), the event types you want, your `sink` URL, the `sinkCredential`, and an expiry. Request only the event types you will act on — each one is a separate consent.
3. **Record the returned `subscriptionId`.** There is no idempotency key on this platform. If the create call times out, call `retrieveGeofencingSubscriptionList` (`GET /geofencing-subscriptions/v0.3/subscriptions`) and reconcile **before** retrying, or you will create a duplicate that bills and fires twice.
4. **Handle `subscription-ends`.** Subscriptions expire. Treat the `subscription-ends` CloudEvent as an operational signal and re-subscribe if you still need the feed — do not assume a standing stream.
5. **Inspect and clean up.** `retrieveGeofencingSubscription` (`GET /geofencing-subscriptions/v0.3/subscriptions/{subscriptionId}`) reads one; `deleteGeofencingSubscription` (`DELETE` on the same path) tears it down. Delete subscriptions you no longer consume — there is no pagination on the list operation (`conventions/nokia-network-as-code-conventions.yml`), so an accumulated backlog becomes genuinely hard to audit.

## Errors that matter

- `400 INVALID_SINK` — your sink URL is malformed or not reachable.
- `400 INVALID_CREDENTIAL` / `400 INVALID_TOKEN` — the `sinkCredential` was rejected.
- `400 INVALID_PROTOCOL` — the sink protocol is not supported (HTTP POST is expected).
- `422 MULTIEVENT_SUBSCRIPTION_NOT_SUPPORTED` — you asked for several event types in one subscription where the operator only supports one. Split it.
- `409 ALREADY_EXISTS` / `409 CONFLICT` — an equivalent subscription is already live. Reconcile rather than force.
- `403 SUBSCRIPTION_MISMATCH` — the subscription does not belong to the caller.
- `422 SERVICE_NOT_APPLICABLE` — the serving operator does not offer geofencing for this subscriber.

## Do not

- Do not point a subscription at a sink you do not control.
- Do not request event types you have no purpose for; the scope names them individually precisely so you can ask narrowly.
