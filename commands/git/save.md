---
allowed-tools: Bash(git *)
argument-hint: [prompt]
model: sonnet
disable-model-invocation: true
context: fork
description: Commit changes and push to remote branch.
---

## Context

- User instructions: $ARGUMENTS
- Current git status: !`git status`
- Current git diff (staged and unstaged changes): !`git diff HEAD`
- Current branch: !`git branch --show-current`
- Recent commits: !`git log --oneline -10`
- Repo root: !`git rev-parse --show-toplevel`

## You task

Based on the above changes, create a single git commit, consider user instruction.
Step:

1. Analyze current changes.
2. Stage changes.
3. Create commit with semantic format with message of summarized changes.
  Commit message should be minimal and descriptive.
  For commit scope you can use ticket name, otherwise leave it empty.
4. Push the changes to remote branch. If current branch not have upstream, setup it automaticaly.

For ticket name you can find on current branch name.
Example:

- fix-SBPD-0000 and fix/SBPD-000 ticket name is SBPD-0000
- fix-SBPD-0001-fix-bugs ticket name is SBPD-0001

## Specific repo rules
- main-app: always use --no-verify to commit
