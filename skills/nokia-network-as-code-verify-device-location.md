---
name: Verify or retrieve a device's location from the network
description: Confirm that a device is inside a claimed area, or retrieve its network-derived location, using CAMARA Location Verification and Location Retrieval through Nokia Network as Code.
api: openapi/nokia-network-as-code-platform-openapi.json
generated: '2026-07-25'
method: generated
operations:
  - verifyLocation
  - verifyLocation-LocV-V0
  - retrieveLocation
scopes:
  - location-verification:verify
  - location-retrieval:read
---

# Verify or retrieve a device's location

Network-derived location is not GPS. It cannot be spoofed by a rooted handset or a VPN, it does not need an app in the foreground, and it is coarse. Use it to answer "is this plausible?", not "where exactly is this?".

## Before you start

- Base URL `https://network-as-code.p-eu.rapidapi.com`, `x-rapidapi-key` on every request.
- Two generations are live: `verifyLocation` (`POST /location-verification/v1/verify`) is v1; `verifyLocation-LocV-V0` (`POST /location-verification/v0/verify`) is the older one. Neither is flagged deprecated in the contract — see `lifecycle/nokia-network-as-code-lifecycle.yml` — so pin v1 deliberately.
- Location is consent-heavy. Prefer a three-legged token; with a two-legged token the identifier goes in the body.

## Steps

1. **Prefer verification over retrieval.** `verifyLocation` takes a device and an `Area` (a `Circle` with a centre `Point` and a radius) and returns a match verdict — `TRUE`, `FALSE` or a partial/unknown result — without ever handing you coordinates. This is the shape to reach for: it answers the fraud question and minimises the data you hold.
2. **Set `maxAge` honestly.** You are asking how fresh the network's fix must be. Too tight and you get `422 LOCATION_VERIFICATION.UNABLE_TO_FULFILL_MAX_AGE`; too loose and the answer is not evidence of anything.
3. **Retrieve coordinates only with a reason.** `retrieveLocation` (`POST /location-retrieval/v0/retrieve`) returns the network's location estimate with its own accuracy radius. Record the purpose you registered; this is the operation a regulator will ask about.
4. **Read the accuracy, not just the point.** A retrieved location comes with an uncertainty radius. A decision that would flip if the radius were doubled is a decision this API should not be making.

## Errors that matter

- `422 AREA_NOT_COVERED` — the area you asked about is outside the serving network's coverage.
- `422 POI_NOT_SUPPORTED` — the area shape you supplied is not supported; use a `Circle`.
- `422 LOCATION_VERIFICATION.UNABLE_TO_FULFILL_MAX_AGE` — the network has no fix fresh enough. Widen `maxAge` or accept an unknown.
- `400 LOCATION_RETRIEVAL.MAXAGE_INVALID_ARGUMENT` — the `maxAge` value itself is out of range.
- `422 SERVICE_NOT_APPLICABLE` — the serving operator does not expose location for this subscriber.
- `422 UNNECESSARY_IDENTIFIER` / `422 MISSING_IDENTIFIER` — the token/identifier mismatch described in `conventions/nokia-network-as-code-conventions.yml`.

## Do not

- Do not treat a `FALSE` verdict as proof of fraud on its own. Roaming, network handover and coarse cell geometry all produce honest false negatives.
- Do not store retrieved coordinates longer than the purpose you registered requires.
