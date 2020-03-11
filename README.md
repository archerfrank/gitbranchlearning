# Git branch learning

<https://github.com/pcottle/learnGitBranching> 

<https://learngitbranching.js.org/?demo> 

## Basic

1. Git branch
用来新建branch，如 git branch bugFix

2. Git checkout 
切换HEAD指针到对应的指针。
```
 git checkout bugFix --切换到branch bugFix。其实bugFix也是git tree上的一个指针。
 git checkout bugFix^ --切换到bugFix指针的前一个提交
 git checkout bugFix~3 ----切换到bugFix指针的前三个提交
 git checkout sha1  -- 切换到摸一个提交
```
3. git commit
* git commit -m 一个提交

4. git merge
* git merge。在 Git 中合并两个分支时会产生一个特殊的提交记录，它有两个父节点。翻译成自然语言相当于：“我要把这两个父节点本身及它们所有的祖先都包含进来。”  我们要把 bugFix 合并到 master 里. 在master branch上执行 **git merge bugFix**。 之后， 咱们再把 master 分支合并到 bugFix：**git checkout bugFix; git merge master** , 这样两个branch就合并了。因为 master 继承自 bugFix，Git 什么都不用做，只是简单地把 bugFix 移动到 master 所指向的那个提交记录。现在所有提交记录的颜色都一样了，这表明每一个分支都包含了代码库的所有修改！大功告成！

5. git rebase

* Rebase 实际上就是取出一系列的提交记录，“复制”它们，然后在另外一个地方逐个的放下去。
Rebase 的优势就是可以创造更线性的提交历史，这听上去有些难以理解。如果只允许使用 Rebase 的话，代码库的提交历史将会变得异常清晰。

* 我们想要把 bugFix 分支里的工作直接移到 master 分支上。移动以后会使得两个分支的功能看起来像是按顺序开发，但实际上它们是并行开发的。在bugFix上执行 **git rebase master** 现在唯一的问题就是 master 还没有更新，下面咱们就来更新它吧. 切换到master上，然后**git checkout master; git rebase bugFix**

下面的命令就不用checkout到bugFix了
```
git rebase master bugFix
```

## Advance

### 强制修改分支位置

我使用相对引用最多的就是移动分支。可以直接使用 -f 选项让分支指向另一个提交。例如:
```
git branch -f master HEAD~3
```
上面的命令会将 master 分支强制指向 HEAD 的第 3 级父提交。
```
git branch -f master C2
```
把master的指针指向C2这个提交。

## 撤销变更

在 Git 里撤销变更的方法很多。和提交一样，撤销变更由底层部分（暂存区的独立文件或者片段）和上层部分（变更到底是通过哪种方式被撤销的）组成。我们这个应用主要关注的是后者。

主要有两种方法用来撤销变更 —— 一是 git reset，还有就是 git revert。接下来咱们逐个进行讲解。

**git reset** 通过把分支记录回退几个提交记录来实现撤销改动。你可以将这想象成“改写历史”。git reset 向上移动分支，原来指向的提交记录就跟从来没有提交过一样。
```
git reset HEAD~1
```
Git 把 master 分支移回到 C1；现在我们的本地代码库根本就不知道有 C2 这个提交了。在reset后， C2 所做的变更还在，但是处于未加入暂存区状态。


虽然在你的本地分支中使用 git reset 很方便，但是这种“改写历史”的方法对大家一起使用的远程分支是无效的哦！

为了撤销更改并分享给别人，我们需要使用 **git revert**。
```
git revert head
```
在我们要撤销的提交记录后面居然多了一个新提交！这是因为新提交记录 C2' 引入了更改 —— 这些更改刚好是用来撤销 C2 这个提交的。也就是说 C2' 的状态与 C1 是相同的。

revert 之后就可以把你的更改推送到远程仓库与别人分享啦。

## 整理提交记录

本系列的第一个命令是 git cherry-pick, 命令形式为:

```
git cherry-pick <提交号>...
```
如果你想将一些提交复制到当前所在的位置（HEAD）下面的话， Cherry-pick 是最直接的方式了。我个人非常喜欢 cherry-pick，因为它特别简单。

这里有一个仓库, 我们想将 side 分支上的工作复制到 master 分支，你立刻想到了之前学过的 rebase 了吧？但是咱们还是看看 cherry-pick 有什么本领吧。

```
git cherry-pick C2 C4   
```
我们只需要提交记录 C2 和 C4，所以 Git 就将被它们抓过来放到当前分支下了。 就是这么简单! 如果head当前指向master， C2'和C4'就会被加到master上然后master向前移动到C4'

## 交互式的 rebase

当你知道你所需要的提交记录（并且还知道这些提交记录的哈希值）时, 用 cherry-pick 再好不过了 —— 没有比这更简单的方式了。

