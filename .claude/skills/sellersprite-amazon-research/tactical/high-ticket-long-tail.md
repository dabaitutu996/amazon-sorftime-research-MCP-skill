# 高客单长尾

找均价≥$80 且搜索量适中的关键词（高利润长尾市场）。

## 工具
`mcp__sellersprite__keyword_miner` — `{"request": {...}}`

> ⚠️ **不要用 `keyword_research`** —— 该工具会忽略 `keyword` 参数、返回全局热词（见 `SKILL.md`「常见故障排查」）。
> 本卡需要的 `avgPrice` / `searches` 都是 `keyword_miner` 的响应字段。

## 筛选
- 客户端过滤 `avgPrice >= 80`
- `minSearch`: 500

## 输出
高客单关键词表 + 搜索量/供需比/竞价

## 空结果处理
客户端过滤后若**无匹配**，禁止编造数据：
1. 放宽阈值重试一次（如 `avgPrice >= 50` 或降低 `minSearch`），并在报告中注明"已放宽阈值"
2. 仍为空则如实报告"该条件下无匹配"，并列出实际返回的均价分布供参考
