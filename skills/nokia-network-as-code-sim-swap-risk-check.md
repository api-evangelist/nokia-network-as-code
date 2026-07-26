---
name: Check SIM swap and device swap risk before a high-value action
description: Ask the mobile network whether the SIM or the handset behind a phone number changed recently, and gate a payout, password reset or high-value transaction on the answer.
api: openapi/nokia-network-as-code-platform-openapi.json
generated: '2026-07-25'
method: generated
operations:
  - checkSimSwap
  - retrieveSimSwapDate
  - checkDeviceSwap
  - retrieveDeviceSwapDate
  - checkNumberRecycling
scopes:
  - sim-swap:check
  - sim-swap:retrieve-date
  - device-swap:check
  - device-swap:retrieve-date
  - number-recycling:check
---

# Check SIM swap and device swap risk

An attacker who has ported a victim's number onto their own SIM passes every SMS-based check you have. These operations let you ask the network the one question SMS cannot answer: *did this number move recently?*

## Before you start

- Base URL `https://network-as-code.p-eu.rapidapi.com`, `x-rapidapi-key` on every request.
- These work with a **two-legged** token, which matters: you are checking a number your risk engine already holds, not a subscriber who is present to consent. With a two-legged token you MUST put the `phoneNumber` in the request body.
- Decide the age threshold up front. The check operations take a max-age window and answer yes/no against it; the retrieve-date operations return the actual timestamp.

## Steps

1. **Ask the yes/no question first.** `checkSimSwap` (`POST /passthrough/camara/v1/sim-swap/sim-swap/v0/check`) with the number and your window (e.g. 240 hours). A `true` result means the SIM changed inside the window.
2. **Get the timestamp only when you need to reason about it.** `retrieveSimSwapDate` (`POST /passthrough/camara/v1/sim-swap/sim-swap/v0/retrieve-date`) returns the last SIM change date. Prefer step 1 where a boolean will do — it is the smaller data ask and easier to justify to a DPO.
3. **Add the handset dimension.** `checkDeviceSwap` (`POST /passthrough/camara/v1/device-swap/device-swap/v1/check`) and `retrieveDeviceSwapDate` answer the same question about the physical device. A SIM that did not move but a handset that did is a different risk story from both moving together.
4. **Guard against recycled numbers.** `checkNumberRecycling` (`POST /passthrough/camara/v1/number-recycling/number-recycling/v0.2/check`) tells you whether the MSISDN has been reassigned to a new subscriber — the failure mode that silently sends a returning customer's account to a stranger.
5. **Score, do not gate hard.** Combine the answers into your existing risk score. A recent swap should raise friction (step-up auth, hold, manual review), not necessarily block.

## Errors that matter

- `422 MISSING_IDENTIFIER` — two-legged token with no `phoneNumber` in the body.
- `422 UNNECESSARY_IDENTIFIER` — you used a three-legged token and repeated the identifier.
- `422 SERVICE_NOT_APPLICABLE` — the serving operator does not expose SIM Swap for this subscriber. Not retryable; degrade to your existing signals and record that you had no network answer.
- `404 IDENTIFIER_NOT_FOUND` — the number is not on a network Nokia reaches.
- `403 PERMISSION_DENIED` — your token lacks the scope, or the purpose you registered does not cover fraud prevention.

## Do not

- Do not cache a swap answer across a risk decision boundary. The value is its freshness.
- Do not infer "no swap" from an error. `SERVICE_NOT_APPLICABLE` and `IDENTIFIER_NOT_FOUND` mean *unknown*, and treating unknown as safe is exactly the gap an attacker will find.
