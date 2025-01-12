---
layout: post
title: Why Git Merge Conflicts Keep Coming Back (And How I Solved It)
tags: en git gitlab
---
I recently faced a puzzling problem while rebasing and merging a branch on GitLab. The main causes were conflicting merge strategies and my limited understanding of Git rebase and GitLab's merge methods. This post is a reflection on the situation, to consolidate my knowledge and to share what I've learned about Git and GitLab.

# Background
I took over a GitLab project from a colleague who was its sole contributor. The project/repository is not a part of our regular projects and it has a different git strategy than what we usually have. In this setup, changes were made in a `development` branch, and then merged into a `staging` branch. When no development was ongoing, the two branches needed to be in sync.

During the handover I noticed that **while the two branches contained the exact same files, their git commit histories weren't identical**. I assumed this was a problem easy to fix so I didn’t confirm with my colleague what happened to the git history. There was no defined workflow for the project, and neither me and my colleague had set up the Git process for it.

After the handover, I became the only one contributing to this project. I'm highlighting this to explain that only I and the existing commit history could cause potential conflicts.

# The Problem
When I started to introduce changes in this repository, by committing on `development` and merging it to `staging`, I immediately encountered merge conflicts. Our typical workflow when merge conflict happens is to rebase source branch on target branch, so I did a rebase for development branch on staging:
```
git checkout <source branch>
git rebase <target branch>
```
Conflicts blocked the rebase, requiring edits to the conflicting commits to include the expected changes. When those conflicts are manually resolved, we continue the rebase via:
```
git add .
git rebase —-continue
```

During the rebase process I had to manually resolve several conflicts, and since I wasn't sure about what the old commits should include, I just accepted all former changes from `staging` branch as eventually `development` should be in sync with `staging`. And I have noticed, a bunch of commit had to be skipped (needed to run `git rebase —-skip` instead of  `git add . && git rebase -—continue`) after edited, because **they were empty and didn’t include any changes.**

After the rebase was done, I could merge `development` to `staging` on the GitLab web UI, using squash and merge. However, at the next time I wanted to merge `development` to `staging`, **the same merge conflicts happened again**, and I had to redo the same flow, which was frustrating. After this has happened several times, I unfortunately introduced a human mistake when manually resolving a merge conflict. It caused an hour of production downtime (which is another story) and because of this, I made up my mind to uncover the root of this Git rebase issue and get rid of the merge conflicts.

# Key Issues
There are several concerning behaviors that worth to pay attention to:
## Merge Conflicts Despite Identical Files
The two branches had identical content, but merge conflicts still exist. It means the Git commit history is having something fishy, and not related to the file content.

By the way, the commands I used to compare the two branches were:
```
git checkout development
git diff staging
```
The output was empty, which indicated the two branches were identical.

## Empty Commits During Rebase
The empty commits suggest that the same changes might have been applied in both branches but through different commits. 

## Recurring Merge Conflicts
The merge conflicts didn't get resolved after the rebase. This indicates that after my merge to `staging`, Git was still unable to track if branches are in sync.

# Understanding GitLab Merge
I realized I might have misunderstood Git merge and the GitLab's merge methods. Here's what I found:

There are three types of merge strategies and options one can choose for a GitLab merge request:

## Merge commit
GitLab's default merge behavior. It creates a merge commit when a branch is merged via a merge request. With the merge commit, Git is able to know that the two branches converged at that point. This equals to the command `git merge --no-ff <source branch>`.

GitLab also offers an option of a squash no-ff merge, which is probably not very easy to understand, as it has a somewhat complex process:
```
  git checkout `git merge-base <source> <target>`
  git merge --squash <source>
  git commit --no-edit
  SOURCE_SHA=`git rev-parse HEAD`
  git checkout <target>
  git merge --no-ff $SOURCE_SHA
```
It does the following things in order:
- moves HEAD to the common ancestor of both branches
- squashes all commits from the source branch into a single commit
- creates a environment variable with the newly created squashed commit hash
- finally, it merges the squashed commit into the target branch using a merge commit.

When `git merge --no-ff` simply adding all relevant commits from the source branch into target branch git history, the GitLab "squash no-ff merge" squashes the multiple commits to keep a clean git history on the target branch, and in the meanwhile Git still gets the merge event recorded.

## Fast-forward only 
This method keeps a clean commit history without merge commits. It cannot be performed if source branch is not updated, which is to say, if there's conflicts, the source branch has to be rebased on the target branch.

Notice that ff-only merge can also be squashed, this means a squash commit will present on the target branch after the merge, and the commit will contain all changes from the original commits, and will only be on the target branch (as the squashed commit isn't from any other existing branches). This behaves the same as command `git merge --squash <source branch>`.

**With the squashed fast-forward merge method, there is no merge commit created, and the squash commit only exists on the target branch, which causes the issue that no record for Git to know a merge has happened.** 

## Merge commit with Semi-linear history
This method is a combination of the above two: it works exactly like the merge commit method, but it can't be performed if source branch is not up-to-date as GitLab guards the merge. Every new commit has to be rebased before merging. This ensures the target branch always has a linear history where users can easily see where a branch starts and ends(merged).

(More explanation can be found in [GitLab's documentation](https://docs.gitlab.com/ee/user/project/merge_requests/methods/))

After sorting out, now it's very clear that **the fast-forward merges with squashed commits would cause the diverge of the source branches and target branches, if source branch remains after the merge**. When the source branch is deleted after the merge, usually this method won't cause problem, but in my case, the source branch is a long-living branch, so it's unsuitable to use this merge method.

# Assumed Causes
- The original owner of the project used fast-forward merge strategy, without any merge commit. They have also configured the project to use ff-only merge method by default.
- My colleague and I however, used the fast-forward merge strategy but with squash enabled. Neither of us was aware that this would cause branches sync issues.
- The diverging history of the two branches caused the issue I encountered. Even after rebase, diverged histories remained.

These causes can explain all the concerning behaviors I previously encountered. Since we used squash merge, the two branches became identical, however with different git commit histories. The history diverge could never be resolved when we kept using the fast-forward squash merge.

# Problem Solving
Though the root cause was a bit difficult to diagnose, the fix was pretty simple when knowing the reasons behind the diverging commits. I reset the `development` branch history and force pushed it:
```
git checkout development
git reset --hard origin/staging
git push --force
```
This rewrote the commit history of `development` to be the same as `staging`.
Notice that this should only be done when you are confident, as rewriting git history can no longer go back once pushed. Ensure all important changes are backed up before proceeding.
Alternatively, the following method utilizes --no-ff merge to realign history without rewriting:
```
git checkout staging
git merge --no-ff development
<resolve conflicts and commit the merge>
git push origin staging
```
This would introduce a merge commit on `staging` for Git to know when the merge happened, so it would also be a solution to the problem of branch diverging.

At the end, I disabled the squash merge option in the project configuration, to prevent the issue from happening again.
