# AgroTech 项目协作指南

本文件说明协会成员参与项目时最基本的 Git / GitHub 协作流程

## 核心原则

请记住一条：

> 不直接修改 `main`；所有开发在独立分支完成，并通过 Pull Request 合并

仓库角色通常约定为：

| 角色 | GitHub 权限 | 主要职责 |
| --- | --- | --- |
| 项目负责人 | Admin | 管理仓库、检查 PR、最终合并到 `main` |
| 项目成员 | Write | 开发、Push 开发分支、提交 PR |

`main` 用于保存当前可用、可追溯的主版本，不作为日常开发分支

## 1. 开始开发前

先切回 `main` 并拉取最新内容：

```bash
git switch main
git pull
```

然后创建自己的开发分支：

```bash
git switch -c feat/<功能名称>
```

常用分支命名：

```text
feat/xxx       新功能
fix/xxx        Bug 修复
docs/xxx       文档修改
refactor/xxx   重构
chore/xxx      工程或维护工作
```

例如：

```bash
git switch -c feat/auto-navigation
```

## 2. 开发并提交

完成一个相对独立的修改后：

```bash
git status
git add .
git commit -m "feat: add auto navigation"
```

建议 Commit 信息简洁说明“做了什么”

常用前缀：

```text
feat:     新功能
fix:      修复问题
docs:     文档
refactor: 重构
chore:    维护工作
```

## 3. Push 开发分支

第一次 Push：

```bash
git push -u origin feat/<功能名称>
```

之后继续开发时：

```bash
git push
```

不要执行：

```bash
git push origin main
```

也不要对 `main` 执行：

```bash
git push -f origin main
git push --force origin main
```

如果 GitHub 拒绝向 `main` Push，这是仓库保护规则正常生效，不是权限故障

## 4. 创建 Pull Request

开发分支 Push 到 GitHub 后，创建 Pull Request：

```text
你的开发分支
      ↓
     main
```

PR 中至少说明：

- 本次修改解决什么问题
- 主要修改了什么
- 是否已经完成基本测试
- 是否存在已知问题或后续工作

## 5. 谁负责 Merge

### 项目成员提交的 PR

项目成员完成 PR 后，由项目负责人检查并最终 Merge

```text
项目成员
  ↓
开发分支
  ↓
Pull Request
  ↓
项目负责人检查
  ↓
Merge → main
```

普通成员不负责自行把 PR 合并进 `main`

### 项目负责人自己的修改

如果项目由负责人单独维护，或负责人自己完成某项修改，也仍然先建立开发分支和 PR：

```text
项目负责人
  ↓
开发分支
  ↓
Pull Request
  ↓
检查 Diff
  ↓
Merge → main
```

不需要额外找其他成员进行形式审批

GitHub 在负责人合并时可能显示绕过 Ruleset 的提示，这是仓库规则中为 Repository Admin 预留的 PR-only bypass，属于正常现象

## 6. 合并后

PR 合并后，本地同步最新 `main`：

```bash
git switch main
git pull
```

确认开发分支已经不再需要后，可以删除本地分支：

```bash
git branch -d feat/<功能名称>
```

远程开发分支可在 PR 合并后通过 GitHub 删除

## 7. 遇到冲突怎么办

如果 PR 提示与 `main` 冲突，不要强推 `main`

先更新本地：

```bash
git switch main
git pull
```

再回到开发分支，将最新 `main` 合入自己的分支并解决冲突：

```bash
git switch feat/<功能名称>
git merge main
```

解决冲突后：

```bash
git add .
git commit
git push
```

然后继续原 PR

如果不确定如何处理冲突，先询问项目负责人，不要通过 Force Push `main` 解决

## 8. 一分钟记忆版

普通成员只需要记住：

```text
拉取 main
  ↓
新建分支
  ↓
修改
  ↓
Commit
  ↓
Push 自己的分支
  ↓
创建 PR
  ↓
负责人 Merge
```

项目负责人只需要多负责最后一步：

```text
检查 PR → Merge
```

Ruleset 的具体说明与常见 Push 报错见：

```text
.github/rulesets/README.md
```
