# 09 看懂 git status 与 git log

## 1. 这一节要解决什么问题

这一节让你能看懂 `git status` 的每种输出，以及 `git log` 里每个字段代表什么。这两个命令是你判断"现在处于什么状态"的最重要工具。

## 2. 基础概念解释

### git status

通俗解释：git status 像仪表盘，告诉你"现在工作区有没有未保存的修改""有没有准备好要提交的文件""本地和远端是否同步"。

技术解释：`git status` 显示当前工作区和暂存区的状态，以及当前分支和远端追踪分支的关系。

例子：每次操作前后都应该先看 `git status`，确认当前状态再行动。

### git log

通俗解释：git log 像翻日记本，显示所有提交记录，包括谁改的、什么时候改的、改了什么。

技术解释：`git log` 显示当前分支的提交历史，从最新到最旧排列，包含 commit hash、作者、时间、message。

例子：`git log --oneline` 是最常用的简洁版，每行一条提交记录。

## 3. 为什么要学这个

如果你不会读 git status，你就不知道"为什么我改了东西但 commit 提示没东西"，或者"为什么工作区显示 dirty"。看懂这两个命令，是独立判断当前状态、不依赖别人告诉你怎么操作的基础。

## 4. 关键知识点

### 4.1 git status 的七种输出状态

**状态1：工作区干净**

```text
On branch feat/weekly-tanglx-w26
Your branch is up to date with 'origin/feat/weekly-tanglx-w26'.

nothing to commit, working tree clean
```

含义：工作区没有任何未提交改动，本地和远端分支同步。  
操作：可以放心切换分支或 pull。

---

**状态2：有修改但未 add（modified）**

```text
On branch feat/weekly-tanglx-w26
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   docs/weekly/2026-W26-tanglx.md
```

含义：文件被修改了，但还没有 git add。  
操作：`git add docs/weekly/2026-W26-tanglx.md` 把它加入暂存区。

---

**状态3：有新文件（Untracked）**

```text
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        docs/weekly/2026-W26-tanglx.md
```

含义：这是 Git 从未见过的新文件，还没有被追踪。  
操作：`git add docs/weekly/2026-W26-tanglx.md` 让 Git 开始追踪它。

---

**状态4：已 add，等待 commit（staged）**

```text
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        new file:   docs/weekly/2026-W26-tanglx.md
```

含义：文件已经在暂存区，下次 commit 会包含它。  
操作：`git commit -m "docs: 添加周报"` 提交。

---

**状态5：本地比远端多 commit（ahead）**

```text
On branch feat/weekly-tanglx-w26
Your branch is ahead of 'origin/feat/weekly-tanglx-w26' by 2 commits.
  (use "git push" to publish your local commits)
```

含义：你本地有 2 个 commit 还没有 push 到 GitHub。  
操作：`git push` 上传。

---

**状态6：本地比远端少 commit（behind）**

```text
On branch main
Your branch is behind 'origin/main' by 3 commits, and can be fast-forwarded.
  (use "git pull" to update your local branch)
```

含义：远端有 3 个新 commit 你本地还没有（队友 push 了新内容）。  
操作：`git pull` 同步。

---

**状态7：本地和远端都有各自的新 commit（diverged）**

```text
Your branch and 'origin/main' have diverged,
and have 1 and 2 different commits each, respectively.
  (use "git pull" to merge the remote branch into yours)
```

含义：你本地有 1 个新 commit，远端有 2 个新 commit，两边都前进了，产生了分叉。  
操作：`git pull` 合并（可能产生冲突），或 `git fetch` 后手动处理。

注意事项：diverged 是最需要注意的状态，处理不当可能有冲突。

### 4.2 看懂 git log 的每个字段

命令：

```powershell
git log -3
```

输出示例：

```text
commit 3a7b1d9f82e4c1b56a9d8e7f12345678abcdef90  ← commit hash（唯一ID）
Author: Firebat <firebat@example.com>             ← 作者（来自 git config）
Date:   Fri Jun 26 20:15:33 2026 +0800            ← 提交时间

    docs: 添加唐玲欣 2026-W26 周报               ← commit message

commit f2e8c1a3456789012bcdef34567890abcdef1234
Author: Firebat <firebat@example.com>
Date:   Fri Jun 26 18:30:00 2026 +0800

    feat: 初始化项目目录结构
```

字段说明：

