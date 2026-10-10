# scripts/ — 卖家精灵 MCP 脚本

这些脚本是「真实跑一遍」的示例/工具脚本（不是 skill 的必需组件），调用卖家精灵 MCP 并生成 Markdown 报告。

> 原版脚本硬编码了 Windows 路径（`D:/sellersprite-skills/...`）和空密钥，在非 Windows 机器上跑不起来。
> 现已改为**全部走环境变量**，并保留原值为默认值，行为向后兼容。

## 配置（环境变量，仓库里不存密钥）

| 环境变量 | 用途 | 默认值 |
|---------|------|--------|
| `SELLERSPRITE_SECRET_KEY` | 卖家精灵 API 密钥（**必填**，否则 API 返回未授权） | 空 |
| `SELLERSPRITE_MCP_URL` | MCP endpoint | `https://mcp.sellersprite.com/mcp` |
| `SELLERSPRITE_OUT_DIR` | 报告 / 原始数据输出目录 | `scripts/output/` |
| `SELLERSPRITE_NODE_ID` | 类目节点路径（`lavalier_analysis.py`、`tactical_strategies.py` 用） | 无线领夹麦节点 |
| `SELLERSPRITE_ASIN` | 目标 ASIN（`competitor_analysis.py`、`review_analysis.py` 用） | `B0CMJTSVRW` |
| `SELLERSPRITE_PARENT_DATA` | 父类目数据文件（`tactical_strategies.py` 用） | `<OUT_DIR 同级>/wirelessinstruments/research_data.json` |

## 用法

```bash
export SELLERSPRITE_SECRET_KEY="你的密钥"
export SELLERSPRITE_OUT_DIR="./output"

# 会调 API 的脚本
python3 lavalier_analysis.py        # 类目全景
python3 keyword_research.py         # 关键词研究
python3 competitor_analysis.py      # 竞品拆解
python3 review_analysis.py          # 评论分析
python3 tactical_strategies.py      # 5 张战术卡（调 API + 读本地数据）
python3 dji_pocket3_analysis.py     # DJI Pocket 3 综合案例

# 纯本地脚本（只读上游产出的 json，不调 API）
python3 keyword_deep_analysis.py
python3 fix_keyword_report.py
```

## 通用调用助手

`mcp_call.py` 可当命令行工具单发一个工具调用：

```bash
SELLERSPRITE_SECRET_KEY=xxx python3 mcp_call.py product_node \
  '{"request":{"marketplace":"US","keyword":"yoga mat"}}'
```

## 注意

- 密钥**只从环境变量读**，不要写回代码或提交进仓库（`.env` / `.env.local` 已被 `.gitignore` 忽略）。
- 输出目录 `scripts/output/` 已加入 `.gitignore`。
- 部分脚本之间有**数据依赖**（如 `tactical_strategies.py` 读 `keyword_data.json` 和父类目的 `research_data.json`），需先跑上游脚本。
- 密钥通过 `secret-key` **请求头**传入（不是 URL 参数），见仓库根目录 `.mcp.json`。
