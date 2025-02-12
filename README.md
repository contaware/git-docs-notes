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
  - [Basic setup](#basic-setup)
  - [Management](#management)
- [Use](#use)
  - [Init a repository](#init-a-repository)
  - [Status](#status)
  - [Delete a repository](#delete-a-repository)
  - [Add files](#add-files)
  - [Ignore files](#ignore-files)
  - [Commit](#commit)
  - [Log](#log)
  - [Diff](#diff)
  - [Rename/move files/directories](#renamemove-filesdirectories)
  - [Remove files](#remove-files)
  - [Restore files](#restore-files)
  - [Branch](#branch)
  - [Tags](#tags)
- [Remote Use](#remote-use)
  - [Remote repository connections](#remote-repository-connections)
  - [Clone](#clone)
  - [Push](#push)
  - [Pull](#pull)
- [Advanced Use](#advanced-use)
  - [Fetch](#fetch)
  - [Merge](#merge)
  - [Stash](#stash)
  - [Switch to a commit](#switch-to-a-commit)
  - [Revert a commit](#revert-a-commit)
  - [Pick a commit](#pick-a-commit)
- [Use with care](#use-with-care)
  - [Amend](#amend)
  - [Rebase](#rebase)
  - [Reset](#reset)
  - [Push (force)](#push-force)
  - [Remove files completely](#remove-files-completely)
- [Work with GitHub](#work-with-github)
  - [Repository names](#repository-names)
  - [Make a Pull-request (PR)](#make-a-pull-request-pr)
  - [Merge a Pull-request (PR)](#merge-a-pull-request-pr)
  - [Update a fork](#update-a-fork)
- [Legacy git checkout](#legacy-git-checkout)
  - [Switch vs checkout](#switch-vs-checkout)
  - [Restore vs checkout](#restore-vs-checkout)


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

If it's not the first, then open the Environment Variables dialog, under System variables select the `Path` variable, press Edit... and Move Up the `C:\Program Files\Git\cmd` directory to the top. Now close and reopen your terminal to confirm the correct version.


## Config

### Basic setup

1. Set your identity:

   ```
   git config set --global user.name "FirstName LastName"
   git config set --global user.email "email@example.com"
   ```

2. Line ending conversion:

   ```
   git config set --global core.autocrlf <value>
   ```

   - `true`:  **LF → CRLF** when checking out text files and **CRLF → LF** when adding text files to the Staging Area (that's the default of the Windows installer).
   - `input`: No conversion on checkout and **CRLF → LF** when adding text files to the Staging Area.
   - `false`: No conversion (that's the **default** value if `core.autocrlf` is not set).

   In most situations it's best to avoid letting Git interpret your data, and thus set `core.autocrlf` to `false`; that's also the safest option, because the automatic heuristic may incorrectly detect some binary files as text files. If a team is working from Linux/macOS and Windows machines, there must be a convention to only use the LF line ending, but if the Windows users can't follow that, they must set `core.autocrlf` to `true`.

3. By default the branch name for new repositories is `master` (can also be set by Windows installer), but `main` is getting more popular:

   ```
   git config set --global init.defaultBranch main
   ```

4. Set a default text editor other than vim (can also be set by Windows installer):

   - To use nano on Linux:

     ```
     git config set --global core.editor "nano"
     ```
   
   - To use TextEdit on macOS:
   
     ```
     git config set --global core.editor "open -e -W -n"
     ```
   
   - To use Notepad++ on Windows:
   
     ```
     git config set --global core.editor "'C:/Program Files/Notepad++/notepad++.exe' -multiInst -notabbar -nosession -noPlugin"
     ```


### Management

There are multiple places to store the configurations. In order of precedence, from **highest to lowest**, we have:

1. Repository specific stored in `.git/config` and selected by `--local`.
2. User-specific stored in `~/.gitconfig` and selected by `--global`.
3. System-wide stored in `/etc/gitconfig` and selected by `--system`.

Configurations are organized in sections and subsections. The following commands use `<name>` to refer to a configuration entry. A `<name>` has the form `<section>.<key>` or `<section>.<subsection>.<key>`. A `<value>` may contain spaces, in which case it should be enclosed in double-quotes.

- List all configurations:

  ```
  git config list --show-origin
  git config list --show-scope
  ```

- Read a single value:

  ```
  git config get <name>
  ```

  - It returns the value with the **highest precedence**, unless `--local`, `--global` or `--system` is specified.

- Read multiple values:

  ```
  git config get --all --show-scope <name>
  ```

  - It returns the values from **all configuration** files, unless `--local`, `--global` or `--system` is specified.
  - If `<name>` is multi-valued, all its values are returned.

- Write `<value>` to the **local configuration** file:

  ```
  git config set <name> <value>
  ```

  - Use `--global` or `--system` to write to the other locations.
  - Use `--append` to add another `<value>` with the same `<name>`. 

- Remove `<name>` from the **local configuration** file:

  ```
  git config unset <name>
  ```

  - Use `--global` or `--system` to remove from the other locations.
  - Must use `--all` if `<name>` is multi-valued.

- Remove a section or a subsection from the **local configuration** file:

  ```
  git config remove-section <section>
  git config remove-section <section>.<subsection>
  ```

  - Use `--global` or `--system` to remove from the other locations.
  - The removal of a section will not result in the removal of its subsections.


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
  
- Show the unstaged/staged files count:

  ```
  git diff --shortstat
  git diff --staged --shortstat
  ```


### Delete a repository

Git keeps all of its files in the `.git` directory. Just delete that directory.


### Add files

In a Working Tree there are two kinds of files, the **tracked** files which Git knows and the **untracked** files that Git does not know. The `git add` command adds new files or modified/deleted files to the **Staging Area**.

- Stage given files and all files in given directories, and recursively in all sub-directories:

  ```
  git add <filename1> <filename2> <dirname1> <dirname2>
  ```

  - Often used like `git add .` to stage files in current directory, and recursively in all sub-directories.
  - Use `-n` to just show what would be staged, handy to test `.gitignore`.

- Stage files by globs:

  ```
  git add <dirname>/*.txt
  git add "<dirname>/*.txt"
  ```

  - The first form uses **shell globs** which do stage the files inside `<dirname>` only, dot-files are NOT staged and `.gitignore` is NOT honored.
  - The double-quotes in the second form do prevent shell globbing and thus **let git do the globbing**. This means that all files under `<dirname>` are staged recursively, dot-files are also staged and `.gitignore` is honored. Note: the Windows Command Prompt does not know shell globbing, for that terminal git will always perform the globbing.

- Stage all tracked files that have been modified or deleted:

  ```
  git add -u
  ```

- Interactive stage by hunks (=area where the file changed):

  ```
  git add -p <filename>
  ```

- Override (add/remove) execute permissions in Staging Area (the files in Working Tree are left unchanged):

  ```
  git add --chmod=+x <filename>
  git add --chmod=-x <filename>
  ```

  - On systems supporting execute permissions (not Windows), that's usually not necessary, because Git reads the execute permissions from the Working Tree files.
  - Use `git ls-files --stage <filename>` to check the current permissions in Staging Area.

Hint: Git tracks files, not directories. To track an empty directory (that contains no files) create an empty `.placeholder` file in it.


### Ignore files

Create a file named `.gitignore`, open it and one per line enter the file/directory names to ignore:

```
# Ignored files/directories
*.dat
/obj/
data/binary/
```

- A line starting with `#` serves as a **comment**.
- The slash `/` is used as the directory separator.
- An asterisk `*` matches **anything** except a slash. The character `?` matches any **one character** except a slash.
- If there is a slash `/` at the **end**, then the pattern will only match directories.
- If there is a slash `/` at the **beginning** or **middle** (or both), then the pattern is relative to the location of the `.gitignore` file. Otherwise the pattern matches at any level.  
  Examples:
  1. `obj/` matches at any level, while `/obj/` only matches relative to `.gitignore` location.
  2. `data/binary/` and `/data/binary/` both only match relative to `.gitignore` location.

Note: if any of the ignored files were already being tracked, Git would continue to track them.


### Commit

- Commit staged files:

  ```
  git commit
  ```
  - Git launches the [configured](#config) editor to write your message.
  - To quickly add a message use the `-m "msg"` option.

- Stage and commit all tracked files that have been modified or deleted:

  ```
  git commit -a
  ```

A **good commit message** has a subject line with the first letter capitalized, not ending with a period and of 50 characters or less. Use the imperative mood in the subject line. If a description is needed, separate it from the subject with a blank line and wrap it at 72 characters.

After a commit has been created, it can be referenced by its hash value, and usually the first 4-6 hash characters are enough. A branch always refers to its latest commit. In a local repository `HEAD` points to the currently checked-out branch, or directly to a commit when in [detached HEAD state](#switch-to-a-commit).

> - Appending a tilde with a number to `HEAD` or a branch, means to go back by the given amount of generations, so `HEAD~1` refers to the parent commit and `HEAD~2` is the grandparent one. The tilde symbol always follows the first parent ([merge commits](#merge) have two parents).
> - With the caret symbol we can specify the parent to follow, `HEAD^<n>` means the n-th parent.
> - `HEAD~1` or `HEAD~` or `HEAD^1` or `HEAD^` are all equivalent.
> - We can use multiple carets or even combine tilde and caret, like `HEAD~2^2` which means "go back to grandparent and then take the second parent of it".

### Log

- Show commits for given `<revision-range>` following the parents; limit to `<filenames>` if provided:

  ```
  git log [<revision-range>] [-- <filenames>]
  ```

  - `<revision-range>` defaults to `HEAD`. It's possible to provide `<CommitID1>..<CommitID2>` which includes commits reachable from `<CommitID2>` but not from `<CommitID1>`.
  - In `<filenames>` it's possible to use globs, more details about them under [Add files](#add-files). When `<filenames>` is a single file, use `--follow` to show the history across renames.
  - `-<count>` limits the shown commits to the given count.
  - `--reverse` displays in reverse order (applied after commit limiting options).
  - `--oneline` displays in one-liners.
  - `--graph` displays a text-based graph in topological order.
  - `-p` produces patch text.
  - `--name-status` shows names and status of changed files. The status letter `R` is followed by a score which denotes the percentage of similarity between source and target.
  - `--diff-filter=<filterchars>` includes by status, `<filterchars>` can be any combination of A (added), D (deleted), M (modified), R (renamed). Use lower-case letters to exclude.
  - To filter by date many documents show examples without a time, like `--after="2023-12-20"`, but then the time **defaults to the time when you run the command**. To avoid confusion also provide the time using the [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) format:  
  `--after="2023-12-20T10:30:00+01:00"`  
  `--before="2023-12-20T17:30:00+01:00"`
  - `-i --grep="pattern"` shows commits with commit message case-insensitive matching `pattern`.
  - `-S "code"` shows commits in which the number of `code` occurrences changed.

- Show information about the given `<CommitID>`:

  ```
  git show [<CommitID>]
  ```

  - If `<CommitID>` is not provided it **defaults** to `HEAD`.
  - `--name-status` shows names and status of changed files and prevents diff output.

- Show `<filename>` at given `<CommitID>`:

  ```
  git show <CommitID>:<filename>
  ```

  - To save it: `git show <CommitID>:<filename> > <savename>`.

- Show `<filename>` with the commits that last changed each line:

  ```
  git blame <filename>
  ```

  - Use `-L <start>[,<end>]` to limit the lines range (one-based indexes).
  
Hint: exit the pager by pressing the `q` key. To search type `/<text>`, press `n` for the next occurrence and `N` for the previous one.


### Diff

If no `<filenames>` are provided the command will show changes for all files. By default it shows the lines that changed, with the `--word-diff` option it highlights the words differences as `[-removed-]` and `{+added+}`.

- `<CommitID>` vs Working Tree:

  ```
  git diff [<CommitID>] [-- <filenames>]
  ```

  - If `<CommitID>` is not provided it **defaults** to the **Staging Area**.

- `<CommitID>` vs Staging Area:

  ```
  git diff --staged [<CommitID>] [-- <filenames>]
  ```

  - If `<CommitID>` is not provided it **defaults** to `HEAD`.
  - Compare renamed files with `git diff --staged -- <oldfilename> <newfilename>`.

- `<CommitID1>` vs `<CommitID2>`:

  ```
  git diff <CommitID1> <CommitID2> [-- <filenames>]
  ```

  - Compare renamed files with `git diff <CommitID1> <CommitID2> -- <oldfilename> <newfilename>`.

- Compare files outside Git, or two Working Tree files:
  
  ```
  git diff --no-index <filename1> <filename2>
  ```

Hint: exit the pager by pressing the `q` key. To search type `/<text>`, press `n` for the next occurrence and `N` for the previous one.


### Rename/move files/directories

- Stage for rename and rename in Working Tree (can be undo):

  ```
  git mv <oldname> <newname>
  ```

  - `<oldname>` can be a file or a directory.
  - `<newname>` must be non-existing.

- Stage for move and move in Working Tree (can be undo):

  ```
  git mv <filename1> <filename2> <dirname1> <dirname2> <existing_dir>
  ```

  - The given files and directories will be moved into `<existing_dir>`.

Manually:

1. Git will detect manually renamed/moved files/directories as soon as you stage with the `git add .` command.
2. Renamed/moved files with modified content will also be detected if they do not differ too much. If the detection does not work, split the modifications and the renames/moves into two commits.
3. File history is preserved when Git detects the renames/moves.


### Remove files

- Stage for removal and delete from Working Tree (can be undo):

  ```
  git rm <filename1> <filename2>
  git rm -r <filename1> <filename2> <dirname1> <dirname2>
  ```

  - With the `-r` option we can remove all known files in given directories, and recursively in all sub-directories.

- Remove files not under version control:

  ```
  git clean
  ```

  - `-f` force option is usually needed.
  - `-n` for a dry-run, it shows what would be removed.
  - `-x` removes also the ignored files.


### Restore files

File restoration does copy a file from the given source to the Working Tree and/or the Staging Area overwriting without warning.

To better understand the following commands, it's important to remember that when a tracked file is not staged, the Staging Area contains the `HEAD` commit version of that file.

- Revert Staging Area (unstage) by taking it from `HEAD`:

  ```
  git restore --staged <filename>
  ```

  - If `<filename>` is not in `HEAD`, then it is removed from the Staging Area. Note that if the repository is new, with no commits, you must unstage with `git rm --cached <filename>`.

- Revert Working Tree by taking it from Staging Area:
  
  ```
  git restore <filename>
  ```

- Revert Working Tree and Staging Area by taking it from given source:

  ```
  git restore -s <CommitID> --staged --worktree <filename>
  ```

> Restore command defaults:
> 1. If no destination is specified, it defaults to `--worktree`.
> 2. `HEAD` is the default source if `--staged` is given, otherwise it is the Staging Area.


### Branch

In addition to the local branches stored in your machine and the remote branches residing on a remote server, there is also the concept of a **remote-tracking branch**. This type of branches are locally stored references pointing to remote branches. They are named like `<remotename>/<remotebranchname>` and can be listed with `git branch -r`.

The explicit association between a remote branch and a local branch is not mandatory, but comfortable when synchronizing, it avoids us typing the full commands with all the arguments. With `git config list` we can see those **upstream tracking configurations** for all the local branches, they are `branch.<branchname>.remote` and `branch.<branchname>.merge`.

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
  git switch -c <branchname> [<CommitID>]
  ```

  - If `<CommitID>` is not provided it **defaults** to `HEAD`.

- Switch:
  
  ```
  git switch <branchname>
  ```

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

  Hint: the [Push](#push) section shows how to upload the tags to the remote repository.

- Delete local:
  
  ```
  git tag -d <tagname>
  ```

- Delete remote:

  ```
  git push -d <remotename> <remotetagname>
  ```


## Remote Use

### Remote repository connections

- Add:
  
  The following command adds the `<remotename>` alias for the given url:

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

The clone command creates a remote connection named `origin` pointing back to the cloned repository.

The clone command only creates the local branch **main** (or master) with the correct [upstream tracking configurations](#branch). To create the **other local branches** and set their [upstream tracking configurations](#branch):

```
git branch <localbranchname> <remotename>/<remotebranchname>
```


### Push

A push is used to upload local repository content to a remote repository.

If someone else pushes its code and then you try to push as well, your push will rightly be rejected. You need to first do a pull and after that retry your push.

- Update the remote branch with the changes of your local branch:

  ```
  git push [<remotename> [<localbranchname>]]
  ```
  
  - The local branch is pushed to the remote branch having the same name.
  - If the local repository was not created by a `git clone` and the local branch has never been pushed, then with your first push provide the `-u` option to set the [upstream tracking configurations](#branch).
  - When omitting `<localbranchname>`, then the current branch is pushed to the remote branch with the same name, but only if the [upstream tracking configurations](#branch) have the same two names set.
  - If `<remotename>` is not provided, then Git pushes to the remote from the [upstream tracking configurations](#branch).

- Tags have to be pushed explicitly (as opposed to pull and fetch which get them automatically):

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

- Download content from the given remote branch and create a new local branch:

  ```
  git fetch <remotename> <remotebranchname>
  git branch <localbranchname> <remotename>/<remotebranchname>
  ```

  - The branch command will also set the [upstream tracking configurations](#branch).

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

Fetch from remote branch into remote-tracking branch:

```
git fetch [<remotename> [<remotebranchname>]]
```

- Remote branches can be listed with `git ls-remote <remotename>`.
- If `<remotebranchname>` is omitted, then all branches from `<remotename>` are fetched.
- If `<remotename>` is also not present, then it is read from the current branch's [upstream tracking configurations](#branch). When the [upstream tracking configurations](#branch) are missing, `origin` is fetched.
- Fetch knows the destination thanks to the `remote.<remotename>.fetch=+refs/heads/*:refs/remotes/<remotename>/*` configuration automatically created with `<remotename>`.

Fetch from remote branch without passing through the remote-tracking branch:

```
git fetch <repourl> <remotebranchname>[:<localbranchname>]
```

- Remote branches can be listed with `git ls-remote <repourl>`.
- If `<localbranchname>` is given, the command may refuse to update it, use `-f` to force that.
- If `<localbranchname>` is omitted, the fetched objects can be accessed through `FETCH_HEAD (e.g. git log FETCH_HEAD)`. Remember that with the next fetch, the Git garbage collector can clean-up unreferenced objects of a previous fetch.

> If a branch name does not start with `refs/ (e.g. refs/heads/main)` the command will try to infer where in `refs/*` it belongs.


### Merge

> Git chooses one of these two types of merge:
>
> 1. When the current branch's tip is a direct ancestor of the branch you are merging in, Git does a **fast-forward merge** by just moving the `HEAD` and branch pointers of the current branch forward.
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


### Switch to a commit

- Normally `HEAD` is a pointer to a branch which by itself refers to its latest commit. When `HEAD` directly points to a commit, it gets detached from all branches:

  ```
  git switch -d <CommitID>
  ```

- To "re-attach" the `HEAD`, just switch back to a branch:

  ```
  git switch <branchname>
  ```

- In the detached `HEAD` state, commits can be performed, but as soon as we switch away, the Git garbage collector will delete them after some time. To avoid that, create a branch and switch to it:

  ```
  git switch -c <branchname>
  ```


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


### Pick a commit

- Create a new commit with the changes introduced by the given commit:

  ```
  git cherry-pick <CommitID>
  ```

  - If there are merge conflicts, review the annotated files, `git add` them and continue with `git cherry-pick --continue`.
  - The `-e` option lets you edit the commit message.
  - With the `--no-commit` option the changes are only applied to the Working Tree and the Staging Area.

- Interrupt a cherry-pick process and reconstruct the pre-cherry-pick state:

  ```
  git cherry-pick --abort
  ```


## Use with care

The commands in this section will **delete commits** and **replace commits**, so never use them on commits that already exist outside your local repository and that people may have based work on.


### Amend

Edit current branch's tip commit:

```
git add <correctfile>
git rm <wrongfile>
git commit --amend
```

- The editor will open to let you change the commit message.
- It removes the tip commit and replaces it with a new one having a different hash.


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

- Show the branch and `HEAD` modifications log to find the wanted commit:

  ```
  git reflog
  ```

  - The most recent operations are listed at the top.
  - The Git garbage collector keeps local commits not accessible by a branch, tag or `HEAD` for some time before deleting them definitively.
  
- Move both `HEAD` and the current branch pointer to the given commit:

  ```
  git reset [<CommitID>]
  ```

  - If `<CommitID>` is not provided it **defaults** to `HEAD`.
  - By default the files in the Staging Area are reset (unstaged), but the **Working Tree** remains **untouched**.
  - With the `--hard` option also the Working Tree is reset to match the state of the given commit (uncommitted changes are overwritten).


### Push (force)

The push command refuses to update a remote ref that is not an ancestor of the local ref used to overwrite it. So it's normal that a push attempt will fail after one of the operations from this section. Your Git GUI Client may suggest to perform a **Sync or Pull, do not do that!** If you really want to update your remote repository, despite the warning from the beginning of this section, you need to force the push like:

```
git push --force-with-lease <remotename> <localbranchname>
```

- The `--force-with-lease` option will protect all remote refs that are going to be updated by requiring their current value to be the same as the remote-tracking branch we have for them. In other words, you can push your changes as long as no one else has pushed any updates to the remote repository since your last fetch.


### Remove files completely

There may be situations in which you want to purge the repository history from a file because it contains sensitive data or simply because it is too big.

If it's the last commit, just [amend](#amend) it. If it's not the last commit and you are comfortable with the [rebase](#rebase) command, you may try an interactive rebase dropping the commit that introduced the file.

However, there is also a utility designed specifically for this purpose and which can be employed in any situation. To use it, first [install git-filter-repo](https://github.com/newren/git-filter-repo/blob/main/INSTALL.md) with your favorite package manager or do a manual install like:

1. Download the [git-filter-repo](https://github.com/newren/git-filter-repo/blob/main/git-filter-repo) python script making sure it has no extension.
   
2. If your python3 executable is named `python` instead of `python3`, modify the shebang inside the downloaded script file.
   
3. Move the script file to the directory shown by the `git --exec-path` command. 

As `git filter-repo` irreversibly rewrites your history, either do a fresh clone of your repository or use the `--force` option being aware that you must have a backup copy of your repository. As a precaution, the configured remotes are removed when executing `git filter-repo`, reason more to have a backup from which you can restore the `.git/config` file containing your remotes. The `git filter-repo` command works by including the paths specified, but usually you want the inverse behavior, you want it to discard the specified paths, that's achieved by providing the `--invert-paths` option. There are many more options, [see them here](https://www.mankier.com/1/git-filter-repo).
   
1. Generate reports under `.git/filter-repo/analysis` to find big files, deleted files or renames:

   ```
   git filter-repo --analyze
   ```

2. Remove the specified **files/directories** from all commits in all branches:

   ```
   git filter-repo --invert-paths --path <path1> --path-glob "<path2>"
   git filter-repo --invert-paths --use-base-name --path <basename1> --path-glob "<basename2>"
   ```

   - Without `--use-base-name` the given paths are **relative to the repository root**. A provided path ending with a slash, only matches directories, while one without ending slash, matches both files and directories.
   
   - With `--use-base-name` all provided basenames are matched to the **basename of the files, directories are NOT matched**.

   - `--path` requires an **exact** match.

   - `--path-glob` supports glob characters. Remember the quotes around the given path to prevent shell globbing. The `*` glob character works **across multiple directories**.
   
   - **Renames are NOT followed**, you may need to specify multiple path options or use globs.

   - To test your command, first run `git filter-repo --invert-paths --dry-run` and save `.git/filter-repo/fast-export.filtered`. Next run the command you wish to test with `--dry-run` and diff the two filtered versions. Do not compare to `.git/filter-repo/fast-export.original` because that shows many irrelevant differences.
   
   Examples:

   ```
   git filter-repo --invert-paths --path app.exe --path-glob "src/*/data"
   git filter-repo --invert-paths --path ".DS_Store" --path-glob "*/.DS_Store"
   git filter-repo --invert-paths --use-base-name --path ".DS_Store"
   git filter-repo --invert-paths --use-base-name --path-glob "*.zip"
   ```
   
3. With `git log` make sure there is no trace left of your deleted file(s).
   
4. Restore `.git/config` or re-create the remotes:

   ```
   git remote add origin https://github.com/username/repo.git
   ```

5. Optionally delete the `.git/filter-repo` directory (if present).

6. As the history has been re-written, force the push:

   ```
   git push origin --force --all
   git push origin --force --tags
   ```


## Work with GitHub

### Repository names

- Better if your repository name is unique, that avoids confusion with other GitHub users.

- The GitHub repository names can only contain ASCII letters, digits and the characters:  
  `. - _`

- If you want your GitHub repositories to be easily readable and recognizable, use hyphens instead of underscores.

- Prefer all lowercase letters.


### Make a Pull-request (PR)

1. Fork another's person repository:
   
   Login to your GitHub account, go to another's person repository and press the **Fork** button.

2. In your terminal clone the above forked repository:

   ```
   git clone https://github.com/username-fork/repo.git
   cd repo
   ```

3. Create a new branch with the name you like and switch to it:
   
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


### Merge a Pull-request (PR)

It's possible to merge a PR from the GitHub web interface, but sometimes you want to test the code in your local repository and also merge it there, let's see how.

1. Download the PR into a new local branch with the name you like:

   - **Option A** is the preferred choice, the PR is taken from the forked repository:

     ```
     git switch -c <branchname> main
     git pull https://github.com/username-fork/repo.git <branchname>
     ```

     - The fork url can be found in your GitHub under the PR.
     - In your GitHub you can ask the creator of the PR to make changes through additional commits. The PR will automatically update itself, you only have to repeat the above pull command to get the new commits.
   
   - **Option B** must be used when the fork has been deleted:

     ```
     git fetch origin pull/<ID>/head:<branchname>
     git switch <branchname>
     ```

     - Replace `<ID>` with the PR number found in your GitHub.
     - The remote `refs/pull/` namespace is read-only, it's not possible to push to it.

2. Optionally do your corrections by adding commits to `<branchname>`:
   
   ```
   git add <filename>
   git commit -m "Adaptations to PR"
   ```

   - Your changes will not appear in the GitHub PR, use the commit messages to refer to the PR.
   
3. Merge `<branchname>` into `main` and push `main` to GitHub:

   ```
   git switch main
   git merge --no-ff <branchname>
   git push origin main
   ```

   - Most of the time GitHub will detect that the PR has been merged from the terminal, in case it does not detect it, you can manually close the PR.
   - The `--no-ff` option is suggested by GitHub, it prevents fast-forward merges. You can also omit it, in which case a fast-forward merge is performed when possible. Note that fast-forward merges can only be done from the terminal, GitHub does not offer this possibility in the web interface.


### Update a fork

After a while the original repository may change, before doing another PR, it's important to update your fork. There is a handy button in your GitHub, but if you also have a local clone, you must update it from the terminal. By convention the connection alias of the original repository is named `upstream`.

1. Add the original repository (read-only for you):

   ```
   git remote add upstream https://github.com/username-original/repo.git
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


## Legacy git checkout

The `git checkout` is confusing, especially for beginners, because it performs two distinct tasks in one command. Fortunately we now have `git switch` and `git restore`, but many documents still use `git checkout`, so we must know about it.


### Switch vs checkout

- Switch to the given branch:
  - `git switch <branchname>`
  - `git checkout <branchname>` (legacy command)
  
- Create and switch to the given branch:
  - `git switch -c <branchname>`
  - `git checkout -b <branchname>` (legacy command)

- Switch to a commit detaching the `HEAD`:
  - `git switch -d <CommitID>` (without `-d` the command fails)
  - `git checkout <CommitID>` (legacy command)


### Restore vs checkout

- Revert Staging Area (unstage) by taking it from `HEAD`:
  - `git restore --staged <filename>`
  - `git reset <filename>` (legacy command)
  
- Revert Working Tree by taking it from Staging Area:
  - `git restore <filename>`
  - `git checkout <filename>` (legacy command)
  
- Revert Working Tree and Staging Area by taking it from given source:
  - `git restore -s <CommitID> --staged --worktree <filename>`
  - `git checkout <CommitID> <filename>` (legacy command)
