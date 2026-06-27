# 17 GitHub Actions 与 PR 检查

## 1. 这一节要解决什么问题

很多团队的 PR 页面上会出现绿色对勾或红色叉号，提示 checks passed 或 checks failed。新手常常不知道这是什么，也不知道失败后该看哪里。

这一节帮你理解 GitHub Actions、CI、workflow、job、check 的基本关系，以及 PR 检查失败时如何定位问题。

## 2. 基础概念解释

### GitHub Actions

通俗解释：GitHub Actions 像 GitHub 里的自动助手，当你 push 或开 PR 时，它会自动运行一些检查任务。

技术解释：GitHub Actions 是 GitHub 提供的自动化平台，可以通过 workflow 文件定义触发条件和执行步骤。

例子：每次 PR 都自动运行测试、检查格式、构建项目。

### CI

通俗解释：CI 像每次交作业前自动跑一遍检查，确认代码至少能安装、能测试、能构建。

技术解释：CI（Continuous Integration，持续集成）是在代码变更时自动执行测试、构建和静态检查的流程。

例子：PR 上显示 `tests / build` 通过，说明自动测试和构建成功。

### Workflow

通俗解释：workflow 是一份自动化说明书，告诉 GitHub 什么时候做什么。

技术解释：workflow 是 `.github/workflows/*.yml` 文件，定义触发事件、运行环境、job 和 steps。

例子：`.github/workflows/ci.yml`

### Job

通俗解释：job 是 workflow 里的一个任务组。

技术解释：job 由多个 step 组成，可以在指定操作系统环境中运行。

例子：一个 workflow 里可以有 `test` job 和 `lint` job。

### Check

通俗解释：check 是 PR 页面上看到的自动检查结果。

技术解释：check 通常来自 GitHub Actions job，也可能来自第三方服务。它会显示 success、failure、pending 等状态。

例子：`ci / test (pull_request)` 显示失败，需要点进去看日志。

## 3. 为什么要学这个

团队项目常常要求“所有 checks 通过后才能 merge”。你不需要一开始就精通 Actions，但至少要知道：自动检查是谁触发的、失败日志在哪里、哪些失败是代码问题、哪些失败是环境或配置问题。

## 4. 关键知识点

### 4.1 PR 检查的基本流程

解释：当你 push 分支或更新 PR 时，GitHub 会根据 workflow 自动运行检查。

流程：

```text
1. 你 push 分支
2. PR 自动更新
3. GitHub 发现匹配的 workflow
4. Actions 开始运行 job
5. PR 页面显示 checks pending
6. job 成功 → 绿色对勾
7. job 失败 → 红色叉号
8. 如果分支保护要求 checks 通过，失败时不能 merge
```

注意事项：checks pending 时不要急着重复 push，先等它跑完。

### 4.2 一个最小 workflow 长什么样

解释：workflow 文件通常放在 `.github/workflows/` 目录下。

示例：

```yaml
name: CI

on:
  pull_request:
  push:
    branches:
      - main

jobs:
  check:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Show files
        run: ls
```

含义：

| 字段 | 含义 |
| --- | --- |
| `name` | workflow 名称 |
| `on` | 什么时候触发 |
| `jobs` | 要执行哪些任务 |
| `runs-on` | 在什么系统环境运行 |
| `steps` | 具体执行步骤 |

注意事项：YAML 对缩进非常敏感，缩进错误会导致 workflow 无法运行。

### 4.3 Python 项目的简单 CI 示例

解释：如果项目是 Python，可以自动安装依赖并运行测试。

示例：

```yaml
name: Python CI

on:
  pull_request:
  push:
    branches:
      - main

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: "3.11"

      - name: Install dependencies
        run: pip install -r requirements.txt

      - name: Run tests
        run: pytest
```

注意事项：如果仓库没有 `requirements.txt` 或测试命令不同，需要按项目实际情况调整。

### 4.4 Node.js 项目的简单 CI 示例

解释：如果项目是前端或 Node.js，可以自动安装依赖并运行测试或构建。

示例：

```yaml
name: Node CI

on:
  pull_request:
  push:
    branches:
      - main

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Set up Node.js
        uses: actions/setup-node@v4
        with:
          node-version: "20"

      - name: Install dependencies
        run: npm ci

      - name: Run tests
        run: npm test

      - name: Build
        run: npm run build
```

