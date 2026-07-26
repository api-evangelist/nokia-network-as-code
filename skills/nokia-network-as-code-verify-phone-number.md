---
name: Verify a phone number against the mobile network (silent network auth)
description: Replace an SMS OTP with a network-verified check that the device on the call is really the claimed phone number, using CAMARA Number Verification through Nokia Network as Code.
api: openapi/nokia-network-as-code-platform-openapi.json
generated: '2026-07-25'
method: generated
operations:
  - get_client_credentials
  - phoneNumberVerify-NV-V2
  - phoneNumberShare-NV-V2
scopes:
  - number-verification:verify
  - number-verification:device-phone-number:read
---

# Verify a phone number against the mobile network

This is the flow Deutsche Telekom put into production to replace OTPs, and the one Philippine banks use through Globe's G Verify. The network already knows which MSISDN is behind the data session; you are asking it to confirm, not asking the user to retype a code.

## Before you start

- Base URL is `https://network-as-code.p-eu.rapidapi.com`. Every request carries `x-rapidapi-key`.
- Number Verification is a **three-legged** capability. The subscriber must consent, and the token carries the identity. Get the token through the subscriber-consented flow; `get_client_credentials` (`GET /oauth2/v1/auth/clientcredentials`) issues the application credentials you exchange from.
- The call must traverse the **mobile data network**, not Wi-Fi. If the handset is on Wi-Fi the network cannot see the session and you will get `403 NUMBER_VERIFICATION.USER_NOT_AUTHENTICATED_BY_MOBILE_NETWORK`.
- Two generations are live. Use `phoneNumberVerify-NV-V2` (`POST /passthrough/camara/v1/number-verification/number-verification/v2/verify`) for new work; `phoneNumberVerify` (v0) still exists for compatibility.
- Send `x-correlator` on every request and log the echoed value.

## Steps

1. **Confirm a claimed number.** Call `phoneNumberVerify-NV-V2` with the number the user typed, in plain E.164 or as its hash. The response is a boolean match — it never returns the real number, so this is the privacy-preserving shape and the one to prefer.
2. **Or read the number you were not told.** Call `phoneNumberShare-NV-V2` (`GET /passthrough/camara/v1/number-verification/number-verification/v2/device-phone-number`) to have the network return the MSISDN associated with the access token. Only do this when you genuinely need the value; it is a larger consent ask.
3. **Branch on the result, not on the HTTP status.** A `200` with a false match is a legitimate answer meaning "this device is not that number" — treat it as a failed verification, not an error.

## Errors that matter

- `422 UNNECESSARY_IDENTIFIER` — you sent a `phoneNumber` in the body while holding a three-legged token. Remove it; the token already identifies the subscriber.
- `422 MISSING_IDENTIFIER` — the mirror image: a two-legged token with no identifier in the body.
- `403 NUMBER_VERIFICATION.USER_NOT_AUTHENTICATED_BY_MOBILE_NETWORK` — the session is not on the mobile network. Prompt the user to disable Wi-Fi and retry, or fall back to your existing factor.
- `422 SERVICE_NOT_APPLICABLE` — the serving operator does not offer Number Verification for this subscriber. This is the aggregator boundary. Do not retry; fall back.
- `429 TOO_MANY_REQUESTS` / `429 QUOTA_EXCEEDED` — no `Retry-After` header is published; back off exponentially on your own schedule.

Full catalogue: `errors/nokia-network-as-code-problem-types.yml`.

## Do not

- Do not retry a verification blindly. There is no idempotency key on this platform (`conventions/nokia-network-as-code-conventions.yml`), and repeated verification attempts are a fraud signal in their own right.
- Do not treat coverage as global. Verification only works where Nokia holds an agreement with the operator serving that subscriber.
