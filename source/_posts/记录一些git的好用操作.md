---
title: 记录一些git好用操作
date: 2021-09-09 23:05:24
tags: 
- git
categories: 
- CS
- 杂
---

因为好久没写了，又懒得动脑子写新东西，就打算整理一些工作中常用的git命令。
其实也是因为9月就要过去了，但我啥文章也没写过。。。

&emsp;
**<h1 id="menu-git">目录</h1>** 
    **[1.清空所有没有加入暂存区的修改](#git1)**
    **[2.清空已经加入暂存区的修改](#git2)**
    **[3.改东西改到一半临时切分支](#git3)**
    **[4.配置命令行代理](#git4)**
    **[5.有几个分支，只想合并某个commit](#git5)**
    **[6.拉取本地没有的远程分支](#git6)**
    **[7.将本地某分支强推到到远程另外一个指定分支](#git7)**
    **[8.本地切来切去干了一堆无用功想撤回](#git8)**
    **[9.我只想回滚某个文件](#git9)**
    **[10.虽然没有冲突，但我想手动合](#git10)**
    **[11.git的global config不小心设置了两个相同的key，想删删不掉](#git11)**
<br/>

**<p id="git1">1.清空所有没有加入暂存区的修改</p>** 

一般可以用`git checkout .`作为后悔药，但是如果有新增或删除文件就需要下面这个命令了。

可以放弃所有修改、新增、删除文件

```bash
    git checkout . && git clean -df
```
**[回到目录](#menu-git)**
&emsp;
**<p id="git2">2.清空已经加入暂存区的修改</p>** 

如果已经不小心用了`git add .`把修改加入了暂存区，但是又中途不想要这个修改了，可以如下操作。
这个做法可能比较邪道，但是能用。

```bash
    git add .
    git stash
```

至于有没有强迫症，要不要删掉这个stash的内容就看自己习惯了。
**[回到目录](#menu-git)**
&emsp;
**<p id="git3">3.改东西改到一半临时切分支</p>** 

经常遇到在某个功能分支上开发到一半被临时叫去主分支修bug，代码写了一半又因为各种原因不想提交，就可以用下面的命令暂存修改。

首先在当前分支输入如下命令暂存修改
```bash
    git add .
    git stash
```

然后切出去改东西，等全部操作完了再切到要把暂存内容放出来的分支，使用如下命令把暂存的东西放出来。
```bash
    git stash pop
```

需要注意的是，用stash存的一个个内容可以理解成一个栈，先进后出。

也就是说，如果有多个分支都做了stash这个操作来暂存内容，那么你直接使用`git stash pop`放出暂存内容的时候，遵循先进后出的规则，放出的一定是你最后那次暂存的东西，而且也会自动从这个暂存栈删除这次暂存。但有的时候我们又希望跳着来。就可以用下面这个命令先查看这个暂存栈里的暂存记录。

```bash
    git stash list

    stash@{0}: WIP on branch1: ffb8e1b Merge pull request #202 from branch2
    stash@{1}: WIP on branch2: fc36bd0 Merge pull request #171 from branch
    stash@{2}: WIP on branch3: 404474c commit message
```

根据提示内容找到想要放出的暂存记录，使用如下命令放出暂存，不过使用下面这个命令是不会从暂存栈删除这条暂存记录的。

```bash
    git stash apply stash@{1}
```
**[回到目录](#menu-git)**
&emsp;
**<p id="git4">4.配置命令行代理</p>** 

其实这个命令自从github版本更新以后已经没什么意义了但是还是写下。

由于众所周知的原因，在国内如果是使用https的地址来git clone仓库到本地，比如这种地址`https://github.com/用户名/仓库名.git`，那么在使用git push时经常会遇到网络问题。
以前用windows系统的时候把代理配置手动改成全局就可以了，但是换了mac之后好像没啥用。后来才知道mac的命令行需要另外配置代理命令才能走代理push。实际的端口号需要找自己的ssr配置来看。我这个应该算是比较常见的配置。命令行回车一下再push就畅通无阻了。

```bash
    export http_proxy=socks5://127.0.0.1:1086;
    export https_proxy=socks5://127.0.0.1:1086;
```

但是这个命令自从上次github更新之后就没意义了，gayhub现在为了安全问题好像需要配置一堆东西才能使用https来push代码，因为我过于讨厌配置这件事，就直接把https改成ssh了。~~早知今日何必当初~~

下面这个是把原本的https换成ssh的命令。

```bash
    git remote set-url origin （ssh-url）
```
**[回到目录](#menu-git)**
&emsp;
**<p id="git5">5.有几个分支，只想合并某个commit</p>** 
实际场景中经常有基于某个分支开出的不同分支来应付不同项目的需求，有时候某个分支上更新的某个功能另外一个分支也需要，并且不希望吧这个分支上除了新功能以外的内容合过去，这个时候就可以用cherry-pick提交某个指定的commit。

```bash
    git cherry-pick  要合过来的分支名 
    //写分支名会合并这个分支最新的一次commit
    //也可以写某个commit的hash值，如果是commit的hash值那么只会合并这个指定的commit
    git log //可以用来查看当前分支的commit历史，用来查询commit的hash值
```

如果cherry-pick遇到了冲突

```bash
    // 先修改冲突
    git status //查看修改
    git add . //添加修改
    git cherry-pick --continue //继续cherry-pick
```
**[回到目录](#menu-git)**
&emsp;
**<p id="git6">6.拉取本地没有的远程分支</p>** 
经常有多人同时开发，然后需要拉取别人已经提交的开发好的分支进行修改。

需要先`git fetch --all`拉取远程所有分支。
然后使用`git checkout 要拉取的分支名`切到想拉的分支上，这时候本地已经是远程的那个分支了。

~~我怎么越写越敷衍了~~
&emsp;
**<p id="git7">7.将本地某分支强推到到远程另外一个指定分支</p>** 
当各种奇怪操作导致了无可救药的问题时，就只能强推了。

一般情况下分支之间不会有太大不同，可以把本地能用的分支强推到线上的这个分支上，再用pr抢救下。但。。。架不住妖魔鬼怪的操作导致主分支完全不想要了，这个时候可以偷懒的用以下这个危险操作。

```bash
    git push origin 本地分支名:想推送的远程分支名 -f 
```
**[回到目录](#menu-git)**
&emsp;
**<p id="git8">8.本地切来切去干了一堆无用功想撤回</p>** 
输入`git reflog`查看时间顺序下的所有操作,大概长下面这样
```bash
    1a8bebe (HEAD -> develop, origin/develop) HEAD@{0}: commit: fix: 🐛 change avatar
    a09ce5a HEAD@{1}: commit: feat: 🎸 add something
    f6b66b7 HEAD@{2}: commit: fix: 🎸 fix bug
```
找到想要回滚的时间点，比如我想回到`commit: feat: 🎸 add something`这个commit,对应的是`HEAD@{1}` 
```bash
    git reset --hard HEAD@{1}
```
这个时候所有的内容都会彻底回到这个commit的版本，新发生的变更将会丢失。需要注意的是，回滚的版本只能是已经提交了commit，如果啥也没提交，那reflog后啥也没有，那你也回不去了。所以记得勤提交。

这个操作配合`git push origin 想回滚的分支名 -f`就可以实现远程分支的回滚。

不过hard还是要慎用，虽然后悔药有用，但是你放弃的那些commit也不见了。~~就像脑子里进的那些水~~
**[回到目录](#menu-git)**
&emsp;
**<p id="git9">9.我只想回滚某个文件</p>** 
这个也挺常见的，有时候改了一大堆，发现某个文件的某个版本才是对的，就只想回滚这个文件。
首先找到正确版本的文件所在的commit的hash值。
```bash
    git log ./README.md //想修改的文件路径
    
    commit hash值1 (HEAD -> develop)
    Merge: 12857b7 8fc2e03
    Author: icey0126 
    Date:   Tue Jun 29 16:01:41 2021 +0800

        fix: 🐛 fix bug

    commit hash值2
    Author: icey0126 
    Date:   Tue Jun 29 15:55:41 2021 +0800

        fix: 🐛 fix bug2
```
找到了`fix bug2`这个commit的hash值。

```bash
    git checkout hash值2 ./README.md //利用hash回滚特定文件，注意，这里为了方便操作，使用hash的前六位就可以
```

回滚后注意需要提交。
```bash
    git commit -m '回滚README.md' 
```
**[回到目录](#menu-git)**
&emsp;
**<p id="git10">10.虽然没有冲突，但我想手动合</p>** 
怎么会有这么奇怪的要求。~~还真有~~
在某个开发分支，想合并主分支时，虽然并没有冲突，但是想手动确认修改。可以配置如下命令。

```bash
    git merge --no-commit --no-ff 要合进来的分支名
```
但这个命令有时候会不生效，我也不知道原因。~~我只是想凑个10~~
**[回到目录](#menu-git)**
&emsp;
**<p id="git11">11.git的global config不小心设置了两个相同的key，想删删不掉</p>** 
突然想起来的一个比较常见的问题，有时候用`git config --global key value`设置全局配置时会不小心给同一个key设置了不同的value。

本来吧，用`git config --global --unset key`就可以删掉这个全局配置了，但是出现了同一个key多个value的时候就会报错不让你这么操作。

先用`git config --global -l`看看自己做了什么（看看全局配置了配了哪些参数）。

然后可以用正规的办法删掉这个key对应的所有value
```bash
    git config --unset -all key
```

或者用不正规的办法，把这个key对应的值全部替换成我们设定的值。
```bash
    git config --global --replace-all key "随便给个value"
```

由于git config的机制，只会保留一个相同的键值对，就可以通过正常的删除单个的操作来删除了。
**[回到目录](#menu-git)**