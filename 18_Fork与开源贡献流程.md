# 18 Fork 与开源贡献流程

## 1. 这一节要解决什么问题

12 节已经解释了 fork 和 clone 的区别，但没有完整展开开源贡献流程。这一节补齐：如何 fork 别人的仓库、如何配置 `upstream`、如何保持 fork 同步、如何从自己的 fork 向原仓库提交 PR。

学完这一节，你应该能参与一个没有直接写权限的 GitHub 项目。

## 2. 基础概念解释

### Fork

通俗解释：fork 是把别人的 GitHub 仓库复制一份到你自己的 GitHub 账号下。

技术解释：fork 是 GitHub 平台操作，生成一个属于你的远端仓库副本。你对 fork 有写权限，但不会直接影响原仓库。

例子：你 fork `open-source/project` 后，得到 `your-name/project`。

### origin

通俗解释：origin 通常是你 clone 下来的那个远端仓库。

技术解释：在 fork 流程里，origin 通常指向你自己的 fork，而不是原仓库。

例子：`origin = git@github.com:your-name/project.git`

### upstream

通俗解释：upstream 是原始仓库，用来同步上游最新代码。

技术解释：upstream 是手动添加的 remote 名称，通常指向被 fork 的原仓库。

例子：`upstream = git@github.com:open-source/project.git`

### 跨仓库 PR

通俗解释：你在自己的 fork 上改完代码，然后向原仓库发起一个合并请求。

技术解释：Pull Request 的 base repository 是原仓库，head repository 是你的 fork。

例子：`your-name/project:fix-docs` → `open-source/project:main`

## 3. 为什么要学这个

在公司或班级项目里，你通常是仓库 collaborator，可以直接 push 功能分支。但在开源项目里，你往往没有原仓库写权限，不能直接 push。这时就需要 fork 流程：在自己的副本里改，再通过 PR 请求原项目维护者合并。

## 4. 关键知识点

### 4.1 Fork 流程总览

解释：开源贡献的基本路线是 fork → clone fork → 添加 upstream → 新建分支 → 修改 → push 到 fork → 向原仓库开 PR。

流程：

```text
1. 在 GitHub 上 fork 原仓库
2. clone 自己的 fork 到本地
3. 添加 upstream 指向原仓库
4. 从最新 upstream/main 新建功能分支
5. 修改并 commit
6. push 分支到自己的 fork
7. 在 GitHub 上创建跨仓库 PR
8. 根据维护者意见修改
9. PR 合并后，同步自己的 main
```

注意事项：不要在自己的 main 上直接做贡献改动，仍然要新建功能分支。

### 4.2 Fork 并 clone 自己的仓库

解释：你没有原仓库写权限，所以先 fork 到自己账号下，再 clone 自己的 fork。

步骤：

```text
1. 打开原仓库页面
2. 点击右上角 Fork
3. 选择自己的账号
4. 等待 GitHub 创建 fork
5. 进入自己的 fork 页面
6. 复制 SSH 地址
```

命令：

```powershell
git clone git@github.com:your-name/project.git
cd project
git remote -v
```

输出示例：

```text
origin  git@github.com:your-name/project.git (fetch)
origin  git@github.com:your-name/project.git (push)
```

注意事项：确认 origin 指向的是你的 fork，而不是原仓库。

### 4.3 添加 upstream

解释：为了持续获取原仓库更新，需要添加一个 upstream remote。

命令：

```powershell
git remote add upstream git@github.com:open-source/project.git
git remote -v
```

输出示例：

```text
origin    git@github.com:your-name/project.git (fetch)
origin    git@github.com:your-name/project.git (push)
upstream  git@github.com:open-source/project.git (fetch)
upstream  git@github.com:open-source/project.git (push)
```

注意事项：你通常不能 push 到 upstream，因为 upstream 是原仓库。

### 4.4 从最新上游 main 新建分支

解释：贡献前先同步上游 main，减少冲突。

命令：

```powershell
# 获取上游最新内容
git fetch upstream

# 切回本地 main
git switch main

# 合并上游 main
git merge upstream/main

# 同步自己的 fork
git push origin main

# 从最新 main 新建贡献分支
git switch -c docs/fix-typo
```

