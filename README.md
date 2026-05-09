# Agent Skills

Agent 技能集，使用 **GitButler** (`but`) 进行版本管理，备份于 GitHub。

## 环境要求

- **GitButler CLI** (`but`): 安装指南 https://docs.gitbutler.com/cli-overview
- **GitHub CLI** (`gh`): 用于创建 PR、fork 等 GitHub 操作（可选，推送也可直接用 `but push`）
- **Git**: 版本 `2.40+`

## 首次初始化

### 1. 初始化 GitButler 工作区

```bash
# 在当前目录初始化 git 仓库并配置 GitButler
but setup --init
```

`--init` 会自动执行 `git init`（如果还不是 git 仓库），然后建立 GitButler 工作区模型。

### 2. 创建 GitHub 远程仓库并推送

```bash
# 方法 A: 使用 gh CLI（一步完成创建 + 推送）
gh repo create skills --public --source=. --remote=origin --push

# 方法 B: 手动创建
#   1. 在 GitHub 网页新建仓库 (如 skills)
#   2. 关联远程并推送:
git remote add origin git@github.com:lNwNl/skills.git
but push
```

### 3. 配置 GitButler 远程认证（可选，用于 PR）

```bash
# 配置 Forge 认证，用于 but pr 命令
but config forge auth
```

## 日常操作

### 查看状态

```bash
but status -fv    # 文件视角的完整状态（推荐）
but status        # 简洁状态
but diff          # 查看所有未提交差异
```

### 提交更改

```bash
# 1. 先查看当前状态的 CLI ID
but status -fv

# 2. 使用 CLI ID 提交指定文件到分支
but commit <分支名> -m "提交说明" --changes <文件ID>,<文件ID> --status-after

# 示例:
but commit main -m "add new skill" --changes g0,h1 --status-after
```

**核心概念**: GitButler 不使用 `git add` + `git commit`，而是通过 `--changes` 指定要提交的文件 CLI ID。

### 推送到 GitHub

```bash
but push          # 推送所有有未推送提交的分支
but push <分支ID>  # 推送指定分支
```

### 创建 Pull Request

```bash
but pr new <分支ID>                        # 推送并创建 PR（交互式）
but pr new <分支ID> -m "PR 标题"           # 非交互式创建
but pr new <分支ID> -t                     # 使用 commit message 作为 PR 内容
```

## 分支管理

### 创建分支

```bash
but branch new <分支名>               # 创建独立（并行）分支
but branch new <分支名> -a <锚点分支>   # 创建堆叠（依赖）分支
```

### 堆叠已有分支

```bash
but move <子分支名> <父分支名>    # 将子分支堆叠到父分支上
but move <分支名> zz              # 取消堆叠，变为独立分支
```

### 切换活跃分支

```bash
but apply <分支ID>      # 激活分支（合并到工作区）
but unapply <分支ID>    # 停用分支（移出工作区）
but branch              # 列出所有分支
```

## 历史编辑

### 修正到已有提交

```bash
but absorb <文件ID>      # 自动分析并将更改修正到最合适的提交
but absorb <文件ID> --dry-run  # 预览 absorb 效果
```

### 修改提交信息

```bash
but reword <提交ID> -m "新的提交信息"
```

### 压缩提交

```bash
but squash <分支ID>              # 压缩分支内所有提交为一个
but squash <提交ID>..<提交ID>     # 压缩指定范围
```

### 移动/重排提交

```bash
but move <提交ID> <目标提交ID>    # 移动提交到目标之前
```

### 撤销操作

```bash
but undo                          # 撤销上一次操作
but oplog                         # 查看操作历史
but oplog restore <快照ID>        # 恢复到历史快照
```

### 丢弃未提交更改

```bash
but discard <文件ID>    # 丢弃指定文件的更改
```

## 冲突解决

```bash
# 1. 查看冲突状态
but status -fv

# 2. 进入冲突解决模式
but resolve <提交ID>

# 3. 编辑冲突文件，移除 <<<<<<< ======= >>>>>>> 标记

# 4. 确认冲突已解决
but resolve status

# 5. 完成解决
but resolve finish
```

## 与 Git 命令对照

| 操作 | Git | GitButler (`but`) |
|------|-----|-------------------|
| 查看状态 | `git status` | `but status -fv` |
| 暂存+提交 | `git add` + `git commit` | `but commit <分支> -m "msg" --changes <ID>` |
| 创建分支 | `git checkout -b` | `but branch new <名称>` |
| 推送 | `git push` | `but push` |
| 拉取 | `git pull` | `but pull` |
| 交互式 rebase | `git rebase -i` | `but move` / `but squash` / `but reword` |
| 修正提交 | `git commit --amend` | `but absorb <文件ID>` / `but amend <文件> <提交>` |
| 储藏 | `git stash` | `but unapply <分支ID>` |
| 合并 | `git merge` | `but merge <分支ID>` |

## Tips

- **永远不要**在 GitButler 工作区使用 `git add`、`git commit`、`git push` 等写入命令
- **CLI ID 每次会话都会变化**，始终从 `but status -fv` 获取最新 ID
- 所有变更命令加上 `--status-after` 可以立即看到变更后的状态
- 使用 `but diff` 可以查看每个 change hunk 级别的 ID，实现更精细的提交
- 如果命令行为与本说明不一致，参考内置文档文件
- 只读 git 命令（`git log`、`git show`、`git blame`）可以安全使用

## 更多文档

- [GitButler CLI 完整命令参考](./gitbutler/references/reference.md)
- [GitButler 核心概念](./gitbutler/references/concepts.md)
- [GitButler 工作流示例](./gitbutler/references/examples.md)
