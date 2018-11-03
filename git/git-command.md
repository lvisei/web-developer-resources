### Git常用命令收集

#### Git修改远程仓库地址
1. ##### 修改命令
```
git remote set-url origin [url]
例如：
git remote set-url origin gitlab@gitlab.chumob.com:php/hasoffer.git
```
2. ##### 先删后加
```
git remote rm origin
git remote add origin [url]
```
3. ##### 创建一个分支
```
git checkout -b <newbranch> <soruceBranch>
# 把分支推送到远程库
git push origin <newbranch>
```
4. ##### 查看所有的分支情况
```
git branch -a #查看所有的分支包含远程分支
git branch --set-upstream-to=origin/dev dev #设置拉取和推送的分支
```
5. ##### 标签操作

```
git tag -a "<name>" -m "<message>" #创建一个标签
git tag -l  #查看所有的tag列表
git push --tags #推送所有的标签
git push origin --delete tag <tagname> #删除远程tag
git tag -d <tagname> #删除本地tag
git push origin :refs/tags/<tagname> #推送一个tag到远程
```

6. ##### 代码冲突常见解决方法
 如果希望保留生产服务器上所做的改动,仅仅并入新配置项, 处理方法如下:

```
git stash
git pull
git stash pop
```

### 本地创建并关联远程git

1. #### 本地创建git仓库

```
git init
```

2. #### 关联远程git

```
git remote add origin url
```
3. #### 把本地内容推送到远程

```
git push -u origin master
```
4. #### 查看状态

```
git status
```



然后可以使用git diff -w +文件名 来确认代码自动合并的情况.

反过来,如果希望用代码库中的文件完全覆盖本地工作版本. 方法如下:
git reset --hard
git pull
其中git reset是针对版本,如果想针对文件回退本地修改,使用
git checkout HEAD file/to/restore

#### 清理git库

```
# 用下面的命令从归档历史中清理已删除的文件，并重写commit记录。
$ git filter-branch --tag-name-filter cat --index-filter 'git rm -r --cached --ignore-unmatch FILENAME ' --prune-empty -f -- --all
#将FILENAME替换为文件名，多个以空格分隔， 可以用*匹配目录。
执行完成后，归档历史已经清理完成，但还有一些垃圾文件，用下面的命令清理.
$ rm -rf .git/refs/original/
$ git reflog expire --expire=now --all
$ git gc --prune=now
$ git gc --aggressive --prune=now
#ok,到此，大功告成，du -sh . 可以看到成果。
```

#### push优化后的代码到远处仓库

```
$ git push origin --force --all
$ git push origin --force --tags
```



#### 查看、添加、提交、删除、找回，重置修改文件


```
git help <command> # 显示command的help
git show # 显示某次提交的内容 git show $id
git checkout -- <file> # 抛弃工作区修改
git checkout . # 抛弃工作区修改
git add <file> # 将工作文件修改提交到本地暂存区
git add . # 将所有修改过的工作文件提交暂存区
git rm <file> # 从版本库中删除文件
git rm <file> --cached # 从版本库中删除文件，但不删除文件
git reset <file> # 从暂存区恢复到工作文件
git reset -- . # 从暂存区恢复到工作文件
git reset --hard # 恢复最近一次提交过的状态，即放弃上次提交后的所有本次修改
git ci <file> git ci . git ci -a # 将git add, git rm和git ci等操作都合并在一　　　　　　　　　　　　　
git ci --amend # 修改最后一次提交记录
git revert <$id> # 恢复某次提交的状态，恢复动作本身也创建次提交对象
git revert HEAD # 恢复最后一次提交的状态
```


#### 查看文件diff

```
git diff <file> # 比较当前文件和暂存区文件差异 git diff
git diff <id1><id2> # 比较两次提交之间的差异
git diff <branch1>..<branch2> # 在两个分支之间比较
git diff --staged # 比较暂存区和版本库差异
git diff --cached # 比较暂存区和版本库差异
git diff --stat # 仅仅比较统计信息
```


#### 查看提交记录

```
git log git log <file> # 查看该文件每次提交记录
git log -p <file> # 查看每次详细修改内容的diff
git log -p -2 # 查看最近两次详细修改内容的diff
git log --stat #查看提交统计信息
tig
```


Mac上可以使用tig代替diff和log，brew install tig

### Git 本地分支管理
#### 查看、切换、创建和删除分支

