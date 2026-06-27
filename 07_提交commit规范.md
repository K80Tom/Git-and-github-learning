# 07 提交 commit 规范

## 1. 这一节要解决什么问题

这一节让你理解 commit 不只是"保存"，还是给团队成员看的历史记录。好的 commit message 让人一眼看出"这次改动做了什么"，坏的 commit message 让人完全看不懂，后期维护靠猜。

## 2. 基础概念解释

### Commit Message（提交信息）

通俗解释：commit message 像快递单上的内容备注，写"服装一件"和写"红色连衣裙M码"给人的感受完全不同，后者出问题时能快速定位。

技术解释：commit message 是 `git commit -m "..."` 引号里的文字，附加在这次提交上，出现在 `git log` 里，是团队协作和项目维护的重要文档。

例子：`fix: 修复登录页面密码错误后不清空输入框的问题` 比 `fix bug` 有用得多。

### Conventional Commits（约定式提交）

通俗解释：一套大家约定好的 commit message 格式，用"类型: 描述"的统一格式，让 git log 像一份清晰的操作日志。

技术解释：Conventional Commits 是一种 commit message 规范，格式为 `type(scope): description`，其中 scope 可选，type 固定为几种类型。

例子：`docs(weekly): 添加 2026-W26 周报`、`feat(auth): 新增 JWT 登录功能`。

## 3. 为什么要学这个

三个月后你回看自己的 git log，"update"、"改了一些东西"、"修复"这类 message 完全没用，你根本不知道那次改了什么。规范的 commit message 是给未来的自己和队友的礼物，也是代码 review 和 bug 溯源的重要线索。

## 4. 关键知识点

### 4.1 commit 的基本操作

命令：

```powershell
# 基本提交（-m 后跟 commit message）
git commit -m "docs: 添加 2026-W26 周报"

# 提交时查看暂存区内容（进入编辑器写多行 message）
git commit

# 修改最近一次 commit message（只改信息，不改代码）
# ⚠️ 注意：只能改本地还未 push 的 commit
git commit --amend -m "docs: 修正周报标题格式"
```

注意事项：`git commit --amend` 会改变 commit 的 hash，如果该 commit 已经 push 了，不要用这个命令。

### 4.2 Commit Message 格式规范

解释：推荐格式 `type: 简短描述`，type 用英文，描述可以用中文。

type 类型对照表：

| type | 含义 | 使用场景 |
| --- | --- | --- |
| `feat` | 新功能 | 添加了一个新功能或新内容 |
| `fix` | 修复 bug | 修复了一个问题 |
| `docs` | 文档变更 | 改了 README、添加了周报等 |
| `style` | 格式调整 | 代码格式、空格、缩进（不影响功能） |
| `refactor` | 重构 | 代码结构优化，不改功能 |
| `test` | 测试 | 添加或修改测试 |
| `chore` | 杂项 | 依赖更新、配置变更等 |

例子：

```text
✅ 好的 commit message：
  docs: 新增 2026-W26 周报
  docs(weekly): 添加唐玲欣第26周周报
  fix: 修复用户名显示为 undefined 的问题
  feat: 新增按日期筛选任务的功能
  chore: 升级 fastapi 依赖到 0.115.0

❌ 不好的 commit message：
  update
  修改
  fix bug
  改了点东西
  123
  asdfgh
  test
```

注意事项：中文描述完全没问题，团队内统一风格即可。

### 4.3 完整提交流程

命令：

```powershell
# 第一步：查看当前状态，确认要提交什么
git status

# 第二步：把要提交的文件加入暂存区
git add docs\weekly\2026-W26-tanglx.md

# 第三步：确认暂存区内容
git status

# 第四步：提交（写规范的 commit message）
git commit -m "docs: 添加唐玲欣 2026-W26 周报"

# 第五步：确认提交成功
git log --oneline -3
```

输出结果怎么看：

