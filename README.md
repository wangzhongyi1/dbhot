# Git 的学习笔记

## git 三大区
- 工作区：新增和修改文件会自动加入工作区
- 暂存区：使用 `git add .` 命令以后加入暂存区
- 版本库：使用 `git commit -m "xxx" ` 命令以后创建一个新版本

## 版本回滚
- 通过 `git log` 查看所有的版本号
- `git reset --hard xxxx` xxxx 是指通过 git log 查看到的你想回退的版本号
  + 也可以通过 `git reset --hard HEAD^1` 来指定要往前恢复的版本
  + `git push -f` 让远程的 gitlab 也进行回滚操作
  
- 你回滚后又想恢复之前的版本
  + 通过 `git reflog` 可以查看到更详细的操作记录，里面就有所有存在过的版本号
  + 再次通过 `git reset --hard xxxx` 来切换版本
- `git log --graph` 以图形的方式展示记录
- `git log --graph --pretty=format:"%h %s"` 中的 --pretty 用来格式化显示的

## 分支
- 切换和创建分支
1. 查看本地所有分支： `git branch` 
  + `git branch -a` 查看所有分支包括远程分支
2. 创建分支：`git branch dev`
3. 切换分支：`git checkout dev`
  + 第二和第三步可以合并：`git checkout -b dev` 创建并切换到 dev 分支

- 合并分支（比如将 dev 分支的代码合并到 master 上）
  + 先要切换到 master 分支 `git checkout master`
  + 再将 dev 分支合并到 master，`git merge dev`

- 删除分支（比如一些为了解决bug临时创建的分支）
  + 删除 bug 分支 `git branch -d bug`

## 推送
- 远程仓库起别名 `git remote add origin git@github.com:wangzhongyi1/dbhot.git` origin 就是后面仓库地址的别名
- 推送指定分支的代码 `git push -u origin master`、`git push -u origin dev`

## 克隆
- `git clone git@github.com:wangzhongyi1/dbhot.git`
  + 只要克隆了，就拥有了所有的分支信息，只不过不能显示出来，但是你可以直接`git checkout xxx` 来切换分支
  + 只要克隆了，就自动执行了起别名的语句，你可以直接来用 origin 这个别名进行推送了

## 拉取指定分支的代码
- `git pull origin dev` 这一句可以拆分成下面的两句
  + `git fetch origin dev` 将远程仓库代码拉到本地的版本库
  + `git merge origin/dev` 将本地的版本库代码合并到工作区

## rebase(变基)
> 使 git 记录简洁
### 基本用法
- `git rebase -i HEAD~3` 后面的 `HEAD~3` 代表合并最新的三条记录
  ```git
    pick aee184d v2
    s 627e721 v3
    s 93b1aa7 v4
  ```
  + 需要把下面的 v3 和 v4 前面的 `pick` 改成 `s`，表示将 v3 和 v4 版本合并到 v2 中
  + 然后 `:wq` 保存退出 vim ，会自动进入另一个界面让你编写合并信息。
  + 编写完合并信息后退出，就完成了记录合并
- 合并记录时建议不要合已经提交到远程仓库的版本
### 用法二
- 和 merge 操作相反
  + 先切换到 dev ，然后执行 `git rebase master`
  + 接着切换回 master ，然后执行 `git merge dev`
  + 这样在 master 上执行 `git log --graph` 就在一条线上了，不会出现分叉了
### 用法三
- 当你使用 `git pull origin dev` 从远程拉取代码的时候会自动执行一次合并，也就可能会产生分叉
  + 不适用 `git pull origin dev` ，而使用下面的来代替
  + `git fetch origin dev` 将远程仓库代码拉到本地的版本库
  + `git rebase origin/dev` 这样合并就不会产生分叉
### 当使用 git rebase 时产生冲突
- 在 dev 中执行 `git rebase master` 的过程中出现冲突
- 接着手动解决冲突
- 然后执行 `git add .` 重新加入缓存区
- 最后执行 `git rebase --continue` 继续被中断的 rebase 操作

## 打 tag, 更容易观看版本
- 本地：`git tag -a v1 -m "第一版"`
- 远程：`git push origin dev --tags`

## 如何给开源项目贡献代码
- 第一步：fork 源代码
  + 点右上角 fork 按钮，就会自动在自己的仓库创建同名项目，并拉取源代码到同名仓库
- 第二步：将同名项目克隆到本地，然后修改代码
- 第三步：提交修改的代码
- 第四步：提交一个 pull request，然后等待作者的回复


## git 高级操作

### 查看操作
- 查看本次修改内容
  + `git diff`
- 查看分支图
  + `git log --graph --decorate --oneline --simplify-by-decoration --all`

### 撤销操作
- 撤销本次修改内容（未使用 git add 缓存代码时）
  + 撤销全部 `git checkout -- .`
  + 撤销某个文件 `git checkout -- readme.md`
- 撤销本次修改内容（已经使用了 git add 缓存代码时）
  + 撤销全部 `git reset HEAD .`
  + 撤销某个文件 `git reset HEAD readme.md`
