---
name: Run KYC checks against operator-held subscriber data
description: Match, age-verify, tenure-check or pre-fill customer identity data against what the mobile operator already holds, using the CAMARA KYC family through Nokia Network as Code.
api: openapi/nokia-network-as-code-platform-openapi.json
generated: '2026-07-25'
method: generated
operations:
  - verifyKYCMatch
  - verifyAge
  - checkTenure
  - kycFillIn
  - retrieveStatus
scopes:
  - kyc-match:match
  - kyc-age-verification:verify
  - kyc-tenure:check-tenure
  - kyc-fill-in:set-all
  - consent-info:retrieve
---

# Run KYC checks against operator-held subscriber data

The operator onboarded this subscriber with identity documents. These operations let you compare what your customer told you against what the operator already verified — without either party handing over a document image.

## Before you start

- Base URL `https://network-as-code.p-eu.rapidapi.com`, `x-rapidapi-key` on every request.
- This is the most consent-sensitive family in the catalogue and its scopes reflect that. `kyc-fill-in` alone declares **26 separate field-level scopes** — `kyc-fill-in:givenName`, `:familyName`, `:birthdate`, `:address`, `:idDocument`, `:nationality` and so on, plus `kyc-fill-in:set-all`. Request the individual fields you need, not `set-all`, unless you can defend the whole set.
- Prefer three-legged (subscriber-consented) tokens throughout. Full scope list in `scopes/nokia-network-as-code-scopes.yml`.

## Steps

1. **Check what you are allowed to ask.** `retrieveStatus` (`POST /passthrough/camara/v1/consent-info/consent-info/v0.1/retrieve`) returns the consent state for the subscriber. Call it before an ambitious ask rather than discovering the limit through a `403`.
2. **Match rather than retrieve, wherever you can.** `verifyKYCMatch` (`POST /passthrough/camara/v1/kyc-match/kyc-match/v0.3/match`) takes the attributes your customer gave you and returns per-attribute match verdicts. You learn whether the data agrees without receiving the operator's copy — the smallest possible data ask for the most common question.
3. **Age-verify without a birthdate.** `verifyAge` (`POST /passthrough/camara/v1/kyc-age-verification/kyc-age-verification/v0.1/verify`) answers "is this subscriber over N?" as a boolean. Use it for age gating instead of collecting a date of birth you then have to protect.
4. **Use tenure as a fraud signal.** `checkTenure` (`POST /passthrough/camara/v1/kyc-tenure/kyc-tenure/v0.1/check-tenure`) tells you how long the subscriber has held the line. A line opened last week behind a new high-value account is a different risk than one held for a decade.
5. **Pre-fill only at the end.** `kycFillIn` (`POST /passthrough/camara/v1/kyc-fill-in/kyc-fill-in/v0.4/fill-in`) returns operator-held attributes to populate a form. It is the largest data ask in the family — reach for it to reduce onboarding friction, not to build a profile.

## Errors that matter

- `400 KNOW_YOUR_CUSTOMER.INVALID_PARAM_COMBINATION` — the attribute combination you supplied is not a valid query.
- `403 KNOW_YOUR_CUSTOMER.ID_DOCUMENT_REQUIRED` — the check requires an ID document reference you did not supply.
- `403 KNOW_YOUR_CUSTOMER.ID_DOCUMENT_MISMATCH` — the document reference does not match the operator's record.
- `403 CONSENT_INFO.NOT_ALLOWED_SCOPES_PURPOSE` — the purpose registered with your credential does not cover this scope. This is a configuration problem, not a runtime one.
- `403 CONSENT_INFO.CAPTURE_FREQUENCY_EXCEEDED` — you are re-asking for consent too often.
- `422 SERVICE_NOT_APPLICABLE` — the serving operator does not expose KYC for this subscriber.
- `422 UNNECESSARY_IDENTIFIER` / `422 MISSING_IDENTIFIER` — token-type and identifier mismatch.

## Do not

- Do not request `kyc-fill-in:set-all` as a default. The field-level scopes exist so you can ask narrowly, and a regulator will read the scope you requested, not the field you used.
- Do not cache operator-held identity attributes beyond the purpose you registered.
- Do not treat a non-match as fraud. Name transliteration, married names and address staleness all produce honest mismatches — that is why `verifyKYCMatch` returns per-attribute verdicts rather than one boolean.
