# 🛠️ 手动配置指南

跟着 7 个步骤做，**不需要任何 AI 工具**，普通用户首选。

> 想让 AI 自动跑完这些步骤？见 [`skill/SKILL.md`](skill/SKILL.md)。
> 遇到错误？查 [`docs/troubleshooting.md`](docs/troubleshooting.md)。

---

## 🖥️ 第一台 Mac

### 1️⃣ 安装 Git

大多数 Mac 自带 `git`，先验证：

```bash
git --version
```

若提示 `command not found` 或版本过旧，直接下载官方安装包：

👉 [git-scm.com/download/mac](https://git-scm.com/download/mac)

### 2️⃣ 配置 Git 身份

```bash
git config --global user.name  "你的名字"
git config --global user.email "你的邮箱"
git config --global core.editor "true"   # 防止弹出 vim
```

### 3️⃣ 生成 SSH Key 并添加到 GitHub

```bash
ssh-keygen -t ed25519 -C "你的邮箱"
# 提示输入密码时直接回车跳过

cat ~/.ssh/id_ed25519.pub   # 复制输出
```

打开 [github.com/settings/ssh/new](https://github.com/settings/ssh/new)，粘贴公钥，Key type 选 **Authentication Key**。

```bash
ssh -T git@github.com
# 看到 "Hi 用户名! You've successfully authenticated..." 即成功
```

### 4️⃣ 在 GitHub 新建私有仓库

新建 **Private** 仓库，**不勾选任何初始化选项**，记下 SSH 地址：
`git@github.com:用户名/仓库名.git`

### 5️⃣ 推送 vault

```bash
cd /path/to/your/vault
git init
git add .
git commit -m "init"
git remote add origin git@github.com:用户名/仓库名.git
git branch -M main
git push -u origin main
```

### 6️⃣ 防止 workspace 冲突

`workspace.json` 是界面状态，两台 Mac 各改各的会冲突，把它从追踪移除：

```bash
git rm --cached .obsidian/workspace.json
git rm --cached .obsidian/workspace-mobile.json 2>/dev/null || true
git commit -m "stop tracking workspace files"
git push
```

确保 `.gitignore` 包含：

```
.obsidian/workspace.json
.obsidian/workspace-mobile.json
.trash/
```

### 7️⃣ 安装 obsidian-git 插件

Obsidian → 设置 → 第三方插件 → 关闭安全模式 → 搜索 **obsidian-git**（作者 Vinzent）→ 安装并启用。

推荐配置：

| 配置项 | 推荐值 |
|--------|--------|
| Auto commit-and-sync interval | `10`（分钟） |
| Auto pull interval | `10`（分钟） |
| Commit message | `vault backup: {{date}}` |
| Pull on startup | ✅ 开启 |

> 完整配置参考：[`docs/plugin-config.md`](docs/plugin-config.md)

**完成。第一台 Mac 就绪 🎉**

---

## 💻 第二台 Mac

重复第 1-3 步（装 Git、配身份、**生成新的 SSH Key** 并加到 GitHub），然后：

```bash
git clone git@github.com:用户名/仓库名.git ~/path/to/vault
```

用 Obsidian 打开这个目录，装 obsidian-git 插件，配置同上。
**两台 Mac 共享同一个 vault，每 10 分钟自动同步。**

---

## 🔄 换电脑 / 设备清理

两步清得干干净净，互不影响其他设备：

```bash
# 1. GitHub → Settings → SSH and GPG keys → 删掉这台设备的 Key
# 2. 删掉本地私钥
rm ~/.ssh/id_ed25519 ~/.ssh/id_ed25519.pub
```

详见 [`docs/troubleshooting.md` 第 6 节](docs/troubleshooting.md#6-换电脑--设备清理)。
