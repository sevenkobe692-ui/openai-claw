# 📈 OpenClaw 炒股方案汇总

**整理日期：** 2026 年 2 月 23 日

本文件汇总了 GitHub 上使用 OpenClaw 进行股票交易的方案和资源。

---

## 🔥 核心资源

### 1. 股票分析技能

#### 📊 stock-daily-analysis-skill
- **仓库：** https://github.com/chjm-ai/stock-daily-analysis-skill
- **功能：** LLM 驱动的每日股票分析系统
- **支持市场：** A 股/港股/美股
- **核心功能：**
  - ✅ 技术面分析（MA/MACD/RSI/乖离率）
  - ✅ AI 决策建议（支持 DeepSeek/Gemini）
  - ✅ 市场数据源集成
  - ✅ 买入/持有/观望建议

**安装方法：**
```bash
cd ~/workspace/skills/
git clone https://github.com/chjm-ai/stock-daily-analysis-skill.git
pip3 install akshare pandas numpy requests
```

#### 📈 openclaw-market-data
- **仓库：** https://github.com/chjm-ai/openclaw-market-data
- **功能：** 市场数据获取技能
- **说明：** 可与 stock-daily-analysis 集成使用

---

### 2. 交易技能库

#### 💰 openclaw-skills (BankrBot)
- **仓库：** https://github.com/BankrBot/openclaw-skills
- **功能：** AI 代理技能库
- **包含：**
  - Polymarket 交易
  - 加密货币交易
  - DeFi 操作
  - 自动化交易

**特色：** 自主财务操作 - 代理管理投资组合、执行交易、处理支付

---

### 3. Polymarket 交易技能

#### 🎯 polyclaw
- **仓库：** https://github.com/chainstacklabs/polyclaw
- **功能：** Polymarket 交易技能
- **功能：**
  - 浏览市场
  - 执行交易
  - 追踪持仓
  - 发现对冲机会

---

## 📚 完整教程

### 阿里云开发者社区教程

#### 教程 1：OpenClaw skills 重构量化交易逻辑
- **链接：** https://developer.aliyun.com/article/1712090
- **发布时间：** 1 周前
- **内容：**
  - 阿里云零门槛部署指南
  - 四大核心交易场景实战
  - 风险控制体系
  - 策略优化技巧

#### 教程 2：AI 量化炒股 24 小时自动盯盘
- **链接：** https://developer.aliyun.com/article/1712082
- **发布时间：** 1 周前
- **内容：**
  - 企业级部署方案
  - 实战案例分享
  - 技能组合优化
  - 避坑指南

---

## 🎯 四大核心交易场景

### 场景 1：实时盯盘 - 精准捕捉买卖信号

**核心技能：** `stock-monitor`

**配置示例：**
```bash
# 配置监控标的
openclaw skill config stock-monitor --stocks "00700.HK,600519.SH,AAPL.US"

# 设置预警规则
openclaw skill config stock-monitor --rules '{
  "fallThreshold": -3,
  "riseThreshold": 5,
  "volumeMultiplier": 2
}'

# 设置监控频率（港股交易时间每 5 分钟检查）
openclaw cron add "stock-monitor-task" --expression "*/5 9-16 * * 1-5" --command "openclaw skill run stock-monitor"
```

**自然语言指令：**
```
帮我监控腾讯控股（00700.HK）、贵州茅台（600519.SH）、苹果（AAPL.US），
设置以下预警条件：下跌超过 3%、上涨超过 5%、成交量翻倍，
每周一至周五港股交易时间每 5 分钟检查一次，预警消息发送到我的飞书
```

---

### 场景 2：市场情绪分析 - 把握资金动向

**核心技能：** `market-sentiment`

**配置示例：**
```bash
# 配置监控来源
openclaw skill config market-sentiment --sources "finance-kol,twitter,weibo,industry-news"

# 设置监控关键词
openclaw skill config market-sentiment --keywords "人工智能，新能源汽车，白酒，半导体"

# 设置分析频率（每小时一次）
openclaw cron add "market-sentiment-task" --expression "0 * * * *" --command "openclaw skill run market-sentiment"
```

---

### 场景 3：财报异常分析 - AI 帮你"扫雷"

**核心技能：** `financial-report-analysis`

**配置示例：**
```bash
# 配置分析维度
openclaw skill config financial-report-analysis --focusPoints "unmentioned-info,non-recurring-gains,inventory-turnover"

# 上传财报并分析
openclaw skill run financial-report-analysis --file "~/网易 2025Q3 财报.pdf" --company "NTES/9999"

# 自动抓取财报
openclaw skill config financial-report-analysis --autoFetch "00700.HK,600519.SH" --interval "quarterly"
```

---

### 场景 4：竞对分析 - 寻找价值洼地

**核心技能：** `competitor-analysis`

**配置示例：**
```bash
# 配置对比标的与分析维度
openclaw skill config competitor-analysis --companies "沃尔玛，亚马逊" --dimensions "revenue,growth-rate,profit-margin"

# 执行对比分析
openclaw skill run competitor-analysis --industry "零售业" --reportType "detailed"
```

