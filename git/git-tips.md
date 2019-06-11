# 一些 git 常用和一些记不住的命令

## 分支

### 新建

```shell
git branch test # 新建test分支
git checkout -b newBrach # 新建 newBrach 分支，并切换到该分支
git branch newBrach # 指定哈希3defc69，新建分支名字为newBrach
git checkout -b newBrach origin/master # 取回远程主机的更新以后，在它的基础上创建一个新的分支  
```

### 新建一个空白分支

```shell
git checkout --orphan <branchname> # 首先创建一个没有父节点的分支
git rm --rf . # 清除工作目录
```

### 删除

```shell
git push origin :branchName  # 删除远程分支  
git push origin --delete new # 删除远程分支new   
git branch -d branchName     # 删除本地分支，强制删除用-D  
git branch -d test      # 删除本地test分支   
git branch -D test      # 强制删除本地test分支   
git remote prune origin # 远程删除了，本地还能看到远程存在，这条命令删除远程不存在的分支
```