# 常见问题与排错

按问题类型组织。手动配置和 AI Skill 共用同一份。

---

## 1. Git 安装失败（Apple 服务器被屏蔽）

**现象**：弹窗提示"无法从软件更新服务器获得"

**解决**：直接下载安装包绕过 Apple 服务器：

- 下载地址：[git-scm.com/download/mac](https://git-scm.com/download/mac)
- 安装时若提示"无法验证开发者"：系统设置 → 隐私与安全性 → 点"仍要打开"

---

## 2. GitHub 登录提示密码错误

**原因**：GitHub 自 2021 年起不再支持密码认证，必须用 SSH Key 或 Personal Access Token。

**解决**：按 [手动配置 Step 3](manual-setup.md#3️⃣-生成-ssh-key-并添加到-github) 配置 SSH Key。

---

## 3. SSH Key 无法添加到 GitHub（公司网络屏蔽）

**现象**：GitHub Settings 页面打不开。

**解决**：切换到手机流量打开 [github.com/settings/ssh/new](https://github.com/settings/ssh/new) 完成公钥添加。

---

## 4. git push 提示 Permission denied (publickey)

**原因**：SSH Key 未正确添加到 GitHub，或本地私钥路径不对。

**排查**：

```bash
ssh -T git@github.com
```

成功应返回：`Hi 用户名! You've successfully authenticated...`

若失败：

1. `cat ~/.ssh/id_ed25519.pub` 重新复制公钥
2. 去 [github.com/settings/ssh/new](https://github.com/settings/ssh/new) 重新添加（Key type 选 **Authentication Key**）
3. 再次运行 `ssh -T git@github.com` 验证

---

## 5. SSH 连接超时（22 端口被封）

**现象**：`ssh -T git@github.com` 长时间无响应或 `Connection timed out`。

**测试 443 端口**：

```bash
ssh -T -p 443 git@ssh.github.com
```

**若 443 通，永久切到 443**：

```bash
cat >> ~/.ssh/config << 'EOF'
Host github.com
  Hostname ssh.github.com
  Port 443
EOF
```

---

## 6. git push 被拒绝（rejected / non-fast-forward）

**现象**：`rejected ... non-fast-forward` 或 `Updates were rejected`。

**修复**：

```bash
git pull origin main --allow-unrelated-histories --no-edit
git push -u origin main
```

---

## 7. 弹出 vim 编辑器卡住

**原因**：`git pull` / `git merge` 触发 merge commit 编辑。

**预防**：

```bash
git config --global core.editor "true"
```

**已卡住时**：关闭当前终端重开一个，然后：

```bash
git merge --abort
git pull origin main --allow-unrelated-histories --no-edit
```

---

## 8. obsidian-git 提示 workspace.json 冲突

**原因**：两台 Mac 各自修改了界面状态文件，Git 不知道用哪个版本。

**解决**：让这个文件不再被 Git 追踪：

```bash
cd /path/to/vault
git checkout --theirs .obsidian/workspace.json
git rm --cached .obsidian/workspace.json
git add -A
git commit -m "resolve conflict and stop tracking workspace.json"
git push
```

另一台 Mac 执行 `git pull` 即可。

---

## 9. 换电脑 / 设备清理

两步清得干干净净，互不影响其他设备：

```bash
# 1. GitHub → Settings → SSH and GPG keys → 删除这台设备的 Key
# 2. 删除本地私钥
rm ~/.ssh/id_ed25519 ~/.ssh/id_ed25519.pub
```

完成后，这台电脑与你的 GitHub 账号彻底脱钩，其他设备同步不受影响。