但是如果你不清楚你想要的提交记录的哈希值呢? 幸好 Git 帮你想到了这一点, 我们可以利用交互式的 rebase —— 如果你想从一系列的提交记录中找到想要的记录, 这就是最好的方法了.

交互式 rebase 指的是使用带参数 --interactive 的 rebase 命令, 简写为 -i

如果你在命令后增加了这个选项, Git 会打开一个 UI 界面并列出将要被复制到目标分支的备选提交记录，它还会显示每个提交记录的哈希值和提交说明，提交说明有助于你理解这个提交进行了哪些更改。

在实际使用时，所谓的 UI 窗口一般会在文本编辑器 —— 如 Vim —— 中打开一个文件。 考虑到课程的初衷，我弄了一个对话框来模拟这些操作。

```
git rebase -i head~4
```
上面的命令可以重新组织最近的4次提交， 这样可以组织自己的提交，方便日后跟踪。

## 本地栈式提交

来看一个在开发中经常会遇到的情况：我正在解决某个特别棘手的 Bug，为了便于调试而在代码中添加了一些调试命令并向控制台打印了一些信息。

这些调试和打印语句都在它们各自的提交记录里。最后我终于找到了造成这个 Bug 的根本原因，解决掉以后觉得沾沾自喜！

最后就差把 bugFix 分支里的工作合并回 master 分支了。你可以选择通过 fast-forward 快速合并到 master 分支上，但这样的话 master 分支就会包含我这些调试语句了。你肯定不想这样，应该还有更好的方式……

实际我们只要让 Git 复制解决问题的那一个提交记录就可以了。跟之前我们在“整理提交记录”中学到的一样，我们可以使用
```
git rebase -i
git cherry-pick
```
来达到目的。

## 提交的技巧

### 提交的技巧
接下来这种情况也是很常见的：你之前在 newImage 分支上进行了一次提交，然后又基于它创建了 caption 分支，然后又提交了一次。

此时你想对的某个以前的提交记录进行一些小小的调整。比如设计师想修改一下 newImage 中图片的分辨率，尽管那个提交记录并不是最新的了。

我们可以通过下面的方法来克服困难：

* 先用 git rebase -i 将提交重新排序，然后把我们想要修改的提交记录挪到最前
* 然后用 commit --amend 来进行一些小修改
* 接着再用 git rebase -i 来将他们调回原来的顺序
* 最后我们把 master 移到修改的最前端（用你自己喜欢的方法），就大功告成啦！
当然完成这个任务的方法不止上面提到的一种（我知道你在看 cherry-pick 啦），之后我们会多点关注这些技巧啦，但现在暂时只专注上面这种方法。 最后有必要说明一下目标状态中的那几个' —— 我们把这个提交移动了两次，每移动一次会产生一个 '；而 C2 上多出来的那个是我们在使用了 amend 参数提交时产生的，所以最终结果就是这样了。

也就是说，我在对比结果的时候只会对比提交树的结构，对于 ' 的数量上的不同，并不纳入对比范围内。只要你的 master 分支结构与目标结构相同，我就算你通过。

### 提交的技巧

正如你在上一关所见到的，我们可以使用 rebase -i 对提交记录进行重新排序。只要把我们想要的提交记录挪到最前端，我们就可以很轻松的用 --amend 修改它，然后把它们重新排成我们想要的顺序。

但这样做就唯一的问题就是要进行两次排序，而这有可能造成由 rebase 而导致的冲突。下面还是看看 git cherry-pick 是怎么做的吧。

**要在心里牢记 cherry-pick 可以将提交树上任何地方的提交记录取过来追加到 HEAD 上（只要不是 HEAD 上游的提交就没问题）。**

## Git Tags
相信通过前面课程的学习你已经发现了：分支很容易被人为移动，并且当有新的提交时，它也会移动。分支很容易被改变，大部分分支还只是临时的，并且还一直在变。

你可能会问了：有没有什么可以永远指向某个提交记录的标识呢，比如软件发布新的大版本，或者是修正一些重要的 Bug 或是增加了某些新特性，有没有比分支更好的可以永远指向这些提交的方法呢？

当然有了！Git 的 tag 就是干这个用的啊，它们可以（在某种程度上 —— 因为标签可以被删除后重新在另外一个位置创建同名的标签）永久地将某个特定的提交命名为里程碑，然后就可以像分支一样引用了。

更难得的是，它们并不会随着新的提交而移动。你也不能检出到某个标签上面进行修改提交，它就像是提交树上的一个锚点，标识了某个特定的位置。

咱们先建立一个标签，指向提交记录 C1，表示这是我们 1.0 版本。

```
git tag v1 C1
```
很容易吧！我们将这个标签命名为 v1，并且明确地让它指向提交记录 C1，如果你不指定提交记录，Git 会用 HEAD 所指向的位置。

## Git Describe

