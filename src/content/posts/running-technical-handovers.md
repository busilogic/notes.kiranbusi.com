---
title: "How I Run Technical Handovers"
date: 2026-04-14
description: "A handover isn't a farewell ritual. Done well, it's the best opportunity you'll get to surface tribal knowledge, close gaps, and de-risk the engagement before the next thing goes wrong."
draft: true
---

Most technical handovers are rushed. Someone announces they're leaving, a replacement is found, there's a frantic week of knowledge transfer, and then the outgoing engineer is gone and the new one is quietly drowning. Three months later, the team discovers something critical that only the previous person knew.

I've run enough handovers — as a team lead, as the incoming engineer, and as the engagement owner sitting above both — to have a view on how to do this better. The short version: treat it as an audit, not a farewell.

## The timeline shapes everything

Before you plan anything, you need to know how long you actually have. And this varies significantly depending on where your team is based.

In Australia, standard notice periods are four weeks. In India, it's typically two to three months — sometimes longer for senior engineers. This isn't just a logistical detail. It fundamentally changes what kind of handover is possible.

With a month, you're doing triage. You identify the highest-risk knowledge gaps and focus everything there. Documentation is minimal — you don't have time for comprehensive wikis. You're optimising for continuity of the most critical functions.

With three months, you can run a proper overlap. The outgoing engineer can shadow, then lead together, then support the incoming engineer as they lead. You can produce documentation that will actually be useful twelve months from now. You can surface the edge cases — the things that only come up once a quarter but will break something important when they do.

The mistake I see most often is teams with three months of runway operating like they have four weeks. They start the handover in month two. They've wasted the most valuable asset they had.

## Start the moment you know

The day someone announces they're leaving — or the day you hire their replacement — is the day the handover starts. Not the week before their last day.

The first thing I do is a knowledge inventory. Not a formal audit with a spreadsheet (though that comes later), but a conversation: *What are the five things that would go wrong in the next six months if you just disappeared today?* Most engineers can answer this in ten minutes. Those five things are your handover backlog.

From there, I build a simple matrix: what does the incoming engineer need to know, how critical is it, and how complex is the knowledge to transfer? Some things are quick — credentials, access, runbooks. Others take weeks of working alongside someone to absorb — client relationship dynamics, the unwritten rules of how a particular stakeholder communicates, why a decision was made the way it was two years ago.

The complex, high-criticality items go into the overlap calendar immediately. The simple items get documented in the first week and checked off.

## Documentation as de-risking, not administration

Most engineers hate writing documentation. The framing I've found that actually works: you're not writing for the incoming engineer. You're writing for yourself at 2am six months from now when something breaks and they call you.

That reframe makes people write differently. They include the things they'd actually need — the specific error that means the certificate is about to expire, the client contact who actually knows how to get things done versus the one who's officially responsible, the workaround for the thing that should work but doesn't.

The format that works best for technical handover documentation isn't a wiki. It's a decision log. For every significant technical decision made during the engagement, record: what was decided, what the alternatives were, and why this option was chosen. This is the hardest knowledge to transfer and the most valuable. When the new engineer inherits a system and wonders why something is built the way it is, a decision log gives them the answer. Without it, they'll either assume it was arbitrary and change it (breaking things), or assume it was deliberate and never question it (accumulating debt).

## The tribal knowledge problem

Tribal knowledge is information that lives in someone's head rather than in any system. It's the most dangerous category in a handover because it's invisible until it's gone.

It takes three forms:

**Relationship knowledge** — who the real decision maker is (not the org chart one), who to call when the official channel is blocked, which stakeholder needs to be kept informed even when they're not officially involved. This is especially important in client-facing roles. New engineers can have the technical skills and still fail engagements because they don't understand the human topology.

**Context knowledge** — why the system is the way it is. What was tried and didn't work. What the client asked for that was refused and why. What the constraints were that shaped the architecture. Without this, incoming engineers make locally rational decisions that are globally wrong.

**Operational knowledge** — the things you just know after running something for a while. The deployment that needs to happen in a particular order. The check that should be run before the monthly maintenance window. The thing that looks like an error but isn't.

The way to surface tribal knowledge is to run incident simulations. Give the incoming engineer a scenario — something that has actually gone wrong, or something you can imagine going wrong — and watch how they'd handle it. The gaps in their response show you exactly what hasn't been transferred yet. Do this early enough that you have time to fill the gaps.

## What a good handover structure looks like

Assuming a three-month runway:

**Month one: shadow and document.** The outgoing engineer leads everything. The incoming engineer observes, asks questions, and writes. Every meeting, every client interaction, every deployment. The outgoing engineer reviews the incoming engineer's documentation and corrects it. By the end of month one, you have a baseline knowledge base and the incoming engineer has context.

**Month two: lead together.** They share responsibility. The incoming engineer starts leading client interactions and technical decisions, with the outgoing engineer present and available. The outgoing engineer's job is to let the new person make mistakes that are recoverable, and to intervene on the ones that aren't. This is the phase where the real knowledge transfer happens — not through documents, but through doing.

**Month three: independent with support.** The incoming engineer leads. The outgoing engineer is available for questions but not present in meetings. By week two of month three, any question the incoming engineer has that the outgoing engineer is still the only one who can answer is a gap you need to close before day one of independence.

With a one-month runway, compress this to: week one is documentation sprint, week two is shadow, weeks three and four are lead together. Accept that month three doesn't exist and plan accordingly — lighter workload for the incoming engineer in their first month of independence, and a maintained line to the outgoing engineer for at least sixty days post-departure.

## Handovers reveal your real documentation debt

Here's the uncomfortable thing a handover will show you: the state of your documentation is usually worse than you thought.

I've started treating every handover as an audit of the engagement's operational maturity. How long does it take to onboard someone new? That's your onboarding complexity score. What questions does the incoming engineer ask that no document can answer? Those are your documentation gaps. What breaks in the first month of their independence? Those are your brittleness points.

A team that can hand over cleanly in four weeks is a team that has been running well. A team that needs six months of overlap and still has significant gaps has been running on tribal knowledge and individual heroics. The handover doesn't create the problem — it reveals it.

If you lead technical teams, run a handover drill every twelve months whether or not anyone is leaving. Pick a senior engineer and ask: if you disappeared tomorrow, what would break and when? The answer will tell you more about your operational risk than any audit.

## The mindset shift that changes everything

The engineers I've seen do handovers well share one thing: they view the incoming engineer's success as their own legacy, not as a threat to their indispensability.

Some engineers — unconsciously, usually — leave incomplete handovers because being the only one who knows something feels like job security. It isn't. What it actually does is tether you to a role you've left, make you the person who gets called at 11pm six months after you've moved on, and build a reputation as someone who hoards knowledge.

The engineers who hand over well become the people teams want to hire again. They're trusted with more complexity, more autonomy, and more scope — because their leaders know that when they eventually move on, they'll leave the place better than they found it.

That's the standard worth holding yourself to.
