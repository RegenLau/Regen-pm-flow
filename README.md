# Regen-pm-flow

面向产品经理的 AI 工作流 Skill 集合，围绕「策略判断 → 原型生成 → PRD 交付 → 正式评审」组织，并支持”虚拟同事”作为上层协作能力接入。

当前仓库维护的是 **本地 `.claude/skills` 下的 6 个核心 skill**，它们都可以独立作为入口使用，但推荐按工件流转组织成完整 workflow。

## 当前本地 Skills

| Skill | 类型 | 用途 |
|-------|------|------|
| `regen-material2proto` | 生产型 | 会议纪要 / 微信聊天 / 客户文档 → 可交互 HTML 高保真原型 |
| `regen-prototype-html` | 生产型 | 功能架构描述 / 架构图 → 中保真 HTML 原型（Notion 暖灰基座 + 场景设计规范） |
| `regen-proto2prd` | 生产型 | 定稿 HTML 原型 → 结构化 PRD |
| `regen-review-board` | 辅助型 | 多角色评审 PRD / 原型 / 方案，输出阻断项、重要项、建议项 |
| `regen-competitor-deconstructor` | 辅助型 | 做竞品拆解，输出差异化建议和行动清单 |
| `regen-virtual-colleagues` | 协作型 | 为项目或话题绑定虚拟同事团队，在关键节点提供建议、发起圆桌讨论、沉淀工件 |

## 工作流原则

1. 每个 skill 都是独立入口，不依赖其他 skill 先执行。
2. 一次请求只选一个主 skill。其他 skill 只作为前置背景、后置校验或下一步建议。
3. skill 之间通过工件衔接，而不是硬编码串联。
4. `regen-material2proto`、`regen-proto2prd` 负责正式产物；`regen-review-board`、`regen-competitor-deconstructor` 负责判断和约束；`regen-virtual-colleagues` 负责项目级协作。
5. 虚拟同事是可选增强层。没启用时，不影响主 workflow 正常使用。

## 推荐工作流

### 1. 标准主链路

`regen-material2proto` → `regen-review-board`（可选，评原型） → `regen-proto2prd` → `regen-review-board`（推荐，评 PRD）

适用场景：
- 已有原始材料，希望快速出页面并最终交付研发

### 2. 策略前置链路

`regen-competitor-deconstructor` → `regen-material2proto` 或 `regen-proto2prd`

适用场景：
- 方向还没完全定，需要先看竞品、差异化和可借鉴点

### 3. 虚拟同事增强链路

`regen-virtual-colleagues` → 任一主 skill

推荐接法：
- 先用 `regen-virtual-colleagues` 给项目或话题绑定虚拟团队
- 再进入 `regen-material2proto` / `regen-proto2prd` / `regen-review-board` / `regen-competitor-deconstructor`
- 主 skill 在关键节点读取虚拟团队配置和意见工件，做“先讨论再执行”或“确认前提醒”

适用场景：
- 希望项目有持续陪跑的虚拟同事
- 想围绕某个话题做圆桌讨论
- 想把建议沉淀成项目工件，而不是一次性聊天

## 快速入口

- 只有原始材料，要先出页面：`regen-material2proto`
- 有功能架构，要出中保真原型：`regen-prototype-html`
- 已有 HTML 原型，要补 PRD：`regen-proto2prd`
- 想正式评审方案：`regen-review-board`
- 想先看竞品和差异化：`regen-competitor-deconstructor`
- 想给项目配虚拟同事或开圆桌：`regen-virtual-colleagues`

## 工件流转表

| Skill | 主要输入 | 主要输出 | 推荐下一步 |
|-------|---------|---------|-----------|
| `regen-virtual-colleagues` | 项目背景、话题、行业、团队配置意图 | 虚拟团队配置、同事建议、圆桌纪要、协作工件 | 回到对应主 skill 继续 |
| `regen-competitor-deconstructor` | 竞品名单、分析目标、我方产品背景 | 竞品洞察、可借鉴点、差异化建议 | `regen-material2proto` 或 `regen-proto2prd` |
| `regen-material2proto` | 原始材料、会议纪要、聊天记录、需求描述 | 结构化摘要、待确认事项、HTML 高保真原型 | `regen-review-board` 或 `regen-proto2prd` |
| `regen-prototype-html` | 功能架构描述、架构图 | 中保真 HTML 原型 | `regen-review-board` 或 `regen-proto2prd` |
| `regen-proto2prd` | 已定稿 HTML 原型、原始材料 | 结构化 PRD、待确认事项 | `regen-review-board` |
| `regen-review-board` | 原始材料、HTML 原型、PRD | 阻断项、重要项、建议项、复评清单 | 回到对应生产型 skill 修改 |

## 虚拟同事接入方式

`regen-virtual-colleagues` 是当前 workflow 的上层协作层，不替代主 skill。

### 作用

- 给项目或话题绑定“真实行业人物映射”的虚拟同事
- 在关键节点给建议
- 支持切换“高频陪跑模式”
- 支持围绕某个议题发起圆桌
- 把意见落盘为 `json + md` 工件

### 作用域

- **项目级团队**：长期陪跑，跨多个主 skill 共享
- **话题级团队**：围绕某个具体问题临时组队

### 生效优先级

`话题级 > 项目级 > 系统推荐`

### 主 skill 读取规则

如果项目或话题已启用虚拟同事，主 skill 按以下顺序读取配置：

1. `.regen/topics/<topic-id>/virtual-team.json`
2. `.regen/virtual-teams/project-team.json`

并读取相关意见工件作为背景输入，但不把它们当成正式产物结论。

## 目录与存储

### Skills 目录

本仓库的本地 skill 存放在：

```text
.claude/skills/
```

### 虚拟同事工件目录

建议项目内使用：

```text
project/
  .regen/
    virtual-teams/
      project-team.json
      registry.json
    topics/
      <topic-id>/
        virtual-team.json
        opinions/
        roundtables/
```

## 使用方式

1. 将本仓库放在工作目录中
2. 确保本地 skill 位于 `.claude/skills/`
3. 直接根据任务选择入口 skill
4. 如果需要项目级协作，先启用 `regen-virtual-colleagues`
5. 按工件流转继续进入对应主 skill

## 当前 README 对齐范围

本 README 只描述当前仓库内已经存在并维护的本地 skill 和它们之间的工作流关系，不再列出未在本仓库 `.claude/skills` 中维护的其他外部或全局 skill。