由于标签在代码库中起着“锚点”的作用，Git 还为此专门设计了一个命令用来描述离你最近的锚点（也就是标签），它就是 git describe！

Git Describe 能帮你在提交历史中移动了多次以后找到方向；当你用 git bisect（一个查找产生 Bug 的提交记录的指令）找到某个提交记录时，或者是当你坐在你那刚刚度假回来的同事的电脑前时， 可能会用到这个命令。

git describe 的​​语法是：
```
git describe <ref>
```
<ref> 可以是任何能被 Git 识别成提交记录的引用，如果你没有指定的话，Git 会以你目前所检出的位置（HEAD）。

## 多分支 rebase

下面的命令就不用checkout到bugFix了, 在rebase，可以直接把bugFix， rebase到master上。这其实还有一个找到共同祖先的过程。
```
git rebase master bugFix
```

## 选择父提交记录
操作符 ^ 与 ~ 符一样，后面也可以跟一个数字。

但是该操作符后面的数字与 ~ 后面的不同，并不是用来指定向上返回几代，而是指定合并提交记录的某个父提交。还记得前面提到过的一个合并提交有两个父提交吧，所以遇到这样的节点时该选择哪条路径就不是很清晰了。

Git 默认选择合并提交的“第一个”父提交，在操作符 ^ 后跟一个数字可以改变这一默认行为。

使用 ^ 和 ~ 可以自由地在提交树中移动
```
git checkout HEAD^2; git checkout HEAD~2;
git checkout HEAD^2~2;
```


# 远程仓库

## 远程分支

远程分支反映了远程仓库(在你上次和它通信时)的状态。这会有助于你理解本地的工作与公共工作的差别 —— 这是你与别人分享工作成果前至关重要的一步.

远程分支有一个特别的属性，在你检出时自动进入分离 HEAD 状态。Git 这么做是出于不能直接在这些分支上进行操作的原因, 你必须在别的地方完成你的工作, （更新了远程分支之后）再用远程分享你的工作成果。

你可能想问这些远程分支的前面的 o/ 是什么意思呢？好吧, 远程分支有一个命名规范 —— 它们的格式是:

<remote name>/<branch name>
因此，如果你看到一个名为 o/master 的分支，那么这个分支就叫 master，远程仓库的名称就是 o。
```
git checkout o/master; git commit;
```
Git 变成了分离 HEAD 状态，当添加新的提交时 o/master 也不会更新。这是因为 o/master 只有在远程仓库中相应的分支更新了以后才会更新。

## fetch

```
git fetch
```

就是这样了! C2,C3 被下载到了本地仓库，同时远程分支 o/master 也被更新，反映到了这一变化

git fetch 完成了仅有的但是很重要的两步:

1. 从远程仓库下载本地仓库中缺失的提交记录
2. 更新远程分支指针(如 o/master)

git fetch 并不会改变你本地仓库的状态。它不会更新你的 master 分支，也不会修改你磁盘上的文件。

## Pull
当远程分支中有新的提交时，你可以像合并本地分支那样来合并远程分支。也就是说就是你可以执行以下命令:

1. git cherry-pick o/master
2. git rebase o/master
3. git merge o/master

git pull 就是 git fetch 和 git merge <just-fetched-branch> 的缩写

## 偏离的工作

假设你周一克隆了一个仓库，然后开始研发某个新功能。到周五时，你新功能开发测试完毕，可以发布了。但是 —— 天啊！你的同事这周写了一堆代码，还改了许多你的功能中使用的 API，这些变动会导致你新开发的功能变得不可用。但是他们已经将那些提交推送到远程仓库了，因此你的工作就变成了基于项目旧版的代码，与远程仓库最新的代码不匹配了。

这种情况下, git push 就不知道该如何操作了。如果你执行 git push，Git 应该让远程仓库回到星期一那天的状态吗？还是直接在新代码的基础上添加你的代码，异或由于你的提交已经过时而直接忽略你的提交？

因为这情况（历史偏离）有许多的不确定性，Git 是不会允许你 push 变更的。实际上它会强制你先合并远程最新的代码，然后才能分享你的工作。

**最直接的方法就是通过 rebase 调整你的工作。**

```
git fetch; git rebase o/master; git push;
```

我们用 git fetch 更新了本地仓库中的远程分支，然后用 rebase 将我们的工作移动到最新的提交记录下，最后再用 git push 推送到远程仓库。

我们还可以使用 **merge**
```
git fetch; git merge o/master; git push;
```
我们用 git fetch 更新了本地仓库中的远程分支，然后合并了新变更到我们的本地分支（为了包含远程仓库的变更），最后我们用 git push 把工作推送到远程仓库.

当然 —— 前面已经介绍过 git pull 就是 fetch 和 merge 的简写，类似的 git pull --rebase 就是 fetch 和 rebase 的简写！

