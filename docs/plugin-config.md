# obsidian-git 插件推荐配置

安装插件后，在插件设置页面配置以下关键项：

| 配置项 | 推荐值 | 说明 |
|--------|--------|------|
| Auto commit-and-sync interval (minutes) | `10` | 每 10 分钟自动提交并同步 |
| Auto pull interval (minutes) | `10` | 每 10 分钟自动拉取 |
| Commit message | `vault backup: {{date}}` | 自动 commit 消息格式 |
| Pull on startup | 开启 | 打开 Obsidian 时自动拉取最新内容 |

## 手动同步

`Cmd+P` → 搜索 `git push` 或 `git pull` 可手动触发同步
