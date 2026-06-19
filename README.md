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

## 🚦 选一条路径开始

<table>
<tr>
<td width="50%" align="center">

### 🧑‍💻 手动配置

跟着指南一步步做
**不需要任何 AI 工具**

👉 **[manual-setup.md](manual-setup.md)**

</td>
<td width="50%" align="center">

### 🤖 AI 自动配置

让支持 Skills 协议的 AI agent
自动跑完所有步骤

👉 **[skill/SKILL.md](skill/SKILL.md)**

</td>
</tr>
</table>

> 两条路径产出完全相同。没装 AI 工具？直接走左边即可。

---

## 🤔 为什么需要它？

你也许遇到过这些问题：

- 💸 **官方付费 Sync 有限制**：标准版 1GB / 5MB 单文件，Plus 版 10GB / 200MB，按月订阅，不能买断
- 🌍 **国内同步龟速**：官方服务器在海外，写一段笔记等半天
- 🍎 **iCloud 跨 Apple ID 直接失效**：工作 Mac 和个人 Mac 用不同 Apple ID，两边笔记彼此隔离
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

## 📁 仓库结构

```
obsidian-free-sync/
├── README.md             # 你正在看的入口
├── manual-setup.md       # 手动配置完整指南（人类视角）
├── skill/
│   └── SKILL.md          # AI agent 自动化指令（Skills 协议）
├── docs/
│   ├── plugin-config.md  # obsidian-git 插件配置参考
│   └── troubleshooting.md # 9 类常见问题与排错（手动 + AI 共用）
└── LICENSE
```

---

## 🆘 遇到问题？

直接查 [`docs/troubleshooting.md`](docs/troubleshooting.md)，覆盖：

- Git 安装失败 / GitHub 登录密码错误 / SSH Key 配置问题
- 公司网络屏蔽 / SSH 22 端口被封（走 443）
- push 被拒绝 / vim 卡住 / workspace.json 冲突
- 换电脑 / 设备清理

或欢迎提 Issue。

---

## 🌱 写在最后

好用的工具应该为效率服务，不应该有诸多限制——它本就该丝滑流畅。

这套方案完全免费、完全开源、数据完全在你手里。觉得有用就 ⭐ 一下。

<div align="center">

—— Made with ☕ by [@itschriszhao](https://github.com/itschriszhao) · MIT License

</div>
