# Git操作

[廖雪峰Git教程](https://www.liaoxuefeng.com/wiki/896043488029600)



## 安装

### linux

```bash
sudo apt-get install git
```

也可以下载源码，解压

```bash
./config
make
sudo make install
```

### windows

直接官网下载安装包，然后就会出现**git bash**选项

## 初始化

安装完成后，需要设置用户名和邮箱才能使用git

```bash
git config --global user.name "binlong.lbl"
git config --global user.email "binlong.lbl@alibaba-inc.com"
```

## 创建版本库

新建一个目录，使用git init初始化

```bash
cd ~
mkdir ice_wk
cd ice_wk
git init
```

## 把文件添加到版本库

把一个文件放到Git仓库需要两步。

第一步，用命令`git add`告诉Git，把文件添加到**暂存区**

```bash
git add file.txt
```

git add -A .来一次添加所有改变的文件。 注意 -A 选项后面还有一个句点。 

git add -A表示添加所有内容， git add . 表示添加新文件和编辑过的文件不包括删除的文件; git add -u 表示添加编辑或者删除的文件，不包括新添加的文件。

[git add参数详解](#git_add)

第二步，用命令`git commit`告诉Git，一次性把文件从**暂存区**提交到仓库：

```bash
git commit -m "write a file"
```

`-m`后面输入的是本次提交的说明，可以输入任意内容

## Git状态查看

`git status`命令可以让我们时刻掌握仓库当前的状态

## 查看文件变动 

`git diff`顾名思义就是查看difference，显示的格式正是Unix通用的diff格式

## 版本回退

`git log` 查看提交版本，加上参数`--pretty=oneline`显示简约版信息。

`git reset`回滚操作。通过`git log`可以查到每一个版本的commit id，然后可以回到任意指定版本。

``` bash
git reset --hard 1094a
HEAD is now at 1094acd
```

版本好不用写全，写前面几位就可以了，Git会自动查找。`HEAD`表示当前版本，回归当前版本即删除本地未提交的修改。`HEAD^`表示上一个版本，几个`^`表示前面几个版本。

`git reflog` 用来查找所有git操作。当回退版本失误时可以通过这个命令找到之前的commit id，从而回滚到正确的版本。

### 单个文件回滚

使用`git checkout <commit id> <path>`，相当于重新拉取指定版本的该文件。

使用`git checkout -- <path>`，放弃当前修改。

如果修改已经提交到暂存区，`git reset HEAD <path>`，清空暂存区修改，这个命令不会清除没有`git add`的修改内容。



## 远程仓库

### 建立远程仓库

[github操作](#github)

### 关联远程仓库

```bash
$ git remote add origin git@github.com:icedragon817/20201116.git
```

### 本地内容推送

```bash
$ git push -u origin master
```

先将github的SSH key 配置好，否则可能会推送不成功。

### 远程仓库拉取

```bash
git clone git@github.com:icedragon817/20201116.git
```

要克隆一个仓库，首先必须知道仓库的地址，然后使用`git clone`命令克隆。

Git支持多种协议，包括`https`，但`ssh`协议速度最快。



## 分支管理

### 创建与合并

* 查看分支：`git branch` 
* 创建分支：`git branch <name>`
* 切换分支：`git checkout <name>` 或者 `git switch <name>`
* 创建+切换分支：`git checkout -b <name>` 或者 `git switch -c <name>`
* 合并某分支到当前分支：`git merge <name>`
* 删除分支：`git branch -d <name>`



### 解决冲突

当merge的分支修改的文件与当前分支产生冲突，合并的时候会报错

```bash
$ git merge feature
Auto-merging readme.txt
CONFLICT (content): Merge conflict in readme.txt
Automatic merge failed; fix conflicts and then commit the result.
```

这个时候需要到冲突的文件中手解决，Git用`<<<<<<<`,`=======`,`>>>>>>>`标记出不同分支的内容。

修改之后再次提交

```bash
$ git add .
$ git commit -m "conflict fixed"
[master 9394c48] conflict fixed
```

用带参数的`git log`命令可以看到分支的合并情况

```bash
$ git log --pretty=oneline --graph --abbrev-commit 
*   9394c48 (HEAD -> master) conflict fixed
|\  
| * e6f4856 (feature) 1.1.3 feature brance
* | fc4ff72 1.0.3 something was begining
|/  
* 34a04c9 (dev) 1.2.0 brance dev
* 2b2b6d5 (origin/master) 1.1.2 key move to the folder
* 2a005ca 1.0.2 key folder
* 4762763 1.1.1 my ssh key
* db43d5c [binlong.lbl][1.1.0] first push to github
* 4e8cfe4 1.0.8 remove test file
* b6ea7ce 1.0.7 add test file
* fb7fe38 1.0.6 change readme second
* b0ea02c 1.0.5 change readme first
* 78d5396 1.0.4 add file
* 9f0ca3d 1.0.4 mkdir test
* a830346 1.0.3 commit again
* d5da851 1.0.2 third commit
* a5d9609 1.0.1 second commit
* 2a78fd3 1.0.0 start use git
* e957e45 1.0.0 start use git
```

当Git无法自动合并分支时，就必须首先解决冲突。解决冲突后，再提交，合并完成。

解决冲突就是把Git合并失败的文件手动编辑为我们希望的内容，再提交。

用`git log --graph`命令可以看到分支合并图。

### 管理策略

通常，合并分支时，如果可能，Git会用`Fast forward`模式，但这种模式下，删除分支后，会丢掉分支信息。

如果要强制禁用`Fast forward`模式，Git就会在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息。

准备合并分支，使用`--no-ff`参数，表示禁用`Fast forward`：

```bash
$ git merge --no-ff -m "merge with no-ff" dev
```

这样，即使分支被删除，也会保留commit信息。

### Bug分支

修复bug时，我们会通过创建新的bug分支进行修复，然后合并，最后删除；

当手头工作没有完成时，先把工作现场`git stash`一下，然后去修复bug，修复后，再`git stash pop`，回到工作现场；

在master分支上修复的bug，想要合并到当前dev分支，可以用`git cherry-pick <commit>`命令，把bug提交的修改“复制”到当前分支，避免重复劳动。

### Feature分支

发一个新feature，最好新建一个分支；

如果要丢弃一个没有被合并过的分支，可以通过`git branch -D <name>`强行删除。

### 多人协作

- 查看远程库信息，使用`git remote -v`；
- 本地新建的分支如果不推送到远程，对其他人就是不可见的；
- 从本地推送分支，使用`git push origin branch-name`，如果推送失败，先用`git pull`抓取远程的新提交；
- 在本地创建和远程分支对应的分支，使用`git checkout -b branch-name origin/branch-name`，本地和远程分支的名称最好一致；
- 建立本地分支和远程分支的关联，使用`git branch --set-upstream branch-name origin/branch-name`；
- 从远程抓取分支，使用`git pull`，如果有冲突，要先处理冲突。`git fetch`也是从远程拉取，但是不会合并，简单来讲，`git pull` = `git fetch` + `git merge`



## 标签管理

tag就是一个让人容易记住的有意义的名字，它跟某个commit绑在一起

### 创建标签

- 命令`git tag <tagname>`用于新建一个标签，默认为`HEAD`，也可以指定一个commit id；
- 命令`git tag -a <tagname> -m "blablabla..."`可以指定标签信息；
- 命令`git tag`可以查看所有标签。
- 命令`git show <tagname>`查看标签信息

### 操作标签

- 命令`git push origin <tagname>`可以推送一个本地标签；
- 命令`git push origin --tags`可以推送全部未推送过的本地标签；
- 命令`git tag -d <tagname>`可以删除一个本地标签；
- 命令`git push origin :refs/tags/<tagname>`可以删除一个远程标签。



## Git自定义

### 忽略文件

- 忽略某些文件时，需要编写`.gitignore`；
- `.gitignore`文件本身要放到版本库里，并且可以对`.gitignore`做版本管理！

有些时候，你想添加一个文件到Git，但发现添加不了，原因是这个文件被`.gitignore`忽略了：

```
$ git add App.class
The following paths are ignored by one of your .gitignore files:
App.class
Use -f if you really want to add them.
```

如果你确实想添加该文件，可以用`-f`强制添加到Git：

```
$ git add -f App.class
```

或者你发现，可能是`.gitignore`写得有问题，需要找出来到底哪个规则写错了，可以用`git check-ignore`命令检查：

```
$ git check-ignore -v App.class
.gitignore:3:*.class	App.class
```

Git会告诉我们，`.gitignore`的第3行规则忽略了该文件，于是我们就可以知道应该修订哪个规则。



### 搭建Git服务器

第一步，安装`git`：

```
$ sudo apt-get install git
```

第二步，创建一个`git`用户，用来运行`git`服务：

```
$ sudo adduser git
```

第三步，创建证书登录：

收集所有需要登录的用户的公钥，就是他们自己的`id_rsa.pub`文件，把所有公钥导入到`/home/git/.ssh/authorized_keys`文件里，一行一个。

第四步，初始化Git仓库：

先选定一个目录作为Git仓库，假定是`/srv/sample.git`，在`/srv`目录下输入命令：

```
$ sudo git init --bare sample.git
```

Git就会创建一个裸仓库，裸仓库没有工作区，因为服务器上的Git仓库纯粹是为了共享，所以不让用户直接登录到服务器上去改工作区，并且服务器上的Git仓库通常都以`.git`结尾。然后，把owner改为`git`：

```
$ sudo chown -R git:git sample.git
```

第五步，禁用shell登录：

出于安全考虑，第二步创建的git用户不允许登录shell，这可以通过编辑`/etc/passwd`文件完成。找到类似下面的一行：

```
git:x:1001:1001:,,,:/home/git:/bin/bash
```

改为：

```
git:x:1001:1001:,,,:/home/git:/usr/bin/git-shell
```

这样，`git`用户可以正常通过ssh使用git，但无法登录shell，因为我们为`git`用户指定的`git-shell`每次一登录就自动退出。

第六步，克隆远程仓库：

现在，可以通过`git clone`命令克隆远程仓库了，在各自的电脑上运行：

```
$ git clone git@server:/srv/sample.git
Cloning into 'sample'...
warning: You appear to have cloned an empty repository.
```

剩下的推送就简单了。



#### 注意

出现`Permission denied (publickey).`检查服务器上`/home/git/.ssh` 的权限

```bash
root@ubuntu:/home/git# ll
total 36
drwxr-xr-x 6 git       git  4096 Jan 22 22:07 ./
drwxr-xr-x 4 root      root 4096 Jan 22 19:36 ../
-rw-r--r-- 1 git       git   220 Jan 22 19:36 .bash_logout
-rw-r--r-- 1 git       git  3771 Jan 22 19:36 .bashrc
drwx------ 4 git       git  4096 Jan 22 21:03 .cache/
drwx------ 4 git       git  4096 Jan 22 21:03 .config/
drwxr-xr-x 3 git       git  4096 Jan 22 21:03 .local/
-rw-r--r-- 1 git       git   807 Jan 22 19:36 .profile
drwx------ 2 root root 4096 Jan 22 22:07 .ssh/
```

出现这种情况是因为`.ssh`文件是用root用户创建的，需要修改.ssh文件权限

```bash
root@ubuntu:/home/git# chown -R git:git .ssh/
```




# Git 命令详解

## Git add

<span id = "git_add">git add 参数详解</span>

### git add <path>

把我们<path>添加到索引库中，<path>可以是文件也可以是目录。

### git add -u [<path>]

把<path>中所有tracked文件中被修改过或已删除文件的信息添加到索引库。它不会处理untracted的文件。省略<path>表示.(英文符号**.**),即当前目录。

### git add -A: [<path>]

把<path>中所有tracked文件中被修改过或已删除文件和所有untracted的文件信息添加到索引库。省略<path>表示.,即当前目录。

### git add -i [<path>]

查看<path>中被所有修改过或已删除文件但没有提交的文件，
并通过其revert子命令可以查看<path>中所有untracted的文件，同时进入一个子命令系统。

例如：

```bash
$ git add -i 
		staged     unstaged path
1:        +0/-0      nothing branch/t.txt
2:        +0/-0      nothing branch/t2.txt
3:    unchanged        +1/-0 readme.txt
*** Commands ***
1: [s]tatus     2: [u]pdate     3: [r]evert     4: [a]dd untracked
5: [p]atch      6: [d]iff       7: [q]uit       8: [h]elp
What now>
```

这里的t.txt和t2.txt表示已经被执行了git add，待提交。即已经添加到索引库中。readme.txt表示已经处于tracked下，它被修改了，但是还没有被执行了git add。即还没添加到索引库中。

#### revert

把已经添加到索引库中的文件从索引库中剔除。

（3: [r]evert）表示通过3或r或revert加回车执行该命令。执行该命令后，git会例出索引库中的文件列表.
然后通过数字来选择。输入"1"表示git会例出索引库中的文件列表中的第1个文件。
"1-15"表示git会例出索引库中的文件列表中的第1个文件到第15个文件.回车将执行。
如果我们不输入任何东西，直接回车，将结束revert子命令，返回git add -i的主命令行。

#### update

可以通过update子命令（2: [u]pdate）把已经tracked的文件添加到索引库中。其操作和revert子命令类似。

#### add untracked

通过add untracked子命令（4: [a]dd untracked）可以把还没被git管理的文件添加到索引库中。其操作和revert子命令类似。

#### diff

可以通过diff子命令（6: [d]iff）可以比较索引库中文件和原版本的差异。其操作和revert子命令类似。

#### status

status子命令(1: [s]tatus)功能上和git add -i相似

#### qiut

quit子命令（7: [q]uit）用于退出git add -i命令系统

### git add -h

查看帮助



# 辅助功能

## github操作

<span id = "github">github操作</span>

### 建立版本库

New repository

### 配置`SSH key`

头像——》`Settings`——》`SSH and GPG keys`——》`New SSH key`

### 配置本地用户

```bash
git config --global user.name "icedragon817"
git config --global user.email  "st_love_forever@163.com"
```

