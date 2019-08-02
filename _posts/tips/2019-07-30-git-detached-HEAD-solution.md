---
layout: post
title: git detached HEAD解决方案 
categories: [Tips]
description: 如何解决git detached HEAD的方法
keywords: git, detached
---

# 问题

git branch显示detach是一种特殊的状态，表明目前的HEAD指针指向一个具体的commit id而不是一个分支，是一种较危险状态，是一个临时的状态，一旦丢弃，似乎就不
好找回了。这个出现在repo download-topic时会出现这种detach的情况。


# 解决方法

解决方法很暴力，就是从这个临时状态新建一个分支，然后换到这个新建的分支tmp上，此时head指针指向新建的tmp分支，临时的修改也在新建的tmp的分支上。

```shell
git branch tmp
git checkout tmp
```

然后，从tmp分支切换回正常分支dev，将tmp分支merge进入dev分支，如果出现conflict，处理之。最后删除tmp分支，happy ending！

```shell
git checkout dev
git merge tmp
git branch -d tmp
```
