# GapQuery Research Tools

All tools are prefixed with `mcp__gapquery__` when calling them. This reference covers the tools used during deep research. For the discovery tools (finding new opportunities), see the `gapquery-discover` skill.

## Loading the opportunity
Use `list-opportunities` with a `slug` to load one opportunity in full, including any `market_reality` block written by `gapquery-validate`. Without a slug it lists the whole pipeline (filter by `status`, `verdict`, `ecosystem`, or `type`).

## Saving / updating the opportunity
Use `save-opportunity` to create the record if it does not exist, and to set status after research:

| Parameter | Required | Type | Description |
|-----------|----------|------|-------------|
| `name` | Yes | string | Name of the opportunity |
| `type` | Yes | string | `micro_app`, `integration`, `keyword_gap`, `pain_point`, `disruption_target`, `price_gap` |
| `ecosystem` | No | string | Ecosystem slug |
| `problem` / `solution` / `how_it_works` / `target_audience` | No | string | Context fields |
| `status` | No | string | `new`, `interested`, `dismissed`, `building` |
| `build_complexity` | No | string | `easy`, `medium`, `hard`, `expert` |
| `build_complexity_note` | No | string | Notes on build complexity |
| `verdict` | No | string | If set, also saves research (fallback for `save-research`) |

After research, set status from the verdict: `build` or `maybe` to `interested` (and set `build_complexity` from the technical analysis); `skip` to `dismissed`.

## Saving research
Use `save-research` (preferred for research data):

| Parameter | Required | Type | Description |
|-----------|----------|------|-------------|
| `opportunity_slug` | Yes | string | Slug of the opportunity |
| `verdict` | Yes | string | `build`, `skip`, or `maybe` |
| `executive_summary` | Yes | string | 2-3 paragraph briefing that stands on its own |
| `verdict_reasoning` | Yes | string | Why this verdict |
| `pros` | Yes | array | Advantages (minimum 3, specific) |
| `cons` | Yes | array | Disadvantages (minimum 2, specific) |
| `revenue_analysis` | No | object | `{model, estimated_mrr, pricing_strategy, assumptions}` |
| `market_analysis` | No | object | `{tam_sam, target_customers, growth_trends, market_timing}` |
| `keyword_research` | No | object | `{primary_keywords, search_volume_notes, seo_difficulty, content_strategy}` |
| `competition_analysis` | No | object | `{direct_competitors, indirect_competitors, advantages, barriers}` |
| `technical_analysis` | No | object | `{apis_needed, complexity, dev_time_estimate, stack_recommendation, challenges}` |
| `how_it_works_detailed` | No | string | Multi-paragraph implementation description |
| `go_to_market` | No | object | `{launch_strategy, channels, partnerships}` |
| `risks` | No | array | Risk strings |
| `next_steps` | No | array | Next-step strings |
| `sources` | No | array | Source URLs visited (minimum 3; reuse `market_reality` URLs) |
| `summary` | No | object | Summary brief, see below (saved in the second call) |

Fill in every optional field. The research is only useful when it is comprehensive.

### Quality bar
- **Executive summary**: 2-3 paragraphs, a complete briefing on its own.
- **Pros/cons**: 3+ pros, 2+ cons, specific. "Large market of 2M Shopify stores," not "good market."
- **Revenue**: real numbers with the math shown.
- **Competition**: name competitors with pricing, ratings, user counts. Not "several competitors exist."
- **Technical**: specific APIs, SDKs, limits; a realistic dev-time estimate.
- **Sources**: real URLs, minimum 3.

### Summary brief (the second save)
The `summary` object powers the web app's Quick Summary flyout:
- `one_liner`: 1 sentence, verdict + key reason (max 150 chars)
- `key_strengths`: exactly 3 short strings (max 80 chars each), reasons TO build
- `key_risks`: exactly 3 short strings (max 80 chars each), reasons NOT to build
- `recommended_action`: 1 sentence, the most important next step (max 150 chars)
- `effort_estimate`: e.g. "2-4 weeks, medium complexity"

Save it in a **second** `save-research` call with only `opportunity_slug` and `summary`. The tool detects this and writes only the summary, preserving the full record from the first call. A successful second call returns `mode: "summary_only"`.

```jsonc
// Save 1: full record
{ "opportunity_slug": "shopify-multi-store-bulk-product-editor", "verdict": "build",
  "executive_summary": "...", "verdict_reasoning": "...", "pros": [...], "cons": [...],
  "revenue_analysis": {...}, "market_analysis": {...} /* ...all six areas, sources */ }

// Save 2: summary only (separate call)
{ "opportunity_slug": "shopify-multi-store-bulk-product-editor",
  "summary": { "one_liner": "...", "key_strengths": [...], "key_risks": [...],
               "recommended_action": "...", "effort_estimate": "..." } }
```

If `save-research` is unavailable, fall back to `save-opportunity` with the verdict and research fields.
