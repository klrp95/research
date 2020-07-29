# research
## git相关知识
### 1. git的简介
github存在工作区（working tree），
暂存区（index /stage），
版本库（repository）。  
1. **工作区**就是在本地的相对应的文件夹.  
2. **暂存区**英文叫index/stage。一般存放在 ".git目录下" 下的index文件（.git/index）中，所以我们把暂存区有时也叫作索引（index）。  
3. **版本库**指的是工作区有一个隐藏目录.git，这个不算工作区，而是Git的版本库。



### 2. 提交、修改
* `git add <file name> ` 提交修改或新文件把文件添加到暂存区  
* `git commit -m “引号里写提交的摘要”` 把暂存区里的所有内容提交到当前分支（创建Git 版本库 时，Git自动为我们创建了唯一一个master分支)
* 如果要提交到远程仓库上，需要使用：`git push origin 当前分支`。
![imamge](https://github.com/klrp95/research/blob/master/images/0.jpeg)

### 3. 版本退回
`git log` 命令显示从最近到最远的提交日志。使用它来查看提交历史，以便确定要退回到哪个版本。  
`git log —pretty=online` 输出第一列为 commit id（版本号），是用SHA1计算出来的十六进制数字，是版本控制系统，防止冲突。第二列是提交的日志历史信息。
可以使用 `git reset —hard HEAD^`回到上一个版本（HEAD 是指向**当前版本**的**指针**，HEAD^ 是上一个版本，HEAD^^ 是上上版本，HEAD~100是前100个的版本）。  
如果退回后后悔了可以使用：  
`git reflog`来找到想要的版本id，后面跟 `git reset --hard 1094a` （版本号不用写全）来撤销相关操作。

### 4. 撤销修改
场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令`git checkout -- file`。  
场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令`git reset HEAD <file>`，就回到了场景1，第二步按场景1操作。  
场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，参考版本回退的方法，不过前提是没有推送到远程库。

### 5. 删除文件
在正常使用`rm <file>`后，使用`git status`可以看到当前的状态  
若要撤回删除：`git checkout -- <file>`（如果文件已经提交到版本库 可以恢复）
若要彻底删除：`git rm <file>`  后面跟 `git commit -m <file>`

### 6. 分支
#### 创建、合并与删除分支
可以使用`git branch`来查看当前分支。
Git创建一个分支很快，只增加一个新指针，将HEAD的指向从之前的分支改为当前分支这两个操作。
创建+切换分支：`git checkout -b <name>`或者`git switch -c <name>`,相当于`git branch <name>` + `git checkout <name>`
合并某分支到当前分支：`git merge <name>`
删除分支：`git branch -d <name>`

#### 冲突解决
当不同分支提交了不同的修改时，Git无法自动合并分支，就必须首先解决冲突。解决冲突后，再提交，合并完成。**解决冲突**就是_把Git合并失败的文件手动编辑为我们希望的内容_，再提交。
`git log --graph --pretty=oneline --abbrev-commit`命令可以看到分支合并图。

### 7. 多人协作
使用`git clone <地址>`从远程仓库克隆时，git自动把本地 master 分支和远程的 master 分支对应起来。远程仓库默认名称是 origin。  
把当前分支上的所有本地提交推送到远程库。推送时，要指定本地分支，Git就会把该分支推送到远程库对应的远程分支上：
`git push origin master`  
如果要推送其他分支，比如dev，就改成：  
`git push origin dev`
#### 冲突解决

当多人协作，push 有冲突时：先用`git pull`把最新的提交从origin/dev抓下来，然后，在本地合并，解决冲突，再推送。

如果`git pull`显示失败，根据下面的提示`git branch --set-upstream-to=origin/dev dev`来将远程分支和本地分支关联起来。

### 8. fork
和其它的Git工作流一样，Forking工作流要先有一个公开的正式仓库存储在服务器上。 但一个新的开发者想要在项目上工作时，不是直接从正式仓库克隆，而是fork正式项目在服务器上创建一个拷贝。  
这个仓库拷贝作为他个人公开仓库 —— 其它开发者不允许`push`到这个仓库，但可以`pull`到修改（后面我们很快就会看这点很重要）。 在创建了自己服务端拷贝之后，和之前的工作流一样，开发者执行`git clone`命令克隆仓库到本地机器上，作为私有的开发环境。

要提交本地修改时，`push`提交到自己公开仓库中 —— 而不是正式仓库中。 然后，给正式仓库发起一个`pull request`，让项目维护者知道有更新已经准备好可以集成了。 

为了把功能集成到正式代码库，维护者`pull`贡献者的变更到自己的本地仓库中，检查变更以确保不会让项目出错， 合并变更到自己本地的 master 分支， 然后`push origin master`分支到服务器的正式仓库中。 到此，贡献的提交成为了项目的一部分，其它的开发者应该执行`pull`操作与正式仓库同步自己本地仓库。
