# Ubuntu 20.04 上传文件到 GitHub

目前 GitHub 不再支持使用账户密码直接推送代码，因此**配置 SSH 密钥**是最推荐且一劳永逸的方法。

------

## 第一阶段：环境准备

首先，确保 Ubuntu 已经安装了 git。打开终端（Terminal），依次输入以下命令：

1. **更新软件源并安装 Git：**

   Bash

   ```
   sudo apt update
   sudo apt install git
   ```

2. 配置身份信息：

   （Git 需要知道是谁在提交代码，请将引号内的内容替换为你自己的 GitHub 用户名和邮箱）

   Bash

   ```
   git config --global user.name "你的GitHub用户名"
   git config --global user.email "你的GitHub注册邮箱"
   ```

------

## 第二阶段：配置 SSH 密钥（关键步骤）

这是为了让 GitHub 信任你的 Ubuntu 电脑，避免每次上传都报错或要求输入 Token。

1. 生成 SSH 密钥：

   在终端输入以下命令（一路按回车键即可，不需要设置密码）：

   Bash

   ```
   ssh-keygen -t ed25519 -C "你的GitHub注册邮箱"
   ```

   *(注：如果你的系统较老不支持 ed25519，可以使用 `ssh-keygen -t rsa -b 4096 -C "邮箱"`)*

2. 查看并复制公钥：

   输入以下命令查看生成的密钥内容：

   Bash

   ```
   cat ~/.ssh/id_ed25519.pub
   ```

   *(如果是用 rsa 生成的，则是 `cat ~/.ssh/id_rsa.pub`)*

   **操作：** 选中并复制终端里显示出来的以 `ssh-` 开头的一长串字符。

3. **添加到 GitHub 网站：**

   - 登录 GitHub，点击右上角头像 -> **Settings**。
   - 在左侧菜单找到 **SSH and GPG keys**。
   - 点击绿色的 **New SSH key** 按钮。
   - **Title** 随便填（例如：`Ubuntu 20.04`）。
   - **Key** 粘贴刚才复制的那串字符。
   - 点击 **Add SSH key**。

4. 测试连接：

   回到终端，输入：

   Bash

   ```
   ssh -T git@github.com
   ```

   如果你看到 `Hi <用户名>! You've successfully authenticated...`，说明配置成功！

------

## 第三阶段：上传文件

现在我们开始正式上传文件。假设你的文件在一个叫 `my-project` 的文件夹里。

### 1. 在 GitHub 网站上创建一个新仓库

- 点击 GitHub 页面右上角的 **+** 号 -> **New repository**。
- 输入 **Repository name**（仓库名）。
- 保持 Public 或 Private。
- **不要**勾选 "Initialize this repository with a README"（为了避免冲突，我们创建空仓库）。
- 点击 **Create repository**。
- **复制** 页面上显示的 SSH 地址（格式通常是 `git@github.com:用户名/仓库名.git`）。

### 2. 在 Ubuntu 终端操作

进入你的文件夹：

Bash

```
cd /path/to/my-project  # 将路径替换为你实际的文件夹路径
```

依次执行以下命令：

Bash

```
# 1. 初始化 Git 仓库
git init

# 2. 将所有文件添加到暂存区
git add .

# 3. 提交文件到本地仓库 (引号内写备注信息)
git commit -m "第一次提交代码"

# 4. 将刚才的分支重命名为 main (GitHub 现在默认主分支叫 main)
git branch -M main

# 5. 关联远程仓库 (将下面的地址替换为你刚才在 GitHub 上复制的 SSH 地址)
git remote add origin git@github.com:你的用户名/你的仓库名.git

# 6. 推送到 GitHub
git push -u origin main
```

------

## 常见问题排查

- 报错 Permission denied (publickey)：

  这通常意味着第二阶段的 SSH 没配置好，或者你复制的是 HTTPS 地址而不是 SSH 地址。请确保 git remote add 使用的是 git@github.com... 开头的地址。

- 报错 fatal: remote origin already exists：

  说明你之前关联过别的仓库。你可以输入 git remote remove origin 删除旧关联，然后重新执行第 5 步。

------

如果在执行 `git push` 时遇到了这种报错信息，

![image-20251126125036727](D:/Records/images/image-20251126125036727.png)



## 错误原因

**“non-fast-forward”** 的意思很简单：**GitHub 仓库里有一些文件（通常是你创建仓库时自动生成的 README.md 或 License 文件），但是 Ubuntu 本地电脑里没有。**

Git 为了防止意外覆盖掉云端已有的文件，默认阻止了这次推送。

---

## 解决方案

### 方案一：强制覆盖

如果**不在乎**GitHub 仓库里现有的那个文件（比如只是个空的 README），只想把 Ubuntu 里的代码立刻传上去，可以用**强制推送**。

在终端输入以下命令：

Bash

```
git push -f origin main
```

> **注意：** `-f` 代表 force（强制）。这会把 GitHub 上原本的内容直接覆盖掉，变成和你本地现在的样子。这对于第一次上传项目通常是安全的。

------

### 方案二：先拉取再推送（保留双方文件）

如果想**保留** GitHub 上的文件（比如那里有个你写好的 README），你需要先把它“拉”下来，合并到你的本地，然后再推上去。

在终端依次输入：

Bash

```
# 1. 允许合并不相关的历史记录并拉取代码
git pull origin main --allow-unrelated-histories

# 2. 如果弹出编辑器界面（通常是 nano 或 vim），直接按 Ctrl+X 退出即可（默认会保存合并信息）

# 3. 再次尝试推送
git push -u origin main
```

------

