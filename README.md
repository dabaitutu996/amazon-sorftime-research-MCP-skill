# 卖家精灵 Amazon 全链路数据调研 Skill

> 基于 **卖家精灵（SellerSprite）MCP** 的 Amazon 数据调研工具集。
> 通过 43 个 MCP 数据工具完成选品分析、关键词研究、竞品监控、市场分析、定价策略、评论分析、广告优化、流量分析、Listing 优化和蓝海机会挖掘。

---

## 简介

本仓库只包含一个技能：`sellersprite-amazon-research`。

- **数据来源**：卖家精灵 MCP（`https://mcp.sellersprite.com/mcp`）
- **使用方式**：在 Claude Code 里输入 `/命令` 或用自然语言对话，由 LLM 通过 MCP 直接取数并生成报告
- **输出**：每个调研生成 `market_report.md` + `market_report.html` + `research_data.json`

---

## 目录结构

```
.
├── .mcp.json                         # MCP 配置（只含 sellersprite）
├── .claude/
│   └── skills/
│       └── sellersprite-amazon-research/
│           ├── skill.md              # 技能主入口（frontmatter + 指令 + 字段陷阱）
│           ├── README.md             # 技能英文说明
│           ├── comprehensive/        # 10 个综合分析工作流
│           ├── tactical/             # 17 张战术选品策略卡
│           ├── reference/            # MCP 工具参数参考（含 43 工具清单）
│           └── scripts/              # 示例/工具脚本（非必需）
└── REPORTS-ALL/
    └── 卖家精灵reports/              # 示例报告
```

---

## 前置条件

