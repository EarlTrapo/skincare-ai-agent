# GLOW Skincare Store AI Assistant — Telegram

> Built with n8n + Claude API + Telegram Bot API.
> A production-ready AI customer handling agent with two-tier escalation and human handoff — deployed for a skincare brand.

[Demo on X](https://x.com/0xTrapo/status/2029489985796153524) 
[Human escalation Demo](https://x.com/0xTrapo/status/2029508195731878035)

---

## What It Does

GLOW's customers message on Telegram. This workflow handles them end-to-end — product questions, recommendations, complaints — and knows exactly when to stop handling and get a human involved.

No missed messages. No overwhelmed staff. No cold hand-offs.

**Core behaviour:**
- Receives any Telegram message and immediately classifies intent
- Runs a keyword escalation check before the AI even sees the message (Tier 1)
- Passes clean, structured context into Claude Sonnet via a dynamic Prompt Builder
- Parses Claude's XML-structured output to extract reply, intent, and handoff decision
- Routes the response: direct AI reply OR escalation path with dual alerts (customer + agent)

---

## Architecture — Node by Node

```
Telegram Trigger
  → Input Processor         (normalise message, extract chat ID + user data)
  → Escalation Check        (Tier 1: keyword match — refund / legal / urgent)
      ├── true  → Alert customer of escalation + Keyword Alert to Agent
      └── false → Prompt Builder
                    → AI Agent (Claude Sonnet)
                        → Cleanup Code        (regex strip XML tags)
                        → Response Parser     (extract reply, intent, readyToHandoff)
                        → AI Escalation Check (Tier 2: IF readyToHandoff = true)
                            ├── true  → Escalation Message Builder
                            │             → Send Customer Acknowledgement
                            │             → Alert Human Agent
                            └── false → Send AI Reply
```

**Two-tier escalation logic:**
- **Tier 1 (keyword gate):** Fires before the AI. Catches hard stops — words like "refund", "lawyer", "urgent". Customer gets an immediate acknowledgement, agent gets a Telegram alert with full context.
- **Tier 2 (AI judgment):** Claude decides mid-conversation when a situation is beyond its scope — complex complaints, unresolved frustration, explicit human requests. Sets `readyToHandoff = true` in its XML output. The IF node catches it and routes accordingly.

---

## Stack

| Layer | Tool |
|---|---|
| Automation platform | n8n |
| AI model | Claude Sonnet (Anthropic API) |
| Channel | Telegram Bot API |
| Output format | XML-structured Claude responses |
| Human handoff | Telegram agent alert |

---

## How to Import

1. Download `Skincare Store AI Assistant – Telegram.json` from this repo
2. Open your n8n instance → **Workflows → Import from file**
3. Drop the JSON in
4. Set your credentials: Telegram Bot token, Anthropic API key
5. Update the Prompt Builder node with your product catalogue / brand context
6. Activate

---

## What Makes This Production-Ready

Most Telegram bots are just wrappers. This one has:

- **Structured output parsing** — Claude responds in XML tags, not freeform text. The Cleanup Code node extracts fields reliably without brittle regex hacks.
- **Seven-state conversation awareness** — the AI tracks conversation stage across turns using memory passed via the Prompt Builder.
- **Dual escalation paths** — the customer never gets silence. Both escalation routes send an immediate acknowledgement before alerting the agent.
- **Separation of concerns** — each node has one job. Easy to debug, easy to adapt to a new client.

---

## Files

```
skincare-ai-agent/
├── Skincare Store AI Assistant – Telegram.json   ← import this into n8n
├── README.md
└── assets/
    └── workflow-screenshot.png
```

---

## Built By

Elvis — AI automation builder. I build client-facing systems with n8n and Claude.

GitHub: [github.com/EarlTrapo](https://github.com/EarlTrapo)

X/Twitter: [0xTrapo](https://x.com/0xTrapo)
