---
name: writing-code
description: Minimalist, human-written coding style. Produce the smallest complete change with zero AI tells. Use on EVERY task that writes or edits code in any language or framework - features, bug fixes, refactors, scripts, config - even when the user just says "fix this", "add X", or "clean this up". Apply without being asked.
---

# Writing Code Like a Careful Human

The target: a diff a senior maintainer would merge without a single review comment, indistinguishable from the best code already in the repo. Everything below targets the specific habits that give generated code away. Rules models already follow by default (naming, guard clauses, const-correctness, small functions) are deliberately absent: a short list followed exactly beats a long list followed mostly.

## Match the room

Before writing, read enough surrounding code to absorb how this codebase does things: naming, comment density, error handling, module boundaries, test style. Then write code that looks like it was always there. With no conventions to inherit (greenfield), default to the plainest construct that works. Boring beats clever.

Match conventions, never pathologies. Conventions are arbitrary choices where consistency is the whole value: naming scheme, file layout, formatting, import order, test structure. When this skill and a local convention disagree, the convention wins. Pathologies are defects, and being widespread makes them worse, not normal: narration comments, redundant defensive checks, swallowed errors, copy-pasted near-duplicates, dead code, `_old` copies, abstraction sprawl. Never reproduce these for consistency's sake; new code follows this skill even when the surrounding file doesn't.

When the surrounding code is visibly machine-generated slop (the pathologies above, naming drift, boilerplate density far beyond what the logic needs), say so plainly in your response: name the specific defects, where they are, and what a refactor would look like. But scope discipline still holds: write your change clean, report the slop, and refactor it only when asked.

## The smallest complete change

Implement exactly what was asked, completely, and nothing adjacent.

- No drive-by refactors, renames, or reformatting of code you weren't asked to touch. They bloat the diff and bury the real change.
- No options, parameters, config flags, or generality today's task doesn't need. The second caller, if it ever arrives, can add them, and will know better what shape they need.
- An abstraction must pay for itself *now*: extract a helper when there are two real call sites, or when the surrounding flow is unreadable without it. Not because a block "feels like a function".
- Prefer extending the file or module where the behavior lives over creating new parallel files and helper modules.
- Before writing a helper, search for the one that already exists. Duplicating a utility the repo already has is how codebases rot.
- Replacing something means deleting the old version. No `_old`/`_legacy` copies, commented-out blocks, re-export shims, or compatibility layers nobody asked for.

If adjacent work genuinely seems needed, finish the task, then mention it in your response. Don't fold it into the diff. One exception: a doc claim the change itself falsified is a bug in the change, not adjacent work; fix it in the same diff (capturing-knowledge has the routing).

## Comments

Comments are for the next reader of the file, never for the reviewer of this change. The reviewer has the diff; the next reader has only the code.

- Never narrate the change: "now handles X", "changed to use Y", "added validation" are review notes, meaningless once merged.
- A comment earns its line only by carrying what the code *can't*: the why, an external constraint, a non-obvious consequence, a pointer to the source of truth. If it restates the code, delete it.
- For a typical change the right number of new comments is zero. Match the file's existing density; don't import your own.
- No boilerplate docstrings on functions whose signature already says everything.
- A comment reads like a teammate's quick note, not generated prose: plain punctuation (no em dashes), no emoji, no decorative section banners, no adjectives selling the code ("robust", "comprehensive", "elegant"). Match the casing and tone of the comments already in the file.

Bad: `// loop through users and send each an email`
Good: `// billing requires send order to match invoice order, don't parallelize`

## Errors and defense

Handle what can actually fail, where something can actually be done about it.

- Never catch-log-and-continue (or return null). Swallowing converts a loud failure at the fault into silent corruption three frames later. If you can't handle it meaningfully here, let it propagate.
- Don't guard against states the types or callers already rule out. Every redundant null/empty check teaches the reader to doubt an invariant that holds; defensive noise reads as not understanding the data flow.
- Fix causes, not symptoms. A guard or retry that hides a bug upstream is not a fix; follow the bad value to where it was born.
- Validate at trust boundaries (user input, network, files, IPC); trust your own code on the inside.
- Never hardcode credentials, keys, or tokens. Source them the way the repo already does (env, config, secret store).
- A fallback value (`?? default`, `or default`) is correct only when absence is a legitimate state, never when it would mean something upstream broke.

## Honesty

- No placeholder logic, stub returns, or `// TODO: handle this` inside work presented as done. If something is out of scope, leave it out and say so in your response.
- Never silence the type checker (casts, `any`, ignore-directives) to make an error go away. Fix the type or the design it's complaining about.
- Never special-case code to make a particular test or example pass.
- Every constant you introduce needs a reason you could state. If a value is a guess (timeout, retry count, buffer size), say it's a guess in your response.
- Name each concept once and keep the name: `userData` to `user` to `data` drift within one function is a tell that nobody held the whole picture.

## The final pass

Before presenting, reread the complete diff as the maintainer, not the author:

1. **Deletion test**: for every line, comment, guard, parameter, and helper, if removing it wouldn't break the task's actual requirements, remove it.
2. **Comment audit**: does each surviving comment say something the code can't?
3. **Catch audit**: can each handler actually do something about its error?
4. **Blame test**: will any line look strange in `git blame` two years from now, with no PR context attached?
5. **Reality check**: anything claimed to work was actually run or tested. Report failures and untested paths plainly; "should work" is not a result.

The diff that survives this pass is the deliverable.
