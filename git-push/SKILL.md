---
name: git-push
description: 将本地分支安全推送到远程，含状态检查、fetch/rebase 同步与推送总结。Use when the user asks to push, sync to remote, or @ git-push; not for commit-only requests.
metadata:
  author: phinnpeng
  version: "1.0"
---

# Git Push

## 目标

在用户明确要求推送时，检查本地仓库状态，同步远程最新代码，安全执行 `git push`，并汇报推送结果与变更概要。

## 何时使用

- 用户 @ 本技能，或说「推送 / push / 同步到远程」
- 本地已有 commit，需要将分支或标签推送到 origin

**不适用**（先引导用户完成前置步骤）：

- 工作区有未提交改动且用户未要求 commit → 提醒 `git add` + `git commit`
- 用户仅要求写 commit message 或查看 diff → 不推送
- 用户未明确要求 push → **不得**主动推送

## 工作流

```
Task Progress:
- [ ] 1. 环境确认
- [ ] 2. 同步远程
- [ ] 3. 推送决策
- [ ] 4. 执行推送
- [ ] 5. 总结报告
```

### 1. 环境确认

并行或依次执行：

```bash
git status
git branch --show-current
```

| 状态 | 处理 |
|------|------|
| 有未暂存/未跟踪文件 | 提醒先 `git add` 与 `git commit`，暂停推送 |
| 工作区干净 | 继续 |
| 无 commit / 空分支 | 告知无可推送内容 |
| 当前分支为 `main` / `master` 且需 force push | **拒绝** `--force`，警告用户并询问替代方案 |

**禁止**：修改 `git config`；使用 `--no-verify` 等跳过 hook 的选项（除非用户明确要求）。

### 2. 同步远程

```bash
git fetch origin
git pull --rebase origin <当前分支>
```

| 结果 | 处理 |
|------|------|
| 成功 | 继续推送决策 |
| 冲突 | **暂停**；说明冲突文件，指导解决后 `git rebase --continue`，再从头检查 |
| 无 upstream | 首次推送用 `git push -u origin <分支>` |

### 3. 推送决策

默认：

```bash
git push origin <当前分支>
```

**强制推送**（仅当用户**明确**要求，且非 main/master）：

```bash
git push --force-with-lease origin <当前分支>
```

| 选项 | 说明 |
|------|------|
| `--force-with-lease` | 远程未被他人更新时才覆盖，**优先使用** |
| `--force` | 可能覆盖他人提交，**避免**；用户坚持时需再次确认风险 |

推送**标签**时：

```bash
git push origin <tag名>
# 或推送全部标签
git push origin --tags
```

### 4. 执行推送

- 执行选定命令，**原文展示**终端输出
- 根据输出确认成功（如 `new branch`、`* ->`、up-to-date）
- 失败时保留错误信息，给出下一步（权限、网络、冲突、protected branch）

### 5. 总结报告

```bash
git log origin/<分支>..<分支> --oneline
git status
```

汇报内容：

- 分支名与远程跟踪关系
- 本次推送的 commit 数量与列表（`oneline`）
- 是否推送了标签
- 远程最新状态（已与 origin 同步 / 仍领先 N 个 commit）

## 输出格式

向用户汇报时使用中文（命令与终端输出可保留原文）：

```markdown
## 推送结果

**分支**：`<branch>` → `origin/<branch>`

**前置检查**：
- 工作区：干净 / 存在未提交改动（已暂停）

**同步**：fetch + rebase 成功 / 冲突（已暂停）

**推送命令**：`git push ...`

**本次提交**（N 个）：
- `abc1234` 提交说明
- ...

**状态**：已与远程同步 / ...
```

## 原则

- **用户授权**：仅在用户明确要求时推送
- **先 commit 后 push**：未提交改动不代为 commit（除非用户在同一请求中明确要求）
- **安全优先**：禁止对 main/master 做 force push；强制推送只用 `--force-with-lease`
- **不碰配置**：不执行 `git config` 变更
- **全程中文**：与用户交互使用简体中文；命令与 git 输出保持原文
