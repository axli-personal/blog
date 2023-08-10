---
title: "Git"
date: 2022-08-06T16:00:00+08:00
---

Git作为开发中最常用的工具, 本次总结一下**stash**和**cherry-pick**.

<!--more-->

## 切换分支

切换分支时, 如果存在未提交的更改, 那么更改需要和目标分支兼容.

发生冲突时, 可以选择**stash**或**commit**.

## stash

```shell
git stash list
git stash push [-m "msg"]
git stash <apply/pop/drop> [n] # 列表中的编号, 0 (default).
```

## [cherry-pick](https://www.git-tower.com/learn/git/faq/cherry-pick)

从其他分支上挑选commit提交到当前分支, 一般用于hotfix.

```shell
git cherry-pick [--no-commit] <commit> # 可选择不提交而只是在工作区进行更改.
```
