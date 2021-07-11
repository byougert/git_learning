git config --global user.name "byyoung"
git config --global user.email "byougert@qq.com"
--global参数，表示你这台机器上所有的Git仓库都会使用这个配置

git init
把当前目录变成Git可以管理的仓库

git add readme.txt
把文件添加到仓库

git commit -m "wrote a readme file"
把已添加的所有文件一次性提交到仓库，可以同时提交多个已添加的文件

git status
仓库当前的状态

git diff readme.txt
查看不同

git log
显示从最近到最远的提交日志

git log --graph --pretty=oneline --abbrev-commit
--graph 显示分支图
--pretty=oneline 单行格式
--abbrev-commit 只显示commit_id前几个字符，而非所有

git reset --hard HEAD^
回退到上一个版本
HEAD表示当前版本，HEAD^表示上一个版本，HEAD^^表示上上个版本，HEAD~100表示上100个版本

git reset --hard 1094a
1094a为commit id，回退到指定版本，可以是未来的版本

git reflog
记录每一次命令，以便确定要回到未来的哪个版本

git restore readme.txt
让这个文件回到最近一次git commit或git add时的状态。

git reset HEAD readme.txt
把暂存区的修改回退到工作区。

场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令git resotre file。
场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令git reset HEAD <file>，就回到了场景1，第二步按场景1操作。
场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，参考版本回退一节，不过前提是没有推送到远程库。

git rm test.txt
在工作区删除文件，工作区和版本库就不一致了。当确实要从版本库中删除该文件，那就用命令git rm删掉，并且git commit

git restore test.txt
在工作区删除文件，发现删错了，因为版本库里还有呢，所以可以很轻松地把误删的文件恢复到最新版本
git restore其实是用版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以“一键还原”。
注意：从来没有被添加到版本库就被删除的文件，是无法恢复的！

git remote
查看远程库信息，-v 查看更详细的信息

git remote add origin git@github.com:byougert/git_learning.git
将当前仓库与远程仓库关联

git push -u origin master
把当前分支master推送到远程
-u Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令
第一次加即可，后续不用添加

git push origin master
只要本地作了提交，把本地master分支的最新修改推送至GitHub

git push origin dev
把本地dev分支的最新修改推送至GitHub

git remote rm origin
解除了本地和远程的绑定关系

git switch -c dev
创建并选择dev分支

git switch -c dev origin/dev
创建并选择dev分支，并将其与origin/dev关联，但只是关联，并不会主动pull

git pull
主动从远程仓库拉取

git branch
列出所有分支，当前分支前面会标一个*号

git branch -d dev
删除dev分支，如果dev分支尚未被merge，会报错，可以使用git branch -D dev强行删除

git merge dev
合并指定分支到当前分支
默认合并方式为Fast-forward：直接把master指向dev的当前提交

git merge --no-ff -m "merge with no-ff" dev
--no-ff 禁止以Fast-forward合并，在merge时生成一个新的commit，-m表示commit的信息
合并分支时，加上--no-ff参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而fast forward合并就看不出来曾经做过合并。

git stash
把当前工作现场“储藏”起来，等以后恢复现场后继续工作

git stash list
查看保存的现场

git stash apply
恢复现场，但是恢复后，stash内容并不删除，需要用git stash drop来删除

git stash pop
恢复的同时把stash内容也删了

git stash apply stash@{0}
恢复到指定现场

git cherry-pick 4c805e2
复制4c805e2这个提交所做的修改，并不是把整个master分支merge过来

git clone git@github.com:byougert/git_learning.git
将远程仓库复制到本地，但默认只有master分支

git branch --set-upstream-to=origin/dev dev
指定本地dev分支与远程origin/dev分支的链接


多人协作
阅读: 114845980
当你从远程仓库克隆时，实际上Git自动把本地的master分支和远程的master分支对应起来了，并且，远程仓库的默认名称是origin。

要查看远程库的信息，用git remote：

$ git remote
origin
或者，用git remote -v显示更详细的信息：

$ git remote -v
origin  git@github.com:michaelliao/learngit.git (fetch)
origin  git@github.com:michaelliao/learngit.git (push)
上面显示了可以抓取和推送的origin的地址。如果没有推送权限，就看不到push的地址。

多人协作的工作模式通常是这样：
首先，可以试图用git push origin <branch-name>推送自己的修改；
如果推送失败，则因为远程分支比你的本地更新，需要先用git pull试图合并；
如果合并有冲突，则解决冲突，并在本地提交；
没有冲突或者解决掉冲突后，再用git push origin <branch-name>推送就能成功！
如果git pull提示no tracking information，则说明本地分支和远程分支的链接关系没有创建，用命令git branch --set-upstream-to <branch-name> origin/<branch-name>

git tag <name>
打一个新标签，也即快照，默认标签是打在最新提交的commit上的

git tag <name> commit_id
在指定commit_id上打标签

git tag
查看所有标签

git tag -a v0.1 -m "version 0.1 released" 1094adb
创建带有说明的标签，用-a指定标签名，-m指定说明文字

git show <tagname>
查看标签信息

git tag -d v0.1
删除本地标签

git push origin :refs/tags/<tagname>
删除一个远程标签

git push origin <tagname>
推送某个标签到远程

git push origin --tags
一次性推送全部尚未推送到远程的本地标签

git config --global color.ui true
让Git显示颜色，会让命令输出看起来更醒目

git config --global --add configName configValue
新增，可以不用--add
git config  --global --unset configName
删除
git config --global configName
查看