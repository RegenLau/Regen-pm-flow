# Regen-pm-flow

产品经理 AI 工作流 Skill 集合，覆盖「客户材料 → HTML 原型 → PRD → 交付」全链路。

## 使用原则

1. 四个核心 skill 都是独立入口，任意一个都可以直接响应用户请求，不依赖其他 skill 先执行。
2. 一次请求只选一个主 skill。其他 skill 只作为前置背景、后置校验或下一步建议，不要自动连环调用。
3. skill 之间通过工件衔接，而不是通过硬编码依赖衔接。这里的工件包括：竞品洞察、结构化摘要、HTML 原型、PRD、评审结论。
4. `regen-material2proto`、`regen-proto2prd` 是生产型 skill，负责生成正式产物；`regen-review-board`、`regen-competitor-deconstructor` 是辅助型 skill，负责提供判断、约束和修改建议。
5. 每个 skill 在结束时只输出当前产物，并给出“推荐下一步”，不要擅自继续执行下一个 skill。

## 标准执行流

### 主链路

`regen-material2proto` → `regen-review-board`（可选，评原型） → `regen-proto2prd` → `regen-review-board`（推荐，评 PRD）

### 策略前置链路

`regen-competitor-deconstructor` → `regen-material2proto` 或 `regen-proto2prd`

### 快速场景

- 只有原始材料，要先出页面：`regen-material2proto`
- 已有 HTML 原型，要补 PRD：`regen-proto2prd`
- 想看方案有没有问题：`regen-review-board`
- 想先看竞品和差异化：`regen-competitor-deconstructor`

## 工件流转表

| Skill | 主要输入 | 主要输出 | 推荐下一步 |
|-------|---------|---------|-----------|
| `regen-competitor-deconstructor` | 竞品名单、分析目标、我方产品背景 | 竞品洞察、可借鉴点、差异化建议 | `regen-material2proto` 或 `regen-proto2prd` |
| `regen-material2proto` | 原始材料、会议纪要、聊天记录、需求描述 | 结构化摘要、待确认事项、HTML 原型 | `regen-review-board` 或 `regen-proto2prd` |
| `regen-proto2prd` | 已定稿 HTML 原型、原始材料 | 结构化 PRD、待确认事项 | `regen-review-board` |
| `regen-review-board` | 原始材料、HTML 原型、PRD | 阻断项、重要项、建议项、复评清单 | 回到对应的生产型 skill 修改 |

## 核心工作流

| Skill | 用途 |
|-------|------|
| `regen-material2proto` | 会议逐字稿 / 微信聊天 / 客户文档 → 可交互 HTML 原型 |
| `regen-proto2prd` | 定稿原型 → 结构化 PRD 交付技术团队 |
| `regen-prd-writer` | 从零撰写 PRD，支持传统应用 / AI 增强 / AI Agent 三种模板 |

## 辅助工具

| Skill | 用途 |
|-------|------|
| `space-image2proto` | 截图 / 设计稿 → HTML 原型，带设计体系记忆 |
| `space-url2proto` | 参考网站 → 本地 Next.js 原型工程 |
| `regen-review-board` | 6 角色模拟 PRD 评审（产品/研发/测试/设计/运营/法务） |
| `regen-competitor-deconstructor` | 四维竞品拆解（策略/功能/体验/增长） |
| `pm-image2pencil` | 截图 → Pencil 设计稿 + 结构化设计文档 |

## 配套工具

| 工具 | 类型 | 用途 |
|------|------|------|
| [Web to Figma](https://gwrdluzl9j9.feishu.cn/wiki/TTibw3e8niWpvmkfSIWc8bUmnJc) | Chrome 插件 | 将网页直接转为 Figma 设计稿，配合原型迭代使用 |
| [Liaison](https://chromewebstore.google.com/detail/liaison-ai-%E7%BC%96%E7%A8%8B%E5%AE%9A%E4%BD%8D%E3%80%81%E7%BD%91%E9%A1%B5%E6%A0%B7%E5%BC%8F%E7%BC%96%E8%BE%91%E4%B8%8E%E6%89%B9%E6%B3%A8/keeeahbnkkbengbjmmblmpgbccjeoebf) | Chrome 插件 | 网页样式可视化编辑与批注，导出结构化 Prompt 给 AI 开发协作 |

## 使用方式

将本仓库放置在工作目录，Cursor / Claude Code 会自动识别 `.claude/skills/` 下的 Skill 文件。
