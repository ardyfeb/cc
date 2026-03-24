---
name: fix-branch
description: Fixes branches that show thousands of commits after main branch history rewrite. Use when merge requests show 9000+ commits due to old history.
argument-hint: [branch-name]
allowed-tools: Bash, TodoWrite
---

# Fix Branch After Git History Rewrite

## Purpose
After force pushing a rewritten main branch (e.g., to remove sensitive files from history), other branches may show 10,000+ commits in merge requests because they still contain the old main history. This skill fixes those branches by rebasing them onto the new main.

## Instructions

When invoked with a branch name, follow these steps:

### Step 1: Fetch and Check Current State

```bash
git fetch origin
git rev-list --count origin/$BRANCH_NAME ^main
```

If the count is very high (e.g., 9,000+), the branch needs fixing.

### Step 2: Find the Correct Base Tag

The base tag is the version that the branch originally diverged from. Try recent tags to find which one has a reasonable commit count:

```bash
# List recent tags
git tag --sort=-creatordate | head -20

# Check commit count from different tags (run in parallel)
git rev-list --count v1.50.1..origin/$BRANCH_NAME
git rev-list --count v1.50.0..origin/$BRANCH_NAME
git rev-list --count v1.49.0..origin/$BRANCH_NAME
git rev-list --count v1.48.3..origin/$BRANCH_NAME
```

**The correct base tag should give you a reasonable number (e.g., 10-100 commits, not 9,000+).**

Selection rules:
- If one tag shows significantly fewer commits (e.g., 19 vs 95), use that tag
- If multiple tags show the same low count, use the most recent one
- The goal is to find where the branch originally diverged from main

### Step 3: Create Fixed Branch

```bash
# Ensure you have latest main
git fetch origin main:main

# Create a new branch from main
git checkout main
git checkout -b $BRANCH_NAME-fixed

# Rebase the branch commits onto new main
git rebase --onto main $BASE_TAG origin/$BRANCH_NAME -X theirs

# Set the branch to current position
git branch -f $BRANCH_NAME-fixed
git checkout $BRANCH_NAME-fixed
```

**Note:** The `-X theirs` strategy automatically resolves conflicts by preferring the branch's version.

### Step 4: Verify the Fix

```bash
# Check commit count (should be reasonable now)
git rev-list --count $BRANCH_NAME-fixed ^main

# Show the commits
git log --oneline $BRANCH_NAME-fixed -20

# Compare with original (diffs are expected due to history rewrite)
git diff --stat $BRANCH_NAME-fixed origin/$BRANCH_NAME
```

### Step 5: Report Results

Report to the user:
1. **Original commit count** vs main (the large number before fixing)
2. **Fixed branch commit count** vs main (the reasonable number after fixing)
3. **Base tag used** (the tag you selected)
4. **First 20 commit messages** on the fixed branch (to show it contains the right commits)
5. **Confirmation** that the branch was created successfully
6. **Push instructions** for the user to push the fixed branch

## Important Notes

1. **Do NOT replace the original branch** - always create a new branch with `-fixed` suffix
2. **The `-X theirs` strategy** helps avoid conflicts by preferring the branch's changes
3. **Base tag selection is critical** - choose the tag where the branch originally diverged
4. **Diffs are expected** - the fixed branch is based on cleaned main history
5. **Always verify** before suggesting to push

## Example Output Format

Present results like this:

```
Results:
- Original commit count vs main: 10,001 commits
- Fixed branch commit count vs main: 17 commits
- Base tag used: v1.50.0

First 20 commits on fixed branch:
[list commits]

Status: The branch feature-xyz-fixed has been created successfully!

To push this fixed branch:
# Push as new branch
git push origin $BRANCH_NAME-fixed

# Or replace the original (use with caution!)
git push origin $BRANCH_NAME-fixed:$BRANCH_NAME --force-with-lease
```

## Usage

Users invoke this skill with:
- `/fix-branch feature-chat-privacy`
- `/fix-branch my-feature-branch`

The skill will automatically fix the branch and create a `-fixed` version.
