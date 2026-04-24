---
title: "The Foundational Step Most Teams Skip When Adopting AI"
date: 2026-04-24
description: "Most teams are getting a fraction of what they should from AI tools."
---

Most teams I work with have adopted AI tools. Most people want to be more productive, to focus on higher order work. But the productivity friction isn't tools, models, or prompts. It's context — specifically, the lack of it.

## What I keep seeing

Engineers are opening a new AI session, re-explaining the project, re-establishing constraints, re-describing the architecture — and then doing it again the next day. Or they use copilot next to their IDE to pair program. The AI is capable. But it starts from zero every single time. Time is spent feeding AI context instead of getting work done.

The smarter engineers figure out workarounds. They paste in chunks of documentation, keep notes they copy in at the start of each session, build their own informal systems. But these are individual habits, not team infrastructure. When the engineer leaves, the system leaves with them.

This is the same tribal knowledge problem I see in handovers — knowledge that lives in a person's head instead of a shared system. AI makes it more visible, faster.

## The fix is not a new tool

When I started running AI-assisted engagements seriously, I made the same mistakes. I had to stop thinking about AI context as a prompt problem and start thinking about it as an infrastructure problem. I started treating GitHub as the team's memory.

I started a standard of sorts. Every engagement now gets the same structure from day one. 
- A `deliverables/` folder for outputs 
- `notes/` for working material 
- `memory/` for what the AI needs to know (e.g. decisions made, constraints, architecture, client context) 
- A single context file at the root that wires it all together and gets read automatically when a session opens.

No re-establishing context. No "remind me where we were." The AI picks up where the last session left off because the context is persistent and shared.

## Why this has to come from leadership

Here's what I've observed: engineers left to their own devices will each invent their own system. One uses a shared Notion doc. Another keeps a text file locally. Another just re-prompts from scratch. None of it is transferable, none of it compounds, and none of it survives a team change.

Getting AI to actually deliver productivity gains at a team level isn't a tooling decision — it's a standards decision. Someone with authority needs to say: this is how we structure context for AI-assisted work, and it lives in the repo alongside the code.

That's a five-minute decision with compounding returns. Teams that do it early build on each session. Teams that don't keep starting over.

## What this unlocks

Once context lives in the repo, three things happen.

**Continuity across sessions.** Work builds on itself instead of resetting. The productivity gains people expected from AI actually start materialising.

**Decisions as artefacts.** When a stakeholder asks why something was built a certain way, the answer is in the commit history — not reconstructed from memory six months later.

**Handovers that actually work.** The repo is the handoff. The context is there. A new engineer or a new AI session inherits everything without extraction.

The teams I've seen get real value from AI didn't necessarily have the best tools. They had the best context.
