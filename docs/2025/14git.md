# Git Scenario-Based Questions 

## Basic Git Scenarios

#### **1 Accidentally committed sensitive credentials in Git. How do you remove them from history?**

```
Use git filter-branch (deprecated) or git filter-repo, then force push.
```

#### **2 How do you undo your last commit without losing your changes?**

• Use `git reset --soft HEAD~1`


#### You need to discard local changes in a specific file. How would you do it?

- `git checkout -- <file>`

- `git restore <file>`

#### What happens if you run git reset --hard HEAD~1?


**It removes the last commit and all changes permanently**.

#### How can you recover a deleted branch?

Use git reflog to find the commit and `git checkout -b <branch_name> <commit_hash>`.

Use `git checkout <correct_branch>`, then `git cherry-pick <commit_hash>`.


#### How do you revert a specific commit without affecting other changes?

```
git revert <commit_hash>.
```

#### You cloned a repository but forgot to specify `--recurse-submodules`. How do you initialize submodules afterward?

```
git submodule update --init --recursive.
```

#### How do you rebase a feature branch onto the main branch?

```
git checkout feature-branch 

git rebase main
```

#### What is the difference between git merge and git rebase?


**git merge creates a new merge commit, while git rebase moves commits on top of the latest branch.**

#### How do you delete a remote branch that is no longer needed?

```
git push origin --delete <branch_name>
```

#### Your main branch is ahead of your local branch. How do you update your branch without affecting your commits?

`git pull --rebase origin main`

What happens if you try to merge a branch that has already been merged?


#### How do you force push a commit while ensuring you don't overwrite other team members' work

`git push --force-with-lease`

#### You need to contribute to an open-source project. What steps will you take to fork and create a pull request?

Fork repo, clone it, create a feature branch, push changes, and open a PR.

#### How do you revert a pushed commit that is causing issues in production?

Use `git revert <commit_hash>`, then push.


## Advanced Git Scenarios

#### You need to squash multiple commits into one before pushing. How do you do that?

Use `git rebase -i HEAD~<n>`, then choose squash.

#### How do you rewrite commit history to change a commit message from a few commits ago?

Use `git rebase -i HEAD~<n>` and modify the commit message.

#### Explain how git cherry-pick works and when you would use it.

It applies a specific commit from one branch to another. Useful for selective fixes.


#### You have diverged from the remote branch, and your local commits conflict with remote changes. How do you fix this?

```
git pull --rebase and resolve conflicts
```

#### How do you temporarily save changes that are not ready to be committed?

• Use git stash.


#### Your Git repository has become very large. How do you reduce its size without affecting its history?

Use git gc or git filter-repo.

#### Explain the difference between git revert and git reset. When would you use each?

- `git revert` undoes a commit safely, 

- `git reset` removes commits. 

- Use revert for public branches and reset for local changes.