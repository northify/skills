---
name: gapquery-discover
description: "Find candidate app-marketplace opportunities from GapQuery's proprietary database of 35,600+ apps across Shopify, QuickBooks, Atlassian, WordPress, Xero, Slack, Monday, GitHub, Freshworks, Zendesk, and Zoho. Use this whenever the user wants to discover WHAT to build: 'what should I build for Shopify', 'find gaps', 'disruption targets', 'pricing gaps', 'integration opportunities', 'underserved categories', 'show me the marketplace data', 'compare ecosystems', or names an app idea/category and wants to know which platform to target. ALSO use it when the user describes the goal in their own casual words without naming a 'gap' or 'opportunity', for example 'which apps have tons of installs but terrible ratings I could beat', 'where is the Shopify app store weak', 'apps I could do better than', 'what's missing in the WordPress plugins', or 'is there room for an X app'. When in doubt about an app-idea or marketplace question, reach for this skill rather than answering from memory, since it has data you otherwise cannot see. This skill surfaces and presents opportunities and saves the promising ones to investigate. It does NOT do the deep build/skip research (that is gapquery-research) and does NOT validate demand on the web (that is gapquery-validate). Do not use for writing application code or enriching app data (use gapquery-enrich)."
license: MIT
metadata:
  author: gapquery
  version: 2.1.0
---

# GapQuery Discover

GapQuery has data on 35,600+ apps across 11 ecosystems. The MCP tools surface quantitative signals: missing integrations, poorly-rated apps, pricing gaps, underserved categories, workflows nobody serves. Your job in this skill is to find the strongest signals and turn them into concrete opportunity pitches the user can act on, then save the promising ones.

This skill ends at "saved as an opportunity to investigate." Validating real-world demand and running the full build/skip research are separate skills (`gapquery-validate`, `gapquery-research`); hand off to them at the end.

Follow `references/voice.md` for tone in everything you write (no em dashes, plain words, short sentences). Follow `references/cards.md` for how to present results.

## Skill version check
This skill's version is in the frontmatter above. MCP tool responses include a `latest_skill_version`. If a response shows a newer version, tell the user: "A newer version of the GapQuery skills is available. Run `npx skills add northify/skills -y` to update."

## How the tools work
Four tools use a **guided prompt**: called without a focus/mode parameter, they return a clarifying question instead of data. This keeps the user in control of the angle.

- `discover-opportunities`: omit `mode` to get an 8-option menu
- `find-disruption-targets`: omit `focus` to get a 4-option menu
- `analyze-integrations`: omit `focus` to get a 4-option menu
- `find-price-gaps`: omit `focus` to get a 4-option menu

The pattern: call once with no focus/mode, present the menu the tool returns, wait for the user's choice, call again with their choice, then present results as cards.

For the full tool list, parameters, and the pagination contract, read `references/tools.md`.

## Intent routing
Route the query by keywords. When unsure, use the guided-prompt path so the user can clarify.

| Keywords in the query | Route to | Tool |
|---|---|---|
| A specific **app idea or category** ("I want to build a time tracking app", "invoice app opportunities") | Category exploration | See "Category-specific discovery" below |
| A **specific ecosystem** but no category ("what should I build for Shopify?") | Guided prompt | `discover-opportunities` (no mode) for that ecosystem |
| "integration", "connector", "connect", "bridge", "sync" | Integration discovery | `analyze-integrations` (no focus) |
| "disruption", "disrupt", "replace", "competitor", "alternative", "better than" | Disruption discovery | `find-disruption-targets` (no focus) |
| "price", "pricing", "affordable", "expensive", "cheap", "cost" | Pricing discovery | `find-price-gaps` (no focus) |
| "underserved", "quality", "low rated", "poorly rated", "few apps", "neglected" | Underserved | `discover-opportunities` (mode: "comprehensive"), filter for underserved signals |
| "industry", "vertical", "niche", or a specific industry name | Industry gaps | `discover-opportunities` (mode: "comprehensive"), filter for industry_gap signals |
| "category overview", "category data", "reviews per app", "average rating per category", "marketplace dashboard", any single-ecosystem category-level table | Marketplace mental model | `category-overview` |
| "show me", "list", "what are the", "compare", "table", "raw data" | Raw data | Call the tool with a default focus to skip the guided prompt (see `references/tools.md`) |
| No dimension-specific keywords | Guided prompt | `discover-opportunities` (no mode) |

### Category-specific discovery (the most common query)
When a user says "I want to build a [category] app" or "where are the [category] opportunities?", they have an app type in mind but not a target platform. This is the highest-value query, so do not just call `search-apps` and stop.

