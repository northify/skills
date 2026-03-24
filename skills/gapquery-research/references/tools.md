# GapQuery Tool Reference

## Available Tools

| Tool | What It Finds | Guided Prompt? |
|------|--------------|----------------|
| `discover-opportunities` | Comprehensive scan — all dimensions scored and ranked. Omit mode for 8-option menu; pass `mode: "comprehensive"` for full scan. | Yes — omit `mode` |
| `find-disruption-targets` | Apps with high usage but terrible ratings — ripe for replacement. Omit focus for guided prompt; pass `focus: "worst_rated"` etc. for data. | Yes — omit `focus` |
| `analyze-integrations` | Missing/underserved integrations. Omit focus for guided prompt; pass `focus: "workflow_gaps"` etc. for data. | Yes — omit `focus` |
| `analyze-integrations` (mode: bridges) | Shared integrations between two ecosystems — cross-ecosystem bridge opportunities | No |
| `find-price-gaps` | Categories where every app is expensive — room for affordable options. Omit focus for guided prompt; pass `focus: "most_expensive"` etc. for data. | Yes — omit `focus` |
| `find-category-gaps` | Categories thriving in one ecosystem but missing in another | No |
| `find-keyword-opportunities` | Low-saturation tags — features users want but nobody builds | No |
| `find-combination-gaps` | Feature coverage gaps a single new app could fill | No |
| `find-developer-whitespace` | Successful devs in ecosystem A who haven't entered B | No |
| `find-cross-ecosystem-patterns` | Categories/developers successful in one ecosystem but missing in another | No |
| `find-negative-space` | Workflows users need but no app addresses | No |
| `find-nascent-ecosystems` | Growing platforms with low saturation — early mover territory | No |
| `compare-ecosystems` | Side-by-side metrics for any set of ecosystems | No |
| `search-apps` | Full-text search across all ecosystems — check existing competition | No |

Use `list-opportunities` to see all saved opportunities (or pass `slug` for full details).

All tools are prefixed with `mcp__gapquery__` when calling them.

---

## Raw Data Bypass (Section 8)

When the user wants raw data tables, call tools with these defaults to skip the guided prompt:

| Tool | Parameters for Raw Data | When to Use |
|------|------------------------|------------|
| `find-disruption-targets` | `focus: "worst_rated"` | "Show me disruption targets" |
| `analyze-integrations` | `focus: "workflow_gaps"` | "List missing integrations" |
| `find-price-gaps` | `focus: "most_expensive"` | "What are the price gaps" |
| `discover-opportunities` | `mode: "comprehensive"` | "Show me all opportunities" |
| `find-category-gaps` | _(no focus needed)_ | "Compare categories between ecosystems" |
| `find-keyword-opportunities` | _(no focus needed)_ | "Show keyword opportunities" |
| `find-combination-gaps` | _(no focus needed)_ | "Find feature pair gaps" |
| `compare-ecosystems` | _(no focus needed)_ | "Compare ecosystems" |
| `search-apps` | _(no focus needed)_ | "Search for invoice apps" |
| `find-developer-whitespace` | _(no focus needed)_ | "Find developers not in target" |
| `find-nascent-ecosystems` | _(no focus needed)_ | "Show nascent ecosystems" |

---

## How to Save an Opportunity

Use `mcp__gapquery__save-opportunity`:

| Parameter | Required | Type | Description |
|-----------|----------|------|-------------|
| `name` | Yes | string | Name of the opportunity |
| `type` | Yes | string | One of: `micro_app`, `integration`, `keyword_gap`, `pain_point`, `disruption_target`, `price_gap` |
| `ecosystem` | No | string | Ecosystem slug (e.g. `shopify`, `quickbooks`) |
| `problem` | No | string | Problem this opportunity addresses |
| `solution` | No | string | Proposed solution |
| `how_it_works` | No | string | How the solution works |
| `target_audience` | No | string | Target audience description |
| `source_signal` | No | string | Which gap analysis tool found this signal |
| `signal_data` | No | object | Raw signal data from the discovery tool |
| `status` | No | string | `new`, `interested`, `dismissed`, `building` |
| `build_complexity` | No | string | `easy`, `medium`, `hard`, `expert` |
| `build_complexity_note` | No | string | Notes about build complexity |
| `verdict` | No | string | `build`, `skip`, `maybe` — if provided, also saves research (fallback for `save-research`) |

