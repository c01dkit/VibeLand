---
name: vibeland-export
description: >-
  Analyze the current local project/repository and generate a vibeland.json file
  describing it (title, category, tags, status, server, code path, git remote,
  live URL, intro, dev log, plan) for import into the VibeLand project portal via
  its "快速新增 / Quick Add" feature. Use whenever the user wants to export, index,
  or register the current repo into VibeLand, or says things like
  "用 vibeland-export 生成项目 JSON", "导出到 VibeLand", "生成 vibeland.json",
  "把这个项目加到门户", "export this repo to VibeLand".
---

# VibeLand Export

**Version: 1.1.0**

Generate a single `vibeland.json` describing **the current repository / project** so it
can be uploaded into the [VibeLand](https://vibe-land.c01dkit.com) portal's
**快速新增（Quick Add）**. The instructions are agent-agnostic — they work whether this file
is installed as a Claude Code skill, a Codex prompt, or fed to any other agent CLI.

## What to do

1. **Confirm the project root.** Use the current working directory unless the user points
   elsewhere. Record its absolute path → `codePath`.

2. **Gather facts from the repo** (use read-only commands; never modify project files
   other than writing `vibeland.json`):

   | Field | How to derive |
   |---|---|
   | `title` | `package.json` → `name`, or `pyproject.toml` / `Cargo.toml` name, else the folder name. Prefer a human-readable name; strip scopes like `@org/`. |
   | `summary` | One short sentence (≤ ~40 chars) describing what the project does, from the README's first line / `description`. Used on cards. |
   | `gitUrls` | **Array.** All git remotes: `git remote -v` → unique fetch URLs. Convert SSH `git@host:owner/repo.git` to `https://host/owner/repo`. `[]` if no remote. |
   | `liveUrl` | `package.json` → `homepage`, deployed URL / demo link in README, or a CNAME/`vercel.json`/`netlify.toml` hint. Empty if none. |
   | `devServers` | **Array.** Development hosts if clearly named in config/README (e.g. a dev/staging host). Usually `[]` — leave for the user. |
   | `deployServers` | **Array.** Production/deploy hosts if a deploy config clearly names one. Usually `[]` — leave for the user. |
   | `category` | One short label inferred from the project type, e.g. `Web 应用`, `CLI 工具`, `库 / SDK`, `脚本`, `移动端`. If unsure use `未分类`. |
   | `tags` | Key languages / frameworks detected from deps and file types, e.g. `["react","astro","typescript"]` or `["python","fastapi"]`. Keep ≤ 6, lowercase. |
   | `status` | `live` if there is a working deployed/homepage URL; `archived` if README/git says deprecated or no commits in a long time; otherwise `developing`. |
   | `intro` | A concise Markdown summary from the README's description / first section. 1–3 short paragraphs. |
   | `devLog` | A Markdown bullet list of recent progress, from `CHANGELOG.md` if present, else the last ~10 commit subjects via `git log --pretty=format:'- %ad %s' --date=short -n 10`. |
   | `plan` | A Markdown summary of the roadmap/TODO, from a `Roadmap`/`TODO`/`Plan` section in the README or a `TODO.md`. Empty if none. |

   Do **not** invent screenshots, servers, or URLs. Leave an array empty (`[]`) rather than guessing.

3. **Write `vibeland.json`** at the project root, using this exact shape (single project):

   ```json
   {
     "vibeland": 1,
     "project": {
       "title": "My Project",
       "summary": "一句话说明项目做什么",
       "category": "Web 应用",
       "tags": ["astro", "react", "firebase"],
       "status": "developing",
       "devServers": [],
       "deployServers": [],
       "codePath": "/abs/path/to/repo",
       "gitUrls": ["https://github.com/owner/repo"],
       "liveUrl": "https://example.com",
       "intro": "## 简介\n\n用一句话说明项目做什么…",
       "devLog": "- 2026-05-01 初始化项目\n- 2026-05-10 完成登录",
       "plan": "- [ ] 支持批量导入\n- [ ] 接入 CI"
     }
   }
   ```

   Rules:
   - `title` is **required** and must be non-empty.
   - `status` must be one of `developing` | `live` | `archived` (defaults to `developing`).
   - `tags` / `gitUrls` / `devServers` / `deployServers` are arrays of strings; `intro` / `devLog` / `plan` are Markdown strings.
   - Output **valid JSON** (UTF-8, no comments, no trailing commas).

4. **Tell the user** the file was written and that they should open VibeLand →
   **新增项目 → 快速新增**, then upload `vibeland.json`.

## Notes
- This produces one project per run (VibeLand Quick Add imports a single project).
- Screenshots can't be auto-generated locally — add them later in the portal's editor.
