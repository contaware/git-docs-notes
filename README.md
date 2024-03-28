# Git Docs & Notes <!-- omit from toc -->

This document is a reference guide for the common Git commands run from a terminal. GitHub is taken as an example of remote repository.

## Table of contents <!-- omit from toc -->

- [Introduction](#introduction)
  - [Code organization](#code-organization)
  - [Git repositories](#git-repositories)
  - [Git architecture](#git-architecture)
- [Install](#install)
  - [Linux](#linux)
  - [macOS](#macos)
  - [Windows](#windows)
- [Config](#config)
- [Use](#use)
  - [Init a repository](#init-a-repository)
  - [Status](#status)
  - [Delete a repository](#delete-a-repository)
  - [Add files](#add-files)
  - [Ignore files](#ignore-files)
  - [Commit](#commit)
  - [Log](#log)
  - [Diff](#diff)
  - [Rename files/directories](#rename-filesdirectories)
  - [Remove files](#remove-files)
  - [Restore files](#restore-files)
  - [Branch](#branch)
  - [Tags](#tags)
  - [Remote repository connections](#remote-repository-connections)
  - [Clone](#clone)
  - [Push](#push)
  - [Pull](#pull)
- [Advanced Use](#advanced-use)
  - [Fetch](#fetch)
  - [Merge](#merge)
  - [Stash](#stash)
  - [Switch to a commit/tag](#switch-to-a-committag)
  - [Revert a commit](#revert-a-commit)
- [Use with care](#use-with-care)
  - [Amend](#amend)
  - [Rebase](#rebase)
  - [Reset](#reset)
- [Work with GitHub](#work-with-github)
  - [Repository names](#repository-names)
  - [Pull-request (PR)](#pull-request-pr)
  - [Update a fork](#update-a-fork)


## Introduction

Version control systems (VCS) are used to log your changes while you progress in your coding.

### Code organization

1. **Main branch** is where your current stable production code is located. It only includes tested and unbroken code. It acts as a base from where all changes are made.

2. **Other branches** are where you develop new code and features. A branch separates from the main branch at some point in time. Later on, when your branch is ready for production, it can be merged back into the main branch.

3. **Tags** mark releases of your code.

### Git repositories

A repository contains all the files of a project including the history of changes. Unlike fully centralized versioning systems, Git utilizes a central repository and a series of local repositories. Those local repositories are an exact copy of the central repository with the complete history of changes. The central repository is only needed to synchronize the repositories, in fact, it's possible to run a single local repository without a central repository.

### Git architecture

1. **Working Tree** is the location where your repository has been checked out. Some call it Working Directory but that can easily be confused with the Current Directory your are in with your terminal.
   
2. **Staging Area** is where you put the files to be committed without having to include every change from the **Working Tree**. Some call it Index because it is implemented as a binary index file inside the `.git` directory.

3. **Local Repository** is where your commits, branches, tags and log messages are stored. It is located inside the `.git` directory.

4. **Remote Repository** (or Central Repository) is synchronized with your Local Repository. They usually communicate through a secure network connection.

![Git Overview](figures/git-architecture.svg)


## Install


To see whether Git is already installed, in your terminal run:

```
git version
```

### Linux

To install on Debian/Ubuntu:

```
sudo apt install git
```

### macOS

To install using [Homebrew](https://brew.sh/):

```
brew install git
```

Close and reopen your terminal so that it finds the new version.

### Windows

For Windows [download](https://git-scm.com/download/win) the installer and run it. When done make sure your installed Git is the first in list or the only one:

```
where git.exe
```

If it's no the first, then open the Environment Variables dialog, under System variables select the `Path` variable, press Edit... and Move Up the `C:\Program Files\Git\cmd` directory to the top. Now close and reopen your terminal to confirm the correct version.


## Config

To list the current configurations run:
```
git config --list
```

1. Set your credentials:
   ```
   git config --global user.name "username"
   git config --global user.email "email"
   ```

2. Line ending conversion (can also be set by Windows installer) should be set to `false` because today all text editors on all systems understand the different line-endings and honor them:
   
   ```
   git config --global core.autocrlf false
   ```

   If your really want to set it to something else:

   - `true`:  LF to CRLF when checking out text files, when committing text files CRLF -> LF.
   - `input`: No conversion on checkout, when committing text files CRLF -> LF.
   - `false`: No conversion.

3. By default the branch name for new repositories is `master` (can also 
   be set by Windows installer), but `main` is getting more popular:
   ```
   git config --global init.defaultBranch main
   ```

4. Set a default text editor other than vim (can also be set by Windows installer):
   
   - To use nano on Linux:

     ```
     git config --global core.editor "nano"
     ```
   
   - To use TextEdit on macOS:
   
     ```
     git config --global core.editor "open -e -W -n"
     ```
   
   - To use Notepad++ on Windows:
   
     ```
     git config --global core.editor "'C:/Program Files/Notepad++/notepad++.exe' -multiInst -notabbar -nosession -noPlugin"
     ```


## Use

### Init a repository

```
git init
```

After a `git init` both `git status` and `git branch --show-current` tell us that we are on `main` branch, but `git branch` returns nothing. That's so because a **branch is a pointer to a commit** and `git branch` only lists branches pointing to somewhere. The necessity of this empty current branch is obvious when we take the `git merge` command and remember that it requires a current branch to merge into.


### Status

- Show the status of the repository:

  ```
  git status
  ```

- To view ignored files:

  ```
  git status --ignored
  ```


### Delete a repository

Git keeps all of its files in the `.git` directory. Just delete that directory.


### Add files

In a Working Tree there are two kinds of files, the **tracked** files which Git knows and the **untracked** files that Git does not know.

- Add given file(s) to the Staging Area:

  ```
  git add <filename1> <filename2>
  ```

- Add files in current directory recursively (.gitignore is honored):

  ```
  git add .
  ```

  Attention: avoid `git add *` because .gitignore is not taken into account and files beginning with a dot are not added.

- Add all tracked files that have been modified or deleted:

  ```
  git add -u
  ```

- Interactive add by hunks (=area where the file changed):

  ```
  git add -p <filename>
  ```

Hint: Git tracks files, not directories. To track an empty directory (that contains no files) create an empty `.placeholder` file in it.


### Ignore files

Create a file named `.gitignore`, open it and one per line enter the file/directory names to ignore:

```
*.dat
results/
```

Note: if any of the ignored files were already being tracked, Git would continue to track them.


### Commit

- Commit staged files:

  ```
  git commit
  ```
  - Git launches the [configured](#config) editor to write your message. A good commit message has a subject line with the first letter capitalized and not ending with a period. If a description is needed, separate it from the subject with a blank line and wrap it at 72 characters.
  - To quickly add a message use the `-m "msg"` option.

- Stage and commit all tracked files that have been modified or deleted:

  ```
  git commit -a
  ```

After a commit has been created, it can be referenced by its hash value, and usually the first 4-6 hash characters are enough. In a local repository `HEAD` points to the currently checked-out branch or commit when in [detached HEAD state](#switch-to-a-committag). A branch always refers to its latest commit. Appending a tilde with a number means to go back by the given amount of generations, so `HEAD~1` refers to the 2nd most recent commit and `HEAD~2` is the 3rd most recent one.


### Log

- Show commits from `HEAD` following the parents; limit to `<filename>` if provided:

  ```
  git log [<filename>]
  ```

  - `--follow` shows the history across renames (single file only).
  - `-n` shows the last n commits.
  - `--oneline` displays in one-liners.
  - `--graph` displays a text-based graph in topological order.

- Show information about the given `<CommitID>`:

  ```
  git show [<CommitID>]
  ```

  - If `<CommitID>` is not provided it **defaults** to `HEAD`.

Hint: exit the pager by pressing the `q` key.


### Diff

If no `<filename>` is provided the command will show changes for all files. If a file is not staged, then the Staging Area contains the file's last commit.

- `<CommitID>` vs Working Tree:

  ```
  git diff [<CommitID>] [<filename>]
  ```

  - If `<CommitID>` is not provided it **defaults** to the **Staging Area**.

- `<CommitID>` vs Staging Area:

  ```
  git diff --staged [<CommitID>] [<filename>]
  ```

  - If `<CommitID>` is not provided it **defaults** to `HEAD`.

- `<CommitID1>` vs `<CommitID2>`:

  ```
  git diff <CommitID1>..<CommitID2> [<filename>]
  ```

Hint: exit the pager by pressing the `q` key.


### Rename files/directories

Stage for rename and rename in Working Tree (can be undo):

```
git mv <oldname> <newname>
```


### Remove files

- Stage for removal and delete from Working Tree (can be undo):

  ```
  git rm <filename>
  ```

- Remove files not under version control:

  ```
  git clean
  ```

  - `-f` force option is usually needed.
  - `-n` for a dry-run, it shows what would be removed.
  - `-x` removes also the ignored files.


### Restore files

- Revert Staging Area (unstage) by taking it from HEAD:

  ```
  git restore --staged <filename>
  ```

  There must be at least a commit, if not, then unstage with:

  ```
  git rm --cached <filename>
  ```

  (legacy command `git reset <filename>`)

- Revert Working Tree by taking it from Staging Area:
  
  ```
  git restore <filename>
  ```

  (legacy command `git checkout <filename>`)

- Revert Working Tree and Staging Area by taking it from given source:

  ```
  git restore -s <CommitID> --staged --worktree <filename>
  ```

  (legacy command `git checkout <CommitID> <filename>`)


### Branch

In addition to the local branches stored in your machine and the remote branches residing on a remote server, there is also the concept of a **remote-tracking branch**. This type of branches are locally stored references pointing to remote branches. They are named like `<remotename>/<remotebranchname>` and can be listed with `git branch -r`.

The explicit association between a remote branch and a local branch is not mandatory, but comfortable when synchronizing, it avoids us typing the full commands with all the arguments. With `git config --list` we can see those **upstream tracking configurations** for all the local branches, they are `branch.<branchname>.remote` and `branch.<branchname>.merge`.

- List:

  ```
  git branch
  ```

  - `-v` to be more verbose.
  - `-vv` really verbose, it shows upstream tracking configurations.
  - `-r` to see the remote-tracking branches.
  - `-a` to see all branches.

- Create:

  ```
  git branch <branchname> [<CommitID>]
  ```

  - If `<CommitID>` is not provided it **defaults** to `HEAD`.

- Create and switch to it (uncommitted changes are not touched):

  ```
  git switch -c <branchname>
  ```

  (legacy command `git checkout -b <branchname>`)

- Switch:
  
  ```
  git switch <branchname>
  ```

  (legacy command `git checkout <branchname>`)

- Delete local:

  ```
  git branch -d <branchname>
  ```

  Note: that will also remove the upstream tracking configurations for the given `<branchname>`.

- Delete remote:

  ```
  git push -d <remotename> <remotebranchname>
  ```

  Hint: `git fetch` with the `-p` prune option has to be called to remove the remote-tracking branch if you didn't execute the above command but instead deleted the remote branch from inside a remote interface such as GitHub.

- Rename:

  ```
  git branch -m <oldbranchname> <newbranchname>
  ```

- Set upstream tracking configurations:

  ```
  git branch -u <remotename>/<remotebranchname> <branchname>
  ```

  Note: that's done automatically by a `git clone` or when using `-u` with `git push`.

- Unset upstream tracking configurations:

  ```
  git branch --unset-upstream <branchname>
  ```


### Tags

Tags do reference specific commits. There are annotated tags which store more information, and lightweight tags meant as private local bookmarks.

- List:

  ```
  git tag
  ```

  - The `-n` option will also print annotation messages or commit messages for lightweight tags.

- Create/update:

  ```
  git tag <tagname> [<CommitID>]
  ```

  - If `<CommitID>` is not provided it **defaults** to `HEAD`.
  - With `-a -m "msg"` we get an **annotated tag** instead of a **lightweight tag**.
  - The `-f` option permits **updating** and existing tag.

- Delete:
  
  ```
  git tag -d <tagname>
  ```


### Remote repository connections

- Add:
  
  The following command adds the `<remotename>` alias for the given URL:

  ```
  git remote add <remotename> https://github.com/username/repo.git
  ```

  Note: usually it works also without providing the `.git` suffix.

- List:

  ```
  git remote -v
  ```

- Info:

  ```
  git remote show <remotename>
  ```

- Edit:
  
  ```
  git remote set-url <remotename> https://github.com/username/new.git
  ```

- Rename:
  
  ```
  git remote rename <remotename> <newremotename>
  ```

- Remove:
  
  ```
  git remote rm <remotename>
  ```


### Clone

The clone command copies the entire repository to a local directory which may already exist, but must be empty.

- To clone into a directory named like the remote repository (under your current directory):

  ```
  git clone https://github.com/username/repo.git
  ```

- To clone into a specific local directory:

  ```
  git clone https://github.com/username/repo.git local_dir
  ```

Note: the command also creates a remote connection named `origin` pointing back to the cloned repository.


### Push

A push is used to upload local repository content to a remote repository.

If someone else pushes its code and then you try to push as well, your push will rightly be rejected. You need to first do a pull and after that retry your push.

- Update the remote branch with the changes of your local branch:

  ```
  git push [<remotename> [<localbranchname>]]
  ```
  
  - The local branch is pushed to the remote branch having the same name.
  - If the local repository was not created by a `git clone` and the local branch has never been pushed, then with your first push provide the `-u` option to set the [upstream tracking configurations](#branch).
  - When omitting `<localbranchname>`, then the current branch is pushed to the remote branch with the same name, but only if the [upstream tracking configurations](#branch) have also the same two corresponding names set.
  - If `<remotename>` is not provided, then it pushes to the remote set by the [upstream tracking configurations](#branch).

- Tags have to be pushed explicitly:

  ```
  git push <remotename> <tagname>
  ```

  If you have many tags to push, then when pushing your commits, use one of the following two options. The `--follow-tags` option pushes all annotated tags and the `--tags` option pushes all tags (not recommended).


### Pull

- Download content from the given remote branch and update the current branch:

  ```
  git pull [<remotename> [<remotebranchname>]]
  ```

  - Default values for `<remotename>` and `<remotebranchname>` are read from the current branch's [upstream tracking configurations](#branch).

- A pull is a combination of two commands:

  ```
  git fetch <remotename> <remotebranchname>
  ```
  and
  ```
  git merge <remotename>/<remotebranchname>
  ```
  or with pull's `--rebase` option a rebase is performed instead of the merge:
  ```
  git rebase <remotename>/<remotebranchname>
  ```


## Advanced Use

### Fetch

Fetch remote branch (including tags) and update the remote-tracking branch:

```
git fetch [<remotename> [<remotebranchname>]]
```

- If `<remotebranchname>` is omitted, then all branches from `<remotename>` are fetched.
- If `<remotename>` is also not present, then it is read from the current branch's [upstream tracking configurations](#branch). When the [upstream tracking configurations](#branch) are missing, `origin` is fetched.


### Merge

> Git chooses one of these two types of merge:
>
> 1. When the current branch's tip is a direct ancestor of the branch you are merging in, Git does a **fast-forward merge** by just moving the HEAD and branch pointers of the current branch forward.
> 2. When the current branch's tip isn't a direct ancestor of the branch you are merging in, Git does a **three-way merge** using the snapshots of the two branch tips and the **common ancestor** of the two.

- Incorporate changes from the given branch into the current branch:

  ```
  git merge [<branchname>]
  ```

  - If `<branchname>` is not provided, then it is read from the current branch's [upstream tracking configurations](#branch).
  - This command will try to auto-merge, if it fails, it will annotate your text files with the **conflicts**. You have to review the annotated files, `git add` them and continue with `git merge --continue`.
  - With the `--squash` option the merge produces its results in the Working Tree and the Staging Area. You will then do a commit on top of the current branch. This commit will only have one parent as opposed to the two parents of a Git created merge commit.
 
- Interrupt a merge process and reconstruct the pre-merge state:

  ```
  git merge --abort
  ```

<br>

![Git Merge](figures/git-merge.svg)


### Stash

The stash command saves your current local changes and goes back to a clean state. If we switch to another branch, our stash will remain the same. This means that stashing can be used to carry changes through different branches.

The stash is organized as a stack. The items are accessed by `stash@{n}` with `n=0` being the latest added one. When `stash@{n}` is not provided it defaults to `stash@{0}`. When applying a stash item, Git tries to auto-merge and on conflicts it will prompt to solve.

- Stash Working Tree and Staging Area (all files are reverted to a clean state):

  ```
  git stash -m "msg"
  ```

- Stash Staging Area (only the files in the Staging Area are reverted to a clean state):

  ```
  git stash --staged -m "msg"
  ```

- List the stash entries that you currently have:

  ```
  git stash list
  ```

- Apply the given stash entry (stash entry is not removed):

  ```
  git stash apply [stash@{n}]
  ```

- Remove the given stash entry:

  ```
  git stash drop [stash@{n}]
  ```


### Switch to a commit/tag

- Normally HEAD is a pointer to a branch which by itself refers to its latest commit. When HEAD directly points to a commit, it gets detached from all branches:

  ```
  git switch -d <CommitID>
  ```

  (legacy command `git checkout <CommitID>`)

- To "re-attach" the HEAD, just switch back to a branch:

  ```
  git switch <branchname>
  ```

  (legacy command `git checkout <branchname>`)

- In the HEAD detached state, commits can be performed, but as soon as we switch away, the Git garbage collector will delete them after some time. To avoid that, create a branch and switch to it:

  ```
  git switch -c <branchname>
  ```

  (legacy command `git checkout -b <branchname>`)


### Revert a commit

- Create a new commit that reverts the changes introduced by the given commit:

  ```
  git revert <CommitID>
  ```

  - If there are merge conflicts, review the annotated files, `git add` them and continue with `git revert --continue`.
  - The `-m parent-number` option is necessary to revert merge commits, it specifies the side of the merge to reverse. The parents can be seen in the log entry of the merge commit and are indexed starting from 1. Most of the time you want to revert the branch you merged into which would be `-m 1`.

- Interrupt a revert process and reconstruct the pre-revert state:

  ```
  git revert --abort
  ```

> If after reverting a merge commit you want to perform other merges from the same branch, it may be necessary to cancel out the revert by doing a revert of the revert, [see document here](https://git-scm.com/docs/howto/revert-a-faulty-merge).  
> Hint: to avoid this, a team could choose to exclusively perform squash merges.


## Use with care

The commands in this section will **delete commits** and **replace commits**, so never use them on commits that already exist outside your local repository and that people may have based work on.


### Amend

Edit last commit:

```
git add <correctfile>
git rm <wrongfile>
git commit --amend
```

- The editor will open to let you change the commit message.
- It removes your last commit and replaces it with a new one having a different hash.


### Rebase

> A rebase command executes the following steps:
> 
> 1. Goes to the common ancestor of the current branch and the given commit.
> 2. Gets the diffs introduced by each commit of the current branch.
> 3. Saves those diffs to temporary files.
> 4. Resets the current branch to the given commit.
> 5. Finally applies each change in turn.

- Rebase the current branch on the given commit:

  ```
  git rebase [<CommitID>]
  ```

  - If `<CommitID>` is not provided, then it is read from the current branch's [upstream tracking configurations](#branch).
  - At each commit rewrite step, Git checks for merge conflicts. When there are conflicts, review the annotated files, `git add` them and continue with `git rebase --continue`.
  - The `-i` option opens a file where all commits are listed, with the leading command you decide the fate of each commit. To re-order the commits move them in the list, to change the commit messages use **reword**, **squash** is to meld into the previous listed commit and **drop** is self-explanatory.
  - By default a rebase will put the rebased commits into a single linear branch. With `--rebase-merges` the branching structure is preserved.

- Interrupt a rebase process and reconstruct the pre-rebase state:

  ```
  git rebase --abort
  ```

<br>

![Git Rebase](figures/git-rebase.svg)


### Reset

With the reset command we can drop commits or fix operations gone wrong like a rebase for example.

- Show the branch and HEAD modifications log to find the wanted commit:

  ```
  git reflog
  ```

  - The most recent operations are listed at the top.
  - The Git garbage collector keeps local commits not accessible by a branch, tag or HEAD for some time before deleting them definitively.
  
- Move both HEAD and the current branch pointer to the given commit:

  ```
  git reset [<CommitID>]
  ```

  - If `<CommitID>` is not provided it **defaults** to `HEAD`.
  - By default the Working Tree is untouched when resetting, files which differ will be marked as changed. The `--hard` option updates the Working Tree to match the state of the given commit (uncommitted changes are overwritten).


## Work with GitHub

### Repository names

- Better if your repository name is unique, that avoids confusion with other GitHub users.

- The GitHub repository names can only contain ASCII letters, digits and the characters:  
  `. - _`

- If you want your GitHub repositories to be easily readable and recognizable, use hyphens instead of underscores.

- Prefer all lowercase letters.


### Pull-request (PR)

1. Fork another's person repository:
   
   Login to your GitHub account, go to another's person repository and press the **Fork** button.

2. In your terminal clone the above forked repository:

   ```
   git clone https://github.com/username/repo.git
   cd repo
   ```

3. Create a new branch and switch to it:
   
   ```
   git switch -c <branchname>
   ```

4. Make changes and commit:

   ```
   git add <filename>
   git commit -m "msg"
   ```

5. Push changes to GitHub:

   ```
   git push -u origin <branchname>
   ```

6. On GitHub go to the forked repository and create the pull request.


### Update a fork

After a while the repository of the other person may have changed, before doing another PR, it's important to update your fork. By convention the connection alias of the original repository is named `upstream`. 

1. Add the original repository source (read-only for you):

   ```
   git remote add upstream https://github.com/otherusername/repo.git
   ```

2. Pull changes into `main`:

   ```
   git switch main
   git pull upstream main
   ```

3. Sync also your forked repository on GitHub:

   ```
   git push origin main
   ```
