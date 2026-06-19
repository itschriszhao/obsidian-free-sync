# 错误处理手册

## git push 被拒绝（远程有内容）

**错误特征**：`rejected ... non-fast-forward` 或 `Updates were rejected`

**自动修复**：

```bash
git pull origin main --allow-unrelated-histories --no-edit
git push -u origin main
```

---

## vim 编辑器弹出

**错误特征**：终端进入 vim 界面，光标停在文件内

**自动修复**：关闭当前终端，新开终端执行：

```bash
git config --global core.editor "true"
git merge --abort
git pull origin main --allow-unrelated-histories --no-edit
git push -u origin main
```

---

## SSH Permission denied (publickey)

**错误特征**：`Permission denied (publickey)` 或 `git@github.com: Permission denied`

**原因**：公钥未添加到 GitHub

**处理**：
1. 输出公钥让用户重新添加：`cat ~/.ssh/id_ed25519.pub`
2. 引导用户去 https://github.com/settings/ssh/new 添加（手机操作）
3. 验证：`ssh -T git@github.com`

---

## GitHub 页面打不开（公司网络屏蔽）

**场景**：用户反馈 GitHub Settings 页面无法访问

**处理**：提示用户用手机流量打开 https://github.com/settings/ssh/new 完成公钥添加

---

## Git 安装失败（Apple 服务器被屏蔽）

**错误特征**：弹窗提示"无法从软件更新服务器获得"

**处理**：
1. 引导用户直接下载：https://git-scm.com/download/mac
2. 安装时若提示"无法验证开发者"：系统设置 → 隐私与安全性 → 仍要打开

---

## SSH 连接超时（22 端口被封）

**错误特征**：`ssh -T git@github.com` 长时间无响应或 `Connection timed out`

**自动修复**：改用 443 端口测试：

```bash
ssh -T -p 443 git@ssh.github.com
```

如果 443 可通，配置 SSH 永久走 443：

```bash
cat >> ~/.ssh/config << 'EOF'
Host github.com
  Hostname ssh.github.com
  Port 443
EOF
```

---

## workspace.json 冲突

**错误特征**：obsidian-git 弹窗提示 workspace.json 冲突

**自动修复**：

```bash
git checkout --theirs .obsidian/workspace.json
git rm --cached .obsidian/workspace.json
git add -A
git commit -m "resolve workspace.json conflict"
git push
```

另一台 Mac 执行 `git pull`。
