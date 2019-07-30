# [Git](https://git-scm.com)

## Initial Setup

### Clone a repo

Clone the foo repo into a new directory called foo:

```sh
git clone https://github.com/<username>/foo.git foo
```

### Setup Remotes

First, let's see a list of the repositories (remotes) whose branches you track:

```sh
git remote -v
```

Oh, it looks like we haven't setup upstream. Now is the time:

```sh
git remote add upstream https://github.com/<upstream_username>/<repo_name>.git
git fetch upstream
```

## Workflow

### Branching

When working on a fork, you could be switching between different branches quite commonly. As such, you generally want to stay off the master branch and work on your own feature branches so that master is always clean and you can base new branches off of it.

```sh
git checkout -b <new_branch_name>
```

If upstream has a special develop branch or something, you can checkout that branch separately, but setup tracking so you can sync it up from time to time. Like the master branch, don't work directly on this one. Try to keep it clean.

```sh
git checkout -b <new_branch_name> --track upstream/<remote_branch_to_track>
```

Maybe you made some progress on a branch at work, but now you want to continue work at home. In that case, you're dealing with your own fork's branch, so you'll checkout from origin.

```sh
git checkout -b <new_branch_name> --track origin/<remote_branch_to_track>
```

Use the `-B` option flag to force it.

### Switching Branches

First, you'll want to know what branches are available in your working directory:

```sh
git branch
```

Result:

```md
develop
feature_x
master
```

Now, you can easily switch between branches with git checkout:

```sh
git checkout master
git checkout develop
git checkout feature_x

git config --global alias.co 'checkout'
git co master
```

### Status

Not sure if you're working on a clean branch? Want to see what files have changed? Git status will show you a report.

```sh
git status
```

### Staging Changes

Now that you've added or modified some files, you need to stage those commits into "the staging area." Think of git commits like an array of airlock hatches on a space ship. On this space ship, you can only open the door to one airlock at a time. When you open the hatch, you can put stuff in or take stuff out at will. Not until you've closed the door have you committed those changes (git commit) and not until you hit the red button do all those hatches open up into space (git push).

You can stage inidividual files or all files at once.

```sh
git add foo.js
git add .
```

### Unstaging Changes

Maybe you accidentally staged some files that you don't want to commit.

```sh
git reset HEAD foo.js
git reset HEAD .
git config --global alias.unstage 'reset HEAD'
git unstage .
```

### Commits

Commit often. You can always squash down your commits before a push.

```sh
git commit -m "Updated README"
```

Want to automatically stage files that have been modified and deleted, but new files you haven't told git about will be unaffected? Pass the `-a` or `--all` option flag:

```sh
git commit -am "Updated README"
```

### Undoing Commits

The following command will undo your most recent commit and put those changes back into staging, so you don't lose any work:

```sh
git reset --soft HEAD~1
```

The next one will completely delete the commit and throw away any changes. Be absolutely sure this is what you want:

```sh
git reset --hard HEAD~1
```

### Squashing Commits

Maybe you have 4 commits, but you haven't pushed anything yet and you want to put everything into one commit so your boss doesn't have to read a bunch of garbage during code review.

```sh
git rebase -i HEAD~4
```

An interactive text file is displayed. You'll see the word "pick" to the left of each commit. Leave the one at the top alone and replace all the others with "s" for squash, save and close the file. This will display another interactive window where you can update your commit messages into one new commit message. I like to use "f" instead of "s", because I usually work in such a way that I name my first commit appropriately from the get-go. "f" just skips the 2nd interactive file and uses the first commit message.

### Pushing

Push a local branch for the first time:

```sh
git push --set-upstream origin <branch>
git push
```

Push a local branch to a different remote branch:

```sh
git push origin <local_branch>:<remote_branch>
```

Use the `-f` option flag to force it.

### Undo Last Push

Some would say this is bad practice. Once you push something you shouldn't overwrite those changes. Instead, you're supposed to create a new commit that reverts the changes in the last one. So, technically, you shouldn't do this, but... you can?

```sh
git reset --hard HEAD~1 && git push -f origin master
```

### Fetching

Fetch changes from upstream:

```sh
git fetch upstream
```

Fetch changes from both origin and upstream in the same shot:

```sh
git fetch --multiple origin upstream
```

### Merging

To be honest, I haven't used this command in quite some time. In my experience, it has created merge bubbles that have overwritten mine or others' code. For a better workflow, refer to rebasing, below.

Nonetheless, here's how you merge-in changes from origin's `feature_x` branch:

```sh
git fetch origin
git merge origin/feature_x
```

### Pulling

Pulling is just doing a fetch followed by a merge. If you know your branch is clean (e.g., master branch), go ahead and get the latest changes. There will be no merge conflicts as long as your branch is clean.

```sh
git pull origin/feature_x
```

### Rebasing

Rebasing is a way of rewriting history. In place of merge, what this does is stacks your commits on top of commits that are already pushed up. In this case, you want to stack your commits on top of `origin/feature_x`:

```sh
git fetch origin
git rebase origin/feature_x
```

If you already have a local branch set to track `feature_x` then just do:

```sh
git rebase feature_x
```

Would you like to fetch, merge and then stack your changes on top, all in one shot? You can! If you have tracking setup on the current branch, just do:

```sh
git pull --rebase
```

Another great use of rebasing is just rewriting commit messages. To get an interactive text editor for the most recent commit, do:

```sh
git rebase -i HEAD~1
```

Now, you can replace "pick" with "r" and just change the commit message.

### Manually Set Tracking

Perhaps you forgot to setup tracking when you pulled down a remote branch. No worries:

```sh
git config branch.<local_branch>.remote origin
git config branch.<local_branch>.merge refs/heads/<remote_branch>
```

### Deleting Branches

Delete a local branch:

```sh
git branch -d <local_branch>
```

Use the `-D` option flag to force it.

Delete a remote branch on origin:

```sh
git push origin :<remote_branch>
```

### Stashing

Sometimes you need to stash your changes so you can be on a clean branch or maybe because you want to go back and try something before you make a commit with these changes. Here's how you do a stash:

```sh
git stash
```

Now, if you want to unstash those changes and bring them back into your working directory:

```sh
git stash pop
```

Or maybe you want to unstash your changes without popping them off the stack. In other words, you might want to apply these stashed changes multiple times. To do this:

```sh
git stash apply
```

For a list of stashes:

```sh
git stash list
```

And to apply a specific stash from that list (e.g., stash@{3}):

```sh
git stash apply stash@{3}
```

### Checkout as CRLF, Commit as LF

```sh
git config --global core.autocrlf true
```
