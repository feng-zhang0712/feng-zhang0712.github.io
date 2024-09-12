# Git

## Workspace：工作区

Index / Stage：暂存区
Repository：仓库区（或本地仓库）
Remote：远程仓库

## Git 常用命令

一、新建代码库

git init：在当前目录新建一个 Git 代码库
git clone [url]：下载一个项目和它的整个代码历史

二、配置

Git 的设置文件为 .gitconfig，它可以在用户主目录下（全局配置），也可以在项目目录下（项目配置）。
git config --list：显示当前的Git配置
git config -e [--global]：编辑Git配置文件
git config [--global] user.name "[name]"：设置提交代码时的用户信息
git config [--global] user.email "[email address]"：设置提交代码时的用户信息

三、增加/删除文件

git add [file1] [file2] …：添加指定文件到暂存区
git add [dir]：添加指定目录到暂存区，包括子目录
git add .：添加当前目录的所有文件到暂存区
git add -p：添加每个变化前，都会要求确认。对于同一个文件的多处变化，可以实现分次提交
git rm [file1] [file2] …：删除工作区文件，并且将这次删除放入暂存区
git rm --cached [file]：停止追踪指定文件，但该文件会保留在工作区
git mv [file-original] [file-renamed]：改名文件，并且将这个改名放入暂存区

四、代码提交

git commit -m [message]：提交暂存区到仓库区
git commit [file1] [file2] ... -m [message]：提交暂存区的指定文件到仓库区
git commit -a：提交工作区自上次 commit 之后的变化，直接到仓库区
git commit -v：提交时显示所有 diff 信息
git commit --amend -m [message]：使用一次新的commit，替代上一次提交。如果代码没有任何新变化，则用来改写上一次 commit 的提交信息
git commit --amend [file1] [file2] …：重做上一次commit，并包括指定文件的新变化

五、分支

git branch：列出所有本地分支
git branch -r：列出所有远程分支
git branch -a：列出所有本地分支和远程分支
git branch [branch-name]：新建一个分支，但依然停留在当前分支
git checkout -b [branch]：新建一个分支，并切换到该分支
git branch [branch] [commit]：新建一个分支，指向指定commit
git branch --track [branch] [remote-branch]：新建一个分支，与指定的远程分支建立追踪关系
git checkout [branch-name]：切换到指定分支，并更新工作区
git checkout -：切换到上一个分支
git branch --set-upstream [branch] [remote-branch]：在现有分支与指定的远程分支之间，建立追踪关系
git rebase
git merge [branch]：合并指定分支到当前分支
git cherry-pick [commit]：选择一个 commit，合并进当前分支
git branch -d [branch-name]：删除分支
git push origin --delete [branch-name]：删除远程分支
git branch -dr [remote/branch]：删除远程分支

六、标签

git tag：列出所有tag
git tag [tag]：新建一个tag在当前commit
git tag [tag] [commit]：新建一个tag在指定commit
git tag -d [tag]：删除本地tag
git push origin :refs/tags/[tagName]：删除远程tag
git show [tag]：查看tag信息
git push [remote] [tag]：提交指定tag
git push [remote] --tags：提交所有tag
git checkout -b [branch] [tag]：新建一个分支，指向某个tag

七、查看信息

git status：显示有变更的文件
git log：显示当前分支的版本历史
git log --stat：显示commit历史，以及每次commit发生变更的文件
git log -S [keyword]：搜索提交历史，根据关键词
git log [tag] HEAD --pretty=format:%s：显示某个commit之后的所有变动，每个commit占据一行
git log [tag] HEAD --grep feature：显示某个commit之后的所有变动，其"提交说明"必须符合搜索条件
git log --follow [file]：显示某个文件的版本历史，包括文件改名
git whatchanged [file]：显示某个文件的版本历史，包括文件改名
git log -p [file]：显示指定文件相关的每一次diff
git log -5 --pretty --oneline：显示过去5次提交
git shortlog -sn：显示所有提交过的用户，按提交次数排序
git blame [file]：显示指定文件是什么人在什么时间修改过
git diff：显示暂存区和工作区的差异
git diff --cached [file]：显示暂存区和上一个commit的差异
git diff HEAD：显示工作区与当前分支最新commit之间的差异
git diff [first-branch]...[second-branch]：显示两次提交之间的差异
git diff --shortstat "@{0 day ago}"：显示今天你写了多少行代码
git show [commit]：显示某次提交的元数据和内容变化
git show --name-only [commit]：显示某次提交发生变化的文件
git show [commit]:[filename]：显示某次提交时，某个文件的内容
git reflog：显示当前分支的最近几次提交

