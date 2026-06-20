---
name: capturing-knowledge
description: Writes durable project knowledge where the next session will find it. Use when committing, after finishing significant work, after solving a hard bug or proving a dead end, or when the user says "update the docs", "update CLAUDE.md", "add this to history", or "write this down". Covers commit messages, CLAUDE.md upkeep, history/log docs, memory, and what NOT to write down. Not for authoring READMEs, API docs, or runbooks; this skill is knowledge capture only.
---

# Capturing Knowledge

Once the session dies, all you've got left is the repo. Anything you worked out the hard way is lost if it only lived in the conversation, and the next session just hits the same wall and figures it out all over again. So the goal is simple: put each fact where the next reader will actually look, and keep it short enough that they read it.

## Where a fact goes

Every fact has one home. Pick it based on who needs it and when.

- A rule or gotcha every future session has to follow goes in the project's CLAUDE.md, as one tight line in the voice that's already there. CLAUDE.md loads into every session, so every line costs you on every run. If it needs more detail than that, put the detail in a docs/ file and point to it from the line.
- The story behind a diagnosis, fix, or dead end goes in the project's history or log doc if it has one, otherwise the commit body. Dead ends are the most valuable thing you can write down. They're the only thing stopping you from burning a session on the same dead end twice. Once you hit a dead end too big for a commit body, suggest starting a history doc, but don't just make one on your own.
- A constraint tied to a specific bit of code goes in a comment right there at the code. The writing-code skill covers what a comment has to earn to stay.
- A user preference or something that spans projects goes in a memory file in the auto-loaded memory directory. Check the index first so you're not adding a second home for something that already has one.
- Stuff that just changed mechanically goes nowhere. Git already has it. Writing prose to restate a diff is rot the moment you save it.

## Voice and density

- Match whatever doc you're editing: the heading style, the entry format, how terse it is. These get read by sessions that pay by the token, so any line that doesn't change what someone does later is a line you should cut.
- Use real dates only ("2026-06-10"), never "recently" or "currently" or "the new approach". Every relative date turns into a lie down the road.
- Write down the why and the proof, not just the rule. "X breaks" gets ignored. "X breaks because Y, confirmed by Z" gets obeyed.

## Stale docs are worse than no docs

When a change invalidates something a doc claims, fixing the doc is part of the change, not a follow-up: a claim the diff falsified is now a bug, not adjacent work (the stated exception to writing-code's nothing-adjacent rule). A doc that's right 80% of the time gets trusted 100% of the time and burns the reader on the other 20.

This does not mean touching docs on every edit. Touch them when the change adds or invalidates durable knowledge, otherwise leave them alone.

## Commit messages

- The title names the achievement in the project's voice, not the mechanical change; titles are the index a future session scans with `git log --oneline`. Read the recent log before the first commit of a session and match what's there.
- The body exists only for the why. A dead-end story lives here only when the project keeps no history doc; when it does, the body carries one pointer line, never the story twice. No file lists, no restating the diff.

## What not to write

- Anything you could just read off the code or git history.
- Anything that only mattered for this one conversation.
- A second copy of something. If it's already written down somewhere, point to that. Duplicates drift apart over time, and then you can't trust either one.

## Before closing significant work

1. Did this work produce a rule a future session must obey? Is it one line in CLAUDE.md?
2. Was there a dead end or diagnosis worth the history doc? Is it there, dense and dated?
3. Did the change invalidate any documented claim? Is the doc fixed in this same change?
