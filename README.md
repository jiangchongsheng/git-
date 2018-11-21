# git-
git基本操作
Git 基础操作文档
Git配置相关文件夹及文件
.gitgit基础文件夹,被git管理的根文件夹下会出现一个.git文件夹

.gitignore一般放于git管理的根文件夹下,内容是git版本控制忽略的文件夹以及文件,即执行add命令时会忽略的文件夹或文件

Git结构介绍


服务端
包含最新的完整的源码以及分支副本
客户端(用户端)
包含完整的源码以及分支副本
用户端的代码又称为本地库
基本流程
用户修改文件(change) —> 添加修改文件到commit(add) —-> 提交commit到本地库(commit) —-> 推送到远程服务器(push)

创建新的Git版本库
在需要版本控制的文件夹(以folder为例)下执行命令
$ git init

执行成功会出现
Initialized empty Git repository in /folder/.git/

将新的文件添加到已有的版本库内基本流程
在当前folder目录下添加添加一个a.txt文件
查询当前状态
$ git status
显示
On branch master
No commits yet
Untracked files:
  (use "git add <file>..." to include in what will be committed)
    a.txt
nothing added to commit but untracked files present (use "git add" to track)
说明我们有一个文件修改但是还没有提交到本地仓库,执行add命令添加本次修改
$ git add a.txt
使用$ git status 查询当前状态
On branch master
No commits yet
Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
    new file:   a.txt
说明当前临时工作空间有一个待commit的文件了
$ git commit -m "commit description"
使用commit 将刚刚add的a.txt提交到本地仓库上去 -m 表示 本次提交的说明,描述本次提交的一个或多个文件的原因.
显示 表示提交本地仓库成功
[master (root-commit) 4541564] commit description
 1 file changed, 3 insertions(+)
 create mode 100644 a.txt
查询状态$ git status
On branch master
nothing to commit, working tree clean
说明本地没有修改的文件,以及未提交的文件

解决冲突
同分支不同用户分支
用户1拉取分支master
用户2拉去分支master

用户1

提交文件a.txt
内容,三行文本
hi
hello
git
提交本地仓库
推送中央仓库
用户2
修改了文件a.txt
内容,三行文本
add z
conflict 
content
提交本地仓库
推送中央仓库
这时,推送失败,会提示当前本地库与中央仓库不同步(因为在期间用户1修改了a.txt文件)
To xxx.xxx.com/xxx.git
! [rejected]        master -> master (fetch first)
error: failed to push some refs to 'xxx.xxx.com/xxx.git'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. You may want to first integrate the remote changes
hint: (e.g., 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
使用$ git pull 同步最新的代码(此时会发生冲突)
一般情况下互不影响或者简单的修改git会自动合并冲突(auto mering)
但是本次例子中两个用户修改完全不同,git会自动合并出错
remote: Counting objects: 3, done.
remote: Total 3 (delta 0), reused 0 (delta 0)
Unpacking objects: 100% (3/3), done.
From xxx.xxx.com/xxx
4541564..e7462f1  master     -> origin/master
Auto-merging a.txt
CONFLICT (content): Merge conflict in a.txt
Automatic merge failed; fix conflicts and then commit the result.
注意关键词auto-mergeing a.txt 说明git帮我们自动合并了a.txt,紧接着Automatic merge failed,说明合并失败了,这时候需要我们手动合并

我们手动打开a.txt或者命令行vi a.txt会看到合并失败的文本内容

<<<<<<< HEAD
add 
conflict 
content
=======
hi
hello
git
>>>>>>> e7462f18d791305b34ae7fdd89715a3655b07434
注意git给我们添加的标志

<<<<<<< HEAD
我的内容
=======
 他人的内容
>>>>>>> e7462f18d791305b34ae7fdd89715a3655b07434
这边我们人工判断哪些内容是需要的哪些是不需要要的

本例中我们人工修改内容为

add 
conflict 
hello
git
这个内容我们认为是正确的了
执行$ git status

On branch master
Your branch and 'origin/master' have diverged,
and have 1 and 1 different commits each, respectively.
  (use "git pull" to merge the remote branch into yours)
You have unmerged paths.
  (fix conflicts and run "git commit")
  (use "git merge --abort" to abort the merge)
Unmerged paths:
  (use "git add <file>..." to mark resolution)
    both modified:   a.txt
no changes added to commit (use "git add" and/or "git commit -a")
提示我们当前有一个未处理的冲突
执行$ git add a.txt命令将我们解决了冲突的文件提交(这边流程和普通提交流程一致), $ git commit -m "merge a.txt", $ git push
这是我们提交中央仓库,解决冲突成功结束

不同分支合并冲突
用户X从master拉取分支branch1
用户X从master拉取分支branch2
此时a.txt文件内容为

master
branch
用户X在branch1完成了需求修改了a.txt文件,内容为

user
change
file
用户X为branch1提出了pr(pull request),并成功合并到了master分支.

用户X开始在branch2开发需求,修改a.txt文件为

update
demand
用户提出PR合并到master分支,会提示失败,因为和master冲突了,开始手动解决冲突
将本地master代码更新到最新,切换到branch2
运行命令$git merge master 本地合并主分支
同样的提示 auto mering failed ,开始正常的冲突合并流程
最终提交branch2至中央仓库,再次提交pr,合并成功.
