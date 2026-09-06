# Accruent

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

Accruent is a workplace, facilities and asset management software company serving more than 10,000
customers in over 150 countries. Its portfolio spans CMMS/EAM (Maintenance Connection), engineering
document management (Meridian), IWMS and lease accounting (Lucernex), space and event scheduling
(EMS), telecom and wireless site management (Siterra), and IoT condition monitoring (Observe).

## What this profile found

Accruent runs an Azure API Management developer network at
[developer.accruent.com](https://developer.accruent.com/), but its anonymous API catalogue exposes
only the stock Azure "Echo API" sample — every real product API sits behind developer-program
approval or a customer tenancy.

One contract is published without a login. The **Maintenance Connection Web API** serves a
Swagger 2.0 document at `https://api.maintenanceconnection.com/v8/swagger/docs/v8` — 126 paths and
255 operations across assets, work orders, parts and inventory, purchasing, labor and lookup
tables. It is captured verbatim in `openapi/`, and every derived artifact in this repository is
built from it.

Two things about that API matter more than its size:

- **Rate-limit exhaustion returns `403`, not `429`,** with no `Retry-After` and no `RateLimit-*`
  headers — 200 requests per minute, discoverable only from a prose table in the docs.
- **Nothing written through it can be taken back.** There is no `DELETE` operation on any of the
  126 paths, no cancel or void, no idempotency key, and both status-update surfaces are read-only.

## Surfaces

| Surface | State |
|---|---|
| Maintenance Connection Web API | Swagger 2.0 published, captured in `openapi/` |
| Siterra API | Documented; definition behind Accruent Developer Program approval |
| Meridian Cloud API | Documented; definition behind a regional APIM portal sign-in |
| EMS Platform Services | Documented; Swagger ships inside the customer deployment |
| Lucernex REST API | Documented; schema browser served from the customer tenancy |
| EMS SOAP API Web Service | Accruent states it is no longer being developed |

Accruent publishes no `/.well-known/` surface of any kind (13 named paths probed on 8 hosts, all
404), no A2A agent card, no MCP server, no first-party API SDK, no webhooks or AsyncAPI, no API
deprecation policy or changelog, and no vulnerability disclosure programme. It does publish a live
StatusHub status page, a Vanta trust centre, ISO/IEC 27001 / SOC 1 / SOC 2 certifications, and
indicative list pricing for three of its products.

- Website: https://www.accruent.com/
- Developer network: https://developer.accruent.com/
- Documentation: https://help.accruent.com/
