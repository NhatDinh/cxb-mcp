# Creators x Brands (CxB) MCP server

Find real UGC creators who are a brand's actual customer: an ER nurse for a nurse app, an account executive for a sales tool, a parent for a kids' product. Each one is checked from their own bio and posts, and every match quotes the line that proves it.

- **Remote MCP server** (Streamable HTTP): `https://creatorsxbrands.com/mcp`
- **3 free searches** with a free account. No card.
- **Site and setup page:** [creatorsxbrands.com](https://creatorsxbrands.com) · [creatorsxbrands.com/connect](https://creatorsxbrands.com/connect)
- **Official MCP Registry:** `com.creatorsxbrands/cxb`

This repository is the documentation for a hosted service. There is nothing to install or run yourself.

## What it does

1. You describe a customer in plain words: "ER nurses who work nights", "account executives at SaaS companies", "parents of kids with food allergies".
2. CxB answers at once with creators it has already checked who fit, then runs a fresh search on TikTok. That usually takes 1 to 2 minutes.
3. Only exact fits come back. For each: who they are, the quoted evidence, followers, typical views, days since their last post, their best video, and how to reach them.

A creator counts as a match only if:

- every must-have shows in their own bio or posts,
- they posted in the last 30 days, and
- their typical views are at least 0.3 times their followers.

A must-have that nothing in their public posts shows counts as not met.

## Quick start

**1. Get your personal link.** Sign up at [creatorsxbrands.com/connect](https://creatorsxbrands.com/connect) with your email. You get a link like `https://creatorsxbrands.com/mcp/cxb_...`. It works like a password, so don't commit it or paste it in public.

**2. Add it to your AI.**

Claude Code:

```bash
claude mcp add --transport http cxb https://creatorsxbrands.com/mcp/cxb_YOUR_KEY
```

Codex:

```bash
codex mcp add cxb --url https://creatorsxbrands.com/mcp/cxb_YOUR_KEY
```

Cursor (`~/.cursor/mcp.json`):

```json
{
  "mcpServers": {
    "cxb": { "url": "https://creatorsxbrands.com/mcp/cxb_YOUR_KEY" }
  }
}
```

VS Code:

```bash
code --add-mcp '{"name":"cxb","type":"http","url":"https://creatorsxbrands.com/mcp/cxb_YOUR_KEY"}'
```

Claude, ChatGPT, Grok and Gemini: add the link as a custom connector. The clicks for each are on the [setup page](https://creatorsxbrands.com/connect).

Prefer a header to a key in the URL? Use `https://creatorsxbrands.com/mcp` with `Authorization: Bearer cxb_YOUR_KEY`.

**3. Ask.**

> Use CxB to find 5 TikTok creators who are ER nurses who work nights. For each one, show the quote that proves they fit, their typical views, their best video and how to reach them.

## Tools

| Tool | What it does |
|---|---|
| `find_creators` | Starts a search. Returns creators already checked right away, plus a `job_id` for the fresh search. Needs `who`; optional `must`, `nice`, `followers`, `country`, `brand`, `product_url`, `max_matches`, `plan`. |
| `get_job` | Status and results of a search. Waits up to 45 seconds for a running search, so an agent gets the matches in the same turn. |
| `get_account` | Free. Which account this connection belongs to, the plan, free searches left, and the links to upgrade. Call it to confirm a new connection works. |
| `request_outreach` | Done for you: CxB contacts creators with your brief and comes back with who said yes and their rates. Needs `who`, `budget`, `timeline`. A person replies with a quote; nothing goes to a creator until you accept it. |
| `get_request` | Status of an outreach request. |

## Without an account

The server still answers. `find_creators` returns up to 5 creators CxB has already checked, without contacts, and a signup link to give the user. No search is started and nothing is stored. See [`examples/find_creators-without-account.json`](examples/find_creators-without-account.json) for a real answer.

Other answers you may see in place of a search:

| `status` | Meaning |
|---|---|
| `sign_in_needed` | No account on this connection. Give the user the `signup_url`. |
| `out_of_free_searches`, `payment_needed` | Give the user the payment link in the answer. The plan turns on within a minute of paying. |
| `free_full` | Free searches are full for today. The account's own 3 are untouched. |
| `busy` | Other free searches are running. Try again in 2 minutes. |
| `fair_use_wait` | A paid plan's window is used up. The answer says when it reopens. |

## REST API

The same tools over HTTP, with the same key. Full description: [openapi.json](https://creatorsxbrands.com/openapi.json).

```bash
curl -X POST https://creatorsxbrands.com/api/v1/searches \
  -H "Authorization: Bearer cxb_YOUR_KEY" \
  -H "content-type: application/json" \
  -d '{"who": "ER nurses who work nights"}'

curl "https://creatorsxbrands.com/api/v1/searches/JOB_ID?wait_seconds=45" \
  -H "Authorization: Bearer cxb_YOUR_KEY"
```

## Pricing

| Plan | Price | What you get |
|---|---|---|
| Free | $0 | 3 searches per account, up to 5 creators each. |
| Solo | $27/month | Unlimited searches, fair use 3 fresh searches every 5 hours, every exact fit with contacts. |
| Pro | $97/month | The same with 10 fresh searches every 5 hours. |
| Done for you | Quoted per order | CxB reaches out and brings back the creators who said yes, with their rates. |

## How well it works

On 8 real buyer briefs, graded by one judge that did not know which tool found each creator, CxB found 34 creators who are the customer and still get watched. Claude working alone found 6, Influencers.club 2, Exa 1. Charts, every brief and the open data (CC BY 4.0): [creatorsxbrands.com/benchmarks](https://creatorsxbrands.com/benchmarks).

## Limits

- Fresh searches cover TikTok only for now.
- Creators shown are public profiles found from what they post. They are not affiliated with CxB. A creator who wants a profile changed or removed can email hello@creatorsxbrands.com.

## Links

- Notes for AI agents: [llms.txt](https://creatorsxbrands.com/llms.txt)
- About: [creatorsxbrands.com/about](https://creatorsxbrands.com/about) · Privacy: [creatorsxbrands.com/privacy](https://creatorsxbrands.com/privacy) · Terms: [creatorsxbrands.com/terms](https://creatorsxbrands.com/terms)
- Contact: hello@creatorsxbrands.com
