title: Git syntax
date: 2016-06-28 17:23:47
tags: git
---

#### 1，git起步：

git基本上都是本地操作，三种文件状态关系见图示：（Git 的工作目录，暂存区域，以及本地仓库）


`/etc/gitconfig `文件：系统中对所有用户都普遍适用的配置。若使用` git config` 时用 `--system` 选项，读写的就是这个文件。
`~/.gitconfig `文件：用户目录下的配置文件只适用于该用户。若使用 `git config` 时用` --global `选项，读写的就是这个文件。
当前项目的 Git 目录中的配置文件（` .git/config `）。这里的配置仅仅针对当前项目有效。

<!-- more -->


>下面 的配置将会覆盖上面的配置，查看配置：`git  config --list`。
获取帮助：`git help` ，例如：`git help config`会用默认浏览器打开说明。还可以寻求[网上帮助](irc.freenode.net)。

<br>

#### 2，git基础：

##### 创建项目：

* 创建项目：
`git init`(目录下生成.git文件)；
`git add .`（跟踪当前目录所有文件）；
`git add xx`（将修改文件提交暂存区）；
`git commit -m "submit info"`（提交到本地库并写上版本说明）；

* 从现有库克隆：
`git clone git://github.com/schacon/grit.git`（从远程克隆grit项目）
`git clone git://github.com/schacon/grit.git mygrit`（从远程克隆项目并重命名为mygrit）
> git:// 协议， http(s):// 或者 (user@server:/path.git ) SSH 传输协议。

##### 更新到仓库：

通过`git status`查看当前文件状态：

在`.gitignore`文件中配置被忽略文件或目录，支持glob匹配模式（简化的shell正则表达式），例子：
```
# 此为注释 – 将被 Git 忽略
*.a    # 忽略所有 .a 结尾的文件
!lib.a    # 但 lib.a 除外
/TODO    # 仅仅忽略项目根目录下的 TODO 文件，不包括 subdir/TODO
build/    # 忽略 build/ 目录下的所有文件
doc/*.txt    # 会忽略 doc/notes.txt 但不包括 doc/server/arch.txt
doc/**/*.txt     # ignore all .txt files in the doc/ directory
```

* 查看已暂存和未暂存：`git diff`；查看已暂存和本地库：`git diff --cached`。
* 提交更新：`git commit -m "xxx"`；跳过暂存提交：`git commit -a -m "yyyy"`。
* 移除文件：`git rm xxx`（需要先`rm xxx`删除文件不再跟踪再执行`git rm xxx`记录此删除，如果已经提交到暂存区，则需要用`-f`参数强制删除） ；从本地仓库删除保留工作空间文件（取消跟踪）使用：`git rm --cached readme.txt`。
* 移动文件：`git mv file_from  file_to`，此命令等效于：`mv README.txt README`，`$ git rm README.txt`，`$ git add README`三条命令一起执行。

##### 查看历史及撤销操作：

