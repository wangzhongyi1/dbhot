# Git 的学习笔记

## git 三大区
- 工作区：新增和修改文件会自动加入工作区
- 暂存区：使用 `git add .` 命令以后加入暂存区
- 版本库：使用 `git commit -m "xxx" ` 命令以后创建一个新版本

## 版本回滚
- 通过 `git log` 查看所有的版本号
- `git reset --hard xxxx` xxxx 是指通过 git log 查看到的你想回退的版本号
- 你回滚后又想恢复之前的版本
  + 通过 `git reflog` 可以查看到更详细的操作记录，里面就有所有存在过的版本号
  + 再次通过 `git reset --hard xxxx` 来切换版本

## 分支
- 切换和创建分支
1. 查看所有分支： `git branch` 
2. 创建分支：`git branch dev`
3. 切换分支：`git checkout dev`

- 合并分支（比如将 dev 分支的代码合并到 master 上）
  + 先要切换到 master 分支 `git checkout master`
  + 再将 dev 分支合并到 master，`git merge dev`

- 删除分支（比如一些为了解决bug临时创建的分支）
  + 删除 bug 分支 `git branch -d bug`

## 推送
- 远程仓库起别名 `git remote add origin git@github.com:wangzhongyi1/dbhot.git` origin 就是后面仓库地址的别名
- 推送指定分支的代码 `git push -u origin master`、`git push -u origin dev`