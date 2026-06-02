# Git 使用详细指南

面向从零基础到能够参与团队协作的开发者。本指南以命令行为主，兼顾 GitHub、GitLab、VS Code 等常见工作流。

## 目录

1. [Git 是什么](#git-是什么)
2. [安装与初始配置](#安装与初始配置)
3. [Git 的核心概念](#git-的核心概念)
4. [命令速查表](#命令速查表)
5. [创建或获取仓库](#创建或获取仓库)
6. [日常基本流程](#日常基本流程)
7. [查看状态、差异和历史](#查看状态差异和历史)
8. [分支使用](#分支使用)
9. [合并、冲突和解决方法](#合并冲突和解决方法)
10. [Rebase 的使用](#rebase-的使用)
11. [撤销与回退](#撤销与回退)
12. [远程仓库](#远程仓库)
13. [团队协作工作流](#团队协作工作流)
14. [Stash 临时保存](#stash-临时保存)
15. [Tag 版本标签](#tag-版本标签)
16. [.gitignore 忽略文件](#gitignore-忽略文件)
17. [常用高级命令](#常用高级命令)
18. [Git 配置、别名和 SSH](#git-配置别名和-ssh)
19. [VS Code 和 GUI 工具](#vs-code-和-gui-工具)
20. [最佳实践](#最佳实践)
21. [常见问题排查](#常见问题排查)
22. [练习示例](#练习示例)

## Git 是什么

Git 是一个分布式版本控制系统，用来记录文件变化、比较不同版本、多人协作开发，并在需要时回退到历史状态。

Git 常用于：

- 记录代码、文档或配置文件的修改历史
- 创建分支并行开发功能
- 审查代码变化
- 与 GitHub、GitLab、Bitbucket 等远程平台协作
- 通过 Pull Request 或 Merge Request 做团队代码评审
- 发布版本并打标签

Git 的核心价值是：每次提交都是一个可追踪的历史快照。你可以知道谁在什么时候改了什么、为什么改，以及如何回到某个历史版本。

## 安装与初始配置

### 检查是否已安装

```bash
git --version
```

示例输出：

```text
git version 2.45.0
```

逐行解读：

| 输出内容 | 含义 |
| --- | --- |
| `git version 2.45.0` | 当前安装的 Git 版本号；主版本 `2`，次版本 `45`，补丁 `0`。能看到此输出说明 Git 已正确安装并可在当前路径调用 |

如果能看到版本号，例如 `git version 2.45.0`，说明 Git 已安装。

### macOS 安装

方式一：使用 Xcode Command Line Tools。

```bash
xcode-select --install
```

方式二：使用 Homebrew。

```bash
brew install git
```

### Linux 安装

Debian / Ubuntu：

```bash
sudo apt update
sudo apt install git
```

Fedora：

```bash
sudo dnf install git
```

Arch Linux：

```bash
sudo pacman -S git
```

### 配置用户名和邮箱

用户名和邮箱会写入提交记录。

```bash
git config --global user.name "Your Name"
git config --global user.email "your-email@example.com"
```

查看配置：

```bash
git config --global --list
```

示例输出：

```text
user.name=Alice
user.email=alice@example.com
init.defaultbranch=main
core.editor=code --wait
core.autocrlf=input
```

逐行解读：

| 输出内容 | 含义 |
| --- | --- |
| `user.name=Alice` | 提交时记录的作者名，由 `git config --global user.name` 设置 |
| `user.email=alice@example.com` | 提交时记录的作者邮箱，由 `git config --global user.email` 设置 |
| `init.defaultbranch=main` | 新建仓库时的默认分支名 |
| `core.editor=code --wait` | Git 打开编辑器时使用的程序（此处为 VS Code） |
| `core.autocrlf=input` | 换行符处理策略；`input` 适用于 macOS/Linux |

只查看某一项：

```bash
git config --global user.name
git config --global user.email
```

### 设置默认分支名

现代项目通常使用 `main` 作为默认分支。

```bash
git config --global init.defaultBranch main
```

### 设置默认编辑器

VS Code：

```bash
git config --global core.editor "code --wait"
```

Vim：

```bash
git config --global core.editor "vim"
```

### 设置换行符处理

macOS / Linux 推荐：

```bash
git config --global core.autocrlf input
```

Windows 推荐：

```bash
git config --global core.autocrlf true
```

这可以减少不同系统之间换行符导致的无意义差异。

## Git 的核心概念

### 仓库

仓库是 Git 管理的项目目录。一个仓库包含：

- 工作文件
- `.git` 目录
- 分支信息
- 提交历史
- 远程仓库配置

### 三个区域

Git 日常操作主要围绕三个区域：

| 区域 | 英文 | 说明 |
| --- | --- | --- |
| 工作区 | Working Tree | 你正在编辑的文件 |
| 暂存区 | Staging Area / Index | 准备放入下一次提交的修改 |
| 本地仓库 | Local Repository | 已提交的历史记录 |

基本流程：

```text
编辑文件 -> git add -> git commit
工作区  -> 暂存区  -> 本地仓库
```

### 提交

提交是 Git 历史中的一个快照。每个提交都有：

- 唯一哈希值
- 作者
- 时间
- 提交说明
- 文件变化
- 父提交

查看最近提交：

```bash
git log --oneline
```

示例输出：

```text
a1b2c3d Add login page
e4f5g6h Initialize project
```

逐行解读：

每行格式为：`[短哈希] [提交信息]`

| 字段 | 示例值 | 含义 |
| --- | --- | --- |
| `a1b2c3d` | 短哈希 | 完整提交哈希的前 7 位，唯一标识这次提交，可用来引用它（如 `git show a1b2c3d`） |
| `Add login page` | 提交信息 | 提交时 `-m` 参数填写的说明，描述本次改动做了什么 |
| 第二行 `e4f5g6h` | 更早的提交 | `git log --oneline` 默认从最新到最旧排列，越往下的提交越早 |

### HEAD

`HEAD` 表示当前所在的提交，通常指向当前分支的最新提交。

```bash
git log --oneline --decorate
```

示例：

```text
a1b2c3d (HEAD -> main) Add login page
```

### 分支

分支是指向某个提交的可移动指针。创建分支可以让你在不影响主线代码的情况下开发新功能。

常见分支：

- `main`：稳定主分支
- `develop`：开发集成分支
- `feature/login`：功能分支
- `fix/payment-bug`：修复分支
- `release/v1.2.0`：发布分支

## 命令速查表

### 初始化与克隆

| 目的 | 命令 |
| --- | --- |
| 初始化仓库 | `git init` |
| 克隆仓库 | `git clone <url>` |
| 查看状态 | `git status` |
| 查看简洁状态 | `git status -sb` |

### 暂存与提交

| 目的 | 命令 |
| --- | --- |
| 暂存文件 | `git add <file>` |
| 暂存所有修改 | `git add .` |
| 提交 | `git commit -m "message"` |
| 修改最后一次提交 | `git commit --amend` |

### 差异与历史

| 目的 | 命令 |
| --- | --- |
| 查看未暂存差异 | `git diff` |
| 查看已暂存差异 | `git diff --staged` |
| 查看日志 | `git log` |
| 单行日志 | `git log --oneline` |
| 图形日志 | `git log --oneline --graph --decorate --all` |
| 查看提交 | `git show <commit>` |

### 分支

| 目的 | 命令 |
| --- | --- |
| 查看分支 | `git branch` |
| 创建分支 | `git branch <name>` |
| 创建并切换 | `git switch -c <name>` |
| 切换分支 | `git switch <name>` |
| 删除分支 | `git branch -d <name>` |
| 强制删除分支 | `git branch -D <name>` |

### 远程

| 目的 | 命令 |
| --- | --- |
| 查看远程 | `git remote -v` |
| 添加远程 | `git remote add origin <url>` |
| 拉取更新 | `git pull` |
| 只获取不合并 | `git fetch` |
| 推送 | `git push` |
| 首次推送分支 | `git push -u origin <branch>` |

### 合并与 rebase

| 目的 | 命令 |
| --- | --- |
| 合并分支 | `git merge <branch>` |
| 放弃合并 | `git merge --abort` |
| rebase | `git rebase <base>` |
| 继续 rebase | `git rebase --continue` |
| 放弃 rebase | `git rebase --abort` |

### 撤销

| 目的 | 命令 |
| --- | --- |
| 丢弃工作区修改 | `git restore <file>` |
| 取消暂存 | `git restore --staged <file>` |
| 回退提交但保留修改 | `git reset --soft HEAD~1` |
| 回退提交并取消暂存 | `git reset --mixed HEAD~1` |
| 强制回退并丢弃修改 | `git reset --hard HEAD~1` |
| 创建反向提交 | `git revert <commit>` |
| 查看 HEAD 历史 | `git reflog` |

## 创建或获取仓库

### 初始化新仓库

进入项目目录：

```bash
mkdir my-project
cd my-project
git init
```

示例输出：

```text
Initialized empty Git repository in /Users/alice/my-project/.git/
```

逐行解读：

| 输出内容 | 含义 |
| --- | --- |
| `Initialized empty Git repository in ...` | 说明 Git 已在当前目录创建了一个新的空仓库，并生成了 `.git/` 子目录 |
| 路径部分 | 显示 `.git/` 目录的完整路径，确认仓库位置正确 |

创建文件并提交：

```bash
echo "# My Project" > README.md
git add README.md
git commit -m "Initial commit"
```

### 克隆远程仓库

HTTPS：

```bash
git clone https://github.com/user/repo.git
```

SSH：

```bash
git clone git@github.com:user/repo.git
```

克隆时的进度输出示例（以 HTTPS 克隆为例）：

```bash
git clone https://github.com/alice/my-project.git
```

示例输出：

```text
Cloning into 'my-project'...
remote: Enumerating objects: 42, done.
remote: Counting objects: 100% (42/42), done.
remote: Compressing objects: 100% (28/28), done.
remote: Total 42 (delta 10), reused 38 (delta 8), pack-reused 0
Receiving objects: 100% (42/42), 56.12 KiB | 1.23 MiB/s, done.
Resolving deltas: 100% (10/10), done.
```

逐行解读：

| 输出内容 | 含义 |
| --- | --- |
| `Cloning into 'my-project'...` | 在当前目录创建 `my-project/` 文件夹并开始克隆 |
| `remote: Enumerating objects: 42, done.` | 远程服务器统计出仓库共有 42 个对象（提交、文件等） |
| `remote: Counting objects: 100% (42/42), done.` | 服务器计算需要传输的对象数量完成 |
| `remote: Compressing objects: 100% (28/28), done.` | 服务器对 28 个对象进行压缩，以减少传输大小 |
| `remote: Total 42 (delta 10), reused 38 ...` | 共传输 42 个对象，其中 10 个是增量压缩对象；`reused` 表示服务器复用了已有打包对象 |
| `Receiving objects: 100% (42/42), 56.12 KiB ...` | 本地接收进度；后面的速度（`1.23 MiB/s`）反映当前网络带宽 |
| `Resolving deltas: 100% (10/10), done.` | 在本地重建增量对象完成，克隆结束 |

克隆到指定目录：

```bash
git clone git@github.com:user/repo.git my-folder
```

进入仓库：

```bash
cd my-folder
```

## 日常基本流程

最常见的 Git 使用流程：

```bash
git status
git add .
git commit -m "Describe the change"
git pull
git push
```

建议顺序：

1. 开始前先拉取远程更新
2. 创建或切换到功能分支
3. 修改文件
4. 查看变化
5. 暂存需要提交的内容
6. 提交
7. 推送到远程
8. 创建 Pull Request / Merge Request

### 示例：新增一个页面

```bash
git switch -c feature/about-page
mkdir pages
echo "<h1>About</h1>" > pages/about.html
git status
git add pages/about.html
git commit -m "Add about page"
git push -u origin feature/about-page
```

`git commit -m "..."` 提交成功时的示例输出：

```text
[feature/about-page a1b2c3d] Add about page
 1 file changed, 1 insertion(+)
 create mode 100644 pages/about.html
```

逐行解读：

| 输出内容 | 含义 |
| --- | --- |
| `[feature/about-page a1b2c3d]` | 方括号内：提交所在分支名，以及该提交的短哈希 |
| `Add about page` | 本次提交的提交信息 |
| `1 file changed, 1 insertion(+)` | 统计改动：有 1 个文件发生变化，新增了 1 行 |
| `create mode 100644 pages/about.html` | 这是一个新增文件；`100644` 表示普通文件权限 |

## 查看状态、差异和历史

### 查看仓库状态

```bash
git status
```

示例输出：

```text
On branch feature/login
Your branch is ahead of 'origin/feature/login' by 2 commits.
  (use "git push" to publish your local commits)

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
	new file:   src/auth.js

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   src/login.js

Untracked files:
  (use "git add <file>..." to include in what will be committed)
	tests/auth.test.js
```

逐行解读：

| 输出内容 | 含义 |
| --- | --- |
| `On branch feature/login` | 当前所在分支 |
| `ahead of 'origin/feature/login' by 2 commits` | 本地比远程多 2 次提交，执行 `git push` 可同步 |
| `Changes to be committed` | 已暂存，下次 `git commit` 会包含这些改动 |
| `new file:   src/auth.js` | 新文件已通过 `git add` 进入暂存区 |
| `Changes not staged for commit` | 已修改但尚未 `git add` 的文件 |
| `modified:   src/login.js` | 文件有改动，还没有执行 `git add` |
| `Untracked files` | 新文件，Git 完全不知道它的存在 |
| `tests/auth.test.js` | 未被跟踪的新文件，需要 `git add` 才能纳入版本管理 |

简洁格式：

```bash
git status -sb
```

示例：

```text
## feature/login
 M src/login.js
?? src/login.test.js
```

含义：

- `M`：文件已修改
- `??`：新文件未被 Git 跟踪
- `A`：新文件已加入暂存区
- `D`：文件被删除

### 查看未暂存差异

```bash
git diff
```

示例输出：

```diff
diff --git a/src/login.js b/src/login.js
index 3a1b2c4..d5e6f7a 100644
--- a/src/login.js
+++ b/src/login.js
@@ -10,7 +10,8 @@ function login(user) {
   if (!user) {
-    return null;
+    return { error: "User not found" };
   }
 }
```

逐行解读：

| 输出内容 | 含义 |
| --- | --- |
| `diff --git a/src/login.js b/src/login.js` | 正在比较的文件，`a/` 是修改前版本，`b/` 是修改后版本 |
| `index 3a1b2c4..d5e6f7a 100644` | 旧对象哈希 → 新对象哈希；`100644` 是文件权限（普通文件） |
| `--- a/src/login.js` | 以 `---` 标注的是修改前的文件版本 |
| `+++ b/src/login.js` | 以 `+++` 标注的是修改后的文件版本 |
| `@@ -10,7 +10,8 @@` | 变更块位置：旧文件从第 10 行起共 7 行；新文件从第 10 行起共 8 行 |
| `-    return null;` | 被删除的行，终端通常显示为红色 |
| `+    return { error: "..." };` | 新增的行，终端通常显示为绿色 |
| 无前缀的行 | 未修改的上下文行，帮助定位代码位置，不会被提交 |

### 查看已暂存差异

```bash
git diff --staged
```

也可以使用：

```bash
git diff --cached
```

### 查看某个文件差异

```bash
git diff src/app.js
```

### 查看提交历史

完整日志：

```bash
git log
```

示例输出：

```text
commit f3a1b2c4d5e6f7a8b9c0d1e2f3a4b5c6d7e8f9a0
Author: Alice <alice@example.com>
Date:   Wed Jan 17 14:05:33 2024 +0800

    Add about page

    Added a static about page under pages/about.html.

commit a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0
Author: Alice <alice@example.com>
Date:   Mon Jan 15 10:23:45 2024 +0800

    Initial commit
```

逐行解读：

| 输出内容 | 含义 |
| --- | --- |
| `commit f3a1b2c4...` | 该提交的完整 40 位哈希，全局唯一标识这次提交 |
| `Author: Alice <alice@example.com>` | 提交作者的姓名和邮箱 |
| `Date:   Wed Jan 17 14:05:33 2024 +0800` | 提交时间及时区；多次提交按从新到旧排列 |
| 空行后的段落 | 提交信息；第一行是标题，空行后是详细说明（可选） |

单行日志：

```bash
git log --oneline
```

图形化日志：

```bash
git log --oneline --graph --decorate --all
```

示例输出：

```text
* f3a1b2c (HEAD -> main) Merge branch 'feature/login'
|\
| * d4e5f6g (origin/feature/login, feature/login) Add login validation
| * a1b2c3d Add login form
* | b2c3d4e Update homepage layout
|/
* e1f2g3h Initialize project
```

逐行解读：

```text
* f3a1b2c (HEAD -> main) Merge branch 'feature/login'
```
`*` 是一次提交；`HEAD -> main` 表示这是当前分支（main）的最新提交；括号后是提交信息。

```text
|\
```
分支在这里分叉。左侧 `|` 是 main 的历史线，右侧 `\` 延伸出 feature 分支的历史线。

```text
| * d4e5f6g (origin/feature/login, feature/login) Add login validation
| * a1b2c3d Add login form
```
`|` 右侧的 `*` 是 feature/login 分支上的提交，缩进在管道符右边。括号内同时出现本地分支和远程跟踪分支，说明两者指向同一提交。

```text
* | b2c3d4e Update homepage layout
```
左侧的 `*` 是 main 分支在同一时期的独立提交，与 feature 分支并行进行，两者互不干扰。

```text
|/
* e1f2g3h Initialize project
```
`|/` 表示两条历史线在此汇合，上面的 merge commit 就是合并产生的节点。最后这个 `*` 是两个分支的共同祖先提交。

查看某个文件历史：

```bash
git log -- src/app.js
```

查看某次提交详情：

```bash
git show a1b2c3d
```

示例输出：

```text
commit a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0
Author: Alice <alice@example.com>
Date:   Mon Jan 15 10:23:45 2024 +0800

    Add login form

    Includes basic validation for required fields.

diff --git a/src/login.js b/src/login.js
new file mode 100644
index 0000000..3a1b2c4
--- /dev/null
+++ b/src/login.js
@@ -0,0 +1,5 @@
+export function login(user) {
+  if (!user) {
+    return null;
+  }
+}
```

逐行解读：

| 输出内容 | 含义 |
| --- | --- |
| `commit a1b2c3d4e5f6...` | 完整提交哈希，唯一标识这次提交，可用前几位引用 |
| `Author: Alice <alice@example.com>` | 提交作者的姓名和邮箱，对应 `git config user.name/email` 配置 |
| `Date:   Mon Jan 15 10:23:45 2024 +0800` | 提交时间；`+0800` 是时区偏移量（东八区即中国标准时间） |
| 空行后的第一段文字 | 提交信息的标题行，应简洁说明做了什么 |
| 空行后的第二段文字 | 提交信息的详细描述，说明为什么这样改（可选） |
| `diff --git ...` 及之后 | 该提交改动的完整 diff，格式与 `git diff` 输出一致 |
| `new file mode 100644` | 表示这是新建文件；`--- /dev/null` 说明改动前该文件不存在 |

只查看某次提交的文件列表：

```bash
git show --name-only a1b2c3d
```

示例输出：

```text
commit a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0
Author: Alice <alice@example.com>
Date:   Mon Jan 15 10:23:45 2024 +0800

    Add login form

    Includes basic validation for required fields.

src/login.js
src/login.css
tests/login.test.js
```

逐行解读：

| 输出内容 | 含义 |
| --- | --- |
| 顶部的提交元数据 | 与 `git show` 相同，显示哈希、作者、时间和提交信息 |
| 末尾的文件路径列表 | 本次提交中被新增、修改或删除的文件路径，每行一个，不显示具体 diff 内容 |

与 `git show` 的区别：`--name-only` 只列出文件名，省略 diff，适合快速了解某次提交改了哪些文件。

## 分支使用

### 查看分支

查看本地分支：

```bash
git branch
```

示例输出：

```text
* feature/login
  develop
  main
```

逐行解读：

| 输出内容 | 含义 |
| --- | --- |
| `* feature/login` | 前缀 `*` 表示当前所在分支（HEAD 指向这里） |
| `  develop` | 其他本地分支，无 `*` 前缀 |
| `  main` | 其他本地分支 |

查看本地和远程分支：

```bash
git branch -a
```

示例输出：

```text
* feature/login
  develop
  main
  remotes/origin/HEAD -> origin/main
  remotes/origin/develop
  remotes/origin/feature/login
  remotes/origin/main
```

逐行解读：

| 输出内容 | 含义 |
| --- | --- |
| `* feature/login` / `  develop` / `  main` | 本地分支，与 `git branch` 结果相同 |
| `remotes/origin/HEAD -> origin/main` | 远程仓库的默认分支指向；`origin/main` 是服务器检出的默认分支 |
| `remotes/origin/develop` 等 | 远程跟踪分支，是上次 fetch/pull 时从远程同步的只读快照 |

查看分支与上游关系：

```bash
git branch -vv
```

示例输出：

```text
* feature/login  a1b2c3d [origin/feature/login: ahead 2] Add login form
  main           e1f2g3h [origin/main] Initialize project
  develop        b2c3d4e [origin/develop: behind 3] Update README
```

逐行解读，以第一行为例：

```text
* feature/login  a1b2c3d [origin/feature/login: ahead 2] Add login form
```

| 字段 | 示例值 | 含义 |
| --- | --- | --- |
| `*` | `*` | 当前所在分支（其他分支没有这个符号） |
| 分支名 | `feature/login` | 本地分支名 |
| 短哈希 | `a1b2c3d` | 该分支最新提交的短哈希，可用于引用该提交 |
| `origin/feature/login` | — | 该分支追踪的远程分支（upstream） |
| `ahead 2` | — | 本地比远程多 2 次提交，执行 `git push` 可同步 |
| `behind 3` | — | 远程比本地多 3 次提交，执行 `git pull` 可同步 |
| 末尾文字 | `Add login form` | 该分支最新提交的提交信息 |

没有 `ahead`/`behind` 表示本地和远程已完全同步。

### 创建分支

```bash
git branch feature/login
```

创建并切换：

```bash
git switch -c feature/login
```

示例输出：

```text
Switched to a new branch 'feature/login'
```

逐行解读：

| 输出内容 | 含义 |
| --- | --- |
| `Switched to a new branch 'feature/login'` | Git 成功创建了新分支并立即切换到该分支，HEAD 现在指向 `feature/login` |

旧命令也可以：

```bash
git checkout -b feature/login
```

示例输出：

```text
Switched to a new branch 'feature/login'
```

逐行解读：

| 输出内容 | 含义 |
| --- | --- |
| `Switched to a new branch 'feature/login'` | 与 `git switch -c` 输出相同，是旧版 Git 的等价命令；效果和含义完全一致 |

### 切换分支

```bash
git switch main
git switch feature/login
```

以切换到 `main` 为例，示例输出：

```text
Switched to branch 'main'
Your branch is up to date with 'origin/main'.
```

逐行解读：

| 输出内容 | 含义 |
| --- | --- |
| `Switched to branch 'main'` | 已成功切换到 main 分支，HEAD 现在指向 `main` |
| `Your branch is up to date with 'origin/main'.` | 本地 main 与远程 origin/main 同步，无差异；如果本地有未推送提交则会显示 `ahead` 信息 |

### 重命名分支

重命名当前分支：

```bash
git branch -m feature/sign-in
```

重命名指定分支：

```bash
git branch -m old-name new-name
```

### 删除分支

删除已合并分支：

```bash
git branch -d feature/login
```

示例输出：

```text
Deleted branch feature/login (was a1b2c3d).
```

逐行解读：

| 输出内容 | 含义 |
| --- | --- |
| `Deleted branch feature/login` | 分支已成功删除 |
| `(was a1b2c3d)` | 删除前该分支最新提交的短哈希；若需找回，可用 `git switch -c feature/login a1b2c3d` 重建 |

强制删除未合并分支：

```bash
git branch -D feature/login
```

示例输出：

```text
Deleted branch feature/login (was a1b2c3d).
```

逐行解读：

| 输出内容 | 含义 |
| --- | --- |
| `Deleted branch feature/login` | 分支已被强制删除，与 `-d` 格式相同 |
| `(was a1b2c3d)` | 删除前的最新提交哈希；`-D` 不检查是否已合并，直接删除，若有未合并提交请谨慎操作 |

删除远程分支：

```bash
git push origin --delete feature/login
```

示例输出：

```text
To github.com:alice/my-project.git
 - [deleted]         feature/login
```

逐行解读：

| 输出内容 | 含义 |
| --- | --- |
| `- [deleted]         feature/login` | 远程分支已被删除；`-` 表示删除操作 |
| 注意 | 本地同名分支不受影响，只删除远程分支；需要时再单独用 `git branch -d feature/login` 删除本地分支 |

### 推送新分支

```bash
git push -u origin feature/login
```

`-u` 会设置上游分支。之后可以直接：

```bash
git push
git pull
```

## 合并、冲突和解决方法

### 合并分支

假设你想把 `feature/login` 合并到 `main`：

```bash
git switch main
git pull
git merge feature/login
```

如果没有冲突，Git 会自动完成合并。

### 快进合并

如果 `main` 没有新的提交，而 `feature/login` 在 `main` 之后继续提交，Git 可以直接把 `main` 指针移动到功能分支最新提交，这叫 fast-forward merge。

```text
main:    A---B
feature:     C---D

合并后：
main:    A---B---C---D
```

fast-forward 合并的示例输出：

```text
Updating e1f2g3h..a1b2c3d
Fast-forward
 src/login.js | 20 ++++++++++++++++++++
 1 file changed, 20 insertions(+)
 create mode 100644 src/login.js
```

逐行解读：

| 输出内容 | 含义 |
| --- | --- |
| `Updating e1f2g3h..a1b2c3d` | main 指针从旧提交哈希移动到新提交哈希 |
| `Fast-forward` | 说明这次合并是快进合并，没有创建额外的 merge commit |
| `src/login.js \| 20 +++++...` | 文件变化统计：文件名、行数，`+` 表示新增行数 |
| `1 file changed, 20 insertions(+)` | 汇总：1 个文件变化，共新增 20 行 |
| `create mode 100644 src/login.js` | 这是一个新建文件 |

### 非快进合并

如果两个分支都各自有新提交，Git 会创建一个 merge commit。

```text
main:    A---B---E
feature:     C---D

合并后：
main:    A---B---E---M
              \     /
               C---D
```

merge commit 合并的示例输出：

```text
Merge made by the 'ort' strategy.
 src/login.js | 20 ++++++++++++++++++++
 1 file changed, 20 insertions(+)
 create mode 100644 src/login.js
```

逐行解读：

| 输出内容 | 含义 |
| --- | --- |
| `Merge made by the 'ort' strategy.` | Git 使用 `ort`（默认合并策略）创建了一个 merge commit；不是 fast-forward |
| `src/login.js \| 20 +++++...` | 被合并分支引入的文件变化 |
| `1 file changed, 20 insertions(+)` | 汇总：本次合并带来的总体改动行数 |

### 强制创建合并提交

有时团队希望保留分支合并记录：

```bash
git merge --no-ff feature/login
```

### 什么是冲突

冲突通常发生在两个分支修改了同一个文件的同一部分，Git 无法自动判断该保留哪一份。

冲突文件会出现类似内容：

```text
<<<<<<< HEAD
console.log("Login");
=======
console.log("Sign in");
>>>>>>> feature/login
```

含义：

- `<<<<<<< HEAD` 到 `=======`：当前分支内容
- `=======` 到 `>>>>>>> feature/login`：被合并分支内容

### 解决冲突步骤

1. 查看冲突文件

```bash
git status
```

2. 打开文件，手动编辑成正确内容

```js
console.log("Login");
```

3. 标记冲突已解决

```bash
git add src/login.js
```

4. 完成合并提交

```bash
git commit
```

如果 Git 已经自动准备好合并提交信息，直接保存退出编辑器即可。

### 放弃一次合并

如果合并过程中发现不想继续：

```bash
git merge --abort
```

## Rebase 的使用

`rebase` 可以把当前分支的提交移动到另一个分支的最新提交之后，使历史更线性。

### 基本示例

```bash
git switch feature/login
git fetch origin
git rebase origin/main
```

无冲突时的示例输出：

```text
Successfully rebased and updated refs/heads/feature/login.
```

逐行解读：

| 输出内容 | 含义 |
| --- | --- |
| `Successfully rebased` | 所有提交都已成功移动到 origin/main 最新提交之后 |
| `updated refs/heads/feature/login` | 本地分支指针已更新到新的提交链末尾；原来的提交哈希（D、E）变成了新的哈希（D'、E'） |

原始历史：

```text
main:    A---B---C
feature:     D---E
```

rebase 后：

```text
main:    A---B---C
feature:         D'---E'
```

注意：`D'` 和 `E'` 是新的提交哈希。

### rebase 发生冲突

解决冲突后：

```bash
git add conflicted-file.js
git rebase --continue
```

跳过当前提交：

```bash
git rebase --skip
```

放弃 rebase：

```bash
git rebase --abort
```

### 交互式 rebase

用于整理提交历史，例如合并提交、修改提交信息、删除提交。

```bash
git rebase -i HEAD~3
```

示例界面：

```text
pick a1b2c3d Add login form
pick b2c3d4e Fix typo
pick c3d4e5f Add login validation
```

常用操作：

| 命令 | 说明 |
| --- | --- |
| pick | 保留提交 |
| reword | 修改提交信息 |
| edit | 停下来修改提交 |
| squash | 合并到前一个提交，并编辑提交信息 |
| fixup | 合并到前一个提交，丢弃当前提交信息 |
| drop | 删除提交 |

把修复拼写的提交合并到前一个：

```text
pick a1b2c3d Add login form
fixup b2c3d4e Fix typo
pick c3d4e5f Add login validation
```

### rebase 的重要规则

不要随意 rebase 已经被多人共享的公共分支，因为 rebase 会改写提交历史。

可以 rebase：

- 自己本地的功能分支
- 尚未被别人基于开发的分支
- PR 合并前清理自己的提交

避免 rebase：

- `main`
- `develop`
- release 分支
- 已经被多人共同使用的分支

## 撤销与回退

Git 的撤销命令很多，关键是先确认要撤销哪个区域的内容。

### 丢弃工作区修改

丢弃某个文件的未暂存修改：

```bash
git restore src/app.js
```

丢弃所有未暂存修改：

```bash
git restore .
```

注意：这会丢失工作区未保存到提交的修改。

### 取消暂存

如果已经 `git add`，但还没有 commit：

```bash
git restore --staged src/app.js
```

取消暂存所有文件：

```bash
git restore --staged .
```

### 修改最后一次提交

如果忘记添加文件：

```bash
git add missing-file.js
git commit --amend
```

示例输出（打开编辑器修改提交信息并保存退出后）：

```text
[feature/login a2b3c4d] Add login form with validation
 Date: Mon Jan 15 10:23:45 2024 +0800
 1 file changed, 25 insertions(+)
 create mode 100644 src/login.js
```

逐行解读：

| 输出内容 | 含义 |
| --- | --- |
| `[feature/login a2b3c4d]` | 方括号内是分支名和新提交的短哈希；注意 amend 会生成一个全新的提交，原来的提交哈希作废 |
| `Add login form with validation` | 编辑器中保存的提交信息 |
| `Date: Mon Jan 15 10:23:45 2024 +0800` | 沿用原提交的创建时间，不是执行 amend 的时间 |
| `1 file changed, 25 insertions(+)` | 包含了新追加的文件在内的改动统计 |

如果只想修改提交信息：

```bash
git commit --amend -m "Better commit message"
```

示例输出：

```text
[feature/login a2b3c4d] Better commit message
 Date: Mon Jan 15 10:23:45 2024 +0800
 1 file changed, 25 insertions(+)
 create mode 100644 src/login.js
```

逐行解读：

| 输出内容 | 含义 |
| --- | --- |
| `[feature/login a2b3c4d]` | 与不加 `-m` 的 amend 格式相同；哈希已更新，原提交哈希作废 |
| `Better commit message` | `-m` 参数直接指定的新提交信息，无需打开编辑器 |
| `Date:` | 同样沿用原提交时间 |

如果最后一次提交已经推送到共享分支，需要谨慎使用 amend，因为它会改写历史。

### reset

`reset` 用来移动当前分支指针，可以影响暂存区和工作区。

只移动 HEAD，保留暂存区和工作区：

```bash
git reset --soft HEAD~1
```

示例输出：

```text
（无输出）
```

此命令通常没有任何输出，执行完毕后 HEAD 移动到上一次提交，但改动仍留在暂存区，可用 `git status` 确认：

```text
On branch feature/login
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   src/login.js
```

移动 HEAD，保留工作区，清空暂存区：

```bash
git reset --mixed HEAD~1
```

示例输出：

```text
Unstaged changes after reset:
M	src/login.js
```

逐行解读：

| 输出内容 | 含义 |
| --- | --- |
| `Unstaged changes after reset:` | HEAD 移动后，原来暂存区的内容被退回到工作区（未暂存状态） |
| `M	src/login.js` | `M` 表示该文件有修改，但已从暂存区退出，等待重新 `git add` |

移动 HEAD，并丢弃工作区和暂存区修改：

```bash
git reset --hard HEAD~1
```

示例输出：

```text
HEAD is now at e1f2g3h Initial commit
```

逐行解读：

| 输出内容 | 含义 |
| --- | --- |
| `HEAD is now at e1f2g3h` | HEAD 已移动到指定提交的短哈希 |
| `Initial commit` | 该提交的提交信息；暂存区和工作区已被完全丢弃，无法恢复（除非通过 reflog） |

`--hard` 有破坏性，使用前确认没有需要保留的改动。

### revert

`revert` 会创建一个新的提交，用来抵消某个旧提交的修改。

```bash
git revert a1b2c3d
```

示例输出（Git 打开编辑器让你确认提交信息后）：

```text
[feature/login b2c3d4e] Revert "Add login form"
 1 file changed, 5 deletions(-)
 delete mode 100644 src/login.js
```

逐行解读：

| 输出内容 | 含义 |
| --- | --- |
| `[feature/login b2c3d4e]` | 新创建的 revert 提交所在分支及其短哈希 |
| `Revert "Add login form"` | 自动生成的提交信息，说明本次提交是在撤销哪一条提交 |
| `1 file changed, 5 deletions(-)` | 改动统计：原提交新增了 5 行，revert 后删除了这 5 行 |
| `delete mode 100644 src/login.js` | 原提交新建了该文件，revert 后文件被删除 |

适合公共分支，因为它不会改写历史。

### reset 和 revert 的区别

| 命令 | 是否改写历史 | 是否适合公共分支 | 典型用途 |
| --- | --- | --- | --- |
| reset | 是 | 通常不适合 | 本地撤销提交 |
| revert | 否 | 适合 | 回滚已经推送的提交 |

### 使用 reflog 找回误删提交

`reflog` 记录 HEAD 的移动历史。

```bash
git reflog
```

示例：

```text
a1b2c3d HEAD@{0}: reset: moving to HEAD~1
d4e5f6g HEAD@{1}: commit: Add payment page
```

找回提交：

```bash
git switch -c recover-payment d4e5f6g
```

## 远程仓库

### 查看远程仓库

```bash
git remote -v
```

示例：

```text
origin  git@github.com:user/repo.git (fetch)
origin  git@github.com:user/repo.git (push)
```

逐行解读：

| 输出内容 | 含义 |
| --- | --- |
| `origin` | 远程仓库的别名；克隆时默认命名为 `origin`，可通过 `git remote rename` 自定义 |
| `git@github.com:user/repo.git` | 远程仓库的 SSH 地址 |
| `(fetch)` | 该地址用于 `git fetch` / `git pull` 时拉取数据 |
| `(push)` | 该地址用于 `git push` 时推送数据；通常 fetch 和 push 地址相同，也可以分开配置 |

### 添加远程仓库

```bash
git remote add origin git@github.com:user/repo.git
```

### 修改远程地址

```bash
git remote set-url origin git@github.com:user/new-repo.git
```

### 拉取远程更新

`fetch` 只下载远程变化，不自动合并：

```bash
git fetch origin
```

示例输出：

```text
remote: Enumerating objects: 5, done.
remote: Counting objects: 100% (5/5), done.
remote: Compressing objects: 100% (3/3), done.
remote: Total 3 (delta 1), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), 892 bytes | 892.00 KiB/s, done.
From github.com:alice/my-project
   e1f2g3h..f3a1b2c  main       -> origin/main
 * [new branch]      feature/nav -> origin/feature/nav
```

逐行解读：

| 输出内容 | 含义 |
| --- | --- |
| `remote: Enumerating/Counting/Compressing...` | 服务器在准备并压缩要传输的对象 |
| `Unpacking objects: 100%...` | 本地正在解包接收到的对象 |
| `From github.com:alice/my-project` | 数据来自哪个远程仓库 |
| `e1f2g3h..f3a1b2c  main -> origin/main` | `main` 分支的远程跟踪指针从旧哈希更新到新哈希；本地 `main` 不受影响 |
| `* [new branch]  feature/nav -> origin/feature/nav` | 远程出现了一个本地没有的新分支 |

`pull` 等于 fetch 加 merge 或 rebase：

```bash
git pull
```

示例输出（fast-forward 情形）：

```text
remote: Enumerating objects: 5, done.
remote: Counting objects: 100% (5/5), done.
remote: Compressing objects: 100% (3/3), done.
remote: Total 3 (delta 1), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), 892 bytes | 892.00 KiB/s, done.
From github.com:alice/my-project
   e1f2g3h..f3a1b2c  main       -> origin/main
Updating e1f2g3h..f3a1b2c
Fast-forward
 README.md | 2 ++
 1 file changed, 2 insertions(+)
```

逐行解读：

| 输出内容 | 含义 |
| --- | --- |
| 前几行 `remote:` 和 `Unpacking` | 与 `git fetch` 相同，表示下载和解包过程 |
| `Updating e1f2g3h..f3a1b2c` | 本地 `main` 指针从旧哈希移动到新哈希 |
| `Fast-forward` | 使用快进方式合并，没有产生新的 merge commit |
| `README.md \| 2 ++` | 拉取的新提交中，README.md 新增了 2 行 |

使用 rebase 方式拉取：

```bash
git pull --rebase
```

示例输出：

```text
remote: Enumerating objects: 5, done.
remote: Counting objects: 100% (5/5), done.
remote: Compressing objects: 100% (3/3), done.
remote: Total 3 (delta 1), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), 892 bytes | 892.00 KiB/s, done.
From github.com:alice/my-project
   e1f2g3h..f3a1b2c  main       -> origin/main
Successfully rebased and updated refs/heads/feature/login.
```

逐行解读：

| 输出内容 | 含义 |
| --- | --- |
| 前几行 `remote:` 和 `Unpacking` | 与普通 `git pull` 的 fetch 阶段相同，表示下载和解包远程对象 |
| `e1f2g3h..f3a1b2c  main -> origin/main` | 远程跟踪指针更新范围 |
| `Successfully rebased and updated refs/heads/feature/login.` | 使用 rebase 而非 merge 完成同步，历史更线性，不产生额外的 merge commit |

### 推送到远程

```bash
git push
```

示例输出：

```text
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Delta compression using up to 8 threads
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 362 bytes | 362.00 KiB/s, done.
Total 3 (delta 1), reused 0 (delta 0), pack-reused 0
To github.com:alice/my-project.git
   e1f2g3h..a1b2c3d  feature/login -> feature/login
```

逐行解读：

| 输出内容 | 含义 |
| --- | --- |
| `Enumerating/Counting/Compressing...` | 本地正在打包要上传的对象 |
| `Writing objects: 100%...` | 对象正在上传到远程服务器 |
| `To github.com:alice/my-project.git` | 推送目标的远程地址 |
| `e1f2g3h..a1b2c3d  feature/login -> feature/login` | 远程 `feature/login` 分支从旧哈希更新到新哈希 |

首次推送新分支：

```bash
git push -u origin feature/login
```

示例输出：

```text
Enumerating objects: 8, done.
Counting objects: 100% (8/8), done.
Delta compression using up to 8 threads
Compressing objects: 100% (5/5), done.
Writing objects: 100% (8/8), 734 bytes | 734.00 KiB/s, done.
Total 8 (delta 1), reused 0 (delta 0), pack-reused 0
remote: Resolving deltas: 100% (1/1), done.
To github.com:alice/my-project.git
 * [new branch]      feature/login -> origin/feature/login
Branch 'feature/login' set up to track remote branch 'feature/login' from 'origin'.
```

逐行解读：

| 输出内容 | 含义 |
| --- | --- |
| `* [new branch]  feature/login -> origin/feature/login` | 远程仓库上创建了一个同名新分支 |
| `Branch 'feature/login' set up to track remote branch 'feature/login' from 'origin'.` | `-u` 的效果：建立了本地分支与远程分支的追踪关系，之后可直接用 `git push` / `git pull` 不加参数 |

### 强制推送

如果 rebase 后需要更新远程分支：

```bash
git push --force-with-lease
```

成功时的示例输出：

```text
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Delta compression using up to 8 threads
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 362 bytes | 362.00 KiB/s, done.
Total 3 (delta 1), reused 0 (delta 0), pack-reused 0
To github.com:alice/my-project.git
 + a1b2c3d...b2c3d4e feature/login -> feature/login (forced update)
```

被拒绝时的示例输出（有人在你 rebase 后更新了远程）：

```text
error: failed to push some refs to 'github.com:alice/my-project.git'
hint: Updates were rejected because the remote contains work that you do not
hint: have locally. This is usually caused by another push.
```

逐行解读：

| 输出内容 | 含义 |
| --- | --- |
| `(forced update)` | 成功时末尾标识，表示强制更新，覆盖了远程历史 |
| `+` 前缀 | 与普通推送的空格前缀不同，`+` 表示这是强制推送 |
| `error: failed to push some refs` | 被拒绝时的错误信息 |
| `Updates were rejected because the remote contains work...` | `--force-with-lease` 的保护生效：自你上次 fetch 后，远程已被他人更新，Git 拒绝推送以免覆盖别人的提交 |
| 被拒绝时的处理 | 先执行 `git fetch`，检查远程新提交，再决定如何处理 |

优先使用 `--force-with-lease`，它比 `--force` 更安全，因为它会检查远程分支是否被别人更新。

## 团队协作工作流

### 常见分支模型

简单团队可以使用：

```text
main
feature/*
fix/*
```

较复杂团队可能使用：

```text
main
develop
feature/*
release/*
hotfix/*
```

### 推荐日常流程

1. 更新主分支

```bash
git switch main
git pull
```

2. 创建功能分支

```bash
git switch -c feature/user-profile
```

3. 开发并提交

```bash
git add src/profile.js
git commit -m "Add user profile page"
```

4. 与主分支同步

```bash
git fetch origin
git rebase origin/main
```

5. 推送分支

```bash
git push -u origin feature/user-profile
```

6. 创建 Pull Request 或 Merge Request

在 GitHub / GitLab 页面选择：

```text
base: main
compare: feature/user-profile
```

7. 代码评审后合并

合并方式通常有：

- Merge commit
- Squash and merge
- Rebase and merge

### Pull Request 写法示例

标题：

```text
Add user profile page
```

描述：

```markdown
## Summary

- Add user profile page
- Load current user data
- Add empty state when profile is incomplete

## Testing

- Ran unit tests
- Manually checked profile page in browser

## Screenshots

Attach screenshots if UI changed.
```

### Commit message 建议

好的提交信息应该说明“做了什么”和“为什么做”。

推荐：

```text
Add validation for checkout form
```

不推荐：

```text
fix
update
changes
```

可以使用 Conventional Commits：

```text
feat: add user profile page
fix: handle empty cart checkout
docs: update Git guide
test: add login validation tests
refactor: simplify payment service
```

## Stash 临时保存

`stash` 用来临时保存未提交的修改，适合需要快速切换分支但当前工作还不想提交的场景。

### 保存修改

```bash
git stash
```

示例输出：

```text
Saved working directory and index state WIP on feature/login: a1b2c3d Add login form
```

逐行解读：

| 输出内容 | 含义 |
| --- | --- |
| `Saved working directory and index state` | 工作区和暂存区的改动已被保存 |
| `WIP on feature/login:` | 当前所在分支名（WIP = Work In Progress） |
| `a1b2c3d Add login form` | 保存时 HEAD 所指向的提交哈希和提交信息，帮助你识别这条 stash 的上下文 |

带说明：

```bash
git stash push -m "WIP login form"
```

示例输出：

```text
Saved working directory and index state On feature/login: WIP login form
```

逐行解读：

| 输出内容 | 含义 |
| --- | --- |
| `Saved working directory and index state` | 工作区和暂存区的改动已被保存，与 `git stash` 输出格式相同 |
| `On feature/login:` | 执行 stash 时所在的分支名 |
| `WIP login form` | `-m` 参数指定的自定义描述，代替了自动生成的提交信息，便于在 `git stash list` 中识别 |

包括未跟踪文件：

```bash
git stash -u
```

### 查看 stash 列表

```bash
git stash list
```

示例：

```text
stash@{0}: On feature/login: WIP login form
stash@{1}: On main: Try new navbar
```

### 恢复 stash

恢复并保留 stash：

```bash
git stash apply stash@{0}
```

示例输出：

```text
On branch feature/login
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   src/login.js

no changes added to commit (use "git add" and/or "git commit -a")
```

逐行解读：

| 输出内容 | 含义 |
| --- | --- |
| `On branch feature/login` | 当前所在分支 |
| `Changes not staged for commit:` | stash 中的修改已恢复到工作区，但处于未暂存状态 |
| `modified:   src/login.js` | 被恢复的具体文件；需要重新 `git add` 才能暂存 |

`apply` 不会删除 stash 条目，可再次应用。

恢复并删除 stash：

```bash
git stash pop
```

示例输出：

```text
On branch feature/login
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   src/login.js

Dropped stash@{0} (7a3f1b2c4d5e6f7a8b9c0d1e2f3a4b5c6d7e8f9a)
```

`pop` 与 `apply` 的输出基本相同，区别在于最后一行 `Dropped stash@{0} (...)` 表示该 stash 条目在恢复后已被自动删除。

删除某个 stash：

```bash
git stash drop stash@{0}
```

示例输出：

```text
Dropped stash@{0} (7a3f1b2c4d5e6f7a8b9c0d1e2f3a4b5c6d7e8f9a)
```

逐行解读：

| 输出内容 | 含义 |
| --- | --- |
| `Dropped stash@{0}` | 指定的 stash 条目已被成功删除 |
| 括号内的哈希 | 该 stash 的完整对象哈希，仅供参考；删除后无法通过常规方式恢复 |

清空所有 stash：

```bash
git stash clear
```

## Tag 版本标签

Tag 用来标记版本，例如 `v1.0.0`。

### 查看标签

```bash
git tag
```

示例输出：

```text
v0.9.0
v1.0.0
v1.1.0
v2.0.0-beta
```

逐行解读：

| 输出内容 | 含义 |
| --- | --- |
| 每一行 | 一个标签名，按字母/版本号顺序排列 |
| `v1.0.0` 等 | 通常遵循语义化版本规范（SemVer）：主版本.次版本.补丁 |
| `v2.0.0-beta` | 带预发布标识的标签，说明这是测试版本 |

如果仓库没有任何标签，命令不会有任何输出。

### 创建轻量标签

```bash
git tag v1.0.0
```

### 创建带注释标签

推荐发布版本使用带注释标签：

```bash
git tag -a v1.0.0 -m "Release version 1.0.0"
```

### 推送标签

推送单个标签：

```bash
git push origin v1.0.0
```

示例输出：

```text
Enumerating objects: 1, done.
Counting objects: 100% (1/1), done.
Writing objects: 100% (1/1), 162 bytes | 162.00 KiB/s, done.
Total 1 (delta 0), reused 0 (delta 0), pack-reused 0
To github.com:alice/my-project.git
 * [new tag]         v1.0.0 -> v1.0.0
```

逐行解读：

| 输出内容 | 含义 |
| --- | --- |
| `* [new tag]         v1.0.0 -> v1.0.0` | 远程仓库上成功创建了新标签 `v1.0.0`；`*` 表示这是新创建的对象 |

推送所有标签：

```bash
git push origin --tags
```

示例输出：

```text
Enumerating objects: 3, done.
Counting objects: 100% (3/3), done.
Writing objects: 100% (3/3), 486 bytes | 486.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
To github.com:alice/my-project.git
 * [new tag]         v0.9.0 -> v0.9.0
 * [new tag]         v1.0.0 -> v1.0.0
 * [new tag]         v1.1.0 -> v1.1.0
```

逐行解读：

| 输出内容 | 含义 |
| --- | --- |
| 每个 `* [new tag]` 行 | 对应一个被推送到远程的标签；本次共推送了 3 个本地有而远程没有的标签 |

### 删除标签

删除本地标签：

```bash
git tag -d v1.0.0
```

示例输出：

```text
Deleted tag 'v1.0.0' (was a1b2c3d)
```

逐行解读：

| 输出内容 | 含义 |
| --- | --- |
| `Deleted tag 'v1.0.0'` | 本地标签已成功删除 |
| `(was a1b2c3d)` | 该标签删除前指向的提交哈希；如需找回，可用 `git tag v1.0.0 a1b2c3d` 重新创建 |

删除远程标签：

```bash
git push origin --delete v1.0.0
```

示例输出：

```text
To github.com:alice/my-project.git
 - [deleted]         v1.0.0
```

逐行解读：

| 输出内容 | 含义 |
| --- | --- |
| `- [deleted]         v1.0.0` | 远程仓库上的标签已被删除；`-` 表示删除操作 |
| 注意 | 删除远程标签后，本地标签仍然存在，需单独用 `git tag -d v1.0.0` 删除本地标签 |

## .gitignore 忽略文件

`.gitignore` 用来告诉 Git 不要跟踪某些文件。

常见应该忽略：

- 依赖目录，例如 `node_modules/`
- 构建产物，例如 `dist/`、`build/`
- 日志文件，例如 `*.log`
- 本地环境文件，例如 `.env`
- IDE 配置，视团队约定而定
- 系统文件，例如 `.DS_Store`

示例：

```gitignore
# Dependencies
node_modules/
vendor/

# Build outputs
dist/
build/
coverage/

# Logs
*.log
npm-debug.log*

# Environment files
.env
.env.local

# macOS
.DS_Store

# Editor
.vscode/
.idea/
```

### 已被跟踪的文件不会因为 .gitignore 自动取消跟踪

如果文件已经提交过，后来加入 `.gitignore` 不会自动生效。需要：

```bash
git rm --cached .env
git commit -m "Stop tracking env file"
```

`git rm --cached .env` 的示例输出：

```text
rm '.env'
```

逐行解读：

| 输出内容 | 含义 |
| --- | --- |
| `rm '.env'` | Git 将该文件从暂存区移除，不再追踪；本地文件仍然保留，只是不再被 Git 管理 |
| 后续操作 | 执行后还需 `git commit -m "Stop tracking env file"` 才能将该变更写入历史 |

保留本地文件，但从 Git 记录中移除跟踪。

## 常用高级命令

### cherry-pick

把某个提交应用到当前分支。

```bash
git cherry-pick a1b2c3d
```

示例输出：

```text
[main b2c3d4e] Add login form
 Date: Mon Jan 15 10:23:45 2024 +0800
 1 file changed, 20 insertions(+)
 create mode 100644 src/login.js
```

逐行解读：

| 输出内容 | 含义 |
| --- | --- |
| `[main b2c3d4e]` | cherry-pick 在当前分支（main）上创建了一个新提交，哈希为 `b2c3d4e`（注意：与原提交哈希不同） |
| `Add login form` | 沿用了原提交的提交信息 |
| `Date:` | 显示原提交的创建时间（非 cherry-pick 执行时间） |
| `1 file changed, 20 insertions(+)` | 本次 cherry-pick 引入的改动统计 |

适合：

- 只想拿某个 bug fix
- 从 release 分支同步一个补丁

### bisect

用于定位哪个提交引入了 bug。

开始：

```bash
git bisect start
git bisect bad
git bisect good v1.0.0
```

示例输出：

```text
Bisecting: 6 revisions left to test after this (roughly 3 steps)
[d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0u1v2] Add payment service
```

逐行解读：

| 输出内容 | 含义 |
| --- | --- |
| `Bisecting: 6 revisions left to test after this` | 在已知好版本和坏版本之间还有 6 次提交待检测 |
| `roughly 3 steps` | 二分查找还需要大约 3 步就能定位到引入问题的提交 |
| `[d4e5f6g7...]` | Git 自动切换到的中间提交哈希 |
| `Add payment service` | 该中间提交的提交信息，帮助你判断是否需要测试这个时间点的代码 |

你运行测试后告诉 Git：

```bash
git bisect good
```

或：

```bash
git bisect bad
```

Git 会继续缩小范围，每次输出类似格式，直到找到第一个 bad 提交：

```text
d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0u1v2 is the first bad commit
commit d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0u1v2
Author: Bob <bob@example.com>
Date:   Fri Feb 2 11:30:00 2024 +0800

    Add payment service
```

结束：

```bash
git bisect reset
```

### blame

查看某个文件每一行最后是谁修改的：

```bash
git blame src/app.js
```

示例输出：

```text
a1b2c3d4 (Alice   2024-01-15 10:23:45 +0800  1) import { validateUser } from './auth';
b2c3d4e5 (Bob     2024-01-20 14:30:12 +0800  2) 
a1b2c3d4 (Alice   2024-01-15 10:23:45 +0800  3) export function login(user) {
d4e5f6g7 (Charlie 2024-02-01 09:15:33 +0800  4)   if (!validateUser(user)) {
a1b2c3d4 (Alice   2024-01-15 10:23:45 +0800  5)     return null;
d4e5f6g7 (Charlie 2024-02-01 09:15:33 +0800  6)   }
a1b2c3d4 (Alice   2024-01-15 10:23:45 +0800  7) }
```

每行格式为：

```text
[哈希] ([作者]  [日期]  [时区]  [行号]) [代码]
```

以第一行为例：

```text
a1b2c3d4 (Alice   2024-01-15 10:23:45 +0800  1) import { validateUser } from './auth';
```

| 字段 | 示例值 | 含义 |
| --- | --- | --- |
| `a1b2c3d4` | 短哈希 | 最后修改该行的那次提交的 ID |
| `Alice` | 作者名 | 做出该提交的人 |
| `2024-01-15 10:23:45` | 提交时间 | 该行最后被改动的时间 |
| `+0800` | 时区 | UTC 偏移量，`+0800` 为中国标准时间 |
| `1` | 行号 | 该行在文件中的位置 |
| `import { validateUser }...` | 代码内容 | 该行的实际代码 |

同一哈希出现在连续多行，说明这些行都是在同一次提交中一起写入的。用 `git blame` 可以快速定位是谁引入了某段代码，以及为什么做这次改动（结合 `git show <哈希>` 查看提交详情）。

查看指定行范围：

```bash
git blame -L 10,30 src/app.js
```

### clean

删除未被跟踪的文件。

先预览：

```bash
git clean -n
```

示例输出：

```text
Would remove build/app.js
Would remove build/app.css
Would remove temp.log
```

逐行解读：

| 输出内容 | 含义 |
| --- | --- |
| `Would remove build/app.js` | 如果执行 `git clean -f`，这个文件将被删除；`-n` 只预览，不实际删除 |
| 每一行 `Would remove ...` | 一个将被清除的未跟踪文件或目录（不含 `.gitignore` 中已忽略的文件，需加 `-x` 才会显示忽略文件） |

删除未跟踪文件：

```bash
git clean -f
```

删除未跟踪文件和目录：

```bash
git clean -fd
```

这是破坏性操作，建议先用 `-n` 预览。

### worktree

`git worktree` 可以让同一个仓库同时签出多个分支到不同目录。

```bash
git worktree add ../my-project-hotfix hotfix/payment
```

查看：

```bash
git worktree list
```

示例输出：

```text
/Users/alice/my-project          a1b2c3d [main]
/Users/alice/my-project-hotfix   d4e5f6g [hotfix/payment]
```

逐行解读：

| 输出内容 | 含义 |
| --- | --- |
| 第一列（路径） | 每个 worktree 在磁盘上的绝对路径 |
| 第二列（短哈希） | 该 worktree 当前 HEAD 所指向的提交哈希 |
| 第三列（方括号内） | 该 worktree 当前检出的分支名；如果处于 detached HEAD 状态则显示哈希 |
| 第一行 | 主 worktree（即原始仓库目录） |
| 其余行 | 通过 `git worktree add` 创建的额外 worktree |

删除：

```bash
git worktree remove ../my-project-hotfix
```

### submodule

子模块用于在一个仓库中引用另一个仓库。

添加：

```bash
git submodule add git@github.com:user/library.git external/library
```

克隆包含子模块的仓库：

```bash
git clone --recurse-submodules git@github.com:user/repo.git
```

初始化和更新：

```bash
git submodule update --init --recursive
```

子模块会增加维护复杂度，使用前需要确认团队确实需要。

### Git LFS

Git LFS 用于管理大文件，例如设计稿、模型文件、视频等。

安装：

```bash
git lfs install
```

跟踪文件类型：

```bash
git lfs track "*.psd"
git add .gitattributes
git commit -m "Track PSD files with Git LFS"
```

## Git 配置、别名和 SSH

### 查看配置来源

```bash
git config --list --show-origin
```

配置级别：

- `--system`：系统级
- `--global`：用户级
- `--local`：当前仓库级

### 常用别名

```bash
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.lg "log --oneline --graph --decorate --all"
```

使用：

```bash
git st
git lg
```

### 生成 SSH Key

```bash
ssh-keygen -t ed25519 -C "your-email@example.com"
```

启动 ssh-agent：

```bash
eval "$(ssh-agent -s)"
```

添加私钥：

```bash
ssh-add ~/.ssh/id_ed25519
```

复制公钥：

macOS：

```bash
pbcopy < ~/.ssh/id_ed25519.pub
```

Linux：

```bash
xclip -selection clipboard < ~/.ssh/id_ed25519.pub
```

Windows PowerShell：

```powershell
Get-Content ~/.ssh/id_ed25519.pub | Set-Clipboard
```

把公钥添加到 GitHub / GitLab 账号的 SSH Keys 页面。

测试：

```bash
ssh -T git@github.com
```

## VS Code 和 GUI 工具

### VS Code 常用操作

VS Code 左侧 Source Control 面板可以完成：

- 查看修改文件
- 查看 diff
- 暂存文件
- 提交
- 切换分支
- 解决冲突
- 推送和拉取

命令面板：

```text
Cmd/Ctrl + Shift + P
```

常用命令：

```text
Git: Clone
Git: Checkout to...
Git: Create Branch
Git: Commit
Git: Pull
Git: Push
```

### GUI 工具

常见 GUI：

- GitHub Desktop
- Sourcetree
- Fork
- GitKraken
- Tower

GUI 适合查看历史、解决冲突和审查差异，但建议至少掌握基本命令行，因为命令行更通用，也更适合自动化和排查问题。

## 最佳实践

### 提交要小而清晰

一次提交只做一件逻辑完整的事情。

推荐：

```text
Add checkout form validation
```

不推荐把无关改动混在一起：

```text
Update checkout, refactor navbar, change README, fix tests
```

### 提交前先检查

```bash
git status
git diff
git diff --staged
```

确认只提交你想提交的内容。

### 不要提交敏感信息

不要提交：

- 密码
- API Token
- `.env`
- 私钥
- 数据库备份

如果误提交敏感信息，即使之后删除，历史中仍然可能存在。需要立即轮换密钥，并清理历史。

### 保持分支命名清晰

示例：

```text
feature/login-page
fix/checkout-timeout
docs/git-guide
refactor/payment-service
hotfix/production-login
```

### 经常同步主分支

长期分支越久不合并，冲突风险越高。

```bash
git fetch origin
git rebase origin/main
```

或：

```bash
git merge origin/main
```

使用 rebase 还是 merge 应遵循团队约定。

### 公共分支慎用强制推送

不要在 `main`、`develop` 或多人共享分支上随意：

```bash
git push --force
```

如果必须强推自己的功能分支，优先：

```bash
git push --force-with-lease
```

### PR 应该容易审查

好的 PR：

- 范围清晰
- 描述完整
- 提交历史可读
- 包含测试说明
- UI 改动有截图
- 不混入无关格式化或重构

## 常见问题排查

### 问题：fatal: not a git repository

原因：当前目录不是 Git 仓库，或不在仓库子目录中。

解决：

```bash
pwd
ls -la
git status
```

确认是否存在 `.git` 目录。必要时进入正确目录，或初始化：

```bash
git init
```

### 问题：Your branch is ahead of origin/main

含义：本地有提交还没有推送。

解决：

```bash
git push
```

### 问题：Your branch is behind origin/main

含义：远程有新提交，本地没有。

解决：

```bash
git pull
```

或：

```bash
git pull --rebase
```

### 问题：Updates were rejected

常见原因：远程分支有你本地没有的提交。

解决：

```bash
git pull --rebase
git push
```

如果是因为你 rebase 过自己的功能分支：

```bash
git push --force-with-lease
```

### 问题：merge conflict

解决：

```bash
git status
```

打开冲突文件，删除冲突标记，保留正确内容，然后：

```bash
git add conflicted-file
git commit
```

如果在 rebase 中：

```bash
git add conflicted-file
git rebase --continue
```

### 问题：忘记切分支，改在 main 上了

如果还没有提交：

```bash
git switch -c feature/my-work
```

你的工作区修改会跟着切到新分支。

如果已经在 `main` 上提交了：

```bash
git branch feature/my-work
git reset --hard origin/main
git switch feature/my-work
```

执行 `reset --hard` 前要确认 `origin/main` 是你想回到的位置，并且没有未保存的工作区修改。

### 问题：提交到了错误分支

假设错误提交在当前分支最新一次：

```bash
git switch correct-branch
git cherry-pick wrong-branch
git switch wrong-branch
git reset --hard HEAD~1
```

如果已推送，需要根据团队规则选择 `revert` 或 `push --force-with-lease`。

### 问题：误删分支

查看 reflog：

```bash
git reflog
```

找到提交哈希后：

```bash
git switch -c recovered-branch a1b2c3d
```

## 练习示例

### 练习 1：创建本地仓库并提交

```bash
mkdir git-practice
cd git-practice
git init
echo "# Git Practice" > README.md
git status
git add README.md
git commit -m "Initial commit"
git log --oneline
```

目标：

- 理解 `git init`
- 理解工作区、暂存区和提交
- 学会查看历史

### 练习 2：修改文件并查看差异

```bash
echo "This is my first Git practice project." >> README.md
git diff
git add README.md
git diff --staged
git commit -m "Update README description"
```

目标：

- 理解 `git diff`
- 理解 `git diff --staged`
- 提交前检查改动

### 练习 3：创建功能分支

```bash
git switch -c feature/add-notes
echo "My notes" > notes.md
git add notes.md
git commit -m "Add notes file"
git switch main
git merge feature/add-notes
git branch -d feature/add-notes
```

目标：

- 理解分支开发
- 理解合并
- 学会删除已合并分支

### 练习 4：制造并解决冲突

创建分支 A：

```bash
git switch -c feature/title-a
echo "# Title A" > README.md
git add README.md
git commit -m "Change title to A"
```

回到 main 并创建另一个修改：

```bash
git switch main
echo "# Title B" > README.md
git add README.md
git commit -m "Change title to B"
```

合并并触发冲突：

```bash
git merge feature/title-a
```

打开 `README.md`，解决冲突后：

```bash
git add README.md
git commit
```

目标：

- 识别冲突标记
- 手动解决冲突
- 完成合并提交

### 练习 5：使用 stash

```bash
echo "Temporary work" > temp.txt
git status
git stash -u
git status
git stash list
git stash pop
```

目标：

- 临时保存未完成工作
- 恢复 stash

### 练习 6：rebase 同步主分支

```bash
git switch -c feature/rebase-demo
echo "Feature work" > feature.txt
git add feature.txt
git commit -m "Add feature work"
git switch main
echo "Main work" > main.txt
git add main.txt
git commit -m "Add main work"
git switch feature/rebase-demo
git rebase main
git log --oneline --graph --decorate --all
```

目标：

- 理解 rebase 后历史变线性
- 理解 rebase 会生成新的提交哈希

### 练习 7：恢复误删提交

```bash
echo "Important work" > important.txt
git add important.txt
git commit -m "Add important work"
git reset --hard HEAD~1
git reflog
```

找到 `Add important work` 的提交哈希：

```bash
git switch -c recover-important <commit-hash>
```

目标：

- 理解 `reflog`
- 学会找回误删提交

## 学习路径建议

第一阶段：掌握基础命令。

```text
git status
git add
git commit
git log
git diff
```

第二阶段：掌握分支协作。

```text
git branch
git switch
git merge
git pull
git push
```

第三阶段：掌握撤销和冲突处理。

```text
git restore
git reset
git revert
git stash
git merge --abort
```

第四阶段：掌握团队工作流。

```text
Pull Request
Code Review
Rebase
Squash
Release Tag
```

第五阶段：按需学习高级工具。

```text
git cherry-pick
git bisect
git blame
git worktree
git submodule
git lfs
```

## 总结

Git 的学习重点不是背下所有命令，而是理解：

- 文件在工作区、暂存区、本地仓库之间如何流动
- 分支如何指向提交
- 合并和 rebase 如何改变历史结构
- reset、restore、revert 分别适合什么场景
- 团队协作时如何避免破坏公共历史

日常开发中，最常用的命令并不多。先把 `status`、`add`、`commit`、`diff`、`log`、`switch`、`merge`、`pull`、`push` 用熟，再逐步学习 rebase、stash、reflog 等命令，会更稳。
