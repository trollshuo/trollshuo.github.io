---
title: "如何从“ GitHub贡献列表”中移除贡献者"
date: 2021-08-31T20:43:57+08:00
showDate: true
draft: false
tags: [GitHub]
---

哎哎哎，GitHub贡献列表怎么就莫名其妙多出来一个贡献者？翻看了提交记录，原来是自己的锅？某次提交时忘记使用`git config`来重新设置名字和电子邮件地址？[能不能给我台时光机啊](https://ohshitgit.com/)? 啊不对，能不能从“ GitHub贡献列表”中移除贡献者啊？

<!--more--> 

对于GitHub而言，贡献者其实就是版本库中任何有提交记录的用户。

换句话说，一旦这些用户在你的版本库历史中有提交记录，就代表着他们的代码行都是由他们添加的。即使删除了他们所有的代码行，他们仍然会显示为贡献者。

付出惨痛代价之后，终于含泪承认了以上事实。

那是不是没救了？ 莫慌，这篇文章或许会有帮助。

接下来，就是最最重要的事：

**请使用 `git config` 重新设置好正确的名字和电子邮件地址**

## 全局更改名字和电子邮件地址

先缓一缓，事情也许并没有那么糟糕？如果移除贡献者不是必须的，能接受只更改提交记录里的名字和电子邮件地址的话，那么请往下看。

查文档后发现，[filter- branch](https://git-scm.com/book/en/v2/Git-Tools-Rewriting-History)命令尽管不能移除贡献者，用处可一点不小。

**任何情况下，都可以通过 filter-branch 来一次性修改多个提交中的邮箱地址。**

不过由于这个命令只能修改属于自己的电子邮件地址，所以应该使用 --commit-filter：

修改用户作者信息
```

  git filter-branch --commit-filter '
        if [ "$GIT_AUTHOR_EMAIL" = "your-old-email@localhost" ];
        then
                GIT_AUTHOR_NAME="Your Correct Name";
                GIT_AUTHOR_EMAIL="your-correct-email@example.com";
                git commit-tree "$@";
        else
                git commit-tree "$@";
        fi' HEAD

```

修改用户提交者信息
```

  git filter-branch  -f --commit-filter '
        if [ "$GIT_COMMITTER_EMAIL" = "your-old-email@localhost" ];
        then
                GIT_COMMITTER_NAME="Your Correct Name";
                GIT_COMMITTER_EMAIL="your-correct-email@example.com";
                git commit-tree "$@";
        else
                git commit-tree "$@";
        fi' HEAD

```

这两个命令会遍历并重写每一个提交来包含你的新名字和新邮箱地址。 因为提交包含了它们父提交的 SHA-1 校验和，这两个命令会修改你的历史中的每一个提交的 SHA-1 校验和， 而不仅仅只是那些匹配邮箱地址的提交。

## [无论如何，我就是要移除贡献者](https://stackoverflow.com/questions/44563131/removing-contributor-from-github-com)

如果不管付出什么惨痛代价，好吧，那么开始之前，请心中大喊三遍：

> **备份不做，十恶不赦**

如果你是版本库的所有者，可以使用版本库的重命名功能。恭喜你，请往下看。

这个方法就是创建-重命名-删除原版本库。本质上就是创建原版本库的副本，接着过滤掉某个贡献者的提交，再把两个版本库的名字互相交换，这样就得到了一个全新的版本库。

步骤如下：
1. 在GitHub上创建一个全新的`copy`版本库。
2.  从`original`版本库中复制并挑选只包含想要展示的贡献者的提交内容。
3.  将`original`版本库重命名为to_be_deleted，将`copy`版本库重命名为`original`版本库。
   
如何挑选`original`版本库只包含想要展示的贡献者的提交内容：
```
git remote add localrepository https://github.com/your-git-account/original.git
git pull localrepository
git cherry-pick <commit>
git push 
```
其中，localrepository是远程`original`版本库的本地名称，我们将从该版本库拉取提交。

## 小孩才做选择，我全都要

那么能不能把这两个方法结合起来了？当然可以。

如何做呢？把以上的两个方法按照文章顺序执行即可。

还记得如何全局更改名字和电子邮件地址吗？使用那两条filter-branch 命令来修改提交中的名字和电子邮箱地址。更改过后的版本库，与我们想要的版本库是一致的。

接下来，在操作创建-重命名-删除原版本库过程中，我们可以直接复制`original`版本库，而无需挑选提交内容了。

更改后的步骤二内容：
```
git remote add localrepository https://github.com/your-git-account/original.git
git pull localrepository
git push 
```

## 有没有更好的方法

很遗憾，并没有。

或许能等到GitHub开放移除贡献者功能的那一天？大概率也没有。

当然啦，如果有更好的方法，希望能在评论区告诉我。