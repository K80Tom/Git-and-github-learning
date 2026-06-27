# 16 GitHub 协作功能与分支保护

## 1. 这一节要解决什么问题

前面已经学会了 push 和 Pull Request，但真实团队协作不只是“开 PR 等合并”。这一节补齐 GitHub 上常用的协作功能：Issue、Reviewer、Assignee、Label、分支保护、PR 模板、CODEOWNERS 等。

学完这一节，你应该能看懂一个比较规范的 GitHub 团队仓库是如何组织任务、审查代码和保护 main 分支的。

## 2. 基础概念解释

### Issue

通俗解释：Issue 像项目里的任务卡片，可以记录 bug、需求、讨论和待办事项。

技术解释：Issue 是 GitHub 的任务与讨论单元，可以添加标签、负责人、里程碑，并与 PR 关联。

例子：`Issue #12: 补充 GitHub Actions 入门文档`

### Reviewer

通俗解释：Reviewer 是你邀请来检查 PR 的人。

技术解释：Reviewer 会收到审查请求，可以在 PR 中 approve、request changes 或 comment。

例子：你写完文档后，请老师作为 reviewer 查看内容是否准确。

### Assignee

通俗解释：Assignee 表示这件事由谁负责推进。

技术解释：Issue 或 PR 可以分配给一个或多个人，用于明确责任人。

例子：某个 Issue assign 给你，表示你负责完成它。

### Label

通俗解释：Label 是任务标签，用来快速分类。

技术解释：Label 可用于标记 bug、documentation、enhancement、good first issue、help wanted 等。

例子：文档补充任务可以打 `documentation` 标签。

### Branch Protection

通俗解释：分支保护像给 main 分支加一道门，不能随便直接推送，必须走 PR 和审查。

技术解释：Branch protection rules 可以要求 PR review、状态检查通过、禁止 force push、禁止删除分支等。

例子：main 分支要求至少 1 个 reviewer approve 后才能 merge。

### CODEOWNERS

通俗解释：CODEOWNERS 像“谁负责哪些目录”的名单，改到某些文件时自动请求对应负责人 review。

技术解释：`.github/CODEOWNERS` 文件定义路径和负责人的映射，GitHub 会根据改动自动请求 review。

例子：`docs/ @teacher` 表示 docs 目录改动需要 @teacher 审查。

## 3. 为什么要学这个

当项目只有一个人时，GitHub 像远端备份；当项目有多人时，GitHub 就是协作系统。Issue 管任务，PR 管变更，Review 管质量，Branch protection 管底线。理解这些功能，才能从“会提交代码”升级到“会在团队里工作”。

## 4. 关键知识点

### 4.1 推荐的 Issue 驱动工作流

解释：先有任务，再有分支，再有 PR，协作会更清楚。

流程：

```text
1. 创建 Issue：说明要解决什么问题
2. 给 Issue 添加 label、assignee、milestone
3. 从 main 新建功能分支
4. 开发并提交 commit
5. 创建 PR，并在描述中关联 Issue
6. Reviewer 审查
7. 修改直到 approve
8. merge 后关闭 Issue
```

PR 描述中关联 Issue：

```text
Closes #12
Fixes #18
Related to #21
```

注意事项：`Closes #12` 表示 PR 合并后自动关闭 Issue 12；`Related to #12` 只表示相关，不会自动关闭。

### 4.2 一个好的 Issue 应该写什么

解释：Issue 不只是标题，应该让别人知道背景、目标和完成标准。

模板：

```markdown
## 背景
为什么需要做这件事？

## 目标
完成后应该达到什么效果？

## 任务清单
- [ ] 补充文档章节
- [ ] 增加实战任务
- [ ] 更新 README 链接

## 验收标准
- 文档能独立阅读
- 命令示例可以直接复制
- 没有明显重复或过时内容
```

注意事项：Issue 越清楚，PR 越容易被 review。

### 4.3 一个好的 PR 应该写什么

解释：PR 描述要让 reviewer 快速知道你改了什么、为什么改、怎么验证。

模板：

```markdown
## 改动内容
- 新增 14_从零创建仓库与首次推送.md
- 更新 README 的阅读顺序

## 验证方式
- 已检查 Markdown 标题结构
- 已确认命令示例和前文风格一致

## 关联 Issue
Closes #12

## 风险点
- 无
```

