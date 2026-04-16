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

  Run Copilot in a different branch (worktree)?
  ❯ No, run Copilot here
    Yes, use a different branch            ← pick this

  Select branch:
    feature/auth
  ❯ fix/login-bug                          ← arrow-key navigation
    refactor/db-layer
    … 12 more (keep typing to filter)

  ✔ Creating worktree → ../myproject-fix-login-bug
  ✔ Launching copilot…

  ─── Copilot does its thing ───

  What next?
  ❯ Yes, switch to fix/login-bug
    No, stay on main
    Delete fix/login-bug branch (drop all changes)
```

No new commands to learn. Just type `copilot` like you always do.

## 🎯 Features

| | |
|---|---|
| 🔀 **Arrow-key branch picker** | Navigate branches with ↑↓, fuzzy-filter as you type |
| ✏️ **Create branches on the fly** | Just type a name — no need to `git branch` first |
| 🧹 **Post-session cleanup** | Commit, keep, switch, or delete — your call |
| 🔄 **Existing worktree support** | Reuse or timestamp-suffix — handles collisions gracefully |
| 🪄 **Drop-in replacement** | Alias as `copilot` — zero workflow change |
| 📦 **Full argument passthrough** | `copilot ask "..."`, `copilot --help` — everything just works |
| 🚫 **No-op outside git repos** | Falls back to plain `copilot` seamlessly |

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

# Stay on current branch — just pick "No, run Copilot here"
copilot
```

## ⚙️ Under the Hood

```
copilot-worktree
       │
       ├── Not in a git repo? ──► copilot "$@"  (pass-through)
       │
       ├── "No, run here" ──────► copilot "$@"  (pass-through)
       │
       └── "Yes, use branch" ──► git worktree add …
                                         │
                                         └── cd <worktree> ──► copilot "$@"
                                                                      │
                                                          Post-session cleanup menu
```

## 📋 Requirements

- **git** ≥ 2.5 (worktree support)
- **GitHub Copilot CLI** (`copilot` or `gh copilot`) on your `PATH`
- **bash** ≥ 4 or **zsh**

## 📄 License

MIT — do whatever you want with it.
