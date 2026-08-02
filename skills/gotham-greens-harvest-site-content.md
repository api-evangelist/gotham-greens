---
name: Harvest Gotham Greens site content
description: >-
  Mirror the corporate content of gothamgreens.com — company pages, media assets and the full
  Journal archive — into your own store, using the discovery routes first so the crawl is driven
  by what the site actually registers rather than by guesswork.
api: openapi/gotham-greens-discovery-api-openapi.yml
operations:
  - getRootIndex
  - listTypes
  - listTaxonomies
  - listPages
  - getPage
  - listPosts
  - listMedia
generated: '2026-08-01'
method: generated
---

# Harvest Gotham Greens site content

Use this when you need a local copy of what Gotham Greens publishes — company story, farm
locations, product pages, press, and the Journal archive — rather than a single lookup. Base URL:
`https://www.gothamgreens.com/wp-json`. No credential required.

## Step 1 — Ask the site what it has (`getRootIndex`, `listTypes`, `listTaxonomies`)

```
GET /
GET /wp/v2/types
GET /wp/v2/taxonomies
```

Do not hardcode the content model. `getRootIndex` returns the full route table with per-route
methods and argument schemas — 406 routes across 17 namespaces at time of writing, most of them
plugin and administrative surfaces you should ignore. `listTypes` tells you which content types
exist; on this site they are the WordPress defaults (`post`, `page`, `attachment`, plus block and
template internals) with **no custom public types**. `listTaxonomies` confirms `category` and
`post_tag` are the only classifications attached to `post`.

If a future crawl finds a new public type here, that is your signal the site has changed shape.

## Step 2 — Pull the pages (`listPages`, `getPage`)

```
GET /wp/v2/pages?per_page=100&_fields=id,slug,link,title,parent,menu_order,modified
```

This is the corporate spine: Our Story, Our Farms, Our Products, Find Us, FAQ, Careers, Recent
Press, Terms of Use, Privacy Policy. `parent` gives you the hierarchy (`0` = top level) and
`menu_order` the intended sequence, so you can rebuild the site tree without scraping navigation —
`/wp/v2/menus` is gated at `401` and will not help you.

Fetch bodies with `getPage` only for the pages you actually need; `content.rendered` is full HTML.

## Step 3 — Pull the Journal (`listPosts`)

```
GET /wp/v2/posts?per_page=100&page=N&orderby=date&order=asc&_fields=id,slug,link,title,excerpt,date,modified,categories,tags,featured_media
```

132 published posts at time of capture. Page through until `page > X-WP-TotalPages`. Sort ascending
by `date` so a resumed crawl is stable — new posts land at the end rather than shifting your pages.

**For incremental re-runs**, filter on `modified` rather than re-walking everything:

```
GET /wp/v2/posts?after=2026-07-01T00:00:00&orderby=modified&per_page=100
```

## Step 4 — Pull the media (`listMedia`)

```
GET /wp/v2/media?per_page=100&page=N&media_type=image&_fields=id,slug,source_url,alt_text,media_details,post,mime_type
```

`media_details.sizes` carries every generated variant with its own URL, width and height — mirror
the size you need, not the original. `post` links an attachment back to the post or page it was
uploaded to; it is `null` for unattached library items.

## Rate and etiquette rules

- Cap concurrency at 1–2 and pause between pages. `robots.txt` requests `Crawl-delay: 10`, and no
  rate-limit headers exist to tell you when you have gone too far.
- Responses carry `Cache-Control: max-age=600`. Re-crawling faster than ten minutes gains nothing.
- There is no `ETag` or `Last-Modified`, so conditional requests are unavailable — use the
  `modified` field as your change key instead.
- `X-Robots-Tag: noindex` is set on API responses. The content is public but the provider does not
  intend it to be indexed as an API; respect their Terms of Use
  (https://www.gothamgreens.com/terms-of-use/) when republishing.

## Failure handling

- `400 rest_invalid_param` — you exceeded `per_page=100` or sent a malformed date. Read
  `data.params`.
- `401` on `/wp/v2/users`, `/wp/v2/settings`, `/wp/v2/themes`, `/wp/v2/plugins`, `/wp/v2/menus` —
  expected and permanent. These are administrative. Skip them; do not retry with credentials.
- `404 rest_no_route` — the route moved or the plugin providing it was removed. Re-run step 1.
