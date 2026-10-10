# 低质量 Listing 高销量

找 Listing 质量差但销量高的商品（优化即可超越）。

## 工具
`mcp__sellersprite__product_research` — `{"request": {...}}`

## 筛选
- `minUnits`: 400（月销量 ≥400）
- 客户端过滤 `lqs <= 60`（Listing 质量分 ≤60）

## ⚠️ 参数陷阱
**不要用 `minLqs: 60`** —— 该参数语义是"**最低**质量分"（≥60），与本卡"找低质量 Listing"的意图**完全相反**，会把高质量 Listing 筛出来。
正确做法：拉回数据后在客户端过滤 LQS 字段（`lqs`，部分场景为 `listingQualityScore`）。
若响应中无 LQS 字段，则退化为仅用 `minUnits` + 低评分/差主图等替代信号，并在报告中注明该限制。

## 输出
候选 ASIN + LQS 各维度分 + 优化建议
