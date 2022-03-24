##### *git --help  可以查看所有与git相关的命令* 

git help -a 可以查看更过的与git相关的命令

Ctrl + insert 拷贝

shift + insert 粘贴

origin（远程库的名字，git默认叫法，一般不做修改，在第一次push的时候要带上-u，之后的每次push就直接 git push 即可）

 

##### git安装及命令

1. git --version 查看git版本
2. git config --global user.name "名字"
3. git config --global user.email "邮箱"
4. git config --list   查看所有信息



##### git文件的三种模式

三个工作状态：

commit  表示数据已经保存在本地数据库中

modified 表示修改了文件，但是还没有保存到数据库中

staged  表示对一个已修改文件的当前版本做了标记，使之包含在下次提交的快照中



三个工作区域：

工作区

暂存区

git仓库（版本库）



git命令

git init 创建本地工作的版本库

git add “文件夹的名称”

git status 查看当前本地仓库中文件的状态

git commit -m "注释" 将当前暂存区的内容全部提交到版本库

git commit -a  -m 相当于git add和git commit的结合

git log 查看提交的日志

clear 清屏



git commit 在提交时如果没有输入注释，会进入编辑模式，i->写注释->ESC->: wq即可完成

：进入底行模式 wq表示保存并且退出



##### 时光穿梭机

git diff HEAD -- "文件名称"  将工作区的内容与版本库中的内容做比较

其中 --- ：表示变动前的文件

​       +++：表示变动后的文件

​      -1,2表示从第一行开始连续两行

​      +1,3 表示从第一行开始连续三行



**暂存区文件提交与撤销**

git reset HEAD “文件”   将add后的文件撤销，防止在后续提交时提交到版本库

*git restore “文件”*   取消该文件的本次变更  

git log 查看提交的日志信息

git reset --hard HEAD^  回退到上一个版本（回到版本库的上一个版本）

git reset --hard HEAD^^   回退到上两次提交

git reset --hard HEAD~n 回退到前n个版本

git reflog  查看所有之前提交过的版本（比 git log 完整）

git reset --hard "哈希字符串"  回退到固定的某个提交版本

git log --pretty=oneline 只显示哈希值和提交注释信息的日志（只用一行的形式展示）

git ls-files 查看本地仓库中的文件



**文件删除**

git checkout "文件"   其中checkout表示从版本库中拉取文件到工作目录

git rm "文件" 用于删除版本库中对应的文件

 

**远程仓库**

gitlab /github /码云

码云 （GVP、其他带图标的）较官方，bug较少

github中方便看代码层级  可借助谷歌商店插件Octotree

git本地仓库取消与远程仓库的绑定 git remote remove origin

***HTTPS推送方式***

git clone “HTTPS的地址”、

git remote add origin “远程仓库地址”  将本地仓库与远程仓库作绑定

git push -u origin master  推送到远程的主干分支

***SSH推送***

使用本地git客户端生成SSH公钥和私钥。ssh-keygen -t rsa -C "github邮箱地址"

id_rsa.pub表示公钥 id_rsa表示密钥

检查测试连接命令  ssh -T git@github.com



**git 分支操作** 

git checkout branchname 切换到指定分支

git checkout -b new_branch 新建分支并切换到新建分支

git checkout -b new_branch origin/branch 在origin_branch远程分支上创建本地new_branch分支(origin/需要加上)

git branch -d branchname 删除指定分支（会在删除前检查merge的状态，如果该分支有提交未进行合并，则删除会失败）

git branch -D branchname 删除指定分支（如果该分支有提交未进行合并，也会删除成功）

git branch 查看所有分支，并且*号标记当前所在分支

git merge branchname 合并分支

git branch -m|-M oldbranch newbranch 重命名分支，如果newbranch已经存在，则需要用-M强制重命名，否则使用-M进行重命名。

git branch -vv 查看本地分支与远程分支的映射关系

**要在主干上合并分支，而不能在分支上合并主干**（原因）



##### 分支push与pull操作

git branch -a 查看本地与远程分支

git branch -r 查看远程所有分支

git push origin branch_name 推送本地分支到远程（远程分支和本地分支的名称相同）

git push origin :remote_branch 删除远程分支（本地分支还在保留）  

git checkout -b local_branch origin/remote_branch 拉取远程指定分支并在本地创建分支



##### 冲突问题解决

git log  --graph --pretty=oneline  对冲突修改的分支执行该命令可查看分支合并图

git push 默认推送的是主干分支（即推送到当前开发的这个远程分支上去）



##### 标签管理（Releases）

git tag tag_name  新建标签，默认为HEAD

git tag -a tag_name -m "XXX" 添加标签并指定标签描述信息

git tag 查看所有标签

git tag -d tag_name 删除一个本地标签

git push origin tag_name 推送本地标签到远程

