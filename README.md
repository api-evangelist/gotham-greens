# Gotham Greens

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
