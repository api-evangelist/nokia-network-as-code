# Nokia Network as Code (nokia-network-as-code)

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **Not from the company, and here with a question?** You are welcome here — we would rather be the
> front line and point you the right way than have a good report go nowhere. What this repository
> can answer is narrow, though, so it is worth knowing who you are actually looking for:
>
> - **A question about how the API works, an account, billing, or a bug in the service** — that is
>   the company's own support, not us. We profile this API; we do not operate it and cannot see
>   your account.
> - **A bug in an open-source project we only catalog** — file it on that project's own repository.
>   This has happened with a real and correct bug report that reached us instead of the people who
>   could fix it, which helped nobody.
> - **Anything about this listing itself** — the description, the tags, the rating, a missing or
>   wrong artifact — is ours. Open an issue here.
> - **Not sure, or something general about API Evangelist or APIs.io** — open an issue on the
>   [APIs.io Inbox](https://github.com/api-search/inbox) and we will route it.
>
> **This repository contains no software, and we will never ask you to download anything.** There is
> no build, release, installer, or binary here — only text and machine-readable API descriptions, so
> there is nothing here that can be "corrupt" or need "repairing". Any issue, comment, or email
> claiming otherwise and offering a download link is not from us and is hostile. Do not follow the
> link; it is a lure. Report it to GitHub and, if you like, tell us at
> [info@apievangelist.com](mailto:info@apievangelist.com) so we can take it down.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

Nokia Network as Code is the network-API exposure and aggregation platform operated by Nokia Oyj from Espoo, Finland, launched in September 2023 to make CAMARA-standardised mobile network capabilities commercially reachable by ordinary application developers. Beneath it, Nokia's Network Exposure Platform (NEP) is deployed inside an operator's core to expose that operator's network; above it, the Network as Code developer platform aggregates many operators behind one contract, one credential and one set of SDKs.

**APIs.json:** [https://raw.githubusercontent.com/api-evangelist/nokia-network-as-code/refs/heads/main/apis.yml](https://raw.githubusercontent.com/api-evangelist/nokia-network-as-code/refs/heads/main/apis.yml)

## Tags

- Telecommunications
- Finland
- Network APIs
- CAMARA
- Open Gateway
- Network API Exposure
- Network API Aggregator
- 5G
- Identity Verification
- SIM Swap
- Number Verification
- Device Location
- Quality on Demand
- Network Slicing
- Anti-Fraud
- KYC
- IoT
- eSIM
- Roaming
- Network Exposure

## Timestamps

- **Created:** 2026-07-25
- **Modified:** 2026-07-25

## APIs

### Nokia Network as Code Platform API

The single aggregated Network as Code API surface published by Nokia as one OpenAPI 3.0 document — 58 paths and 78 operations spanning device status, location, geofencing, quality on demand, network slicing, congestion insights, OAuth2 and a `/passthrough/camara/v1/*` namespace that proxies CAMARA identity and anti-fraud APIs.

- **Human URL:** [https://networkascode.nokia.io/docs](https://networkascode.nokia.io/docs)
- **Base URL:** `https://network-as-code.p-eu.rapidapi.com`

#### Properties

- [OpenAPI](openapi/nokia-network-as-code-platform-openapi.json)
- [Documentation](https://networkascode.nokia.io/docs)
- [Getting Started](https://networkascode.nokia.io/docs/getting-started)
- [Source Code](https://github.com/nokia/network-as-code-sdks)

### Nokia Network as Code CAMARA API

Nokia's consolidated CAMARA-conformant network API specification — OpenAPI 3.0.3, 52 paths and 70 operations, declaring `x-camara-commonalities: 0.4.0`, with `oAuth2ClientCredentials` as the global security requirement.

- **Human URL:** [https://github.com/nokia/network-as-code-sdks](https://github.com/nokia/network-as-code-sdks)

#### Properties

- [OpenAPI](openapi/nokia-network-as-code-camara-openapi.yml)
- [Source Code](https://github.com/nokia/network-as-code-sdks)

### Network as Code Digital Identity and Anti-Fraud APIs

Call Forwarding Signal, Device Swap, KYC Age Verification, KYC Fill-in, KYC Match, Location Verification, Number Verification and SIM Swap — all present as real paths in the harvested specification.

- **Human URL:** [https://networkascode.nokia.io/products/digital-identity-and-anti-fraud](https://networkascode.nokia.io/products/digital-identity-and-anti-fraud)

### Network as Code Device Intelligence APIs

Device Reachability Status, Device Roaming Status, their subscription variants, Geofencing Subscriptions and Location Retrieval.

- **Human URL:** [https://networkascode.nokia.io/products/device-intelligence](https://networkascode.nokia.io/products/device-intelligence)

### Network as Code Programmable Connectivity APIs

Quality on Demand, Specialized Networks / Network Slice Management, Network Slice Device Attachment and eSIM Provisioning. QoD, slicing and device attach are callable; no eSIM path appears in the published specification.

- **Human URL:** [https://networkascode.nokia.io/products/programmable-connectivity](https://networkascode.nokia.io/products/programmable-connectivity)

### Network as Code Network Intelligence APIs

Congestion Insights and Consent Info are callable. Population Density Data and Network Aware Route Optimization are advertised capabilities with no published contract.

- **Human URL:** [https://networkascode.nokia.io/products/network-intelligence](https://networkascode.nokia.io/products/network-intelligence)

### Network as Code Authorization Server

OAuth2 / OpenID Connect surface — `GET /oauth2/v1/auth/clientcredentials`, per-app JWKS, and the OIDC / OAuth authorization-server well-known documents (served behind the gateway; not anonymously retrievable).

- **Human URL:** [https://networkascode.nokia.io/docs/getting-started](https://networkascode.nokia.io/docs/getting-started)

## CAMARA posture

Direct CAMARA implementer and multi-operator aggregator with callable, publicly specified APIs — not a press release. Seventeen CAMARA API families are present as real paths in the harvested specification: Number Verification, SIM Swap, Device Swap, Device Location Retrieval, Location Verification, Geofencing Subscriptions, Device Status (Reachability and Roaming), Quality on Demand, KYC Match, KYC Age Verification, KYC Fill-in, KYC Tenure, Call Forwarding Signal, Number Recycling, Consent Info and Congestion Insights.

Nokia is a GSMA Open Gateway participant as a vendor and platform provider rather than an operator signatory — it is not itself a mobile network operator. It does **not** reach the market through Aduna; Network as Code is Nokia's own competing aggregation channel, with Google Cloud Marketplace as a secondary route.

## Honest read on the developer surface

- **Portal:** [https://networkascode.nokia.io/](https://networkascode.nokia.io/) — HTTP 200, a real self-serve portal with open signup and two public plans, `DEFAULT` and `SIMULATOR`.
- **Gated parts:** `/console` redirects to `/auth/login`; the in-portal spec viewer is switched off (`NEXT_PUBLIC_SHOW_SPECS: "false"`); live network access in any market depends on Nokia holding a commercial agreement with that market's operator.
- **Docs:** the documentation site is a client-rendered SPA that returns HTTP 200 for *any* `/docs/*` slug with a byte-comparable payload, so it is not machine readable.
- **Platform:** the whole developer platform is a white-labelled RapidAPI Enterprise Hub; production traffic is routed through `network-as-code.p-eu.rapidapi.com`, not a Nokia-owned API hostname.
- **Specs:** both OpenAPI documents were harvested from Nokia's public Apache-2.0 GitHub repository, not from the portal.
- **Auth:** two layers — an `x-rapidapi-key` gateway API key plus OAuth2 client-credentials / OIDC tokens. CAMARA two-legged and three-legged tokens are both described; **CIBA does not appear** in either specification.
- **Webhooks:** CAMARA `sink` + `sinkCredential` subscriptions delivering CloudEvents. No AsyncAPI is published.
- **SDKs:** `network_as_code` on PyPI and `network-as-code` on npm, both at 10.0.0, generated with Fern.
- **MCP:** `https://mcp.prodeu.apihub.nokia.io` is live (returns a version document) but no MCP transport path is anonymously reachable.

## Links

- **Portal:** https://networkascode.nokia.io/
- **Documentation:** https://networkascode.nokia.io/docs
- **Getting Started:** https://networkascode.nokia.io/docs/getting-started
- **MCP Server:** https://networkascode.nokia.io/docs/mcp-server
- **Sign Up:** https://networkascode.nokia.io/auth/sign-up
- **Blog:** https://networkascode.nokia.io/blog
- **Source Code / OpenAPI:** https://github.com/nokia/network-as-code-sdks
- **Python SDK:** https://pypi.org/project/network-as-code/
- **Product page:** https://www.nokia.com/programmable-networks/network-as-code/
