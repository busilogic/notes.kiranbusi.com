---
title: "GitHub as the Nervous System"
date: 2026-04-14
description: "Why every engagement I take on starts with a GitHub repo — and what that discipline actually buys you."
---

Every engagement I take on starts the same way. Before the kickoff call is over, there's a GitHub repo. Before I've written a single deliverable, the context is committed.

This isn't a preference or a habit. It's a structural decision with real consequences for how work goes.

## What a repo actually is

A git repo isn't a file storage location. It's a timeline. Every decision that gets committed has a timestamp, an author, and a message. Every file that changes can be diffed against what it was before. Every conversation that results in a direction change can be traced.

In consulting, this matters more than in software development — because in software, the code *is* the output. In consulting, the output is advice, documents, and decisions. Those are easy to lose. A repo is the discipline that stops you losing them.

## The practical pattern

Every engagement gets its own repo with a consistent structure: a `deliverables/` folder for final outputs, a `notes/` folder for working material, a `memory/` folder for what Claude needs to know about the engagement, and a `CLAUDE.md` that wires up the project context automatically.

The `CLAUDE.md` pattern is the piece I get asked about most. At the top, it references my identity document — a 200-line file that describes how I think, what I've learned, and what I don't do. Below that, the engagement specifics: the client context, the deliverables list, the relevant constraints.

When I open the repo, that's all there — without me having to re-establish it. The tool already knows what it needs to know.

## What it buys you

**Continuity across sessions.** Each session picks up where the last one ended. No "what did we decide last time?" No reconstructing context from memory.

**Decisions as artefacts.** When a client asks "why did we do it this way?" — the answer is in the commit history. Not in someone's head or a Slack thread.

**Handoff that actually works.** When a project transitions to another person or team, the repo *is* the handoff. The decisions are documented. The context is present. There's no hidden knowledge to extract.

## The deeper principle

If you're advising clients to treat their infrastructure as code — version-controlled, auditable, automated — you should be able to show what that looks like for the way you work.

The repo isn't overhead. It's the work.
