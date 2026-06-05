# Serenity Thesis Tracker

用于跟踪 Serenity（[@aleabitoreddit](https://x.com/aleabitoreddit)）X/Twitter 投资观点的 PaiWork-first Skill。

它从 Serenity 推文出发，沉淀公司研究文件、可验证 thesis/claim 台账、供应链关系图谱、每日/每周报告和 research backlog，服务于持续更新的投资观点跟踪。

## 重要说明

当前版本面向 **PaiWork** 运行环境设计。

完整工作流依赖 PaiWork 的 analyst report、市场数据、Paipai 搜索、研究数据源和工作区结构。当前版本主要支持 PaiWork 场景，不提供脱离 PaiWork 后的完整通用 agent 运行保证。

`scripts/fetch_daily.py` 只使用 Python 标准库；抓取能力依赖 PaiWork 或前端环境提供的 CDP browser bridge。

## 核心能力

- 通过已有浏览器登录态抓取 Serenity 推文。
- 将原始推文写入 `raw/serenity_statuses.jsonl`。
- 生成每日 markdown intake 文件。
- 引导 PaiWork 判断推文研究价值。
- 从推文中抽取结构化 thesis、claim、ticker 和公司间关系。
- 维护公司研究文件和供应链 Map，避免重复维护同一信息。
- 生成增量式日报、周报和 research backlog。

## 目录结构

```text
serenity-thesis-tracker/
├── assets/
│   └── sample-deep-research-report.md
├── references/
│   ├── schemas.md
│   ├── company_one_pager_prompt.md
│   ├── daily_report_prompt.md
│   └── templates.md
├── scripts/
│   └── fetch_daily.py
├── agents/
│   └── openai.yaml
├── SKILL.md
└── README.md
```

## 运行环境

完整功能需要在 PaiWork 或 PaiWork-compatible 环境中运行，并具备：

- PaiWork analyst/report 工具，用于生成公司页、日报和周报。
- PaiWork 搜索、市场数据和研究数据源，用于独立验证。
- 已登录 X/Twitter 的浏览器会话。
- 一个 CDP-compatible bridge，默认地址为 `http://localhost:3456`，并支持 `/health`、`/new`、`/eval`、`/scroll`、`/close`。
- Python 3.10+，用于运行内置抓取脚本。

`scripts/fetch_daily.py` 不需要安装任何 Python 包。

## 快速开始

在 Skill 根目录运行：

```powershell
python scripts/fetch_daily.py --out-dir tree/Serenity --handle aleabitoreddit
```

脚本会写出：

```text
tree/Serenity/
├── raw/
│   ├── serenity_statuses.jsonl
│   └── serenity_status_ids.txt
└── daily/
    └── serenity_YYYY-MM-DD.md
```

然后在 PaiWork 中继续处理每日 intake，例如：

```text
执行 Serenity 每日观点处理
```

## Skill 工作流

主流程定义在 `SKILL.md`。

高层流程：

1. 抓取 Serenity 推文。
2. 将推文分类为 thesis、update、evidence、catalyst、risk、rebuttal、price-action comment、noise 或 meta。
3. 按 5-15 分做 materiality 评分。
4. 只在推文包含明确投资因果链时抽取 thesis。
5. 将 thesis 拆成可验证 claims 和公司间关系。
6. 更新公司研究文件，作为单公司唯一事实源。
7. 更新供应链 Map，作为关系导航层。
8. 生成日报/周报，作为增量日志。

## 限制

- 这不是一个独立 X scraper；它依赖已登录浏览器和 PaiWork/front-end CDP bridge。
- 它本身不负责翻译和投资分析；抓取完成后的推理、分类、验证和报告生成由 PaiWork 执行。
- X/Twitter DOM 可能变化；如果抓取失败，需要更新 `scripts/fetch_daily.py` 中的 DOM 提取逻辑。
- 数据访问和结果质量取决于登录账号、可见内容和 PaiWork 环境。

## Roadmap

- 通用 agent 工作流。
- 非 PaiWork CDP bridge adapter。
- 可公开的 tests/fixtures。

## License

MIT
