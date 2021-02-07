# git rebase 的使用

![](../.gitbook/assets/image%20%2820%29.png)

git作为现在热门的代码版本管理工具，在现在各大公司已被广泛应用。但你对git的认识和熟悉程度有多深入呢。 鄙人认为git是值得深入探索和研究的，而不是停留在简单的pull、push的使用中。本文主要结合我在项目中的经验详细对 `git rebase`（变基）阐述一下。

## 认识git rebase

rebase提供了一种在你合并代码到主干时一种有别于merge的方式。我们都知道我们在直接使用`git merge`合并代码时会出现多个分叉，并且有可能再你落后他人分支时会保留一个无意义的合并记录，这使得代码记录很乱，git 图表则会杂乱无章。而正好`git rebase`则可提供一条完美的线性提交记录。

![](../.gitbook/assets/image%20%2818%29.png)

## 用法

git rebase有两种用法用得比较多。 一、用来改写提交到版本库的commit 二、用来在合并代码到主干时，将基底提前，合并后使得主线保持一条干净完美的线性，便于代码审阅人员review code。 基于第一种情况，假设我们当前在feature/develop当我们在终端执行

```c
git rebase -i 

```

此时终端会唤起vim界面，显示的是最近提交的commt历史记录，注意：前开后闭即是（chose commit id, last commit id\]其实你可通过键入i 更改commit历史，我们可将pick改为squash或者fixup,然后键入:wq保存退出vim编辑器。然后你在`git log`查看历史记录，则会发现合并了提交，并且会出现一个新的commit id。记住，这会丢弃合并区间的commit id，重新生成一个新的commit id。熟悉git的朋友可能发觉这很像`git commit --amend` ，的确这起到的作用和其大同小异。 基于第二种情况，假设我们当前在feature/develop，且我们的分支的base已经落后于master我们想要在合并master时先在终端执行

```text
git rebase master

```

![](../.gitbook/assets/image%20%2819%29.png)

这时会开始git rebase的进程，一旦遇到出现冲突，rebase进程将被终止，我们此时可以选择解决冲突继续rebase，也可终止这次rebase操作。 前者，我们需要在解决冲突后在终端执行

```text
git add 
git rebase --continue

```

![](../.gitbook/assets/image%20%2817%29.png)



继续我们此次rebase进程，直到完成所有文件的rebase。我们则在切换到master执行merge工作，

```text
git checkout master
git merge feature/develop

```

然后我们需要强推到master

```text
git push -f origin master

```

![](../.gitbook/assets/image%20%2815%29.png)

而此时也是fast-forward模式。来看一下rebase之后的log graph，是不是看起来很美呢？

![](../.gitbook/assets/image%20%2816%29.png)

后者的话我们可以执行

```text
git rebase --abort
复制代码
```

来终止这次rebase进程，此时将回到我们rebase前的状态。

## 不用force push应遵守

在push到远程master时，

1. 先checkout到本地master分支上fetch origin/master
2. 再在checkout到feature分支上rebase master分支
3. 再在checkout到master分支merge feature分支
4. 最后git push到远程master

## 风险

当在rebase公共分支时要特别注意，因为这极大可能给你的团队带来灾难性的后果。因为当你rebase多人一起开发的公共分支时，执行force push时，因为会改变commit id，则当你同事在merge代码则会出现冲突，可能会导致代码的丢失，所以我们要特别注意这一点。

**永远不要衍合那些已经推送到公共仓库的更新。** 如果你遵循这条金科玉律，就不会出差错。否则，人民群众会仇恨你，你的朋友和家人也会嘲笑你，唾弃你。

## 后记

总得来说，git rebase是个很nice的用来管理git仓库的命令，适用于团队多人开发时，而且有review code需要时使用。放心，虽然存在风险，但我们有`git reflog`来解决这些灾难性操作。

