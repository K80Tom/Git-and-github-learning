# 02 配置 Git 与 SSH

## 1. 这一节要解决什么问题

这一节让你完成两件事：配置 Git 的用户信息（让每条 commit 知道是谁写的），以及配置 SSH 连接（让你的电脑可以安全地和 GitHub 通信）。

## 2. 基础概念解释

### git config 用户配置

通俗解释：git config 像填写一份工作牌，告诉 Git "我叫什么名字、我的邮箱是什么"，这样每条提交记录都能标注是你写的。

技术解释：git config 命令用来设置 Git 的配置项，`--global` 表示全局生效（对这台电脑上所有仓库有效），不加则只对当前仓库生效。

例子：配置后 `git log` 里会显示 `Author: Firebat <firebat@example.com>`。

### SSH（安全外壳协议）

通俗解释：SSH 像给你和 GitHub 之间建了一条专属的加密隧道，不用每次都输密码，而是靠"认识你的密钥对"来确认身份。

技术解释：SSH（Secure Shell）是一种加密网络协议，用于安全地访问远端服务器。Git 使用 SSH 连接 GitHub 时，通过公私钥对进行身份验证。

例子：配置好 SSH 后，`git push` 时不需要输用户名密码，GitHub 通过你的密钥自动识别你的身份。

### 公钥（Public Key）

通俗解释：公钥是你家门上挂的"锁"，可以公开给任何人，包括 GitHub。别人有了这把锁，但没有你的钥匙，也进不了门。

技术解释：公钥是 SSH 密钥对中可以公开分享的部分，通常是 `.pub` 后缀的文件，添加到 GitHub 账号设置中，让 GitHub 认识你。

例子：文件 `C:\Users\Firebat\.ssh\id_ed25519.pub` 里的内容就是公钥，可以粘贴到 GitHub。

### 私钥（Private Key）

通俗解释：私钥是只有你自己有的"钥匙"，绝对不能发给别人。有了私钥，才能打开公钥那把"锁"，证明你就是你。

技术解释：私钥是 SSH 密钥对中绝对保密的部分，存储在本地，从不上传。身份验证时，本地用私钥生成签名，GitHub 用公钥验证。

例子：文件 `C:\Users\Firebat\.ssh\id_ed25519`（没有 .pub 的那个）就是私钥，只能存在你自己电脑上。

### HTTPS Remote

通俗解释：HTTPS 连接方式就像用账号密码登网站，每次操作都可能需要输入用户名和密码（或 Token）。

技术解释：HTTPS remote 地址格式是 `https://github.com/用户名/仓库名.git`，通过 HTTPS 协议连接，需要凭据认证。

例子：`git remote add origin https://github.com/Firebat/zhongwen-shortdrama-context.git`

### SSH Remote

通俗解释：SSH 连接方式配置好之后一劳永逸，不用每次输密码，靠密钥对自动认证。

技术解释：SSH remote 地址格式是 `git@github.com:用户名/仓库名.git`，通过 SSH 协议连接，使用本地私钥自动认证。

例子：`git remote add origin git@github.com:Firebat/zhongwen-shortdrama-context.git`

## 3. 为什么要学这个

不配置用户信息，你的 commit 没有署名，团队看不出是谁提交的。不配置 SSH，你每次 push/pull 都要输密码（或者在 GitHub 禁用密码后根本连不上）。这两步是开始一切操作之前必须完成的准备工作。

## 4. 关键知识点

### 4.1 配置 Git 用户名和邮箱

解释：这两个信息会出现在每一条 commit 记录里，是团队协作的基本标注。

命令：

```powershell
# 配置全局用户名
git config --global user.name "Firebat"

# 配置全局邮箱（建议和 GitHub 账号邮箱保持一致）
git config --global user.email "firebat@example.com"

# 查看所有配置
git config --list
```

输出结果怎么看：

```text
user.name=Firebat
user.email=firebat@example.com
core.autocrlf=true
...
```

注意事项：`--global` 是全局配置，对这台电脑上所有仓库生效。如果某个仓库需要不同身份，在该仓库目录里用不带 `--global` 的命令单独设置。

### 4.2 生成 SSH Key

解释：SSH 密钥对在本地生成，私钥留在本机，公钥上传到 GitHub。

命令：

```powershell
# 生成 SSH Key（推荐 ed25519 算法，更安全）
ssh-keygen -t ed25519 -C "firebat@example.com"

# 一路回车即可（不需要设置密码短语，除非你需要额外保护）
```

输出结果怎么看：

```text
Generating public/private ed25519 key pair.
Enter file in which to save the key (C:\Users\Firebat/.ssh/id_ed25519):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in C:\Users\Firebat/.ssh/id_ed25519
Your public key has been saved in C:\Users\Firebat/.ssh/id_ed25519.pub
```

注意事项：看到两个文件：`id_ed25519`（私钥）和 `id_ed25519.pub`（公钥）。私钥绝对不能发给任何人。

