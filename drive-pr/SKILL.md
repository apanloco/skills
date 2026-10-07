---
name: drive-pr
description: Drive a change from local commit to a merge-ready GitHub PR - open it as a draft, iterate on Gemini Code
  Assist review, get the pipeline green, then request code-owner review and iterate. Use when opening, updating, or
  pushing a PR toward merge.
---

# Ship a PR

1. Check the diff against every reviewer in `~/.claude/skills/crucible/reviewers/` with `enabled: true`. Fix what it
   would find with the fix its reviewer asks for, and check each fix against every reviewer again. A finding with no
   such fix is not one to work around: list it for the user and leave it unfixed.
2. Push the branch as `<author>/<branch>` and open the PR as a draft (`gh pr create --draft --fill`). Gemini Code
   Assist starts reviewing automatically when the PR is opened.
3. Iterate on Gemini's comments until each is fixed or dismissed. Request another round with a `/gemini review` PR
   comment, and repeat until its findings are no longer significant.
4. Make sure the pipeline is green (`gh pr checks`).
5. Make sure no review threads are unresolved.
6. Mark the PR ready for review (`gh pr ready`).
7. Request review from the code owners (`.github/CODEOWNERS`) of the changed files, excluding the author. If the author
   is the only owner of every changed file, skip to 10.
8. Iterate on review comments until each is fixed or dismissed. Do not accept comments blindly; discuss with the
   reviewer when you disagree.
9. If review comments led to major changes, go back to 1.
10. The PR is ready to merge.
