# Gotham Greens

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

Gotham Greens is an American fresh food and indoor farming company, founded in 2009 by Viraj Puri
and Eric Haley in Brooklyn, New York, where it built one of the first commercial-scale rooftop
hydroponic greenhouses in the world. It operates a national network of climate-controlled
hydroponic greenhouses across the United States, growing pesticide-free leafy greens, lettuces and
herbs year-round, and sells packaged salad greens, salad kits, herbs, dressings, dips, pestos and
cooking sauces under its own brand.

- https://www.gothamgreens.com/
- Secondary-market listing: https://forgeglobal.com/gotham-greens_stock/

## API surface

Gotham Greens is a CPG and controlled-environment agriculture business, not a software vendor. It
publishes **no developer program**: no API documentation, no SDKs, no CLI, no sandbox, no MCP
server, no A2A agent card, no webhooks or events, and no `/.well-known/` documents. There is no
`api.`, `developer.` or `docs.` subdomain (all NXDOMAIN).

What it *does* expose is the platform-provided **WordPress REST API** at
`https://www.gothamgreens.com/wp-json`, whose read half is fully public. The live route index
advertises 406 routes across 17 namespaces; 20 operations were verified to return HTTP 200 to an
anonymous request and are documented here across eight derived OpenAPI 3.1 specifications:

| API | Operations |
|---|---|
| Journal Posts | `listPosts`, `getPost` |
| Pages | `listPages`, `getPage` |
| Media | `listMedia`, `getMediaItem` |
| Taxonomy | `listCategories`, `getCategory`, `listTags`, `getTag` |
| Search | `searchContent` |
| Discovery | `getRootIndex`, `getNamespaceIndex`, `listTypes`, `getType`, `listTaxonomies`, `getTaxonomy`, `listStatuses` |
| oEmbed | `getOembed` |
| SEO Metadata | `getSeoHead` |

Administrative routes (`users`, `settings`, `themes`, `plugins`, `menus`, `block-types`,
revisions) return 401 anonymously and are deliberately excluded — see
`authentication/gotham-greens-authentication.yml` for the verified public/private split.

**The specifications in `openapi/` were derived by API Evangelist from the live route index, not
published by Gotham Greens.** This is an incidental CMS surface with no versioning policy, no
deprecation policy, no SLA, no status page and no support channel for API consumers.

## Artifacts

`openapi/` · `overlays/` · `authentication/` · `conventions/` · `errors/` · `data-model/` ·
`conformance/` · `lifecycle/` · `examples/` · `well-known/` · `security/` · `llms/` · `mcp/`
(candidate) · `skills/` · `agentic-access/`
