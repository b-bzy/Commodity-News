# Commodity-News · 铝市早报(自动)

每天早晨(**北京时间约 07:07**)自动生成一份聚焦**铝(电解铝 / 氧化铝 / 铝加工)**产业链的
中文早报,并推送到 **Telegram 群(Jacob 的个人助手,`-5275069079`)**。

设计对标公司「AI 早报」([b-bzy/AI-Daily-Briefing](https://github.com/b-bzy/AI-Daily-Briefing))。

## 工作原理

```
┌─────────────────────┐  每天 ~07:07(北京)  ┌──────────────────────────┐
│  云端定时任务 routine │ ───────────────────▶ │  全新会话:                │
│  (Claude Code)      │                       │  1. 锁定 Commodity-News 仓库 │
└─────────────────────┘                       │  2. 对齐 main 分支           │
                                              │  3. WebSearch 检索铝市       │
                                              │  4. 写 briefings/日期-AM.md  │
                                              │  5. push origin main         │
                                              └────────────┬─────────────┘
                                                           │ push (briefings/*.md)
                                                           ▼
                                       ┌──────────────────────────────────┐
                                       │ .github/workflows/daily-briefing  │
                                       │  按 ---SPLIT--- 拆成多张卡片        │
                                       │  逐条发送到 Telegram 群            │
                                       │ (HTML 失败自动降级纯文本重发)      │
                                       └──────────────────────────────────┘
```

- **内容生成**由定时任务(routine)完成,用 `WebSearch` 抓取过去 24h 铝市动态,按
  `docs/GENERATION-GUIDE.md` 的板块与格式写成 `briefings/YYYY-MM-DD-AM.md`。
- **推送投递**由 GitHub Action 完成:监听 `briefings/*.md` 的 push,按 `---SPLIT---`
  拆卡片,用 Telegram Bot API 逐条发到群里(HTML 解析失败会自动降级为纯文本重发,保证送达)。

## 目录结构

```
.
├── .github/workflows/daily-briefing.yml   # 推送到 Telegram 的工作流(含降级/容错)
├── briefings/                              # 每日早报产物(YYYY-MM-DD-AM.md)
├── docs/
│   ├── GENERATION-GUIDE.md                 # 生成规范(定时任务照此执行)★唯一权威
│   └── example-briefing.md                 # 格式示例/模板(含转义与空板块示范)
├── CLAUDE.md                               # 给 Claude 会话的仓库说明
└── README.md
```

## 启用清单

- [x] 骨架已在 `main` 分支(Action 与定时任务都跟踪 `main`)。
- [x] 定时任务(Routine)已创建:每天北京 ~07:07,生成并 push。
- [ ] **添加两个 Secret**(Settings → Secrets and variables → Actions → New secret):
  | 名称 | 值 |
  |------|----|
  | `TELEGRAM_BOT_TOKEN` | 「Jacob 的个人助手」bot 的 token(@BotFather;可复用 AI 早报同款 bot) |
  | `TELEGRAM_CHAT_ID`   | `-5275069079` |
- [ ] 确认该 bot 已被拉进群 `-5275069079` 且有发言权限。
- [ ] **建议**把默认分支设为 `main`(Settings → Branches → Default branch)。
      不改也能自动推送(push 触发不看默认分支),但**手动 Run workflow 时需在分支下拉里选 `main`**。

> 🔒 Token 只放 Secret,绝不写进代码。仓库为 public,群 ID 也放 Secret(已如此设计)。

## 手动补发 / 测试

GitHub Actions → **Aluminum Daily Briefing → Telegram** → Run workflow:
- **分支下拉必须选 `main`**(默认分支若还没切成 main,这里默认会预选到别的分支,导致找不到文件)。
- `file` 留空 = 发 `main` 上最新一篇 `briefings/*.md`;或填某个 `briefings/xxxx-AM.md` 路径。
  (只接受 `briefings/*.md`;`docs/example-briefing.md` 不能通过手动触发发送。)

想验证「真实生成 + 推送」整条链路:直接手动触发一次定时任务(routine),它会生成当天真实早报
并 push,随后 Action 自动发到群。
