---
name: regen-prototype-html
description: |
  将功能架构描述或架构图转化为中保真 HTML 原型（介于线框图和高保真之间）。采用基座+场景按需加载架构，Notion 暖灰基座，根据场景自动叠加补充设计规范：仪表盘/表单→Stripe、排程→Cal.com、文档→Mintlify、营销→Apple。触发条件：用户提供功能架构/架构图并要求生成中保真原型、mid-fi prototype、评审用可点 demo 时使用。
argument-hint: [场景类型]
allowed-tools: Read Write Bash(mkdir *) Bash(ls *)
---

# 中保真原型生成器

## 一句话定义

**Mid-fi = 真实品牌 token（颜色/字体/间距）+ 简化交互（无真实图片、无 JS 动画、静态路由）。** 目标是评审视觉方向与信息架构，不替代视觉稿。

## 架构

采用「基座 + 场景按需加载」架构。仅亮色主题。基座提供通用骨架（布局、字体、间距、底色），场景层叠加特定页面类型的皮肤（强调色、阴影、组件变体）。

### 基座

| 品牌 | 文件 | 特征 |
|------|------|------|
| Notion | `${CLAUDE_SKILL_DIR}/brands/base-notion.md` | 暖灰底色、超细边框、四级字重、亲和力强 |

### 场景品牌映射

| 场景类型 | 品牌 | 文件 |
|----------|------|------|
| dashboard | Stripe | `${CLAUDE_SKILL_DIR}/brands/scene-stripe.md` |
| form | Stripe | `${CLAUDE_SKILL_DIR}/brands/scene-stripe.md` |
| schedule | Cal.com | `${CLAUDE_SKILL_DIR}/brands/scene-cal.md` |
| docs | Mintlify | `${CLAUDE_SKILL_DIR}/brands/scene-mintlify.md` |
| landing | Apple | `${CLAUDE_SKILL_DIR}/brands/scene-apple.md` |
| general | 无（仅使用 Notion 基座） | — |

## Font Stack（全局）

```css
font-family: -apple-system, BlinkMacSystemFont, "PingFang SC", "Microsoft YaHei", Inter, Arial, sans-serif;
```

中英文兼容，**不需要 @import**。
Mono 场景统一用 `ui-monospace, "SF Mono", Menlo, Consolas, monospace`。

## Scene Detection

当用户未显式指定场景类型时，通过信号词自动检测：

| Scene | 信号词 | 补充品牌 |
|-------|--------|----------|
| **dashboard** | metrics, charts, tables, analytics, KPIs, 后台, 数据, 仪表盘, 患者统计, 科室报表 | Stripe |
| **form** | checkout, payment, auth, login, signup, 结账, 登录, 表单, 工作流, 病历录入, 处方, 挂号 | Stripe |
| **schedule** | calendar, booking, appointment, 排程, 预约, 排班, 门诊安排, 手术排期 | Cal.com |
| **docs** | documentation, content, articles, guides, 文档, 知识库, 帮助中心, 临床指南, 用药说明 | Mintlify |
| **landing** | landing page, 落地页, 营销, 首页, hero, 官网 | Apple |
| **general** | 其他 | Notion 基座兜底 |

用户可显式指定：`/prototype dashboard`、`/prototype schedule` 等。

## 参数解析

从 `$ARGUMENTS` 中解析场景类型参数：

**场景类型**（可选，可通过信号词自动检测）：`dashboard` | `form` | `schedule` | `docs` | `landing`

示例调用：
- `/prototype dashboard` → 数据面板
- `/prototype schedule` → 排程/预约页
- `/prototype` → 根据用户后续输入的架构描述自动检测场景

**优先级：显式参数 > 信号词自动检测 > AskUserQuestion**

如果用户通过参数指定了场景类型，即使架构描述中包含其他场景的信号词，也以参数为准。仅当参数缺失且信号词无法判断时，使用 `AskUserQuestion` 让用户选择。

## Viewport by Product Type

