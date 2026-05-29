# vibeland-export

一个 agent skill：分析**当前本地仓库**，生成 `vibeland.json`，用于导入 [VibeLand](https://vibe-land.c01dkit.com) 门户的「快速新增」。

> 安装/更新命令与当前版本号以网站「快速新增」页为准（页面顶部可切换 agent）。下面是常见 agent 的命令。

## Claude Code

```bash
# 安装
git clone -b skill --depth 1 https://github.com/c01dkit/VibeLand.git ~/.claude/skills/vibeland-export
# 更新到最新
cd ~/.claude/skills/vibeland-export && git fetch --depth 1 origin skill && git reset --hard FETCH_HEAD
```

用法：在你的项目仓库里说「用 vibeland-export 生成项目 JSON」。

## Codex CLI

```bash
# 安装 / 更新（幂等覆盖）
curl -fsSL https://raw.githubusercontent.com/c01dkit/VibeLand/skill/SKILL.md -o ~/.codex/prompts/vibeland-export.md
```

用法：在 Codex 中运行 `/vibeland-export`。

## 其它 agent

```bash
curl -fsSL https://raw.githubusercontent.com/c01dkit/VibeLand/skill/SKILL.md -o vibeland-export.md
```

把 `vibeland-export.md` 的内容作为提示词，让你的 agent 分析当前仓库生成 `vibeland.json`。

---

在项目仓库根目录运行后会生成 `vibeland.json`，回到 VibeLand →「新增项目 → 快速新增」上传即可。

## 输出格式

见同目录 `SKILL.md` 中的 JSON 约定：顶层 `{ "vibeland": 1, "project": { … } }`，`title` 必填，
`status ∈ {developing, live, archived}`，`intro` / `devLog` / `plan` 为 Markdown 文本。
