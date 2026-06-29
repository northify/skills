---
name: gapquery-validate
description: "Pressure-test a single app-marketplace opportunity against the real world before investing research or build time. Use when the user wants to validate demand or check whether a gap is real: 'validate this', 'is there real demand for [idea]', 'is this already covered', 'reality check [slug]', 'will anyone actually want this', 'check the market for [name]', or after gapquery-discover surfaces a candidate. It searches the web (forums, Reddit, blogs) for evidence that people actually do this workflow, and checks whether standalone tools outside the marketplace already solve it, then saves a market-reality verdict to the opportunity. It does NOT discover new opportunities (use gapquery-discover) and does NOT run the full 6-area build/skip research (use gapquery-research). Do not use for writing code or enriching app data."
license: MIT
metadata:
  author: gapquery
  version: 2.1.0
---

# GapQuery Validate

A GapQuery signal tells you a category is thin or an incumbent is weak inside one marketplace. That is not the same as a real opportunity. Two things can fool you:

1. **Demand that reviews miss.** App-store reviews undercount demand. People describe a painful workflow on Reddit, in forums, and on blogs far more than they do in a 5-star review box. If nobody anywhere talks about doing this work, that is a warning.
2. **A gap that only exists inside the marketplace.** A category can be thin on, say, the Shopify app store while a mature standalone tool (or Zapier, or a spreadsheet template everyone uses) already solves it well. The gap is real in the marketplace but not in the wider market.

This skill does a fast web cross-check for both, then saves a `market_reality` verdict on the opportunity. It is the step between discovering a candidate (`gapquery-discover`) and committing to full research (`gapquery-research`). Keep it quick: a handful of searches, the strongest evidence, a clear verdict.

Follow `references/voice.md` for how you write (no em dashes, plain words). Read `references/web-research.md` for how to run the searches and assign the verdicts.

## Skill version check
This skill's version is in the frontmatter. MCP tool responses include `latest_skill_version`. If a response shows a newer version, tell the user to run `npx skills add northify/skills -y`.

## Step A: Load the opportunity
Look it up with `list-opportunities` using the `slug`. If it does not exist yet, either create it with `save-opportunity` (name, type, ecosystem, problem) or send the user to `gapquery-discover` first. You need the slug to save the result.

Note the workflow or pain at the heart of it. That phrase, not the marketing name, is what you search for.

## Step B: The two checks
Read `references/web-research.md` for query patterns and the verdict rubrics. In short:

**Demand check (the web).** Search forums, Reddit, blogs, and communities for people describing this workflow or pain in their own words. Use 3-5 searches, then read the most promising threads with WebFetch. Capture 2-3 verbatim quotes. Decide: `confirmed`, `weak`, or `none`.

**Coverage check (outside the marketplace).** Search for standalone tools, SaaS products, Zapier integrations, or common manual templates that already do this workflow outside the marketplace. Cross-reference the in-marketplace picture using `category-overview`, `find-disruption-targets`, or `search-apps`. Decide: `open` (nothing really does it), `partial` (some tools touch it but poorly, expensively, or without integration), or `covered_outside_marketplace` (a mature tool already does it well, so the gap is only inside the marketplace).

Keep quotes verbatim. They are evidence; do not clean them up.

## Step C: Save the verdict
Call `save-market-reality` with the slug and a `market_reality` object: the two verdicts, a 2-3 sentence plain-language summary, and 2-5 cited signals. Each signal is `{source, url, quote, type}` where type is `demand` or `coverage`. See `references/web-research.md` for the exact shape. The summary follows `voice.md`; the quotes stay verbatim.

## Step D: Tell the user and hand off
Lead with the two verdicts and what they mean for the decision. Be honest: a `covered_outside_marketplace` result is a yellow flag worth saying plainly, not burying. Then point to the next step:
- Looks real: "Demand checks out and the gap holds. Want the full build/skip research? Say research [name]." (the `gapquery-research` skill)
- Looks weak or already covered: say so, and suggest the user reconsider or look at other candidates in `gapquery-discover` before sinking research time in.
