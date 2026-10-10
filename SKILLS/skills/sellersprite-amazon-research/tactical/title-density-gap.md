# 标题密度漏洞

找标题密度低的长尾词（竞品未优化的关键词）。

## 工具
`mcp__sellersprite__keyword_miner` — `{"request": {...}}`

## 筛选
- `minSearch`: 500
- 客户端过滤 `titleDensity <= 5`

## 输出
低标题密度词表 + 搜索量 + 建议埋词位置

## 注意
⚠️ **不要用 `keyword_research` 验证需求趋势** —— 该工具会忽略 `keyword` 参数（见 `skill.md` 常见故障排查）。
改用 `keyword_research_trends`（**扁平参数**，字段 `time`/`search`/`chainGrowth`/`yearlyGrowth`）。

## 空结果处理
客户端过滤后若**无匹配**，禁止编造数据：
1. 放宽阈值重试一次（如 `titleDensity <= 8` 或降低 `minSearch`），并在报告中注明"已放宽阈值"
2. 仍为空则如实报告"该条件下无匹配"，并列出实际返回的标题密度分布供参考
