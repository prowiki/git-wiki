# Git Quick Search

- [Git Quick Search](#git-quick-search)
  * [Setup](#setup)
  * [Basic](#basic)
  * [Branch](#branch)
  * [Log](#log)
  * [Diff](#diff)
  * [Stash](#stash)
  * [Fetch vs. Pull](#fetch-vs-pull)
  * [Rebase](#rebase)
  * [Cherry-pick](#cherry-pick)
  * [Revert](#revert)
  * [Tag](#tag)
  * [Reflog](#reflog)

## Setup

1. Config git.

```bash
git config --global user.username "MY_NAME"
git config --global user.email "MY_NAME@example.com"
```

or edit the `.gitconfig` file directly:
```bash
git config --global --edit
```

2. Config remote for a repo.

```bash
git remote add origin <git-url> # hereby, we use origin as the name of remote repo
git remote set-url origin <git-url> # change remote repo
git remote -v # show remote info
```

3. Config `.gitignore`. You can check out all aliases in `~/.gitconfig`.

```bash
*.pyc # ignore by suffix
Debug/ # ignore all subdirectories(including current directory) named Debug
/Debug # ignore the sub-directory named Debug in current directory
**/bin/Debug # ** wildcard can match any level of sub-directories 
```

4. Config alias.

```bash
git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
```

5. Config default editor.

```bash
git config --global core.editor vim
```

6. Config bash prompt for git.

Here's my favorite theme: [link](https://gist.github.com/justintv/168835#gistcomment-1717504).


## Basic

1. Add files or directories from `Working Directory` to `Staging Area`. Add files or directories from `Staging Area` to `Local Repo`.

```bash
git add <file/dir>
git commit -m "COMMIT MESSAGE"
git commit --amend # change commit message of the last commit
```

However, it's not recommended to use `git commit -m "xxx"` because it provides limited description of what has been changed. Use `git commit` instead and following the example below to fill the prompt editor.

```
Capitalized, short (50 chars or less) summary

More detailed explanatory text, if necessary.  Wrap it to about 72
characters or so.  In some contexts, the first line is treated as the
subject of an email and the rest of the text as the body.  The blank
line separating the summary from the body is critical (unless you omit
the body entirely); tools like rebase can get confused if you run the
two together.

Write your commit message in the imperative: "Fix bug" and not "Fixed bug"
 or "Fixes bug."  This convention matches up with commit messages generated
by commands like git merge and git revert.

Further paragraphs come after blank lines.

- Bullet points are okay, too
- Typically a hyphen or asterisk is used for the bullet, followed by a single space, with blank lines in between, but conventions vary here
- Use a hanging indent
```

2. Show repo status.

```bash
git status
```

3. Show commit info.

```bash
git log
git lg # with alias config
```

4. Switch branch.

```bash
git checkout <branch>
git checkout -b <new-branch> # create a new branch and switch
```

5. Merge a certain branch to current branch. **Note that with `fast-forward`, git will simply move the `HEAD` pointer of your current branch to the `HEAD` of another branch.**

```bash
git merge <another-branch> # merge another branch to current branch.
git merge --no-ff -m "commit message" <another-branch> # merge disallowing fast-forward 
```

6. Push current local branch to remote.

```bash
git push -u <remote-name> <local-branch> # run for the first time to bind the upstream
git push # push according to the binding
```

7. Discard changes in `Working Directory`. Note staged changes will not be discard.

```bash
git checkout -- <file/dir>
```

8. Discard staged changes to `Working Directory`. It will not affect changes in `Working Directory`.

```bash
git reset HEAD <file/dir>
```

9. Stop tracking.

```bash
git rm <file/dir>
```

10. **[DANGER]** Go to a certain version by moving the `HEAD` pointer to the corresponding commit. Any changes in `Working Directory` and `Staging Area` will be discarded.

```bash
git reset --hard HEAD^ # last version
git reset --hard HEAD~10 # the 10th last version
```

## Branch

1. Show local branches and current branch.

```bash
git branch
git branch -av # show all branches in verbose
```

2. Delete local branch.

```bash
git branch -d <branch> # the branch should be fully merged
git branch -D <branch> # delete the branch anyway
```

## Log

1. Show commit info, and make the output concise.

```bash
git log --pretty=oneline
```

2. Show logs in graph view.

```bash
git log --graph --pretty=oneline --abbrev-commit
```

3. Show commit history of a certain file.

```bash
git log -p <file/dir>
```

## Diff

1. Show unstaged updates.

```bash
git diff <file/dir>
```

2. Show uncommitted updated.

```bash
git diff -- HEAD <file/dir>
```

3. Show the difference between staged code and `HEAD`.

```bash
git diff --staged
```

## Stash

1. Stash all uncommitted(including `working directory` and `staging area`) changes. The *stash area* is organized as a **stack**.

```bash
git stash # stash
git stash push "stash message" # stash with specified message
git stash list # show all stashed version
git stash pop # restore the latest stashed version, then pop out the version
git shash apply # restore the latest stashed version, without popping out the version
git stash clear # clear all stashed versions
```

2. Restore a specific stashed version.

```bash
git stash pop stash@{1} # restore the second last stashed version
git stash apply stash@{1} # restore the second last stashed version
```

## Fetch vs. Pull

1. Fetch remote branch. **Newly fetched commit is pointed by `FETCH_HEAD`**.

```bash
git fetch # fetch updates of remote branch binding to current local branch
git log -p FETCH_HEAD # show fetched commits
git merge FETCH_HEAD # merge
```

1. Pull is a faster but simpler way of `fetch+merge`. In most cases, we use `pull` instead of `fetch`.

```bash
git pull # pull updates from binding remote branch
```

## Rebase

1. **[DANGER]** Apply another branch as the *base* of current branch. It's useful when you want to merge current branch to another, while another branch has changed since you create this new branch from it. However, this will change the commit history, so it might involve potential risks.

```bash
git rebase <branch>
```

2. **[DANGER]** Modify commit history.

```bash
git rebase -i HEAD~2 # modify the last 3 commit, interactively
git rebase --edit-todo # when you encounter some errors during the changes
git rebase --continue # after you fix the errors
git rebase --abort # abort the rebase
```

Also, you can use `git status` to show some help.

## Cherry-pick

1. Apply the change of a certain commit from other branches to current branch. This will automatically make a new commit with the same message.

```bash
git cherry-pick <commit-id>
```

## Revert

1. Revert a commit by creating a new commit.

```bash
git revert <commit-id>
```

2. Revert a change against a specific file.

Assuming the commit history is `commit-1 - commit-2` now and you want to revert the change against the specific file in `commit-2`. You can either do `git checkout commit-1 -- file` or `git checkout commit-2~1 -- file`.

## Tag

1. Tag is just a pointer to commit id.

```bash
git tag <tag> # create a tag on current commit
git tag <tag> <commit-id> # create a tag for a certain commit
git tag -a <tag> -m <tag-message> # create a tag with message
git tag # show all tags, in alphabetic order
git show <tag> # show tag message (if exist) and commit message
```

2. Delete a tag.

```bash
git tag -d <tag> # delete a local tag
git push origin :refs/tags/<tag> # delete a remote tag (delete local tag first)
```

3. Push a tag. Github will create a release based on your tags automatically.

```bash
git push origin <tag> # push a certain tag
git push origin --tags # push all tags
```

## Reflog

1. Show reference logs of current repo. Very useful when you what to check out the changes of `HEAD`.

```bash
git reflog
```
