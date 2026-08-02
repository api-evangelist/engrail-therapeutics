---
name: Search and map Engrail Therapeutics site content
description: Discover what content types and taxonomies the Engrail Therapeutics site exposes, run a full-text search across it, and retrieve the corporate pages (pipeline, science, partners) behind the results.
api: openapi/engrail-therapeutics-content-openapi.yml
operations: [listTypes, listTaxonomies, listStatuses, listSearch, listPages, getPage, listTags, listComments]
generated: '2026-08-01'
method: generated
source: openapi/engrail-therapeutics-content-openapi.yml
---

# Search and map Engrail Therapeutics site content

Use this when you need to answer a question about Engrail Therapeutics from its own site — what is in
the pipeline, who the partners are, what the science is — without scraping rendered HTML. Base URL:
`https://www.engrail.com/wp-json`. No authentication is required for any operation below.

## Steps

1. **Learn the vocabulary first.** Call `listTypes` and `listTaxonomies` before searching. These
   return the content types and taxonomies actually registered on *this* host, which is the only
   authoritative answer — do not assume the stock WordPress set. `listStatuses` tells you which
   statuses are publicly visible.
2. **Search.** Call `listSearch` with `search=<terms>`, `per_page=100`. Constrain with `type` and
   `subtype` using values you learned in step 1. Results are a thin projection — each carries an
   `id`, a `type`, a `subtype`, a `title` and a `url`, not the body.
3. **Fetch the real object.** Branch on the result's `subtype`: call `getPage` for pages, `getPost`
   for posts (see the *Read Engrail Therapeutics company news* skill). Read `content.rendered` for
   the body.
4. **Or walk the pages directly.** For a structural map rather than a search, call `listPages` with
   `per_page=100` and `_fields=id,slug,link,title,parent,menu_order`, then order by `menu_order` and
   nest by `parent` to reconstruct the site hierarchy. The corporate pages are `about-us`,
   `our-science`, `pipeline`, `partners`, `careers`, `contact-us`, `privacy-policy`, `terms-of-use`
   and `legal-disclaimer`.
5. **Look up a page by slug.** `listPages` accepts a `slug` filter, which is cheaper and more stable
   than searching when you already know the URL — for example `slug=pipeline`.
6. **Check for discussion.** `listComments` is public. On a corporate site like this it is normally
   empty; a non-empty result is worth noting rather than assuming.

## Conventions that apply to every call

- **Pagination:** `page` + `per_page` (max 100). Read `X-WP-Total` and `X-WP-TotalPages`; follow the
  `Link` header `rel="next"`.
- **Sparse fields:** `_fields=<csv>` trims the response. Always use it — full page objects carry
  large rendered HTML blobs.
- **Expansion:** `_embed` inlines linked resources under `_embedded`.
- **Context:** `context=view` is the default and the only one available anonymously; `context=edit`
  returns HTTP 401 `rest_forbidden`.
- **No rate-limit headers.** Pace requests conservatively and back off on 5xx.

## Errors

Branch on the `code` field of the WordPress error envelope and read `data.status`; there is no
RFC 9457 `type` URI. `rest_invalid_param` (400) usually means `per_page` exceeded 100;
`rest_no_route` (404) means the path is not registered — re-discover at
`https://www.engrail.com/wp-json/`; `rest_forbidden` (401) means the collection or context is gated.

## Note on the agent surface

This host also runs two Model Context Protocol servers at
`https://www.engrail.com/wp-json/mcp/mcp-oauth-server` and
`.../mcp-adapter-default-server`. Both require an `mcp`-scoped OAuth 2.1 bearer token
(authorization-code + PKCE S256 via `https://www.engrail.com/oauth/authorize`) and return HTTP 401
to anonymous callers, including for `tools/list`. Their tool set is therefore unknown — do not
assume it mirrors the REST operations above. See `mcp/engrail-therapeutics-tool-crosswalk.yml`.
