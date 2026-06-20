---
name: debugging-code
description: Evidence-first debugging discipline. Use on EVERY bug, error, crash, stack trace, regression, flaky test, or "this stopped working" report, in any language, BEFORE proposing any fix. Also use when a previous fix didn't hold. Enforces reproduce, isolate, root cause, verify, and searching the project's own history before re-deriving anything. Composes with project playbooks like minecwaft-teavm and supersedes generic structured-debugging workflows; apply without being asked.
---

# Debugging on Evidence, Not Vibes

The target: a fix you can explain as a causal chain from root cause to observed symptom, where every link was checked against evidence. A fix that merely makes the symptom stop is a coincidence wearing a fix's clothes, and it comes back. Everything below targets the specific ways debugging goes wrong by default: pattern-matching to a familiar failure, editing before understanding, and declaring victory without proof.

## Search before deriving

The project usually already knows. Before forming any theory:

- `git log` and `git blame` the failing area: what changed last, and why.
- Search the project's own record: history docs, CLAUDE.md gotchas, issue notes, old branches. Five minutes of grep beats an hour of re-derivation, and re-diagnosing a documented dead end is the most expensive way to spend a session.
- If the bug appeared after a change, diff against the last known-good state before reading any code.

## Reproduce first

- Get a repro you can trigger on demand, as small and fast as affordable. If you can't make it fail, you can't prove you fixed it.
- For flaky failures, tighten the loop until the failure is reliable enough to test against: run it N times, pin the seed, insert the sleep that widens the race window. A fix verified against a 1-in-10 repro run once proves nothing.

## Read the actual error

All of it. The full message, the stack, the line numbers, and the FIRST error, not the last (later errors are usually fallout). More time is lost to skimmed errors than to hard bugs. If the message names a value, go look at that value before theorizing about it.

## Isolate with hypotheses, not edits

- A hypothesis must predict something observable you haven't looked at yet. Then look. "It might be X" with no testable prediction is a guess wearing a lab coat.
- One variable at a time. Binary-search the space: commits (bisect), layers (does the bad value already exist before this boundary?), inputs (which half still triggers it?).
- Finding something suspicious is not finding the cause. The cause explains every symptom, including the weird ones. A symptom your theory doesn't explain is the theory telling you it's wrong.
- A signal that pattern-matches a known failure may still have a different cause this time. Check that the evidence supports the specific diagnosis, not just the familiar shape.
- Instrument with targeted prints or asserts at the boundaries you're testing, not shotgun logging everywhere. Ad-hoc instrumentation comes out in the final diff; standalone verification entries a project keeps on purpose (Minecwaft's src/probes) are deliverables, not instrumentation.

## Fix at the root

The layer bisect from isolation tells you which boundary the bad value first crosses; the fix belongs at the first frame where the value is wrong, never at the crash site (writing-code's fix-causes-not-symptoms rule, applied with a stack trace in hand).

When a fix attempt fails: revert it fully before trying the next hypothesis. Stacked half-fixes turn one bug into an unreproducible three.

## Verify like a skeptic

- Check the fix didn't just silence the symptom: assert the value is now right, not merely that the crash is gone.
- Check the blast radius: who else calls the thing you changed?
- Report faithfully: the cause, the evidence, what was verified, and anything still unknown labeled as unknown.

## Before claiming fixed

1. The repro ran and passed after the fix, in front of you. A fix you didn't watch pass is a hypothesis, not a result.
2. Your explanation predicts every observed symptom, not just the loudest one.
3. The diff contains no leftover instrumentation and no symptom-site guards.
