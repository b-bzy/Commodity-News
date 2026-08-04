# CLAUDE.md

本仓库是**每日「铝市早报」自动推送**系统。

## 如果你是被定时任务(routine)唤起来生成今天早报的

请**严格按 [`docs/GENERATION-GUIDE.md`](docs/GENERATION-GUIDE.md) 执行**,要点:

1. 用 `WebSearch` / `WebFetch` 检索过去约 24 小时的**铝(电解铝/氧化铝/铝加工)**市场动态。
2. 按 5 大板块(行情 / 供应 / 需求 / 库存 / 宏观)写成 **Telegram HTML** 格式,卡片间用
   单独一行 `---SPLIT---` 分隔。版式照抄 [`docs/example-briefing.md`](docs/example-briefing.md)。
3. 文件写到 `briefings/YYYY-MM-DD-AM.md`(日期用**北京时间 Asia/Shanghai**)。
4. **提交前先对齐分支**(fired 会话默认不在 main):`git fetch origin main && git checkout -B main origin/main`,
   再 `git add / commit / pull --rebase origin main / push origin main`。**完整命令见 GENERATION-GUIDE §5**。
   push 后 GitHub Action 自动推送到 Telegram 群(`-5275069079`,Jacob 的个人助手)。
5. **只聚焦铝**;数据必须有真实来源链接,**严禁编造**;空板块写「🈚️ 过去 24 小时内无显著进展」;
   字面 `& < >` 必须转义;封面计数 = 各板块条目数。

**防御**:若仓库缺 `docs/GENERATION-GUIDE.md` 或 `.github/workflows/daily-briefing.yml`
(骨架未合并到 main),不要推送,直接报告并结束。

## 关键约定

- **仓库固定为 `b-bzy/Commodity-News`**:所有 git 操作只在本仓库进行。任何 push 前必须
  先 `git remote get-url origin` 确认输出含 `Commodity-News`;若指向别的仓库(如
  `ai-daily-briefing` / `ai-hotspot-daily`)立即中止,绝不误推。
- 推送目标群 `TELEGRAM_CHAT_ID` = `-5275069079`;bot token 存仓库 Secret,不进代码。
- 生成产物只放 `briefings/`,`docs/` 下的示例不会触发推送。
