# review-aaai（审AAAI）

一个 Codex-first、面向论文作者的非官方 AAAI 投稿预审技能。它读取用户指定的 PDF 或主 LaTeX 文件，生成 desk check（格式与合规预检）、三份彼此独立的正式审稿、meta-review（元评审）、总览及可审计候选草稿。

> 本项目与 AAAI 无隶属或认可关系，不替代正式同行评审，也不预测官方决定。内部评分和 Phase 1 风险均明确标为非官方启发式结果。

## 特性

- 支持绝对路径、相对路径和自然语言文件位置。
- 默认输出简体中文；可明确要求纯英文或中英双语。
- 为 Overall 1–6 生成六份彼此隔离的 score-conditioned candidate reviews（分数条件化候选审稿）。
- 使用证据准确性、覆盖度、rubric 完整性、内部一致性和可辩护性选择 R1、R2、R3。
- 关键判断要求页码、章节、图表、公式或明确的“未核验 / 无法判定”边界。
- 不覆盖历史输出；输入不可解析或候选不足时 fail closed。
- 不需要 Web UI、服务器、数据库、外部 LLM API 或额外运行时依赖。

## 仓库结构

```text
review-aaai/
├── README.md
├── .gitignore
├── skill/
│   └── review-aaai/
│       ├── SKILL.md
│       ├── agents/openai.yaml
│       └── references/
└── compat/
    ├── codex/审AAAI.md
    └── claude/审AAAI.md
```

`skill/review-aaai/` 是唯一 canonical workflow（规范工作流）。两个 `compat/` 文件只是可选薄入口，不复制 rubric 或审稿流程。

## 安装到 Codex

当前 Codex 用户级技能目录为 `~/.agents/skills/`。安装前先确认目标不存在，避免覆盖已有版本。

### Windows PowerShell

```powershell
$target = Join-Path $HOME '.agents\skills\review-aaai'
if (Test-Path -LiteralPath $target) { throw "Target already exists: $target" }
New-Item -ItemType Directory -Force -Path (Split-Path -Parent $target) | Out-Null
Copy-Item -Recurse -LiteralPath '.\skill\review-aaai' -Destination $target
```

### macOS / Linux

```bash
target="$HOME/.agents/skills/review-aaai"
test ! -e "$target" || { echo "Target already exists: $target" >&2; exit 1; }
mkdir -p "$HOME/.agents/skills"
cp -R ./skill/review-aaai "$target"
```

如果当前会话没有立即刷新技能列表，请新开 Codex 会话。

## 使用

推荐的正式入口：

```text
$review-aaai C:\path\to\paper.pdf
$review-aaai 论文在当前项目的 submissions 文件夹，纯英文
```

也可以在 Codex 中输入 `$` 或使用 `/skills`，然后选择显示名“审AAAI”；自然语言“审AAAI：请审当前目录里的投稿 PDF”也可触发。

默认输出位于论文同级的新目录：

```text
AAAI_review/<paperstem>_<YYYYMMDD-HHmmss>/
```

正式输出：

```text
00_summary.md
01_desk_check.md
02_review_R1.md
03_review_R2.md
04_review_R3.md
05_meta_review.md
```

候选与选择记录保存在 `_drafts/`。

## 联网与隐私

- 网络工具可用且用户未禁止联网时，默认只对 3–5 条关键或可疑引用执行最小书目事实核验。
- 外部查询只使用题名、作者、年份、DOI、arXiv ID 等最少信息。
- 不向外部服务发送论文全文、未公开表格、实验数据或作者身份信息。
- 用户要求“禁止联网”时，只做正文—参考文献内部一致性检查，并把外部事实逐项标为“未核验”。

## 可选兼容入口

### Deprecated Codex custom prompt

将 `compat/codex/审AAAI.md` 复制到 `~/.codex/prompts/审AAAI.md`，调用形式为：

```text
/prompts:审AAAI <论文位置与要求>
```

Custom prompts 已属于 deprecated compatibility（弃用兼容）机制；新使用者应优先采用 `$review-aaai`。

### Claude Code legacy command

将 `compat/claude/审AAAI.md` 复制到 `~/.claude/commands/审AAAI.md`，然后尝试：

```text
/审AAAI <论文位置与要求>
```

该入口读取同一个 `~/.agents/skills/review-aaai/` canonical skill。Unicode legacy command 的发现行为可能因平台或版本而异。

## 规则来源与边界

技能自己的 `references/rubric-aaai27.md` 记录了核验日期和 AAAI 官方页面 URL，并把官方事实与非官方内部模拟量表明确分区。运行时不依赖本项目开发时使用的任务书、理论文章或验证日志。

## English summary

`review-aaai` is an unofficial, author-facing, Codex-first skill for structured AAAI pre-submission review. It supports PDF and main LaTeX inputs, Chinese/English/bilingual output, six isolated score-conditioned candidates, three defensible formal reviews, a desk check, meta-review, evidence anchoring, privacy-aware citation checks, and fail-closed delivery. The project is not affiliated with or endorsed by AAAI.

## License

This project is licensed under the [MIT License](LICENSE).

## Documentation

- Codex skills: <https://learn.chatgpt.com/docs/build-skills>
- Codex custom prompts: <https://learn.chatgpt.com/docs/custom-prompts>
- Claude Code skills: <https://code.claude.com/docs/en/skills>