注意事项：如果本地 main 没有自己的改动，`git merge upstream/main` 通常是 fast-forward。

### 4.5 修改、提交并推送到自己的 fork

解释：所有改动都在功能分支上完成，然后 push 到 origin，也就是自己的 fork。

命令：

```powershell
# 修改文件后
git status
git diff

git add docs/README.md
git commit -m "docs: 修正文档错别字"

# 推送到自己的 fork
git push -u origin docs/fix-typo
```

注意事项：这里 push 的是 `origin docs/fix-typo`，不是 upstream。

### 4.6 创建跨仓库 PR

解释：push 到 fork 后，需要在 GitHub 上向原仓库创建 PR。

PR 方向：

```text
base repository: open-source/project
base branch: main

head repository: your-name/project
compare branch: docs/fix-typo
```

PR 描述建议：

```markdown
## 改动内容
- 修正 README 中的错别字

## 背景
阅读文档时发现该处表述有误。

## 验证方式
- 本地预览 Markdown

## 关联 Issue
Related to #12
```

注意事项：创建 PR 前，确认 base 是原仓库，不是你自己的 fork。

### 4.7 PR 后续修改

解释：开源维护者可能要求你修改。你只需要继续在同一个分支上 commit 并 push，PR 会自动更新。

命令：

```powershell
git switch docs/fix-typo

# 修改文件
git add docs/README.md
git commit -m "docs: 按 review 意见调整表述"
git push
```

注意事项：不要因为 reviewer 要求修改就重新开一个 PR。继续 push 同一分支即可。

### 4.8 PR 合并后的清理

解释：PR 合并后，本地和 fork 都可以清理功能分支，并同步上游 main。

命令：

```powershell
# 切回 main
git switch main

# 同步上游最新 main
git fetch upstream
git merge upstream/main

# 推送到自己的 fork
git push origin main

# 删除本地贡献分支
git branch -d docs/fix-typo

# 删除 fork 上的远端分支
git push origin --delete docs/fix-typo
```

注意事项：确认 PR 已合并后再删除分支。

## 5. 和前后知识的关系

12 节讲 fork 的概念，本节讲 fork 的完整工作流。它和 08 节 PR 流程类似，但多了 `upstream` 和跨仓库 PR。遇到冲突时回看 10 节，涉及撤销时回看 15 节。

## 6. 实战任务

找一个公开仓库做只读练习，不一定真的提交 PR：

```text
1. fork 一个公开仓库
2. clone 自己的 fork
3. 添加 upstream
4. 运行 git remote -v，确认 origin 和 upstream 的区别
5. fetch upstream
6. 从 main 新建一个练习分支
7. 修改一个本地文件但不 push
8. 用 git status 和 git diff 观察变化
```

如果你准备真的贡献开源项目，先阅读该项目的 `CONTRIBUTING.md`。

## 7. 检查自己是否学会

1. fork 和 clone 有什么区别？
2. fork 流程里 origin 通常指向哪里？
3. upstream 通常指向哪里？
4. 为什么贡献前要先同步 upstream/main？
5. 跨仓库 PR 的 base 和 head 分别是什么？

## 8. 常见误区

1. 误区：fork 后原仓库会自动同步我的修改。  
   解释：fork 是独立副本，必须通过 PR 才能把修改提交给原仓库。

2. 误区：origin 永远是原仓库。  
   解释：在 fork 流程中，origin 通常是你的 fork，upstream 才是原仓库。

3. 误区：PR 被要求修改就重新开一个 PR。  
   解释：继续在同一分支提交并 push，原 PR 会自动更新。

4. 误区：可以直接 push upstream。  
   解释：大多数开源项目不会给你 upstream 写权限。

## 9. 本节总结

Fork 流程适合没有原仓库写权限的开源贡献场景。核心关系是：origin 指向你的 fork，upstream 指向原仓库。贡献前同步 upstream/main，从最新 main 新建功能分支，改完 push 到 origin，再向 upstream 创建跨仓库 PR。
