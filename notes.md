# 创建版本库
## 指令：
**git init**: 初始化仓库

**git add xxx** 添加文件到暂存区

**git commit -m "balabala"** 将暂存区内容添加到仓库中

# 版本回退：
## 指令：
**git status**: 用于显示工作目录和暂存区的状态


**git diff**: 当工作区有改动，临时区为空，diff的对比是“工作区与最后一次commit提交的仓库的共同文件”；当工作区有改动，临时区不为空，diff对比的是“工作区与暂存区的共同文件”。

**git log**: 按时间先后顺序列出所有的提交，最近的更新排在最上面

## 总结：
1.HEAD指向的版本就是当前版本，因此，Git允许我们在版本的历史之间穿梭，使用命令**git reset --hard commit_id**

2.穿梭前，用**git log**可以查看提交历史，以便确定要回退到哪个版本。

3.要重返未来，用**git reflog**查看命令历史，以便确定要回到未来的哪个版本。

# 工作区和暂存区:
**git add**命令实际上就是把要提交的所有修改放到暂存区（Stage），然后，执行**git commit**就可以一次性把暂存区的所有修改提交到分支

![avatar](https://github.com/vitalemonate/learngit/blob/main/pics/1.jpg)

![avatar](https://github.com/vitalemonate/learngit/blob/main/pics/2.png)


# 撤销修改
场景1：当你改乱了**工作区**某个文件的内容，想直接丢弃工作区的修改时，用命令**git checkout -- file**, **git checkout**其实是用版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以“一键还原”

场景2：当你不但改乱了工作区某个文件的内容，还添加到了**暂存区**时，想丢弃修改，分两步，第一步用命令**git reset HEAD file**，就回到了场景1，第二步按场景1操作。

场景3：已经提交了不合适的修改到**版本库**时，想要撤销本次提交，参考版本回退一节，不过前提是没有推送到远程库。

最新版本的git已经使用**git restore**代替了原来的reset和checkout命令了，如下：

**git restore readme**（使用 "git restore <文件>..." 丢弃工作区的改动）

  (use "git restore <file>..." to discard changes in working directory)

**git restore --staged readme**（使用 "git restore --staged <文件>..." 以取消暂存）

  (use "git restore --staged <file>..." to unstage)

## 删除文件
**git rm**: 删除工作区文件，并且将这次删除放入暂存区, 注意要删除的文件是**没有修改过的**，就是说和当前版本库文件的内容相同

# 添加远程库
**git remote add origin git@github.com:wanghao/learngit.git**

关于origin:远程库的名字就是origin，这是Git默认的叫法，也可以改成别的，但是origin这个名字一看就知道是远程库。

## 本地向远程库推送内容
**git push origin <本地分支名>:<远程分支名>** 本地分支向指定远程分支推送

**git push origin <本地分支名> 将本地当前分支** 推送到与本地当前分支同名的远程分支上

**git push** 将本地当前分支推送到与本地当前分支同名的远程分支上

第二种和第三种方式需要在第一次使用时将本地分支与远程同名分支相关联：

**git push --set-upstream origin <本地分支名>**

简写方式：**git push -u origin <本地分支名>**

## 查看本地分支与远程分支的关联
**git branch -vv**

**git remote show origin**

**cat .git/config**

# 分支管理
## 创建与合并分支
查看本地分支：**git branch**

查看远程分支：**git branch -r**

创建分支：**git branch <name>**

切换分支：**git checkout name**或者**git switch name**

创建+切换分支：**git checkout -b name**或者**git switch -c name**

合并某分支到当前分支：**git merge name**

删除分支：**git branch -d name**

## --no-ff方式的git merge

![avatar](https://github.com/vitalemonate/learngit/blob/main/pics/3.png)

## 分支处理策略
首先，master分支应该是非常稳定的，也就是仅用来发布新版本，平时不能在上面干活

那在哪干活呢？干活都在dev分支上，也就是说，dev分支是不稳定的，到某个时候，比如1.0版本发布时，再把dev分支合并到master上，在master分支发布1.0版本

团队合作的分支看起来就像这样

![avatar](https://github.com/vitalemonate/learngit/blob/main/pics/4.png)

## bug分支
**git stash**命令的作用就是将目前还不想提交的但是已经修改的内容进行保存至堆栈中，后续可以在某个分支上恢复出堆栈中的内容。这也就是说，stash中的内容不仅仅可以恢复到**原先开发的分支，也可以恢复到其他任意指定的分支上**

### 应用场景
1 当正在dev分支上开发某个项目，这时项目中出现一个bug，需要紧急修复，但是正在开发的内容只是完成一半，还不想提交，这时可以用git stash命令将修改的内容保存至堆栈区，然后顺利切换到hotfix分支进行bug修复，修复完成后，再次切回到dev分支，从堆栈中恢复刚刚保存的内容。

2 由于疏忽，本应该在dev分支开发的内容，却在master上进行了开发，需要重新切回到dev分支上进行开发，可以用git stash将内容保存至堆栈中，切回到dev分支后，再次恢复内容即可。

### 恢复现场
**git stash apply** 恢复，但是恢复后，stash内容并不删除，需要用**git stash drop**来删除

**git stash pop** 恢复的同时把stash内容也删了

**git stash pop stash@{$num}** 可以恢复指定num的内容并在stash中删除

**git stash list** 可以查看stash了哪些存储

## 多人协作
### 多人协作的工作模式通常是这样
首先，可以试图用**git push origin branch-name** 推送自己的修改

如果推送失败，则因为远程分支比你的本地更新，需要先用**git pull**试图合并

如果合并有冲突，则解决冲突，并在本地提交

没有冲突或者解决掉冲突后，再用**git push origin branch-name**推送就能成功

如果**git pull**提示**no tracking information**，则说明本地分支和远程分支的链接关系没有创建，用命令**git branch --set-upstream-to branch-name origin/branch-name**

# 标签管理
标签是版本库的一个快照,每个标签都与一个commit绑定

命令**git tag tagname**用于新建一个标签，默认为**HEAD**，也可以指定一个**commit id**

命令**git tag -a tagname -m "blablabla..."**可以指定标签信息

命令**git tag**可以查看所有标签

命令**git push origin tagname**可以推送一个本地标签

命令**git push origin --tags**可以推送全部未推送过的本地标签

命令**git tag -d tagname**可以删除一个本地标签

命令**git push origin :refs/tags/tagname**可以删除一个远程标签