```
git branch -r # 查看远程分支
git branch <new_branch> # 创建新的分支
git branch -v # 查看各个分支最后提交信息
git branch --merged # 查看已经被合并到当前分支的分支
git branch --no-merged # 查看尚未被合并到当前分支的分支
git chekout <branch> # 切换到某个分支
git chekout -b <new_branch> # 创建新的分支，并且切换过去
git chekout -b <new_branch> <branch> # 基于branch创建新的new_branch
git chekout $id # 把某次历史提交记录checkout出来，但无分支信息，切换到其他分支会自动删除
git chekout $id -b <new_branch> # 把某次历史提交记录checkout出来，创建成一个分支
git branch -d <branch> # 删除某个分支
git branch -D <branch> # 强制删除某个分支 (未被合并的分支被删除的时候需要强制)
```


####  分支合并和rebase

```
git merge <branch> # 将branch分支合并到当前分支
git merge origin/master --no-ff # 不要Fast-Foward合并，这样可以生成merge提交
git rebase master <branch> # 将master rebase到branch，相当于： git co <branch> && git rebase master && git co master && git merge <branch>
 Git补丁管理(方便在多台机器上开发同步时用)
git diff > ../sync.patch # 生成补丁
git apply ../sync.patch # 打补丁
git apply --check ../sync.patch #测试补丁能否成功
```


####  Git暂存管理

```
git stash # 暂存
git stash list # 列所有stash
git stash apply # 恢复暂存的内容
git stash drop # 删除暂存区
```


#### Git远程分支管理

```
git pull # 抓取远程仓库所有分支更新并合并到本地
git pull --no-ff # 抓取远程仓库所有分支更新并合并到本地，不要快进合并
git fetch origin # 抓取远程仓库更新
git merge origin/master # 将远程主分支合并到本地当前分支
git chekout --track origin/branch # 跟踪某个远程分支创建相应的本地分支
git chekout -b <local_branch> origin/<remote_branch> # 基于远程分支创建本地分支，功能同上
git push # push所有分支
git push origin master # 将本地主分支推到远程主分支
git push -u origin master # 将本地主分支推到远程(如无远程主分支则创建，用于初始化远程仓库)
git push origin <local_branch> # 创建远程分支， origin是远程仓库名
git push origin <local_branch>:<remote_branch> # 创建远程分支
git push origin :<remote_branch> #先删除本地分支(git br -d <branch>)，然后再push删除远程分支
```


#### Git远程仓库管理


```
git remote -v # 查看远程服务器地址和仓库名称
git remote show origin # 查看远程服务器仓库状态
git remote add origin git@ github:robbin/robbin_site.git # 添加远程仓库地址
git remote set-url origin git@ github.com:robbin/robbin_site.git # 设置远程仓库地址(用于修改远程仓库地址) git remote rm <repository> # 删除远程仓库
```


#### 创建远程仓库

```
git clone --bare robbin_site robbin_site.git # 用带版本的项目创建纯版本仓库
scp -r my_project.git git@ git.csdn.net:~ # 将纯仓库上传到服务器上
mkdir robbin_site.git && cd robbin_site.git && git --bare init # 在服务器创建纯仓库
git remote add origin git@ github.com:robbin/robbin_site.git # 设置远程仓库地址
git push -u origin master # 客户端首次提交
git push -u origin develop # 首次将本地develop分支提交到远程develop分支，并且track
git remote set-head origin master # 设置远程仓库的HEAD指向master分支也可以命令设置跟踪远程库和本地库
git branch --set-upstream master origin/master
git branch --set-upstream develop origin/develop
```




### git 解决本地冲突

```
处理的方式非常简单，主要是使用git stash命令进行处理，分成以下几个步骤进行处理。
1、先将本地修改存储起来
$ git stash
这样本地的所有修改就都被暂时存储起来 。是用git stash list可以看到保存的信息：
git stash暂存修改
git stash暂存修改
其中stash@{0}就是刚才保存的标记。
2、pull内容
暂存了本地修改之后，就可以pull了。
$ git pull
3、还原暂存的内容
$ git stash pop stash@{0}
系统提示如下类似的信息：
Auto-merging c/environ.c
CONFLICT (content): Merge conflict in c/environ.c
意思就是系统自动合并修改的内容，但是其中有冲突，需要解决其中的冲突。
4、解决文件中冲突的的部分
打开冲突的文件，会看到类似如下的内容：
git冲突内容
git冲突内容
其中Updated upstream 和=====之间的内容就是pull下来的内容，====和stashed changes之间的内容就是本地修改的内容。碰到这种情况，git也不知道哪行内容是需要的，所以要自行确定需要的内容。
解决完成之后，就可以正常的提交了。
```

### git常用命令
#### 查看文件diff

```
git diff # 比较当前文件和暂存区文件差异 git diff
git diff # 比较两次提交之间的差异
git diff .. # 在两个分支之间比较
git diff –staged # 比较暂存区和版本库差异
git diff –cached # 比较暂存区和版本库差异
git diff –stat # 仅仅比较统计信息
```

#### 查看提交记录

