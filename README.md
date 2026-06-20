## Skills

**`/writing-code`** keeps the change as small as it can while still actually doing the job. No random refactors on the side, none of the usual AI giveaways. Basically the kind of diff someone reviews and merges without leaving a single comment.

**`/debugging-code`** means no editing until the bug actually reproduces and the real cause is found. It checks the git history first too, so you're not sitting there solving a dead end someone already hit months ago.

**`/capturing-knowledge`** writes down only what the next session can't figure out on its own. The dead end, the gotcha, the why. Never a restated diff.

## How they fit together

Debugging goes first and finds the real cause, the spot where things first go wrong. It passes that to writing-code, which fixes it right there instead of patching where it crashed. Once the fix is in, capturing-knowledge saves the part that was a pain to figure out, so the AI doesn't have to relearn it next time in another session with a similar issue.

They also lean on each other on purpose. Debugging leaves the fixing to writing-code. Writing-code makes one exception to its "don't touch anything extra" rule, a doc the change just made wrong, and hands that to capturing-knowledge. And capturing-knowledge points back to writing-code for what a comment needs to earn its place. Nothing overlaps and nothing slips through. Each step picks up where the last left off, and every fact ends up in one spot.

So bugs get fixed once, at the root, with a clean diff, and what was learned sticks around across sessions.
