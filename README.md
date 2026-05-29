# vibeland-export skill

一个 Claude Code skill：分析**当前本地仓库**，生成 `vibeland.json`，用于导入 [VibeLand](https://vibe-land.c01dkit.com) 门户的「快速新增」。

## 安装（给使用者）

把 skill 克隆到 Claude Code 的 skills 目录（一次性）：

```bash
git clone -b skill --depth 1 https://github.com/c01dkit/VibeLand.git ~/.claude/skills/vibeland-export
```

然后在你的项目仓库根目录打开 Claude Code，说：

> 用 vibeland-export 生成项目 JSON

skill 会分析仓库并在根目录写出 `vibeland.json`。回到 VibeLand →「新增项目 → 快速新增」上传它即可。

## 发布（给维护者）

这个目录（`skill/`）的内容就是 `skill` 分支的**根目录**。`git clone -b skill ... ~/.claude/skills/vibeland-export`
要求 `SKILL.md` 位于分支根，因此发布时把本目录内容放到 `skill` 分支根：

```bash
# 在仓库内，用 subtree split 把 skill/ 目录单独切到 skill 分支并推送
git subtree split --prefix skill -b skill
git push origin skill --force
```

> 之后每次更新 `skill/SKILL.md` 后重复上面两条命令即可同步 `skill` 分支。

## 输出格式

见 `SKILL.md` 中的 JSON 约定（与网站 `src/lib/importProject.ts` 的解析器保持一致）：
顶层 `{ "vibeland": 1, "project": { … } }`，`title` 必填，`status ∈ {developing, live, archived}`。