git push origin  --tags 推送全部未推送过的本地标签到远程

git push origin :ref/tags/tag_name 删除一个远程标签，不影响本地标签



##### 更多

1. git fetch 和 git pull 区别

   ![](C:\Users\anna\Desktop\git命令原理图.jpg)

git fetch 抓取到新的远程分支，本地的工作区不会自动生成可编辑副本，而是直接将抓取的结果送进版本库。https://blog.csdn.net/qq_42780289/article/details/98049574

git fetch取回更新后会返回一个FETCH_HEAD,指的是某个branch在服务器上的最新状态，可以通过 git log -p FETCH_HEAD 查看刚取回来的更新信息，在通过git merge FETCH_HEAD将抓取的结果合并到本地分支。

git fetch+git merge FETCH_HEAD === git pull

https://www.cnblogs.com/ruiyang-/p/10764711.html



##### git stash

https://www.jianshu.com/p/14afc9916dcb

https://blog.csdn.net/stone_yw/article/details/80795669

默认情况先，git stash 会缓存下列文件

- 添加到暂存区的修改（staged changes）
- Git跟踪的但并未添加到暂存区的修改（unstaged changes）

但不会缓存以下文件

- 在工作目录中新的文件（untracked files）
- 被忽略的文件（ignored files）

应用场景：

1，当正在dev分支上开发某个项目，这时项目中出现一个bug，需要紧急修复，但是正在开发的内容只是完成一半，还不想提交，这时可以用git stash命令将修改的内容保存至堆栈区，然后顺利切换到hotfix分支进行bug修复，修复完成后，再次切回到dev分支，从堆栈中恢复刚刚保存的内容。

2.2 由于疏忽，本应该在dev分支开发的内容，却在master上进行了开发，需要重新切回到dev分支上进行开发，可以用git stash将内容保存至[堆栈](https://so.csdn.net/so/search?q=堆栈&spm=1001.2101.3001.7020)中，切回到dev分支后，再次恢复内容即可。

总的来说，git stash命令的作用就是将目前还不想提交的但是已经修改的内容进行保存至堆栈中，后续可以在某个分支上恢复出堆栈中的内容。这也就是说，stash中的内容不仅仅可以恢复到原先开发的分支，也可以恢复到其他任意指定的分支上。git stash作用的范围包括**工作区和暂存区**中的内容，也就是说没有提交的内容都会保存至**堆栈**中。

*git pull和git fetch拉取远程分支的区别*

git stash 效果：stash@{0}: WIP on master: b2f489c second

git stash save 效果：stash@{0}: On master: test1   {0}表示当前数据在堆栈的最外层。

git stash pop 将堆栈中的最外层数据弹出，并应用与当前分支的工作目录上（只弹出最外面的一层）可能会出现冲突，**可以 通过创建新的分支来解决**。

git stash apply 讲堆栈中的内容应用到当前目录，与git stash pop 不同，该命令不会删除堆栈中的内容，**也就说该命令能够将堆栈的内容多次应用到工作目录中，适应于多个分支的情况。**

可以使用git stash apply + stash名字（如stash@{1}）指定恢复哪个stash到当前的工作目录

git stash drop + 名称  从堆栈中移除某个指定的stash

git stash clear 清除堆栈中的所有内容

git stash show 查看堆栈中最新保存的stash和当前目录的差异。

git stash show stash@{1}查看指定的stash和当前目录的差异。

 git stash show -p 查看详细的不同

git stash show stash@{1} -p查看指定的stash的差异内容

git stash list 查看当前stash中的内容。

git stash branch  从最新的stash创建一个新的分支，然后删除最近的stash（和stash pop）一样，如果需要某个stash，可以指明stash id 如 git stash branch <branch_name> stash@{1}

**应用场景：**当储藏了部分工作，暂时不去理会，继续在当前分支进行开发，后续想将stash中的内容恢复到当前工作目录时，如果是针对同一个文件的修改（即便不是同行数据），那么可能会发生冲突，恢复失败，这里通过创建新的分支来解决。可以用于解决stash中的内容和当前目录的内容发生冲突的情景。
发生冲突时，需手动解决冲突。



##### 撤销pull命令

首先运用git reflog  查看历史变更记录

在运用git reset --hard HEAD@{n}回退到之前提交的版本。

##### git rebase

https://www.jianshu.com/p/4a8f4af4e803

##### 创建新分支的方法

https://blog.csdn.net/tterminator/article/details/78108550

- git branch -vv 查看本地与远程分支的映射关系

git branch -u origin/r_branchname 将当前分支与远程分支建立映射关系

或者使用命令git branch --set-upstream-to origin/ r_branchname 

本地分支和远程分支建立联系后即可从远端拉取分支以及从本地推送内容。

- 撤销本地分支与远程分支之间的联系 git branch --unset-upstream







