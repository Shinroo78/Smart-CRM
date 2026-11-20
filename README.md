# 🍽️ Smart CRM — Chat-Driven Menu Assistant (n8n)

![Status](https://img.shields.io/badge/status-working%20workflow-brightgreen)
![Type](https://img.shields.io/badge/type-n8n%20chatbot-blue)
![Platform](https://img.shields.io/badge/platform-n8n-orange)
![AI](https://img.shields.io/badge/AI-OpenAI%20gpt--4.1--mini-blueviolet)
![Storage](https://img.shields.io/badge/storage-n8n%20Data%20Tables-yellow)
![License](https://img.shields.io/badge/license-MIT-green)

A **chat-first n8n workflow** that turns a small **"Menu" data table** into a conversational mini-CRM. Users talk to the agent in natural language; the agent **adds records** via the `Insert` tool and **retrieves records** via the `Get` tool — and it refuses to do anything outside that scope.

> ⚠️ **This is a narrow-scope assistant.** It is designed to manage one data table (`Menu`) — nothing more. It is not a general-purpose chatbot, not a full CRM, and not secured for public exposure.

---

## ⚠️ What this project is and is not

### ✅ What this is
- A **working n8n workflow** using the **AI Agent** node with **tools**, **memory**, and a **chat trigger**.
- A **tool-using agent** — the LLM decides *when* to call `Insert` or `Get`, then reads the tool results back into the conversation.
- A **stateful chat** thanks to `Simple Memory` (window buffer) — the agent remembers earlier turns in the session.
- A **scoped assistant** — the system prompt explicitly forbids chatting outside the menu-management scope and forces **short answers**.
- A **tabular renderer** — when you ask for a list, the agent returns a **markdown table**.

### ❌ What this is not
- ❌ Not a full CRM — no update, no delete, no contacts, no pipeline, no auth, no roles.
- ❌ Not multi-tenant — one data table, one dataset.
- ❌ Not secured — the chat trigger webhook is **public** by default (unless protected).
- ❌ Not a database — it relies on **n8n Data Tables**, which have their own limits and availability.
- ❌ Not validated — duplicate IDs, missing names, and bad prices are **not** enforced at the schema level.

---

## 🎯 Purpose

Small teams often have a list — a **menu**, a **price list**, a **catalog** — that lives in a spreadsheet and gets edited by asking a colleague. That "colleague" can be replaced by a chat interface if:

1. The model can **read** from the list.
2. The model can **write** to the list.
3. The model is **forbidden** from doing anything else.

This workflow demonstrates exactly that pattern with two tools (`Insert`, `Get`) over one `Menu` data table.

> **Ask it "show me the menu" → get a table. Ask it "add Latte at 4.50 with id 12" → it inserts.**

---

## 🚀 Quick start

### 1. Import the workflow
```text
n8n → Workflows → ⋯ menu → Import from File → select Smart CRM.json

Delete — remove an item

Search — filter by name or price range

Then update the system message so the model knows about them.

Change the model
Open the OpenAI Chat Model node → change gpt-4.1-mini to gpt-4.1, gpt-4o, or another supported model.

Tighten the system prompt
If the agent drifts off-topic, add explicit refusals, e.g.:

text
If the user asks anything not related to menu items, reply:
"Sorry, I can only help with menu records."
Point at a different table
Re-select the data table in both Insert and Get, then update the column names in the system message and in $fromAI(...) values.

Protect the chat trigger
Add Basic Auth or an API key check in front of the webhook.

Or restrict the webhook to internal n8n chat only.

⚠️ Known limitations
No Update / Delete tools. The agent can only add and read.

No duplicate protection. Inserting MenuID=12 twice creates two rows.

No schema validation. The model can insert empty names or negative prices.

No authentication on the chat trigger. Anyone with the webhook URL can talk to the agent.

Memory is per-session, not persistent. Restarting n8n or resetting the session clears it.

returnAll is AI-controlled. A poorly-worded prompt may fetch the entire table.

"Id" naming mismatch. The system prompt says "Id" / "Name", the table uses MenuID / MenuName — the model usually maps them correctly, but it's a source of confusion.

No rate limiting or cost control on OpenAI calls.

n8n Data Tables may be limited in self-hosted setups depending on version.

🐛 Troubleshooting
Issue	Solution
Agent chats about unrelated topics	Strengthen the system prompt with explicit refusals
Insert does nothing	Re-select the Menu data table in the node
Get returns everything	Instruct the model to pass returnAll=false when a specific item is asked
Wrong field mapping	Align the table column names with the $fromAI(...) keys
Chat trigger not responding	Verify the workflow is Active (or use the in-editor chat)
Memory not working	Confirm Simple Memory is connected as ai_memory to the agent
📁 File structure
text
.
├── Smart CRM.json    # n8n workflow export
└── README.md         # This file
🤝 Contributing
Fork the repository

Create a feature branch

Submit a pull request

📄 License
MIT License. See LICENSE file for details.

Disclaimer: The chat trigger is public by default. Do not expose this workflow to the open internet without adding authentication and rate limiting.

📞 Support
Open a GitHub issue