When verdict is provided, `save-opportunity` also accepts all research fields below as a fallback.

**Status logic after research:**
- Verdict `build` → status: `interested`, set `build_complexity` from technical analysis
- Verdict `maybe` → status: `interested`, note uncertainty in `build_complexity_note`
- Verdict `skip` → status: `dismissed`

---

## How to Save Research

Use `mcp__gapquery__save-research` (preferred over save-opportunity for research data):

| Parameter | Required | Type | Description |
|-----------|----------|------|-------------|
| `opportunity_slug` | Yes | string | Slug of the opportunity |
| `verdict` | Yes | string | `build`, `skip`, or `maybe` |
| `executive_summary` | Yes | string | 2-3 paragraph summary of findings |
| `verdict_reasoning` | Yes | string | Detailed reasoning for the verdict |
| `pros` | Yes | array | Array of advantage strings (minimum 3) |
| `cons` | Yes | array | Array of disadvantage strings (minimum 2) |
| `revenue_analysis` | No | object | `{model, estimated_mrr, pricing_strategy, assumptions}` |
| `market_analysis` | No | object | `{tam_sam, target_customers, growth_trends, market_timing}` |
| `keyword_research` | No | object | `{primary_keywords, search_volume_notes, seo_difficulty, content_strategy}` |
| `competition_analysis` | No | object | `{direct_competitors, indirect_competitors, advantages, barriers}` |
| `technical_analysis` | No | object | `{apis_needed, complexity, dev_time_estimate, stack_recommendation, challenges}` |
| `how_it_works_detailed` | No | string | Multi-paragraph implementation description |
| `go_to_market` | No | object | `{launch_strategy, channels, partnerships}` |
| `risks` | No | array | Array of risk strings |
| `next_steps` | No | array | Array of next step strings |
| `sources` | No | array | Array of source URLs visited during research |
| `summary` | No | object | `{one_liner, key_strengths, key_risks, recommended_action, effort_estimate}` |

**Always fill in all optional fields.** The research is only valuable when comprehensive.

### Summary Brief Format

The `summary` field is displayed in the web app's Quick Summary flyout:

- `one_liner`: 1 sentence summarizing verdict + key reason (max 150 chars)
- `key_strengths`: exactly 3 short strings (max 80 chars each) — top 3 reasons TO build
- `key_risks`: exactly 3 short strings (max 80 chars each) — top 3 reasons NOT to build
- `recommended_action`: 1 sentence, most important next step (max 150 chars)
- `effort_estimate`: e.g. "2-4 weeks, medium complexity"

---

## Research Quality Guidelines

- **Executive summary**: 2-3 paragraphs. Should stand alone as a complete briefing.
- **Pros/Cons**: Minimum 3 pros and 2 cons. Be specific — "Large addressable market of 2M Shopify stores" not "Good market."
- **Revenue analysis**: Include specific numbers and show your math. "$8K MRR at 200 users x $39/mo" not "good revenue potential."
- **Market analysis**: Include actual market size data when available. Cite sources.
- **Keyword research**: Include real keywords users would actually search for. Note volume signals.
- **Competition**: Name specific competitors with their pricing, ratings, and user counts. Don't say "several competitors exist."
- **Technical**: Identify specific APIs, SDKs, and known limitations. Estimate dev time realistically.
- **Sources**: Include actual URLs visited during research. Minimum 3 sources.
