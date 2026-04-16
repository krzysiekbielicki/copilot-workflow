# copilot-workflow

> Run multiple GitHub Copilot sessions on the **same git project** — simultaneously, without stepping on each other's toes.

## The Problem

GitHub Copilot CLI works great for a single task. But what if you want Copilot to work on a feature in `feature/auth` while you continue hacking on `main`? Or spin up three Copilot agents in parallel on the same repo?

Git doesn't let you check out two branches at once — but **git worktrees** do.

## The Solution

`copilot-worktree` is a tiny Bash wrapper around the `copilot` CLI. Before launching Copilot, it:

1. Asks whether you want to run Copilot **in the current branch** or **in a different branch**
2. If you choose a different branch, it creates a [git worktree](https://git-scm.com/docs/git-worktree) in a sibling directory (e.g. `../myproject-feature-auth`)
3. Launches `copilot` inside that worktree (passing through **all arguments** you provide)
4. After Copilot exits, offers to commit any changes, switch back, or clean up the worktree

This lets you run as many parallel Copilot sessions as you like — each isolated in its own worktree — with zero conflicts.

```
myproject/               ← you work here on main
myproject-feature-auth/  ← Copilot works here (worktree)
myproject-fix-bug-123/   ← another Copilot session (worktree)
```

## Features

- ✅ Arrow-key branch selection with pagination
- ✅ Type a new branch name on the fly
- ✅ Handles existing worktrees gracefully
- ✅ Post-session cleanup: commit, keep, switch, or delete branch
- ✅ Falls back to plain `copilot` outside git repos
- ✅ Passes all arguments through to `copilot` (flags, subcommands, etc.)
- ✅ Drop-in `copilot` alias — zero workflow change

## Install

Single-line install to `/usr/local/bin`:

```bash
curl -fsSL https://raw.githubusercontent.com/krzysiekbielicki/copilot-workflow/main/copilot-worktree | sudo tee /usr/local/bin/copilot-worktree > /dev/null && sudo chmod +x /usr/local/bin/copilot-worktree
```

> **No `sudo`?** Install to `~/.local/bin` instead (make sure it's on your `PATH`):
> ```bash
> curl -fsSL https://raw.githubusercontent.com/krzysiekbielicki/copilot-workflow/main/copilot-worktree -o ~/.local/bin/copilot-worktree && chmod +x ~/.local/bin/copilot-worktree
> ```

## Create the alias (shadow `copilot`)

After installing, add an alias so typing `copilot` transparently invokes `copilot-worktree`:

**zsh** (default on macOS):
```bash
echo "alias copilot='copilot-worktree'" >> ~/.zshrc && source ~/.zshrc
```

**bash**:
```bash
echo "alias copilot='copilot-worktree'" >> ~/.bashrc && source ~/.bashrc
```

From that point on, `copilot`, `copilot --help`, `copilot ask "..."`, etc. all work exactly as before — but now you get the worktree superpower on every invocation inside a git repo.

## Usage

```
copilot-worktree [<copilot-args>...]
```

All arguments are forwarded verbatim to `copilot`.  Examples:

```bash
# Interactive (the worktree menu appears first)
copilot-worktree

# Ask a quick question — worktree menu still appears, then Copilot runs with the flag
copilot-worktree ask "How do I reverse a linked list in Go?"

# Skip menu entirely by staying on current branch
copilot-worktree          # choose "No, run Copilot here"
```

## Requirements

- `git` ≥ 2.5 (worktree support)
- `gh copilot` / `copilot` CLI on your `PATH`
- A Unix-like shell (bash ≥ 4 or zsh)

## How it works

```
copilot-worktree
       │
       ├── Not in a git repo? ──► copilot "$@"  (pass-through)
       │
       ├── "No, run here" ──────► copilot "$@"  (pass-through)
       │
       └── "Yes, use branch" ──► git worktree add ...
                                         │
                                         └── cd <worktree> ──► copilot "$@"
                                                                      │
                                                          Post-session cleanup menu
```

## License

MIT
