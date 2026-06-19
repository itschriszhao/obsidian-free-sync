# obsidian-free-sync

> 免费、私密、跨 Mac 的 Obsidian 笔记同步方案 —— 不依赖任何订阅服务

![License](https://img.shields.io/badge/license-MIT-green) ![Platform](https://img.shields.io/badge/platform-macOS-lightgrey) ![Obsidian](https://img.shields.io/badge/Obsidian-1.x-7c3aed) ![Comate Skill](https://img.shields.io/badge/Comate-Skill-blue)

## 目录

- [为什么做这个？](#为什么做这个)
- [它是怎么工作的？](#它是怎么工作的)
- [存储限制？](#存储限制)
- [开始配置](#开始配置)
  - [第一台 Mac](#第一台-mac)
  - [第二台 Mac](#第二台-mac)
- [换设备](#换设备)
- [一键自动配置（Comate Skill）](#一键自动配置comate-skill)
- [常见问题](#常见问题)

---

## 为什么做这个？

我需要的东西很简单：

- 多台设备写同一份 obsidian 笔记，随时切换
- 不依赖订阅服务和限制
- 如需换电脑，两行命令清得干干净净

目前已有的同步方案：

Obsidian 官方有付费的同步，但有诸多限制。标准版 1G 空间、仅 1 个库、单文件上限 5MB；Plus 版 10GB、最多 10 库、单文件 200MB。无国内服务器同步慢，多人协作成本高，按月订阅不能买断，数据存官方服务器，容量满即停同步。

而 icloud 同步方案，工作 Mac 登的是公司 Apple ID，个人 Mac 登的是自己的 Apple ID，iCloud 方案直接失效。两台电脑写的东西彼此隔离，想看上午在公司写的东西，晚上回家打开是空的。

所以我用 Git + GitHub 私有仓库做了这套方案。

无需订阅，免费，容量几乎无限制，随时可以换电脑，数据完全掌控在自己手里。

---

## 它是怎么工作的？

核心逻辑：把 Obsidian vault 变成一个 Git 仓库，推到你自己的 GitHub 私有仓库，每台 Mac 装一个 obsidian-git 插件，每 10 分钟自动同步一次。

```
Mac A（工作电脑）                    Mac B（个人电脑）
      │                                     │
      ▼                                     ▼
obsidian-git 插件              obsidian-git 插件
      │                                     │
      └──────────── GitHub 私有仓库 ─────────┘
                    （你自己的，只有你能看）
```

没有中间商。没有额外服务。你的笔记在你自己的 GitHub 仓库里，版本历史完整，随时可以回退。

---

## 存储限制？

几乎没有。

GitHub 私有仓库没有硬性容量上限。

唯一需要注意：单文件不超过 100 MB。你的 `.md` 文件永远不会触发这个限制。

---

## 开始配置

### 第一台 Mac

**1. 安装 Git**

macOS 自带的安装方式可能被公司网络屏蔽，直接下载安装包更稳：

👉 [git-scm.com/download/mac](https://git-scm.com/download/mac)

安装完验证：

```bash
git --version
```

**2. 配置 Git 身份**

```bash
git config --global user.name "你的名字"
git config --global user.email "你的邮箱"
git config --global core.editor "true"   # 防止弹出 vim 编辑器
```

**3. 生成 SSH Key，添加到 GitHub**

```bash
ssh-keygen -t ed25519 -C "你的邮箱"
# 提示输入密码时直接回车跳过

cat ~/.ssh/id_ed25519.pub   # 复制输出内容
```

打开 [github.com/settings/ssh/new](https://github.com/settings/ssh/new)，粘贴公钥，Key type 选 **Authentication Key**。

验证连接：

```bash
ssh -T git@github.com
# 成功：Hi 用户名! You've successfully authenticated...
```

**4. 在 GitHub 新建私有仓库**

新建一个 **Private** 仓库，**不勾选任何初始化选项**，拿到 SSH 地址：`git@github.com:用户名/仓库名.git`

**5. 推送 vault**

```bash
cd /path/to/your/vault
git init
git add .
git commit -m "init"
git remote add origin git@github.com:用户名/仓库名.git
git branch -M main
git push -u origin main
```

**6. 防止 workspace 冲突**

`workspace.json` 记录的是界面状态，不是笔记内容。两台 Mac 各自改它，Git 就会冲突。把它从追踪中移除：

```bash
git rm --cached .obsidian/workspace.json
git rm --cached .obsidian/workspace-mobile.json 2>/dev/null || true
git commit -m "stop tracking workspace files"
git push
```

确认 `.gitignore` 包含：

```
.obsidian/workspace.json
.obsidian/workspace-mobile.json
.trash/
```

**7. 安装 obsidian-git 插件**

Obsidian → 设置 → 第三方插件 → 关闭安全模式 → 搜索 `obsidian-git`（作者 Vinzent）→ 安装 → 启用

插件设置：

| 配置项 | 推荐值 |
|--------|--------|
| Auto commit-and-sync interval | `10`（分钟） |
| Auto pull interval | `10`（分钟） |
| Commit message | `vault backup: {{date}}` |
| Pull on startup | 开启 |

---

### 第二台 Mac

重复步骤 1-3（安装 Git、配置身份、**生成新的 SSH Key** 并添加到 GitHub），然后：

```bash
git clone git@github.com:用户名/仓库名.git ~/path/to/vault
```

用 Obsidian 打开这个目录，装好 obsidian-git 插件，配置同上。两台 Mac 开始共享同一个 vault。

---

## 换设备

两步清理，不留痕迹：

```bash
# 1. 去 GitHub 删除这台设备的 SSH Key
# GitHub → Settings → SSH and GPG keys → Delete

# 2. 删除本地私钥
rm ~/.ssh/id_ed25519 ~/.ssh/id_ed25519.pub
```

删完之后，这台电脑和你的 GitHub 账号彻底没有关系。其他设备的同步不受影响。

---

## 一键自动配置（Comate Skill）

如果你在用 [Comate](https://comate.baidu.com/)，本仓自带一个 Skill，能自动跑完上面 7 步配置。

```bash
# clone 本仓
git clone git@github.com:itschriszhao/obsidian-free-sync.git ~/obsidian-free-sync

# 软链到 Comate skills 目录
ln -sfn ~/obsidian-free-sync/skill ~/.comate/skills/obsidian-free-sync
```

之后在 Comate 里说一句“帮我配置 obsidian 同步”，Skill 会自动检测 Git、生成 SSH Key、初始化 vault、推送到 GitHub。详见 [`skill/SKILL.md`](skill/SKILL.md)。

---

## 常见问题

详见 [troubleshooting.md](references/troubleshooting.md)

---

## 最后

好用的工具为效率服务，不应该有诸多限制，它本就应该丝滑流畅。

这套方案完全免费，数据在你自己的 GitHub 仓库里，代码完全开源，随时可以 fork 或修改。

如果你在配置过程中遇到问题，欢迎提 Issue。

---

MIT License · [itschriszhao](https://github.com/itschriszhao)