### 4.3 查看公钥内容

解释：需要把公钥粘贴到 GitHub，所以要先看到它的内容。

命令：

```powershell
# 查看公钥内容
Get-Content C:\Users\Firebat\.ssh\id_ed25519.pub

# 或者用 cat（PowerShell 里也可以）
cat ~/.ssh/id_ed25519.pub
```

输出结果怎么看：

```text
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx firebat@example.com
```

全部复制这一行，包括开头的 `ssh-ed25519` 和结尾的邮箱。

注意事项：复制时不要多复制空格或换行，否则 GitHub 会提示"无效的 Key"。

### 4.4 把公钥添加到 GitHub

解释：GitHub 需要知道你的公钥，才能在你 push/pull 时识别你的身份。

步骤：

```text
1. 打开 GitHub → 右上角头像 → Settings
2. 左侧菜单 → SSH and GPG keys
3. 点击 New SSH key
4. Title 填一个好认的名字，比如 "Firebat-Windows-2026"
5. Key type 选 Authentication Key
6. Key 里粘贴刚才复制的公钥内容
7. 点击 Add SSH key
```

注意事项：每台电脑生成一个独立的 SSH Key 并分别添加到 GitHub，不要在多台电脑之间复制私钥文件。

### 4.5 测试 SSH 连接

解释：添加公钥后，用这个命令测试是否连接成功。

命令：

```powershell
ssh -T git@github.com
```

输出结果怎么看：

```text
# 成功的输出
Hi Firebat! You've successfully authenticated, but GitHub does not provide shell access.

# 失败的输出
Permission denied (publickey).
```

成功就代表你的 SSH 配置完成了。失败参考 `10_常见报错` 章节。

注意事项：第一次连接时可能会提示"是否信任这个主机"，输入 `yes` 回车即可。

### 4.6 HTTPS 和 SSH 的选择

解释：两种方式都能用，各有优缺点，初学者选其中一种即可。

| 对比项 | SSH | HTTPS |
| --- | --- | --- |
| 身份验证 | 密钥对，一次配置永久有效 | 每次输密码或 Token |
| 配置复杂度 | 稍复杂（需生成 Key） | 简单（直接 clone） |
| 日常使用 | 更顺畅，不用每次输密码 | 2021 年后 GitHub 禁用密码，需要 Personal Access Token |
| 推荐程度 | 推荐长期使用 | 适合临时使用或不方便配置 SSH 时 |

注意事项：GitHub 从 2021 年 8 月起不再支持用账号密码做 HTTPS 认证，需要生成 Personal Access Token 代替密码。SSH 方式没有这个问题。

## 5. 和前后知识的关系

本节是后面所有 push/pull 操作的前提。没有完成 SSH 配置，第一次 push 就会报 `Permission denied`。下一节克隆仓库时，选择 SSH 或 HTTPS 地址格式也取决于你用哪种方式。

## 6. 实战任务

在 PowerShell 里完成：

1. 配置用户名和邮箱，运行 `git config --list` 确认配置生效。
2. 生成 SSH Key，找到 `id_ed25519.pub` 文件，复制内容。
3. 把公钥添加到 GitHub，运行 `ssh -T git@github.com` 测试连接。
4. 把两种 remote 地址格式抄下来对比：`https://github.com/xxx/xxx.git` 和 `git@github.com:xxx/xxx.git`。

## 7. 检查自己是否学会

1. 为什么每条 commit 要有用户名和邮箱？
2. 公钥和私钥分别存在哪里？
3. 私钥为什么绝对不能发给别人？
4. `ssh -T git@github.com` 成功的输出是什么？
5. SSH remote 地址格式和 HTTPS remote 地址格式有什么区别？
6. GitHub 为什么从 2021 年后不再支持密码认证？

## 8. 常见误区

1. 误区：公钥和私钥都要上传到 GitHub。  
   解释：只有公钥上传到 GitHub，私钥永远留在本地，不能外发。

2. 误区：配置了全局 user.name 就能 push 了。  
   解释：user.name 只是 commit 的署名，push 需要 SSH Key 或 HTTPS 认证，两者是不同的配置。

3. 误区：SSH Key 生成一次就够了。  
   解释：每台新电脑都需要生成一个新的 SSH Key，并分别添加到 GitHub。不要在多台电脑间复制私钥。

4. 误区：添加公钥后不用测试。  
   解释：一定要用 `ssh -T git@github.com` 测试，否则出了问题不知道是哪步配置错了。

## 9. 本节总结

Git 配置分两层：一是用户信息（user.name、user.email），让每条 commit 有署名；二是 SSH Key，让你的电脑和 GitHub 之间可以无密码安全通信。公钥给 GitHub，私钥只留本地。配置完成后用 `ssh -T git@github.com` 测试，看到"successfully authenticated"即可。
