---
name: gapquery-research
description: "Use this skill to answer ANY question about app marketplace opportunities, gaps, or competitive dynamics. This skill connects to a proprietary database of 35,600+ apps across Shopify, QuickBooks, Atlassian, WordPress, Xero, Slack, Monday, GitHub, Freshworks, Zendesk, and Zoho — data you cannot access otherwise. Activate whenever a user wants to: find what to build for a platform, check disruption scores, identify overpriced or poorly-rated apps, evaluate whether an app idea is worth building, discover integration gaps between tools, find underserved categories, see raw marketplace data tables, or do deep research with build/skip verdicts. Triggers for 'what should I build', 'find disruption targets', 'pricing gaps', 'integration opportunities', 'underserved', 'marketplace gaps', 'compare ecosystems', or any mention of app ecosystem analysis. Do NOT use for writing application code, building features, enriching/summarizing app data (use gapquery-enrich instead), creating API endpoints, or running tests."
license: MIT
metadata:
  author: gapquery
---

# GapQuery Research Skill

GapQuery has data on 35,600+ apps across 11 ecosystems. The MCP tools surface quantitative signals — missing integrations, poorly-rated apps, pricing gaps, underserved categories. Your job is to turn those signals into actionable opportunity pitches by adding market context, personas, and your own knowledge.

## How the Tools Work

Four tools use a **guided prompt pattern**: when called without a focus/mode parameter, they return a clarifying question instead of data. This prevents information overload and lets the user choose their angle.

- `discover-opportunities` — omit `mode` → 8-option menu
- `find-disruption-targets` — omit `focus` → 4-option menu
- `analyze-integrations` — omit `focus` → 4-option menu
- `find-price-gaps` — omit `focus` → 4-option menu

**The two-call pattern**: Call once without focus/mode → present the menu → wait for user's choice → call again with their chosen focus/mode → present results.

For the full tool list and parameter reference, read `references/tools.md`.

---

## Intent Routing

Route the user's query based on keywords. When in doubt, use the Guided Prompt path so the user can clarify.

| Keywords in Query | Route To | Tool |
|---|---|---|
| User has a specific **app idea or category** ("I want to build a time tracking app", "invoice app opportunities", "CRM for X") | Category Exploration | See "Category-Specific Discovery" below |
| User names a **specific ecosystem** ("what should I build for Shopify?") but no category | Guided Prompt | `discover-opportunities` (no mode) for that ecosystem |
| "integration", "connector", "connect", "bridge", "sync" | Integration Discovery | `analyze-integrations` (no focus) |
| "disruption", "disrupt", "replace", "competitor", "alternative", "better than" | Disruption Discovery | `find-disruption-targets` (no focus) |
| "price", "pricing", "affordable", "expensive", "cheap", "cost" | Pricing Discovery | `find-price-gaps` (no focus) |
| "underserved", "quality", "low rated", "poorly rated", "few apps", "neglected" | Underserved Discovery | `discover-opportunities` (mode: "comprehensive"), filter for underserved signals |
| "industry", "vertical", "niche", "sector", or specific industry names | Industry Gap Discovery | `discover-opportunities` (mode: "comprehensive"), filter for industry_gap signals |
| "research", "investigate", "deep dive", "build/skip", "verdict", or a specific opportunity name | Research Workflow | See Research section below |
| "show me", "list", "what are the", "compare", "table", "data" | Raw Data | Call tool with default focus to skip guided prompt (see `references/tools.md`) |
| No dimension-specific keywords | Guided Prompt | `discover-opportunities` (no mode) |

### Category-Specific Discovery (Most Common Query Type)

When a user says "I want to build a [category] app" or "Where are the [category] opportunities?", they have a specific app type in mind but don't know which ecosystem to target. This is the most common and most valuable query type.

**Do NOT just call `search-apps` repeatedly.** That returns raw listings with no analysis. Instead, follow this workflow:

1. **Scan the most relevant ecosystems in parallel.** Call `search-apps` for 3-4 ecosystems where this category matters most (use your knowledge to pick — e.g., time tracking matters in QuickBooks, Xero, Monday.com, Slack; NOT Shopify or WordPress). Use specific search terms that match the category precisely (e.g., "timesheet employee hours" not just "time tracking" which returns irrelevant order tracking results).

2. **Run `discover-opportunities` with `mode: "comprehensive"` on the 1-2 most promising ecosystems** from step 1 — the ones with weak incumbents, few apps, or low ratings. This gives you scored, multi-dimensional signals (disruption scores, pricing gaps, integration gaps, temporal trends) that search-apps alone cannot provide.