八、远程同步

git fetch [remote]：下载远程仓库的所有的更新
git remote -v：显示所有远程仓库
git remote show [remote]：显示某个远程仓库的信息
git remote add [shortname] [url]：增加一个新的远程仓库，并命名
git pull [remote] [branch]：取回远程仓库的变化，并与本地分支合并
git push [remote] [branch]：上传本地指定分支到远程仓库
git push [remote] --force：强行推送当前分支到远程仓库，即使有冲突
git push [remote] --all：推送所有分支到远程仓库

九、撤销

git checkout [file]：恢复暂存区的指定文件到工作区
git checkout [commit] [file]：恢复某个commit的指定文件到暂存区和工作区
git checkout .：恢复暂存区的所有文件到工作区
git reset [file]：重置暂存区的指定文件，与上一次commit保持一致，但工作区不变
git reset --hard：重置暂存区与工作区，与上一次 commit 保持一致
git reset [commit]：重置当前分支的指针为指定 commit，同时重置暂存区，但工作区不变
git reset --hard [commit]：重置当前分支的HEAD为指定 commit，同时重置暂存区和工作区，与指定 commit 一致
git reset --keep [commit]：重置当前HEAD为指定 commit，但保持暂存区和工作区不变
git revert [commit]：新建一个commit，用来撤销指定commit。后者的所有变化都将被前者抵消，并且应用到当前分支
git stash / git stash pop：暂时将未提交的变化移除，稍后再移入

Git cherry-pick

一、基本用法

git cherry-pick 命令的作用，是将指定的提交（commit）应用于其他分支。
$ git cherry-pick <commitHash>

上面命令将提交 commitHash，应用于当前分支。这会在当前分支产生一个新的提交，它们的哈希值不同。

git cherry-pick 命令的参数，也可以是分支名，表示转移该分支的最新提交。
$ git cherry-pick feature

上面代码表示将 feature 分支的最近一次提交，转移到当前分支。

二、转移多个提交

Cherry pick 支持一次转移多个提交。
$ git cherry-pick <HashA> <HashB>

上面的命令将 A 和 B 两个提交应用到当前分支。这会在当前分支生成两个对应的新提交。

如果想要转移一系列的连续提交，可以使用下面的简便语法。
$ git cherry-pick A..B

上面的命令可以转移从 A 到 B 的所有提交。它们必须按照正确的顺序放置：提交 A 必须早于提交 B，否则命令将失败。
注意，使用上面的命令，提交 A 将不会包含在 Cherry pick 中。如果要包含提交 A，可以使用下面的语法。
$ git cherry-pick A^..B 

三、配置项

git cherry-pick 命令的常用配置项如下。
1. -e，--edit
打开外部编辑器，编辑提交信息。
2. -n，--no-commit
只更新工作区和暂存区，不产生新的提交。
3. -x
在提交信息的末尾追加一行(cherry picked from commit ...)，方便以后查到这个提交是如何产生的。
4. -s，--signoff
在提交信息的末尾追加一行操作者的签名，表示是谁进行了这个操作。
5. -m parent-number，--mainline parent-number
如果原始提交是一个合并节点，来自于两个分支的合并，那么 Cherry pick 默认将失败，因为它不知道应该采用哪个分支的代码变动。
-m 配置项告诉 Git，应该采用哪个分支的变动。它的参数 parent-number 是一个从1开始的整数，代表原始提交的父分支编号。
$ git cherry-pick -m 1 <commitHash>

上面命令表示，Cherry pick 采用提交commitHash来自编号1的父分支的变动。
一般来说，1号父分支是接受变动的分支（the branch being merged into），2号父分支是作为变动来源的分支（the branch being merged from）。