| 字段 | 说明 |
| --- | --- |
| commit hash | 这次提交的唯一ID（40位十六进制），通常只用前7位 |
| Author | 谁提交的（来自 git config user.name 和 user.email） |
| Date | 什么时候提交的 |
| commit message | 这次提交做了什么（你写的说明） |

### 4.3 git log 常用变体

命令：

```powershell
# 简洁单行（最常用）
git log --oneline

# 简洁 + 图形化分支树（推荐收藏）
git log --oneline --graph --all

# 只看最近 5 条
git log --oneline -5

# 查看某个文件的历史
git log --oneline -- docs/weekly/2026-W26-tanglx.md

# 查看两个 commit 之间的变化
git log --oneline abc1234..def5678
```

### 4.4 在 git log 里看懂 HEAD 和分支标记

命令：

```powershell
git log --oneline --all -10
```

输出示例：

```text
3a7b1d9 (HEAD -> feat/weekly-tanglx-w26) docs: 添加周报
f2e8c1a (origin/main, origin/HEAD, main) feat: 初始化项目
8b9c3d5 docs: 更新 README
```

标记说明：

| 标记 | 说明 |
| --- | --- |
| `HEAD -> feat/weekly-tanglx-w26` | 你现在在这个分支上，最新 commit 是这条 |
| `origin/main` | 上次 fetch 时远端 main 的位置 |
| `origin/HEAD` | 远端仓库的默认分支指向 |
| `main` | 本地 main 分支的最新位置 |

注意事项：如果 `HEAD` 和 `origin/main` 在同一条 commit 上，说明本地和远端同步了。如果 `HEAD` 比 `origin/main` 靠前，说明有未 push 的 commit。

### 4.5 git status 的使用习惯

解释：什么时候应该看 git status。

例子：

```text
✅ 开始工作前：确认工作区干净，当前在哪个分支
✅ git add 之后：确认文件进入了暂存区
✅ git commit 之后：确认暂存区已清空
✅ 准备 push 之前：确认没有漏掉的文件
✅ 遇到任何奇怪情况：先看 status 再说
```

注意事项：养成"不确定就先 git status"的习惯，比什么都强。

## 5. 和前后知识的关系

本节是"读懂当前状态"的工具节。前面每一节讲的操作（add/commit/push/pull），都可以通过 git status 和 git log 来验证是否生效。后面 10 节排错时，第一步也是先看 git status。

## 6. 实战任务

在项目目录里，制造出 git status 的 5 种状态，每次都截图或记录输出：

```powershell
# 状态1：干净状态
git switch main
git status

# 状态2：制造 modified
# （用编辑器改任意一个已有文件的内容，保存）
git status

# 状态3：制造 untracked
New-Item docs\test-untracked.txt
git status

# 状态4：制造 staged
git add docs\test-untracked.txt
git status

# 状态5：查看 ahead（需要先 commit）
git commit -m "test: 临时测试文件"
git status   # 应该看到 ahead

# 清理
git switch main
git branch -d 当前分支  # 如果不是 main 上操作的
git checkout -- .      # 丢弃 main 上的修改（见11节危险命令，这里用于清理）
Remove-Item docs\test-untracked.txt -ErrorAction SilentlyContinue
```

## 7. 检查自己是否学会

1. `nothing to commit, working tree clean` 是什么意思？
2. `modified` 状态下文件在哪个区域？
3. `Untracked files` 说明什么？
4. `Changes to be committed` 说明什么？
5. `ahead of 'origin/main' by 2 commits` 是什么意思？
6. `diverged` 是什么情况？
7. git log 里 HEAD 指向什么？
8. `origin/main` 在 git log 里代表什么？

## 8. 常见误区

1. 误区：git status 显示 modified，说明代码有问题。  
   解释：modified 只是说文件被修改了还没 add，不代表有错误。

2. 误区：git log 里的 `origin/main` 一直是最新远端状态。  
   解释：`origin/main` 只在 fetch/pull 时更新，不是实时的。

3. 误区：git status 显示 clean，代表代码已经 push 了。  
   解释：clean 只代表工作区和最近一次 commit 一致，commit 可能还没有 push。

4. 误区：HEAD 就是 main。  
   解释：HEAD 是"当前位置"的标记，指向你当前所在的分支（或 commit），不一定是 main。

## 9. 本节总结

`git status` 是最重要的"当前状态仪表盘"，七种输出状态都有明确含义。`git log --oneline --graph --all` 是看提交历史的最直观方式，HEAD 标记你现在在哪，`origin/main` 标记上次同步时远端的位置。养成"不确定就先看 status"的习惯。
