# Opportunity Card Format

How to present discovery results. The tools hand you signals (raw facts about what is missing or weak). Your job is to turn each into a short, concrete pitch a builder can act on. Follow `voice.md` for tone (no em dashes, plain words).

## The rules
1. Never use a table for discovery results. Tables are only for raw-data requests.
2. Pick the top 3-5 results. Do not dump every row.
3. Each card reads like a mini pitch. If a field would fit in a table cell, it is too shallow.
4. After presenting, offer to validate or deep-research a specific one.

## Card structure
Adapt the header and fields to the opportunity type, but always write substantive, multi-sentence content for each field.

```
### 1. [Creative Name]: [What it is]
**Type**: [Integration / Disruption target / Pricing gap / Underserved / Industry gap / Negative space] | **Key metric**: [the one stat that matters]

**The opportunity**: 2-3 sentences. What would you build? Describe the actual
workflow, not "sync data." What triggers what, and what data moves where?

**Why there's demand**: Evidence. User counts, review complaints, workaround
mentions, install numbers. Not just "no app exists yet."

**Evidence** (negative-space cards only): At least one direct quote from the
tool's excerpts array, formatted as:
> "[exact insight_text]": [source_app] · [insight_type, confidence]
Then 1-2 sentences on what the user is actually doing today.

**Current workaround**: How people solve this now, and why it hurts. Be specific:
how many hours, how often. Use the workaround metadata (manual_step,
frequency_signal) when present.

**Target persona**: A specific person. Job title, company size, how often they
hit this, what they would pay to fix it.

**Why now**: What changed recently that opens a window? Why has nobody built it?
```

## Negative-space cards: quote the evidence
If a negative-space row includes `excerpts`, you must quote at least one in the Evidence line. A negative-space card with no quoted user voice is the exact "vague workflow" failure we are fixing. If the excerpts array is empty (rare), say so plainly: "No matching review insights yet, so this workflow is inferred from category names alone."

## Synthesis patterns
Apply these when turning signals into cards:
- **Workaround to product**: a manual step becomes a feature. "Users export CSV by hand" becomes "automatic sync, no CSV."
- **Feature-level drill-down**: do not stop at "build a better X." Use the pain points and `ai_description` data to name the 2-3 specific broken features.
- **Persona drill-down**: narrow "users who need X" to the one persona who feels it most. "Freelance bookkeepers invoicing 20+ clients a month" beats "QuickBooks users."
- **Timing**: when temporal signals show growth, say why now. "Category grew 30% while the top app's rating fell. The window is open."
- **Adjacent intersection**: look for gaps where two categories or an industry and a category meet.

## Use your own judgment
The tool data is the starting point, not the answer. For every card:
1. Skip false positives: low ratings caused by inherent complexity, data moats, or tiny markets are not openings.
2. Explain the pain in specifics, using what you know about common complaints in the category.
3. Be honest about buildability: could a solo dev or small team realistically ship this?
4. Add market context: platform size, what competitors charge, what changed recently.
