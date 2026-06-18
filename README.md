# Version Control with Git (Abridged)

This is an abridged version of the Software Carpentry "Version Control with Git" lesson, designed to be completed in approximately 60 minutes. It covers the essential concepts and commands needed to start using Git for version control.

## Table of Contents

1. [Automated Version Control](#1-automated-version-control)
2. [Setting Up Git](#2-setting-up-git)
3. [Creating a Repository](#3-creating-a-repository)
4. [Tracking Changes](#4-tracking-changes)
5. [Exploring History](#5-exploring-history)
6. [Ignoring Things](#6-ignoring-things)
7. [Remotes in GitHub](#7-remotes-in-github)
8. [Appendix](#appendix)
   - [Intermediate Topics: Collaboration, Conflicts, and Branching](#intermediate-topics-collaboration-conflicts-and-branching)
   - [Quick Reference](#quick-reference)
   - [Attribution](#attribution)

---

## 1. Automated Version Control

### What is version control?

Version control systems track changes to files over time. Think of it as an unlimited "undo" that also lets many people work in parallel.

Instead of keeping multiple copies of files (`app.py`, `app_v2.py`, `app_v2_john.py`), a version control system records each set of changes as a **commit** — a snapshot you can always return to. Each snapshot contains a timestamp and records who made the change, which is extremely helpful when collaborating with others.

Unlike tools such as Word, Excel, Google Docs, or Google Sheets — which auto-save continuously as you work — Git does not automatically record your progress. Instead, commits are **intentional save points** that you choose to create. Each commit is a deliberate snapshot: you decide what to capture and when. This means your project's history isn't a stream of every keystroke, but a curated timeline of meaningful milestones that you can rewind to, compare against, or restore at any time. To build that history, you must make commits. More on this later.

Key concepts:

- A **commit** is an intentional save point — a recorded set of changes you choose to capture
- A **repository** is the complete history of commits for a project
- Repositories can be synced across computers, enabling collaboration

![A diagram showing how a single document grows as the result of sequential changes](https://swcarpentry.github.io/git-novice/fig/play-changes.svg)

_Image: Software Carpentry, [CC-BY 4.0](https://creativecommons.org/licenses/by/4.0/)_

---

## 2. Setting Up Git

When using Git for the first time on a computer, configure your identity and preferences:

```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
git config --global core.editor "nano -w"
git config --global init.defaultBranch main
```

On macOS/Linux, also set:

```bash
git config --global core.autocrlf input
```

On Windows:

```bash
git config --global core.autocrlf true
```

The `--global` flag applies these settings to every project on your computer. Review your settings with:

```bash
git config --list --global
```

---

## 3. Creating a Repository

Create a new directory and initialize it as a Git repository:

```bash
mkdir recipes
cd recipes
git init
```

Git stores all tracking information in a hidden `.git` directory:

```bash
ls -a
```

```
.    ..    .git
```

Check the status of your repository at any time:

```bash
git status
```

```
On branch main

No commits yet

nothing to commit (create/copy files and use "git add" to track)
```

> **Note:** Never create a Git repository inside another Git repository. Check `git status` before running `git init` to make sure you're not already inside one.

---

## 4. Tracking Changes

### The modify-add-commit cycle

Create a file:

```bash
nano guacamole.md
```

```markdown
# Guacamole

## Ingredients

## Instructions
```

Check status — Git sees the new untracked file:

```bash
git status
```

**Stage** the file (tell Git to include it in the next commit):

```bash
git add guacamole.md
```

**Commit** the staged changes with a descriptive message:

```bash
git commit -m "Create initial structure for a Guacamole recipe"
```

### Making more changes

Edit the file to add ingredients, then review what changed:

```bash
git diff
```

The output shows lines added (`+`) and removed (`-`).

Stage and commit:

```bash
git add guacamole.md
git commit -m "Add ingredients for basic guacamole"
```

### The staging area

The staging area is where you build up a commit before making it permanent:

![A diagram showing how git add registers changes in the staging area, while git commit moves changes from the staging area to the repository](https://swcarpentry.github.io/git-novice/fig/git-staging-area.svg)

_Image: Software Carpentry, [CC-BY 4.0](https://creativecommons.org/licenses/by/4.0/)_

- `git add` specifies **what** goes in the next snapshot
- `git commit` **takes** the snapshot

Use `git diff` to see unstaged changes, and `git diff --staged` to see what's about to be committed.

### Viewing history

```bash
git log
```

Useful options:

```bash
git log --oneline
git log --oneline --graph
git log -3              # last 3 commits only
```

---

## 5. Exploring History

### Comparing versions

`HEAD` refers to the most recent commit. Use `~` to go further back:

```bash
git diff HEAD~1         # compare working directory to previous commit
git diff HEAD~2         # two commits back
```

You can also use the first 7 characters of a commit ID:

```bash
git diff f22b25e
```

### Restoring old versions

Discard uncommitted changes to a file:

```bash
git restore guacamole.md
```

Restore a file to a specific older version:

```bash
git restore -s HEAD~1 guacamole.md
```

To unstage a file (undo `git add`):

```bash
git restore --staged guacamole.md
```

![A diagram showing the entire git workflow: local changes are staged using git add, applied to the local repository using git commit, and can be restored from the repository using git restore](https://swcarpentry.github.io/git-novice/fig/git-checkout.svg)

_Image: [How Git works - a cartoon](https://figshare.com/articles/How_Git_works_a_cartoon/1328266), [CC-BY 4.0](https://creativecommons.org/licenses/by/4.0/)_

---

## 6. Ignoring Things

Create a `.gitignore` file to tell Git which files to skip:

```bash
nano .gitignore
```

```
*.png
pictures/
```

Common patterns:

- `*.log` — ignore all `.log` files
- `build/` — ignore an entire directory
- `!important.png` — exception: do NOT ignore this file
- `**/*.csv` — ignore `.csv` files in any subdirectory

Add and commit your `.gitignore`:

```bash
git add .gitignore
git commit -m "Ignore png files and the pictures folder"
```

---

## 7. Remotes in GitHub

### Create a remote repository

1. On [GitHub](https://github.com), create a new repository (leave it empty — no README, no license, no `.gitignore`)
2. Copy the SSH URL (e.g., `git@github.com:username/recipes.git`)

### Connect local to remote

```bash
git remote add origin git@github.com:username/recipes.git
git remote -v
```

### Set up SSH authentication

Check for existing SSH keys:

```bash
ls -al ~/.ssh
```

If no keys exist, generate a new key pair:

```bash
ssh-keygen -t ed25519 -C "your.email@example.com"
```

Press Enter to accept the default file location. Enter a passphrase when prompted (recommended).

Copy your **public** key:

```bash
cat ~/.ssh/id_ed25519.pub
```

Add it to GitHub:

1. Go to **Settings → SSH and GPG keys → New SSH key**
2. Paste your public key and give it a descriptive title
3. Click **Add SSH key**

Test the connection:

```bash
ssh -T git@github.com
```

You should see: `Hi username! You've successfully authenticated...`

### Authorize your SSH key for SSO (if required)

If your GitHub organization uses SAML Single Sign-On (SSO), you must authorize your SSH key before you can access organization repositories:

1. Go to **Settings → SSH and GPG keys**
2. Next to your SSH key, click **Configure SSO**
3. Find your organization and click **Authorize**

Without this step, push/pull operations to organization repos will fail with a permission error even though your key works for personal repos.

### Push and pull

Push local commits to GitHub:

```bash
git push -u origin main
```

Pull changes from GitHub:

```bash
git pull origin main
```

![A diagram showing how git push origin will push changes from the local repository to the remote](https://swcarpentry.github.io/git-novice/fig/github-repo-after-first-push.svg)

_Image: Software Carpentry, [CC-BY 4.0](https://creativecommons.org/licenses/by/4.0/)_

---

## Appendix

### Intermediate Topics: Collaboration, Conflicts, and Branching

This abridged lesson covers the fundamentals of using Git individually and pushing to a remote. The following intermediate topics are essential for team collaboration but require additional time to practice:

- **Collaborating** — cloning repos, the owner/collaborator workflow
- **Conflicts** — what happens when two people change the same lines, and how to resolve merge conflicts
- **Branching strategies** — working on features in parallel and merging them together

These topics are covered in the full Software Carpentry lesson. The conflicts episode is available here:
https://swcarpentry.github.io/git-novice/09-conflict.html

When your team is ready to adopt a branching strategy, [Git Flow](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow) is a powerful and robust workflow that supports both small and large teams with clearly defined roles for feature, release, and hotfix branches — though it comes with significant overhead and management complexity. It is well worth learning, but smaller teams looking to move quickly may prefer lighter-weight alternatives such as the [Feature Branch Workflow](https://www.atlassian.com/git/tutorials/comparing-workflows/feature-branch-workflow) (one branch per feature, merged back to main) or [Trunk-Based Development](https://www.atlassian.com/continuous-delivery/continuous-integration/trunk-based-development) (short-lived branches with frequent integration) to minimize process overhead while still maintaining a clean history.

---

### Quick Reference

| Action                  | Command                                |
| ----------------------- | -------------------------------------- |
| Configure Git           | `git config --global user.name "Name"` |
| Initialize a repo       | `git init`                             |
| Check status            | `git status`                           |
| Stage changes           | `git add <file>`                       |
| Commit changes          | `git commit -m "message"`              |
| View differences        | `git diff`                             |
| View staged differences | `git diff --staged`                    |
| View history            | `git log --oneline`                    |
| Restore a file          | `git restore <file>`                   |
| Restore from commit     | `git restore -s <commit> <file>`       |
| Add a remote            | `git remote add origin <url>`          |
| Push to remote          | `git push -u origin main`              |
| Pull from remote        | `git pull origin main`                 |

---

### Attribution

This material is adapted from [Version Control with Git](https://swcarpentry.github.io/git-novice/) by [Software Carpentry](https://software-carpentry.org/), licensed under [CC-BY 4.0](https://creativecommons.org/licenses/by/4.0/).

Source: https://swcarpentry.github.io/git-novice/