```
git log git log # 查看该文件每次提交记录
git log -p # 查看每次详细修改内容的diff
git log -p -2 # 查看最近两次详细修改内容的diff
git log –stat #查看提交统计信息
tig
Mac上可以使用tig代替diff和log，brew install tig
Git 本地分支管理
查看、切换、创建和删除分支
git br -r # 查看远程分支
git br # 创建新的分支
git br -v # 查看各个分支最后提交信息
git br –merged # 查看已经被合并到当前分支的分支
git br –no-merged # 查看尚未被合并到当前分支的分支
git co # 切换到某个分支
git co -b # 创建新的分支，并且切换过去
git co -b # 基于branch创建新的new_branch
git co id # 把某次历史提交记录checkout出来，但无分支信息，切换到其他分支会自动删除  
git coid -b # 把某次历史提交记录checkout出来，创建成一个分支
git br -d # 删除某个分支
git br -D # 强制删除某个分支 (未被合并的分支被删除的时候需要强制)
分支合并和rebase
git merge # 将branch分支合并到当前分支
git merge origin/master –no-ff # 不要Fast-Foward合并，这样可以生成merge提交
git rebase master # 将master rebase到branch，相当于： git co && git rebase master && git co master && git merge
Git补丁管理(方便在多台机器上开发同步时用)
git diff > ../sync.patch # 生成补丁
git apply ../sync.patch # 打补丁
git apply –check ../sync.patch #测试补丁能否成功
Git暂存管理
git stash # 暂存
git stash list # 列所有stash
git stash apply # 恢复暂存的内容
git stash drop # 删除暂存区
Git远程分支管理
git pull # 抓取远程仓库所有分支更新并合并到本地
git pull –no-ff # 抓取远程仓库所有分支更新并合并到本地，不要快进合并
git fetch origin # 抓取远程仓库更新
git merge origin/master # 将远程主分支合并到本地当前分支
git co –track origin/branch # 跟踪某个远程分支创建相应的本地分支
git co -b origin/ # 基于远程分支创建本地分支，功能同上
git push # push所有分支
git push origin master # 将本地主分支推到远程主分支
git push -u origin master # 将本地主分支推到远程(如无远程主分支则创建，用于初始化远程仓库)
git push origin # 创建远程分支， origin是远程仓库名
git push origin : # 创建远程分支
git push origin : #先删除本地分支(git br -d )，然后再push删除远程分支
Git远程仓库管理
GitHub
git remote -v # 查看远程服务器地址和仓库名称
git remote show origin # 查看远程服务器仓库状态
git remote add origin git@ github:robbin/robbin_site.git # 添加远程仓库地址
git remote set-url origin git@ github.com:robbin/robbin_site.git # 设置远程仓库地址(用于修改远程仓库地址) git remote rm # 删除远程仓库
创建远程仓库
git clone –bare robbin_site robbin_site.git # 用带版本的项目创建纯版本仓库
scp -r my_project.git git@ git.csdn.net:~ # 将纯仓库上传到服务器上
mkdir robbin_site.git && cd robbin_site.git && git –bare init # 在服务器创建纯仓库
git remote add origin git@ github.com:robbin/robbin_site.git # 设置远程仓库地址
git push -u origin master # 客户端首次提交
git push -u origin develop # 首次将本地develop分支提交到远程develop分支，并且track
git remote set-head origin master # 设置远程仓库的HEAD指向master分支
也可以命令设置跟踪远程库和本地库
git branch –set-upstream master origin/master
git branch –set-upstream develop origin/develop
查看、添加、提交、删除、找回，重置修改文件
git help # 显示command的help
git show # 显示某次提交的内容 git show $id
git co – # 抛弃工作区修改
git co . # 抛弃工作区修改
git add # 将工作文件修改提交到本地暂存区
git add . # 将所有修改过的工作文件提交暂存区
git rm # 从版本库中删除文件
git rm –cached # 从版本库中删除文件，但不删除文件
git reset # 从暂存区恢复到工作文件
git reset – . # 从暂存区恢复到工作文件
git reset –hard # 恢复最近一次提交过的状态，即放弃上次提交后的所有本次修改
git ci git ci . git ci -a # 将git add, git rm和git ci等操作都合并在一起做git ci -am “some comments”
git ci –amend # 修改最后一次提交记录
git revert # 恢复某次提交的状态，恢复动作本身也创建次提交对象
git revert HEAD # 恢复最后一次提交的状态
```



通过命令行创建一个新的版本库
```
    touch README.md
    git init
    git add README.md
    git commit -m "first commit"
    git remote add origin http://github.com/shaipe/r/ehr.git
    git push -u origin master
```
通过命令行推送一个已存在的版本库
```
    git remote add origin http://github.com/shaipe/r/ehr.git
    git push -u origin master
```