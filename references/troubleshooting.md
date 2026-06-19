# 常见问题处理

## 1. Git 安装失败（Apple 服务器被屏蔽）

**现象**：弹窗提示"无法从软件更新服务器获得"

**解决**：直接下载安装包，绕过 Apple 服务器：
- 下载地址：`https://git-scm.com/download/mac`
- 安装时若提示"无法验证开发者"：系统设置 → 隐私与安全性 → 点"仍要打开"

---

## 2. GitHub 登录提示密码错误

**原因**：GitHub 2021 年起不再支持密码认证，必须用 SSH Key 或 Personal Access Token。

**解决**：按本 skill 的 Step 3 配置 SSH Key。

---

## 3. SSH Key 无法添加到 GitHub（公司网络屏蔽）

**现象**：GitHub Settings 页面打不开

**解决**：用手机打开 `https://github.com/settings/ssh/new` 完成添加。

---

## 4. git push 提示 Permission denied (publickey)

**原因**：SSH Key 未正确添加到 GitHub，或本地私钥路径不对。

**排查**：
```bash
ssh -T git@github.com   # 测试 SSH 连接
```
成功返回：`Hi 用户名! You've successfully authenticated...`

若失败，重新执行 Step 3。

---

## 5. git push 提示 rejected（远程有内容）

```bash
git pull origin main --allow-unrelated-histories --no-edit
git push -u origin main
```

---

## 6. 弹出 vim 编辑器卡住

**原因**：git pull 触发 merge commit 编辑。

**预防**：提前执行 `git config --global core.editor "true"` 可彻底避免。

**已卡住时**：关闭终端重新开一个，然后运行：
```bash
git merge --abort
git pull origin main --allow-unrelated-histories --no-edit
```

---

## 7. obsidian-git 提示 workspace.json 冲突

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

## 8. 离职/换电脑清理

```bash
# 1. GitHub 上删除对应 SSH Key
# GitHub → Settings → SSH and GPG keys → Delete

# 2. 删除本地私钥
rm ~/.ssh/id_ed25519
rm ~/.ssh/id_ed25519.pub
```
