---
name: drive-pr-crucible
description: Drive a change to a merge-ready GitHub PR like drive-pr, with crucible reviews run in the background
  after Gemini and the pipeline are done, fixing what each run finds until no harm or fragile finding is left. Use when
  the user asks to drive a PR with crucible.
---

# Ship a PR with crucible

Follow `~/.claude/skills/drive-pr/SKILL.md`, with the crucible phase below between its step 5 (no unresolved review
threads) and step 6 (mark the PR ready). When code-owner review leads to major changes, go back to the crucible phase,
not to step 1.

## Crucible phase

1. Run crucible in the background, from the repo's root, so its exit notifies you:
   `claude -p "/crucible <n>" --dangerously-skip-permissions > <scratchpad>/crucible-<n>-<k>.log 2>&1`
   with `run_in_background`, where `<k>` counts the runs. Never push to the PR's branch while it runs: crucible posts
   nothing when the head moves during a run.
2. When it exits, read the newest review on the PR whose body ends with `*crucible <run id> · posted automatically*`
   and whose `commit_id` is the head, and its finding comments. If there is none, read the log, tell the user why the
   run posted nothing, and stop.
3. Handle each finding. Do not accept it blindly: check its claim against the code first.
   - 🔴 harm, 🟡 fragile, 🔵 debt: push its fix, or reply on its thread saying why it stays.
   - ⚪ polish: fix it when the fix is cheap; otherwise leave it.
   - A fragile finding that stays needs someone to accept the risk: ask the user, never accept it on their behalf.
   - Check that every test a fix adds fails without the fix.
   Amend into the PR's single commit and push once, then reply on each thread with what was done, and resolve it.
4. Go back to 1 only if this run's merge risk was harm or fragile, once the pipeline is green again. Stop otherwise:
   what is left is debt and polish, and another run mostly finds more of it. Also stop after three runs, and list for
   the user what is left.
5. Report each run's merge risk and what was fixed or kept, and anything the user or a reviewer found that crucible
   did not, so the user can decide whether it deserves a reviewer rule.