四、代码冲突

如果操作过程中发生代码冲突，Cherry pick 会停下来，让用户决定如何继续操作。
1. --continue
用户解决代码冲突后，第一步将修改的文件重新加入暂存区（git add .），第二步使用下面的命令，让 Cherry pick 过程继续执行。
$ git cherry-pick --continue

2. --abort
发生代码冲突后，放弃合并，回到操作前的样子。
3. --quit
发生代码冲突后，退出 Cherry pick，但是不回到操作前的样子。
五、转移到另一个代码库
Cherry pick 也支持转移另一个代码库的提交，方法是先将该库加为远程仓库。
$ git remote add target git://gitUrl

上面命令添加了一个远程仓库 target。
然后，将远程代码抓取到本地。
$ git fetch target

上面命令将远程代码仓库抓取到本地。
接着，检查一下要从远程仓库转移的提交，获取它的哈希值。
$ git log target/master

最后，使用 git cherry-pick 命令转移提交。
$ git cherry-pick <commitHash>

撤销操作

一、撤销提交

如果提交代码以后，想要撤销这次提交，可以使用下面的命令。
$ git revert HEAD

上面命令的原理是，在当前提交后面，新增一次提交，抵消掉上一次提交导致的所有变化。它不会改变过去的历史，所以是首选方式，没有任何丢失代码的风险。

git revert 命令只能抵消上一个提交，如果想抵消多个提交，必须在命令行依次指定这些提交。比如，抵消前两个提交，要像下面这样写。
$ git revert [倒数第一个提交] [倒数第二个提交]

git revert 命令还有两个参数。
--no-edit：执行时不打开默认编辑器，直接使用 Git 自动生成的提交信息。
--no-commit：只抵消暂存区和工作区的文件变化，不产生新的提交。
二、丢弃提交
如果希望以前的提交在历史中彻底消失，而不是被抵消掉，可以使用 git reset 命令，丢弃掉某个提交之后的所有提交。
$ git reset [last good SHA]

git reset 的原理是，让最新提交的指针回到以前某个时点，该时点之后的提交都从历史中消失。

默认情况下，git reset 不改变工作区的文件（但会改变暂存区），--hard 参数可以让工作区里面的文件也回到以前的状态。
$ git reset --hard [last good SHA]

执行 git reset 命令之后，如果想找回那些丢弃掉的提交，可以使用 git reflog 命令，具体做法参考这里。不过，这种做法有时效性，时间长了可能找不回来。
三、替换上一次提交
git commit 命令的 --amend 参数，可以修改上一次的提交信息。
$ git commit --amend -m "Fixes bug #42"

它的原理是产生一个新的提交对象，替换掉上一次提交产生的提交对象。
这时如果暂存区有发生变化的文件，会一起提交到仓库。所以，--amend 不仅可以修改提交信息，还可以整个把上一次提交替换掉。
四、撤销工作区的文件修改
如果工作区的某个文件被改乱了，但还没有提交，可以用 git checkout 命令找回本次修改之前的文件。
$ git checkout -- [filename]

它的原理是先找暂存区，如果该文件有暂存的版本，则恢复该版本，否则恢复上一次提交的版本。
注意，工作区的文件变化一旦被撤销，就无法找回了。
五、从暂存区撤销文件
如果不小心把一个文件添加到暂存区，可以用下面的命令撤销。
$ git rm --cached [filename]

上面的命令不影响已经提交的内容。
六、撤销当前分支的变化
如果在当前分支上做了几次提交，突然发现放错了分支，这几个提交本应该放到另一个分支。
# 新建一个 feature 分支，指向当前最新的提交
# 注意，这时依然停留在当前分支
$ git branch feature

# 切换到这几次提交之前的状态
$ git reset --hard [当前分支此前的最后一次提交]

# 切换到 feature 分支

$ git checkout feature

