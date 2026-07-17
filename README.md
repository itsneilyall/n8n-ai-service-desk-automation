# AI MSP Service Desk

**An AI-powered IT support ticket automation built in n8n** — classifies incoming tickets, resolves common issues automatically using a verified knowledge base, and escalates anything higher-risk to a human, with manager approval required in Slack before any high-priority action is taken.

<img width="1637" height="701" alt="AI Service Desk Platform" src="https://github.com/user-attachments/assets/90c7957f-bb84-4567-8a68-2ad7a780d03c" />


---

## Overview

The AI MSP Service Desk is an end-to-end IT support automation built in n8n that classifies incoming customer tickets, resolves common issues automatically using a verified knowledge base, and escalates anything higher-risk to a human, with manager approval required in Slack before any high-priority action is taken. It matters because it removes the repetitive Level 1 triage work that eats up a support team's day, while keeping a human firmly in control of anything with real business impact.

---

## Problem & Solution

**Problem**

MSP support teams spend a large share of their day manually reading, classifying, and responding to routine tickets — things like password resets, VPN issues, or license errors — that follow the same known fix every time. This manual triage is slow, inconsistent between technicians, and pulls skilled engineers away from harder problems. At the same time, fully automating ticket resolution is risky: an AI that improvises technical fixes or acts on high-stakes issues without oversight can cause more damage than it prevents.

**Solution**

I built a workflow where an AI classifier and decision engine handle triage and resolution, but only using solutions documented in an approved knowledge base — never invented on the fly. Low and medium priority tickets with a known fix are resolved and emailed to the customer automatically. Anything without a clear match, or in a high-risk category like security or hardware, escalates straight to a human. And for high-priority tickets that could be resolved automatically, the workflow pauses and requires a manager's explicit approval via interactive Slack buttons before the customer is contacted — a human-in-the-loop safeguard exactly where it matters most.

---

## How It Works

```
Customer Form Submission
        │
  Generate Ticket ID
        │
  AI Classifier          → category / priority / confidence
        │
  Knowledge Base Search  → Google Sheets
        │
  AI Decision Engine     → resolve or escalate?
        │
   ┌────┴─────────────────────────┐
   │                               │
 RESOLVE                       ESCALATE
   │                               │
 High/Critical priority?       Email customer
   │         │                 Slack #escalations
  YES        NO                Update ticket + log
   │         │
 Slack     Email customer
 Approval  Slack #service-desk
 (buttons) Update ticket + log
   │
 Approved  → resolve path
 Rejected  → escalate path
```

1. **Intake** — a customer submits a ticket via an n8n Form Trigger (name, company, email, problem description)
2. **Classification** — an AI agent (GPT-4o-mini) tags the ticket with category, priority, and confidence
3. **Knowledge base check** — the ticket is matched against documented fixes stored in Google Sheets
4. **Decision** — a second AI agent decides whether the ticket can be safely auto-resolved, grounded strictly in the KB (no hallucinated fixes)
5. **Human approval gate** — High/Critical priority tickets pause here; a manager approves or rejects via native Slack buttons before anything is sent
6. **Resolution or escalation** — the customer receives an email either way; unresolved tickets alert the support team in Slack
7. **Logging** — every ticket and execution event is recorded in Google Sheets, with retry logic and a dedicated error-handling lane for failed notifications

---

## My Role

- Designed the end-to-end workflow architecture, mapping the full ticket lifecycle from intake through resolution, escalation, and logging
- Built and configured all 30+ n8n nodes, including two AI agents, conditional routing logic, and integrations with Google Sheets, Gmail, and Slack
- Engineered the AI prompts for classification and decision-making, including explicit priority rules and a knowledge-base-grounding rule to prevent hallucinated fixes
- Implemented production-readiness features: retry logic with a dedicated error-handling lane, structured logging, and a native Slack approval flow for human oversight
- Diagnosed and resolved multiple real-world integration issues (credential mapping, webhook resume behavior, Slack interactivity configuration) through iterative testing
- Authored two audience-specific documentation deliverables: a technical reference for engineers and a plain-language SOP for end users and managers

---

## Tech Stack

- **n8n** — workflow orchestration (self-hosted)
- **OpenAI GPT-4o-mini** — ticket classification and automated decision-making
- **Google Sheets** — knowledge base and ticket database
- **Gmail** — automated customer communication
- **Slack** — team alerts, error notifications, and interactive approval buttons

---

## Key Outcomes

- Each auto-resolved ticket saves an estimated **10–15 minutes** of manual technician work, translating to **over 12 hours of support time saved per week** at typical ticket volume
- **Zero-hallucination resolution** — all automated fixes are grounded in a documented knowledge base, eliminating a key risk of AI-driven support
- **Built-in human oversight** for high-priority tickets via Slack approval, ensuring no high-stakes customer communication goes out without sign-off
- **Full audit trail** — every ticket and execution event is logged automatically, with a dedicated error-handling path so failed notifications are never silently dropped

---

## Repository Structure

```
ai-msp-service-desk/
├── README.md
├── workflow/
│   └── AI-MSP-Service-Desk-Production-Ready.json   # importable n8n workflow
├── docs/
│   ├── Technical-Documentation.pdf                  # full technical reference
│   └── IT-Service-Desk-SOP.pdf                       # end-user / manager SOP
├── knowledge-base/
│   └── KnowledgeBase-sample.csv                      # sample KB for testing
                      
```

---

## Setup

Full setup instructions, including required Google Sheets structure, credential names, and Slack configuration, are in [`docs/Technical-Documentation.pdf`](docs/Technical-Documentation.pdf).

**Quick start:**
1. Import `workflow/AI-MSP-Service-Desk-Production-Ready.json` into n8n
2. Create the two required Google Sheets (Knowledge Base + Ticket Database) — see technical docs for exact tab names and columns
3. Load `knowledge-base/KnowledgeBase-sample.csv` into the KnowledgeBase tab
4. Connect credentials for OpenAI, Google Sheets, Gmail, and Slack
5. Enable Slack Interactivity for the approval buttons (see technical docs, Section 9)
6. Activate the workflow and submit a test ticket via the form

---

## Documentation

- 📘 [Technical Documentation](docs/Technical-Documentation.pdf) — full architecture, node reference, AI prompt design, and troubleshooting
- 📗 [Service Desk SOP](docs/IT-Service-Desk-SOP.pdf) — plain-language guide for employees, managers, and new hires

---

