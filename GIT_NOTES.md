# Git Notes — Quick Reference

This file collects the important points we discussed about common Git concepts and commands.

## Pull strategies
- `git config pull.rebase false` (merge):
  - `git pull` performs `fetch` + `merge`.
  - Creates a merge commit when histories diverge.
  - Pros: preserves topology; Cons: can create noisy history.
  - Conflicts: resolve, `git add`, then `git commit` to finish the merge.

- `git config pull.rebase true` (rebase):
  - `git pull` performs `fetch` + `rebase`.
  - Reapplies local commits on top of fetched branch, producing a linear history and new commit SHAs.
  - Pros: clean linear history; Cons: rewrites local history (avoid on shared commits).
  - Conflicts: resolve, `git add`, then `git rebase --continue` (or `--abort`).

- `git config pull.ff only` (fast-forward only):
  - `git pull` will only update if a fast-forward is possible; otherwise it fails.
  - Pros: prevents automatic merges or rebases; Cons: requires manual intervention when diverged.

Runtime flags that override config:
- `git pull --rebase` / `git pull --no-rebase`
- `git pull --ff-only`

## Fetch vs Pull
- `git fetch`:
  - Downloads commits and updates remote-tracking refs (e.g., `origin/main`) but does not change your current branch or working tree.
  - Safe for inspection and scripting.

- `git pull`:
  - Equivalent to `git fetch` + `git merge` (or `git rebase` depending on config/flags).
  - May modify your working tree, create merge commits, or rewrite history (if rebasing).

Use `git fetch` when you want to review remote changes before integrating. Use `git pull` when you want to integrate immediately.

## `origin`, `origin/main`, and `origin/HEAD`
- `origin` is the conventional name for the remote repository (a shortcut stored in `.git/config`).
- `origin/main` is a remote-tracking branch in your local repo (refs/remotes/origin/main) that records the commit `main` on `origin` was at when you last fetched/pulled.
- `origin/HEAD` is a remote-tracking symbolic ref pointing to the remote's default branch (for example, `origin/HEAD -> origin/main`). It indicates the remote's default branch.

Commands:
```
git remote -v
git remote show origin
git ls-remote --symref origin HEAD
git branch -r
git rev-parse origin/main
```

## `HEAD`, detached HEAD, and `ORIG_HEAD`
- `HEAD` points to the current checkout (usually a branch ref like `refs/heads/main`). It represents "where you are" in the commit graph.
- Detached HEAD: when you checkout a commit or tag directly, `HEAD` points to a commit, not a branch. New commits in detached HEAD are orphaned unless you create a branch.
- `ORIG_HEAD` is used by Git to remember previous HEAD positions for recovery after operations like merge, reset, or rebase.

Useful commands:
```
git rev-parse HEAD
git symbolic-ref --short HEAD   # shows current branch name when attached
git symbolic-ref HEAD           # shows full ref when attached
git rev-parse ORIG_HEAD
```

## Quick inspection & sync workflow
1. Fetch and inspect:
```
git fetch origin
git log --oneline HEAD..origin/main   # remote commits not in local
git diff HEAD..origin/main            # code differences
```

2. Integrate after review:
```
git merge origin/main      # merge
git rebase origin/main     # rebase
git pull --ff-only         # fast-forward only
```

## Small tips
- Prefer `fetch` + review for safety. Use `pull` when you're ready to integrate.
- Use rebase for personal feature branches to keep history linear. Avoid rebasing published commits.
- `origin/HEAD` is useful to discover the remote's default branch.

---
Generated from an interactive discussion — keep this file as a handy cheat-sheet.
