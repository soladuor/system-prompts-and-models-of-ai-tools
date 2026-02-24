# 📦 gh-action 分支 · 使用说明

本分支 **仅存放 GitHub Actions 工作流**，**不含业务代码**，确保默认分支（`main`）与上游仓库保持 **100% 一致**。

---

## 🚀 一键复用流程

| 步骤           | 操作                                                         |
| -------------- | ------------------------------------------------------------ |
| ① Fork 原仓库  | 正常点击 GitHub Fork 按钮                                    |
| ② 切到本分支   | `git checkout --orphan gh-action`                            |
| ③ 上传文件     | 把本分支的 `upstream-sync.yml` 文件放进去并推送              |
| ④ 设置默认分支 | Settings → Branches → 默认分支改为 `gh-action`，便于手动触发 |

---

## 📄 工作流文件（upstream-sync.yml）

- **名称**：Upstream Sync
- **功能**：每 1 小时的 0 点自动把上游仓库最新代码同步到本仓库的同名分支
- **触发方式**
  - 定时：`0 */1 * * *`（每 1 小时的 0 点）
  - 手动：GitHub → Actions → Upstream Sync → Run workflow
- **矩阵分支**：当前仅同步 `main`，需要多分支时把 `matrix.branch` 列表改成 `[main, dev, release]` 即可
- **上游仓库**：由仓库变量 `UPSTREAM_REPO` 控制，**无需改动 yml**
- **所需权限**：`contents: write`（自动生成 `GITHUB_TOKEN`）

---

## 🛠️ 本地快速测试

```bash
# 克隆仓库
git clone https://github.com/<your-username>/<repo>.git
cd <repo>

# 切换到 gh-action 分支
git checkout gh-action

# 修改上游仓库变量（也可直接在网页设置）
# 编辑 .github/workflows/upstream-sync.yml 中的 env.UPSTREAM_REPO 以及 jobs.sync.strategy.matrix.branch
git add .
git commit -m "update upstream repo"
git push origin gh-action
```

---

## ❓ 常见问题

| 问题                 | 回答                                                                                                                          |
| -------------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| 上游仓库私有怎么办？ | 额外创建 Secret：`UPSTREAM_REPO_TOKEN`（PAT），并在 yml 里加 `upstream_repo_access_token: ${{ secrets.UPSTREAM_REPO_TOKEN }}` |
| 如何改同步频率？     | 修改 yml 中的 `- cron: "*/30 * * * *"`，可用 [crontab.guru](https://crontab.guru) 验证                                        |
| 默认分支一定要改吗？ | 不改也行，但位于默认分支中的 workflow 才能在 actions 中看到。                                                                 |

---

💡 **提示**：
业务代码请保持在 `main`/`dev` 等分支，**不要向本分支提交任何业务文件**，避免同步冲突。
