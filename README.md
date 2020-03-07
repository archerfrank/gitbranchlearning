# Git branch learning

https://github.com/pcottle/learnGitBranching 
https://learngitbranching.js.org/?demo 

## Basic

1. Git branch
用来新建branch，如 git branch bugFix

2. Git checkout 
切换HEAD指针到对应的指针。
* git checkout bugFix --切换到branch bugFix。其实bugFix也是git tree上的一个指针。
* git checkout bugFix^ --切换到bugFix指针的前一个提交
* git checkout bugFix~3 ----切换到bugFix指针的前三个提交
* git checkout sha1  -- 切换到摸一个提交

3. git commit
* git commit -m 一个提交

4. git merge
* git merge。在 Git 中合并两个分支时会产生一个特殊的提交记录，它有两个父节点。翻译成自然语言相当于：“我要把这两个父节点本身及它们所有的祖先都包含进来。”  我们要把 bugFix 合并到 master 里. 在master branch上执行 **git merge bugFix**。 之后， 咱们再把 master 分支合并到 bugFix：**git checkout bugFix; git merge master** , 这样两个branch就合并了。因为 master 继承自 bugFix，Git 什么都不用做，只是简单地把 bugFix 移动到 master 所指向的那个提交记录。现在所有提交记录的颜色都一样了，这表明每一个分支都包含了代码库的所有修改！大功告成！

5. git rebase

* Rebase 实际上就是取出一系列的提交记录，“复制”它们，然后在另外一个地方逐个的放下去。
Rebase 的优势就是可以创造更线性的提交历史，这听上去有些难以理解。如果只允许使用 Rebase 的话，代码库的提交历史将会变得异常清晰。

* 我们想要把 bugFix 分支里的工作直接移到 master 分支上。移动以后会使得两个分支的功能看起来像是按顺序开发，但实际上它们是并行开发的。在bugFix上执行 **git rebase master** 现在唯一的问题就是 master 还没有更新，下面咱们就来更新它吧. 切换到master上，然后**git checkout master; git rebase bugFix**

## Advance

### 强制修改分支位置

我使用相对引用最多的就是移动分支。可以直接使用 -f 选项让分支指向另一个提交。例如:

git branch -f master HEAD~3

上面的命令会将 master 分支强制指向 HEAD 的第 3 级父提交。

git branch -f master C2

把master的指针指向C2这个提交。