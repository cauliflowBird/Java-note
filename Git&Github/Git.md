# 一、Git简介

## 1、Git---目前世界上最先进的==分布式====版本控制==系统.

#### 1.1  版本控制

![版本控制](https://github.com/cauliflowBird/Java-note-/blob/master/images/版本控制.jpg)

如上图: 同一个人或多人对同一个文件进行修改从而诞生多个版本(即副本),版本控制软件就可以很好的记录修改内容,修改人等信息；方便编辑的人查看改动和检索自己所需要的内容，也有利于多人协作来完成一个项目。

#### 1.2  分布式(与集中式版本控制系统的区别)

##### 	集中式(如CVS、SVN):

![集中式](https://github.com/cauliflowBird/Java-note-/blob/master/Git%26Github/images/集中式.jpg)



版本库集中存放在中央服务器；个人用户使用自己电脑，工作时先从中央处理器取得最新版本，然后工作完后又将自己更新得版本上传至中央服务器(类似于多个人编辑图书馆中得一本书);

弊端: 中央服务器一般都是远程，需要联网，而使用互联网进行文件传输，一旦网速慢就会产生很不好的体验;

##### 	分布式(Git):

![分布式](images\分布式.jpg)

没用中央服务器，每个人的电脑上都有一个完整的版本库，工作时不需要联网，即使A,B两人在各自电脑上修改了同一文件，只需将各自的修改推送给对方即可,就可看到互相的修改(不在同一个局域网内，可能会需要类似的中央服务器，只是为了方便“交换”大家的修改，没有也没关系);



# 二、Git常用命令和概念

## 1、Git常用命令

安装完Git后,找到Git->Git Bash进入命令行:

```shell
$ git config --global user.name "Your Name"    --机器名
$ git config --global user.email "email@example.com" --email地址
--global参数表示所有Git仓库都使用该配置
--配置仓库
1.选择一个位置,创建一个空目录例如repository(mkdir repository)
2.进入该目录位置(cd ...)
3.$ git init  --初始化该目录(文件夹)为Git仓库
将文件（例如readme.txt)添加到Git仓库(例如repository)下:
执行命令:
$ git add readme.txt  --可反复使用，添加多个文件
$ git commit -m "message描述" --提交到仓库
修改readme.txt内容后
$ git status                   ---实时查看仓库状态
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   readme.txt  --readme.txt被修改过了

Untracked files:
  (use "git add <file>..." to include in what will be committed)

        readme.txt.bak

no changes added to commit (use "git add" and/or "git commit -a") --还没准备提交
--查看修改后的内容与原文件内容的差异
$ git diff
diff --git a/readme.txt b/readme.txt
index d8036c1..013b5bc 100644
--- a/readme.txt
+++ b/readme.txt
@@ -1,2 +1,2 @@
-Git is a version control system. --原来的
+Git is a distributed version control system. --新增distributed单词
 Git is free software.
\ No newline at end of file
$ git add readme.txt
$ git status  --查看仓库的状态
On branch master
Changes to be committed:  --关于readme.txt文件的改动将要提交
  (use "git reset HEAD <file>..." to unstage)

        modified:   readme.txt

Untracked files:
  (use "git add <file>..." to include in what will be committed)

        readme.txt.bak
$ git commit -m "add distributed" --修改后的文件提交到仓库
[master b9866a0] add distributed
 1 file changed, 1 insertion(+), 1 deletion(-)
$ git status
On branch master                        --此时没有需要提交的修改，且仓库目录是干净的
nothing to commit, working tree clean
```

```sh
$ git add readme.txt
$ git commit -m "append GPL"
[master 00983f8] append GPL
 1 file changed, 1 insertion(+), 1 deletion(-)

$ git log  --查看提交历史
commit 00983f88a568903a7939b7413097eef2f91d4068 (HEAD -> master) --版本号
Author: wangyuchen <1341769762@qq.com>
Date:   Fri Apr 12 15:54:14 2019 +0800

    append GPL

commit b9866a054345b4db3a466e30062eb8f7f8e8beb2
Author: wangyuchen <1341769762@qq.com>
Date:   Fri Apr 12 15:39:47 2019 +0800

    add distributed

commit a5fad2cecdae58d675302c19c052584ab26913fb
Author: wangyuchen <1341769762@qq.com>
Date:   Fri Apr 12 15:21:14 2019 +0800

    wrote a readme file

wangyuchen@DESKTOP-47REHO2 MINGW64 /D/Git2.21.0/repository (master)
$ git log --pretty=oneline --一行里显示提交历史
00983f88a568903a7939b7413097eef2f91d4068 (HEAD -> master) append GPL --HEAD->指向当前版本
b9866a054345b4db3a466e30062eb8f7f8e8beb2 add distributed
a5fad2cecdae58d675302c19c052584ab26913fb wrote a readme file

--退回到上个版本,HEAD^^就表示上上版本,以此类推
$ git reset --hard HEAD^
HEAD is now at b9866a0 add distributed

$ git log
commit b9866a054345b4db3a466e30062eb8f7f8e8beb2 (HEAD -> master)
Author: wangyuchen <1341769762@qq.com>
Date:   Fri Apr 12 15:39:47 2019 +0800

    add distributed

commit a5fad2cecdae58d675302c19c052584ab26913fb
Author: wangyuchen <1341769762@qq.com>
Date:   Fri Apr 12 15:21:14 2019 +0800

    wrote a readme file

$ git reset --hard 00983 --切换版本($ git reset --hard commit_id(版本号))
HEAD is now at 00983f8 append GPL

$ cat readme.txt
Git is a distributed version control system.
Git is free software distributed under the GPL.

$ git reflog --查看命令历史,以便查看相应版本对应的commit_id，以便切换
00983f8 (HEAD -> master) HEAD@{0}: reset: moving to 00983
b9866a0 HEAD@{1}: reset: moving to HEAD^
00983f8 (HEAD -> master) HEAD@{2}: commit: append GPL
b9866a0 HEAD@{3}: commit: add distributed
a5fad2c HEAD@{4}: commit (initial): wrote a readme file
```

HEAD指向的版本就是当前版本， git reset --hard commit_id 切换版本;

git log查看提交历史(版本号等),用于切换HEAD指向版本;

git reflog --查看命令历史,以便查看相应版本对应的commit_id，以便切换;

## 2、工作区和暂存区

#### 2.1 工作区(Working Directory)

> 电脑中能看见的目录，比如自定义的Git仓库目录(repository文件夹)

#### 2.2 暂存区

> 工作区中有一个隐藏目录`.git`是Git的版本库(Repository);
>
> Git的版本库里存了很多东西，其中最重要的就是称为stage（或者叫index）的暂存区，还有Git为我们自动创建的第一个分支`master`，以及指向`master`的一个指针叫`HEAD`;

![工作区和暂存区](images\工作区和暂存区.jpg)

把文件添加到Git版本库:

1.`git add xxx`把文件添加进去，实际上把文件修改添加到`stage`暂存区;

2.`git commit`提交修改，实际上把暂存区的所有内容提交到当前分支;

Git自动为我们创建了唯一一个`master`分支，所以，现在，`git commit`就是往`master`分支上提交更改。

可以简单理解为，需要提交的文件修改通通放到暂存区，然后，一次性提交暂存区的所有修改。

## 3、撤销修改

场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令`git checkout -- file`。

场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令`git reset HEAD <file>`，就回到了场景1，第二步按场景1操作。

场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，参考[版本回退](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/0013744142037508cf42e51debf49668810645e02887691000)一节，不过前提是没有推送到远程库。

## 4、删除文件

场景:

一个工作区(Git仓库目录)中的文件(test.txt)->git add(添加到暂存区)->git commit(-m "")(添加到版本库分支)后;

此时要删除文件，首先删除(rm xxx/或手动删除)掉工作区的test.txt，此时git status

```shell
$ git status
On branch master
Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        deleted:    test.txt

no changes added to commit (use "git add" and/or "git commit -a")
Git知道你从工作区中删除了test.txt;但版本库中还存在test.txt
```

此时若想彻底删除(删除版本库中的)git rm-->git commit(-m "")

```shell
$ git rm test.txt  --git rm xxx
rm 'test.txt'

$ git commit -m "remove test.txt" --git commit
[master 9b8c3f8] remove test.txt
 1 file changed, 1 deletion(-)
 delete mode 100644 test.txt
```

若想恢复工作区中的文件(版本库中还存在test.txt)

`git checkout -- test.txt`即还原;

`git checkout`其实是用版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以“一键还原”。

命令`git rm`用于删除一个文件。如果一个文件已经被提交到版本库，那么你永远不用担心误删，但是要小心，你只能恢复文件到最新版本，你会丢失**最近一次提交后你修改的内容**。

## 5、远程仓库

第1步：创建SSH Key。在用户主目录下，看看有没有.ssh目录，如果有，再看看这个目录下有没有`id_rsa`和`id_rsa.pub`这两个文件，如果已经有了，可直接跳到下一步。如果没有，打开Shell（Windows下打开Git Bash），创建SSH Key：

```
$ ssh-keygen -t rsa -C "youremail@example.com"
```

你需要把邮件地址换成你自己的邮件地址，然后一路回车，使用默认值即可，由于这个Key也不是用于军事目的，所以也无需设置密码。

如果一切顺利的话，可以在用户主目录里找到`.ssh`目录，里面有`id_rsa`和`id_rsa.pub`两个文件，这两个就是SSH Key的秘钥对，`id_rsa`是私钥，不能泄露出去，`id_rsa.pub`是公钥，可以放心地告诉任何人。

第2步：登陆GitHub，打开“Account settings”，“SSH Keys”页面：

然后，点“Add SSH Key”，填上任意Title，在Key文本框里粘贴`id_rsa.pub`文件的内容：

点“Add Key”，你就应该看到已经添加的Key：

#### 5.1 远程同步

实现本地Git仓库与Github上新建一个Git仓库进行远程同步；这样GitHub上的仓库即可以作为备份也可以用来协作。

1.在Github上新建一个Git仓库

![createGitRepository](images\createGitRepository.jpg)



![createRepository](images\createRepository.jpg)

自定义Repository name(Git仓库名),点击创建

2.根据提示在Git Bash(Git工具命令行中操作),此时所提交的修改要已经commit到本机版本库中,且在当前master

![GitCreate](images\GitCreate.jpg)

```shell
$ git remote add origin https://github.com/cauliflowBird/test.git
添加后，远程库的名字就是origin，这是Git默认的叫法，也可以改成别的，但是origin这个名字一看就知道是远程库;

下一步，把本地库的所有内容推送到远程库上：
$ git push -u origin master

把本地库的内容推送到远程，用`git push`命令，实际上是把当前分支`master`推送到远程。

由于远程库是空的，我们第一次推送`master`分支时，加上了`-u`参数，Git不但会把本地的`master`分支内容推送的远程新的`master`分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令。

现在起，只要本地作了提交，就可以通过命令：
$ git push origin master
把本地master分支的最新修改推送至GitHub，现在就拥有了真正的分布式版本库。
```

