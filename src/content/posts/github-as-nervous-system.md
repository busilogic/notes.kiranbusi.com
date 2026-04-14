---
title: "GitHub as the Nervous System"
date: 2026-04-14
description: "Why every engagement starts with a repo."
---

As I'm forced to switch context often and messages are bombarded to me via messages, emails and phone calls, I've learned to maintain context by putting every engagement I take into a private github repo for organisation. 

## Why version control matters outside of software

Although Git was built for code, the problem it solves isn't code — it's a knowledge problem.

Over the life of a project/engaegment, work accumulates. Decisions get made, context shifts, people move on. Without a record, I'm left reconstructing what happened and why often. In software that's painful. In consulting it's fatal — because the output *is* the thinking, and thinking disappears fast.

The repo fixes that. Every decision has a timestamp. Every change can be diffed. Every direction change leaves a trace. It's not documentation you write after the fact. It's a continuous record of work as it happens.

## Why it matters even more for AI

As I'm using AI more and more, I've learned it helps AI too. AI tools have no memory between sessions. Every time you open a new conversation, you're starting from zero — unless you've built somewhere for the context to live.

The repo solves this problem structurally. The client context, the decisions already made, the constraints that matter — it all lives in the repo. AI can read from it at the start of every session. It already knows where things stand.

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
