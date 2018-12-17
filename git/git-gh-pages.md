## 用 Github 的 gh-pages 分支展示自己的项目

### 提交某目录下的静态文件到远端仓库的 gh-pages 分支
*展示的项目文件中的某个文件*
```bash
git subtree push --prefix='目录' origin gh-pages
```

### 提交项目文件到远端仓库的 gh-pages 分支
*项目里的所有文件*
```bash
# 创建空的分支 gh-pages
git symbolic-ref HEAD refs/heads/gh-pages
git add -A
git commit -m '...'
git push origin gh-pages
```
