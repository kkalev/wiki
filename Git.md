# GitHub Guide
## Getting Started
* [Reference](https://docs.github.com/en/get-started/getting-started-with-git/setting-your-username-in-git)
### Username
* Set global username: `git config --global user.name "Mona Lisa"`
### Remote Repositories
* Add remote origin: `git remote add origin <REMOTE_URL>` (remote name and remote URL)
* Verify remote: `git remote -v`
* Change remote URL: `git remote set-url origin <REMOTE_URL>`
### Push commits
* Main command: `git push REMOTE-NAME BRANCH-NAME`

## Using Git
* [Reference](https://docs.github.com/en/get-started/using-git/about-git)
### Basic Git Commands
* `git init`: initializes a brand new Git repository and begins tracking an existing directory. It adds a hidden subfolder within the existing directory that houses the internal data structure required for version control.
* `git clone`: Creates a local copy of a project that already exists remotely. The clone includes all the project's files, history, and branches.
* `git add`: Stages a change.
* `git commit`: Saves the snapshot to the project history and completes the change-tracking process. In short, a commit functions like taking a photo. Anything that's been staged with `git add` will become a part of the snapshot with `git commit`.
* `git status`: Shows the status of changes as untracked, modified, or staged.
* `git branch`: Shows the branches being worked on locally.
* `git merge`: Merges lines of development together.
* `git pull`: Updates the local line of development with updates from its remote counterpart.
* `git push`: Updates the remote repository with any commits made locally to a branch.
* **Example: Contribute to an existing repository**
```
# download a repository on GitHub to our machine
# Replace `owner/repo` with the owner and name of the repository to clone
git clone https://github.com/owner/repo.git

# change into the `repo` directory
cd repo

# create a new branch to store any new changes
git branch my-branch

# switch to that branch (line of development)
git checkout my-branch

# make changes, for example, edit `file1.md` and `file2.md` using the text editor

# stage the changed files
git add file1.md file2.md

# take a snapshot of the staging area (anything that's been added)
git commit -m "my snapshot"

# push changes to github
git push --set-upstream origin my-branch
```
* **Example: Start a new repository and publish it to GitHub**
```
# create a new directory, and initialize it with git-specific functions
git init my-repo

# change into the `my-repo` directory
cd my-repo

# create the first file in the project
touch README.md

# git isn't aware of the file, stage it
git add README.md

# take a snapshot of the staging area
git commit -m "add README to initial commit"

# provide the path for the repository you created on github
git remote add origin https://github.com/YOUR-USERNAME/YOUR-REPOSITORY-NAME.git

# push changes to github
git push --set-upstream origin main
```
### Push commits to remote repo
* The `git push` command takes two arguments:
  - A remote name, for example, `origin`
  - A branch name, for example, `main`
  - For example: `git push REMOTE-NAME BRANCH-NAME`
  - You usually run `git push origin main` to push your local changes to your online repository.
* If your local copy of a repository is out of sync with, or "behind," the upstream repository you're pushing to, you'll get a message saying `non-fast-forward updates were rejected`. This means that you must retrieve, or "fetch," the upstream changes, before you are able to push your local changes.
* To push a single tag, you can issue the same command as pushing a branch: `git push REMOTE-NAME TAG-NAME`
* The syntax to **delete** a branch is a bit arcane at first glance: `git push REMOTE-NAME :BRANCH-NAME`
  -  Here, you're telling Git to push **nothing** into `BRANCH-NAM`E on `REMOTE-NAME`. Because of this, `git push` deletes the branch on the remote repository.
### Get changes from remote repo
* Use `git fetch` to retrieve new work done by other people. Fetching from a repository grabs all the new remote-tracking branches and tags *without* merging those changes into your own branches. `git fetch REMOTE-NAME`
* Merging combines your local changes with changes made by others: `git merge REMOTE-NAME/BRANCH-NAME`
* `git pull` is a convenient shortcut for completing both `git fetch` and `git merge` in the same command: `git pull REMOTE-NAME BRANCH-NAME`
  - Because `pull` performs a merge on the retrieved changes, you should ensure that your local work is committed before running the `pull` command. If you run into a *merge conflict* you cannot resolve, or if you decide to quit the merge, you can use `git merge --abort` to take the branch back to where it was in before you pulled.

# Atlassian Git Guide
* [Reference](https://www.atlassian.com/git/tutorials)
* Cheat [Sheet](https://www.atlassian.com/git/tutorials/atlassian-git-cheatsheet)
## Git stash
* `git stash` temporarily shelves (or stashes) changes you've made to your working copy so you can work on something else, and then come back and re-apply them later on.
* The `git stash` command takes your uncommitted changes (both staged and unstaged), saves them away for later use, and then reverts them from your working copy.
* You can reapply previously stashed changes with `git stash pop`
* Alternatively, you can reapply the changes to your working copy *and keep them in your stash* with `git stash apply`
* Adding the `-u` option (or `--include-untracked`) tells git stash to also stash your untracked files
* You can run `git stash` several times to create multiple stashes, and then use `git stash list` to view them.
```
$ git stash list
stash@{0}: WIP on main: 5002d47 our new homepage
stash@{1}: WIP on main: 5002d47 our new homepage
stash@{2}: WIP on main: 5002d47 our new homepage
```
* To provide a bit more context, it's good practice to annotate your stashes with a description, using `git stash save "message"`
* By default, `git stash pop` will re-apply the most recently created stash: `stash@{0}`
* You can choose which stash to re-apply by passing its identifier as the last argument
* If you decide you no longer need a particular stash, you can delete it with `git stash drop`: `$ git stash drop stash@{1}`
* Or you can delete all of your stashes with `git stash clear`
## Undoing changes
* The following examples will assume we have a commit history that looks like:
```
git log --oneline
872fa7e Try something crazy
a1e8fb5 Make some important changes to hello.txt
435b61d Create hello.txt
9773e52 Initial import
```
* We will focus on **undoing** the `872fa7e Try something crazy` commit.
* If we execute `git revert HEAD`, Git will create a new commit with the *inverse of the last commit*. This adds a new commit to the current branch history and now makes it look like:
```
git log --oneline
e2f9a78 Revert "Try something crazy"
872fa7e Try something crazy
a1e8fb5 Make some important changes to hello.txt
435b61d Create hello.txt
9773e52 Initial import
```
  - This is the ideal 'undo' method for working with public shared repositories.
* If we invoke `git reset --hard a1e8fb5` the commit history is **reset to that specified commit**. Examining the commit history with git log will now look like:
```
git log --oneline
a1e8fb5 Make some important changes to hello.txt
435b61d Create hello.txt
9773e52 Initial import
```
* The log output shows the `e2f9a78` and `872fa7e` commits no longer exist in the commit history. At this point, we can continue working and creating new commits as if the 'crazy' commits never happened.
  - Doing a reset is great for local changes however it adds complications when working with a shared remote repository.
  - If we have a shared remote repository that has the `872fa7e` commit **pushed to it**, and we try to `git push` a branch where we have reset the history, Git will catch this and **throw an error**. Git will assume that the branch being pushed is not up to date because of it's missing commits. In these scenarios, `git revert` should be the preferred undo method.
* In some cases though, you might not need to remove or reset the last commit. Maybe it was just made prematurely. In this case you can *amend* the most recent commit.
  - Once you have made more changes in the working directory and staged them for commit by using `git add`, you can execute `git commit --amend`.
## Using Branches
* The `git branch` command lets you create, list, rename, and delete branches.
* Instead of copying files from directory to directory, Git stores a branch as a reference to a commit. In this sense, a branch represents the tip of a series of commits—it's not a container for commits. The history for a branch is extrapolated through the commit relationships.
* `git branch`: List all of the branches in your repository. This is synonymous with `git branch --list`
* `git branch <branch>`: Create a new branch called `＜branch＞`. This does *not* check out the new branch.
* `git branch -d <branch>`: Delete the specified branch. This is a “safe” operation in that Git *prevents you from deleting the branch if it has unmerged changes*.
* `git branch -D <branch>`: Force delete the specified branch, *even if it has unmerged changes*. This is the command to use if you want to permanently throw away all of the commits associated with a particular line of development.
* `git branch -a`: List *all* branched (local and remote)
* To delete a *remote branch* execute the following: `git push origin --delete crazy-experiment`
* The `git checkout` command lets you navigate between the branches created by `git branch`. The `git checkout` command accepts a `-b` argument that acts as a convenience method which will create the new branch and immediately switch to it.
* `git merge` will combine multiple sequences of commits into one unified history. In the most frequent use cases, `git merge` is used to combine two branches.
* A fast-forward merge can occur when there is a linear path from the current branch tip to the target branch. Instead of “actually” merging the branches, all Git has to do to integrate the histories is move (i.e., “fast forward”) the current branch tip up to the target branch tip.
* **Fast-forward merge example**
```
# Start a new feature
git checkout -b new-feature main
# Edit some files
git add <file>
git commit -m "Start a feature"
# Edit some files
git add <file>
git commit -m "Finish a feature"
# Merge in the new-feature branch
git checkout main
git merge new-feature
git branch -d new-feature
```
### Detached HEAD
* Remember that the `HEAD` is Git’s way of referring to the current snapshot.
* Internally, the `git checkout` command simply updates the `HEAD` to point to either the specified branch or commit. When it points to a branch, Git doesn't complain, but when you check out a commit, it switches into a `detached HEAD` state.
* If you were to start developing a feature while in a detached `HEAD` state, there would be *no branch allowing you to get back to it*. When you inevitably check out another branch (e.g., to merge your feature in), there would be no way to reference your feature:
## Reset, Checkout and Revert
* A checkout is an operation that moves the `HEAD` ref pointer to a specified commit.
* A revert is an operation that takes a specified commit and creates a new commit which **inverses the specified commit**. `git revert` can only be run at a commit level scope and has no file level functionality.
* A reset is an operation that takes a specified commit and resets the "three trees" to match the state of the repository at that specified commit. A reset can be invoked in three different modes which correspond to the three trees.
* **Checkout and reset are generally used for making local or private 'undos'.** They modify the history of a repository that **can cause conflicts when pushing to remote shared repositories**. Revert is considered a safe operation for 'public undos' as it creates new history which can be shared remotely and doesn't overwrite history remote team members may be dependent on.
![image](https://github.com/kkalev/PersonalWiki/assets/2707736/231d3210-a6be-44c9-b4d3-a28bfbc82879)
* The parameters that you pass to `git reset` and `git checkout` determine their scope. When you don’t include a file path as a parameter, they operate on whole commits.
* On the commit-level, resetting is a way to move the tip of a branch to a different commit. **This can be used to remove commits from the current branch.** For example, the following command moves the hotfix branch backwards by two commits: `git checkout hotfix; git reset HEAD~2`
* The two commits that were on the end of hotfix are now *dangling, or orphaned commits*. This means they will be deleted the next time Git performs a garbage collection.
![image](https://github.com/kkalev/PersonalWiki/assets/2707736/4e574ae2-e78a-42bf-8272-9b0642f8902d)
* This usage of `git reset` is a simple way to undo changes that *haven’t been shared with anyone else*. It’s your go-to command when you’ve started working on a feature and find yourself thinking, “Oh crap, what am I doing? I should just start over.”
* You can also get `git reset` to alter the staged snapshot and/or the working directory by passing it one of the following flags:
  - `--soft`: The staged snapshot and working directory are not altered in any way.
  - `--mixed`: The staged snapshot is updated to match the specified commit, but the working directory is not affected. This is the default option.
  - `--hard`: The staged snapshot and the working directory are both updated to match the specified commit.
* You can also check out arbitrary commits by passing the commit reference instead of a branch. For example, the following command will check out the grandparent of the current commit: `git checkout HEAD~2`
  - This is useful for quickly inspecting an old version of your project. However, since there is no branch reference to the current HEAD, this puts you in a detached HEAD state. This can be dangerous if you start adding new commits because there will be no way to get back to them after you switch to another branch.
* Reverting undoes a commit by creating a new commit. This is a safe way to undo changes, as it has no chance of re-writing the commit history. For example, the following command will figure out the changes contained in the 2nd to last commit, create a new commit undoing those changes, and tack the new commit onto the existing project: `git checkout hotfix; git revert HEAD~2`
* For this reason, `git revert` should be used to undo changes on a **public branch**, and `git reset` should be reserved for undoing changes on a **private branch**.
* You can also think of `git revert` as a tool for undoing **committed** changes, while `git reset HEAD` is for undoing **uncommitted** changes.
