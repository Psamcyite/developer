---
id: functional-specification
title: "Yoast SEO Meta robots: Functional specification"
sidebar_label: Specification
description: An overview of how meta robots tags work in Yoast SEO.
---
Yoast SEO always outputs a _meta robots_ tag in the `<head>` of every page. When a user wishes to proactively restrict the crawling, indexing, or presentation of a page by search engines (via our [controls](/features/controls/overview.md)), we alter the content of that tag.

The tag takes the following format: `<meta name="robots" content="{{values}}" />`.

## Constructing `{{values}}`
Given that a meta robots tag may have multiple (potentially conflicting) values, and that multiple overlapping conditions may apply, then:

* All conditions should be evaluated and the resultant tag values combined additively, for each agent, using the rules defined below (see _Resolving conflict_).
* The final set of values should be de-duplicated and reconciled.
An up-to-date list of all possible meta robots values and their relationships is maintained [here](https://yoast.com/robots-meta-tags/).

## Standard structure
Yoast SEO outputs the following meta robots tags by default on each (public) page, with the following structure:

`<meta name="robots" content="{{values}}, max-snippet:-1, max-image-preview:large, max-video-preview:-1" />`

This provides generic instructions to all robots, and, opts users into Google and Bing's specific handling of snippet/media restrictions.

Unless otherwise defined by the user (or via page/template/filtering logic), `{{values}}` outputs `index, follow`.

## Scenarios

### On non-public pages
Any 'non-public' page - i.e., a page, post or archive type which the user has determined should *not* appear in search results (e.g., via our Search Appearance settings) - should return `noindex` and `follow` properties. E.g.,
`<meta name="robots" content="noindex, follow" />`

### Custom controls per-post/page
Any advanced/custom properties which a user specifies for a given post or page should be returned in the `{{values}}` property, as defined.

If a `noindex` value is set, then the page should be treated as if it is *non-public* (see above).

### Error templates
No robots tag should be output on `4xx` and `5xx` templates.

### Resolving conflict
In the case of opposing directives, the most restrictive setting should always take precedence, using the following rules:

* `noindex` over `index`.
* `nofollow` over `follow`.
* `none` over `nofollow` OR `noindex`.

E.g., a combination of `index`, `noindex`, `noimageindex` and `follow` values should result in an output of `noindex, follow` (as `noindex` is more restrictive than `index`, and `noimageindex` is redundant when combined with `noindex`; see [docs](https://yoast.com/robots-meta-tags/) for a full overview of value relationships).
