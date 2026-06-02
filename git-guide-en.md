# Detailed Git Usage Guide

This guide is for developers who want to go from beginner level to practical team collaboration. It focuses on command-line Git and also mentions common GitHub, GitLab, VS Code, and GUI workflows.

## Table of Contents

1. [What Git Is](#what-git-is)
2. [Installation and Initial Setup](#installation-and-initial-setup)
3. [Core Git Concepts](#core-git-concepts)
4. [Command Cheat Sheet](#command-cheat-sheet)
5. [Creating or Getting a Repository](#creating-or-getting-a-repository)
6. [Daily Basic Workflow](#daily-basic-workflow)
7. [Checking Status, Diffs, and History](#checking-status-diffs-and-history)
8. [Working with Branches](#working-with-branches)
9. [Merging, Conflicts, and Conflict Resolution](#merging-conflicts-and-conflict-resolution)
10. [Using Rebase](#using-rebase)
11. [Undoing and Rolling Back Changes](#undoing-and-rolling-back-changes)
12. [Remote Repositories](#remote-repositories)
13. [Team Collaboration Workflow](#team-collaboration-workflow)
14. [Temporarily Saving Work with Stash](#temporarily-saving-work-with-stash)
15. [Version Tags](#version-tags)
16. [Ignoring Files with .gitignore](#ignoring-files-with-gitignore)
17. [Common Advanced Commands](#common-advanced-commands)
18. [Git Config, Aliases, and SSH](#git-config-aliases-and-ssh)
19. [VS Code and GUI Tools](#vs-code-and-gui-tools)
20. [Best Practices](#best-practices)
21. [Troubleshooting](#troubleshooting)
22. [Practice Exercises](#practice-exercises)

## What Git Is

Git is a distributed version control system. It records file changes, compares versions, supports collaboration, and lets you return to earlier states when needed.

Git is commonly used to:

- Track changes in code, documentation, and configuration files
- Create branches for parallel feature development
- Review code changes
- Collaborate through GitHub, GitLab, Bitbucket, and similar platforms
- Use Pull Requests or Merge Requests for code review
- Mark release versions with tags

The core value of Git is that every commit is a traceable snapshot. You can see who changed what, when it changed, why it changed, and how to return to a previous version.

## Installation and Initial Setup

### Check Whether Git Is Installed

```bash
git --version
```

Example output:

```text
git version 2.45.0
```

Line-by-line breakdown:

| Output | Meaning |
| --- | --- |
| `git version 2.45.0` | The currently installed Git version; major `2`, minor `45`, patch `0`. Seeing this output confirms Git is installed and available in the current shell path |

If you see a version number such as `git version 2.45.0`, Git is installed.

### macOS Installation

Option 1: Install Xcode Command Line Tools.

```bash
xcode-select --install
```

Option 2: Install with Homebrew.

```bash
brew install git
```

### Linux Installation

Debian / Ubuntu:

```bash
sudo apt update
sudo apt install git
```

Fedora:

```bash
sudo dnf install git
```

Arch Linux:

```bash
sudo pacman -S git
```

### Configure User Name and Email

Your name and email are written into commit metadata.

```bash
git config --global user.name "Your Name"
git config --global user.email "your-email@example.com"
```

View your configuration:

```bash
git config --global --list
```

Example output:

```text
user.name=Alice
user.email=alice@example.com
init.defaultbranch=main
core.editor=code --wait
core.autocrlf=input
```

Line-by-line breakdown:

| Output | Meaning |
| --- | --- |
| `user.name=Alice` | The author name recorded in commits, set by `git config --global user.name` |
| `user.email=alice@example.com` | The author email recorded in commits, set by `git config --global user.email` |
| `init.defaultbranch=main` | The default branch name when initializing a new repository |
| `core.editor=code --wait` | The editor Git opens for messages; here it is VS Code |
| `core.autocrlf=input` | Line-ending handling strategy; `input` is recommended for macOS/Linux |

View one setting:

```bash
git config --global user.name
git config --global user.email
```

### Set the Default Branch Name

Modern projects commonly use `main` as the default branch.

```bash
git config --global init.defaultBranch main
```

### Set the Default Editor

VS Code:

```bash
git config --global core.editor "code --wait"
```

Vim:

```bash
git config --global core.editor "vim"
```

### Configure Line Ending Handling

Recommended for macOS / Linux:

```bash
git config --global core.autocrlf input
```

Recommended for Windows:

```bash
git config --global core.autocrlf true
```

This reduces unnecessary diffs caused by line-ending differences across operating systems.

## Core Git Concepts

### Repository

A repository is a project directory managed by Git. It contains:

- Working files
- The `.git` directory
- Branch information
- Commit history
- Remote repository configuration

### The Three Areas

Daily Git work mainly moves changes through three areas:

| Area | Description |
| --- | --- |
| Working Tree | Files you are currently editing |
| Staging Area / Index | Changes prepared for the next commit |
| Local Repository | Committed history |

Basic flow:

```text
Edit files -> git add -> git commit
Working Tree -> Staging Area -> Local Repository
```

### Commit

A commit is a snapshot in Git history. Each commit has:

- A unique hash
- An author
- A timestamp
- A commit message
- File changes
- One or more parent commits

View recent commits:

```bash
git log --oneline
```

Example output:

```text
a1b2c3d Add login page
e4f5g6h Initialize project
```

Line-by-line breakdown:

Each line follows the format: `[short hash] [commit message]`

| Field | Example value | Meaning |
| --- | --- | --- |
| `a1b2c3d` | Short hash | The first 7 characters of the full commit hash; uniquely identifies this commit and can be used to reference it (e.g. `git show a1b2c3d`) |
| `Add login page` | Commit message | The description written with the `-m` flag at commit time; summarizes what this commit changed |
| Second line `e4f5g6h` | Older commit | `git log --oneline` lists commits from newest to oldest — entries further down are earlier commits |

### HEAD

`HEAD` represents the commit you are currently on. Usually it points to the latest commit of the current branch.

```bash
git log --oneline --decorate
```

Example:

```text
a1b2c3d (HEAD -> main) Add login page
```

### Branch

A branch is a movable pointer to a commit. Branches let you develop new work without affecting the main line of development.

Common branch names:

- `main`: stable main branch
- `develop`: integration branch for development
- `feature/login`: feature branch
- `fix/payment-bug`: bug fix branch
- `release/v1.2.0`: release branch

## Command Cheat Sheet

### Initialization and Clone

| Goal | Command |
| --- | --- |
| Initialize repository | `git init` |
| Clone repository | `git clone <url>` |
| Check status | `git status` |
| Check short status | `git status -sb` |

### Stage and Commit

| Goal | Command |
| --- | --- |
| Stage a file | `git add <file>` |
| Stage all changes | `git add .` |
| Commit | `git commit -m "message"` |
| Amend last commit | `git commit --amend` |

### Diff and History

| Goal | Command |
| --- | --- |
| View unstaged diff | `git diff` |
| View staged diff | `git diff --staged` |
| View log | `git log` |
| One-line log | `git log --oneline` |
| Graph log | `git log --oneline --graph --decorate --all` |
| Show commit | `git show <commit>` |

### Branches

| Goal | Command |
| --- | --- |
| List branches | `git branch` |
| Create branch | `git branch <name>` |
| Create and switch | `git switch -c <name>` |
| Switch branch | `git switch <name>` |
| Delete branch | `git branch -d <name>` |
| Force delete branch | `git branch -D <name>` |

### Remotes

| Goal | Command |
| --- | --- |
| View remotes | `git remote -v` |
| Add remote | `git remote add origin <url>` |
| Pull updates | `git pull` |
| Fetch only | `git fetch` |
| Push | `git push` |
| First push of branch | `git push -u origin <branch>` |

### Merge and Rebase

| Goal | Command |
| --- | --- |
| Merge branch | `git merge <branch>` |
| Abort merge | `git merge --abort` |
| Rebase | `git rebase <base>` |
| Continue rebase | `git rebase --continue` |
| Abort rebase | `git rebase --abort` |

### Undo

| Goal | Command |
| --- | --- |
| Discard working tree changes | `git restore <file>` |
| Unstage file | `git restore --staged <file>` |
| Undo commit and keep staged changes | `git reset --soft HEAD~1` |
| Undo commit and unstage changes | `git reset --mixed HEAD~1` |
| Hard reset and discard changes | `git reset --hard HEAD~1` |
| Create reverse commit | `git revert <commit>` |
| View HEAD movement history | `git reflog` |

## Creating or Getting a Repository

### Initialize a New Repository

Enter your project directory:

```bash
mkdir my-project
cd my-project
git init
```

Example output:

```text
Initialized empty Git repository in /Users/alice/my-project/.git/
```

Line-by-line breakdown:

| Output | Meaning |
| --- | --- |
| `Initialized empty Git repository in ...` | Git created a new empty repository in the current directory and generated the `.git/` subdirectory |
| The path portion | Shows the full path to the `.git/` directory, confirming the repository location |

Create a file and commit it:

```bash
echo "# My Project" > README.md
git add README.md
git commit -m "Initial commit"
```

### Clone a Remote Repository

HTTPS:

```bash
git clone https://github.com/user/repo.git
```

SSH:

```bash
git clone git@github.com:user/repo.git
```

Clone progress output example (using HTTPS):

```bash
git clone https://github.com/alice/my-project.git
```

Example output:

```text
Cloning into 'my-project'...
remote: Enumerating objects: 42, done.
remote: Counting objects: 100% (42/42), done.
remote: Compressing objects: 100% (28/28), done.
remote: Total 42 (delta 10), reused 38 (delta 8), pack-reused 0
Receiving objects: 100% (42/42), 56.12 KiB | 1.23 MiB/s, done.
Resolving deltas: 100% (10/10), done.
```

Line-by-line breakdown:

| Output | Meaning |
| --- | --- |
| `Cloning into 'my-project'...` | Git creates a `my-project/` directory in the current location and starts cloning |
| `remote: Enumerating objects: 42, done.` | The remote server counted 42 total objects (commits, files, etc.) in the repository |
| `remote: Counting objects: 100% (42/42), done.` | The server finished counting which objects need to be transferred |
| `remote: Compressing objects: 100% (28/28), done.` | The server compressed 28 objects to reduce transfer size |
| `remote: Total 42 (delta 10), reused 38 ...` | 42 objects transferred total; 10 use delta compression; `reused` means the server reused existing pack objects |
| `Receiving objects: 100% (42/42), 56.12 KiB ...` | Local download progress; the speed (`1.23 MiB/s`) reflects current network bandwidth |
| `Resolving deltas: 100% (10/10), done.` | Delta objects were reconstructed locally; the clone is complete |

Clone into a specific directory:

```bash
git clone git@github.com:user/repo.git my-folder
```

Enter the repository:

```bash
cd my-folder
```

## Daily Basic Workflow

The most common Git workflow is:

```bash
git status
git add .
git commit -m "Describe the change"
git pull
git push
```

Recommended order:

1. Pull remote updates before starting work
2. Create or switch to a feature branch
3. Modify files
4. Review the changes
5. Stage the changes you want to commit
6. Commit
7. Push to the remote repository
8. Open a Pull Request or Merge Request

### Example: Add a New Page

```bash
git switch -c feature/about-page
mkdir pages
echo "<h1>About</h1>" > pages/about.html
git status
git add pages/about.html
git commit -m "Add about page"
git push -u origin feature/about-page
```

Example output of a successful `git commit -m "..."`:

```text
[feature/about-page a1b2c3d] Add about page
 1 file changed, 1 insertion(+)
 create mode 100644 pages/about.html
```

Line-by-line breakdown:

| Output | Meaning |
| --- | --- |
| `[feature/about-page a1b2c3d]` | Inside the brackets: the branch the commit was made on, and the short hash of the new commit |
| `Add about page` | The commit message you provided |
| `1 file changed, 1 insertion(+)` | Change summary: one file was modified, with one line added |
| `create mode 100644 pages/about.html` | This is a newly created file; `100644` is the standard file permission mode |

## Checking Status, Diffs, and History

### Check Repository Status

```bash
git status
```

Example output:

```text
On branch feature/login
Your branch is ahead of 'origin/feature/login' by 2 commits.
  (use "git push" to publish your local commits)

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
	new file:   src/auth.js

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   src/login.js

Untracked files:
  (use "git add <file>..." to include in what will be committed)
	tests/auth.test.js
```

Line-by-line breakdown:

| Output | Meaning |
| --- | --- |
| `On branch feature/login` | The branch you are currently on |
| `ahead of 'origin/feature/login' by 2 commits` | Your local branch has 2 commits the remote does not; run `git push` to sync |
| `Changes to be committed` | These files are staged and will be included in the next `git commit` |
| `new file:   src/auth.js` | A new file that has been staged with `git add` |
| `Changes not staged for commit` | Files that were modified but not yet staged with `git add` |
| `modified:   src/login.js` | This file has changes that have not been staged yet |
| `Untracked files` | New files Git is not aware of at all |
| `tests/auth.test.js` | An untracked new file; needs `git add` to bring it under version control |

Short format:

```bash
git status -sb
```

Example:

```text
## feature/login
 M src/login.js
?? src/login.test.js
```

Meaning:

- `M`: file modified
- `??`: new file not tracked by Git
- `A`: new file staged
- `D`: file deleted

### View Unstaged Differences

```bash
git diff
```

Example output:

```diff
diff --git a/src/login.js b/src/login.js
index 3a1b2c4..d5e6f7a 100644
--- a/src/login.js
+++ b/src/login.js
@@ -10,7 +10,8 @@ function login(user) {
   if (!user) {
-    return null;
+    return { error: "User not found" };
   }
 }
```

Line-by-line breakdown:

| Output | Meaning |
| --- | --- |
| `diff --git a/src/login.js b/src/login.js` | The files being compared; `a/` is the old version, `b/` is the new version |
| `index 3a1b2c4..d5e6f7a 100644` | Old object hash → new object hash; `100644` is the file mode (regular file) |
| `--- a/src/login.js` | The `---` side is the file before the change |
| `+++ b/src/login.js` | The `+++` side is the file after the change |
| `@@ -10,7 +10,8 @@` | Hunk location: old file starts at line 10 for 7 lines; new file starts at line 10 for 8 lines |
| `-    return null;` | A deleted line, shown in red in most terminals |
| `+    return { error: "..." };` | An added line, shown in green in most terminals |
| Lines with no prefix | Unchanged context lines that help locate the change; they are not part of the modification |

### View Staged Differences

```bash
git diff --staged
```

You can also use:

```bash
git diff --cached
```

### View Differences for One File

```bash
git diff src/app.js
```

### View Commit History

Full log:

```bash
git log
```

Example output:

```text
commit f3a1b2c4d5e6f7a8b9c0d1e2f3a4b5c6d7e8f9a0
Author: Alice <alice@example.com>
Date:   Wed Jan 17 14:05:33 2024 +0800

    Add about page

    Added a static about page under pages/about.html.

commit a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0
Author: Alice <alice@example.com>
Date:   Mon Jan 15 10:23:45 2024 +0800

    Initial commit
```

Line-by-line breakdown:

| Output | Meaning |
| --- | --- |
| `commit f3a1b2c4...` | The full 40-character hash that uniquely identifies this commit |
| `Author: Alice <alice@example.com>` | The commit author's name and email |
| `Date:   Wed Jan 17 14:05:33 2024 +0800` | Commit timestamp and timezone; commits are listed newest first |
| Paragraph after the blank line | The commit message; the first line is the subject, and an optional paragraph after a blank line is the body |

One-line log:

```bash
git log --oneline
```

Graph log:

```bash
git log --oneline --graph --decorate --all
```

Example output:

```text
* f3a1b2c (HEAD -> main) Merge branch 'feature/login'
|\
| * d4e5f6g (origin/feature/login, feature/login) Add login validation
| * a1b2c3d Add login form
* | b2c3d4e Update homepage layout
|/
* e1f2g3h Initialize project
```

Line-by-line breakdown:

```text
* f3a1b2c (HEAD -> main) Merge branch 'feature/login'
```
`*` is one commit. `HEAD -> main` means this is the latest commit on your current branch (main). The text after the closing parenthesis is the commit message.

```text
|\
```
The branch forks here. The left `|` continues the main branch history line. The `\` opens the feature branch history line going to the right.

```text
| * d4e5f6g (origin/feature/login, feature/login) Add login validation
| * a1b2c3d Add login form
```
The `*` on the right side of `|` are commits on the feature/login branch, indented to show they belong to the right-side line. Both the local and remote branch names appear in parentheses, meaning they point to the same commit.

```text
* | b2c3d4e Update homepage layout
```
The `*` on the left is a commit on main that happened in parallel with feature branch work. The two lines run side by side without affecting each other.

```text
|/
* e1f2g3h Initialize project
```
`|/` is where the two lines converge. The merge commit above is the node created by merging them. The final `*` is the common ancestor of both branches — the point where they originally split.

History for one file:

```bash
git log -- src/app.js
```

Show details for one commit:

```bash
git show a1b2c3d
```

Example output:

```text
commit a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0
Author: Alice <alice@example.com>
Date:   Mon Jan 15 10:23:45 2024 +0800

    Add login form

    Includes basic validation for required fields.

diff --git a/src/login.js b/src/login.js
new file mode 100644
index 0000000..3a1b2c4
--- /dev/null
+++ b/src/login.js
@@ -0,0 +1,5 @@
+export function login(user) {
+  if (!user) {
+    return null;
+  }
+}
```

Line-by-line breakdown:

| Output | Meaning |
| --- | --- |
| `commit a1b2c3d4e5f6...` | The full commit hash that uniquely identifies this commit; you can reference it by its first few characters |
| `Author: Alice <alice@example.com>` | The commit author's name and email, set by `git config user.name/email` |
| `Date:   Mon Jan 15 10:23:45 2024 +0800` | Commit timestamp; `+0800` is the UTC offset (UTC+8, China Standard Time) |
| First paragraph after the blank line | The commit message subject — a short summary of what changed |
| Second paragraph after a blank line | The commit message body — explains why the change was made (optional) |
| `diff --git ...` onward | The full diff for this commit, in the same format as `git diff` output |
| `new file mode 100644` | The file is newly created; `--- /dev/null` confirms the file did not exist before the commit |

Show only the file list in one commit:

```bash
git show --name-only a1b2c3d
```

Example output:

```text
commit a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0
Author: Alice <alice@example.com>
Date:   Mon Jan 15 10:23:45 2024 +0800

    Add login form

    Includes basic validation for required fields.

src/login.js
src/login.css
tests/login.test.js
```

Line-by-line breakdown:

| Output | Meaning |
| --- | --- |
| The commit metadata at the top | Same as `git show`: hash, author, timestamp, and commit message |
| The file paths at the bottom | Files added, modified, or deleted in this commit — one path per line, without any diff content |

The difference from `git show`: `--name-only` lists only file names and omits the diff. Use it when you want a quick summary of which files a commit touched.

## Working with Branches

### List Branches

List local branches:

```bash
git branch
```

Example output:

```text
* feature/login
  develop
  main
```

Line-by-line breakdown:

| Output | Meaning |
| --- | --- |
| `* feature/login` | The `*` prefix marks the current branch (where HEAD points) |
| `  develop` | Another local branch; no `*` prefix |
| `  main` | Another local branch |

List local and remote branches:

```bash
git branch -a
```

Example output:

```text
* feature/login
  develop
  main
  remotes/origin/HEAD -> origin/main
  remotes/origin/develop
  remotes/origin/feature/login
  remotes/origin/main
```

Line-by-line breakdown:

| Output | Meaning |
| --- | --- |
| `* feature/login` / `  develop` / `  main` | Local branches, same as plain `git branch` output |
| `remotes/origin/HEAD -> origin/main` | The default branch on the remote; `origin/main` is what the server checks out by default |
| `remotes/origin/develop` etc. | Remote-tracking branches — read-only local snapshots last synced from the remote via fetch or pull |

Show upstream tracking information:

```bash
git branch -vv
```

Example output:

```text
* feature/login  a1b2c3d [origin/feature/login: ahead 2] Add login form
  main           e1f2g3h [origin/main] Initialize project
  develop        b2c3d4e [origin/develop: behind 3] Update README
```

Line-by-line breakdown, using the first row as an example:

```text
* feature/login  a1b2c3d [origin/feature/login: ahead 2] Add login form
```

| Field | Example value | Meaning |
| --- | --- | --- |
| `*` | `*` | You are currently on this branch; other branches have no marker |
| Branch name | `feature/login` | Local branch name |
| Short hash | `a1b2c3d` | Short hash of the latest commit on that branch; can be used to reference the commit |
| `origin/feature/login` | — | The remote branch this local branch tracks (upstream) |
| `ahead 2` | — | Local has 2 commits the remote does not — run `git push` to sync |
| `behind 3` | — | Remote has 3 commits the local does not — run `git pull` to sync |
| Trailing text | `Add login form` | The commit message of the latest commit on that branch |

No `ahead`/`behind` means the local and remote branches are fully in sync.

### Create a Branch

```bash
git branch feature/login
```

Create and switch:

```bash
git switch -c feature/login
```

Example output:

```text
Switched to a new branch 'feature/login'
```

Line-by-line breakdown:

| Output | Meaning |
| --- | --- |
| `Switched to a new branch 'feature/login'` | Git created the new branch and immediately switched to it; HEAD now points to `feature/login` |

The older command also works:

```bash
git checkout -b feature/login
```

Example output:

```text
Switched to a new branch 'feature/login'
```

Line-by-line breakdown:

| Output | Meaning |
| --- | --- |
| `Switched to a new branch 'feature/login'` | Identical output to `git switch -c`; this is the older equivalent command with the same effect |

### Switch Branches

```bash
git switch main
git switch feature/login
```

Example output when switching to `main`:

```text
Switched to branch 'main'
Your branch is up to date with 'origin/main'.
```

Line-by-line breakdown:

| Output | Meaning |
| --- | --- |
| `Switched to branch 'main'` | You are now on the main branch; HEAD points to `main` |
| `Your branch is up to date with 'origin/main'.` | Your local main is in sync with the remote; if you have unpushed commits, it would show `ahead` information instead |

### Rename a Branch

Rename the current branch:

```bash
git branch -m feature/sign-in
```

Rename a specific branch:

```bash
git branch -m old-name new-name
```

### Delete a Branch

Delete a merged branch:

```bash
git branch -d feature/login
```

Example output:

```text
Deleted branch feature/login (was a1b2c3d).
```

Line-by-line breakdown:

| Output | Meaning |
| --- | --- |
| `Deleted branch feature/login` | The branch was successfully deleted |
| `(was a1b2c3d)` | The short hash of the branch's last commit before deletion; to recover it, use `git switch -c feature/login a1b2c3d` |

Force delete an unmerged branch:

```bash
git branch -D feature/login
```

Example output:

```text
Deleted branch feature/login (was a1b2c3d).
```

Line-by-line breakdown:

| Output | Meaning |
| --- | --- |
| `Deleted branch feature/login` | The branch was force-deleted; the output format is the same as `-d` |
| `(was a1b2c3d)` | The last commit hash before deletion; `-D` does not check whether the branch was merged, so use it carefully if the branch has unmerged commits |

Delete a remote branch:

```bash
git push origin --delete feature/login
```

Example output:

```text
To github.com:alice/my-project.git
 - [deleted]         feature/login
```

Line-by-line breakdown:

| Output | Meaning |
| --- | --- |
| `- [deleted]         feature/login` | The remote branch has been deleted; `-` indicates a deletion operation |
| Note | The local branch with the same name is not affected; delete it separately with `git branch -d feature/login` if needed |

### Push a New Branch

```bash
git push -u origin feature/login
```

`-u` sets the upstream branch. After that, you can usually run:

```bash
git push
git pull
```

## Merging, Conflicts, and Conflict Resolution

### Merge a Branch

Suppose you want to merge `feature/login` into `main`:

```bash
git switch main
git pull
git merge feature/login
```

If there are no conflicts, Git completes the merge automatically.

### Fast-Forward Merge

If `main` has no new commits and `feature/login` continues from `main`, Git can simply move the `main` pointer forward. This is called a fast-forward merge.

```text
main:    A---B
feature:     C---D

After merge:
main:    A---B---C---D
```

Example output of a fast-forward merge:

```text
Updating e1f2g3h..a1b2c3d
Fast-forward
 src/login.js | 20 ++++++++++++++++++++
 1 file changed, 20 insertions(+)
 create mode 100644 src/login.js
```

Line-by-line breakdown:

| Output | Meaning |
| --- | --- |
| `Updating e1f2g3h..a1b2c3d` | The main pointer moved from the old commit hash to the new one |
| `Fast-forward` | This merge was a fast-forward; no extra merge commit was created |
| `src/login.js \| 20 +++++...` | File change summary: filename, line count, `+` signs for added lines |
| `1 file changed, 20 insertions(+)` | Summary: 1 file changed, 20 lines added in total |
| `create mode 100644 src/login.js` | This file is newly created |

### Non-Fast-Forward Merge

If both branches have new commits, Git creates a merge commit.

```text
main:    A---B---E
feature:     C---D

After merge:
main:    A---B---E---M
              \     /
               C---D
```

Example output of a merge commit:

```text
Merge made by the 'ort' strategy.
 src/login.js | 20 ++++++++++++++++++++
 1 file changed, 20 insertions(+)
 create mode 100644 src/login.js
```

Line-by-line breakdown:

| Output | Meaning |
| --- | --- |
| `Merge made by the 'ort' strategy.` | Git used the `ort` merge strategy (the default) and created a merge commit; this is not a fast-forward |
| `src/login.js \| 20 +++++...` | File changes introduced by the merged branch |
| `1 file changed, 20 insertions(+)` | Summary: total lines changed by this merge |

### Force a Merge Commit

Some teams prefer preserving merge records:

```bash
git merge --no-ff feature/login
```

### What a Conflict Is

A conflict usually happens when two branches modify the same part of the same file, and Git cannot decide which version to keep.

A conflicted file may look like this:

```text
<<<<<<< HEAD
console.log("Login");
=======
console.log("Sign in");
>>>>>>> feature/login
```

Meaning:

- From `<<<<<<< HEAD` to `=======`: content from the current branch
- From `=======` to `>>>>>>> feature/login`: content from the branch being merged

### Steps to Resolve a Conflict

1. Check conflicted files

```bash
git status
```

2. Open the files and manually edit them into the correct final content

```js
console.log("Login");
```

3. Mark the conflict as resolved

```bash
git add src/login.js
```

4. Complete the merge commit

```bash
git commit
```

If Git already prepared a merge commit message, save and exit the editor.

### Abort a Merge

If you decide not to continue:

```bash
git merge --abort
```

## Using Rebase

`rebase` moves the commits of your current branch onto the latest commit of another branch. It makes history more linear.

### Basic Example

```bash
git switch feature/login
git fetch origin
git rebase origin/main
```

Example output when there are no conflicts:

```text
Successfully rebased and updated refs/heads/feature/login.
```

Line-by-line breakdown:

| Output | Meaning |
| --- | --- |
| `Successfully rebased` | All commits were successfully moved on top of the latest commit in origin/main |
| `updated refs/heads/feature/login` | The local branch pointer has been updated to the end of the new commit chain; the original commit hashes (D, E) have been replaced by new ones (D', E') |

Original history:

```text
main:    A---B---C
feature:     D---E
```

After rebase:

```text
main:    A---B---C
feature:         D'---E'
```

Note that `D'` and `E'` are new commit hashes.

### Conflict During Rebase

After resolving a conflict:

```bash
git add conflicted-file.js
git rebase --continue
```

Skip the current commit:

```bash
git rebase --skip
```

Abort the rebase:

```bash
git rebase --abort
```

### Interactive Rebase

Interactive rebase is used to clean up commit history, such as combining commits, editing commit messages, or removing commits.

```bash
git rebase -i HEAD~3
```

Example screen:

```text
pick a1b2c3d Add login form
pick b2c3d4e Fix typo
pick c3d4e5f Add login validation
```

Common actions:

| Command | Meaning |
| --- | --- |
| pick | Keep the commit |
| reword | Edit the commit message |
| edit | Stop to modify the commit |
| squash | Combine into the previous commit and edit the message |
| fixup | Combine into the previous commit and discard this message |
| drop | Remove the commit |

Combine the typo fix into the previous commit:

```text
pick a1b2c3d Add login form
fixup b2c3d4e Fix typo
pick c3d4e5f Add login validation
```

### Important Rebase Rule

Do not casually rebase public branches shared by other people, because rebase rewrites commit history.

Good candidates for rebase:

- Your own local feature branch
- A branch nobody else is building on
- Cleanup before merging your Pull Request

Avoid rebasing:

- `main`
- `develop`
- release branches
- branches shared by multiple people

## Undoing and Rolling Back Changes

Git has several undo commands. First identify which area you want to undo: working tree, staging area, or commit history.

### Discard Working Tree Changes

Discard unstaged changes in one file:

```bash
git restore src/app.js
```

Discard all unstaged changes:

```bash
git restore .
```

Warning: this loses uncommitted working tree changes.

### Unstage Changes

If you already ran `git add` but have not committed:

```bash
git restore --staged src/app.js
```

Unstage all files:

```bash
git restore --staged .
```

### Amend the Last Commit

If you forgot to include a file:

```bash
git add missing-file.js
git commit --amend
```

Example output (after the editor opens, you save and close it):

```text
[feature/login a2b3c4d] Add login form with validation
 Date: Mon Jan 15 10:23:45 2024 +0800
 1 file changed, 25 insertions(+)
 create mode 100644 src/login.js
```

Line-by-line breakdown:

| Output | Meaning |
| --- | --- |
| `[feature/login a2b3c4d]` | The branch name and the short hash of the new commit; amend always produces a brand-new commit — the original hash is discarded |
| `Add login form with validation` | The commit message saved in the editor |
| `Date: Mon Jan 15 10:23:45 2024 +0800` | The original commit's authored date is preserved; this is not the time amend was run |
| `1 file changed, 25 insertions(+)` | Change summary including the newly added file |

If you only want to change the commit message:

```bash
git commit --amend -m "Better commit message"
```

Example output:

```text
[feature/login a2b3c4d] Better commit message
 Date: Mon Jan 15 10:23:45 2024 +0800
 1 file changed, 25 insertions(+)
 create mode 100644 src/login.js
```

Line-by-line breakdown:

| Output | Meaning |
| --- | --- |
| `[feature/login a2b3c4d]` | Same format as amend without `-m`; the hash is updated, the old commit hash is discarded |
| `Better commit message` | The new message supplied directly via `-m`, without opening an editor |
| `Date:` | The original authored date is preserved, same as amend without `-m` |

Be careful amending a commit that has already been pushed to a shared branch, because it rewrites history.

### reset

`reset` moves the current branch pointer and may affect the staging area and working tree.

Move HEAD only, keeping the staging area and working tree:

```bash
git reset --soft HEAD~1
```

Example output:

```text
(no output)
```

This command normally produces no output. After it runs, HEAD moves back one commit but your changes remain staged. You can confirm with `git status`:

```text
On branch feature/login
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   src/login.js
```

Move HEAD, keep the working tree, and clear the staging area:

```bash
git reset --mixed HEAD~1
```

Example output:

```text
Unstaged changes after reset:
M	src/login.js
```

Line-by-line breakdown:

| Output | Meaning |
| --- | --- |
| `Unstaged changes after reset:` | After moving HEAD, the previously staged changes were moved back to the working tree (unstaged state) |
| `M	src/login.js` | `M` means the file has modifications but is no longer staged; run `git add` again when ready |

Move HEAD and discard both staging area and working tree changes:

```bash
git reset --hard HEAD~1
```

Example output:

```text
HEAD is now at e1f2g3h Initial commit
```

Line-by-line breakdown:

| Output | Meaning |
| --- | --- |
| `HEAD is now at e1f2g3h` | HEAD has moved to the specified commit, identified by its short hash |
| `Initial commit` | The commit message of that commit; the staging area and working tree changes are permanently discarded (unless recovered via reflog) |

`--hard` is destructive. Confirm that you do not need the changes before using it.

### revert

`revert` creates a new commit that reverses the changes from an earlier commit.

```bash
git revert a1b2c3d
```

Example output (after Git opens the editor for you to confirm the commit message):

```text
[feature/login b2c3d4e] Revert "Add login form"
 1 file changed, 5 deletions(-)
 delete mode 100644 src/login.js
```

Line-by-line breakdown:

| Output | Meaning |
| --- | --- |
| `[feature/login b2c3d4e]` | The branch and short hash of the newly created revert commit |
| `Revert "Add login form"` | The auto-generated commit message indicating which commit is being reversed |
| `1 file changed, 5 deletions(-)` | Change summary: the original commit added 5 lines; the revert removes them |
| `delete mode 100644 src/login.js` | The original commit created this file; the revert deletes it |

This is suitable for public branches because it does not rewrite history.

### reset vs revert

| Command | Rewrites History | Suitable for Public Branches | Typical Use |
| --- | --- | --- | --- |
| reset | Yes | Usually no | Undo local commits |
| revert | No | Yes | Roll back pushed commits |

### Recover Lost Commits with reflog

`reflog` records movements of `HEAD`.

```bash
git reflog
```

Example:

```text
a1b2c3d HEAD@{0}: reset: moving to HEAD~1
d4e5f6g HEAD@{1}: commit: Add payment page
```

Recover the commit:

```bash
git switch -c recover-payment d4e5f6g
```

## Remote Repositories

### View Remotes

```bash
git remote -v
```

Example:

```text
origin  git@github.com:user/repo.git (fetch)
origin  git@github.com:user/repo.git (push)
```

Line-by-line breakdown:

| Output | Meaning |
| --- | --- |
| `origin` | The alias for the remote repository; the default name when cloning is `origin`, and it can be renamed with `git remote rename` |
| `git@github.com:user/repo.git` | The SSH address of the remote repository |
| `(fetch)` | This address is used when running `git fetch` or `git pull` |
| `(push)` | This address is used when running `git push`; typically fetch and push use the same address, but they can be configured separately |

### Add a Remote

```bash
git remote add origin git@github.com:user/repo.git
```

### Change a Remote URL

```bash
git remote set-url origin git@github.com:user/new-repo.git
```

### Pull Remote Updates

`fetch` downloads remote changes without automatically merging them:

```bash
git fetch origin
```

Example output:

```text
remote: Enumerating objects: 5, done.
remote: Counting objects: 100% (5/5), done.
remote: Compressing objects: 100% (3/3), done.
remote: Total 3 (delta 1), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), 892 bytes | 892.00 KiB/s, done.
From github.com:alice/my-project
   e1f2g3h..f3a1b2c  main       -> origin/main
 * [new branch]      feature/nav -> origin/feature/nav
```

Line-by-line breakdown:

| Output | Meaning |
| --- | --- |
| `remote: Enumerating/Counting/Compressing...` | The server is preparing and compressing objects to transfer |
| `Unpacking objects: 100%...` | Your local machine is unpacking the received objects |
| `From github.com:alice/my-project` | Which remote repository the data came from |
| `e1f2g3h..f3a1b2c  main -> origin/main` | The remote-tracking pointer for `main` moved from the old hash to the new one; your local `main` is not changed |
| `* [new branch]  feature/nav -> origin/feature/nav` | The remote has a new branch that did not exist locally before |

`pull` means fetch plus merge or rebase:

```bash
git pull
```

Example output (fast-forward case):

```text
remote: Enumerating objects: 5, done.
remote: Counting objects: 100% (5/5), done.
remote: Compressing objects: 100% (3/3), done.
remote: Total 3 (delta 1), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), 892 bytes | 892.00 KiB/s, done.
From github.com:alice/my-project
   e1f2g3h..f3a1b2c  main       -> origin/main
Updating e1f2g3h..f3a1b2c
Fast-forward
 README.md | 2 ++
 1 file changed, 2 insertions(+)
```

Line-by-line breakdown:

| Output | Meaning |
| --- | --- |
| The `remote:` and `Unpacking` lines | Same as `git fetch`: download and unpack the new objects |
| `Updating e1f2g3h..f3a1b2c` | Your local `main` pointer moved from the old hash to the new one |
| `Fast-forward` | The merge was a fast-forward; no new merge commit was created |
| `README.md \| 2 ++` | The pulled commits added 2 lines to README.md |

Pull using rebase:

```bash
git pull --rebase
```

Example output:

```text
remote: Enumerating objects: 5, done.
remote: Counting objects: 100% (5/5), done.
remote: Compressing objects: 100% (3/3), done.
remote: Total 3 (delta 1), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), 892 bytes | 892.00 KiB/s, done.
From github.com:alice/my-project
   e1f2g3h..f3a1b2c  main       -> origin/main
Successfully rebased and updated refs/heads/feature/login.
```

Line-by-line breakdown:

| Output | Meaning |
| --- | --- |
| The `remote:` and `Unpacking` lines | Same as the fetch phase of a regular `git pull`; download and unpack remote objects |
| `e1f2g3h..f3a1b2c  main -> origin/main` | The range of updates to the remote-tracking pointer |
| `Successfully rebased and updated refs/heads/feature/login.` | The integration used rebase instead of merge, producing a more linear history with no extra merge commit |

### Push to a Remote

```bash
git push
```

Example output:

```text
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Delta compression using up to 8 threads
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 362 bytes | 362.00 KiB/s, done.
Total 3 (delta 1), reused 0 (delta 0), pack-reused 0
To github.com:alice/my-project.git
   e1f2g3h..a1b2c3d  feature/login -> feature/login
```

Line-by-line breakdown:

| Output | Meaning |
| --- | --- |
| `Enumerating/Counting/Compressing...` | Local Git is packaging the objects to upload |
| `Writing objects: 100%...` | Objects are being uploaded to the remote server |
| `To github.com:alice/my-project.git` | The remote URL being pushed to |
| `e1f2g3h..a1b2c3d  feature/login -> feature/login` | The remote `feature/login` branch moved from the old hash to the new one |

First push of a new branch:

```bash
git push -u origin feature/login
```

Example output:

```text
Enumerating objects: 8, done.
Counting objects: 100% (8/8), done.
Delta compression using up to 8 threads
Compressing objects: 100% (5/5), done.
Writing objects: 100% (8/8), 734 bytes | 734.00 KiB/s, done.
Total 8 (delta 1), reused 0 (delta 0), pack-reused 0
remote: Resolving deltas: 100% (1/1), done.
To github.com:alice/my-project.git
 * [new branch]      feature/login -> origin/feature/login
Branch 'feature/login' set up to track remote branch 'feature/login' from 'origin'.
```

Line-by-line breakdown:

| Output | Meaning |
| --- | --- |
| `* [new branch]  feature/login -> origin/feature/login` | A new branch was created on the remote with the same name |
| `Branch 'feature/login' set up to track remote branch 'feature/login' from 'origin'.` | The effect of `-u`: a tracking relationship is established so future `git push` and `git pull` need no extra arguments |

### Force Push

After rebasing a feature branch, you may need to update the remote branch:

```bash
git push --force-with-lease
```

Example output when successful:

```text
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Delta compression using up to 8 threads
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 362 bytes | 362.00 KiB/s, done.
Total 3 (delta 1), reused 0 (delta 0), pack-reused 0
To github.com:alice/my-project.git
 + a1b2c3d...b2c3d4e feature/login -> feature/login (forced update)
```

Example output when rejected (someone pushed to the remote after your last fetch):

```text
error: failed to push some refs to 'github.com:alice/my-project.git'
hint: Updates were rejected because the remote contains work that you do not
hint: have locally. This is usually caused by another push.
```

Line-by-line breakdown:

| Output | Meaning |
| --- | --- |
| `(forced update)` | The trailing label on a successful push, indicating the remote history was overwritten |
| `+` prefix | Unlike the space prefix on a normal push, `+` indicates a force push |
| `error: failed to push some refs` | The push was rejected |
| `Updates were rejected because the remote contains work...` | The `--force-with-lease` protection activated: someone updated the remote since your last fetch, so Git refuses to overwrite their commits |
| When rejected | Run `git fetch` first, review the new remote commits, then decide how to proceed |

Prefer `--force-with-lease` over `--force`. It is safer because it checks whether someone else updated the remote branch first.

## Team Collaboration Workflow

### Common Branch Models

Simple teams can use:

```text
main
feature/*
fix/*
```

More complex teams may use:

```text
main
develop
feature/*
release/*
hotfix/*
```

### Recommended Daily Workflow

1. Update the main branch

```bash
git switch main
git pull
```

2. Create a feature branch

```bash
git switch -c feature/user-profile
```

3. Develop and commit

```bash
git add src/profile.js
git commit -m "Add user profile page"
```

4. Sync with the main branch

```bash
git fetch origin
git rebase origin/main
```

5. Push the branch

```bash
git push -u origin feature/user-profile
```

6. Open a Pull Request or Merge Request

On GitHub or GitLab, choose:

```text
base: main
compare: feature/user-profile
```

7. Merge after code review

Common merge options:

- Merge commit
- Squash and merge
- Rebase and merge

### Pull Request Description Example

Title:

```text
Add user profile page
```

Description:

```markdown
## Summary

- Add user profile page
- Load current user data
- Add empty state when profile is incomplete

## Testing

- Ran unit tests
- Manually checked profile page in browser

## Screenshots

Attach screenshots if UI changed.
```

### Commit Message Guidance

A good commit message says what changed and, when useful, why it changed.

Recommended:

```text
Add validation for checkout form
```

Not recommended:

```text
fix
update
changes
```

You can use Conventional Commits:

```text
feat: add user profile page
fix: handle empty cart checkout
docs: update Git guide
test: add login validation tests
refactor: simplify payment service
```

## Temporarily Saving Work with Stash

`stash` temporarily saves uncommitted changes. It is useful when you need to switch branches quickly but are not ready to commit.

### Save Changes

```bash
git stash
```

Example output:

```text
Saved working directory and index state WIP on feature/login: a1b2c3d Add login form
```

Line-by-line breakdown:

| Output | Meaning |
| --- | --- |
| `Saved working directory and index state` | Changes from both the working tree and staging area have been saved |
| `WIP on feature/login:` | The branch you were on when you stashed (WIP = Work In Progress) |
| `a1b2c3d Add login form` | The hash and message of the commit HEAD was pointing to; helps you identify the context of this stash entry |

With a message:

```bash
git stash push -m "WIP login form"
```

Example output:

```text
Saved working directory and index state On feature/login: WIP login form
```

Line-by-line breakdown:

| Output | Meaning |
| --- | --- |
| `Saved working directory and index state` | Changes from both the working tree and staging area have been saved; same format as plain `git stash` |
| `On feature/login:` | The branch you were on when you ran the stash command |
| `WIP login form` | The custom description supplied via `-m`, which replaces the auto-generated message and makes the entry easy to identify in `git stash list` |

Include untracked files:

```bash
git stash -u
```

### List Stashes

```bash
git stash list
```

Example:

```text
stash@{0}: On feature/login: WIP login form
stash@{1}: On main: Try new navbar
```

### Restore a Stash

Apply and keep the stash:

```bash
git stash apply stash@{0}
```

Example output:

```text
On branch feature/login
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   src/login.js

no changes added to commit (use "git add" and/or "git commit -a")
```

Line-by-line breakdown:

| Output | Meaning |
| --- | --- |
| `On branch feature/login` | The current branch |
| `Changes not staged for commit:` | The stashed changes have been restored to the working tree but are not staged |
| `modified:   src/login.js` | The specific file restored; run `git add` again to stage it |

`apply` does not remove the stash entry, so you can apply it again if needed.

Apply and remove the stash:

```bash
git stash pop
```

Example output:

```text
On branch feature/login
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   src/login.js

Dropped stash@{0} (7a3f1b2c4d5e6f7a8b9c0d1e2f3a4b5c6d7e8f9a)
```

`pop` produces the same output as `apply`, except for the final line `Dropped stash@{0} (...)`, which confirms the stash entry was automatically deleted after being restored.

Delete one stash:

```bash
git stash drop stash@{0}
```

Example output:

```text
Dropped stash@{0} (7a3f1b2c4d5e6f7a8b9c0d1e2f3a4b5c6d7e8f9a)
```

Line-by-line breakdown:

| Output | Meaning |
| --- | --- |
| `Dropped stash@{0}` | The specified stash entry has been successfully deleted |
| The hash in parentheses | The full object hash of that stash entry, for reference only; once deleted it cannot be recovered through normal means |

Delete all stashes:

```bash
git stash clear
```

## Version Tags

Tags mark release versions such as `v1.0.0`.

### List Tags

```bash
git tag
```

Example output:

```text
v0.9.0
v1.0.0
v1.1.0
v2.0.0-beta
```

Line-by-line breakdown:

| Output | Meaning |
| --- | --- |
| Each line | One tag name, listed in alphabetical or version order |
| `v1.0.0` etc. | Typically follows Semantic Versioning (SemVer): major.minor.patch |
| `v2.0.0-beta` | A pre-release tag indicating this is a beta version |

If the repository has no tags, the command produces no output.

### Create a Lightweight Tag

```bash
git tag v1.0.0
```

### Create an Annotated Tag

Annotated tags are recommended for releases:

```bash
git tag -a v1.0.0 -m "Release version 1.0.0"
```

### Push Tags

Push one tag:

```bash
git push origin v1.0.0
```

Example output:

```text
Enumerating objects: 1, done.
Counting objects: 100% (1/1), done.
Writing objects: 100% (1/1), 162 bytes | 162.00 KiB/s, done.
Total 1 (delta 0), reused 0 (delta 0), pack-reused 0
To github.com:alice/my-project.git
 * [new tag]         v1.0.0 -> v1.0.0
```

Line-by-line breakdown:

| Output | Meaning |
| --- | --- |
| `* [new tag]         v1.0.0 -> v1.0.0` | A new tag named `v1.0.0` was successfully created on the remote; `*` indicates this is a newly created object |

Push all tags:

```bash
git push origin --tags
```

Example output:

```text
Enumerating objects: 3, done.
Counting objects: 100% (3/3), done.
Writing objects: 100% (3/3), 486 bytes | 486.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
To github.com:alice/my-project.git
 * [new tag]         v0.9.0 -> v0.9.0
 * [new tag]         v1.0.0 -> v1.0.0
 * [new tag]         v1.1.0 -> v1.1.0
```

Line-by-line breakdown:

| Output | Meaning |
| --- | --- |
| Each `* [new tag]` line | One tag that was pushed to the remote; three local tags that did not exist on the remote were pushed in this example |

### Delete Tags

Delete a local tag:

```bash
git tag -d v1.0.0
```

Example output:

```text
Deleted tag 'v1.0.0' (was a1b2c3d)
```

Line-by-line breakdown:

| Output | Meaning |
| --- | --- |
| `Deleted tag 'v1.0.0'` | The local tag has been successfully deleted |
| `(was a1b2c3d)` | The commit hash the tag was pointing to before deletion; to recreate it, run `git tag v1.0.0 a1b2c3d` |

Delete a remote tag:

```bash
git push origin --delete v1.0.0
```

Example output:

```text
To github.com:alice/my-project.git
 - [deleted]         v1.0.0
```

Line-by-line breakdown:

| Output | Meaning |
| --- | --- |
| `- [deleted]         v1.0.0` | The tag on the remote repository has been deleted; `-` indicates a deletion operation |
| Note | The local tag still exists after this command; delete it separately with `git tag -d v1.0.0` |

## Ignoring Files with .gitignore

`.gitignore` tells Git not to track certain files.

Common files and directories to ignore:

- Dependency directories, such as `node_modules/`
- Build outputs, such as `dist/` and `build/`
- Log files, such as `*.log`
- Local environment files, such as `.env`
- IDE configuration, depending on team agreement
- System files, such as `.DS_Store`

Example:

```gitignore
# Dependencies
node_modules/
vendor/

# Build outputs
dist/
build/
coverage/

# Logs
*.log
npm-debug.log*

# Environment files
.env
.env.local

# macOS
.DS_Store

# Editor
.vscode/
.idea/
```

### Already Tracked Files Are Not Automatically Ignored

If a file was already committed, adding it to `.gitignore` will not automatically stop tracking it. Use:

```bash
git rm --cached .env
git commit -m "Stop tracking env file"
```

Example output of `git rm --cached .env`:

```text
rm '.env'
```

Line-by-line breakdown:

| Output | Meaning |
| --- | --- |
| `rm '.env'` | Git removed the file from the staging area and stopped tracking it; the local file is preserved, it is simply no longer managed by Git |
| Next step | You still need to run `git commit -m "Stop tracking env file"` to record this change in history |

This keeps the local file but removes it from Git tracking.

## Common Advanced Commands

### cherry-pick

Apply one specific commit to the current branch.

```bash
git cherry-pick a1b2c3d
```

Example output:

```text
[main b2c3d4e] Add login form
 Date: Mon Jan 15 10:23:45 2024 +0800
 1 file changed, 20 insertions(+)
 create mode 100644 src/login.js
```

Line-by-line breakdown:

| Output | Meaning |
| --- | --- |
| `[main b2c3d4e]` | A new commit was created on the current branch (main) with hash `b2c3d4e`; note that this hash differs from the original commit |
| `Add login form` | The original commit message is reused |
| `Date:` | The original commit's authored date (not the time of the cherry-pick) |
| `1 file changed, 20 insertions(+)` | Change summary for the cherry-picked commit |

Useful when:

- You only want one bug fix
- You need to copy a patch from a release branch

### bisect

Use `bisect` to find which commit introduced a bug.

Start:

```bash
git bisect start
git bisect bad
git bisect good v1.0.0
```

Example output:

```text
Bisecting: 6 revisions left to test after this (roughly 3 steps)
[d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0u1v2] Add payment service
```

Line-by-line breakdown:

| Output | Meaning |
| --- | --- |
| `Bisecting: 6 revisions left to test after this` | There are 6 commits between the known good and bad versions still to be tested |
| `roughly 3 steps` | Binary search needs approximately 3 more steps to pinpoint the first bad commit |
| `[d4e5f6g7...]` | The commit hash Git automatically checked out — the midpoint of the range |
| `Add payment service` | The commit message, which helps you decide whether to run tests at this point |

After running tests, tell Git:

```bash
git bisect good
```

or:

```bash
git bisect bad
```

Git will keep narrowing the range, printing a similar message each time, until it finds the first bad commit:

```text
d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0u1v2 is the first bad commit
commit d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0u1v2
Author: Bob <bob@example.com>
Date:   Fri Feb 2 11:30:00 2024 +0800

    Add payment service
```

Finish:

```bash
git bisect reset
```

### blame

Show who last changed each line in a file:

```bash
git blame src/app.js
```

Example output:

```text
a1b2c3d4 (Alice   2024-01-15 10:23:45 +0800  1) import { validateUser } from './auth';
b2c3d4e5 (Bob     2024-01-20 14:30:12 +0800  2) 
a1b2c3d4 (Alice   2024-01-15 10:23:45 +0800  3) export function login(user) {
d4e5f6g7 (Charlie 2024-02-01 09:15:33 +0800  4)   if (!validateUser(user)) {
a1b2c3d4 (Alice   2024-01-15 10:23:45 +0800  5)     return null;
d4e5f6g7 (Charlie 2024-02-01 09:15:33 +0800  6)   }
a1b2c3d4 (Alice   2024-01-15 10:23:45 +0800  7) }
```

Each line follows this format:

```text
[hash] ([author]  [date]  [timezone]  [line]) [code]
```

Using the first line as an example:

```text
a1b2c3d4 (Alice   2024-01-15 10:23:45 +0800  1) import { validateUser } from './auth';
```

| Field | Example value | Meaning |
| --- | --- | --- |
| `a1b2c3d4` | Short hash | The commit that last changed this line |
| `Alice` | Author name | Who made that commit |
| `2024-01-15 10:23:45` | Timestamp | When this line was last changed |
| `+0800` | UTC offset | The timezone of the commit; `+0800` is China Standard Time |
| `1` | Line number | Where this line sits in the file |
| `import { validateUser }...` | Code content | The actual content of the line |

When the same hash appears on consecutive lines, those lines were all written in the same commit. Use `git blame` to find out who introduced a piece of code, then run `git show <hash>` on that commit to see the full context of why it was written.

Show a line range:

```bash
git blame -L 10,30 src/app.js
```

### clean

Delete untracked files.

Preview first:

```bash
git clean -n
```

Example output:

```text
Would remove build/app.js
Would remove build/app.css
Would remove temp.log
```

Line-by-line breakdown:

| Output | Meaning |
| --- | --- |
| `Would remove build/app.js` | This file would be deleted when you run `git clean -f`; the `-n` flag only previews, it does not delete anything |
| Each `Would remove ...` line | One untracked file or directory that would be cleaned (files ignored by `.gitignore` are not shown unless you add `-x`) |

Delete untracked files:

```bash
git clean -f
```

Delete untracked files and directories:

```bash
git clean -fd
```

This is destructive. Preview with `-n` first.

### worktree

`git worktree` lets one repository check out multiple branches into different directories.

```bash
git worktree add ../my-project-hotfix hotfix/payment
```

List worktrees:

```bash
git worktree list
```

Example output:

```text
/Users/alice/my-project          a1b2c3d [main]
/Users/alice/my-project-hotfix   d4e5f6g [hotfix/payment]
```

Line-by-line breakdown:

| Output | Meaning |
| --- | --- |
| First column (path) | The absolute path of each worktree on disk |
| Second column (short hash) | The commit hash that HEAD points to in that worktree |
| Third column (in brackets) | The branch checked out in that worktree; shows a hash instead if in detached HEAD state |
| First row | The main worktree (the original repository directory) |
| Remaining rows | Additional worktrees created with `git worktree add` |

Remove a worktree:

```bash
git worktree remove ../my-project-hotfix
```

### submodule

Submodules let one repository reference another repository.

Add:

```bash
git submodule add git@github.com:user/library.git external/library
```

Clone a repository with submodules:

```bash
git clone --recurse-submodules git@github.com:user/repo.git
```

Initialize and update:

```bash
git submodule update --init --recursive
```

Submodules add maintenance complexity. Use them only when the team really needs them.

### Git LFS

Git LFS manages large files such as design files, model files, and videos.

Install:

```bash
git lfs install
```

Track a file type:

```bash
git lfs track "*.psd"
git add .gitattributes
git commit -m "Track PSD files with Git LFS"
```

## Git Config, Aliases, and SSH

### Show Config Sources

```bash
git config --list --show-origin
```

Configuration levels:

- `--system`: system-level
- `--global`: user-level
- `--local`: current repository-level

### Common Aliases

```bash
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.lg "log --oneline --graph --decorate --all"
```

Usage:

```bash
git st
git lg
```

### Generate an SSH Key

```bash
ssh-keygen -t ed25519 -C "your-email@example.com"
```

Start ssh-agent:

```bash
eval "$(ssh-agent -s)"
```

Add the private key:

```bash
ssh-add ~/.ssh/id_ed25519
```

Copy the public key:

macOS:

```bash
pbcopy < ~/.ssh/id_ed25519.pub
```

Linux:

```bash
xclip -selection clipboard < ~/.ssh/id_ed25519.pub
```

Windows PowerShell:

```powershell
Get-Content ~/.ssh/id_ed25519.pub | Set-Clipboard
```

Add the public key to the SSH Keys page in GitHub or GitLab.

Test:

```bash
ssh -T git@github.com
```

## VS Code and GUI Tools

### Common VS Code Git Operations

The Source Control panel in VS Code can:

- Show modified files
- Show diffs
- Stage files
- Commit
- Switch branches
- Resolve conflicts
- Push and pull

Command Palette:

```text
Cmd/Ctrl + Shift + P
```

Common commands:

```text
Git: Clone
Git: Checkout to...
Git: Create Branch
Git: Commit
Git: Pull
Git: Push
```

### GUI Tools

Common GUI tools:

- GitHub Desktop
- Sourcetree
- Fork
- GitKraken
- Tower

GUI tools are helpful for browsing history, resolving conflicts, and reviewing diffs. Still, it is worth learning the core command-line workflow because it is universal and easier to use in automation and troubleshooting.

## Best Practices

### Keep Commits Small and Clear

Each commit should represent one logical change.

Recommended:

```text
Add checkout form validation
```

Avoid mixing unrelated changes:

```text
Update checkout, refactor navbar, change README, fix tests
```

### Review Before Committing

```bash
git status
git diff
git diff --staged
```

Make sure you are committing only the intended changes.

### Do Not Commit Secrets

Do not commit:

- Passwords
- API tokens
- `.env`
- Private keys
- Database backups

If a secret is committed, deleting it later does not fully remove it from history. Rotate the secret immediately and clean the repository history if needed.

### Use Clear Branch Names

Examples:

```text
feature/login-page
fix/checkout-timeout
docs/git-guide
refactor/payment-service
hotfix/production-login
```

### Sync with the Main Branch Regularly

Long-running branches are more likely to produce conflicts.

```bash
git fetch origin
git rebase origin/main
```

or:

```bash
git merge origin/main
```

Whether to use rebase or merge should follow your team convention.

### Be Careful with Force Push on Public Branches

Do not casually run this on `main`, `develop`, or shared branches:

```bash
git push --force
```

If you must force push your own feature branch, prefer:

```bash
git push --force-with-lease
```

### Make Pull Requests Easy to Review

A good Pull Request:

- Has a clear scope
- Includes a useful description
- Has readable commit history
- Includes testing notes
- Includes screenshots for UI changes
- Does not mix unrelated formatting or refactoring

## Troubleshooting

### Problem: fatal: not a git repository

Cause: The current directory is not a Git repository, or you are not inside a repository subdirectory.

Check:

```bash
pwd
ls -la
git status
```

Confirm whether a `.git` directory exists. If needed, enter the correct directory or initialize one:

```bash
git init
```

### Problem: Your branch is ahead of origin/main

Meaning: You have local commits that have not been pushed.

Solution:

```bash
git push
```

### Problem: Your branch is behind origin/main

Meaning: The remote branch has commits that you do not have locally.

Solution:

```bash
git pull
```

or:

```bash
git pull --rebase
```

### Problem: Updates were rejected

Common cause: The remote branch has commits missing from your local branch.

Solution:

```bash
git pull --rebase
git push
```

If this happened because you rebased your own feature branch:

```bash
git push --force-with-lease
```

### Problem: merge conflict

Resolve:

```bash
git status
```

Open the conflicted files, remove conflict markers, keep the correct content, then:

```bash
git add conflicted-file
git commit
```

If you are in a rebase:

```bash
git add conflicted-file
git rebase --continue
```

### Problem: You Forgot to Create a Branch and Changed main

If you have not committed yet:

```bash
git switch -c feature/my-work
```

Your working tree changes move with you to the new branch.

If you already committed on `main`:

```bash
git branch feature/my-work
git reset --hard origin/main
git switch feature/my-work
```

Before using `reset --hard`, confirm that `origin/main` is the state you want to return to and that there are no uncommitted changes you need.

### Problem: You Committed to the Wrong Branch

Assume the wrong commit is the latest commit on the current branch:

```bash
git switch correct-branch
git cherry-pick wrong-branch
git switch wrong-branch
git reset --hard HEAD~1
```

If the commit was already pushed, choose `revert` or `push --force-with-lease` based on team rules.

### Problem: You Deleted a Branch by Mistake

Check the reflog:

```bash
git reflog
```

After finding the commit hash:

```bash
git switch -c recovered-branch a1b2c3d
```

## Practice Exercises

### Exercise 1: Create a Local Repository and Commit

```bash
mkdir git-practice
cd git-practice
git init
echo "# Git Practice" > README.md
git status
git add README.md
git commit -m "Initial commit"
git log --oneline
```

Goals:

- Understand `git init`
- Understand working tree, staging area, and commits
- Learn to view history

### Exercise 2: Modify a File and View Diffs

```bash
echo "This is my first Git practice project." >> README.md
git diff
git add README.md
git diff --staged
git commit -m "Update README description"
```

Goals:

- Understand `git diff`
- Understand `git diff --staged`
- Review changes before committing

### Exercise 3: Create a Feature Branch

```bash
git switch -c feature/add-notes
echo "My notes" > notes.md
git add notes.md
git commit -m "Add notes file"
git switch main
git merge feature/add-notes
git branch -d feature/add-notes
```

Goals:

- Understand branch-based development
- Understand merging
- Learn to delete a merged branch

### Exercise 4: Create and Resolve a Conflict

Create branch A:

```bash
git switch -c feature/title-a
echo "# Title A" > README.md
git add README.md
git commit -m "Change title to A"
```

Return to main and create another change:

```bash
git switch main
echo "# Title B" > README.md
git add README.md
git commit -m "Change title to B"
```

Merge and trigger a conflict:

```bash
git merge feature/title-a
```

Open `README.md`, resolve the conflict, then:

```bash
git add README.md
git commit
```

Goals:

- Recognize conflict markers
- Resolve a conflict manually
- Complete a merge commit

### Exercise 5: Use Stash

```bash
echo "Temporary work" > temp.txt
git status
git stash -u
git status
git stash list
git stash pop
```

Goals:

- Temporarily save unfinished work
- Restore a stash

### Exercise 6: Sync a Feature Branch with Rebase

```bash
git switch -c feature/rebase-demo
echo "Feature work" > feature.txt
git add feature.txt
git commit -m "Add feature work"
git switch main
echo "Main work" > main.txt
git add main.txt
git commit -m "Add main work"
git switch feature/rebase-demo
git rebase main
git log --oneline --graph --decorate --all
```

Goals:

- Understand that rebase creates a more linear history
- Understand that rebase creates new commit hashes

### Exercise 7: Recover a Lost Commit

```bash
echo "Important work" > important.txt
git add important.txt
git commit -m "Add important work"
git reset --hard HEAD~1
git reflog
```

Find the hash for `Add important work`:

```bash
git switch -c recover-important <commit-hash>
```

Goals:

- Understand `reflog`
- Learn how to recover a lost commit

## Suggested Learning Path

Stage 1: Learn the core commands.

```text
git status
git add
git commit
git log
git diff
```

Stage 2: Learn branch collaboration.

```text
git branch
git switch
git merge
git pull
git push
```

Stage 3: Learn undo and conflict handling.

```text
git restore
git reset
git revert
git stash
git merge --abort
```

Stage 4: Learn team workflow.

```text
Pull Request
Code Review
Rebase
Squash
Release Tag
```

Stage 5: Learn advanced tools as needed.

```text
git cherry-pick
git bisect
git blame
git worktree
git submodule
git lfs
```

## Summary

The key to learning Git is not memorizing every command. The important parts are understanding:

- How files move through the working tree, staging area, and local repository
- How branches point to commits
- How merge and rebase change the shape of history
- When to use reset, restore, and revert
- How to collaborate without damaging public history

In daily development, you only need a moderate set of commands. First get comfortable with `status`, `add`, `commit`, `diff`, `log`, `switch`, `merge`, `pull`, and `push`. Then gradually add `rebase`, `stash`, `reflog`, and other advanced commands.