```text
# git commit 成功的输出
[feat/weekly-tanglx-w26 3a7b1d9] docs: 添加唐玲欣 2026-W26 周报
 1 file changed, 45 insertions(+)
 create mode 100644 docs/weekly/2026-W26-tanglx.md

# 输出含义：
# feat/weekly-tanglx-w26  → 当前分支名
# 3a7b1d9                 → 新 commit 的 hash（前 7 位）
# docs: 添加...           → 你的 commit message
# 1 file changed          → 改了几个文件
```

注意事项：commit hash 是自动生成的，不需要手动填写。

### 4.4 多文件分开提交

解释：不要把不相关的修改塞进一个 commit，应该按逻辑拆分。

例子：

```powershell
# 本次工作修改了两个文件，但它们属于不同任务
# 文件1：docs/weekly/2026-W26-tanglx.md（周报）
# 文件2：README.md（修了个错别字）

# 分开提交，保持历史清晰
git add docs\weekly\2026-W26-tanglx.md
git commit -m "docs: 添加唐玲欣 2026-W26 周报"

git add README.md
git commit -m "fix: 修正 README 中的错别字"
```

注意事项：每个 commit 只做一件事，是良好的工程习惯，方便 code review 和后期 revert。

### 4.5 查看 commit 历史

命令：

```powershell
# 完整日志
git log

# 简洁单行（常用）
git log --oneline

# 图形化显示分支结构
git log --oneline --graph --all

# 只看最近 5 条
git log --oneline -5

# 查看某个文件的修改历史
git log --oneline -- docs/weekly/2026-W26-tanglx.md
```

注意事项：`git log --oneline --graph --all` 是最直观的方式，能看到所有分支的关系。

## 5. 和前后知识的关系

本节是每次工作的核心步骤。前面 04 节讲了 add 和三个区域，这节把 commit 这步讲清楚。下一节讲 push，就是把这些 commit 上传到 GitHub。好的 commit 历史也是 09 节"看懂 git log"的基础。

## 6. 实战任务

在项目目录里完成：

```powershell
# 1. 新建练习分支
git switch main
git pull
git switch -c feat/commit-practice

# 2. 新建一个练习文件
New-Item docs\practice-commit.txt
Add-Content docs\practice-commit.txt "这是一个练习文件"

# 3. 完整提交流程
git status
git add docs\practice-commit.txt
git status
git commit -m "docs: 添加 commit 练习文件"

# 4. 查看提交结果
git log --oneline -3

# 5. 清理（切回 main 删分支）
git switch main
git branch -d feat/commit-practice
```

对照提交输出，说出 commit hash、分支名、message 分别在哪里。

## 7. 检查自己是否学会

1. `git commit -m "xxx"` 里的 `xxx` 叫什么？
2. `feat`、`fix`、`docs` 分别对应什么操作？
3. 为什么说 `update` 是一个坏的 commit message？
4. 如何查看最近 5 条提交记录（简洁格式）？
5. 一次 commit 应该包含多少件事？
6. `git commit --amend` 什么时候绝对不能用？

## 8. 常见误区

1. 误区：commit message 只有自己看，随便写。  
   解释：git log 是团队共享的历史文档，你的 commit message 也是给队友和未来的自己看的。

2. 误区：文件改了就必须马上 commit。  
   解释：你可以改很多文件再一起 commit，也可以每改一个小点就 commit 一次，关键是每个 commit 要有清晰的语义。

3. 误区：commit 了就安全了，不用 push。  
   解释：commit 只保存在本地，电脑坏了或删了仓库，commit 也会消失。push 才能把历史上传到 GitHub 保存。

4. 误区：已经 push 的 commit，用 `--amend` 修改一下 message 没关系。  
   解释：`--amend` 会改变 commit 的 hash，push 过的 commit 被修改后，和远端历史不一致，会导致 push 失败甚至历史混乱。

## 9. 本节总结

commit 不只是保存，是给团队和未来自己写的操作日志。规范格式是 `type: 描述`，常用 type 有 feat/fix/docs/chore 等。提交流程：`git status` 确认状态 → `git add 文件名` → `git commit -m "规范信息"`。一个 commit 只做一件事，message 要让人一眼看懂。