1. **Scan the most relevant ecosystems in parallel.** Call `search-apps` for the 3-4 ecosystems where this category matters most (use your judgment: time tracking matters in QuickBooks, Xero, Monday, Slack, not Shopify or WordPress). Use precise search terms ("timesheet employee hours", not "time tracking", which returns order-tracking noise).
2. **Run `discover-opportunities` with `mode: "comprehensive"` on the 1-2 most promising ecosystems** from step 1, the ones with weak incumbents, few apps, or low ratings. This gives scored, multi-dimensional signals that search alone cannot.
3. **Drill deeper with the dimension tools.** `find-disruption-targets` where incumbents are poorly rated, `find-price-gaps` where they are expensive, `find-negative-space` where the category barely exists.
4. **Present 3-5 cards** ranked by signal strength, each targeting a different ecosystem or angle.

Always use at least one analytical tool, not just `search-apps`. The analytical tools surface signals raw search results do not.

## Discovery workflows

### Guided-prompt tools (integrations, disruption, pricing)
1. Call the tool with only `ecosystem`, no focus.
2. Present the menu it returns, exactly as shown.
3. Wait for the user's choice.
4. Call again with their chosen `focus`.
5. Present results as cards (`references/cards.md`). The tool's response includes "Enhance this card" prompts: that is your cue to add real substance.

### Broad query (no dimension keywords)
1. Call `discover-opportunities` with only `ecosystem`, no mode. It returns an 8-option menu.
2. Present it, wait for the choice, then route: options 1-3 to the dimension tool (no focus, it asks its own question); options 4-5 to `discover-opportunities` (mode: "comprehensive"), filtered for the relevant signals; option 6 to a comprehensive scan; option 7 to `find-cross-ecosystem-patterns`; option 8 to `find-negative-space`.

### Comprehensive scan
Call `discover-opportunities` with `mode: "comprehensive"`. When presenting:
- Lead with compound opportunities (3+ signals), regardless of raw score.
- Open with the convergence line (use `compound_explanation`), in plain words.
- List each signal from `signal_stack` with its strength and meaning.
- Then offer a dimension-specific drill-down.

## Check prior verdicts before presenting (do not re-pitch dead ideas)

Raw signals do not know what the user already ruled out. The negative-space and disruption scores especially have a known blind spot: a workflow can score high because no narrow app matches it, while a broad incumbent (AutoDS, Gorgias, DSers) already covers it. Once the user has validated such an idea, it must not resurface as fresh next session.

So before you present any cards, call `list-opportunities` once to load the user's saved opportunities. Each carries:
- `market_reality`: `demand_verdict` (confirmed / weak / none) and `coverage_verdict` (open / partial / covered_outside_marketplace)
- `verdict`: the research verdict (build / skip / maybe) if it was researched

For each candidate you are about to present, judge whether it matches one already saved. The names will not match exactly, so match on meaning, not string: "Customer service ticket prioritization" is the saved "Shopify-Native Support Triage for Solo Owners"; "Daily morning store health check" is "Unified Morning Dashboard".

If a candidate matches one already judged a dead end, do NOT present it as a fresh top opportunity. Dead end means any of:
- `coverage_verdict` = `covered_outside_marketplace` (already solved by tools outside the marketplace), or
- `demand_verdict` = `weak` or `none`, or
- research `verdict` = `skip`.

Drop it from the headline list. If it was among the strongest raw signals, add one demoted line so the user sees why it is gone: "Skipped [name]: already validated as [verdict] ([one-line reason])." Keep `partial` coverage and `build`/`maybe` verdicts in play (a partial gap can still have a wedge, as digital products did). Present only genuinely new candidates, or still-open ones, as fresh cards.

## Presenting and saving

First cross-reference prior verdicts (see the section above) so already-killed ideas do not reappear. Then present results as cards per `references/cards.md`. Then save the ones worth pursuing with `save-opportunity` (parameters in `references/tools.md`): set `name`, `type`, `ecosystem`, `problem`, `solution`, and pass the raw tool output as `signal_data` plus `source_signal` so the web app can show why it was flagged.

Save liberally when the user shows interest, lightly otherwise. Saving an opportunity does not commit the user to building it; it just adds it to their pipeline to investigate.

## Drill-down
After the first set of results, guide the user into the strongest signal:
- From a disruption target: `find-disruption-targets` with `focus: "specific_app"` on the incumbent, then `find-cross-ecosystem-patterns` for the same category, then `find-price-gaps` for pricing room.
- From a price gap: `find-disruption-targets` with `focus: "by_category"` to see if the same incumbents are also poorly rated.
- From an integration gap: `search-apps` for partial solutions, then check category pain points.

## Handoff
When the user picks one to pursue, point them to the next step:
- To pressure-test real demand before investing: "Want me to validate this against the web (forums, Reddit, standalone tools)? Just say validate [name]." (the `gapquery-validate` skill)
- To run the full build/skip research: "Want the full research with a build or skip verdict? Say research [name]." (the `gapquery-research` skill)
