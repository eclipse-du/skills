---
name: finishing-a-development-branch
description: Use when implementation is complete, all tests pass, and you need to decide how to integrate work - guides finishing development work by offering structured merge, PR, or cleanup options
---

# Finishing a Development Branch

## Overview

Guides finishing development work by offering clear options and handling the chosen workflow.

**Core principle:** Verify tests → Present options → Execute choice → Clean up.

**Announce at start:** "I'm using the finishing-a-development-branch skill to finish this work."

## Process

### Step 1: Verify Tests

**Before presenting options, verify tests pass:**

```bash
# Run the project's test suite
npm test / cargo test / pytest / go test ./...
```

**If tests fail:**
```
Tests failed (<N> failures). Must fix before finishing:

[Show failures]

Cannot proceed to merge/PR until tests pass.
```

Stop. Do not proceed to Step 2.

**If tests pass:** Proceed to Step 2.

### Step 2: Determine Base Branch

```bash
# Try common base branches
git merge-base HEAD main 2>/dev/null || git merge-base HEAD master 2>/dev/null
```

Or ask: "This branch split off from main - is that correct?"

### Step 3: Present Options

Show exactly these 4 options:

```
Implementation complete. What would you like to do?

1. Merge back to <base-branch> locally
2. Push and create a Pull Request
3. Keep the branch as-is (I'll handle it later)
4. Discard this work

Which option?
```

**Do not add explanation** - keep options concise.

### Step 4: Execute Choice

#### Option 1: Merge Locally

```bash
# Switch to base branch
git checkout <base-branch>

# Pull latest
git pull

# Merge feature branch
git merge <feature-branch>

# Verify tests on merge result
<test command>

# If tests pass
git branch -d <feature-branch>
```

Then: Clean up worktree (Step 5)

#### Option 2: Push and Create PR

```bash
# Push branch
git push -u origin <feature-branch>

# Create PR
gh pr create --title "<title>" --body "$(cat <<'EOF'
## Summary
<2-3 bullets of what changed>

## Test Plan
- [ ] <verification steps>
EOF
)"
```

Then: Clean up worktree (Step 5)

#### Option 3: Keep As-Is

Report: "Keeping branch <name>. Worktree remains at <path>."

**Do not clean up worktree.**

#### Option 4: Discard

**Confirm first:**
```
This will PERMANENTLY delete:
- Branch <name>
- All commits: <commit-list>
- Worktree at <path>

Type 'discard' to confirm.
```

Wait for exact confirmation.

If confirmed:
```bash
git checkout <base-branch>
git branch -D <feature-branch>
```

Then: Clean up worktree (Step 5)

### Step 5: Clean Up Worktree

**For Options 1, 2, 4:**

Check if inside a worktree:
```bash
git worktree list | grep $(git branch --show-current)
```

If yes:
```bash
git worktree remove <worktree-path>
```

**For Option 3:** Keep worktree.

## Quick Reference

| Option | Merge | Push | Keep Worktree | Clean Branch |
|--------|-------|------|---------------|----------------|
| 1. Merge Locally | ✓ | - | - | ✓ |
| 2. Create PR | - | ✓ | ✓ | - |
| 3. Keep As-Is | - | - | ✓ | - |
| 4. Discard | - | - | - | ✓ (Required) |

## Common Mistakes

**Skipping test verification**
- **Problem:** Merging bad code, creating failed PR
- **Fix:** Always verify tests before offering options

**Open-ended questions**
- **Problem:** "What should I do next?" → Ambiguous
- **Fix:** Show exactly 4 structured options

**Automatic worktree cleanup**
- **Problem:** Deleting worktree when might be needed (Option 2, 3)
- **Fix:** Clean only for Options 1 and 4

**No discard confirmation**
- **Problem:** Accidental deletion of work
- **Fix:** Require typing "discard" to confirm

## Red Flags

**Never:**
- Continue when tests fail
- Merge without result verification
- Delete work without confirmation
- Force push without explicit request

**Always:**
- Verify tests before offering options
- Show exactly 4 options
- Get typing confirmation for Option 4
- Clean worktree only for Options 1 and 4

## Integration

**Called by:**
- **subagent-driven-development** (Step 7) - After all tasks complete
- **executing-plans** (Step 5) - After all batches complete

**Pairs with:**
- **using-git-worktrees** - Clean up worktree created by this skill
