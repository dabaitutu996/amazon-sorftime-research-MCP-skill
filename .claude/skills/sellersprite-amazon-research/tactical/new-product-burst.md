# 新品快速爆发

找上架不久但销量已快速攀升的新品。

## 工具
`mcp__sellersprite__product_research` — `{"request": {...}}`

## 筛选参数
- `minUnits`: 300, `maxRatings`: 100, `minUnitsCr`: 正增长
- 按 `units` 降序

## ⚠️ 上架时间处理
`product_research` **没有上架时间筛选参数**，所以"上架≤2月"这一条**无法用请求参数实现**。
正确做法：拉回数据后在**客户端过滤**响应里的上架日期字段（字段名以实际响应为准）。
若响应中确实没有上架日期字段，则用 `minUnitsCr`（月销量正增长）+ `maxRatings`（评论少）作为"新品"的近似信号，并在报告中**明确注明该限制**。
市场级的上架时间分布可另用 `market_listing_date_distribution` 佐证。

## 逻辑
上架≤2月 + 月销量≥300 + Review≤100 → 已验证市场需求，评论门槛低。

## 输出
新品 ASIN 列表 + 销量/价格/评分/BSR 对比 + 进入可行性分析

## 注意
响应字段用 `units`(销量)、`revenue`(销售额)、`ratings`(评分)
