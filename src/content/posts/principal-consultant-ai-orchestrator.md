---
title: "My AI Stunt Double: How I Run Multiple Engagements Without Losing the Thread"
date: 2026-05-20
description: "A private GitHub repo, a plain-text inbox, and Claude as orchestrator. The personal version of the system I recommend to teams."
draft: false
---

I wrote recently about [using GitHub as your team's AI memory](./github-as-nervous-system). The argument was: when context lives in the repo instead of someone's head, it compounds instead of evaporating. This is my personal version of that — the system behind how I operate.

---

## The Cost of Context Switching

Email gave everyone instant communication. Messaging made it faster. Now anyone can add something to your plate via email, Teams, Slack, WhatsApp, or a text. If you're running multiple projects, those interruptions add up.

At any given time I'm running multiple concurrent engagements. Each one is at a different point. One is in hypercare, another in mid-migration and one is wrapping up and transitioning support to another consultant. 

The switching cost is real. When I'm deep in one project and I get a message about a completely different client's deployment issue, I have to context-switch. Different stack, different contacts, different history and different open questions. To deal with it, I needed something that holds the operational layer so I can stay at the strategic layer.

## My AI To Do List System

I did what any consultant with too many open tabs would do — built a to-do list. But this one has an AI behind it.

Everything starts with a plain-text file called `inbox.md` in a private GitHub repository.

When something comes in — a message, a new action item, a credential I need to store, a reminder I don't want to lose — it goes in the inbox. No formatting, no categorisation, no friction. The only rule is: capture it before it disappears.

```markdown
# Inbox

- Client A: follow up Nathan on subscription access for new consultant
- Client B: check if T&M or drawdown, confirm with PM
- Article idea: write about this system
- New API key for X service: sk-...
```

That's it. It looks like a mess. It's supposed to.

## The Orchestrator

Once a day — usually in the morning, sometimes at the end of the day — I tell my AI orchestrator to process the inbox. In this case, it's just Claude, but it doesn't have to be. It reads every item and routes it:

- Engagement-specific work → the relevant private GitHub issue for that client
- Cross-cutting admin → a dedicated admin issue
- Credentials → the right file in the vault
- Notes → wherever they belong

I don't touch it. I just say "process my inbox" and review what it did.

The routing is the part that matters. Every engagement has its own GitHub issue — a living thread that accumulates context over time. Status updates, blockers, decisions made, next actions. It's not a ticket. It's a running log of everything that's happened and everything that's open.

## GitHub Issues as a Nervous System

I use private GitHub repositories for this. Not for code. For operational continuity.

Each engagement gets an issue. The issue body is the current state: what's done, what's open, what's blocked. Comments accumulate as things move. The full history is there if I need to reconstruct a timeline or hand over to another consultant.

```
$ gh issue list --repo myorg/engagements --state open

#4  Client D — Azure cost optimisation
#3  Admin & cross-cutting
#2  Side project
#1  Client A — Platform hypercare
```

That single command gives me the complete picture across everything I'm running. No dashboard, no project management tool, no subscription.

Why GitHub specifically: it's where my work already lives. Commits, PRs, infrastructure changes — it's all there. Keeping the operational layer in the same place means I can link an action item directly to a commit or a PR. Context stays connected.

## The Daily View

The issues are my source of truth. I also maintain a short daily view — a `today.md` that gets refreshed each morning. It's the things I'm actually going to work on today, pulled from the issues, trimmed to what matters right now.

The orchestrator handles the refresh. I read it, adjust if needed, and start working.

## What This Actually Unlocks

**Nothing drops.** When six things are moving simultaneously, the failure mode isn't being busy — it's forgetting. The inbox catches everything before it disappears into a conversation thread or a mental note I'll lose by afternoon.

**Context accumulates.** Three months into an engagement, the issue history has everything. Why a decision was made. What we tried that didn't work. Who was involved. When I need to brief a new consultant taking over support, the answer is: read the issue.

**I operate at a higher level.** The orchestrator holds the operational layer. I'm not managing my task list — I'm thinking about the client problems. The switching cost drops because the context is already there when I need it.

**It's legible to others.** A colleague can look at an issue and understand where an engagement stands without asking me. That's not just convenient — it's what makes the system durable.

---

I use Claude Code as the orchestrator. AI does the operational routing so I can do the work that requires judgement.

If you want to apply the same principles to an engineering team — shared context, persistent memory, AI-assisted continuity — [that's here](./github-as-nervous-system).
