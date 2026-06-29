# GapQuery Discovery Tools

All tools are prefixed with `mcp__gapquery__` when calling them.

## Discovery tools

| Tool | What it finds | Guided prompt? |
|------|--------------|----------------|
| `discover-opportunities` | Comprehensive scan, all dimensions scored and ranked. Omit `mode` for the 8-option menu; pass `mode: "comprehensive"` for the full scan. | Yes, omit `mode` |
| `find-disruption-targets` | Popular apps with weak ratings, ripe to beat. Omit `focus` for the menu; pass `focus: "worst_rated"` etc. for data. | Yes, omit `focus` |
| `analyze-integrations` | Missing or underserved integrations. Omit `focus` for the menu; pass `focus: "workflow_gaps"` etc. for data. Use the bridges mode for shared integrations between two ecosystems. | Yes, omit `focus` |
| `find-price-gaps` | Categories where every app is expensive, room for an affordable option. Omit `focus` for the menu. | Yes, omit `focus` |
| `find-category-gaps` | Categories thriving in one ecosystem but missing in another. | No |
| `find-keyword-opportunities` | Low-saturation tags: features users want but nobody builds. | No |
| `find-combination-gaps` | Feature coverage gaps a single new app could fill. | No |
| `find-developer-whitespace` | Successful developers in ecosystem A who have not entered B. | No |
| `find-cross-ecosystem-patterns` | Categories or developers winning in one ecosystem but absent from another. | No |
| `find-negative-space` | Workflows users need but no app addresses. Each row includes up to 5 real review-insight excerpts (workarounds, feature requests, pain points) with confidence and source app. Quote these instead of guessing what the counts mean. | No |
| `find-nascent-ecosystems` | Growing platforms with low saturation, early-mover territory. | No |
| `compare-ecosystems` | Side-by-side metrics for any set of ecosystems. | No |
| `category-overview` | Per-category breakdown for ONE ecosystem: app_count, avg_rating, total_reviews, reviews_per_app, avg/cheapest/most-expensive monthly price, free_tier_count. Sortable and filterable. The marketplace mental-model dashboard (not cross-ecosystem). | No |
| `search-apps` | Full-text search across all ecosystems, to check existing competition. | No |

Use `list-opportunities` to see saved opportunities (pass `slug` for full details).

## Raw-data bypass
When the user wants raw tables, call these with a default focus/mode to skip the guided prompt:

| Tool | Parameters for raw data |
|------|------------------------|
| `find-disruption-targets` | `focus: "worst_rated"` |
| `analyze-integrations` | `focus: "workflow_gaps"` |
| `find-price-gaps` | `focus: "most_expensive"` |
| `discover-opportunities` | `mode: "comprehensive"` |
| `find-category-gaps`, `find-keyword-opportunities`, `find-combination-gaps`, `compare-ecosystems`, `search-apps`, `find-developer-whitespace`, `find-nascent-ecosystems`, `category-overview` | no focus needed |

## Negative-space excerpts
`find-negative-space` returns up to 5 review-insight excerpts per workflow row by default. Each has:
- `text`: the actual user snippet (your evidence to quote)
- `insight_type`: `workaround` (strongest, proves a manual fallback exists), `feature_request`, or `pain_point`
- `confidence`: 0-1 from the AI mining pass
- `metadata`: for workarounds: `manual_step`, `frequency_signal`, `tool_mentioned`, `automation_potential`
- `source_app`: slug of the app the review is from

Ordered workarounds first, then feature requests, then pain points; within each, highest confidence first. Always quote at least one when presenting a negative-space card (see `cards.md`). Pass `include_excerpts: false` only for a deliberately lighter pass.

## Pagination contract
`find-keyword-opportunities`, `find-category-gaps`, `find-combination-gaps`, `find-developer-whitespace`, `find-nascent-ecosystems`, and `category-overview` share this contract (and `find-negative-space` for its excerpts):

| Field | Meaning |
|---|---|
| `count` | Rows returned this call |
| `total_count` | Rows matching the query, ignoring pagination |
| `truncated` | `true` if more rows exist past `offset + count` |
| `pagination.limit` | Cap requested (default 50, max 200) |
| `pagination.offset` | Offset used |
| `pagination.next_offset` | Pass back as `offset` for the next page; `null` on the last page |
| `truncated_reason` | `size_guard:trimmed_heavy_fields` or `size_guard:halved_rows` when trimmed for token budget (separate from pagination) |

When the user asks for more, call again with `offset = pagination.next_offset`. Filter parameters (min/max thresholds, persona, tag_type) are usually more useful than walking the long tail. If you see `truncated_reason: size_guard:halved_rows`, the rows themselves are heavy: narrow with filters rather than paginating.

## Saving a candidate opportunity
Use `save-opportunity` to add a promising result to the pipeline:

| Parameter | Required | Type | Description |
|-----------|----------|------|-------------|
| `name` | Yes | string | Name of the opportunity |
| `type` | Yes | string | `micro_app`, `integration`, `keyword_gap`, `pain_point`, `disruption_target`, or `price_gap` |
| `ecosystem` | No | string | Ecosystem slug (e.g. `shopify`) |
| `problem` | No | string | The problem it addresses |
| `solution` | No | string | The proposed solution |
| `how_it_works` | No | string | How the solution works |
| `target_audience` | No | string | Who it is for |
| `source_signal` | No | string | Which tool found this (so the web app can show why) |
| `signal_data` | No | object | The raw signal data from the discovery tool |
| `status` | No | string | `new`, `interested`, `dismissed`, `building` |

Always pass `source_signal` and `signal_data` so the opportunity page can show why GapQuery flagged it. The full build/skip research and the verdict belong to the `gapquery-research` skill, not here.
