<div align="center">

# 🌳 copilot-worktree

### Run multiple GitHub Copilot sessions on the same repo — in parallel, without conflicts.

**One command. Isolated branches. Zero friction.**

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Shell](https://img.shields.io/badge/Shell-Bash-green.svg)](copilot-worktree)
[![Git Worktrees](https://img.shields.io/badge/Powered%20by-Git%20Worktrees-orange.svg)](https://git-scm.com/docs/git-worktree)

</div>

---

## 💡 The Problem

You're on `main`, deep in a flow. You want Copilot to spike a feature on `feature/auth` — but you can't check out two branches at once.

**Until now.**

## 🚀 The Solution

`copilot-worktree` is a lightweight Bash wrapper that gives Copilot its own branch in an isolated [git worktree](https://git-scm.com/docs/git-worktree) — so you can run as many parallel Copilot sessions as you want, each on a different branch, with zero conflicts.

```
myproject/               ← you work here on main
myproject-feature-auth/  ← Copilot works here (worktree)
myproject-fix-bug-123/   ← another Copilot session (worktree)
```

## ✨ How It Feels

```
$ copilot

  Run Copilot in a different branch?
  ❯ Run Copilot here
    Use a different branch            ← pick this

  Select branch:
    feature/auth
  ❯ fix/login-bug                          ← arrow-key navigation
    refactor/db-layer
    … 12 more (keep typing to filter)

  ✔ Creating worktree → ../myproject-fix-login-bug
  ✔ Launching copilot…

  ─── Copilot does its thing ───

  ✔ Copilot work complete
  ✔ Worktree is clean (no uncommitted changes)

  What next?
  ❯ Stay in worktree                — Continue working
    Remove worktree (keep branch)  — Back to original branch, branch stays
    Remove worktree and delete branch — Delete branch AND worktree
```

**If there were changes to commit:**

```
  ✔ Copilot work complete
  ⚠ Worktree has uncommitted changes

  What next?
  ❯ Stay in worktree with changes              — Stay and commit manually later
    Commit changes                             — Stage all, commit, then show cleanup options
    Push and remove worktree                   — Commit, push to origin, optionally create PR, remove worktree
    Discard changes and remove worktree        — Delete everything (new branch only)
```

No new commands to learn. Just type `copilot` like you always do.

## 🔄 Post-Session Workflows

After Copilot finishes, the tool detects your worktree state and shows you the right cleanup options. Here are the common scenarios:

### Scenario 1: Clean Worktree (no changes)

```
Worktree is clean after Copilot finishes working.

  ✔ Copilot work complete
  ✔ Worktree is clean (no uncommitted changes)

  What next?
  ❯ Stay in worktree                            — Continue working in the branch
    Remove worktree (keep branch)              — Remove worktree, return to original branch
    Remove worktree and delete branch          — Delete branch AND worktree (new branches only)
```

**Use this when:** Copilot finished and committed everything, or there's nothing new to commit.

---

### Scenario 2: Dirty Worktree — New Branch (uncommitted changes)

```
New branch with changes that need committing.

  ✔ Copilot work complete
  ⚠ Worktree has uncommitted changes

  What next?
  ❯ Stay in worktree with changes              — Stay in worktree, commit manually later
    Commit changes                             — Stage all, commit with auto-message, refresh menu
    Push and remove worktree                   — Commit, push to origin, create PR, remove worktree
    Discard changes and remove worktree        — Delete everything (new branch only)
```

**Use "Push and remove worktree" for:** Fast workflow — commit everything, push immediately, optionally create a PR, and get back to your original branch.

---

### Scenario 3: Dirty Worktree — Existing Branch (uncommitted changes)

```
Working on an existing branch with changes.

  ✔ Copilot work complete
  ⚠ Worktree has uncommitted changes

  What next?
  ❯ Stay in worktree with changes
    Commit changes
    Push and remove worktree                              — Commit, push, ask about PR
    Discard changes, delete branch, and remove worktree ⚠ (requires extra confirmation)
```

**Extra safety:** Deleting an existing branch requires an extra confirmation prompt to prevent accidents.

---

### Scenario 4: After "Commit changes"

```
After committing, the menu refreshes to show clean state options.

  ✔ Copilot work complete
  ✔ Worktree is clean (commit in progress)

  What next?
  ❯ Stay in worktree
    Remove worktree (keep branch)
    Push and remove worktree
    Remove worktree and delete branch
```

**State transition:** Once committed, you can push immediately or continue working.

## 🎯 Features

| | |
|---|---|
| 🔀 **Arrow-key branch picker** | Navigate branches with ↑↓, fuzzy-filter as you type |
| ✏️ **Create branches on the fly** | Just type a name — no need to `git branch` first |
| 🧹 **Intelligent cleanup** | Auto-detects clean vs dirty state, shows appropriate options |
| 🚀 **Push + PR automation** | Commit, push, optionally create PR in one workflow |
| ⚠️ **Safety-first** | Extra confirmation for deleting existing branches |
| 🔄 **Flexible workflows** | Multiple cleanup paths for different scenarios |
| 🪄 **Existing worktree support** | Reuse or timestamp-suffix — handles collisions gracefully |
| 📦 **Drop-in replacement** | Alias as `copilot` — zero workflow change |
| 🚫 **Full argument passthrough** | `copilot ask "..."`, `copilot --help` — everything just works |
| ❌ **No-op outside git repos** | Falls back to plain `copilot` seamlessly |

## 📦 Install

**One line:**

```bash
curl -fsSL https://raw.githubusercontent.com/krzysiekbielicki/copilot-workflow/main/copilot-worktree \
  | sudo tee /usr/local/bin/copilot-worktree > /dev/null \
  && sudo chmod +x /usr/local/bin/copilot-worktree
```

<details>
<summary><b>No <code>sudo</code>?</b> Install to <code>~/.local/bin</code> instead</summary>

```bash
curl -fsSL https://raw.githubusercontent.com/krzysiekbielicki/copilot-workflow/main/copilot-worktree \
  -o ~/.local/bin/copilot-worktree \
  && chmod +x ~/.local/bin/copilot-worktree
```

Make sure `~/.local/bin` is on your `PATH`.

</details>

## 🔗 Create the Alias (recommended)

Shadow `copilot` so every invocation gets worktree superpowers automatically:

**zsh** (default on macOS):
```bash
echo "alias copilot='copilot-worktree'" >> ~/.zshrc && source ~/.zshrc
```

**bash**:
```bash
echo "alias copilot='copilot-worktree'" >> ~/.bashrc && source ~/.bashrc
```

From now on, `copilot`, `copilot --help`, `copilot ask "..."` — everything works exactly as before, plus you get the branch/worktree menu on every run inside a git repo.

## 🛠 Usage

```
copilot-worktree [<copilot-args>...]
```

All arguments are forwarded verbatim to `copilot`:

```bash
# Interactive — worktree menu appears first
copilot

# Ask a question — menu appears, then Copilot runs with your args
copilot ask "How do I reverse a linked list in Go?"

# Stay on current branch — just pick "Run Copilot here"
copilot
```

## 🧠 Behavioral Notes

### Auto-Detection of Worktree State

The tool automatically detects whether your worktree has uncommitted changes:
- **Clean:** No changes, shows options for staying, cleaning up, or deleting the branch
- **Dirty:** Uncommitted changes detected, shows options to keep, commit, push+clean, or drop

### State Transitions After Committing

When you select **"Commit changes"**, the tool:
1. Stages all changes
2. Commits with an auto-generated message
3. **Refreshes the menu** to show clean state options

This allows you to immediately push or continue working without re-running the command.

### Safety for Existing Branches

Deleting existing branches (ones that already existed before this session) requires an extra confirmation prompt. This prevents accidentally deleting branch work that was already on the remote.

New branches created during this session can be deleted with a single confirmation.

### Push Behavior

When you select **"Push and remove worktree"**:
1. Changes are committed (if not already)
2. Pushed to `origin/<branch-name>`
3. You're prompted to create a PR using GitHub CLI (`gh pr create`)
4. Worktree is cleaned up and you return to your original branch

**No automatic merge:** PRs are created for your review — you maintain full control over when/how to merge.

### Flexible Cleanup Paths

Different scenarios support different cleanup strategies:
- **Clean worktree + new branch:** Can delete branch entirely or just remove worktree
- **Clean worktree + existing branch:** Can only remove worktree (branch stays)
- **Dirty worktree + new branch:** Can commit, push, or drop everything
- **Dirty worktree + existing branch:** Commit or push options available; deletion requires extra safety confirmation

## ⚙️ Under the Hood

```
copilot-worktree
       │
       ├── Not in a git repo? ──► copilot "$@"  (pass-through)
       │
       ├── "Run Copilot here" ──► copilot "$@"  (pass-through in main worktree)
       │                           └── linked worktree: post-session cleanup menu
       │
       └── "Use a different branch" ──► git worktree add …
                                         │
                                         └── cd <worktree> ──► copilot "$@"
                                                                      │
                                                          Post-session cleanup menu
```

## 📋 Requirements

- **git** ≥ 2.5 (worktree support)
- **GitHub Copilot CLI** (`copilot` or `gh copilot`) on your `PATH`
- **bash** ≥ 4 or **zsh**
- *(Optional)* **GitHub CLI** (`gh`) — for creating PRs with "Push and remove worktree" workflow

## 🔄 Upgrading from Previous Versions

Existing workflows continue to work seamlessly with the new version:

- **Menu consolidation:** Options have been reorganized for clarity, but all previous workflows still work
- **Auto-detection:** The tool now intelligently detects clean vs dirty state automatically
- **New "Push and remove worktree" option:** Available for faster commit+push+PR workflows (opt-in)
- **State transitions:** Menu refreshes after committing, allowing immediate push without re-running
- **Backward compatible:** All existing branch/worktree behavior is unchanged

No changes needed to your current workflow — the script drops in as a replacement.

## 📄 License

MIT — do whatever you want with it.