| 产品类型 | 视口 |
|----------|------|
| B 端后台 | 1440×900 |
| Web 网站 | 1440×900 |
| Mobile App | 375×812 |
| 小程序 | 375×667 |
| iPad（横屏 SplitView）| 1024×768 |
| iPad（竖屏）| 768×1024 |

---

## 执行流程

### 第 1 步：确认参数

先用 `AskUserQuestion` 确认（用户未明示时）：
- **产品类型**（决定视口与导航）：B 端后台 / Web 网站 / Mobile App / 小程序 / iPad 横屏 / iPad 竖屏
- **单文件 vs 多文件**：默认单文件；架构超 5 页建议多文件 + `index.html` 入口

### 第 2 步：加载样式

1. 读取基座文件 `base-notion.md`
2. 根据场景类型读取对应的场景文件；如果场景为 general 则跳过，仅使用基座

声明检测到的场景与补充规范（一行，例如：「场景=dashboard，补充=Stripe」）

从品牌文件中提取以下设计 token：
- 颜色系统（背景、文字、边框、强调色）
- 字体系统（字族、字重、字号层级）
- 间距系统（基数、常用值）
- 圆角系统
- 阴影/层级系统
- 组件样式（按钮、卡片、输入框等）

### 第 3 步：理解输入

用户会提供以下一种或多种输入：
- 功能架构图（截图或文字描述）
- 页面功能列表
- 信息架构 / 导航结构
- 参考截图

分析输入，识别出：
- 页面需要哪些区域（导航、侧边栏、主内容区、卡片网格等）
- 每个区域包含哪些组件（表格、图表、表单、列表等）
- 信息层级和优先级

### 第 4 步：生成原型

生成 HTML 原型，遵循以下原则：

**Token 优先级：场景品牌文件中明确定义的 token 覆盖基座，其余回退到基座。**

| Token 类别 | 场景文件有定义时 | 场景文件无定义时 |
|-----------|----------------|----------------|
| 强调色/品牌色 | 用场景 | 用基座 |
| 阴影/层级 | 用场景 | 用基座 |
| 边框色/样式 | 用场景 | 用基座 |
| 组件样式（按钮、卡片、输入框） | 用场景 | 用基座 |
| 背景色/文字色 | 用基座 | — |
| 字体/字重/字号层级 | 用基座 | — |
| 间距系统 | 用基座 | — |
| 布局网格 | 用基座 | — |

简言之：场景管「皮肤」（颜色、阴影、组件变体），基座管「骨架」（布局、字体、间距、底色）。

**Layout Patterns by Scene：**
- **Dashboard**：侧栏 240 + 主区；顶部 3–4 个指标卡；下方数据表或图表
- **Form**：居中容器 max 480（单步）/ 640（多步）；多步时带进度指示；label 在 input 上方
- **Schedule**：左侧日期导航 + 右侧时间网格；或日/周/月视图切换；时间槽可点击
- **Docs**：左导航 260 + 正文 max 720 + 右 TOC 200（可选）；顶部面包屑
- **Landing**：全宽 section，居中内容，hero + feature blocks + CTA
- **General**：按架构描述自由布局

### 第 5 步：输出

保存文件：
- **单文件**：`prototype-[feature-name].html`
- **多文件**：`prototype-[feature-name]/` 目录下每页一个 HTML；`index.html` 作为入口列出所有页面链接；**页间跳转用 `<a href="xxx.html">` 直跳**

一行总结 + 提示用户「在浏览器中打开 [文件路径] 查看」

---

## HTML Generation Rules

### Structure
单文件 self-contained HTML：
- 所有 CSS 放在 `<style>` 内
- 系统字体栈，无需 @import
- 语义化 HTML5
- 静态布局无需 JS；如需路由/交互可用少量 JS（hash 路由或 `data-action`）