`git log`（按从最近到以前排列所有提交历史）；
`git log -p -2` （列出每次提交内容差异，只显示最近2次更新）；可以使用图形工具查看历史。
更多历史查看格式化，显示输出，参数，参阅书籍或者[访问网站](http://www.git-scm.com/book/zh/v1/Git-%E5%9F%BA%E7%A1%80-%E6%9F%A5%E7%9C%8B%E6%8F%90%E4%BA%A4%E5%8E%86%E5%8F%B2)。

* 撤销操作：
`git commit --amend`修改最后一次提交，例如：补充上次提交时候忘记的文件：
1 ` git commit -m 'initial commit'`，
2 ` git add forgotten_file`，
3 ` git commit --amend`
`git reset HEAD benchmarks.rb`，取消已暂存文件：（状体回到已修改未暂存）
`git checkout -- benchmarks.rb`，取消对文件的修改：（未暂存）

##### 使用远程仓库：

`git remote`，查看远程库，如果是本地init出来的没有，从远程库clone下来的项目才有。
`git remote -v`，显示对应克隆地址。
`git remote add [shortname] [url]`，添加远程仓库，shrotname可以代替长url地址。
`git fetch [shortname]`可以抓取信息，只是抓取不合并分支，它有对应的自己分支：[shortname]/branch。`git pull`会合并分支。
`git push [remote-name] [branch-name]`，推送信息到远程（需要有写权限）。
`git remote show [remote-name]`，查看远程库。
`git remote rename`，更改远程仓库名称； `git remote rm`，删除远程仓库名称。

##### 打标签及小技巧：

`git tag`，列出所有标签，例如：`git tag -l  'v1.2.*'`，列出所有以v1.2开头的标签。
`git tag -a v1.4 -m 'my version 1.4'`，新建标签带注释。
`git show v1.4`，查看相应标签版本信息。
如果有私钥可以再标签上添加GPG信息。更多标签信息参阅书籍或者[网站](http://www.git-scm.com/book/zh/v1/Git-%E5%9F%BA%E7%A1%80-%E6%89%93%E6%A0%87%E7%AD%BE)。

* git小技巧：
 * 代码自动补全：bash shell下有效。（windows下安装msysGit即可）
 * git命令别名：`git config --global alias.co checkout`，当前用户设置'co'代替'checkout'命令；甚至是长指令`git config --global alias.last 'log -1 HEAD'`，都可以。

-----------------
<br>

#### 3，Git分支：

执行`git add README test.rb LICENSE`，`git commit -m 'initial commit of my project'`后，git仓库中有5个对象，如图：
5个对象为：三个文件快照的blob对象，一个目录树对象（包含blob对象索引），包含提交信息的commit对象。

每次提交的commit对象关系，及默认的master分支示意图，默认情况是有一个HEAD指针（当前版本指针）指向master。
执行`git branch testing`，产生新的分支。
执行`git checkout testing`，当前版本指针指向testing。
利用HEAD指针可以在分支之间切换并修改。

##### 分支创建与合并：
`git checkout -b iss53`，创建并切换到iss53分支，修改bug。相当于执行了`branch和checkout`两条指令。（注意切换分支前保证工作区是干净的）
`git checkout master`，`git merge iss53`，切换回master分支，并将iss53分支合并到master分支上。
`git branch -d iss53`，删除iss53分支。
合并分支的时候遇到冲突：通过`git status`查看，在`=======`上面是HEAD当前所在分支的内容，可以手动选择并修改。
` git mergetool`，可以调用可视化工作来解决合并冲突问题。
<span style="color: red ;">最后合并完成，记得提交到版本库`git commit`！</span>

##### 分支管理及开发工作流程：
`git branch`，显示所有分支`*`标志当前所在分支，`git branch -v`，分支最后一次提交对象(commit)信息。
`git branch --merged`，查看哪些分支被并入当前分支；`git branch --no-merged`，未合并分支。
`git branch -d testing`，删除分支，记得合并之后再删除，大写的D表示强制删除。

* 开发工作流程：
一般保留master分支为稳定版本，开发过程另起分支，之后再合并，过程中往往会有多个分支一起工作。

同样的版本示意图，下面的看的更明白：


##### 远程分支：

远程分支表示：(远程仓库名)/(分支名)，`origin/master `，

从远程克隆了一个项目（提交对象为f42c5时），默认为"orgin/master"分支，之后自己的master分支已经修改。

执行`git fetch origin`同步远程数据到本地（不合并）。
`git remote add [shrotname] [url]`，把当前项目分支加为远程分支之一。添加多个远程版本库情况：

更新其中一条分支：


* 远程分支操作：
 * `git push (远程仓库名) (分支名)`，推送本地分支到对应的远程仓库分支。
 * `git push origin serverfix:awesomebranch`，推送本地的serverfix分支到远程的awesomebranch分支。
 * 如果是`git fetch origin serverfix `下来的新项目是无法在本地编辑的，只有一个无法移动的指针，可以`git merge origin/serverfix`合并到当前分支，或者在远程分支上分化出一个分支进行开发：`git checkout -b serverfix origin/serverfix`。

* 跟踪分支：
 * 从远程分支`checkout`出来的本地分支，称为"跟踪分支"。`git push和git pull`都会直接在对应远程分支上获取和推送。
 * `git checkout -b [分支名] [远程名]/[分支名]`在1.6.2版本上可以用`git checkout --track origin/serverfix`代替。
 * clone下来的项目跟踪的是master分支。
 * 删除远程分支`git push origin :serverfix`即本地分支代替为空，推送到相应分支，将分支置空。

##### 分支的衍合：(慎用)

* 把一个分支中的修改整合到另一个分支的办法有两种：`merge` 和 `rebase`(衍合)。（一个是把其他分支合并到本分支，一个是把本分支合并到其他分支）。
1 `merge `命令，它会把两个分支最新的快照（C3 和 C4）以及二者最新的共同祖先（C2）进行三方合并，合并的结果是产生一个新的提交对象（C5）。

2 `git checkout experiment , git rebase master`，切换到experiment分支，并衍合到master分支。

3 一个多次分支的项目，现在要把client分支跳过server分支直接合并到master分支中。

4 执行：`git rebase --onto master server client`，取出 client 分支，找出 client 分支和 server 分支的共同祖先之后的变化，然后把它们在 master 上重演一遍

5 执行：`git checkout master 和 git merge client`，完成合并分支。

6 `git rebase master server`，再合并server和master分支。

7 `git checkout master 和 git merge server`，完成合并。
`git branch -d client 和 git branch -d server`，删除分支。

>注意：<span style="color: red;">一旦分支中的提交对象发布到公共仓库，就千万不要对该分支进行衍合操作。</span>如果把衍合当成一种在推送之前清理提交历史的手段，而且仅仅衍合那些尚未公开的提交对象，就没问题。如果衍合那些已经公开的提交对象，并且已经有人基于这些提交对象开展了后续开发工作的话，就会出现叫人沮丧的麻烦。

#### 实际应用：

在提交代码的之前，执行下以下命令:
 git rm -r --cached .
git add .
git commit -m "fixed untracked files"
之后gitignore就生效了