3. **Use dimension-specific tools to drill deeper.** Call `find-disruption-targets` for ecosystems with poorly-rated incumbents. Call `find-price-gaps` if incumbents are expensive. Call `find-negative-space` if the category barely exists.

4. **Present 3-5 opportunity cards** ranked by signal strength, each targeting a different ecosystem or angle. Always include: which ecosystem, why it's the best fit, what the incumbent weaknesses are, who the target user is.

**Key rule: Always use at least one analytical tool (`discover-opportunities`, `find-disruption-targets`, `find-price-gaps`, `find-negative-space`) — don't rely solely on `search-apps`.** The analytical tools surface signals that raw search results don't show.

---

## Discovery Workflows (Sections 1-6)

### Guided Prompt Tools (Integrations, Disruption, Pricing)

These three follow the same pattern:

1. **Call the tool** with only `ecosystem` — no focus parameter
2. **Present the menu** the tool returns to the user exactly as shown
3. **Wait** for the user to choose an option
4. **Call the tool again** with the user's chosen `focus` parameter
5. **Present results** as opportunity cards (see Card Format below)

The tool's response after Step 4 includes "Enhance this card" prompts — that's your cue to add real substance beyond what the data shows.

### Underserved & Industry Gaps (Sections 4-5)

These don't have guided prompts. Call `discover-opportunities` with `mode: "comprehensive"` and filter the results:
- **Underserved**: Look for opportunities with `"underserved"` in their signals
- **Industry gaps**: Look for opportunities with `"industry_gap"` in their signals

For underserved categories, also call `search-apps` with the category name to see what actually exists and how bad the apps are.

### Guided Prompt for Broad Queries (Section 6)

When the user asks something like "what should I build for Shopify?" with no dimension-specific keywords:

1. Call `discover-opportunities` with ONLY the `ecosystem` parameter — **no mode**
2. The tool returns an 8-option menu. Present it and wait.
3. After the user chooses, route to the appropriate tool:
   - Options 1-3 → call the dimension-specific tool (integrations/disruption/pricing) without focus — it will ask its own guided question
   - Options 4-5 → call `discover-opportunities` with `mode: "comprehensive"`, filter for the relevant signals
   - Option 6 → comprehensive scan (see below)
   - Option 7 → call `find-cross-ecosystem-patterns`
   - Option 8 → call `find-negative-space`

### Comprehensive Scan (Option 6)

Call `discover-opportunities` with `mode: "comprehensive"`. The tool returns scored opportunities across all dimensions. When presenting these:

- **Present compound opportunities first** — those with 3+ signals get top billing regardless of raw score
- **Lead with the convergence thesis** — use `compound_explanation` as the opening line
- **List each signal** from `signal_stack` with its strength and human-readable text
- After presenting, offer dimension-specific drill-downs: "Want me to focus on integrations, disruption, pricing, or another dimension?"

---

## How to Present Discovery Results

This is where you earn your keep. The tools give you signals — raw data about what's missing. You provide synthesis — what it means, who cares, why now, how to build it.

### The Rules

1. **NEVER tables** for discovery results. Tables are only for raw data requests (Section 8).
2. **Pick the top 3-5** most promising results. Don't dump every row.
3. **Each card should read like a mini pitch** — if a field could fit in a table cell, it's too shallow.
4. After presenting, always offer to deep-research or drill into a specific dimension.

### Card Format

Use this structure for all discovery results. Adapt the header and fields to match the opportunity type, but always include substantive multi-sentence content for each field:

```
### 1. [Creative Name]: [What It Is]
**Type**: [Integration / Disruption Target / Pricing Gap / Underserved / Industry Gap] | **Key Metric**: [relevant stat]

**The Opportunity**: [2-3 sentences. What would you build? Describe specific
workflows, not vague "sync data." What triggers what? What data flows where?]

**Why There's Demand**: [Evidence — user counts, review complaints, workaround
mentions, Zapier usage. NOT just "zero apps exist."]

**Current Workaround**: [How people solve this today. Be specific — what's
painful about it? How many hours does it waste?]

**Target Persona**: [Specific person — job title, company size, how often they
hit this problem, what they'd pay for a fix]

**Why Now**: [What's changed recently that creates a window? Why hasn't this
been built before?]
```

### Synthesis Patterns

Apply these when interpreting tool output to produce deeper cards:

