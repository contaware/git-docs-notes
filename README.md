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
  - [Switch to a commit/tag](#switch-to-a-committag)
  - [Remote repository connections](#remote-repository-connections)
  - [Clone](#clone)
  - [Push](#push)
  - [Fetch](#fetch)
  - [Merge](#merge)
  - [Pull](#pull)
- [Work with GitHub](#work-with-github)
  - [Repository names](#repository-names)
  - [Pull-request (PR)](#pull-request-pr)
  - [Update fork](#update-fork)


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

   - `true`:  LF to CRLF when checking out text files, when commiting text files CRLF -> LF
   - `input`: No conversion on checkout, when commiting text files CRLF -> LF
   - `false`: No conversion

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

- Add given file(s) to the Staging Area:

  ```
  git add <filename1> <filename2>
  ```

- Add files in current directory recursively (.gitignore is honored):

  ```
  git add .
  ```

  Attention: avoid `git add *` because .gitignore is not taken into account and files beginning with a dot are not added.

- Add all files in the entire Working Tree:

  ```
  git add -A
  ```

- Add all tracked files that have been modified or deleted:

  ```
  git add -u
  ```


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
  git commit -m "msg"
  ```

- Stage and commit all tracked files that have been modified or deleted:

  ```
  git commit -a
  ```

- Edit last commit (commit message can also be changed):

  ```
  git add <correctfile>
  git rm <wrongfile>
  git commit --amend
  ```

  Attention: it works by removing your last commit and creating a new one. If you already pushed your work to a shared remote repository and others have built on your last commit, do not amend!

Note: without the `-m` option, Git launches the [configured](#config) editor to write/edit your message.

After a commit has been created, it can be referenced by its hash value, and usually the first 4-6 hash characters are enough. There is also `HEAD` that points to a branch which refers to its latest commit. Appending a tilde with a number means to go back by the given amount of generations, so `HEAD~1` refers to the 2nd most recent commit and `HEAD~2` is the 3rd most recent one.


### Log

- Show the history for the given `<filename>`, or for all files if not provided:

  ```
  git log <filename>
  ```

  - `-n` shows the last n commits
  - `--oneline` displays in one-liners

- Show information about the given `<CommitID>`:

  ```
  git show <CommitID>
  ```

Hint: exit the pager by pressing the `q` key.


### Diff

If no `<filename>` is provided the command will show changes for all files.

- Staging Area vs Working Tree:

  ```
  git diff <filename>
  ```

  Remember: if a file is not staged, then the Staging Area contains the file's last commit.

- `<CommitID>` vs Working Tree:

  ```
  git diff <CommitID> <filename>
  ```

- `<CommitID>` vs Staging Area:

  ```
  git diff --staged <CommitID> <filename>
  ```

- `<CommitID1>` vs `<CommitID2>`:

  ```
  git diff <CommitID1>..<CommitID2> <filename>
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

  - `-f` force option is usually needed
  - `-n` for a dry-run, it shows what would be removed
  - `-x` removes also the ignored files


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

  - `-v` to be more verbose
  - `-vv` really verbose, it shows upstream tracking configurations
  - `-r` to see the remote-tracking branches
  - `-a` to see all branches

- Create:

  ```
  git branch <branchname>
  ```

- Create and switch to it:

  ```
  git switch -c <branchname>
  ```

  (legacy command `git checkout -b <branchname>`)

  Note: when switching branch, the Working Tree is updated, but uncommitted changes are not touched.

- Switch:
  
  ```
  git switch <branchname>
  ```

  (legacy command `git checkout <branchname>`)

  Note: when switching branch, the Working Tree is updated, but uncommitted changes are not touched.

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

Tags do reference specific commits. There are annotated tags which store more information and lightweight ones meant for private local bookmarks.

- List:

  ```
  git tag
  ```

- Create for given `<CommitID>` (defaults to HEAD if missing): 
  
  - Lightweight:

    ```
    git tag <tagname> <CommitID>
    ```

  - Annotated:

    ```
    git tag -a -m "msg" <tagname> <CommitID>
    ```

  Note: with the `-f` force option it's possible to update and existing tag.

- Delete:
  
  ```
  git tag -d <tagname>
  ```


### Switch to a commit/tag

- Normally HEAD is a pointer to a branch which by itself refers to its latest commit. When HEAD directly points to a commit, it gets detached from all branches:

  ```
  git switch -d <CommitID>
  ```

  (legacy command `git checkout <CommitID>`)

- To "re-attach" the HEAD, just switch back to a branch:

  ```
  git switch main
  ```

  (legacy command `git checkout main`)

- In the HEAD detached state, commits can be performed, but as soon as we switch away, the Git garbage collector will delete those commits. To avoid the removal of the commits generated in the detached state, create a branch and switch to it:

  ```
  git switch -c <branchname>
  ```

  (legacy command `git checkout -b <branchname>`)


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


### Fetch

Fetch remote branch (including tags) and update the remote-tracking branch:

```
git fetch [<remotename> [<remotebranchname>]]
```

- If `<remotebranchname>` is omitted, then all branches from `<remotename>` are fetched.
- If `<remotename>` is also not present, then it is read from the current branch's [upstream tracking configurations](#branch). When the [upstream tracking configurations](#branch) are missing, `origin` is fetched.


### Merge

Incorporate changes from the given branch into the current branch:

```
git merge [<branchname>]
```

- If `<branchname>` is not provided, then it is read from the current branch's [upstream tracking configurations](#branch).
- It will try to auto merge, if it fails, it will annotate your text files with the differences. You have to review the annotated files and when done, commit them.


### Pull

- Download content from the given remote branch and update the current branch:

  ```
  git pull [<remotename> [<remotebranchname>]]
  ```

  - Default values for `<remotename>` and `<remotebranchname>` are read from the current branch's [upstream tracking configurations](#branch).

- The above pull can be seen as:

  ```
  git fetch <remotename> <remotebranchname>
  git merge <remotename>/<remotebranchname>
  ```


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


### Update fork

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