### CSS Base
```css
*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
:root {
  /* Notion 基座默认值 — 场景激活时用场景 token 覆盖对应变量 */
  --page-bg: #ffffff;
  --surface: #f6f5f4;
  --text-primary: rgba(0,0,0,0.95);
  --text-secondary: rgba(0,0,0,0.65);
  --text-muted: rgba(0,0,0,0.45);
  --accent: #0075de;
  --border: rgba(0,0,0,0.1);
  --border-subtle: rgba(0,0,0,0.06);
  --radius: 8px;
  --shadow: rgba(0,0,0,0.02) 0 1px 1px, rgba(0,0,0,0.03) 0 2px 4px;
  /* 场景覆盖示例：dashboard 激活时 --accent 变为 Stripe #533afd，--shadow 变为 Stripe 蓝调阴影 */
}
body {
  font-family: -apple-system, BlinkMacSystemFont, "PingFang SC", "Microsoft YaHei", Inter, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  background: var(--page-bg);
  color: var(--text-primary);
  line-height: 1.5;
}
```

生成时根据场景用品牌文件中的实际 token 值覆盖对应变量，未被覆盖的变量保留 Notion 基座默认值。

### DO
- 使用品牌文件中的确切 hex / rgba token
- 占位内容贴合用户领域（见下方占位内容规范）
- 导航、侧栏、内容区按真实比例渲染
- 表格/卡片填 3–5 行真实感数据
- 用 CSS Grid/Flexbox
- 768px 断点做基础响应
- 间距严格 8px 网格

### DON'T
- 用真实图片（用纯色 `<div>` 或内联 SVG 占位）
- 实现真实功能逻辑
- 加复杂动画
- 超出架构描述增加功能
- 引外部 icon 库（用内联 SVG 或 Unicode）
- 引外部字体（Google Fonts 等）
- 使用 Lorem ipsum 或 Item 1/2/3 等无意义占位

---

## 占位内容规范

❌ 禁止：`Item 1 / Item 2`、`Lorem ipsum`、`User A`、`xxxxx`、`测试数据`
✅ 要求：贴合用户所在领域的真实感内容

通用示例：
- 报表标题：`Q3 收入分析报告`、`用户增长趋势`
- 用户信息：`zhang.wei@acme.com`、`张维 · 产品经理`
- 工单编号：`#1842 退款工单`、`#2091 权限申请`
- 指标数据：`¥128,450`、`+12.3%`、`1,024 活跃用户`
- 导航项：使用与场景匹配的真实功能名称

如果用户指定了行业领域，占位内容必须贴合该领域。例如医疗领域：
- 患者信息：`张明华 · 男 · 58岁`、`病历号 MR-2024-08172`
- 科室导航：`门诊管理`、`住院部`、`检验中心`、`药房管理`
- 指标数据：`今日门诊 328 人次`、`床位使用率 87.2%`
- 检验结果：`血糖 6.8 mmol/L ↑`、`血压 135/85 mmHg`
- 预约信息：`王医生 · 心内科 · 周三上午 09:30`
- 费用数据：`¥1,280.00`、`医保报销 ¥896.00`

---

## Quality Checklist

生成前逐项核对：

- [ ] 亮色主题
- [ ] 字体栈使用系统栈（中英文兼容），未误引 Google Fonts
- [ ] 间距严格 8px 网格，无 13 / 17 / 23 等杂值
- [ ] 圆角符合对应品牌文件中的 token
- [ ] 场景 token 单一，未混用其他场景颜色
- [ ] 占位内容领域相关（无 "Item 1" / "Lorem ipsum" / "User A"）
- [ ] 强调色克制使用（品牌强调色仅用于 CTA 和关键交互，不泛滥）
- [ ] 金额/数字字段加 `font-variant-numeric: tabular-nums`（所有包含数值的场景均需添加）
- [ ] 正文对比度达 WCAG AA（正文 ≥4.5:1，UI 大字号 ≥3:1）
- [ ] 指定视口下无横向滚动、组件不溢出
- [ ] 没有真实图片链接（全部用 `<div>` 或内联 SVG）
- [ ] 颜色、阴影、圆角严格来自品牌文件，未自行发明
