---
name: Read Engrail Therapeutics company news
description: Pull Engrail Therapeutics press releases and corporate news from the company's public content API, page through the full set, and resolve each item's categories and featured media.
api: openapi/engrail-therapeutics-content-openapi.yml
operations: [listPosts, getPost, listCategories, getCategory, listMedia, getMediaItem]
generated: '2026-08-01'
method: generated
source: openapi/engrail-therapeutics-content-openapi.yml
---

# Read Engrail Therapeutics company news

Engrail Therapeutics is a clinical-stage precision-neuroscience company. It publishes **no product
API** — this skill operates the read-only content API its corporate WordPress site exposes at
`https://www.engrail.com/wp-json/wp/v2`. Use it to track pipeline announcements, financing news and
trial milestones (ENX-102 / ENCALM, ENX-104) programmatically instead of scraping HTML.

## Before you start

- **No authentication.** Every operation below was verified to return HTTP 200 anonymously on
  2026-08-01. Do not send credentials.
- **No rate-limit signalling.** The host returns no `RateLimit-*` or `Retry-After` headers and
  publishes no policy. Pace yourself — one request per second is a courteous ceiling — and treat any
  5xx as a signal to back off rather than retry hard.
- **Not idempotency-protected, but safe.** All operations here are GETs, so retries are safe by HTTP
  method. There is no idempotency-key contract on this API.

## Steps

1. **List the news stream.** Call `listPosts` with `per_page=100` and `orderby=date`, `order=desc`.
   Read `X-WP-Total` and `X-WP-TotalPages` from the response headers to learn the size of the
   collection before deciding whether to page. At probe time the collection held 10 items.
2. **Page if needed.** Increment the `page` parameter until you have consumed `X-WP-TotalPages`
   pages, or follow the `Link` header's `rel="next"` (RFC 8288). Never assume a page size above 100
   — `per_page` is capped at 100 and exceeding it returns HTTP 400 `rest_invalid_param`.
3. **Trim the payload.** Pass `_fields=id,date,modified,slug,link,title,excerpt,categories,featured_media`
   to `listPosts` so you transfer only what you need. Add `_embed` when you want the featured image
   and terms inlined under `_embedded` instead of making follow-up calls.
4. **Window by date for incremental sync.** On subsequent runs pass `modified_after` set to the
   highest `modified` timestamp you have already stored. This is the correct incremental cursor —
   `after` filters on publish date and will miss edits to existing releases.
5. **Fetch a single release.** Call `getPost` with the `id` from step 1 to retrieve the full
   `content.rendered` body of a press release.
6. **Resolve taxonomy.** The `categories` array on a post holds integer ids. Call `listCategories`
   once with `per_page=100` and build an id → name map rather than calling `getCategory` per id.
7. **Resolve imagery.** If `featured_media` is non-zero and you did not use `_embed`, call
   `getMediaItem` with that id to obtain `source_url`.

## Error handling

Errors use the WordPress envelope, **not** RFC 9457 Problem Details — there is no `type` URI, so
branch on `code` and read `data.status`:

| Status | `code` | What to do |
|---|---|---|
| 400 | `rest_invalid_param` | Read `data.details[<param>].message`. Most common cause: `per_page` above 100. |
| 404 | `rest_post_invalid_id` | The id does not exist. Re-list the collection; do not retry. |
| 404 | `rest_no_route` | Wrong path or method. Re-discover routes at `https://www.engrail.com/wp-json/`. |
| 401 | `rest_forbidden` | You touched a gated collection (for example `/wp/v2/users`). Authors are not publicly readable — drop the field rather than authenticating. |

## Do not

- Do not attempt to write. `POST`/`PUT`/`DELETE` exist on the live host but require a WordPress
  Application Password, and this is a third party's corporate site.
- Do not treat this content as clinical or regulatory data. It is marketing and investor-relations
  copy; ENX-102 and ENX-104 are investigational and not approved by any regulator.
