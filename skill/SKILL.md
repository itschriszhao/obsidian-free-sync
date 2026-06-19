---
name: obsidian-free-sync-setup
description: 自动帮用户在 Mac 上完成 Obsidian + Git + GitHub 多设备同步的全流程配置，无需用户手敲命令。当用户说"帮我配置 Obsidian 同步"、"设置 obsidian-git"、"配置 Obsidian 多设备同步"、"帮我把 Obsidian 同步到 GitHub"、"第二台 Mac 接入 Obsidian 仓库"时触发。
---

# Obsidian Free Sync 自动配置

全程代替用户执行命令，逐步完成 Obsidian + Git + GitHub 多设备同步配置。遇到错误自动诊断修复，不让用户手敲命令。

## 执行原则

- **主动执行**：每一步直接运行命令，把输出截图或结果发给用户确认，不让用户自己操作终端
- **错误自愈**：遇到常见错误（见 references/error-handling.md）自动修复，无需用户干预
- **逐步确认**：每个关键节点（SSH Key 添加、vault 路径确认、仓库地址确认）等待用户响应后再继续

## 配置流程

### Step 1：检测 Git

```bash
git --version
```

- 成功 → 直接进入 Step 2
- 失败（command not found）→ 告知用户去 https://git-scm.com/download/mac 下载 `.dmg` 安装包，等用户安装完后再验证

### Step 2：配置 Git 身份

询问用户名和邮箱，然后执行：

```bash
git config --global user.name "用户填写的名字"
git config --global user.email "用户填写的邮箱"
git config --global core.editor "true"
```

### Step 3：SSH Key 配置

检测是否已有 SSH Key：

```bash
ls ~/.ssh/id_ed25519.pub 2>/dev/null && echo "EXISTS" || echo "NOT_FOUND"
```

- 已存在 → 询问是否复用，或重新生成
- 不存在 → 生成：

```bash
ssh-keygen -t ed25519 -C "用户邮箱"
# 自动传入空密码（-N ""）避免交互
ssh-keygen -t ed25519 -C "用户邮箱" -f ~/.ssh/id_ed25519 -N ""
```

输出公钥让用户复制：

```bash
cat ~/.ssh/id_ed25519.pub
```

引导用户添加到 GitHub：https://github.com/settings/ssh/new
- Key type 选 **Authentication Key**
- 若 GitHub 页面打不开 → 提示用手机操作

验证连接：

```bash
ssh -T git@github.com
```

成功标志：`Hi 用户名! You've successfully authenticated`
失败处理：见 references/error-handling.md

### Step 4：确认 vault 路径和 GitHub 仓库

询问用户：
1. Obsidian vault 在本地的完整路径（如 `/Users/xxx/Documents/MyVault`）
2. GitHub 私有仓库的 SSH 地址（如 `git@github.com:用户名/仓库名.git`）

如用户还没建仓库，引导其去 GitHub 新建 **Private** 仓库，不勾选任何初始化选项。

### Step 5：初始化并推送

```bash
cd <vault路径>
git init
git add .
git commit -m "init"
git remote add origin <仓库SSH地址>
git branch -M main
git push -u origin main
```

遇到错误自动按 references/error-handling.md 修复。

### Step 6：处理 workspace 文件冲突

```bash
cd <vault路径>
git rm --cached .obsidian/workspace.json 2>/dev/null || true
git rm --cached .obsidian/workspace-mobile.json 2>/dev/null || true
```

创建/追加 `.gitignore`：

```bash
cat >> .gitignore << 'EOF'
.obsidian/workspace.json
.obsidian/workspace-mobile.json
.trash/
EOF
```

提交：

```bash
git add .gitignore
git commit -m "add gitignore and stop tracking workspace files"
git push
```

### Step 7：obsidian-git 插件安装提示

告知用户：

1. Obsidian → 设置 → 第三方插件 → 关闭安全模式
2. 搜索 `obsidian-git`（作者 Vinzent）→ 安装 → 启用
3. 插件配置：
   - Auto commit-and-sync interval：`10`
   - Auto pull interval：`10`
   - Commit message：`vault backup: {{date}}`
   - Pull on startup：开启

配置完成，告知用户同步已就绪，并提供在线查看地址：`https://github.com/用户名/仓库名`

## 第二台 Mac 接入模式

当用户说"第二台电脑"、"另一台 Mac 接入"时，跳过 Step 4-6 中的 init/push 部分，改为：

```bash
git clone <仓库SSH地址> <vault路径>
```

然后打开 Obsidian 选该目录，继续 Step 7。

## 参考资料

- **错误处理**：见 references/error-handling.md
