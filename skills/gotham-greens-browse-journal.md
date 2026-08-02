---
name: Browse the Gotham Greens Journal
description: >-
  Find and read Gotham Greens Journal content — recipes, seasonal produce guides, sustainability
  stories and company news — from the public WordPress content API, resolving categories, tags and
  featured images correctly.
api: openapi/gotham-greens-posts-api-openapi.yml
operations:
  - listCategories
  - listPosts
  - getPost
  - listTags
  - getMediaItem
generated: '2026-08-01'
method: generated
---

# Browse the Gotham Greens Journal

Gotham Greens is a hydroponic greenhouse produce company. Its Journal is the editorial side of
gothamgreens.com — recipes built around its lettuces, basil and dressings, plus farm and
sustainability stories. The whole thing is readable through the public WordPress REST API with
**no credential of any kind**. Base URL: `https://www.gothamgreens.com/wp-json`.

## Before you start

- **No auth.** Send no `Authorization` header. There is no key to obtain and no signup.
- **Be polite.** No rate limits are signalled, but `robots.txt` asks for a 10-second crawl delay
  and responses are cached for 600 seconds. Do not hammer this; it is a marketing site.
- **Trim the payload.** Every post carries a multi-kilobyte `yoast_head` string you almost never
  want. Always pass `_fields`.

## Step 1 — Learn the categories (`listCategories`)

```
GET /wp/v2/categories?per_page=100&_fields=id,name,slug,count,parent
```

You need this first because posts reference categories by numeric ID only. The live set is small
and hierarchical — `Recipes` (8) and `General` (1) are roots; `Basil Recipes` (1027) hangs off
Recipes, `For The Family` (1026) off General. `count` tells you how many published posts each
holds, which is a good sanity check before you paginate.

## Step 2 — List posts, filtered (`listPosts`)

```
GET /wp/v2/posts?categories=8&per_page=20&page=1&_fields=id,slug,link,title,excerpt,date,categories,tags,featured_media
```

- Filter with `categories`, `categories_exclude`, `tags`, `slug`, `after`/`before` (ISO 8601),
  `search`.
- Sort with `orderby` (`date` default, or `relevance` when combined with `search`) and `order`.
- **Pagination is page-number, not cursor.** `per_page` is hard-capped at 100 — asking for more
  returns `400 rest_invalid_param`. Read `X-WP-Total` and `X-WP-TotalPages` from the response
  headers to know when to stop, or follow the `rel="next"` entry in the RFC 8288 `Link` header.
- Titles and excerpts come back HTML-encoded inside a `{"rendered": "..."}` wrapper. Decode the
  entities (`&#8217;` is an apostrophe) before showing them to anyone.

## Step 3 — Read one post (`getPost`)

```
GET /wp/v2/posts/{id}?_embed=true
```

`content.rendered` is full HTML, not markdown. Adding `_embed=true` inlines the author, terms and
featured image under `_embedded`, which saves you the follow-up calls in step 4.

## Step 4 — Resolve the references (`getMediaItem`, `listTags`)

A post's `featured_media` is a media ID, or `0` when there is no hero image — check for zero
before you fetch. `GET /wp/v2/media/{id}` returns `source_url` plus `media_details.sizes` with
every generated variant, so pick a size rather than serving the original.

`tags` is an array of term IDs resolved through `listTags`; on this site it is frequently empty,
so categories are the useful axis.

## Things that will bite you

- **`author` is a dead end.** Posts carry an author ID, but `/wp/v2/users` returns
  `401 rest_user_cannot_view`. Treat the ID as opaque; do not try to authenticate.
- **Errors are not RFC 9457.** You get `{"code": "...", "message": "...", "data": {"status": N}}`
  as `application/json`. Branch on `code`, not on prose.
- **Nothing here is versioned by Gotham Greens.** `wp/v2` is set by WordPress core. There is no
  deprecation policy, no changelog and no status page — a plugin update could change this surface
  with zero notice. Fail soft.
