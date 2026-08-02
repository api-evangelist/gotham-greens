---
name: Search Gotham Greens and build rich previews
description: >-
  Answer a question about Gotham Greens by searching its site content, then turn the winning URL
  into a rich preview using the oEmbed and Yoast SEO metadata endpoints — including the schema.org
  JSON-LD graph the site publishes.
api: openapi/gotham-greens-search-api-openapi.yml
operations:
  - searchContent
  - getPost
  - getPage
  - getOembed
  - getSeoHead
generated: '2026-08-01'
method: generated
---

# Search Gotham Greens and build rich previews

The shortest path from a natural-language question ("does Gotham Greens have a basil pesto
recipe?", "where are their greenhouses?") to a citable URL with a preview card. Base URL:
`https://www.gothamgreens.com/wp-json`. No credential required.

## Step 1 — Search (`searchContent`)

```
GET /wp/v2/search?search=basil%20pesto&per_page=10&subtype[]=post&subtype[]=page
```

Returns lightweight records only — `id`, `title` (plain text, already decoded), `url`, `type`,
`subtype`. That is deliberate: it is an index, not the content. Use `subtype` to steer the answer:
`post` for Journal recipes and stories, `page` for company facts like farm locations or the FAQ.

Pagination is the same page-number contract as everywhere else on this API, with `X-WP-Total` and
`X-WP-TotalPages` in the headers.

## Step 2 — Fetch the winner (`getPost` / `getPage`)

The result's `subtype` tells you which operation to call:

```
GET /wp/v2/posts/{id}      # subtype == post
GET /wp/v2/pages/{id}      # subtype == page
```

`content.rendered` is HTML — strip tags and decode entities before summarising. If you only need a
snippet, `excerpt.rendered` is already short.

## Step 3 — Build the preview (`getOembed`)

```
GET /oembed/1.0/embed?url={result.url}&maxwidth=600
```

Gotham Greens is an oEmbed 1.0 provider for its own URLs. You get `provider_name`, `title`,
`type: rich`, `width`/`height`, a `thumbnail_url` when the item has a featured image, and an
`html` blob containing a sandboxed iframe. Use the thumbnail and title for a card; only inject the
`html` if you genuinely intend to embed the live page, since it carries a script.

Non-gothamgreens.com URLs are rejected — this endpoint is not a general oEmbed proxy for you to
point at other sites.

## Step 4 — Get structured metadata (`getSeoHead`)

```
GET /yoast/v1/get_head?url={result.url}
```

The richest single call on this surface. `json` contains the canonical URL, description, Open
Graph and Twitter fields, **and** the schema.org JSON-LD `@graph` — `Organization`, `WebSite`,
`WebPage`, `Article`, `BreadcrumbList`. If you need structured facts about Gotham Greens as an
organisation (name, logo, social profiles), read them from that graph rather than parsing prose
off the homepage.

The same graph is available inline on any post, page, category or tag object as `yoast_head_json`,
so if you already fetched the object in step 2 you can skip this call entirely.

## Citing and caching

- Always cite the human `link` / `url`, never the `/wp-json/` path — the API output is served
  `X-Robots-Tag: noindex` and is not the canonical reader-facing address.
- Cache for at least the 600 seconds the `Cache-Control` header asks for.
- If search returns nothing, say so. Do not fall back to inventing product or farm details — this
  API is the only machine-readable source Gotham Greens publishes, and there is no product,
  inventory, store-locator or e-commerce API behind it.
