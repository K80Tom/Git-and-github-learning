# 08 推送与 Pull Request

## 1. 这一节要解决什么问题

这一节让你理解 `git push` 做了什么，第一次 push 新分支时为什么要用 `-u`，以及 Pull Request（PR）是什么、为什么团队要用 PR 而不是直接 push main。

## 2. 基础概念解释

### git push

通俗解释：push 像把你本地日记本里的新内容上传一份到共享网盘，让队友也能看到。

技术解释：`git push` 把本地仓库中当前分支的新 commit 推送到远端仓库对应的分支，只传输本地有而远端没有的部分。

例子：你本地有 3 个新 commit，push 之后 GitHub 上也出现这 3 个 commit。

### -u 参数（--set-upstream）

通俗解释：`-u` 是"设置追踪关系"的意思，告诉 Git"我本地的这个分支对应远端的哪个分支"，之后就可以直接 `git push` 不用每次都写完整命令。

技术解释：`-u` 等同于 `--set-upstream`，为本地分支设置上游（upstream）追踪引用。设置后，后续 `git push` 和 `git pull` 无需再指定 remote 和分支名。

例子：第一次 `git push -u origin feat/weekly-tanglx-w26`，之后在该分支上直接 `git push` 即可。

### Pull Request（PR）

通俗解释：PR 是你在 GitHub 上发的一个"请求"，告诉仓库管理员"我在自己分支上改了这些，请看一下，如果没问题请合并进 main"。

技术解释：Pull Request 是 GitHub 提供的代码审查机制，允许团队成员在将分支合并进主分支之前，先查看差异、留下评论、提出修改意见，形成一次正式的 code review。

例子：你 push 了 `feat/weekly-tanglx-w26` 后，在 GitHub 上创建一个 PR，老师可以看到你改了什么，确认没问题后点击 Merge。

## 3. 为什么要学这个

push 是你把本地工作成果同步到团队的关键步骤；PR 是保护 main 分支质量的机制。没有 PR，任何人任何时候都能把未经审查的代码合进 main，会导致项目不稳定。

## 4. 关键知识点

### 4.1 推送到远端的完整流程

解释：push 之前要确认本地有要提交的 commit，且工作区干净。

命令：

```powershell
# 确认当前分支和状态
git branch
git status
git log --oneline -3

# 第一次 push 新分支（需要 -u 设置追踪）
git push -u origin feat/weekly-tanglx-w26

# 之后再 push（在同一分支上）
git push
```

输出结果怎么看：

```text
# 第一次 push 的输出
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Delta compression using up to 8 threads
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 1.23 KiB | 1.23 MiB/s, done.
Total 3 (delta 1), reused 0 (delta 0)
To github.com:Firebat/zhongwen-shortdrama-context.git
 * [new branch]      feat/weekly-tanglx-w26 -> feat/weekly-tanglx-w26
Branch 'feat/weekly-tanglx-w26' set up to track remote branch 'feat/weekly-tanglx-w26' from 'origin'.

# 关键信息：
# [new branch] → GitHub 上新建了这个分支
# set up to track → 追踪关系已设置，之后直接 git push 即可
```

注意事项：看到 `[new branch]` 说明远端之前没有这个分支，是这次 push 才创建的。

### 4.2 为什么第一次 push 要加 -u

解释：不加 `-u` 也能 push 成功，但之后每次都要写 `git push origin 分支名`，很麻烦。

例子：

```powershell
# 不加 -u 的写法（每次都要写完整）
git push origin feat/weekly-tanglx-w26

# 加 -u 之后，之后在这个分支上直接写
git push
git pull
# Git 知道这个分支追踪 origin/feat/weekly-tanglx-w26，不需要再指定
```

注意事项：每个新分支第一次 push 都需要加 `-u`，之后就不需要了。

### 4.3 在 GitHub 上创建 Pull Request

解释：push 之后，GitHub 通常会自动提示你创建 PR。

步骤：

```text
方式一：GitHub 自动提示
1. push 完成后，打开 GitHub 仓库页面
2. 页面顶部会出现黄色提示栏：
   "feat/weekly-tanglx-w26 had recent pushes — Compare & pull request"
3. 点击 "Compare & pull request"

方式二：手动创建
1. 打开 GitHub 仓库页面
2. 点击 "Pull requests" 标签
3. 点击 "New pull request"
4. base 选 main（合并进哪里）
5. compare 选你的功能分支（从哪里合并）
6. 点击 "Create pull request"
```

PR 填写规范：

