# ACME Assist

One assistant for everyone at ACME (A Company Making Everything): customers on acme.com and every
employee from the stockroom to the C-suite. It's built with LangChain `create_agent` on Google's Gemma
models, and it orchestrates 12 specialist agents over the ACME Systems API.

> **Status:** in production, unowned. The engineer who built it, Dave, has left.
> Start with [`HANDOFF_NOTES.md`](HANDOFF_NOTES.md), then your manager's note in
> [`docs/inbox/KICKOFF.md`](docs/inbox/KICKOFF.md).

## Quick start

You'll need [uv](https://docs.astral.sh/uv/) and a Google AI Studio API key.

```bash
uv sync
cp .env.example .env      # then put your keys and the API URL in .env
uv run acme               # chat in the terminal
```

ACME Assist talks to the **ACME Systems API**, which your instructor hosts. Set `ACME_API_URL` in `.env`
to the URL they give you. The API's interactive docs are at `$ACME_API_URL/docs`.

## What's in here

| Path | What it is |
|---|---|
| `acme_assist/orchestrator.py` | **ACME Assist**, the main agent: routing, shortcut tools, middleware |
| `acme_assist/agents/` | 12 specialist agents (orders, returns, catalog, stock, promos, account, hr, it, procurement, analytics, marketing, cs_desk) |
| `acme_assist/tools/` | Tools that call the ACME Systems API |
| `acme_assist/middleware.py` | Audit logging, staff detection, the dynamic system prompt, tool error handling |
| `acme_assist/prompts/acme_assist.md` | The orchestrator's system prompt |
| `acme_assist/cli.py` | The `rich` terminal client |
| `data/kb/` | Policy documents (returns, warranty, PTO, IT runbooks...) |
| `data/personas.json` | Demo customers and employees, used by the CLI login menu |
| `docs/` | Architecture, and the inbox: your manager's notes and the complaints about ACME Assist |
| `evals/` | Where your eval datasets and evaluators go |
| `tests/` | pytest tests (there's one) |

## Observability

ACME Assist is meant to send traces to LangSmith (`LANGSMITH_TRACING=true` and `LANGSMITH_API_KEY` in
`.env`, project in `LANGSMITH_PROJECT`). The CLI banner shows whether tracing is on.

## Rate limits

The Gemma free tier allows **30 requests per minute per API key**. One chat turn can easily make 4–10
model calls (orchestrator plus specialists).

## Troubleshooting

- *The assistant says it can't find anything:* check that the API is reachable, e.g.
  `curl $ACME_API_URL/health`.
- *Data looks different from yesterday:* it is. The API runs on a live clock: orders keep arriving and
  packages keep moving. Everyone in the class shares the same data, so other people's changes show up too.
