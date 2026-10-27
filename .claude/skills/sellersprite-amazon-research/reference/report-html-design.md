# HTML 报告设计系统（`market_report.html`）

> 目标：让每次生成的报告**视觉一致、可读、可打印成 PDF**，且**零外部依赖、单文件、离线可用**。
> 设计原则借鉴业界报告/编辑式规范（guizang-ppt-skill、executive-market-research、minimalist-ui 的**方法**，不含其代码；本仓库保持 MIT）。

## 1. 设计原则（违反即不合格）

1. **信息优先** — 报告是数据载体，装饰让位于表格与图表的可读性。
2. **克制** — 无渐变、无重阴影、无 3D/玻璃拟态、无 emoji。
3. **单一锚点色** — 一份报告只用一个强调色（accent），用于 KPI、图表主系列、关键结论。
4. **字体对比定义层级** — 大号衬线标题 + 无衬线正文 + 等宽数字/元数据。
5. **留白与网格** — 内容宽度受限，区块之间大间距，以左对齐为主。
6. **语义化颜色** — 颜色只表达含义（推荐=绿 / 谨慎=黄 / 不推荐=红 / 强调=锚点色）。
7. **数据警告可见** — 有 ⚠️ 的表格/结论必须在视觉上可辨识（左侧色条或浅色底）。

## 2. 主题 Token（粘贴到 `<style>` 顶部）

```css
:root{
  /* 中性色 */
  color-scheme:light;
  --bg:#ffffff; --surface:#fafafa; --surface-2:#f0f0ee;
  --border:#d4d4d2;
  --text:#202124; --text-muted:#555555; --text-faint:#606060;
  /* 锚点色（每份报告只选一个） */
  --accent:#0033a0; --accent-ink:#ffffff;
  /* 图表色板（顺序取用，勿随意增删） */
  --c1:var(--accent); --c2:#7c3aed; --c3:#15803d; --c4:#b45309; --c5:#b91c1c; --c6:#0f766e;
  /* 语义色 */
  --ok:#15803d; --warn:#b45309; --bad:#b91c1c;
  /* 字体（系统栈，离线可用） */
  --serif:Georgia,"Songti SC","Noto Serif SC","Source Han Serif SC",serif;
  --sans:-apple-system,BlinkMacSystemFont,"Segoe UI","PingFang SC","Microsoft YaHei","Noto Sans SC",sans-serif;
  --mono:ui-monospace,SFMono-Regular,Menlo,Consolas,"Liberation Mono",monospace;
  /* 尺度 */
  --maxw:1440px; --radius:10px; --gap:32px;
}
```

## 3. 配色预设（三选一，替换中性色/锚点色即可）

| 预设 | 适用 | --bg / --surface | --text | --accent |
|------|------|------------------|--------|----------|
| **A 墨黑专业**（仅明确要求深色时使用） | 深色演示 | `#0f1115` / `#171a21` | `#e8eaed` | `#4c9aff` |
| **B 米白编辑** | 打印/汇报、长阅读 | `#f7f6f3` / `#ffffff` | `#1f1f23` | `#c9a45a` |
| **C 瑞士极简**（默认） | 电脑端数据报告、长阅读 | `#ffffff` / `#fafafa` | `#202124` | `#0033a0` |

> 默认浅色 token 已在上方完整给出；全文保持浅色，不交替黑底区块，不跟随系统暗色模式。切换深色须同步调整全部中性色与图表标签，不能仅替换背景。

## 4. 字体配对

- 标题（h1/h2）：`var(--serif)`，字重 700，`letter-spacing:-0.01em`，`line-height:1.15`
- 正文：`var(--sans)`，`font-size:16px`、`line-height:1.7`；正文段落 `max-width:72ch`，中文长段落可限制为 `40em`
- 表格：`var(--sans)`，`font-size:14px` 至 `15px`；不要用正文行长限制表格或图表
- 数字/百分比/元数据：`var(--mono)`（KPI 数值、BSR、ASIN、价格）

## 5. 组件规范

### 5.1 KPI 卡
- 网格：`grid-template-columns:repeat(auto-fit,minmax(160px,1fr))`
- 结构：小号 mono 标签 + 大号 mono 数值 + 可选同比/环比
- 背景 `--surface`、`1px solid var(--border)`、圆角 `--radius`、**无阴影**

