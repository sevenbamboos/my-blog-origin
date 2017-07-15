---
title: Feature Branch Workflow
date: 2017-07-15 15:09:56
categories: Tool 
tags:
- Git
---

I recommend to use Feature Branch Workflow(FBW) as a daily guideline of source code check-in. [Read more why FBW is a good workflow][1]

It starts from a new branch (either for a new feature or bug-fixing):

```
git checkout -b <feature-branch>

// then make change locally
git add .
git commit -a
```

<!-- more -->

Suppose someone else checks in something important for your feature in the remote develop branch (since developers rarely work on master directly), then it's necessary to pull down the change and merge it into the local feature branch.

```
git checkout develop
git pull
```

At this moment, there are two options to do the merge. One is `git merge`, which creates a "merge commit" in the feature branch.

```
git checkout <feature-branch>
git merge develop
```

It's a safe operation though it can make feature branch's history hard to understand for others. So another way is to use `git rebase`, which cuts the feature branch and pastes it after the last commit on develop to keep a linear history for feature branch. [Read more on the comparison of merge and rebase and when NOT to do rebase][2]

```
git checkout <feature-branch>
// -i flag provides an interactive editing of commit message 
git rebase -i --autosquash develop

// do conflict merge and
git add <file1> <file2> ...
git rebase --continue

// and publish the branch
git push -f
```

[Read more on how to do conflict merge][3]
[Read more on autosquash and how to commit fixup][4]

Just keep in mind that don't do rebase on a public branch that someone else could work on. Once the feature is done and there is no failed tests, make a pull request (via github's website or like). After the request has been accepted and merged and closed, clean up local feature branch.

[Another article about the relation of master/develop/feature/hotfix branch][5]

[1]: https://www.atlassian.com/git/tutorials/comparing-workflows#feature-branch-workflow
[2]: https://www.atlassian.com/git/tutorials/merging-vs-rebasing
[3]: https://help.github.com/articles/resolving-a-merge-conflict-using-the-command-line/ 
[4]: https://robots.thoughtbot.com/autosquashing-git-commits 
[5]: http://nvie.com/posts/a-successful-git-branching-model/ 