---

## 💡 三大经典策略组合

### 策略 1：趋势交易策略（短线）

**技能组合：** `stock-monitor` + `market-sentiment` + `trend-analysis` + `risk-calculator`

**配置：**
```bash
clawhub install trend-analysis
openclaw workflow create "trend-trading" --skills "stock-monitor,market-sentiment,trend-analysis,risk-calculator"
openclaw workflow config "trend-trading" --params '{
  "longCondition": "ma5 > ma10 && sentimentScore > 0.5",
  "shortCondition": "ma5 < ma10 || sentimentScore < -0.3",
  "stopLossRatio": -5,
  "takeProfitRatio": 10,
  "positionRatio": 8
}'
```

---

### 策略 2：价值投资策略（中长期）

**技能组合：** `financial-report-analysis` + `competitor-analysis` + `valuation-calculator` + `dividend-tracker`

**配置：**
```bash
clawhub install valuation-calculator dividend-tracker
openclaw workflow create "value-investing" --skills "financial-report-analysis,competitor-analysis,valuation-calculator,dividend-tracker"
openclaw workflow config "value-investing" --params '{
  "peThreshold": 15,
  "pbThreshold": 2,
  "dividendYieldThreshold": 3,
  "profitGrowthRate": 10
}'
```

---

### 策略 3：套利策略（低风险）

**技能组合：** `arbitrage-scanner` + `finance-data` + `risk-calculator` + `order-executor`

**配置：**
```bash
clawhub install arbitrage-scanner order-executor
openclaw workflow create "arbitrage-trading" --skills "arbitrage-scanner,finance-data,risk-calculator,order-executor"
openclaw workflow config "arbitrage-trading" --params '{
  "spreadThreshold": 10,
  "maxPositionRatio": 5,
  "profitTarget": 3
}'
```

---

## ⚠️ 风险控制体系

### 仓位管理
```bash
# 单只标的不超过总资金的 10%
openclaw config set trading.position.maxSingleStockRatio 10

# 单一行业仓位不超过 30%
openclaw config set trading.position.maxIndustryRatio 30

# 总亏损超过 15% 暂停交易
openclaw config set trading.risk.maxDrawdown 15
```

### 策略回测
```bash
# 安装回测技能
clawhub install backtest-tool

# 回测策略（用近 1 年历史数据）
openclaw skill run backtest-tool --workflow "trend-trading" --startDate "2025-02-23" --endDate "2026-02-23" --initialCapital 100000
```

### 回测关键指标参考
| 指标 | 优秀标准 | 可接受标准 | 需优化标准 |
|------|----------|------------|------------|
| 年化收益率 | ≥20% | 10%-20% | <10% |
| 最大回撤 | <15% | 15%-25% | >25% |
| 胜率 | ≥60% | 50%-60% | <50% |
| 盈亏比 | ≥2:1 | 1.5:1-2:1 | <1.5:1 |

---

## 🚀 快速开始步骤

### 1. 安装基础技能
```bash
cd ~/workspace/skills/
git clone https://github.com/chjm-ai/stock-daily-analysis-skill.git
git clone https://github.com/chjm-ai/openclaw-market-data.git market-data
pip3 install akshare pandas numpy requests
```

### 2. 配置 API Key
```bash
# 配置 DeepSeek API（推荐）
openclaw config set ai.provider openai
openclaw config set ai.api_key "sk-your-deepseek-key"
openclaw config set ai.base_url "https://api.deepseek.com/v1"
```

### 3. 配置数据源
```json
// config.json
{
  "data": {
    "use_market_data_skill": true,
    "market_data_skill_path": "../market-data"
  },
  "ai": {
    "provider": "openai",
    "api_key": "sk-your-key",
    "base_url": "https://api.deepseek.com/v1",
    "model": "deepseek-chat"
  }
}
```

### 4. 测试运行
```bash
# 分析单只股票
openclaw skill run stock-daily-analysis --stock "600519"

# 分析多只股票
openclaw skill run stock-daily-analysis --stocks "600519,AAPL,00700.HK"
```

---

## 📖 更多资源

### 文档与教程
- **Medium 教程：** https://medium.com/@luoyelittledream/building-an-ai-powered-automated-trading-system
- **Phemex 分析：** https://phemex.com/blogs/openclaw-polymarket-automated-trading-analysis
- **Skills Marketplace：** https://lobehub.com/skills/openclaw-skills-polymarket-trading

### 相关项目
- **Rockyzsu/stock：** https://github.com/Rockyzsu/stock（30 天掌握量化交易）
- **OpenClaw 官方：** https://github.com/openclaw/openclaw

---

## ⚠️ 重要提示

1. **OpenClaw 是工具，不是圣杯** - 它执行你的策略，但不会替你创造策略
2. **风险控制第一** - 务必设置止损线和仓位限制
3. **先回测再实盘** - 用历史数据验证策略有效性
4. **持续优化** - 市场在变，策略也需要调整
5. **投资有风险** - 本文档仅供学习参考，不构成投资建议

---

**最后更新：** 2026 年 2 月 23 日
**整理：** Seven (AI 助手)
