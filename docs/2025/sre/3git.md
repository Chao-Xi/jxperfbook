
# 3 GIT TEST 

1. You are working on the main branch. HEAD in main and HEAD in the branch branchi point to the same commit.

What would be the consequences to the placement of the HEAD pointer if you rename branchi to branch2


```
HEAD would still be the same in main and in branch2 .
```


2. A submodule contains changes, including local changes and upstream changes from various contributors. You also have changes ready for a commit in the main project. **What is the best solution to ensure that both main project and submodule changes are available for all contributors to access**

```
git push -- recurse-submodules=on-demand
```

3. Which command renames branch1 to branch2?

```
git branch -m branch1 branch2
```

4. Git stops in the middle of a merge because a file was modified in both branches. How would you detect file caused the conflict? 

**The git status command lists the conflicting file as "Both modified."**


5. **Which statement best describes `git reset`'s hard and `mixed` flags?**

**hard will reset all changes in the staging area and working directory. **

**mixed will only reset the staging area.**

6. What flag can you use to preview which files git clean will delete before actually deleting anything?

```
-n 
```

7. **The sole difference between branches b1 and b2 is that b2 contains a file that b1 does not**. When you merge branch b1 into b2, it results in a conflict. What is the most likely explanation?

**The file was modified on b2 and deleted from b1.**

8. What command can result in a fast-forward?

**pull**

9. Which term defines Git repositories that are included as subdirectories in another repository?

**Submodules**

10. At the command `git log master`, Git displays an error. Which scenario caused the error?

```
The master branch does not exist or it does not have a commit.
```

11. What command allows you to delete the remote branch feature/test?

```
git push origin -d feature/test
```

12. Why would you consider the reflog a safety net?

**It maintains references to the old state of objects even if they are not connected to a branch.**


13. What does git show do?

**It shows blobs, trees, tags, and commits in a human-readable format.**

15. What is the purpose of the command `git branch -m` ?

**To rename or move a branch and the corresponding reflog**


16. You are working on a project where keeping a linear repository history is important. One of your teammates pushes their latest changes to the main branch, so you proceed to pull the latest changes using the following command:

`git pull origin main`


What is the problem with this approach?


**git pull will execute a merge by default, creating merge commits and damaging the linearity of the project's history.**


17. What is a rebase conflict?

**An error where a rebase branch contains objects that have different contents than their versions on the destination branch.**

18. Which situation is impossible in Git?

**A tag pointing to a branch**

19. Which two commands could you use to output all commits from the last two weeks?

`git log --after="2 weeks ago"` and `git log --after="14 days”ago”`


20. How does git bisect help you find bugs in your codebase?

**It performs a binary search that isolates the commit that introduced an error.**