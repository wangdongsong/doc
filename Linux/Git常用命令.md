## 三、常用命令

### 总体

```
//克隆仓库  
git clone https://github.com/wangdongsong/java.git  

//使用帮助  
git --help  

//查看分支帮助  
git help branch  

```

### 分支

```
//-------分支---------

//查看分支
git branch

//切换分支
git checkout BranchName

//删除分支
git branch -d BranchName

//将新建的分支推送到远程
git push origin BranchName

//增加整个目录
git add -A

//commit
git commit -m '提交的描述'

//推送到远程
git push origin BranchName

//将无端代码更新到本地
git pull origin BranchName

```
### 其它

```
//删除文件
git rm **.file

//删除某个文件夹
git rm -r build/


//获取远程分支
git fetch -v --progress origin

//


```