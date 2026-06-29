---
name: gapquery-research
description: "Run deep build-or-skip research on a SPECIFIC app-marketplace opportunity using GapQuery's database of 35,600+ apps. Use this once the user has an opportunity in hand and wants a verdict: 'research this', 'deep dive on [name]', 'investigate [slug]', 'is it worth building', 'give me a build/skip verdict', 'analyze the market for [idea]', or 'save the research'. It runs a 6-area study (market, competition, revenue, technical, keywords, go-to-market), writes the full research record plus a summary brief, and sets the verdict. It does NOT discover new opportunities (use gapquery-discover for 'what should I build', gaps, disruption/pricing/integration scans) and does NOT do the web demand cross-check (use gapquery-validate). Do not use for writing application code or enriching app data (use gapquery-enrich)."
license: MIT
metadata:
  author: gapquery
  version: 2.1.0
---

# GapQuery Research

This skill takes one opportunity and produces a thorough, honest build-or-skip research record. The opportunity usually comes from `gapquery-discover` (saved as a candidate) and ideally has already been through `gapquery-validate` (a web demand check). Your job is the deep study and the verdict.

Discovery (finding what to build) and validation (the web demand check) are separate skills. If the user is still exploring what to build, hand back to `gapquery-discover`.

Follow `references/voice.md` for tone in everything you write and save (no em dashes, plain words, short sentences). For tool parameters, read `references/tools.md`.

## Skill version check
This skill's version is in the frontmatter. MCP tool responses include `latest_skill_version`. If a response shows a newer version, tell the user: "A newer version of the GapQuery skills is available. Run `npx skills add northify/skills -y` to update."

## Step A: Make sure the opportunity exists
1. Look it up with `list-opportunities` using the `slug` parameter.
2. If it does not exist, create it with `save-opportunity` (name, type, ecosystem, problem, solution), or ask the user to run discovery first.
3. Keep the `slug`; you need it to save research.

## Step B: Read the market reality first
The opportunity record may already have a `market_reality` block from `gapquery-validate` (demand verdict, coverage verdict, summary, cited web signals). It comes back in the `list-opportunities` detail.

- If it is present, build on it. Fold its demand and coverage findings into your Market Validation and Competition sections, and add its cited URLs to your `sources`. Do not repeat the same web searches it already ran.
- If it is absent, you may still proceed, but tell the user: "This has not been validated against the web yet. Running validate first (the `gapquery-validate` skill) usually makes the demand read more trustworthy. Want me to do that, or research anyway?" Then respect their choice.

## Step C: Research the 6 areas
Research thoroughly using web search, the GapQuery data, and your own reasoning. Do not fabricate numbers.

1. **Market reality and demand**: rough market size (how many people could realistically buy), the target customer, willingness to pay, growth trend, and timing. Lead with the `market_reality` findings if present.
2. **Competition**: direct competitors with their pricing, ratings, and rough user counts; indirect competitors (standalone tools, spreadsheets); what advantage a new entrant has; barriers to entry.
3. **Revenue**: specific pricing against competitor benchmarks, an MRR estimate with the math shown ("$8K at 200 users x $39/mo"), the pricing approach, and the key assumptions.
4. **Technical feasibility**: the APIs needed and whether they exist, build complexity, a realistic MVP time estimate, a stack suggestion, and the specific hard parts (rate limits, auth).
5. **Keywords**: the real terms people would search, volume signals, SEO difficulty, and a content angle.
6. **Go-to-market**: how you would get the first customers: launch plan, the best channels, partnerships, the first 90 days.

## Step D: Save the research (two calls)
Read `references/tools.md` for the full parameter list and the summary-brief shape.

**Save 1 (full record):** call `save-research` with the slug, verdict (`build`, `skip`, or `maybe`), executive summary, verdict reasoning, pros (3+), cons (2+), and all six analysis areas. Include at least 3 real `sources`: incumbent and competitor app-store pages, marketplace category pages, pricing pages, and any forum, Reddit, or blog threads about the pain (reuse the `market_reality` URLs here). A research record with no sources looks unsupported in the web app. If the response returns a `warnings` array about sources, add real citations rather than ignoring it.

**Save 2 (summary brief):** make a second `save-research` call with **only** `opportunity_slug` and `summary`. The tool detects this and writes just the summary, preserving the full record. A successful second call returns `mode: "summary_only"`. This brief powers the Quick Summary flyout in the web app; skipping it leaves the user staring at "no summary" after a full research run. Do not put the summary inside Save 1 and skip Save 2.

Then set the opportunity's status with `save-opportunity`: `build` or `maybe` to `interested` (set `build_complexity` from the technical analysis), `skip` to `dismissed`.

## Presenting
Write the verdict and reasoning in plain language (`references/voice.md`). Lead with the verdict and the one reason that drives it, then the strengths, then the honest risks. If the user asked to "save this" after seeing research on screen, run Save 1 and Save 2.
