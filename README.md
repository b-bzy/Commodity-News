# Commodity-News · 铝市早报(自动)

每天早晨(**北京时间 07:00**)自动生成一份聚焦**铝(电解铝 / 氧化铝 / 铝加工)**产业链的
中文早报,并推送到 **Telegram 群(Jacob 的个人助手)**。

设计对标公司「AI 早报」([b-bzy/AI-Daily-Briefing](https://github.com/b-bzy/AI-Daily-Briefing))。

## 工作原理

```
┌─────────────────────┐   每天 07:00(北京)   ┌──────────────────────────┐
│  云端定时任务 routine │ ───────────────────▶ │  全新会话:                │
│  (Claude Code)      │                        │  1. 读 docs/GENERATION-… │
└─────────────────────┘                        │  2. WebSearch 检索铝市    │
                                               │  3. 写 briefings/日期-AM.md│
                                               │  4. git push origin main  │
                                               └────────────┬─────────────┘
                                                            │ push (briefings/**.md)
                                                            ▼
                                        ┌──────────────────────────────────┐
                                        │ .github/workflows/daily-briefing  │
                                        │  按 ---SPLIT--- 拆成多张卡片        │
                                        │  逐条发送到 Telegram 群            │
                                        └──────────────────────────────────┘
```

- **内容生成**由定时任务(routine)完成,用 `WebSearch` 抓取过去 24h 铝市动态,按
  `docs/GENERATION-GUIDE.md` 的板块与格式写成 `briefings/YYYY-MM-DD-AM.md`。
- **推送投递**由 GitHub Action 完成:监听 `briefings/**.md` 的 push,按 `---SPLIT---`
  拆卡片,用 Telegram Bot API 逐条发到群里。

## 目录结构

```
.
├── .github/workflows/daily-briefing.yml   # 推送到 Telegram 的工作流
├── briefings/                              # 每日早报产物(YYYY-MM-DD-AM.md)
├── docs/
│   ├── GENERATION-GUIDE.md                 # 生成规范(定时任务照此执行)★权威
│   └── example-briefing.md                 # 格式示例/模板(数字为演示占位)
├── CLAUDE.md                               # 给 Claude 会话的仓库说明
└── README.md
```

## 启用步骤(一次性)

1. **把本骨架合并到 `main`**(Action 与定时任务都跟踪默认分支 `main`)。
2. **在仓库添加两个 Secret**(Settings → Secrets and variables → Actions → New secret):
   | 名称 | 值 |
   |------|----|
   | `TELEGRAM_BOT_TOKEN` | 「Jacob 的个人助手」bot 的 token(从 @BotFather 获取;可复用 AI 早报同款 bot) |
   | `TELEGRAM_CHAT_ID`   | `-5275069079`(目标 Telegram 群) |
3. **确保该 bot 已被拉进目标群** `-5275069079`,且有发言权限。
4. 想立刻测试:Actions 页面手动 `Run workflow`(workflow_dispatch),或用 `--file` 指定
   `docs/example-briefing.md` 试发。

> 🔒 Token 只放 Secret,绝不写进代码。仓库为 public,群 ID 也建议放 Secret(已如此设计)。

## 手动补发 / 测试

- GitHub Actions → **Aluminum Daily Briefing → Telegram** → Run workflow,
  `file` 留空发最新一篇,或填某个 `briefings/xxxx-AM.md` 路径。
