# git与gitHub

## 技巧

在github后面加`1s`可以在github中打开online vscode

# Git

### git国内下载镜像

[Index of git-for-macos-local (huaweicloud.com)](https://mirrors.huaweicloud.com/git-for-macos/)

## 初始化本地仓库

1. 查看本地仓库：`git --version`
2. 清屏：`clear`
3. 设置用户名和邮箱
   1. `git config --global [user.name](<http://user.name>) “wangkun”`
   2. `git config --global [user.email](<http://user.email>) “910009136@qq.com”`
4. 本地仓库的初始化：`git init`
5. `./git` 下的文件不要乱动

## git常见命令

### add和commit命令

1. 创建一个文件
2. 将文件提交到暂存区：`git add Demo.txt`
3. 将暂存区的内容提交到本地库：`git commit -m "提交的第一个文件 Demo.txt" Demo.txt`
4. 将本地库提交到远程库：`git push origin main:main`。 [本地分支名称]:[仓库分支名称]

### status命令

> git status查看工作区与暂存区状态
>
> 1. 红色为工作区未提交到暂存区的文件
> 2. 绿色为暂存器未提交到本地库的文件

1. 修改Demo.txt文件，再次`git status` 查看状态，git会提示你有文件需要更新，需要重新执行`add` 与`commit` 命令将文件更新到本地库

### log命令

> 查看提交记录，从近到远显示日志

1. 提过记录过多，一页展示不下，有分页效果

   1. 下一页：空格
   2. 上一页：b
   3. 到尾页，最底下会显示END
   4. 退出：q

2. 查看提交记录的几种方式

   1. `git log`

   2. `git log --pretty=oneline`

      !https://s3-us-west-2.amazonaws.com/secure.notion-static.com/31a941ab-b78a-4331-b817-631ad6d2203a/Untitled.png

      Untitled

   3. `git log --online`

      !https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b2943b96-7705-4a34-91c8-a38cf874635f/Untitled.png

      Untitled

   4. `git reflog`

      1. 比`git log --oneline` 多了HEAD@{数字}
      2. 数字代表的意思是：指针回到当前版本需要走多少步

      !https://s3-us-west-2.amazonaws.com/secure.notion-static.com/1f7e430c-4968-4d87-9b2a-ffd64750086b/Untitled.png

      Untitled

### reset命令

> 前进或者后退历史版本

1. `git reset --hard 回退版本的哈希值(索引)`
2. 复制：在终端中选中即复制
3. 粘贴：右键 paste
4. reset的三个参数
   1. `-hard` 本地库指针移动的同时，暂存区、工作区版本也跟着变
   2. `-mixed` 本地库指针移动的同时，暂存区版本跟着变，但是工作区版本不变
   3. `-soft` 本地库指针移动的同时，暂存区、工作区版本都不变

### rm删除工作区文件，reset恢复文件

1. 删除文件
   1. `rm test.txt` 删除工作区中名为test.txt文件
   2. `git add test.txt` ：将删除操作同步到暂存区
   3. `git commit -m '删除text.txt文件' test.txt` ：将删除操作同步到本地库
2. 恢复删除文件
   1. `git reset --hard 版本的哈希值(索引)` ：可以实现恢复删除文件的效果
   2. 小技巧：如果删除了工作区中的文件，并且已经将删除操作同步到了暂存区，也可以用此方法，效果相当于撤销删除操作

### diff

1. `git diff` ：比较工作区与暂存区中所有文件差异
2. `git diff 文件名` ：比较工作区与暂存区对应文件名文件内容的差异
3. `git diff 哈希值(索引) 文件名` ：比较暂存区与对应哈希值本地库的对应的文件名 文件内容的差异

## 分支

### 操作分支

1. `git branch -v` ：查看分支
2. `git branch branch01` ：创建了一个名为branch01的分支
3. `git checkout branch01` ：切换到名为branch01的这个分支
4. `git checkout -b dev`：创建并切换
5. `// 删除本地分支git branch -d localBranchName// 删除远程分支git push origin --delete remoteBranchName`

注：如果在A分支没有提交就切换B分支，A分支中的变化就会被带到B分支中

### 合并分支

> 将branch01分支合并到master主分支中

1. `git checkout master` ：切换到主分支
2. `git merge branch01` ：将branch01合并到master主分支
3. 出现了冲突，查看冲突：`cat Test4.txt` 查看或者直接去文件查看
4. 解决冲突：
   1. 决定冲突文件的去留，到底使用哪个方案，将不要的文件删除
   2. `git add Test4.txt` ：提交到暂存区
   3. `git commit Test4.txt -m '冲突已经解决了'` ：提交到本地库（注意这里不要写文件名，否则会报错！）

# GitHub

## 基本使用

1. GitHub创建一个项目

2. 在本地创建本地库

3. 在Code中获取路径

4. `git remote -v` ：查看设置别名列表与对应的远程库

5. `git remote`：查看远程库别名列表

6. `git remote add origin <https://github.com/kunisstrong/Git-Test.git`> :为此链接创建一个名为（origin）的别名

7. `git remote remove origin`：删除别名

8. `git push -u origin master`：将本地的master分支推送到origin主机，同时指定origin为默认主机，后面就可以不加任何参数使用git push了

9. `git push origin master:main` ：将本地库文件master主支提交到远程库main分支

10. `git clone 远程库地址`

    ：将远程库中文件克隆到本地

    1. `git checkout origin/分支名` ：切换远程库分支
    2. `cat 文件名` ：查看文件信息

11. 拉取与合并远程库操作

    1. 程序员对远程库进行了修改，产品经理可以通过`git fetch origin master` 进行抓取操作，只是将远程库的内容下载到本地，工作区中内容没有更新，
    2. 抓取后可以去远程库看看内容是否正确
    3. 然后发现内容都正确，`git merge origin/master`可以进行合并操作（合并之前将分支切换回来）
    4. 拉取和合并两个操作可以用一个操作取代：`git pull origin master`

12. 如果别的程序员想要向远程库提交文件，需要进行成员邀请

    1. 在setting中输入成员名字，邀请成员
    2. 复制邀请链接
    3. 将邀请链接给你要邀请的那个人
    4. 被邀请的人通过此链接接收成员邀请

13. 协同开发出现两个程序员同时修改同一个分支的同一个文件，产生冲突

    1. `git pull origin master` ：将文件拉取到本地库与工作区
    2. 人为解决冲突
    3. add commit 两部曲提交到本地库（注意commit时最后不要加文件名）
    4. `git push origin master` ：提交到远程仓库

14. 跨团队合作

    1. 获取项目GitHub地址
    2. 进入账号，复制进入地址，点击fork操作
    3. `git clone GitHub地址` ：克隆到本地
    4. 写功能
    5. 将文件从本地库提交到远程库
    6. 进行Pull requests操作

## SSH免密登录

1. 进入用户主目录中
   1. `cd ~`
2. 执行命令，生成ssh.的目录
   1. `ssh-keygen -t rsa -C "邮箱地址"`
3. 我们发现主目录.ssh中多了两个文件
   1. 我们打开后缀为.pub的文件，将里面的内容全都复制出来；或者 `cat ~/.ssh/id_rsa.pub`，注意不要用open指令打开
   2. 进入自己的github账号，
   3. 在setting中SSH and GPG keys中将复制的东西粘贴进去并取名字
4. 给SSH取别名：`git remote add origin_ssh ssh地址`(用ssh地址对应的项目仓库起了origin的别名)
5. 使用别名向github远程库提交文件：`git push origin_ssh master` （将master分支上的文件提交到别名为origin_ssh的远程库）