> 此命令用来清除 git 对于文件修改的缓存。仅仅撤出 add 区域，本地修改不会消失。
****
- commit 以后，撤销本次提交
  + 撤销到 add 状态，即不改变修改的代码，仅仅撤出 commit 区域 `git reset --soft HEAD^`
  + 直接撤销本次修改，会回退到上个版本的代码 `git reset --hard HEAD^`

### git 修改 commit 注释
- `git rebase -i HEAD~2` 会展示最近的两次提交注释,最后面的数字可更改
    + 按键盘 `i` 键，进入编辑模式
    + 把你想要修改的注释的前面的 `pick` 更改为 `edit`
    + 然后按 `esc` 退出编辑模式，再输入 `:wq` 保存并退出
- `git commit --amend` 开始编辑注释
- `git rebase --continue`
- `git push --force origin master` 强制推送远程仓库，当然你也可以不强制
- 
### git push 某个特定的commit
- `git push <remotename> <commit SHA>:<remotebranchname>`
    + `remotename` 远程仓库名，默认为 origin
    + `commit SHA` commit的id
    + `remotebranchname` 远程分支名
    + 例如：`git push origin 503edc5aaf:master`
> 上面的命令会将暂存区内 `commit SHA` 代表的提交 ***以及其之前尚未推送到远程的提交一起提交到远程***
- 如果想要通过上面命令推送一个指定的commit，就需要保证这个提交之前没有其他的提交了。如果不是，可以通过`git rebase i`改变提交的位置，使其之前没有其他提交
    + `git rebase i` 会列出暂存区中所有未push的commit
    + i键 进入编辑模式，光标定位到当前行，输入命令 `:m 0` 就会移入行首，然后输入 `:x` 退出
    + 最后通过上面的命令 push 这个特定的 commit
    + 参考：https://blog.csdn.net/u010730126/article/details/100743019

### git 合并某个分支的某次 commit 
- 例如：在 master分支想要合并 dev分支上的某次提交
    + 切换到 dev分支，拷贝下来你想要被合并的 commitId
    + 切换到 master分支，执行 `git cherry-pick 62ecb3` 来进行操作

## 其他

### 配置
- 项目配置文件：项目/.git/config
```js
git config --local user.name "xxx"
git config --local user.email "xxx.com"
```
- 全局配置文件：C:/Users/Administrator/.gitconfig
```js
git config --global user.name "xxx"
git config --global user.email "xxx.com"
```
- 系统配置文件：Git的安装目录/etc/.gitconfig
```js
git config --system user.name "xxx"
git config --system user.email "xxx.com"

注意：需要有 root 权限，不然写不进去
```

### .gitignore 配置
```git

*.txt 排除所有 .txt 结尾的文件

!a.html 排除除了 a.html 以外的文件（也就是只管理 a.html 文件）

!files/b.py  在 files 文件夹下排除除了 b.py 以外的文件（也就是在 fiels 文件夹下只管理 b.py 文件）

.gitignore 排除 .gitignore 文件本身

node_modules/  排除 node_modules 文件夹下的所有文件

```

### fiddler设置代理
- 配置抓取 https 参考链接：https://www.cnblogs.com/liulinghua90/p/9109282.html
- AutoResponder ->  ✔Enable  ❌rules Accept all CONNECTs  ✔Unmatched requests passthrough  ❌Enable Latency  -> Add Rule
- Rule Editor 上面写需要替换远程的文件名，下面选择本地文件
- 本地起 vue 项目，调试远程项目
  + 第一个输入框：regex:(?insx)^http://192.168.89.89/(?<args>.*)$ （远程地址的线上环境的打包后项目）
  + 第二个输入框：http://localhost:8082/${args} （本地正常起的开发环境的项目，${args}里面会进行正则匹配后替换）
- 手机端项目代理调试已经生成的APP
  + 手机连接 wifi, 让电脑和手机在同一个网络下
  + 点击手机 wifi 后面的感叹号，
    * 配置 HTTP代理
    * 切换手动
    * 服务器填你电脑ip (注意前面不用加`http://`，后面不用加`端口`，只需要写类似 `192.168.5.20` 这样)
    * 端口写 fidddler的端口，一般没改过是8888
    * 认证不用开
    * 存储/保存
  + 使用自带浏览器 safari 打开你 `电脑ip + 8888` 如：`192.168.5.20:8888` 去下载 Fiddler证书
  + 然后到 设置->通用->VPN与设备管理 安装上面下载的证书
  + Fiddler 添加规则
    * 第一个输入框：regex:(?insx)^http://192.168.89.89/(?<args>.*)$ （APP中的页面真实存放的远程地址）
    * 第二个输入框：http://localhost:8082/${args} （本地正常起的开发环境的项目）
  + 这样APP里面的页面也被替换掉了，你再改动本地项目代码，APP里面也被代理改掉了
  + 最后调试完，记得把 wifi 手动改为关闭就行了，不然你Fiddler关闭后，手机不能正常上网


