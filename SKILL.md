---
name: daily-stock-picker
description: |
  每日美股智能选股工具。基于 Finnhub API 获取实时行情、新闻和分析师评级，筛选5只具有上涨潜力的股票。
  使用场景：(1) 每日美股收盘前的选股推荐 (2) 需要实时美股数据和热点题材时 (3) 生成美股每日简报
---

# Daily Stock Picker

基于 Finnhub API 的每日美股智能选股工具。

## 前置要求

- Finnhub API Key（免费版足够）：https://finnhub.io/
- 环境变量：`FINNHUB_API_KEY`

## 核心筛选条件

1. **涨幅潜力 > 5%**：只选预计单日涨幅可能超过5%的股票
2. **技术面配合**（至少满足一项）：
   - 放量突破关键阻力位
   - 均线多头排列（5日>10日>20日）
   - 站上50日/200日均线
3. **题材/催化剂**（至少满足一项）：
   - 财报超预期
   - 分析师上调评级
   - 重大新闻/热点事件
   - 机构大单买入

## API 接口

### 1. 实时行情
```bash
curl "https://finnhub.io/api/v1/quote?symbol=SPY&token=$FINNHUB_API_KEY"
```

### 2. 分析师评级
```bash
curl "https://finnhub.io/api/v1/stock/recommendation?symbol=AAPL&token=$FINNHUB_API_KEY"
```

### 3. 市场新闻
```bash
curl "https://finnhub.io/api/v1/news?category=general&token=$FINNHUB_API_KEY"
```

### 4. 公司基本信息
```bash
curl "https://finnhub.io/api/v1/stock/profile2?symbol=NVDA&token=$FINNHUB_API_KEY"
```

### 5. 竞争对手列表
```bash
curl "https://finnhub.io/api/v1/stock/peers?symbol=AAPL&token=$FINNHUB_API_KEY"
```

## 选股步骤

1. **大盘判断**：获取 SPY、QQQ 走势判断整体情绪
2. **获取新闻**：从市场新闻中提取热点题材
3. **筛选标的**：从热门股票中筛选符合"技术面+题材"的5只
4. **生成报告**：输出格式化选股报告

## 输出格式

```
📈 来财 · 每日荐股 [日期]

【大盘情况】
SPY: xxx (x.xx%) | QQQ: xxx (x.xx%)
市场情绪：xxx

🔥 5只潜力标的

1️⃣ 代码: XXX | 胜率: XX% | 预期: >5%
   技术面: xxxx
   题材: xxxx

2️⃣ 代码: XXX | 胜率: XX% | 预期: >5%
   技术面: xxxx
   题材: xxxx
...

⚠️ 风险提示：本推荐仅供参考，不构成投资建议。
```

## 常见问题

**Q: API 返回权限不足怎么办？**
A: 免费版有些接口受限，可以换用其他股票代码或等待配额刷新。

**Q: 如何设置定时任务？**
A: 使用 OpenClaw cron 设置每日执行：
```bash
openclaw cron add --name "每日荐股" --schedule "0 20 * * 1-5" --tz "Asia/Shanghai"
```

## 注意事项

- 美股交易时段（21:30-04:00 北京时间）为数据最准确时段
- 周末和节假日不更新
- 本推荐仅供参考，投资有风险
