![这里写图片描述](http://img.blog.csdn.net/20170112105739374?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZG9uZ3FpdXNoYW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

Git 三个工作区域关系


**本地库关联远程库，在本地仓库目录运行命令：**
```
 $git remote add origin git@github.com:nanfei9330/learngit.git
```

**提示出错信息：fatal: remote origin already exists.**
```
$ git remote rm origin
```

之后再add origin

---
# 原理
## git结构
![git基本结构](http://172.16.1.120:8080/wp-content/uploads/git/git-struct.png)
## 原理探索
1. 分布式
2. 离线可提交
3. 链表式管理
  1.  打分支成本为0，与svn相比
  2. 可以支持rebase这样的操作
  3. 可以有子树合并的操作
  4. 。。。。


# git clone
## 用法一
`git clone <repository> <directory>`
## 用法二
`git clone --bare <repository> <directory.git>`
裸版本库，没有work copy
## 用法三
`git clone --mirro <repository> <directory.git>`
与2的区别是，克隆出来裸版本库对上游库进行了注册，这样可以在裸版本库中使用git fetch命令和上游版本库进行持续同步

# git add
1. git add -u –update 将本地有改动（包括修改和删除）的文件标记到暂存区 update tracked files
2. git add -n dry run
3. git add -A -all add changes from all tracked and untracked files
4. git add -i 交互式add
5. git add -f 强制添加，如强制添加被忽略的文件
6. 解决冲突也是这个
7. `git add dir/` 在库中添加目录时，如果目录本身时也是个git库时用

# git commit
1. git commit --allow-empty -m "blank commit" 空提交
2. git commit -s 提交日志中加入Signed-off-by: user1 <user1@xxx.com>
3. `git commit -C <commit>`  重用commit的提交日志

# git branch
## 用法
  1. `git branch`
  2. `git branch <branchname>`
  3. `git branch <banchname> <start-point>`
  4. `git branch -d <branchname>`
  5. `git branch -D <branchname>`
  6. `git branch -m <oldbranch> <newbranch>`
  7. `git branch -M <oldbranch> <newbranch>`

说明
  * 用法1显示本地分支列表
  * 用法2和3创建分支，2基于当前HEAD指向的提交创建分支，3基于start-point创建新分支，新分支的分支名为branchname
  * 4和5产出分支，4在删除分支时会检查所要删除分支是否已经合并到其他分支中，否则拒绝删除；用法5强制删除
  * 6和7重命名分支，如果版本库中已经存在branchname的分支，用法6拒绝执行重命名，7会强制执行
## other
1. git branch 显示当前分支&本地的分支情况
2. git branch -a 显示当前分支&本地的分支情况&远程分支的情况
3. git branch -v 显示当前牌哪个分支，其他还包括SHA1，commit meesage
4. `git checkout -b <new_branch> [<start-point>]` 创建分支后，立即切换到新分支上
5. git branch -r 显示远程分支

# git blame
1. git blame -L, n,m filename
  * -L 2,+1 表示从第2行开始，只有一行，+2才是2行

# git config
建议默认更改global的name和email
1. git config –list 检查已有的配置信息
2. git config –global user.name "xxx" 如果有global 会更改全局的配置，没有的话只设置当前项目
3. git –git-dir=/xxx/xxx config user.name xxxx 更改特定目录下git仓库的配置
4. git config –global alias.lg "log –color –graph –pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' –abbrev-commit" 一个感觉不错的git log配置
5. git config branch.xxxx.rebase ture 执行pull操作时，用rebase代替merge

# git cat-file
1. git cat-file -t 2a1a132 显示对象的类型
2. git cat-file -p 2a1a132 显示对象内容

# git checkout
## 用法一
`git checkout [-q] [<commit>] [--] <paths> ...`
commit可选，省略=从暂存区进行检出，一般用于覆盖工作区（**如果<commit>不省略，也会替换暂存区中相应的文件**），reset重置暂存区
## 用法二
`git checkout [<branch>]`
切换分支，如果省略<branch>，汇总显示工作区，暂存区与HEAD的差异
## 用法三
`git checkout [-m] [[-b | --orphan] <new_branch>] [<start_point>]`
创建切换分支，新分支从<start_point>指定的提交开始创建
## example
1. git checkout branch 切换到branch分支，更新HEAD以指向branch分支，以及用branch指向的树更新暂存区和工作区
2. git checkout 汇总显示工作区，暂存区与HEAD的差异
3. git checkout HEAD 同上
4. git checkout --filename 用暂存区filename文件来覆盖工作区中的filename文件，相当于取消自上次执行git add filename以来的本地修改 reset为将文件撤出暂存区
5. git checkout branch(可以代表sha1的都可) --filename 维持HEAD的指向不变，用branch所指向的提交中的filename替换暂存区和工作区中相应的文件，注意会将暂存区和工作区中的filename文件直接覆盖
6. `git checkout -- .` 或 `git checkout . ` 用暂存区的所有文件直接覆盖工作区文件，不给提示
7. git checkout --track
  * 基于本地分支创建分支时，建立与远程库的追踪关系
  * 也可跟踪远程分支，当跟踪远程分支时，可以不用写本地的分支名，这时默认用远程分支名来创建
8. git checout -b local_branch_name remote_branch_name


# git reset
## 用法一
`git reset [-q] [<commit>] [--] <paths>...`
commit可选，省略相当于用HEAD的指向作为提交ID
不重置暂存区，不改变工作区，用指定提交状态下的文件替换掉暂存区中的文件
1. git reset -- filename 同 git reset HEAD filename  相当于将filename撤出暂存区，因为用提交状态下的filename替换了暂存区的filename，而这时又不会重置工作区，所以相当于撤出暂存区
## 用法二
`git reset [--soft | --mixed | --hard | --merge | --keep] [-q] [<commit>]`
commit可选，省略相当于用HEAD的指向作为提交ID
1. git reset --mixed  更改暂存区和本地库引用 --mixed为默认参数
2. git reset --soft   仅更本地库引用
3. git reset --hard   本地库引用，暂存区，工作区全部改变
4. git reset   仅用head指向的目录树，重置暂存区，工作区不受影响，相当于将之前git add加到暂存区的内容撤出暂存区。引用未改变，因为将引用重置到HEAD相当于没有重置
5. git reset HEAD 同4
6. git reset --soft HEAD^ **不要用这个**
   工作区暂存区不变，但是引用向前回退一次，当对最新的提交说明或提交的更改不满意时，撤销最新的提交以便重新提交
7. git reset --hard HEAD^
彻底撤销最近的提交，引用回退到前一次，工作区，暂存区都回退到前一次。自上次以来的提交全部丢失


# git cherry
检测当前的分支的改是否在被检测的分支上，如果是+则没有，-有
git cherry -v branchname

# git cherry-pick
`git cherry-pick [<options>] <commit-ish>...`
1. 冲突的情况下，不会自动提交，需要先解冲突，再自己提交一般情况下用git commit -C(重用某个commit的提交日志)
2. 不冲突的情况下，会自动提交

# git rebase
git rebase commit-sha1
想把A分支rebase到master，需要在A分支上执行，与merge正好相反
如果rebase过程中有冲突，先解冲突，然后执行git rebase --continue

# git merge
git merge [选项 …] 将其他分支的提交和当前分支的提交进行合并
支持多个代表的分支和当前分支进行合并
1. –no-commit选项，默认情况下合并后的结果会自动提交，加上这个选项，合并后的结果会放入暂存区
2. –no-ff 非快进式merge 要用这个merge，**不要用快进式的**

# cherry-pick与rebase比较
## cherry-pick
1. cherry-pick会深拷贝commit所对应的对象到所在分支上，这时版本库中会存在两份一样的对象，一个在被cherry-pick的分支中，一个在cherry-pick所在的分支中，虽然对象一样，但是sha1不一样
2. 在当前分支执行，后面的sha1，指要拣选过来的commit

## rebase
1. 将分支a变基到master中，先切换到a分支上，然后强制将a重置到master上，然后找到a和master共同祖先依次拣选a分支的各个提交，因此如果产生冲突时，冲突文件会将a分支中的标记为他人的
2. 将分支a变基到master中，在a分支中执行git rebase master
3. 变基后原来分支的每个提交没有删除，只是不能通过常规手段访问了

# git diff
1. `git diff <commit1> <commit2> -- <paths>` commit1:a;commit2:b
2. git diff 工作区与暂存区  工作区文件是b
3. git diff HEAD 工作区与HEAD  工作文件是b
4. git diff --cached|staged 暂存区与HEAD  暂存区是b
5. git diff A B 比较里程碑A和里程碑B
6. git diff A 工作区和里程碑A
7. git diff --cached A 暂存区和里程碑A
8. git diff <path1> <path2> 非Git目录/文件差异比较
9. git diff --word-diff 逐词比较

# git fetch
1. git fetch --no-tags 不将远程版本库的里程碑引入本地版本库
2. git fetch [remote-name]（可选） [分支名]（可选） 抓取远程数据  只抓取不合并 需要手动合并 啥都不带全都搞回来

#git push
1. git push [remote-name] [branch-name] | [tagname]  推送到远程仓库
2. git push <远程名>  :<远程分支名>|<远程tag名> 删除远程分支远程tag
3. git push [远程仓库名] [分支名]:[远程分支名]  将分支推送到仓库（这个可重命名[远程分支]） 或 git push [远程仓库名] [分支名]
  * 第一个分支名是本地分支
  * 第二个分支是远程分支名
  * 这两上分支均用正常的表过方式，不用要origin/branch-name这种形式，是靠位置区分的，远程仓库中没有origin这种形式

# 远程仓库
## 信息
1. git ls-remote 可加参数 --tags|--heads， 不加的话显示所有
2. git branch -r 显示远程分支不是远程库的分支，是拉到本地的远程分支
3. git show-ref 显示本地所有refs
4. git remote show <remote-name>
## 分支追踪
1. git checkout --track
  * 基于本地分支创建分支时，建立与远程库的追踪关系
  * 也可跟踪远程分支，当跟踪远程分支时，可以不用写本地的分支名，这时默认用远程分支名来创建
2. 建立追踪时的config配置
  ```
[branch "master"]
	remote = origin
	merge = refs/heads/master
  ```
## 远程版本库
1. git remote add <remote-name> <giturl> 添加远程库
2. git remote -v 显示远程信息
3. 更改远程库
  * 修改config（两种方法：1. 直接编辑2. 用config命令）
  * git remote set-url <remote-name> --push <giturl> 只更改push的url
  * git remote set-url <remote-name> <giturl> 更改全部
3. git remote rename <old-name> <new-name>  更改远程版本库的名称
4. 远程版本库更新
  * git remote update
  * 关闭某个库的更新  git config remote.user2.skipDefaultUpdate true
5. git remote rm <name> 删除远程库

# git remote
1. git remote add <remote-name> <giturl> 添加远程库
2. git remote -v 显示远程信息
3. 更改远程库
  * 修改config（两种方法：1. 直接编辑2. 用config命令）
  * git remote set-url <remote-name> --push <giturl> 只更改push的url
  * git remote set-url <remote-name> <giturl> 更改全部
3. git remote rename <old-name> <new-name>  更改远程版本库的名称
4. 远程版本库更新
  * git remote update  对每个源进行fetch操作
  * 关闭某个库的更新  git config remote.user2.skipDefaultUpdate true
5. git remote rm <name> 删除远程库
6. git remote add --no-tags xxxname giturl 不将远程版本库引入本地
7. `git remote show <remote-name>` 可以显示哪些分支被删除了
8. `git remote prune <remote-name>` 将仓库中已删除的分支清除掉

# git ls-remote
1. git ls-remote 显示远程库的所有tag,head,master
2. git ls-remote origin my* 在origin中搜索my开头相关的，注意通配符
3. git ls-remote --tags只显示里程碑
4. git ls-remote --tags git://xxxxxx tag*
5. git ls-remote --heads
6. git ls-remote --heads git://xxxxx xxx*

# 冲突
1. 放弃合并git reset 实际上是对暂存区进行重置，用库中HEAD对暂存区进行重置
2. .git目录中文件解释
  * .git/MERGE_HEAD 所合并的提交ID，合并是哪个提交
  * .git/MERGE_MODE 标识合并状态
  * .git/MERGE_MSG 记录合并失败的信息
     ```
     Merge branch 'master' of /Users/unimen/smdr/write-code/learn_code/git-quan/path/to/repos/shared
     # Conflicts:
     #	doc/README
     ```
3. git add 解决冲突
4. 解决树冲突
  * git rm 删除不需要的文件
  * git add 添加最后协定的文件


# git tag
## 显示里程碑
1. git tag 列出已有的tag
2. git tag -n<num> 最多显示num行说明（个人理解为每个tag多少行）
3. git tag -l 'xxxx*' 搜索tag 可以支持通配符
4. git log --decorate在提交历史里显示tag
5. git describe

## 创建里程碑

1. `git tag <tagname> [<commit>]`
2. `git tag -a <tagname> [<commit>]`
3. `git tag -m <msg> <tagname> [<commit>]`
4. `git tag -s <tagname> [<commit>]`
5. `git tag -u <key-id> <tagname> [<commit>]`

说明
1. 1创建轻量里程碑
2. 2和3相同，创建带说明的里程碑，其中3直接通过-m参数提供
3. 4和5相同，创建带GnuPG签名的里程碑，其中5用-u参数选择指定的私钥进行签名
4. 创建里程碑输入里程碑的名字tagname和一个可选的提交id，如果没有提交id，则基于HEAD创建里程碑

## 删除里程碑
1. git tag -d mytag

## 修改里程碑
不是修改里程碑的名字，而是修改里程碑的重向，要慎重
git tag -f | --force

## 共享里程碑
1. git push origin mytag
2. git push origin refs/tags/*把所有的tag都push
3. git pull origin refs/tags/mytags2:refs/tags/mytags2强制拉回tag
  * A用户提交mytag2
  * B用户pull后，强制更新mytag2
  * A用户必须用上面的强制更新
1. 如果本地已有同名的版本库，默认不会从上游同步tag，里程碑一旦创建，不要再修改
2. tag共享必须显式推送
3. pull或fetch，自动从远程版本库获取里tag，并在本地版本库中创建，而且只会将获取的远程分支所包含的新tag同步到本地，不会将远程版本库的其他分支中的tag获取到本地
4. 远程仓库中tag被删除后，pull或fetch是不能拉下来的
   * git tag | xargs git tag -d; git pull  先将本地的tag全删除掉，再重新拉

## 删除远tag
`git push <remote_url>  :<tagname>`
git push origin  :mytag2
理解<refs>:<refs> 这里是用空值推送到远程库相应的应用中

## other
3. git tag -a v1.4 -m 'xxxxx' 如果没有tag信息 会打开编辑器   创建含附注tag
4. git show v1.4 显示v1.4 tag的详细信息
5. git tag v1.2  轻量级标签
6. git tag -a v1.2 9fceb02 提交对象校验和（可以是前几位字符） 补打tag
7. git push origin [tagname]  推送一个指定标签
8. git push origin -- tags 推送所有标签
9. `git push <remote_url>  :<tagname>` 删除tag
10. `git rev-parse mytag2^{commit} | mytag2^{} | mytag2^0 | mytag2~0`获得mytag2对象所指向的提交对象id

# git rm

# HEAD,master,HEAD^各种含义
1. master：分支master中最新的提交，也可以用全称refs/heads/master呀heads/master
2. HEAD: 版本库中最近的一次提交
3. ^：指代父提交
  * HEAD^:最近一交提交的父提交
  * HEAD^^:HEAD^的父提交
4. 对于一个提交有多个父提交，可以在^号后面用数据表示是第几个父提交
  * a573106^2:提交a5的多个父提交的中的第二个父提交
  * HEAD^1:相当于HEAD^
  * HEAD^^2：HEAD^的多个父提交中的第二个父提交
5. ~<n>用于指代父提交
  * a573106~5:a5^^^^^
6. a573106^{tree}:提交所对应的树对象
  * 注意默认就应该带一个^，在这里带一个^不是指父提交
7. a573106:path/to/file 提交对应的文件对象 **没有实验成功**
8. :path/to/file 暂存区的文件对象 **没有实验成功**

# 获取历史版本
1. `git ls-tree <tree-ish> <paths>` 查看历史提交的目录树
  * git ls-tree 776c5c9 README
  * git ls-tree -r refs/tags/D doc
2. `git checkout <commit>` 整体工作区切换到历史版本
  * git checkout HEAD^^
3. `git chekcout <commit> -- <paths>` 检出某文件的历史版本
  * git chekcout refs/tags/D -- README
  * git checkout 776c5c9 -- doc
4. `git show <commit>:<file> > new_name` 检出某文件的历史版本到其他文件名
  * git show 887113d:README > READMEOLD

# 撤销操作
1. git commint --amend ；可以重新编辑上次提交时的message；或将遗漏的加进暂存再提交，SHA1会改变，会将上次提交的sha删掉，用这次的提交的新sha1给覆盖掉
2. gti reset HEAD filename 取消暂存
3. git checkout -- filename 取消更改  只有文件没有进暂存时生效  进了暂存要先取消暂存，加上HEAD就不用先撤出index了

# ignore
## 语法
1. 空行或#被忽略
2. 可以使用通配符，参见Linux手册：glob(7)。如星号(*)代表任意多个字符，问号（?）代表一个字符，方括号([abc])代表可选字符
3. 如果名称的最前面是一个路径分隔符(/)，表明要忽略的文件在此（当前）目录下，而非子目录中的文件，避免递归
4. 如果名称的最后面是一个路径分隔符(/)，表明要忽略的是整个目录，同名文件不忽略，默认文件和目录都忽略
5. 通过在前面加一个（！）,代表不忽略
```
*.a #忽略所有以.a为扩展名的文件
!lib.a #不忽略lib.a
/TODO #只忽略当前目录下的TODO文件，子目录中的TODO不忽略
build/ #忽略所有build/目录下的文件
doc/*.txt 忽略文件如doc/notes.txt，但是文件如 doc/server/arch.txt则不忽略
```
## 本地忽略
1. .git/info/exclude 只针对单个git仓库
2. core.excludesfile 本地全局的，所有git仓库都起作用
```
git config --global core.excludesfile /home/unimen/.gitignore
```

# 版本范围表示
1. 在一个版本前面加上^，排除这个版本极其历史版本
```git rev-list --oneline ^G D```
2. 点点表示法，使用两个点连接两个版本，如G..D 相当于^G D
3. 版本取反，参数的顺序不重要，但是点点表示法前后的版本顺序很重要
  * B..C == ^B C
  * C..B == ^C B
4. 三点表示法含义是两个版本共同能够访问到的除外，如B...C将B和C共同能够访问到的除外；和顺序无关B...C==C...B
5. r1^@r1提交的历史提交，但r1除外
6. r1^!提交本身，不包括其历史提交

# 子树合并
## 子目录方式合并外部版本库
想在main库master分支中，以子目录lib合并外部版本库sub-lib，假设现在main库的master分支中
1. `git remote add sub-lib sub-lib.git`
2. `git fetch sub-lib`
2. `git checkout -b sub-lib-branch sub-lib/master`
3. `git checkout master`
4. `git read-tree --prefix=lib sub-lib-branch` read-tree 只更新暂存区
5. `git checkout -- lib`
6. `git write-tree`
7. 执行下面的命令
   ```
  echo "subtree merge" | \
  git commit-tree sha1(git write-tree的输出)  \
  -p sha1(HEAD) \
  -p sha1(sub-lib-branch)
  ```
8. 将master分支重置到上面的命令输出的sha1上`git reset sha1`
## 利用子树合并跟踪上游改动
1. 将跟踪的上游代码拉下来
  * git chekcout sub-lib-branch
  * git pull
2. gco master
3. 根据版本，执行下面的命令
  * git merge -s subtree sub-lib-branch
  * `git merge -Xsubtree=lib sub-lib-branch` git version>=1.7 这些版本默认使用recursive合并策略
## git subtree
1. 添加子目录，建立与git项目的关联
  * git remote add -f <子仓库名> <子仓库地址>
    * -f：添加完远程仓库后立即执行fetch操作
  * git subtree add --prefix=<目录名> <子仓库名> <子仓库分支> --squash
    * squash：把subtree的改动合并成一次提交，这样可以不用拉取整个子项目的所有历史提交记录
2. `git subtree pull --prefix=<目录名> <子仓库名> <子仓库分支>`
3. `git subtree push --prefix=<目录名> <子仓库名> <子仓库分支>`
4. `git subtree merge --prefix=<目录名> <子仓库名> <子仓库分支>`
5. --squash 可以用在add merge  pull push中

# git revert
1. git revert HEAD
HEAD提交的反转提交，同时工作区也会跟着变动，不能单独撤回某个文件；使用前提暂存区和工作区必须干净

# git show
1. git show HEAD~1:file  显示 HEAD父提交中的file内容
2. `git show <commit>:<file> > new_name` 检出文件的历史版本到其他文件名
3. `git show :2:<file>` 显示暂存区编号2中文件内容
4. `git show <commit-sha1>` 显示某次提交所有改变的文件及内容

# git rev-parse
1. git rev-parse master|HEAD|refs/heads/master 显示引用对应的提交id
2. git rev-parse --symbolic --branches 显示分支
3. git rev-parse --symbolic --tags 显示所有里程碑
4. git rev-parse --symbolic --glob=refs/* 显示所有引用
5. git rev-parse master refs/heads/master一次显示这两个SHA1
6. git rev-parse 6652 6652a0d 依次显示出完整的SHA1
7. git rev-parse A refs/tags/A  依次显示两个里程碑对象的SHA1
8. git rev-parse A^{} A^0 A^{commit} 依次显示三个里程碑所指向的提交对象的sha1
9. git rev-parse :/"xxxx" 在提交日志中查找字符串的方式显示提交

# git describe
1. 如果该提交恰好被打一个tag，则显示tag的名字
2. 若提交没有对应的tag，但是在其祖先版本上建有里程碑，刚使用类似`<tag>-<num>-g<commit>`的格式显示
  * `<tag>`最接近的祖先提交的里程碑名字
  * `<num>`该里程碑和提交之间的距离
  * `<commit>`该提交的精简commit id
3. 如果对工作区文件有修改，可以通过后缀--dirty表示出来 有的话会在最后面显示dirty
4. --always 如要没有tag显示精简提交ID，这里如果不加--always会报错
5. --tags可以将轻量级里程碑用作版本描述符，默认是不用的

# git reflog
git reflog show master
git reflog
1. refname@{n} 引用refname之前第n次改变时的sha1值

# git stash
1. git stash 保存当前工作进度
2. git stash list 显示列表
3. `git stash pop [--index] [<stash>] `
  * 不用任何参数，会恢复最新保存的工作进度，并将恢复的工作进度从存储的工作进度列表中删除
  * 如果提供`[<stash>]`参数（来自于git stash list），则从<stash>中恢复，恢复完成后，会删除相应的
  * 选项--index除了恢复工作区的文件外，还尝试恢复暂存区
4. `git stash apply [--index] [<stash>]` 不删除恢复的进度，其余和git stash pop一样
5. git stash drop [<stash>] 删除一个存储的进度，默认删除最近的进度
6. git stash clear 删除所有进度
7. git stash branch <branchname> <stash> 基于进度创建分支
8. `git stash [save [--patch]] [-k | --[no-]keep-index] [-q|--quiet] [<message>]`
  * 必须使用这样的格式`git stash save "message"`
  * 使用--patch参数，会显示工作区和HEAD的差异，通过对差异文件的编辑决定在进度中最终要保存的工作区的内容
  * -k 或--keep-index 参数，在保存进度后不会将暂区重置，默认会将暂存区和工作区强制重置

# git archive
1. git archive -o lateset.zip HEAD 基于最新提交建立归档文件latest.zip
2. git archive -o partial.tar HEAD src doc 只净目录src和doc建立到归档partial.tar中
3. git archive --format=tar --prefix=1.0/ v1.0 | gzip > foo-1.0.tar.gz 基于里程碑v1.0创建归档，并且为归档中的文件添加目录前缀1.0

# 注意事项
1. 禁止非快进式推送，用快进式推送,git push -f
2. merge时，不使用快进式merge
3. 一旦向服务器推送后，如果发现错误，不要使用会更改历的操作（变基、reset），而是采用不会改变历史提交的反转提交等操作，一般不要用amend，一个自己merge，一个是让别人merge
4. 里程碑不要随便更改

# Git Tag

```
/// 查看标签
// 打印所有标签
git tag
// 打印符合检索条件的标签
git tag -l 1.*.*
// 查看对应标签状态
git checkout 1.0.0

/// 创建标签(本地)
// 创建轻量标签
git tag 1.0.0-light
// 创建带备注标签(推荐)
git tag -a 1.0.0 -m "这是备注信息"
// 针对特定commit版本SHA创建标签
git tag -a 1.0.0 0c3b62d -m "这是备注信息"

/// 删除标签(本地)
git tag -d 1.0.0

/// 将本地标签发布到远程仓库
// 发送所有
git push origin --tags
// 指定版本发送
git push origin 1.0.0

/// 删除远程仓库对应标签
// Git版本 > V1.7.0
git push origin --delete 1.0.0
// 旧版本Git
git push origin :refs/tags/1.0.0
```

####什么时候应该用 git merge？
1. local分支还需要继续前进，也就是是一个well-known的分支。
   比如feature分支，bugfix分支就不可以使用git rebase

####什么时候应该使用rebase？
1. 只要local分支上需要rebase的所有commits历史没有被push过，就可以安全的使用git rebase操作。
2. 对于不再有子分支的branch，并且为rebase而会被重写的commits都还没有push分享过，可以比较安全的进行rebase操作。
tag
git rebase -i HEAD～4 //将最近4次提交重新提交
//参数
p,pick:直接使用该次提交
r,reword:使用该次提交，但重新编辑提交信息
e,edit:停止到该次提交，通过`git commit --amend`追加提交，完毕之后不要忘记使用`git rebase --continue`完成这此rebase
s,squash,压缩提交，将和上一次提交合并为一个提交
x,exec,运行命令
d,drop,移除这次提交

# Commands:
# p, pick = use commit
# r, reword = use commit, but edit the commit message
# e, edit = use commit, but stop for amending //执行git commit --amend "修改之后的message"，再执行git rebase --continue即可将该提交的message修改并返回到最新状态
# s, squash = use commit, but meld into previous commit
# f, fixup = like "squash", but discard this commit's log message
# x, exec = run command (the rest of the line) using shell

"git rebase --onto base from to"
命令的意义使用(from, to]所指定的范围内的所有commit在base这个commit之上进行重建(左开右闭)

# --preserve-merges
#   Instead of ignoring merges, try to recreate them.  试着重新创建merges，而不是忽略他们。

git rebase <upstream-branch-name> <to-branch-name>
执行上述命令的过程为：
1. 切换到to-branch分支；
2. 将to-branch中比upstream-branch多的commit先撤销掉，并将这些commit放在一块临时存储区（.git/rebase）；
3. 将upstream-branch中比to-branch多的commit应用到to-branch上，此刻to-branch和upstream-branch的代码状态一致；
4. 将存放的临时存储区的commit重新应用到to-branch上；
5. 结束。
执行完上述第3步后，to-branch的代码状态已经改变，接着执行第4步时则可能会产生合并冲突。

解决合并冲突几个常见的办法是：
1. 手动编辑冲突文件，手动删除或者保留冲突的代码；
2. 对于“both added”、“both deleted”、“both modified”等类型的冲突，若想完整地保留某一方的修改可以执行git checkout --ours(或者--theirs) <文件名>来选择想要保留的版本。需要注意的是由于git rebase 是先撤销再应用commit，所以这里的ours指的是upstream-branch，theirs指的是我们将要应用的临时commit。
3. 对于“added by us/them”、“deleted by us/them”等类型的冲突需要使用git rm <file-name>和git add <file-name>来删除/添加file。在此过程中需要特别注意谁是us，谁是them。

冲突解决完之后，使用git add <file-name>来标记冲突已解决，最后执行git rebase --continue继续。

