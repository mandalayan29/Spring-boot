# Git and GitHub Tutorial
### A Simple Guide from Basics to Branches, Merge, Fork and Pull Request

---

## Table of Contents

1. [What is Version Control?](#1-what-is-version-control)
2. [History of Git](#2-history-of-git)
3. [Install and Set Up Git](#3-install-and-set-up-git)
4. [Create Your First Repository (`git init`)](#4-create-your-first-repository-git-init)
5. [Commit Your Work (`git add`, `git commit`, `git log`)](#5-commit-your-work)
6. [Skip the Staging Area (`git commit -a`)](#6-skip-the-staging-area)
7. [See Changes (`git diff`)](#7-see-changes-with-git-diff)
8. [Remove a File from Git](#8-remove-a-file-from-git)
9. [GitHub: Remote Repository](#9-github-remote-repository)
10. [Push New Files to GitHub](#10-push-new-files-to-github)
11. [Tags (Version Numbers)](#11-tags)
12. [Clone Someone Else's Project](#12-clone-a-project)
13. [Branches: Create and Switch](#13-branches-create-and-switch)
14. [Delete Branches and Useful Shortcuts](#14-delete-branches-and-shortcuts)
15. [Push a Branch to GitHub](#15-push-a-branch-to-github)
16. [How Branches Work Inside Git](#16-how-branches-work-inside-git)
17. [Merge](#17-merge)
18. [Rebase](#18-rebase)
19. [Merge Conflicts](#19-merge-conflicts)
20. [Time Travel](#20-time-travel)
21. [Stash](#21-stash)
22. [Fork](#22-fork)
23. [Pull Request](#23-pull-request)
24. [Command Cheat Sheet](#24-command-cheat-sheet)

---

## 1. What is Version Control?

**Git is a distributed version control system.**
To understand this sentence, we first learn what *version control* means.

### 1.1 The problem

When you work on any project (software, a book, a video script), you save many updates.

- If you save over the same file, the **old version is lost**.
- So many people make new copies for every update:

```
quizapp
quizapp1
quizapp2
quizapp-final
quizapp-final-final
quizapp-final-final-very-final
```

This is messy. We do this because we may want to **go back to an older version** if the new one has bugs.

### 1.2 Version control

**Version control** means:

- You can keep **many versions** of your project.
- You can **go back** to any version at any time.
- Many people can **work together** (collaborate) on the same project.

The software that does this is called a **Version Control System (VCS)**.

### 1.3 Three types of version control systems

**A. Local Version Control**

- All versions are saved **only on your own computer**.
- Problem 1: You cannot easily work with other people.
- Problem 2: If your computer breaks, you lose everything.

**B. Centralized Version Control (CVCS)**

- There is **one central server**. Everyone gets files from it and saves changes to it.
- Good: Everyone can see what others are doing.
- Problem: If the central server fails, you lose the history. You only keep the latest copy on your machine.

```
   Developer 1 ----\
   Developer 2 -----+----> [ CENTRAL SERVER ]  (single point of failure)
   Developer 3 ----/
```

**C. Distributed Version Control (DVCS) — this is Git**

- Every developer has a **full copy of the project and its whole history** on their own machine.
- You can also use a **remote repository** (GitHub, GitLab, Bitbucket) to share work.
- You can work **without internet** (for example, on a long flight).
- If the server fails, any developer's copy can restore it.

```
   [Dev 1: full history] <----> [ REMOTE (GitHub) ] <----> [Dev 2: full history]
```

> **Word to know:** In Git, "saving" your work as a version is called a **commit**.

---

## 2. History of Git

- In **open source** projects, anyone in the world can contribute. This is different from a company project where you only trust your own team.
- The **Linux kernel** (created by Linus Torvalds) is a famous open source project.
- From **1991 to 2002**, people sent changes to Linux by **patches and archive files** (a manual process).
- In **2002**, Linux started using a tool called **BitKeeper**, which made sharing code easier.
- Later, BitKeeper changed its policy and started charging money. The Linux community wanted a free tool.
- So in **2005**, **Git** was created.

**Why people like Git**

- Simple to use
- Very fast
- Strong **branching** support
- Fully **distributed**

> Git is not only for code. It works for any text files, like books, essays, or scripts.

---

## 3. Install and Set Up Git

### 3.1 Check if Git is already installed

Open a terminal (Command Prompt, PowerShell, or Mac Terminal) and run:

```bash
git --version
```

- If you see a version number → Git is installed.
- If you see "git is not recognized" → you need to install it.

On a **Mac**, Git comes with Xcode. On **Windows** you must install it. On **Linux** use your package manager.

### 3.2 Install on Windows

1. Search "git download" and open the official website (git-scm.com).
2. Choose **Windows**, then choose the **64-bit standalone installer**.
3. Run the installer. Most options can stay as **default**. Some useful choices:
   - **Default editor:** choose an editor you know (the video used Notepad instead of Vim, which is harder for beginners).
   - **Default branch name:** choose "Let Git decide" (we will set `main` ourselves later).
   - Use the **command line** (not Git Bash) for this guide.
4. Click **Install**, then **Finish**.
5. **Close and reopen** your terminal. Run `git --version` again. You should now see the version.

### 3.3 Tell Git who you are

Git needs your **name** and **email** to record who made each commit. Use the same email as your GitHub account.

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

- `git config` → change or view settings.
- `--global` → apply to all projects on this computer.

Check your settings:

```bash
git config --global --list
```

---

## 4. Create Your First Repository (`git init`)

### 4.1 Working directory

Make a new folder for your project (the video used `FirstProject`) and open it in your editor (VS Code) and terminal. This folder is your **working directory**.

At this moment Git knows nothing about it. If you run:

```bash
git status
```

You will see: `fatal: not a git repository`.

### 4.2 The three areas of Git

```
+-------------------+   git add   +----------------+  git commit  +------------------+
| WORKING DIRECTORY | ----------> |  STAGING AREA  | -----------> |  COMMIT HISTORY  |
| (your files)      |             | (files Git     |              | (saved versions) |
|                   |             |  should track) |              |                  |
+-------------------+             +----------------+              +------------------+
                                  \_____________ inside the .git folder ____________/
```

| Area | Meaning |
|---|---|
| **Working directory** | Your project folder where you edit files. Git does not track these changes by itself. |
| **Staging area** | A waiting place. You choose which files Git should take care of in the next commit. |
| **Commit history** | The saved versions (commits). |

> Do not confuse the **working directory** with the **Git repository**. The repository is the hidden `.git` folder **inside** your project.

### 4.3 Create the repository

```bash
git init
```

This creates an empty **local repository** (a hidden `.git` folder). The staging area and commit history live inside it.

### 4.4 Use `main` instead of `master`

Older Git versions create a branch called `master` by default. We want `main`:

```bash
git init -b main
```

- `-b` means **branch name**.

> If you already ran plain `git init` and got `master`, you can simply rename it: `git branch -M main`.
> (The video deleted the `.git` folder and started again, but renaming is easier.)

Now check:

```bash
git status
```

Output: `On branch main` and `No commits yet`.

`git status` is the command you will use **all the time**. It tells you what is happening in your project.

---

## 5. Commit Your Work

### 5.1 Create a file

Create a file called `MyFirstCode.txt` and write `Hello World` in it. (Git does not care about the file type — `.txt`, `.java`, `.py` all work the same.)

Run `git status`:

```
Untracked files:
    MyFirstCode.txt
```

**Untracked** means the file is in your working directory, but Git is not tracking it yet.

### 5.2 Add the file to the staging area

```bash
git add MyFirstCode.txt
```

Now `git status` shows it under **Changes to be committed** (it is staged).

To remove a file from staging (undo `git add`) you can use:

```bash
git rm --cached MyFirstCode.txt
```

### 5.3 Commit

```bash
git commit -m "My first commit"
```

- `-m` is for the **message**. Every commit needs a message that explains the change.
- If you forget `-m`, Git opens an editor asking for a message. If you leave it empty, the commit is cancelled.

**Tips for messages**

- Write clear, meaningful messages (what feature you added, which bug you fixed).
- Many teams also add a **ticket or issue number** in the message.
- A common saying: **"Commit early and commit often."**

After the commit, `git status` says: `nothing to commit, working tree clean`.

### 5.4 See the history with `git log`

```bash
git log
```

For each commit you will see:

- **Commit ID** (a long code)
- **Author** (name and email)
- **Date**
- **Message**
- **HEAD -> main** (shows where you are now)

### 5.5 What is the long commit code? (Checksum)

- Git gives every commit a **checksum** (like a fingerprint) using **SHA-1**.
- It has **40 characters**. Git often shows only the first **7**.
- If anything in the commit changes, the checksum changes. So Git always knows if data was changed. This gives Git **integrity**.

### 5.6 What is HEAD?

**HEAD** is a **pointer**. It shows which branch/commit you are on right now.

---

## 6. Skip the Staging Area

Normal flow: edit → `git add` → `git commit`.

If you **modify** a file that Git already tracks, `git status` shows:

```
Changes not staged for commit:
    modified: MyFirstCode.txt
```

> Always **save** the file in your editor first. If you do not save, Git cannot see your change.

If you try `git commit -m "..."` now, it will fail, because the file is not staged.

**Shortcut:** use `-a` to stage and commit in one step:

```bash
git commit -a -m "Added exclamation mark"
```

Important: `-a` works **only for files Git already tracks**. A brand new file still needs `git add` first.

---

## 7. See Changes with `git diff`

`git status` tells you **which** file changed. `git diff` tells you **what exactly** changed (lines added or removed).

| Where is the change? | Command |
|---|---|
| In the working directory (not staged) | `git diff` |
| In the staging area (after `git add`) | `git diff --staged` |

Example:

```bash
git diff                  # see changes before staging
git add MyFirstCode.txt
git diff                  # shows nothing now (change is staged)
git diff --staged         # shows the staged changes
git commit -m "Story 3.1 - user input"
```

---

## 8. Remove a File from Git

### 8.1 The problem

Sometimes you commit a file that should **not** be in Git, for example a file with **usernames and passwords** (`creds.txt`). If you push it to GitHub, everyone can see it.

### 8.2 Add many files at once

```bash
git add .
```

The dot (`.`) means **all files** in the folder.

### 8.3 The wrong way

Deleting the file from your folder is **not enough**. Git still has it, and `git status` shows `deleted`.

### 8.4 The right way

First remove it from Git (but keep your local file), then delete the file if you want:

```bash
git rm --cached creds.txt
git commit -m "Remove the creds file"
```

- `--cached` means: remove from Git tracking/staging only, **not** from your folder.
- After that, the file is "untracked" and Git ignores it.

> **Extra tip:** To stop Git from ever tracking files like this, list their names in a file called `.gitignore`. Also, if you already pushed a secret, remember it stays in the old history — change that password.

### 8.5 README file

Every project should have a `README.md` file. It explains what the project is and how to use it. `.md` means **Markdown**, a simple way to format text.

---

## 9. GitHub: Remote Repository

### 9.1 Why do we need a remote repository?

A local repository is only on your machine. To **share** code with your team (or the world), you use a **remote repository** on a server.

| Service | Notes |
|---|---|
| **GitHub** | Most famous. Great for public profiles and open source. |
| **GitLab** | Popular in companies for private projects. |
| **Bitbucket** | By Atlassian. Used by some companies. |

This guide uses **GitHub**.

### 9.2 Get a copy of a project: `git clone`

On any GitHub project page click **Code**, copy the link (HTTPS or SSH), and run:

```bash
git clone <link>
```

This downloads the whole project and its history. (You can also click "Download ZIP", but that has no Git history.)

### 9.3 Create your own repository on GitHub

1. Sign up and log in to GitHub.
2. Click the **+** button → **New repository**.
3. Give it a **name** (example: `git-course`) and a description.
4. Choose **Public** (anyone can see) or **Private** (only people you invite).
5. Do not add README, .gitignore, or license for now.
6. Click **Create repository**.

You get a **unique link** for the repository.

### 9.4 HTTPS vs SSH

| Method | How it works |
|---|---|
| **HTTPS** | Asks for your login every time you push. |
| **SSH** | You set up a secret key **once** per computer. After that Git proves who you are automatically. |

### 9.5 Set up SSH (one time per computer)

1. Create a key:

   ```bash
   ssh-keygen -o
   ```

   Press Enter to accept the default file name. A passphrase is optional (like a password for the key).
   (A newer, recommended form is `ssh-keygen -t ed25519 -C "you@example.com"`.)

2. This makes two files in the hidden `.ssh` folder:
   - **Private key** (keep it secret, never share)
   - **Public key** (`id_*.pub`, safe to share)

3. Show the public key and copy it:

   ```bash
   cat ~/.ssh/id_rsa.pub
   ```

   (Use the file name that your computer created.)

4. On GitHub go to **Settings → SSH and GitHub keys → New SSH key**, give it a title, paste the key, and click **Add SSH key**.

Now every time you push, GitHub checks that your computer's key matches the public key you added.

### 9.6 Connect your local repository to GitHub and push

GitHub shows these steps on the new repository page. Here they are, one by one:

```bash
echo "# git-course demo" >> README.md     # create a README file
git init -b main                            # create local repository with branch main
git add README.md                           # stage the file
git commit -m "first commit"                # commit locally
git branch -M main                          # make sure branch name is main
git remote add origin git@github.com:YOUR-NAME/git-course.git   # connect to GitHub
git push -u origin main                     # send commits to GitHub
```

Explanation of the important parts:

- `echo "text" >> file` → writes text into a file. (`cat file` shows the content.)
- `git remote add origin <link>` → gives the remote repository the short name **origin**.
- `git push -u origin main` → pushes the `main` branch to `origin`. `-u` sets it as the default (upstream) so later you can type just `git push`.

Refresh the GitHub page. Your file is there.

> **Flow:**
> `Working directory → (add) → Staging → (commit) → Local repository → (push) → GitHub`

---

## 10. Push New Files to GitHub

After the first push, the daily routine is:

```bash
git status                              # see what changed
git add UserService.txt                 # stage the new file
git commit -m "user service created"    # commit locally
git push origin main                    # send to GitHub
```

- The commit happens **only on your machine**. It is **not** on GitHub until you `push`.
- On GitHub, click the **commits** link to see each commit. Click a commit to see which lines changed. You can browse the project as it was at each commit.

---

## 11. Tags

### 11.1 What is a remote and "origin"?

A `git push origin main` has four parts:

| Part | Meaning |
|---|---|
| `git` | the tool |
| `push` | send data to the remote |
| `origin` | the name of the remote repository |
| `main` | the branch to push |

See your remotes:

```bash
git remote -v
```

It shows the links used to **fetch** (download) and **push** (upload). `origin` is just a default name. You can have more than one remote and use different names.

### 11.2 What is a tag?

Software has **version numbers** (like 1.0 or 1.1). In Git, a **tag** is a label you put on a commit to mark a release. Use it when you think "this is ready to release".

### 11.3 Commands

```bash
git tag                                  # list all tags
git tag -a v1.0 -m "first release"       # create an annotated tag
git show v1.0                            # show details of a tag
git push origin v1.0                     # push one tag to GitHub
```

- **Annotated tag** (`-a`): stores extra information (who created it, date, message). Use this for real releases.
- **Lightweight tag**: just a name, no extra information.
- Tags are **not** pushed automatically with `git push`. You must push them yourself.
- To push all tags at once: `git push origin --tags`.

On GitHub, tags appear under **Releases/Tags**. You can download the source code for each tag.

Example flow:

```bash
git commit -m "processing user data"
git push origin main
git tag -a v1.1 -m "27th June release"
git push origin v1.1
```

---

## 12. Clone a Project

You can clone any public project, for example VS Code:

```bash
git clone <link-of-the-project>
```

Now you can explore it:

```bash
git log                       # all commits
git log --pretty=oneline      # one line per commit (short)
git tag                       # all release tags
git show 1.79.0               # details of one tag
```

Tip: reading other people's commit messages is a good way to learn how to write good ones.

### Can I push to someone else's project?

You can change the code on your computer and commit locally. But when you run `git push origin main`, GitHub says **permission denied**, because you are not a contributor of that project. You also cannot create branches there.

To contribute to a project you do not own, you use **Fork** and **Pull Request** (see sections 22 and 23).

---

## 13. Branches: Create and Switch

### 13.1 What is a branch?

A **branch** is a separate line of work. Your default branch is `main`. Everything you commit goes to the current branch.

**Why branches?**

- Your `main` branch has working, stable code.
- You want to try a new feature (an experiment). It may take weeks, and you may touch many files.
- If you do it on `main`, you may break working code.
- So you create a **new branch**, work there safely, and **merge** it back when it is ready.

```
main      o---o---o
               \
feature1        o---o       (experiment, main is not affected)
```

### 13.2 Create a branch

Two ways (both work):

| Old way | New way (Git 2.23+) |
|---|---|
| `git checkout -b feature1` | `git switch -c feature1` |

- `-b` (checkout) or `-c` (switch) means **create**.
- Without it, Git says the branch does not match any known name.
- `switch` is easier to understand, because it only switches branches.

### 13.3 See your branches

```bash
git branch
```

The branch with a `*` (and green color) is the **current branch**.

### 13.4 Work on a branch

```bash
git switch -c feature1          # create and move to feature1
# edit UserService.txt: add "create avatar for user"
git add UserService.txt
git commit -m "experimenting with user avatar"
```

This commit belongs only to `feature1`. `main` does not know about it.

### 13.5 Switch between branches

```bash
git switch main        # your new line disappears from the files
git switch feature1    # the line comes back
```

Your files change automatically to match the branch you are on.

---

## 14. Delete Branches and Shortcuts

```bash
git switch -c feature2        # create another branch
git branch                    # list local branches
git branch --all              # list local AND remote branches
git switch -                  # go back to the previous branch
git branch -d feature2        # delete a branch
```

| Command | Meaning |
|---|---|
| `git branch` | Local branches only |
| `git branch --all` | Local + remote branches |
| `git switch -` | Jump to the branch you were on before |
| `git branch -d <name>` (or `--delete`) | Delete a branch |

> You cannot delete the branch you are currently on. Switch to another branch first. Also, `-d` refuses to delete a branch that has unmerged work. (`-D` forces it.)

---

## 15. Push a Branch to GitHub

A new local branch is **not** on GitHub until you push it.

```bash
git switch feature1
# create adminService.txt and write some lines
git add adminService.txt
git commit -m "adding admin service"
git push origin feature1
```

Note: here we push **`feature1`**, not `main`.

On GitHub the repository now shows **2 branches**. Use the branch dropdown to switch between them and see different files.

---

## 16. How Branches Work Inside Git

A branch is **not** a copy of your project. It is only a **small pointer** to a commit.

- Every commit is a **snapshot** of the changed files. It has a checksum and a link to its **parent** commit.
- A branch name (like `main`) points to the latest commit of that line.
- **HEAD** points to the branch you are on.

```
Before branching:

   C1 <- C2 <- C3
                ^
              main  <- HEAD
```

```
After creating feature1 (both point to the same commit):

   C1 <- C2 <- C3
                ^
        main, feature1
```

```
After committing on feature1:

   C1 <- C2 <- C3 <- C4
                ^     ^
              main   feature1
```

Because a branch is only a pointer, creating branches is **fast and cheap**.

### See the history as a graph

```bash
git log --graph
```

You can also install the **Git Graph** extension in VS Code for a visual view. It shows each commit and its **parent(s)**.

---

## 17. Merge

**Merge** brings the work of one branch into another.

### 17.1 Steps

Goal: bring `feature1` into `main`.

1. Move to the branch that will **receive** the changes:

   ```bash
   git switch main
   ```

2. Merge the other branch:

   ```bash
   git merge feature1
   ```

3. Check `git log` or the graph. The feature commits are now in `main`, and `adminService.txt` is in `main`.

### 17.2 Pull before you push

When working with GitHub, **first get the latest changes**:

```bash
git pull origin main
```

Then merge, then push:

```bash
git push origin main
```

This is very important in teams: other people may have pushed changes while you worked.

> `git pull` = download the new changes from the remote **and** merge them into your branch.

---

## 18. Rebase

There are two ways to combine branches: **merge** and **rebase**. They give the same final files, but a different **history**.

### 18.1 Merge history

When both branches have new commits, merge creates a **merge commit**, and the history shows a split and a join:

```
main      o---o---o-------M      (M = merge commit)
               \         /
feature         o---o---o
```

### 18.2 Rebase history

Rebase takes your commits and **replays them on top of the other branch**, so the history is **one straight line**:

```
main      o---o---o---o'---o'
```

### 18.3 Commands

```bash
git switch -c feature3
# change adminService.txt, add, commit

git switch main
# change userService.txt, add, commit

git rebase feature3        # run this while on main
```

Check with `git log --graph`: there is **no extra branch line**.

### 18.4 Which one to use?

| | Merge | Rebase |
|---|---|---|
| History | Shows exactly what happened and in which branch | Clean, single line |
| Best for | When you want to keep the full story | When you want a tidy history |

> **Safety rule (extra tip):** Rebase **rewrites** commits. Do not rebase commits that you have already pushed and others are using.

---

## 19. Merge Conflicts

### 19.1 What is a merge conflict?

A conflict happens when **two branches change the same line of the same file** and Git cannot decide which version to keep.

- Different files → no problem.
- Same file, different lines → usually no problem.
- Same file, **same line** → **conflict**.

### 19.2 Create a conflict (example)

On `main`, edit a line of `adminService.txt` to `change 2 in my database`, then commit.
On `feature3`, change the **same line** to `change 2 in harsh's database`, then commit.

```bash
git switch main
git merge feature3
```

Git says:

```
CONFLICT (content): Merge conflict in adminService.txt
Automatic merge failed; fix conflicts and then commit the result.
```

### 19.3 How the file looks

Git writes **markers** in the file:

```
<<<<<<< HEAD
change 2 in my database
=======
change 2 in harsh's database
>>>>>>> feature3
```

| Marker | Meaning |
|---|---|
| `<<<<<<< HEAD` ... `=======` | Your **current** branch version |
| `=======` ... `>>>>>>> feature3` | The **incoming** branch version |

### 19.4 Fix it

1. Open the file.
2. Decide which version to keep (or combine them). In real projects, **talk to the teammate** who made the other change.
3. **Delete the marker lines** (`<<<<<<<`, `=======`, `>>>>>>>`) and the version you do not want.
4. Save, then:

   ```bash
   git add .
   git commit -m "merged feature3"
   ```

`git status` is clean now. VS Code also shows buttons like "Accept Current / Accept Incoming" to help.

### 19.5 Conflicts can also happen with `git pull`

If your local `main` and the GitHub `main` both have different commits, `git pull origin main` shows a message that the branches have **diverged** and asks how to combine them:

```bash
git config pull.rebase false     # combine using merge
# git config pull.rebase true    # combine using rebase
```

Then run `git pull origin main` again. If the same line changed in both places, you get a conflict. Fix it the same way (edit, `git add .`, `git commit`).

---

## 20. Time Travel

You can go back to **any old commit** and even start new work from there.

Use case: the latest code is big, but you want to create a **Lite version** from an older stable commit.

### 20.1 Steps

1. Find the commit ID:

   ```bash
   git log
   ```

   Copy the commit ID (the first 7 characters are enough).

2. Go to that commit:

   ```bash
   git checkout <commit-id>
   ```

3. Git warns you about **detached HEAD**. This means HEAD is not on any branch. Any commit you make here may get lost, because no branch points to it.

4. Create a branch from this old commit to keep your work safe:

   ```bash
   git switch -c lite-version
   ```

5. Make your changes (for example, remove some features), then:

   ```bash
   git add .
   git commit -m "lite version is ready"
   git push origin lite-version
   ```

Now `lite-version` is a new branch that started from the old commit. The other branches are not changed.

> To go back to normal, use `git switch main`.

---

## 21. Stash

### 21.1 The problem

You are in the middle of a big feature on `feature1`. It is not finished, so you do **not** want to commit. Suddenly there is an urgent bug on `main`.

If you try `git switch main`, Git blocks you:

```
error: Your local changes to the following files would be overwritten by checkout
```

You do not want to commit half-done work, and you do not want to delete it.

### 21.2 The solution: `git stash`

**Stash** saves your uncommitted changes in a safe place and cleans your working directory.

```bash
git stash                  # save unfinished work
git switch main            # now you can switch
# fix the bug
git add .
git commit -m "issue 234 fixed"

git switch feature1
git stash list             # see saved stashes
git stash apply            # bring back your saved work
```

| Command | Meaning |
|---|---|
| `git stash` | Save changes and clean the working directory |
| `git stash list` | Show all stashes |
| `git stash apply` | Put the saved changes back (the stash stays in the list) |

> **Extra tips:** `git stash pop` applies the stash **and removes** it from the list. `git stash drop` deletes a stash.

---

## 22. Fork

### 22.1 The problem

You find a project that belongs to **someone else** (for example VS Code by Microsoft). You want to change it.

- You can **clone** it and change it for your own use.
- But you **cannot push** to their repository, because you are not a contributor.

### 22.2 What is a fork?

A **fork** is **your own copy of someone else's repository on GitHub**, inside your account.

1. Open the project page on GitHub.
2. Click **Fork**.
3. Click **Create fork**.

Now the project is in your account. You can clone it, change it, and push to **your fork** freely.

```
   [ Original repo (owner's account) ]
              |  Fork
              v
   [ Your fork (your account) ] --clone--> [ Your computer ]
```

Also note: a project's **license** (for example MIT) decides what you are allowed to do with the code (for example, selling it).

---

## 23. Pull Request

A **pull request (PR)** is a request to the owner: *"I made changes in my copy. Please review them and merge them into your project."*

This is how people contribute to **open source** projects. (You can also use pull requests between branches inside your own project.)

### 23.1 Steps for the contributor

1. **Fork** the owner's repository.
2. **Clone** your fork:

   ```bash
   git clone git@github.com:YOUR-NAME/quiz-app-spring.git
   ```

3. Make your changes, then commit and push to your fork:

   ```bash
   git add .
   git commit -m "comment added"
   git push origin main
   ```

4. On GitHub, open the **Pull requests** tab → **New pull request**.
   - **Base repository** = the original project (where changes should go).
   - **Head repository** = your fork (where your changes are).
5. Check that there are **no conflicts**, write a short comment, and click **Create pull request**.

### 23.2 Steps for the owner

1. Open the **Pull requests** tab. The new request is there.
2. **Review the code.** Open **Files changed** and read each change. You should never accept code without a review, because anyone can send any code.
3. If it looks good, click **Merge pull request** and confirm.
4. The pull request is now **closed**, and the changes are in the original project.

### 23.3 Full open-source flow

```
Fork  ->  Clone  ->  Change  ->  Commit  ->  Push to your fork
                                                    |
                                                    v
                                            Create Pull Request
                                                    |
                                                    v
                                     Owner reviews -> Merge -> Done
```

---

## 24. Command Cheat Sheet

### Setup

| Command | What it does |
|---|---|
| `git --version` | Check Git version |
| `git config --global user.name "Name"` | Set your name |
| `git config --global user.email "mail"` | Set your email |
| `git config --global --list` | View settings |

### Basic work

| Command | What it does |
|---|---|
| `git init -b main` | Create a repository with branch `main` |
| `git status` | Show the current state |
| `git add <file>` / `git add .` | Stage one file / all files |
| `git commit -m "msg"` | Commit with a message |
| `git commit -a -m "msg"` | Stage tracked files and commit |
| `git log` | Show history |
| `git log --pretty=oneline` | Short history |
| `git log --graph` | History as a graph |
| `git diff` / `git diff --staged` | See changes (unstaged / staged) |
| `git rm --cached <file>` | Stop tracking a file (keep it on disk) |

### Remote (GitHub)

| Command | What it does |
|---|---|
| `git clone <link>` | Download a repository |
| `git remote add origin <link>` | Connect to a remote |
| `git remote -v` | Show remotes |
| `git push -u origin main` | First push of `main` |
| `git push origin <branch>` | Push a branch |
| `git pull origin main` | Download and merge remote changes |

### Tags

| Command | What it does |
|---|---|
| `git tag` | List tags |
| `git tag -a v1.0 -m "msg"` | Create annotated tag |
| `git show v1.0` | Show tag details |
| `git push origin v1.0` | Push a tag |

### Branches

| Command | What it does |
|---|---|
| `git branch` / `git branch --all` | List local / all branches |
| `git switch -c <name>` or `git checkout -b <name>` | Create and switch |
| `git switch <name>` | Switch branch |
| `git switch -` | Go back to the previous branch |
| `git branch -d <name>` | Delete a branch |
| `git merge <branch>` | Merge a branch into the current one |
| `git rebase <branch>` | Replay current branch on top of another |

### Extra tools

| Command | What it does |
|---|---|
| `git checkout <commit-id>` | Go to an old commit (detached HEAD) |
| `git stash` | Save unfinished work |
| `git stash list` | List stashes |
| `git stash apply` | Restore saved work |

### Typical daily routine

```
git pull origin main       # get the latest code
git switch -c my-feature   # work in a new branch
# ... edit files ...
git add .
git commit -m "clear message"
git push origin my-feature
# open a Pull Request on GitHub, get it reviewed, merge
```