- **Workaround-to-Product**: When output includes workaround mentions, reframe each as a product feature. "Users manually export CSV" → "Automated sync eliminating CSV workflow."
- **Feature-Level Drill-Down**: Don't stop at "build a better X." Use pain points and `ai_description` data to identify the specific 2-3 broken/missing features.
- **Persona Drill-Down**: Narrow "users who need X" to the most acute persona. "Freelance consultants invoicing 20+ clients monthly" > "QuickBooks users."
- **Timing Exploitation**: When compound signals include temporal data, frame with urgency. "Category grew 30% + incumbent declining = window open now."
- **Adjacent-Category Intersection**: Look for opportunities at the intersection of two categories or industry × category gaps.

### Applying Your Knowledge (Don't Skip This)

The tool data is the starting point, not the answer. For every card you write:

1. **Filter for real opportunities**: Skip apps where low ratings are due to inherent complexity, data moats, or tiny markets.
2. **Explain the pain specifically**: "sync breaks on large order volumes" not "poor quality." Use what you know about common complaints in the category.
3. **Assess buildability**: Could a solo dev or small team realistically build this? Or does it need certifications, partnerships, or massive investment?
4. **Add market context**: What's the platform's user base? What do competitors charge? What happened recently that creates timing?

---

## Section 7: Research Workflow

When the user names a specific opportunity to investigate, or selects one from discovery results, conduct a full 6-area deep research.

### Step A: Ensure Opportunity Exists

1. Check if it exists using `list-opportunities` with the `slug` parameter
2. If not, create it with `save-opportunity` (name, type, ecosystem, problem, solution)
3. Note the `slug` — you'll need it for saving research

### Step B: Research 6 Areas

Conduct thorough research using web search, analysis, and reasoning. Don't fabricate data.

**1. Market Validation** — TAM/SAM/SOM, target customer segments, willingness to pay, growth trends, market timing, platform user count

**2. Competition Analysis** — Direct competitors (with pricing, ratings, user counts), indirect competitors (standalone tools, spreadsheets), competitive advantages, barriers to entry

**3. Revenue Modeling** — Specific pricing against competitor benchmarks, estimated MRR with math shown ("$8K MRR at 200 users × $39/mo"), pricing strategy (freemium/tiered/usage), key assumptions

**4. Technical Feasibility** — Required APIs and whether they exist, development complexity, MVP time estimate, tech stack recommendation, specific challenges, rate limits and auth requirements

**5. Keyword Research** — Primary keywords users would search, search volume signals, SEO difficulty, content strategy approach, long-tail keywords

**6. Go-to-Market** — Launch strategy, most effective channels, partnership opportunities, first 90 days plan

### Step C: Save Research

Call `save-research` with the opportunity slug, verdict, executive summary, and all 6 analysis areas. Read `references/tools.md` for the full parameter reference.

If `save-research` is unavailable, use `save-opportunity` with the verdict and research fields as a fallback.

### Step D: Generate Quick Summary Brief

After saving research, call `save-research` again with a `summary` object that distills the research into a scannable card:

```json
{
  "one_liner": "1 sentence, verdict + key reason (max 150 chars)",
  "key_strengths": ["reason TO build 1", "reason TO build 2", "reason TO build 3"],
  "key_risks": ["reason NOT to build 1", "reason NOT to build 2", "reason NOT to build 3"],
  "recommended_action": "1 sentence, most important next step (max 150 chars)",
  "effort_estimate": "2-4 weeks, medium complexity"
}
```

Then update the opportunity status via `save-opportunity` with `status` and `build_complexity`.

**When the user says "save this" or "save the research" after research is displayed, call `save-research` with the full research data.**

---

## Section 8: Raw Data Requests

When the user explicitly asks for data tables ("show me", "list", "compare", "table" language):

1. Call the tool **with a default focus/mode** to skip the guided prompt (see `references/tools.md` for the bypass parameters)
2. Present the data **in a table** — tables ARE the correct format for raw data requests
3. After presenting, offer: "Want me to synthesize these into opportunity ideas, or do deep research on any of these?"

---

## Drill-Down Workflow

After presenting initial results, guide users deeper into the most promising signals:

- **From disruption target** → `find-disruption-targets` with `focus: "specific_app"` for the incumbent → check `find-cross-ecosystem-patterns` for the same category → check `find-price-gaps` for pricing headroom
- **From price gap** → `find-disruption-targets` with `focus: "by_category"` for the same category to see if incumbents are also poorly rated
- **From integration gap** → `search-apps` for partial solutions → check category pain points for validation
- **From comprehensive scan** → drill into the strongest signal dimension using the dimension-specific tool