- [Claude Code](https://claude.ai/code) CLI
- 卖家精灵账号 + API 密钥（secret key）
- （仅跑脚本时需要）Python 3

---

## 配置

### 1. MCP 密钥（`.mcp.json`）

⚠️ 密钥通过 **header `secret-key`** 传入，**不是** URL 参数，也**不是** `Authorization`：

```json
{
  "mcpServers": {
    "sellersprite": {
      "url": "https://mcp.sellersprite.com/mcp",
      "headers": {
        "secret-key": "你的密钥"
      }
    }
  }
}
```

| 配置项 | ✅ 正确 | ❌ 错误 |
|--------|--------|--------|
| URL | `https://mcp.sellersprite.com/mcp` | `.../sse`、`.../mcp?key=xxx` |
| 认证 | `headers.secret-key` | URL 参数 `?key=`、`Authorization` |
| Accept | 必须带 `application/json, text/event-stream` | 只带 `application/json` 会 400 |

### 2. 安装技能

技能已放在 `.claude/skills/sellersprite-amazon-research/`，Claude Code 会自动发现，无需额外操作。

在仓库根目录启动即可：

```bash
cd /home/yangji/amazon-sorftime-research-MCP-skill
claude
```

---

## 快速开始

在 Claude Code 里直接输入命令或自然语言：

```
/product-research wireless microphone US
/market-analysis 11091801:11974521:8882489011:11974711
/competitor-analysis B0CMJTSVRW US
/keyword-research wireless microphone US
```

或用自然语言：`用卖家精灵帮我分析 yoga mat 美国市场`。

---

## 10 个综合分析命令

| 命令 | 名称 | 核心工具 | 详见 |
|------|------|----------|------|
| `/product-research` | 智能选品助手 | `product_research` + `product_node` | `comprehensive/product-research.md` |
| `/market-analysis` | 市场全景分析 | `market_research` + 12 分布工具 | `comprehensive/market-analysis.md` |
| `/competitor-analysis` | 竞品深度拆解 | `asin_detail` + `traffic_keyword` | `comprehensive/competitor-analysis.md` |
| `/keyword-research` | 关键词选品研究 | `keyword_miner` + `keyword_research_trends` | `comprehensive/keyword-research.md` |
| `/listing-optimizer` | Listing 优化诊断 | `traffic_listing` + `keyword_order` | `comprehensive/listing-optimizer.md` |
| `/traffic-analysis` | 流量结构分析 | `traffic_source` + `traffic_keyword_stat` | `comprehensive/traffic-analysis.md` |
| `/opportunity-finder` | 蓝海机会挖掘 | `aba_research_trend` + `google_trend` | `comprehensive/opportunity-finder.md` |
| `/review-insights` | 买家评论洞察 | `review` + NLP | `comprehensive/review-insights.md` |
| `/pricing-strategy` | 定价策略分析 | `market_price_distribution` | `comprehensive/pricing-strategy.md` |
| `/ad-optimizer` | 广告投放优化 | `keyword_order` + `traffic_keyword` | `comprehensive/ad-optimizer.md` |

---

## 17 张战术选品策略卡

对话中直接引用策略名称触发，详见 `tactical/`。

| 类型 | 策略卡 | 筛选逻辑 | 文件 |
|------|--------|---------|------|
| 新品爆发 | 新品快速爆发 | 上架≤2月+月销≥300+Review≤100 | `new-product-burst.md` |
| 新品爆发 | 隐形爆款 | 上架≤3月+月销≥500+Review≤50 | `hidden-bestseller.md` |
| 关键词趋势 | ABA 高增长趋势词 | 近3月持续增长+点击不集中 | `aba-high-growth-trend.md` |
| 关键词趋势 | 流量分散关键词 | 搜索≥5000+集中度<50% | `low-monopoly-keyword.md` |
| 关键词趋势 | 标题密度漏洞 | 标题密度≤5 的长尾词 | `title-density-gap.md` |
| 产品缺陷 | 热销低评分产品 | 月销≥1000+评分≤4.2 | `hot-low-rating.md` |
| 产品缺陷 | 评论语义分析 | 差评 NLP 聚类→改良指南 | `review-sentiment.md` |
| 类目结构 | 低品牌垄断类目 | 品牌集中度<45% | `low-brand-monopoly.md` |
| 类目结构 | 高新品占比市场 | 新品占比>5%+新品仍出单 | `high-new-product-ratio.md` |
| 类目结构 | 高毛利轻小品 | FBA≤$4+毛利≥50% | `high-margin-lightweight.md` |
| 流量防伪 | 自然流量反查 | 自然流量占比>60% | `natural-traffic-audit.md` |
| 流量防伪 | 变体拆解模型 | 找未被覆盖的变体缺口 | `variant-gap-analysis.md` |
| 机会捕捉 | 本土溢价降维 | 美国卖家+高价+高销 | `local-premium-disruption.md` |
| 机会捕捉 | FBM 拦截 | FBM 发货+月销≥300 | `fbm-intercept.md` |
| 机会捕捉 | 低质量 Listing 高销量 | LQS≤60+月销≥400 | `poor-listing-winner.md` |
| 机会捕捉 | 高客单长尾 | 均价≥$80+搜索量适中 | `high-ticket-long-tail.md` |
| 机会捕捉 | 季节前置爆破 | 历史同期环比增长>100% | `seasonal-prepositioning.md` |

### 推荐组合链路

```
品类扫描   → 低品牌垄断 / 高新品占比             （找蓝海类目）
   ↓
关键词挖掘 → ABA增长词 / 流量分散词 / 标题密度漏洞 （找增长词）
   ↓
竞品锁定   → 新品爆发 / 隐形爆款 / 热销低评分      （找目标竞品）
   ↓
竞品验真   → 自然流量反查                        （流量防伪）
   ↓
痛点提炼   → 评论语义分析                        （产品改进）
   ↓
产品开发   → 变体拆解 + 高毛利轻小                （利润验证）
```

---

## MCP 工具与参数

43 个工具分 7 类，完整清单见 `reference/tools_index.md`。

| 分类 | 数量 | 代表工具 |
|------|------|---------|
| ASIN 分析 | 7 | `asin_detail`、`keepa_info`、`bsr_prediction` |
| 产品与竞争 | 3 | `product_research`、`competitor_lookup`、`product_node` |
| 关键词 | 4 | `keyword_miner`、`keyword_research`、`keyword_research_trends`、`keyword_order` |
| 流量 | 6 | `traffic_keyword`、`traffic_source`、`traffic_listing` |
| 市场研究 | 15 | `market_research` + 14 分布工具 |
| ABA/趋势 | 4 | `aba_research_weekly/monthly/trend`、`google_trend` |
| 评论 | 1 | `review` |
| 商标 | 4 | `trademark_list`、`trademark_detail` |

**参数传递有两种模式**（看 `required` 字段识别）：

- **扁平参数** `required=["marketplace","asin"]`：`{"marketplace":"US","asin":"B0XXXXX"}`
- **request 嵌套** `required=["request"]`：`{"request":{"marketplace":"US","nodeIdPath":"...","size":50}}`

**全局默认值**：`marketplace=US`、`matchType=2`（模糊）、`size=50`（最大 100）。
支持站点：US/JP/UK/DE/FR/IT/ES/CA/IN。

---

## 输出格式

所有分析**必须保存为文件**（禁止只打印到控制台），输出到 `{类别名}/` 目录：

| 文件 | 必存 | 说明 |
|------|:----:|------|
| `{类别名}/market_report.md` | ✅ | 结构化 Markdown 报告 |
| `{类别名}/market_report.html` | ✅ | MD 完成后自动生成的可视化版（Canvas 图表） |
| `{类别名}/research_data.json` | ✅ | API 原始返回，供二次分析 |

报告四要素：**筛选口径 → KPI 摘要 → 多维分析 → 分级结论**。

---

## 脚本（可选，非必需）

`scripts/` 是作者跑真实案例时留下的示例/工具脚本，**不是技能的必需组件**。日常使用直接下命令即可，无需跑脚本。

```bash
export SELLERSPRITE_SECRET_KEY="你的密钥"
export SELLERSPRITE_OUT_DIR="./output"          # 默认 scripts/output/

python3 scripts/mcp_call.py product_node '{"request":{"marketplace":"US","keyword":"yoga mat"}}'
python3 scripts/lavalier_analysis.py        # 类目全景
python3 scripts/keyword_research.py         # 关键词研究
python3 scripts/competitor_analysis.py      # 竞品拆解
python3 scripts/review_analysis.py          # 评论分析
python3 scripts/tactical_strategies.py      # 战术卡批量跑
python3 scripts/dji_pocket3_analysis.py     # DJI Pocket 3 综合案例
```

环境变量：`SELLERSPRITE_SECRET_KEY`、`SELLERSPRITE_MCP_URL`、`SELLERSPRITE_OUT_DIR`、`SELLERSPRITE_NODE_ID`、`SELLERSPRITE_ASIN`。

---

## ⚠️ 已知限制与字段陷阱

### 工具行为缺陷
- **`review` 每次最多返回 20 条**（size 无效）→ 报告须注明“仅 20 条样本”
- **`traffic_source` 可能返回无关产品流量** → 用 `traffic_keyword` + `traffic_keyword_stat` 交叉验证
- **`keyword_research` 忽略 `keyword` 参数** → 改用 `keyword_miner`
- **`traffic_source` / `traffic_keyword_stat` 要用扁平参数**，不是 request 嵌套
- **`product_node` 的 `nodeLabelLocale`（中文）经常翻译错** → 以 `nodeLabelPath` 英文为准
- **必须用叶子节点**，父节点返回全 0

### 字段改名（网页 API → MCP 响应）
`totalUnits→units`、`totalAmount→revenue`、`reviews→ratings`、`bsrRank→bsr`、`sellerType→fulfillment`

### 刻度换算
- 集中度字段（`top*Crn`/`l*NewRatio`）：0~1，展示需 ×100
- `returnRatio`/`*Proportion`：已是百分数，不再换算
- `purchaseRate`/`monopolyClickRate`：0~1，展示 ×100
- `supplyDemandRatio`：真实比值，不换算

### 实际字段 vs 文档
| 工具 | 别用 | 要用 |
|------|------|------|
| `keyword_research_trends` | `month`/`searches`/`growth` | `time`/`search`/`chainGrowth`/`yearlyGrowth` |
| `aba_research_trend` | `clickShareRate`/`conversionShareRate` | `label`/`searches`/`rank` |
| `traffic_keyword_stat` | `searchTotal`/`naturalTotal` | `keywords`/`ranks`/`ads` |
| `review` | `body`/`createTime` | `content`/`date` |
| `keepa_info` | `currentPrice`/`bsrHistory` | `buyBox`/`bsr`（`[{timePoint,value}]`） |

### 常见报错
| 现象 | 解决 |
|------|------|
| `secret_invalid` | header 名必须是 `secret-key` |
| 数据全 0 | 用了根节点，换叶子节点 `nodeIdPath` |
| `q is required` | `traffic_source` 改用扁平参数 + `"q":"asin"` |
| 报告全是 N/A | 字段名用错，查上面的字段表 |
| 出现 `&amp;` | 用 `html.unescape()` 解码 |

---

## License

MIT