上面的操作等于是撤销当前分支的变化，将这些变化放到一个新建的分支。
参考：如何撤销 Git 操作
git bisect 命令
git bisect 用来查找哪一次代码提交引入了错误。
它的原理很简单，是将代码提交的历史，按照二分法不断缩小定位。所谓"二分法"，就是将代码历史一分为二，确定问题出在前半部分，还是后半部分，不断执行这个过程，直到范围缩小到某一次代码提交。
本文通过一个实例，解释如何使用这个命令。下面是一个代码库，请将它克隆到本地。
网页上是一个计数器，有两个按钮。点击 + 号按钮，可以看到计数器没有递增，反而递减，这说明代码有问题。
现在，就要来查找，到底哪一次代码提交，引入了错误。首先，检查一下代码提交历史。
$ git log --pretty=oneline

可以看到，这个库一共有101次提交。最早的第一次提交的哈希是 4d83cf。

git bisect start 命令启动查错，它的格式如下。
$ git bisect start [终点] [起点]

上面代码中，"终点"是最近的提交，"起点"是更久以前的提交。它们之间的这段历史，就是差错的范围。
这个例子中，我们选择全部的代码历史。起点是第一次提交 4d83cf，终点是最近一次的 HEAD。当然，指定其他范围也可以。
$ git bisect start HEAD 4d83cf

执行上面的命令以后，代码库就会切换到这段范围正当中的那次提交，本例是第51次提交。
现在刷新浏览器，点击+按钮，发现可以正常递增。使用 git bisect good 命令，标识本次提交（第51次）没有问题。
$ git bisect good

既然第51次提交没有问题，就意味着错误是在代码历史的后半段引入的。执行上面的命令，Git 就自动切换到后半段的中点（第76次提交）。
现在刷新浏览器，点击+按钮，发现不能正常递增。使用 git bisect bad 命令，标识本次提交（第76）有问题。
$ git bisect bad

执行上面的命令以后，Git 就自动切换到第51次到第76次的中点（第63次提交）。
接下来，不断重复这个过程，直到成功找到出问题的那一次提交为止。这时，Git 会给出如下的提示。
b47892 is the first bad commit
既然找到那个有问题的提交，就可以检查代码，确定具体是什么错误。
然后，使用 git bisect reset 命令，退出查错，回到最近一次的代码提交。
$ git bisect reset

现在就可以开始修复错误了。
参考：git bisect 命令教程
Git 分支管理策略
参考：Git 分支管理策略
git merge 与 git rebase
git merge
git merge 用于将两个分支的历史记录合并在一起。执行 git merge 时，会创建一个新的合并提交（merge commit），保留两个分支的所有历史记录。

工作方式

假设你有两个分支，feature 和 main：
main:    A---B---C
                        \
feature:             D---E
当前处于 main 分支，执行 git merge feature 时，一个新的合并提交被创建，历史记录如下：
main:    A---B---C-------F
                        \       /
feature:             D---E
F 是合并提交，包含 main 和 feature 分支的所有更改。
优点
保留所有提交历史：合并提交保留了两个分支的所有历史记录。
解决冲突清晰：在合并提交中可以清晰地看到冲突的解决情况。
缺点：对于频繁合并的项目，历史记录中会出现大量的合并提交，可能显得混乱。
git rebase
git rebase 用于将一个分支的更改应用到另一个分支的顶部，从而重写提交历史。它不创建新的合并提交，而是将目标分支的所有提交移动到基底分支之后。
工作方式
同样假设有两个分支，feature 和 main：
main:    A---B---C
                        \
feature:             D---E
当前处于 feature 分支，执行 git rebase main，feature 分支的更改（D和E）会被应用到 main 分支顶部。
main:    A---B---C---D'---E'
D' 和 E' 是重新应用的提交，它们具有新的哈希值。
优点
更清晰的历史记录：历史记录会更加线性和清晰，易于阅读。
简化的提交历史：避免了大量的合并提交，使提交历史更简洁。
缺点
复杂的冲突解决：如果在 rebase 过程中遇到冲突，每次冲突都需要解决并继续 rebase。
修改历史：rebase 会重写提交历史，因此在公共分支（如main）上使用时需谨慎，避免影响他人。
实践建议
merge 用于公共分支：在处理包含多个开发者的公共分支时，建议使用 merge，因为它保留了所有历史记录，解决冲突也更加清晰。
rebase 用于个人分支：在处理个人分支时，使用 rebase 可以保持提交历史的整洁和线性。
