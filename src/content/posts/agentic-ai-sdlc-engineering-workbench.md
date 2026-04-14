---
title: "My Agentic AI SDLC Engineering Workbench"
date: 2026-04-14
description: "How I run my consulting practice — Claude Code, git, auto-journaling, nightly synthesis, and CLAUDE.md context injection."
draft: true
---

Most consultants keep notes in Notion and context in their heads. I run mine through a system built on Claude Code, git, and a handful of Python scripts. Here's how it works.

---

## Layer one: The Voice Document

Every Claude session starts with a 200-line markdown file injected via `CLAUDE.md` — my identity layer. It describes how I think about problems, my technology preferences, what I've learned from past engagements, and anti-patterns I've been burned by.

It's not a prompt. It's institutional memory that travels with me. When I open a new engagement, that document is already there. When I switch between a client infrastructure repo and a side project, the same principles carry across. I don't have to re-explain myself to my tools.

The document is updated automatically. I never edit it by hand.

## Layer two: The Journal Loop

At the end of every working session, a script reads the Claude transcript and structures it into a journal entry: what was done, what decisions were made, what patterns were observed, what's still open. These entries accumulate in their own git repo — a permanent, searchable record of how I've worked across every engagement.

Nightly, a synthesis script reads the recent entries, runs them through Claude, and extracts the patterns that matter — the things that worked, the things that didn't, the calibrations I should carry forward. Those patterns get appended to the voice document automatically.

```
Session ends
     ↓
auto-journal.py reads transcript
     ↓
Claude Haiku structures → YYYY-MM-DD-{project}.md
     ↓
synthesise-voice.py runs nightly (6pm AEST)
     ↓
Claude extracts patterns from recent entries
     ↓
Appended to voice doc → committed → pushed
```

The result is a system that gets incrementally smarter about how I work, without me having to maintain it consciously. Each session feeds the next.

## Layer three: CLAUDE.md Injection

Every project repo contains a `CLAUDE.md` that starts with a single line — a reference to the voice document. Astro project, infrastructure repo, client engagement — they all inherit the same identity. Context injection, not copy-paste.

```markdown
# CLAUDE.md (any project)
@/workspaces/projects/principal-consultant/voice/principal-consultant-voice.md

## Project: {client-name}
Engagement: ...
Deliverables: ...
```

Each project then adds its own layer on top: the engagement context, the deliverables list, the client-specific conventions. The base identity is shared. The project specifics are local.

## Layer four: Project Intake

New engagement? One command creates the GitHub repo, scaffolds the local directory from a template, fills in the placeholders, commits the initial state, and opens a kickoff issue. From the end of the kickoff call to a working, version-controlled environment with context pre-loaded: under two minutes.

The templates are typed — consulting engagement, software project, presentation. Each one has the right structure out of the box: deliverables folder, issue labels, CLAUDE.md wired up.

---

## Why This Matters

This isn't about AI doing the work. It's about building systems that make expertise portable, decisions traceable, and learning automatic.

The same discipline I bring to client infrastructure — version-controlled, automated, observable, with no single point of failure — applied to how I operate as a consultant. The tools change. The principles don't.

If I'm asking a client to treat their infrastructure as code, I should be able to show what that looks like for the way I work.