注意事项：`npm ci` 需要有 `package-lock.json`，否则可能失败。

### 4.5 PR 检查失败怎么看

解释：看到红色叉号不要慌，先点进去看失败 job 的日志。

步骤：

```text
1. 打开 PR 页面
2. 找到红色失败的 check
3. 点击 Details
4. 进入 Actions 日志页面
5. 找到失败的 job
6. 展开红色失败的 step
7. 复制关键错误信息
8. 回到本地修复后重新 commit + push
```

常见失败类型：

| 错误 | 可能原因 | 处理方向 |
| --- | --- | --- |
| dependency install failed | 依赖文件缺失或版本冲突 | 检查 lock 文件和依赖声明 |
| test failed | 测试没通过 | 在本地运行同样测试 |
| build failed | 构建错误 | 看构建日志定位文件 |
| YAML syntax error | workflow 格式错误 | 检查缩进和字段 |
| permission denied | 权限不足或 token 配置问题 | 检查仓库权限和 secrets |

注意事项：先看第一条真正失败的错误，不要只看最后一行。

### 4.6 本地复现 CI 失败

解释：最好的修复方式是在本地运行同样命令，确认通过后再 push。

Python 示例：

```powershell
pip install -r requirements.txt
pytest
```

Node.js 示例：

```powershell
npm ci
npm test
npm run build
```

注意事项：CI 通常运行在 Linux 环境，本地 Windows 能过不代表 CI 一定过。路径大小写、换行符、环境变量都可能影响结果。

### 4.7 Secrets 和敏感信息

解释：CI 有时需要 API Key、部署密钥等，不能直接写进代码或 workflow。

正确方式：

```text
GitHub 仓库 Settings
→ Secrets and variables
→ Actions
→ New repository secret
```

workflow 中使用：

```yaml
env:
  OPENAI_API_KEY: ${{ secrets.OPENAI_API_KEY }}
```

注意事项：不要在日志里打印 secret，不要把 `.env` 提交到仓库。

### 4.8 什么时候需要 rerun

解释：有些失败是临时网络问题，有些是代码问题。不要盲目 rerun。

适合 rerun：

```text
依赖下载临时失败
第三方服务短暂不可用
runner 初始化失败
```

不适合只 rerun：

```text
测试断言失败
语法错误
构建命令不存在
依赖文件缺失
```

注意事项：同一个错误连续失败，通常要改代码或配置，而不是一直重跑。

## 5. 和前后知识的关系

16 节讲分支保护时提到 required status checks，本节解释这些 checks 从哪里来、失败怎么看。08 节创建 PR 后，如果项目配置了 CI，就会在 PR 页面看到本节内容。

## 6. 实战任务

不用真的修改项目，也可以完成一次阅读练习：

```text
1. 打开任意一个有 Actions 的 GitHub 仓库
2. 进入 Pull requests
3. 找一个有 checks 的 PR
4. 点击 Details 查看 Actions 日志
5. 找出 workflow 名称、job 名称、失败或成功的 step
6. 用自己的话解释这个 check 在检查什么
```

如果你有练习仓库，可以添加一个只运行 `ls` 的最小 workflow。

## 7. 检查自己是否学会

1. GitHub Actions 和 CI 是什么关系？
2. workflow 文件通常放在哪里？
3. PR 页面上的 check failed 应该点哪里看日志？
4. 为什么 CI 本地能过，GitHub 上仍可能失败？
5. secret 为什么不能直接写在 workflow 里？

## 8. 常见误区

1. 误区：红色叉号就是 GitHub 坏了。  
   解释：大多数时候是测试、构建、依赖或配置失败。

2. 误区：checks 失败也可以随便 merge。  
   解释：如果分支保护要求 checks 通过，失败时不能 merge；即使能 merge，也应该先理解风险。

3. 误区：CI 失败只要 rerun 就行。  
   解释：代码或配置错误需要修复，rerun 不会改变结果。

4. 误区：为了方便，把 API Key 写进 workflow。  
   解释：密钥必须放在 GitHub Secrets，不能明文提交。

## 9. 本节总结

GitHub Actions 是 GitHub 的自动化系统，CI 是常见用途之一。PR 上的 checks 通常来自 workflow 的 job。检查失败时，先点 Details 看日志，找到失败 step 和第一条关键错误，再本地复现并修复。涉及密钥时使用 GitHub Secrets，不要把敏感信息写进代码。
