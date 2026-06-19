<div align="center">

# 🪴 obsidian-free-sync

**免费、私密、跨 Mac 的 Obsidian 笔记同步方案**
不依赖任何订阅，不被任何平台绑架。

![License](https://img.shields.io/badge/license-MIT-green)
![Platform](https://img.shields.io/badge/platform-macOS-lightgrey)
![Obsidian](https://img.shields.io/badge/Obsidian-1.x-7c3aed)
![Cost](https://img.shields.io/badge/cost-%240-brightgreen)

</div>

---

## ✨ 一句话简介

把你的 Obsidian vault 变成一个 GitHub 私有仓库，每 10 分钟自动同步。
**没有订阅。没有容量焦虑。换 Mac 两行命令搞定。**

---

## 🎯 双轨设计

| 你是谁 | 怎么用 |
|--------|--------|
| 🧑‍💻 **普通用户** | 跟着下面的 README 一步步做，**不需要任何 AI 工具** |
| 🤖 **AI agent 用户** | 让支持 Skills 协议的 AI 读 [`skill/SKILL.md`](skill/SKILL.md) 自动跑完全部步骤 |

> 两条路径产出完全相同。没装 AI 工具？直接忽略 `skill/` 目录即可。

---

## 🤔 为什么需要它？

你也许遇到过这些问题：

- 💸 **官方 Sync 太贵**：标准版 1GB / 5MB 单文件、Plus 版 10GB / 200MB，按月订阅，不能买断
- 🌍 **国内同步龟速**：官方服务器在海外，写一段笔记等半天
- 🍎 **iCloud 跨 Apple ID 直接失效**：工作 Mac 用公司 ID，个人 Mac 用自己 ID，两边笔记彼此隔离
- 🔒 **数据放别人家不放心**：容量满即停同步，账号封了笔记就没了

这套方案的解法很简单：**笔记放你自己的 GitHub 私有仓库里，谁都拿不走。**

---

## 🧠 它是怎么工作的？

```
   Mac A（工作）                    Mac B（个人）
        │                                 │
        ▼                                 ▼
  obsidian-git 插件               obsidian-git 插件
        │                                 │
        └────────── GitHub 私有仓库 ───────┘
                  （只有你能看 / 只有你能改）
```

**核心：每台 Mac 装一个 obsidian-git 插件，每 10 分钟自动 commit + pull。没有中间商。**

---

## 📦 容量限制？

| 项目 | 限制 |
|------|------|
| GitHub 私有仓库总容量 | **几乎无限**（推荐保持 < 5GB 性能最佳） |
| 单文件大小 | 100 MB（你的 `.md` 永远不会超） |
| 价格 | **$0** |

---

## 🚀 开始配置

### 🖥️ 第一台 Mac

<details open>
<summary><b>展开 7 个步骤</b></summary>

#### 1️⃣ 安装 Git

macOS 自带方式可能被公司网络屏蔽，直接下载安装包更稳：

👉 [git-scm.com/download/mac](https://git-scm.com/download/mac)

```bash
git --version    # 验证安装
```

#### 2️⃣ 配置 Git 身份

```bash
git config --global user.name  "你的名字"
git config --global user.email "你的邮箱"
git config --global core.editor "true"   # 防止弹出 vim
```

#### 3️⃣ 生成 SSH Key 并添加到 GitHub

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

#### 4️⃣ 在 GitHub 新建私有仓库

新建 **Private** 仓库，**不勾选任何初始化选项**，记下 SSH 地址：
`git@github.com:用户名/仓库名.git`

#### 5️⃣ 推送 vault

```bash
cd /path/to/your/vault
git init
git add .
git commit -m "init"
git remote add origin git@github.com:用户名/仓库名.git
git branch -M main
git push -u origin main
```

#### 6️⃣ 防止 workspace 冲突

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

#### 7️⃣ 安装 obsidian-git 插件

Obsidian → 设置 → 第三方插件 → 关闭安全模式 → 搜索 **obsidian-git**（作者 Vinzent）→ 安装并启用。

推荐配置：

| 配置项 | 推荐值 |
|--------|--------|
| Auto commit-and-sync interval | `10`（分钟） |
| Auto pull interval | `10`（分钟） |
| Commit message | `vault backup: {{date}}` |
| Pull on startup | ✅ 开启 |

**完成。第一台 Mac 就绪 🎉**

</details>

---

### 💻 第二台 Mac

重复 1-3 步（装 Git、配身份、**生成新的 SSH Key** 并加到 GitHub），然后：

```bash
git clone git@github.com:用户名/仓库名.git ~/path/to/vault
```

用 Obsidian 打开这个目录，装 obsidian-git 插件，配置同上。
**两台 Mac 共享同一个 vault，每 10 分钟自动同步。**

---

## 🤖 AI 一键配置（可选）

如果你在用支持 Skills 协议的 AI agent（Claude Code 等），可以让 AI 自动跑完上面 7 步：

```bash
git clone git@github.com:itschriszhao/obsidian-free-sync.git ~/obsidian-free-sync
ln -sfn ~/obsidian-free-sync/skill ~/.your-agent/skills/obsidian-free-sync
```

然后对 AI 说一句"帮我配置 obsidian 同步"，剩下的它会自己搞定。详见 [`skill/SKILL.md`](skill/SKILL.md)。

> 不用 AI agent 的同学请直接忽略本节。

---

## 🔄 换设备 / 离职清理

两步清得干干净净：

```bash
# 1. GitHub → Settings → SSH and GPG keys → 删掉这台设备的 Key
# 2. 删掉本地私钥
rm ~/.ssh/id_ed25519 ~/.ssh/id_ed25519.pub
```

这台电脑和你 GitHub 账号彻底脱钩，其他设备同步不受影响。

---

## 🆘 常见问题

详见 [references/troubleshooting.md](references/troubleshooting.md)。

包含：
- 推送失败（non-fast-forward）
- Permission denied (publickey)
- 公司网络屏蔽 GitHub / Apple
- SSH 22 端口被封（走 443）
- workspace.json 冲突
- ……

---

## 🌱 写在最后

好用的工具应该为效率服务，不应该有诸多限制——它本就该丝滑流畅。

这套方案完全免费、完全开源、数据完全在你手里。觉得有用就 ⭐ 一下，遇到问题欢迎提 Issue。

<div align="center">

—— Made with ☕ by [@itschriszhao](https://github.com/itschriszhao) · MIT License

</div>
