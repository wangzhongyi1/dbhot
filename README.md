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


