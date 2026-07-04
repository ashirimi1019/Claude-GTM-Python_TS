# Claude GTM Engine

Signal-driven outbound GTM engine built at CirrusLabs (this is the sanitized public version). It turns hiring, funding, and tech-stack signals into researched target accounts, verified buyer contacts, and personalized outreach drafts, with a human review gate in front of anything that leaves the system.

## Architecture

Three stages, each producing artifacts the next stage consumes:

1. **Research** - agents build a positioning and campaign strategy for each offer, then research target companies: what they do, what they are hiring for, and why the offer is relevant right now.
2. **Buyer discovery** - signal routing picks the right provider per signal type (TheirStack for hiring signals, Exa for funding and news, Apollo for company search and contact enrichment), then ICP-scores companies before any contact enrichment so credits are never spent on accounts that will not qualify.
3. **Drafts** - outreach copy is generated from the research artifacts, never from a blank prompt, and personalized per contact.

A FastAPI backend orchestrates six skills and a four-agent optimization layer (`app/`, `core/skills`, `core/agents`), with services for ICP scoring, deduplication, geography filtering, and personalization (`services/`). A Next.js frontend drives campaign setup and review. Both share a Supabase database.

## The Human Review Gate

Nothing sends without approval. Copy runs through a tiered guardrail system (`core/guardrails`): automated checks catch length violations, unfilled placeholders, and hallucination markers; anything flagged (aggressive CTAs, competitor comparisons) is held for explicit human approval before it can be enrolled in a sequence. Agent recommendations follow the same pattern: only actions marked safe auto-apply, the rest wait in an approval queue. AI does the volume; a person owns the judgment.

## Stack

- **Backend:** Python / FastAPI, Celery + Redis, SSE streaming for live run logs
- **Frontend:** TypeScript, Next.js, Tailwind
- **Data:** Supabase (Postgres)
- **LLMs:** Claude and OpenAI
- **Signals and enrichment:** TheirStack, Exa, Apollo

## Status

In production: 100+ campaigns run through the pipeline, with human-gated sends, bounce-rate circuit breakers, and gradual enrollment ramps throughout.