### 5.2 数据表
- `width:100%`；表头 `--surface-2`；行分隔仅用 `1px solid var(--border)`
- 数字列右对齐 + mono；文本列左对齐
- 千分位 / 占比 1 位小数 / 价格 `$X.XX`（同 SKILL.md §8.4）
- 超 ~12 行加斑马纹（`:nth-child(even)`）或滚动容器

### 5.3 Canvas 图表
- 取色按 `--c1..--c6`，单图系列 ≤5
- 网格线 `--border`，轴标签 `--text-faint`，数值标签 mono
- 必带标题 + 单位 + 数据来源脚注；趋势图带坐标轴
- 坐标轴、关键词及数值标签至少 14px；调整左侧标签区域和图表高度，避免关键词或数值截断、重叠；重绘时读取主题 token，避免硬编码深色主题文字
- 按 `devicePixelRatio` 缩放，避免模糊

### 5.4 标注 / 分级
- ⚠️ 数据警告：左侧 `3px solid var(--warn)` 色条 + `rgba(245,158,11,.08)` 底
- 结论分级 pill：推荐=`--ok` / 谨慎=`--warn` / 不推荐=`--bad`

## 6. 电脑端布局

- 主内容 `max-width:var(--maxw); margin:0 auto; padding:var(--gap)`
- 使用 `box-sizing:border-box`；嵌入综合报告时，模块 `main` 使用 `max-width:none; width:100%`，避免外层加宽后仍被内部 1080px 限制
- 表格、图表和 KPI 占满可用内容宽度，正文段落单独限制行长；长表格允许局部横向滚动
- 区块间距 ≥ `var(--gap)`；用留白或 `1px solid var(--border)` 分隔，**不套卡片**
- 面向电脑端，优先检查 1280px、1440px、1920px 窗口；无需新增手机专用布局或缩小电脑端字号
- 报告 >8 屏时建议加锚点目录

## 7. 动效（克制）

- 仅 `IntersectionObserver` 滚动淡入（`opacity` + `translateY(12px)`，≤600ms）
- 尊重 `@media (prefers-reduced-motion: reduce)` → 关闭全部动效
- **禁止**：滚动视差、背景光斑、逐卡 hover 抬升、每段 fade-slide（AI 味最重）

## 8. Print / PDF 样式

```css
html{ -webkit-print-color-adjust:exact; print-color-adjust:exact; }
@media print{
  :root{ --bg:#fff; --surface:#fff; --surface-2:#f0f0ee; --text:#111; --text-muted:#555; --text-faint:#606060; --border:#d4d4d2; }
  .no-print{ display:none!important; }
  table,.kpi{ break-inside:avoid; }
  h2,h3{ break-after:avoid; }
  canvas{ max-width:100%; }
  @page{ margin:14mm; }
}
```

> 报告完成后，浏览器 `Ctrl/Cmd+P → 另存为 PDF` 即可得到不丢色的 PDF。

## 9. Do / Don't

| ❌ 禁止 | ✅ 替代 |
|--------|--------|
| 渐变背景、霓虹色、玻璃拟态 | 纯色 + 1px 边框 |
| 重阴影 | 无阴影或 `0 1px 2px rgba(0,0,0,.06)` |
| emoji 当图标 | 内联 SVG 或纯文字标记 |
| 彩虹高亮、每段不同色 | 单一锚点色 + 语义色 |
| 每段 fade-slide-up、每卡 hover 抬升 | 一次性淡入或不动 |
| 纯黑 `#000` / 纯白 `#fff` 文字 | `--text` / `--bg` token |
| 中英混排无空格、术语乱翻 | 术语统一，中英之间留空格 |

## 10. 生成后自检（对应 SKILL.md §8.5）

- [ ] 只用 1 个锚点色；图表系列来自 `--c1..--c6`
- [ ] 无渐变 / 重阴影 / emoji
- [ ] 数字列 mono + 右对齐；千分位/小数位符合 §8.4
- [ ] ⚠️ 警告项有可见色条/底色
- [ ] 含 `print-color-adjust` 与 `@media print`
- [ ] 1280px、1440px、1920px 电脑窗口下内容随宽度伸缩，表格和图表无截断；正文、表格与图表标签字号可读
- [ ] 单文件、无外链字体/JS（离线可开）
