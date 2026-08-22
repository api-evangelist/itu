# ITU (itu)

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

The International Telecommunication Union (ITU) is the United Nations specialized agency for information and communication technologies, headquartered in Geneva and made up of 194 Member States plus roughly 1,000 sector members. It sits at the top of the global telecom value chain rather than inside it: ITU-R allocates the global radio spectrum and manages the master international frequency register and satellite filings, ITU-T publishes the Recommendations that define interoperable telecom networks, and ITU-D collects and publishes the world's reference ICT statistics. Its market is Global by treaty.

Its API posture is genuinely open but entirely undeclared. ITU operates real, anonymously callable HTTP APIs — one behind the DataHub statistics portal and one behind an ITU-D "Proximity to Fibre Node" demonstration built on the ITU Broadband Maps — and neither requires a key, a login, or a partner agreement. But there is no developer portal, no `api.itu.int`, no published OpenAPI, no SDK, no rate limits, no terms of programmatic use, and the official ITU GitHub organization has zero public repositories. A developer can reach ITU data today only by reverse-engineering ITU's own web client.

ITU is not a CAMARA participant and not a GSMA Open Gateway operator. Network APIs are being standardised outside the UN system, and ITU's role in that layer is limited to the IMT requirements framework rather than any callable interface.

**APIs.json:** [https://raw.githubusercontent.com/api-evangelist/itu/refs/heads/main/apis.yml](https://raw.githubusercontent.com/api-evangelist/itu/refs/heads/main/apis.yml)

## Tags

- Telecommunications
- Global
- Regulator
- Standards
- Spectrum
- Satellite
- Broadband
- ICT Statistics
- Open Data
- United Nations

## Timestamps

- **Created:** 2026-07-25
- **Modified:** 2026-07-25

## APIs

### ITU DataHub API

The undocumented JSON API that serves the ITU DataHub (datahub.itu.int), ITU's official ICT statistics portal and the successor to ICT Eye. Confirmed anonymously callable on 2026-07-25 with no key, header, or referer required. ITU publishes no documentation, no OpenAPI, and no terms of programmatic use for this surface; the endpoint list was recovered from the DataHub client bundle, so it should be treated as an internal backend that happens to be public rather than as a supported product API.

- **Human URL:** [https://datahub.itu.int/](https://datahub.itu.int/)
- **Base URL:** `https://api.datahub.itu.int/v2`

#### Tags

- ICT Statistics
- Open Data
- Indicators
- Connectivity
- Regulator

#### Properties

- [Documentation](https://datahub.itu.int/)
- [API Reference](https://datahub.itu.int/data)
- [Documentation](https://www.itu.int/itu-d/reports/statistics/)

### ITU Proximity to Fibre Node API

An ITU-D Technology and Network Development API that scores a batch of coordinates against the ITU Interactive Transmission Map (Broadband Maps) fibre network data. Two routes accept a POSTed `text/csv` body with `X` and `Y` columns and return a URL to a generated ZIP of results; both were confirmed returning HTTP 200 with real output on 2026-07-25. Access is by an Azure Functions function key that ITU embeds in the public web client, so it is effectively open; there is no signup, no OpenAPI, and no rate limit published.

- **Human URL:** [https://www.itu.int/en/ITU-D/Technology/Pages/EmergingTechnologiesPages/api.aspx](https://www.itu.int/en/ITU-D/Technology/Pages/EmergingTechnologiesPages/api.aspx)
- **Base URL:** `https://bbmaps.itu.int/functionproximity`

#### Tags

- Broadband
- Fibre
- Geospatial
- Connectivity
- Open Data

#### Properties

- [Documentation](https://www.itu.int/en/ITU-D/Technology/Pages/EmergingTechnologiesPages/api.aspx)
- [API Reference](https://www.itu.int/en/ITU-D/Technology/Pages/EmergingTechnologiesPages/api.aspx)
- [Documentation](https://www.itu.int/en/ITU-D/Technology/Pages/InteractiveTransmissionMaps.aspx)

## Common Properties

- [Website](https://www.itu.int/)
- [Documentation](https://www.itu.int/en/ITU-T/publications/Pages/recs.aspx)
- [GitHub Organization](https://github.com/ITUINT)
- [LinkedIn](https://www.linkedin.com/company/itu)
- [Blog](https://www.itu.int/hub/)

## Review

See [review.yml](review.yml) for the full API Evangelist reviewer findings — developer portal probes and HTTP statuses, CAMARA and GSMA Open Gateway posture, TM Forum conformance, 3GPP exposure, auth model, and the confirmed endpoint list.

## Maintainers

- Kin Lane — kin@apievangelist.com
