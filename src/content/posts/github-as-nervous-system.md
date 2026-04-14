---
title: "GitHub as the Nervous System"
date: 2026-04-14
description: "Why every engagement starts with a repo."
---

I'm forced to switch context often. I'm being hit with multiple messages from multiple directions. Context switching costs! The way I've managed to cope is using GitHub as the nervous system.  

Over the life of a project/engaegement, as work accumulates, decisions made, context shifts and people move on, I started using GitHub as the record.
In consulting, the value *is* the thinking, and thinking disappears fast.

The repo fixes that. I try to use it as my general knowledge hub. That way, every decision has a timestamp. Every change can be diffed. Every direction change leaves a trace. It's not documentation you write after but a continuous record of work as it happens.

## Why it matters for AI

As I'm using AI more and more, I've learned it helps AI too. AI tools have no memory between sessions. Every time you open a new conversation, you're starting from zero — unless you've built somewhere for the context to live.

The repo helps me solve this problem structurally. With context, decisions already made, noted with the constraints that matter — it all lives in the repo. AI can read from it at the start of every session. It already knows where things stand.

Without that, you're spending the first ten minutes of every session re-establishing context you've already established before. With it, you pick up exactly where you left off. The repo stops being passive storage and becomes the memory layer for everything — human and AI alike.

## How I set it up

Every engagement gets the same structure: `deliverables/` for outputs, `notes/` for working material, `memory/` for what the AI needs to know, and a `CLAUDE.md` at the root that wires it all together.

That last file is the important one. It references my identity document at the top — how I think, what I've learned, what I don't do. Below that, the engagement specifics: client context, open decisions, relevant constraints.

When I open the project, Claude reads it automatically. No re-establishing context. No "remind me where we were."

## What it buys

Continuity across sessions — the repo is the memory, because AI tools have none by default.

Decisions as artefacts — when a client asks why we did something, the answer is in the commit history. Not reconstructed from memory months later.

Handoffs that actually work — the repo is the handoff. The context is there. No knowledge extraction required.

The repo isn't overhead, it's my nervous system.
