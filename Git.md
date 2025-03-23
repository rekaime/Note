# 安装

[点击进入官网下载](https://git-scm.com)

查看版本号

```bash
git -v
```

使用方式：

- 命令行
- [图形化界面](https://git-scm.com/downloads/guis?os=windows)
- IDE插件 / 扩展



# 初始化配置

省略（`Local`）：本地配置，只对本地仓库生效

- `--global`：全局配置，对所有**仓库**生效
- `--system`：系统配置，对所有**用户**生效

用户名如果不含空格可以省略双引号

```bash
git config --global user.name "<username>"
git config --global user.email <email>
# 可选 保存用户名和密码
git config --global credential.helper store
```

查看 Git 配置信息

```bash
git config --global --list
```

命令行工具：[Posh-Git](https://gitcode.com/gh_mirrors/po/posh-git/overview)



# 仓库

## 新建

新建仓库后，命令行所在路径会新增隐藏文件夹`.git`

```bash
# mkdir project
# cd project
git init [ <path> ]
# OR
git clone <url>
```



## 数据管理区域

+ 工作区（Working Directory）：本地目录
+ 暂存区（Staging Area / Index）：保存即将提交到 Git 仓库的修改内容
+ 本地仓库（Local Repository）：通过`git init`创建的仓库，包含完整的项目历史和元数据



## 文件状态

+ 未跟踪（Untrack）：新创建的还未被 Git 管理的文件
+ 未修改（Unmodified）：已经被 Git 管理、但内容未发生变化的文件
+ 已修改（Modified）：内容发生变化但还没添加到暂存区的文件
+ 已暂存（Staged）：修改并提交到暂存区的文件

<table style="text-align: center">
    <thead>
        <tr>
            <th style="width: 12.5%"></th>
            <th style="width: 12.5%"></th>
            <th style="width: 12.5%"></th>
            <th style="width: 12.5%"></th>
            <th style="width: 12.5%"></th>
            <th style="width: 12.5%"></th>
            <th style="width: 12.5%"></th>
            <th style="width: 12.5%"></th>
        </tr>
        <tr>
            <th colspan="2">未跟踪</th>
            <th colspan="2">未修改</th>
            <th colspan="2">已修改</th>
            <th colspan="2">已暂存</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td colspan="1">👉</td>
            <td colspan="2">
                <code>git add</code>
            </td>
            <td colspan="2">modify</td>
            <td colspan="2">
                <code>git add</code>
            </td>
            <td colspan="1">👉</td>
        </tr>
        <tr>
            <td colspan="1">👉</td>
            <td colspan="6">
                <code>git add</code>
            </td>
            <td colspan="1">👉</td>
        </tr>
        <tr>
            <td colspan="8"></td>
        </tr>
        <tr>
            <td colspan="1">👈</td>
            <td colspan="2">
                <code>git rm</code>
            </td>
            <td colspan="4">
                <code>git commit</code>
            </td>
            <td colspan="1">👈</td>
        </tr>
        <tr>
            <td colspan="3">👈</td>
            <td colspan="2">
                <code>git checkout</code>
            </td>
            <td colspan="2">
                <code>git reset</code>
            </td>
            <td colspan="1">👈</td>
        </tr>
    </tbody>
</table>


## 操作

### 常见简写

+ `HEAD`：版本库最新版本
+ `HEAD^`、`HEAD~`：`HEAD`之前的一个版本
+ `HEAD~n`：`HEAD`之前的 n 个版本
+ `STAGE`：



### 查看仓库状态

检查仓库状态以及仓库中文件处于什么状态

```bash
git status
```



### 添加到暂存区

```bash
git add <filename>
```

```bash
# 将当前目录所有文件添加到暂存区
git add .
```



### 提交暂存区的文件

```bash
git commit
```

```bash
# 提交 并设置提交信息
git commit -m "<message>"
```



### 添加到暂存区并提交

```shell
git commit -a -m "<message>"
# OR
git commit -am "<message>"
```



### 查看提交记录

```bash
git log
```

```bash
# 简洁
git log --online
```



### 版本回退

回退版本，保留工作区和暂存区的内容

```shell
git reset --soft <id>
```

回退版本，丢弃工作区和暂存区的内容

```shell
git reset --hard <id>
```

回退版本，只保留工作区的内容（默认）

```shell
git reset --mixed <id>
```

缩写：

+ `HEAD^`：上一个版本



可以通过命令查看回溯操作的历史操作，来撤销回溯

```shell
git reflog
# git reset --hard <id>
```



### 查看差异

查看工作区、暂存区和本地仓库之间的差异

默认比较的是工作区和暂存区的差异

```shell
git diff
# ======
diff --git a/filename.txt b/filename.txt
index 55bd0ac..a64d9e2 1006444 # Git生成的文件的40位哈希值 只显示前后七位 以及文件的权限
--- a/filename.txt
+++ b/filename.txt
@@ -1 +1 @@
-333
+AAA
```

查看两个版本的差异

```shell
git diff <id_1> <id_2>
```

查看工作区+暂存区与版本库（最新提交）的差异

```shell
git diff HEAD
```

查看暂存区和版本库（最新提交）的差异

```shell
git diff --cached
git diff --staged
```

只查看指定文件的差异

```shell
git diff ... <filename>
```

查看分支差异

```shell
git diff <branch_name_1> <branch_name_2>
```



### 版本库删除文件

方法一：先更新暂存区的状态，然后提交

```shell
# 首先删除本地的文件
git add file1.txt
git commit -m 删除文件
```

方法二：直接删除工作区、暂存区的文件

```shell
git rm <filename>
git commit -m 删除文件
```

其他参数

```shell
# 将文件从暂存区删除 但保留工作区的文件
git rm --cache <filename>

# 递归删除某个目录下的文件
git rm -r *
```



## .gitignore

可以帮助忽略指定的文件不纳入 Git 仓库

一般而言忽略一下内容：

+ 系统、软件自动生成的文件
+ 编译产生的中间、结果文件
+ 运行时生成的日志、缓存、临时文件
+ 涉及敏感信息的文件

[常用 .ignore 文件](https://github.com/github/gitignore)



# 分支

允许多人共同开发，开发结果进行合并



## 操作

默认分支为`master`或`main`

### 查看分支

```shell
git branch
```



### 新建分支

```shell
git branch <branch-name>
```



### 切换分支

注意`checkout`也可以修改目录或文件到文件修改前的状态

如果 branch-name 和目录或文件名相同，则会出现歧义，git 默认选择切换分支

```shell
git checkout <branch-name>
```

因此使用以下新命令就能避免这个问题

```shell
git switch <branch-name>
```



### 合并分支

假设现在所处 main 分支，执行以下命令将 branch-name 合并到 main 分支

```shell
git merge <branch-name>
```



### 查看分支图

```shell
git log --graph --oneline --decorate --all
```



### 删除分支

需要先合并后，才能删除分支

```shell
git branch -d <branch-name>
```

没有合并分支，但希望强制删除

```shell
git branch -D <branch-name>
```



## 合并冲突

如果两个分支修改内容发生重合，就会发生合并冲突

```shell
git merge <branch-name>
# 提示发生冲突 需要手段解决
# 通过 status 检查冲突文件列表
git status

# 或者通过 diff 查看冲突具体内容
git diff
```

然后手动修改对应的文件

```shell
vi conflict_filename.txt
git add .
git commit -m "merge conflict"
```

在提交前如果希望中断此次合并

```shell
git merge --abort
```



## 变基

假定目前在 main 分支，执行以下命令会将 branch-name 提交的修改直接接入 main 分支中，而不是采取分支合并的方式

rebase 操作后，原本的分支会变为一条直线

```shell
# main:5 | | branch:2
# main:4 | | branch:1
# main:3 |/
# main:2 |
# main:1 |
[main]> git rebase <branch-name>
# main:5 |
# main:4  |
# branch:2 |
# branch:1 |
# main:3  |
# main:2 |
# main:1 |
```

如果是在 branch-name 上操作

```shell
# main:5 | | branch:2
# main:4 | | branch:1
# main:3 |/
# main:2 |
# main:1 |
[branch-name]> git rebase main
# branch:2 |
# branch:1 |
# main:5  |
# main:4 |
# main:3 |
# main:2 |
# main:1 |
```



## Git Flow

### main | master

主线分支或基线分支

是项目的核心分支，保证其为最新稳定版本

避免直接修改，一般通过合并分支来更新

每次更新最好生成一个新版本号，便于追踪和回溯

通过`git tag`命令可以标记版本号

> 版本号规则
>
> + 主版本：主要功能变化，重大更新
> + 次版本：新增功能、小量级更新和改进，不影响现有功能
> + 修订版本：bug 修复，安全补丁，一般不更改现有功能和接口



### hotfix

问题修复分支，从 main 分支中分离

包含项目问题修复的源码，解决生产环境发生的缺陷

问题修复完毕后合并到 main 以及 develop 分支，并删除该分支，同时更新修订版本号



### develop

开发分支，从 main 分支中分离

包含最新开发版本代码，用于开发测试

和 main 分支一样长期存在



### \<feture-branch>

功能分支，从 develop 分支中分离

负责开发新的功能，完成任务后合并至 develop，然后删除该分支



### release

版本发布分支或预发布分支，一般从 develop 分支中分离

测试过程发现的问题修复需要持续合并到 develop 分支

代码稳定后会合并至 main 以及 develop 分支，同时删除该分支



## GitHub Flow

相比 Git Flow 模型，GitHub Flow 模型更为简单明了

仅有 Master 主分支是长期存在的，禁止团队成员直接在主分支上进行提交

每个成员可以新建一个 Feature 分支，用于开发测试新功能，然后在本地分支提交代码

开发完毕后发起 Pull Request（PR，拉请求或合并请求），由团队成员进行代码评审，没有问题则发布该功能（GitLab 中类似的行为被称为 Merge Request，MR）

> 分支命名规范：
>
> + release：v1.0.0
> + feature：feture-login-page
> + hotfix：hotfix-#issueid-desc
>
> 分支管理
>
> + 定期合并已通过分支并及时删除
> + 保持合适的分支数量
> + 为分支设置合适的权限



# GitHub

## 托管项目

首先新建一个 GitHub 项目

如果本地没有项目，可以新建一个文件夹：

```shell
# cd project dir
echo "# <repository-name>" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:<username>/<repository-name>.git
git push -u origin main
```

如果本地已经有对应的项目：

```shell
# cd project dir
git remote add origin git@github.com:<username>/<repository-name>.git
git branch -M main
git push -u origin
```



## SSH

帮助我们通过 SSH 连接远程仓库



### 生成公私密钥对

```shell
cd ~/.ssh
ssh-keygen -t rsa -b 4096
# 输入密钥输出文件路径 如 test
# 输入密码
```

此时在目标目录下生成 test 和 test.pub 两份文件

test 为私钥，需要保存在本地

test.pub 为公钥，可以上传到 GitHub



### 上传公钥

在 GitHub 中点击 Settings > SSH and GPG keys > New SSH key

输入一个名称，密钥类别默认为 Authentication Key

将 test.pub 的内容复制到 Key 输入框中



### config

如果修改了输出的公私密钥对，则需要编辑配置文件

配置文件中输入以下五行，指定私钥使用的是`~/.ssh/test`

```text
# github
Host github.com
HostName github.com
PreferredAuthentications publickey
IdentityFile ~/.ssh/test
```



## 同步仓库

克隆仓库

```shell
git clone <repo-address>
```

推送本地仓库

```shell
# remote 默认为 origin
# branch 默认为 main/master
git push <remote> <branch>
```

拉取远程仓库，发生冲突需要手动解决

```shell
git pull <remote>
```



## 关联仓库

见托管仓库

origin 为远程仓库的别名，默认为 origin

```shell
# cd project dir
git remote add origin git@github.com:<username>/<repository-name>.git
# 以下两行其实就是 git push -u origin main:main
# 因为本地分支和远程仓库分支名称相同 因此可以简写 git push -u origin main
git branch -M main
git push -u origin
```



# Gitee

面向国内项目



# GitLab

## 私有化部署
