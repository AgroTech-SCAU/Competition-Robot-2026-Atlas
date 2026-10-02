# Main 分支保护规则说明

本目录用于存放 AgroTech 项目的 GitHub Rulesets 配置

当前统一使用：

```text
.github/rulesets/main-protection.json
```

项目负责人创建仓库后，只需要在 GitHub 中导入该文件并启用即可，按照下方仓库初始化流程完成即可

> [!IMPORTANT]
> ## 仓库初始化
>
> 本仓库由 **AgroTech Repository Template** 创建
>
> 项目负责人 / 仓库管理员首次创建仓库后，请完成以下初始化：
>
> - [ ] 点击绿色按钮 `Code` → `Clone using the web URL.` → `git clone <仓库 URL>`，将仓库克隆到本地
> - [ ] 填写本 README 中的项目基本信息、环境、构建与运行方式
> - [ ] 填写 [`docs/plan.md`](docs/plan.md)，明确当前目标与下一步
> - [ ] 确认默认分支为 `main`
> - [ ] **公开仓库**：进入 `Settings → Rulesets → Rulesets → New ruleset → Import a ruleset`
> - [ ] 导入克隆到本地仓库中的 [`.github/rulesets/main-protection.json`](.github/rulesets/main-protection.json)
> - [ ] 加载后点击页面最下方的绿色按钮 `Create`，确认 Ruleset 已启用并作用于 `main`
>
> GitHub Free Organization 的 Rulesets 仅适用于公开仓库；若本仓库为私有仓库且 Settings 中没有 Rulesets 入口，跳过 Ruleset 导入即可
>

## 这套规则做什么

`main-protection.json` 只保护 `main` 分支，目标是让 `main` 始终作为稳定、可追溯的主分支

启用后：

- 禁止直接向 `main` Push
- 禁止 Force Push
- 禁止删除 `main`
- 所有修改必须先通过 Pull Request
- 普通项目成员可以正常创建和 Push 开发分支、提交 PR
- 只有 Repository Admin 可以最终把 PR 合并到 `main`
- 不强制额外 Approval，不要求 CODEOWNERS

在协会项目中，Repository Admin 原则上对应项目负责人，因此可以理解为：

> 成员负责提交修改，项目负责人负责最终合并

## 为什么不强制 Approval

协会中既有多人协作项目，也存在由负责人单独维护的项目

如果统一要求至少 1 人 Approval，单人项目会出现负责人无法批准自己 PR、还需要额外找人形式审批的问题

因此基础规则不设置强制审批人数，而是通过 `main` 的更新权限控制最终合并权

这样：

### 单人项目

```text
负责人开发分支
    ↓
提交 PR
    ↓
负责人检查 Diff
    ↓
负责人合并到 main
```

### 多人项目

```text
成员开发分支
    ↓
提交 PR
    ↓
项目负责人检查
    ↓
项目负责人合并到 main
```

两种情况使用同一套 Ruleset，不需要针对项目人数修改配置

## 项目负责人为什么可以合并

Ruleset 中预置了：

```text
Repository Admin
→ For pull requests only
```

也就是说，Repository Admin 可以在 Pull Request 场景下绕过 `Restrict updates`，从而完成合并；但不能因此直接绕过流程向 `main` Push

> GitHub 在部分界面中可能显示“Merge without waiting for requirements to be met”或类似的绕过规则提示；这是 `Restrict updates + PR-only bypass` 的正常行为，不代表 Ruleset 配置失败

## Push 被拒绝怎么办

如果执行：

```bash
git push origin main
```

GitHub 返回规则违规、受保护分支或 `GH013` 等提示，通常不是账号坏了，也不是仓库权限异常，而是 `main` 已启用保护

正确做法是创建开发分支：

```bash
git switch main
git pull
git switch -c feat/<功能名称>
```

完成修改后：

```bash
git add .
git commit -m "feat: 描述本次修改"
git push -u origin feat/<功能名称>
```

然后在 GitHub 创建 Pull Request：

```text
feat/<功能名称>
        ↓
       main
```

完整成员协作流程见：

```text
.github/CONTRIBUTING.md
```

## 禁止对 main 使用 Force Push

以下操作不得用于 `main`：

```bash
git push --force origin main
git push -f origin main
```

Force Push 会重写 Git 历史，可能覆盖其他成员已经提交的内容，因此 Ruleset 会直接阻止该操作

## 开发分支不受该规则限制

当前 Ruleset 只匹配：

```text
refs/heads/main
```

因此以下开发分支仍可以正常 Push：

```text
feat/xxx
fix/xxx
docs/xxx
refactor/xxx
```

## 初始化方式

项目负责人创建公开项目仓库后：

1. 将仓库 Clone 到本地
2. 进入 GitHub 仓库 `Settings`
3. 打开 `Rules → Rulesets`
4. 选择 `New ruleset → Import a ruleset`
5. 导入 `.github/rulesets/main-protection.json`
6. 检查目标分支为 `main`
7. 点击 `Create`

完成后无需再额外配置审批人、CODEOWNERS 或第二套 Ruleset

> 如果仓库当前套餐或可见性不支持 Repository Rulesets，请以 GitHub 实际提供的功能为准
