我们常用的 git rm 仅对 Working Tree 构成影响，如果想永久的删除仓库中的文件或目录，那么就要用到 git filter-branch 命令了。 git filter-branch 会检索整个 Commit 历史，逐一改写 Commit Object，重构整个 Tree。
```
git filter-branch --tree-filter 'rm -rf path/folder' HEAD
git filter-branch --tree-filter 'rm -f path/file' HEAD
```
也可以指定 检索的 Commit 历史的范围：
```
git filter-branch --tree-filter 'rm -rf path/folder' 347ae59..HEAD
```
最后，不要忘了向仓库强制推送所有的变化：
```
git push origin master --force
```
执行 git filter-branch 命令后，已经标记为删除的 Object 在本地仓库中要到过期后才会解除关联和进行垃圾回收。检查哪些 tags 和 branch 引用了这些 Object：
```
git for-each-ref --format='delete %(refname)' refs/original
```
如果想立即解除关联，执行垃圾回收，可以这么做：
```
git for-each-ref --format='delete %(refname)' refs/original | git update-ref --stdin
git reflog expire --expire=now --all
git gc --prune=now
```
接下来，就是强制推送到仓库了:
```
git push origin --force --all
git push origin --force --tags
```

不到迫不得已，不要轻易使用 git filter-branch ，因为它重构了整个 Tree，所以每个开发人员都需要重新克隆仓库到本地，对于有很多开发者参与的大型项目来说，这么做会给很多人带来麻烦，与其事后对仓库内容进行修正，不如伊始就对每个 Commit 慎之又慎。
