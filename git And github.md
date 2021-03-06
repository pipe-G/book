github 远程仓库
git 分布式版本控制系统（可以单机操作）
在需要版本控制的文件夹下运行：
git init
运行完之后会有一个隐藏的文件.git
文件编辑完之后，通过命令提交:
(1)git add filename （可以反复多次使用，添加多个文件）
(2)git commit -m "需要说明的信息" （一次commit可以提交多个add）
git log 查看历史记录
git log --pretty=oneline 查看简化的历史记录
git reflog 查看命令历史
HEAD 指向的版本就是当前版本
到达指定版本需要使用 git reset --hard commit_id
返回上一个版本需要使用 git reset --hard HEAD^ 
上上一个 HEAD^ 往上100个 可以简化成 HEAD~100
git add 其实是存放到git的暂存区
git commit 一次性把暂存区中的内容放到工作区(分支)
git status 查看文件状态
git管理的是修改，而非文件
git diff会比较未暂存版本与最后add/commit的版本
git diff HEAD -- filename则会比较未暂存版本与分支中已commit版本的区别

git checkout -- file
一种是文件自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；
一种是文件已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。

git reset HEAD <filename>   
git checkout -- file
改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改

git rm <filename> 删除一个文件

从远程仓库克隆
git clone git@github.com:pipe-G/gitkills.git

与远程仓库建立连接 git remote add origin git@github.com:pipe-G/Django.git 

分支
查看分支：git branch

创建分支：git branch <name>

切换分支：git checkout <name>

创建+切换分支：git checkout -b <name>

合并某分支到当前分支：git merge <name>

删除分支：git branch -d <name>

遇到冲突，合并之后，冲突的重新书写。
git log --graph --pretty=oneline --abbrev-commit 查看分支的合并情况，简化的
git log --graph 命令可以看到分支合并图，详细的
禁用 Fast forward 模式
git merge --no-ff -m "merge with no-ff" dev

git stash 内容隐藏
git stash list 查看隐藏的内容
一是用git stash apply恢复，但是恢复后，stash内容并不删除，你需要用git stash drop来删除；

另一种方式是用--，恢复的同时把stash内容也删了：

恢复指定的stash，通过git stash list查看
git stash apply stash@{0}

强制删除：
git branch -D  分支名

查看远程库信息，使用git remote -v；


本地新建的分支如果不推送到远程，对其他人就是不可见的；

从本地推送分支，使用git push -u origin branch-name，
由于远程库是空的，我们第一次推送master分支时，加上了-u参数，
git push -f origin branch-name
-f 强制提交。
如果推送失败，先用git pull抓取远程的新提交；

在本地创建和远程分支对应的分支，使用git checkout -b branch-name origin/branch-name，本地和远程分支的名称最好一致；

建立本地分支和远程分支的关联，使用git branch --set-upstream-to=origin/branch-name branch-name ；

从远程抓取分支，使用git pull origin branchName，如果有冲突，要先处理冲突。

(?)git rebase
rebase操作可以把本地未push的分叉提交历史整理成直线；

rebase的目的是使得我们在查看历史提交的变化时更容易，因为分叉的提交需要三方对比。

命令git tag <tagname>用于新建一个标签，默认为HEAD，也可以指定一个commit id,写到标签名字后面；

命令git tag -a <tagname> -m "blablabla..."可以指定标签信息；
git show <tagname>可以看到说明文字

命令git tag可以查看所有标签。

命令git push origin <tagname>可以推送一个本地标签；

命令git push origin --tags可以推送全部未推送过的本地标签；

命令git tag -d <tagname>可以删除一个本地标签；

命令git push origin :refs/tags/<tagname>可以删除一个远程标签。

设置别名
git config --global alias.st status
配置Git的时候，加上--global是针对当前用户起作用的，如果不加，那只针对当前的仓库起作用。

每个仓库的Git配置文件都放在.git/config文件中：
别名就在[alias]后面，要删除别名，直接把对应的行删掉即可

（1）git stash save "save message"  : 执行存储时，添加备注，方便查找，只有git stash 也要可以的，但查找时不方便识别。

（2）git stash list  ：查看stash了哪些存储

（3）git stash show ：显示做了哪些改动
（4）git stash pop ：命令恢复之前缓存的工作目录，将缓存堆栈中的对应stash删除，并将对应修改应用到当前的工作目录下
（5）git stash clear ：删除所有缓存的stash

  常规 git stash 的一个限制是它会一下暂存所有的文件。有时，只备份某些文件更为方便，让另外一些与代码库保持一致。一个非常有用的技巧，用来备份部分文件：

（1）add 那些你不想备份的文件（例如： git add file1.js, file2.js）
（2）调用 git stash –keep-index。只会备份那些没有被add的文件。
（3）调用 git reset 取消已经add的文件的备份，继续自己的工作。



###git 多人开发
 1.git reset --soft     （回到冲突点）
 2.git add .
 3.git stash
 4.git  status
 5.git pull origin dev
 6.git stash pop
 7.git add .
 8.git commit -m ''