```text
Title（标题）：
  docs: 添加唐玲欣 2026-W26 周报
  （和 commit message 保持一致风格即可）

Description（描述）：
  ## 改动内容
  - 新增 docs/weekly/2026-W26-tanglx.md

  ## 待确认事项
  - 无

  ## 相关链接
  - 无
```

注意事项：PR 的标题和描述是老师 review 时最先看到的，写清楚改了什么能加快审查效率。

### 4.4 PR 的状态流转

解释：了解 PR 的生命周期，知道自己在哪个阶段应该做什么。

例子：

```text
1. Open（开放）
   你创建 PR，等待 review
   → 操作：等待，如果老师要求修改，继续在同一分支上 commit 和 push

2. Review（审查中）
   老师查看代码，可能：
   a. Approve（通过）→ 可以 merge
   b. Request changes（要求修改）→ 你需要修改后重新提交

3. 如果要修改：
   git switch feat/weekly-tanglx-w26  ← 切回你的分支
   # 修改文件
   git add ...
   git commit -m "fix: 按老师意见修改周报格式"
   git push  ← push 到同一分支，PR 自动更新

4. Merged（已合并）
   老师点击 Merge，你的代码合进 main
   → 操作：git switch main → git pull 同步最新 main

5. Closed（关闭/拒绝）
   PR 被关闭（不合并）
```

注意事项：PR 合并后，在 GitHub 上可以点击"Delete branch"删除远端功能分支，然后本地也可以 `git branch -d` 删除。

### 4.5 push 被拒绝的常见情况

解释：push 不是永远成功的，了解被拒绝的原因才能正确处理。

例子：

```text
# 情况1：远端有你本地没有的提交（most common）
! [rejected] feat/xxx -> feat/xxx (non-fast-forward)
hint: Updates were rejected because the remote contains work that you do not have locally.

解决：先 git pull，合并后再 push

# 情况2：直接 push main 被拒（分支保护规则）
! [remote rejected] main -> main (protected branch hook declined)

解决：不能直接 push main，应该用 PR 流程

# 情况3：没有权限
! [remote rejected] main -> main (permission denied)

解决：确认你是仓库协作者，或联系仓库管理员
```

注意事项：⚠️ `git push -f`（强制推送）能绕过 rejected，但会覆盖远端历史，新手绝对不要随便用。

## 5. 和前后知识的关系

本节是工作流的第七步：commit 完了推送到 GitHub，然后发 PR。后面 09 节讲 git log，能看到 push 之后 commit 在哪里；10 节讲 push 被拒绝的常见报错怎么解决；11 节讲 `git push -f` 为什么危险。

## 6. 实战任务

完整走一遍推送流程：

```powershell
# 1. 确认在功能分支上且有未 push 的 commit
git log --oneline origin/feat/weekly-tanglx-w26..HEAD
# 如果显示你的 commit，说明有未 push 的内容

# 2. 第一次 push
git push -u origin feat/weekly-tanglx-w26

# 3. 打开 GitHub 仓库页面，找到 PR 入口
# 4. 创建 PR，填写标题和描述
# 5. 把 PR 链接记录下来
```

## 7. 检查自己是否学会

1. `git push` 做了什么？
2. 为什么第一次 push 要加 `-u`？
3. 加了 `-u` 之后，下次在同一分支上怎么 push？
4. PR 是什么？为什么团队要用 PR？
5. PR 创建后发现有问题需要修改，怎么操作？
6. push 被 rejected 最常见的原因是什么？
7. 为什么直接 push main 会被拒绝？

## 8. 常见误区

1. 误区：push 成功就等于 PR 创建了。  
   解释：push 只是上传代码到 GitHub，PR 还需要单独在 GitHub 网页上手动创建。

2. 误区：PR 创建后就不能再改了。  
   解释：PR 开着期间，继续在同一分支上 commit 和 push，PR 会自动包含新的提交。

3. 误区：PR merge 后我本地的 main 就自动更新了。  
   解释：GitHub 上 merge 了，你本地的 main 不会自动更新，需要 `git switch main` + `git pull`。

4. 误区：`git push -f` 是解决 push 失败的常用方法。  
   解释：`git push -f` 是危险操作，会覆盖远端历史，正确解决 push 失败应该先 pull 再 push。

## 9. 本节总结

`git push` 把本地 commit 上传到 GitHub，第一次 push 新分支用 `git push -u origin 分支名`，之后直接 `git push`。PR 是在 GitHub 上发起的合并申请，让团队在合并前 review 代码，是保护 main 分支质量的重要机制。push 被拒绝时先 pull 再 push，不要随便用 `git push -f`。
