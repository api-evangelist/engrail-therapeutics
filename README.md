# Engrail Therapeutics

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
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

Engrail Therapeutics is a clinical-stage precision-neuroscience pharmaceutical company founded in
2019 and headquartered in San Diego, California. It develops targeted small-molecule therapies for
neuropsychiatric and neurodevelopmental diseases with significant unmet need — generalized anxiety
disorder, major depressive disorder characterized by anhedonia, PTSD and rare neurodegenerative
conditions. Lead program ENX-102, a selective GABA-A alpha-2,3,5 positive allosteric modulator, is in
Phase 2 (the ENCALM trial) for generalized anxiety disorder; ENX-104 is in clinical development for
anhedonic depression. The company closed an oversubscribed $157M Series B in March 2024 co-led by
F-Prime Capital, Forbion and Norwest Venture Partners, taking total capital raised past $220M.

- https://www.engrail.com/
- https://forgeglobal.com/engrail-therapeutics_stock/

## API surface

**Engrail publishes no product API and no developer program** — no portal, documentation, SDK, CLI,
changelog, status page, Postman collection, or provider-authored OpenAPI. Contract discovery was run
against `www.engrail.com`, `engrail.com`, `api.engrail.com`, `docs.engrail.com` and
`developer.engrail.com`; the three subdomains do not resolve.

Two machine-readable surfaces do exist on the corporate website:

1. **A public WordPress REST content API** at `https://www.engrail.com/wp-json/wp/v2` — 16
   anonymously-readable operations over posts, pages, media, taxonomy and site search.
   `openapi/engrail-therapeutics-content-openapi.yml` is an OpenAPI 3.1 **derivation** of the host's
   own route-discovery document, not a provider artifact.
2. **Two OAuth-protected Model Context Protocol servers** under the `mcp` REST namespace, fronted by
   RFC 8414 and RFC 9728 discovery documents served at the apex. Anonymous `tools/list` returns HTTP
   401 on both, so no tool list is recorded — it was not guessed.

No A2A agent card exists at either the canonical or legacy well-known path, so no `a2a/` artifact was
written.

## Artifacts

| Path | What it holds |
|---|---|
| `openapi/` | Derived OpenAPI 3.1, 16 operations |
| `overlays/` | API Evangelist enhancements and provenance |
| `mcp/` | Both MCP servers, their auth model, recorded 401 probes, and the tool crosswalk |
| `well-known/` | OAuth AS + protected-resource metadata (verbatim) and the full probe index |
| `authentication/` | Application Passwords (content) and OAuth 2.1 + PKCE (MCP) |
| `scopes/` | The single `mcp` scope |
| `conventions/` | Pagination, filtering, field selection, versioning, error envelope, rate-limit signalling |
| `errors/` | Five error codes captured live; WordPress envelope, not RFC 9457 |
| `data-model/` | Entity graph derived from id-reference parameters |
| `conformance/` | Standards asserted with evidence, and the real absences |
| `lifecycle/` | Versioning namespaces; no deprecation policy, SLA or status page |
| `security/` | TLS 1.3, HSTS, SPF, DMARC (quarantine); no DNSSEC, no CAA |
| `skills/` | Two agent skills grounded in real operationIds |
| `llms/` | Generated llms.txt (the provider serves none) |