注意事项：PR 描述不是写给机器看的，是写给 reviewer 和未来回看历史的人看的。

### 4.4 Review 时看什么

解释：Review 不是只看有没有错别字，而是看改动是否符合目标、是否引入风险。

常见关注点：

```text
1. 改动是否解决了 Issue 里的问题
2. 是否有不相关改动混进来
3. 是否影响已有功能或文档结构
4. 命令示例是否安全
5. 是否需要补测试或补说明
6. 是否有密钥、隐私信息、临时文件被提交
```

Review 结论：

| 结论 | 含义 |
| --- | --- |
| Comment | 留普通意见，不阻塞合并 |
| Approve | 同意合并 |
| Request changes | 要求修改，修改前不应合并 |

注意事项：收到 request changes 不代表失败，只是正常协作的一部分。

### 4.5 分支保护建议

解释：main 分支应该代表稳定状态，所以建议开启保护规则。

常见规则：

```text
Require a pull request before merging
Require approvals
Require status checks to pass before merging
Require branches to be up to date before merging
Do not allow force pushes
Do not allow deletions
```

适合新手团队的配置：

| 配置 | 建议 |
| --- | --- |
| 直接 push main | 禁止 |
| PR review | 至少 1 人 approve |
| force push | 禁止 |
| delete branch | 禁止删除 main |
| CI checks | 有自动检查时要求通过 |

注意事项：分支保护是仓库管理员配置，普通协作者通常只能遵守，不能修改。

### 4.6 PR 和 Issue 模板

解释：模板可以让每个人提交的信息更一致。

PR 模板路径：

```text
.github/pull_request_template.md
```

示例：

```markdown
## 改动内容
- 

## 验证方式
- 

## 关联 Issue
Closes #

## 备注
无
```

Issue 模板路径：

```text
.github/ISSUE_TEMPLATE/bug_report.md
.github/ISSUE_TEMPLATE/feature_request.md
```

注意事项：模板不是越复杂越好。对新手团队，短模板更容易坚持使用。

### 4.7 CODEOWNERS 基础用法

解释：CODEOWNERS 用来自动指定审查人，适合多人负责不同目录的项目。

文件路径：

```text
.github/CODEOWNERS
```

示例：

```text
# 所有文档由文档负责人审查
docs/ @teacher

# 后端代码由后端负责人审查
src/api/ @backend-owner

# 默认负责人
* @project-maintainer
```

注意事项：CODEOWNERS 里的用户名或团队名必须有仓库访问权限，否则不会生效。

## 5. 和前后知识的关系

08 节讲的是“如何创建 PR”，本节讲的是“PR 在团队协作里如何被管理”。17 节会继续讲 PR 上的自动检查，也就是 GitHub Actions 和 CI。

## 6. 实战任务

为一个练习仓库设计协作规则：

```text
1. 写一个 Issue 模板，包含背景、目标、验收标准
2. 写一个 PR 模板，包含改动内容、验证方式、关联 Issue
3. 设计 main 分支保护规则：
   - 禁止直接 push
   - 至少 1 人 review
   - 禁止 force push
4. 写 3 个 label：
   - documentation
   - bug
   - enhancement
```

如果你没有管理员权限，可以只写出方案，不需要实际配置。

## 7. 检查自己是否学会

1. Issue 和 PR 分别解决什么问题？
2. Reviewer 和 Assignee 有什么区别？
3. `Closes #12` 在 PR 描述中有什么作用？
4. 为什么 main 分支要开启保护？
5. CODEOWNERS 的作用是什么？

## 8. 常见误区

1. 误区：PR 只是把代码合并进去。  
   解释：PR 也是讨论、审查、记录决策的地方。

2. 误区：Issue 只用来报 bug。  
   解释：Issue 也可以记录需求、任务、讨论和改进计划。

3. 误区：分支保护会降低效率。  
   解释：保护规则会多一道流程，但能减少 main 被直接破坏的风险。

4. 误区：Reviewer 要帮你改完所有问题。  
   解释：Reviewer 负责指出问题，提交者负责理解并修改。

## 9. 本节总结

GitHub 团队协作的核心是：Issue 管任务，PR 管变更，Review 管质量，Branch protection 保护主线。新手团队至少应该做到：不直接 push main，所有改动走 PR，PR 写清楚改动和验证方式，重要分支禁止 force push。