```
git pull --rebase; git push
git pull; git push
```

## 远程服务器拒绝!(Remote Rejected)

如果你是在一个大的合作团队中工作, 很可能是master被锁定了, 需要一些Pull Request流程来合并修改。如果你直接提交(commit)到本地master, 然后试图推送(push)修改, 你将会收到这样类似的信息:

! [远程服务器拒绝] master -> master (TF402455: 不允许推送(push)这个分支; 你必须使用pull request来更新这个分支.)

新建一个分支feature, 推送到远程服务器. 然后reset你的master分支和远程服务器保持一致, 否则下次你pull并且他人的提交和你冲突的时候就会有问题.

```
git checkout -b feature
git branch -f master origin/master
git push
```

## 合并特性分支

在开发社区里，有许多关于 merge 与 rebase 的讨论。以下是关于 rebase 的优缺点：

优点:

Rebase 使你的提交树变得很干净, 所有的提交都在一条线上
缺点:

Rebase 修改了提交树的历史
比如, 提交 C1 可以被 rebase 到 C3 之后。这看起来 C1 中的工作是在 C3 之后进行的，但实际上是在 C3 之前。

一些开发人员喜欢保留提交历史，因此更偏爱 merge。而其他人（比如我自己）可能更喜欢干净的提交树，于是偏爱 rebase。仁者见仁，智者见智。

## 远程跟踪

直接了当地讲，master 和 o/master 的关联关系就是由分支的“remote tracking”属性决定的。master 被设定为跟踪 o/master —— 这意味着为 master 分支指定了推送的目的地以及拉取后合并的目标。

你可能想知道 master 分支上这个属性是怎么被设定的，你并没有用任何命令指定过这个属性呀！好吧, 当你克隆仓库的时候, Git 就自动帮你把这个属性设置好了。

当你克隆时, Git 会为远程仓库中的每个分支在本地仓库中创建一个远程分支（比如 o/master）。然后再创建一个跟踪远程仓库中活动分支的本地分支，默认情况下这个本地分支会被命名为 master。

你可以让任意分支跟踪 o/master, 然后该分支会像 master 分支一样得到隐含的 push 目的地以及 merge 的目标。 这意味着你可以在分支 totallyNotMaster 上执行 git push，将工作推送到远程仓库的 master 分支上。

有两种方法设置这个属性，第一种就是通过远程分支检出一个新的分支，执行:
```
git checkout -b totallyNotMaster o/master
```
就可以创建一个名为 totallyNotMaster 的分支，它跟踪远程分支 o/master。

另一种设置远程追踪分支的方法就是使用：git branch -u 命令，执行：
```
git branch -u o/master foo
```
这样 foo 就会跟踪 o/master 了。如果当前就在 foo 分支上, 还可以省略 foo：
```
git branch -u o/master
```

## Git Push 的参数

Git 是通过当前检出分支的属性来确定远程仓库以及要 push 的目的地的。这是未指定参数时的行为，我们可以为 push 指定参数，语法是：
```
git push <remote> <place>

git push origin master
```
把这个命令翻译过来就是：

*切到本地仓库中的“master”分支，获取所有的提交，再到远程仓库“origin”中找到“master”分支，将远程仓库中没有的提交记录都添加上去，搞定之后告诉我。*


要同时为源和目的地指定 <place> 的话，只需要用冒号 : 将二者连起来就可以了：

```
git push origin <source>:<destination>
git push origin HEAD^:master
``` 

如果你要推送到的目的分支不存在会怎么样呢？没问题！Git 会在远程仓库中根据你提供的名称帮你创建这个分支.
```
git push origin master:newBranch
```

## Git fetch 的参数

git fetch 的参数和 git push 极其相似。他们的概念是相同的，只是方向相反罢了（因为现在你是下载，而非上传）

```
git fetch origin foo
```

Git 会到远程仓库的 foo 分支上，然后获取所有本地不存在的提交，放到本地的 o/foo 上。

```
git fetch origin foo^/bar
```
这个命令会把foo的上一个提交，fetch到本地，并把branch bar指到这个位置，而不是移动o/foo。

## 古怪的 <source>

Git 有两种关于 <source> 的用法是比较诡异的，即你可以在 git push 或 git fetch 时不指定任何 source，方法就是仅保留冒号和 destination 部分，source 部分留空。
```
git push origin :side
git fetch origin :bugFix
```

如果 push 空 <source> 到远程仓库会如何呢？它会删除远程仓库中的分支！
如果 fetch 空 <source> 到本地，会在本地创建一个新分支。

## Git pull 参数
```
git pull origin foo 
```
相当于：
```
git fetch origin foo; git merge o/foo
```

```
git pull origin bar~1:bugFix 
```
相当于：
```
git fetch origin bar~1:bugFix; git merge bugFix
``

