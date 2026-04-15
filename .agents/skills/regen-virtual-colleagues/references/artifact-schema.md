# 虚拟同事工件 Schema

本文定义 `regen-virtual-colleagues` 在 V1 中落盘的工件结构。所有同事意见和圆桌结果都必须同时输出 `json + md` 两份。

## 一、设计目标

- `json` 供系统读取和其他主 skill 消费
- `md` 供用户阅读和回看
- 工件能区分项目级和话题级来源
- 工件能追溯到触发来源、触发阶段和输入工件

---

## 二、单条意见工件

建议路径：

```text
.regen/topics/<topic-id>/opinions/<role>/<timestamp>-<trigger>.json
.regen/topics/<topic-id>/opinions/<role>/<timestamp>-<trigger>.md
```

如果当前没有话题级上下文，也可以落到项目级：

```text
.regen/virtual-teams/opinions/<role>/<timestamp>-<trigger>.json
.regen/virtual-teams/opinions/<role>/<timestamp>-<trigger>.md
```

### JSON 字段

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `project_id` | string | 是 | 项目 ID |
| `topic_id` | string | 否 | 话题 ID |
| `agent_id` | string | 是 | 同事 ID |
| `persona_name` | string | 是 | 展示名称 |
| `mapped_real_person` | string | 是 | 真实人物映射 |
| `role` | string | 是 | 岗位 |
| `trigger_source` | string | 是 | 触发来源，如 `before_material2proto` |
| `trigger_stage` | string | 是 | `pre_execute` / `pre_confirm` / `post_review` |
| `input_artifact_refs` | array | 否 | 输入工件引用 |
| `opinion_summary` | string | 是 | 摘要结论 |
| `full_opinion` | string | 否 | 完整意见正文 |
| `action_items` | array | 否 | 建议动作列表 |
| `created_at` | string | 是 | ISO 时间 |

### JSON 示例

```json
{
  "project_id": "proj_ai_interview",
  "topic_id": "topic_mvp_scope",
  "agent_id": "frontend_gaearon",
  "persona_name": "Dan Abramov",
  "mapped_real_person": "Dan Abramov",
  "role": "frontend",
  "trigger_source": "before_material2proto",
  "trigger_stage": "pre_execute",
  "input_artifact_refs": [
    "materials/meeting-notes.md"
  ],
  "opinion_summary": "不建议首版引入高复杂度语音链路",
  "full_opinion": "从前端实现和交互稳定性看，首版先验证文本面试主流程更稳。",
  "action_items": [
    "先做文本主流程",
    "语音能力放到下一阶段"
  ],
  "created_at": "2026-04-16T20:12:00+08:00"
}
```

### Markdown 模板

```md
---
project_id: proj_ai_interview
topic_id: topic_mvp_scope
agent_id: frontend_gaearon
persona_name: Dan Abramov
mapped_real_person: Dan Abramov
role: frontend
trigger_source: before_material2proto
trigger_stage: pre_execute
created_at: 2026-04-16T20:12:00+08:00
---

# 观点摘要

不建议首版引入高复杂度语音链路。

# 详细意见

从前端实现和交互稳定性看，首版先验证文本面试主流程更稳。

# 建议动作

1. 先做文本主流程
2. 语音能力作为下一阶段验证项
```

---

## 三、圆桌工件

建议路径：

```text
.regen/topics/<topic-id>/roundtables/<timestamp>-<topic>.json
.regen/topics/<topic-id>/roundtables/<timestamp>-<topic>.md
```

### JSON 字段

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `project_id` | string | 是 | 项目 ID |
| `topic_id` | string | 是 | 话题 ID |
| `roundtable_id` | string | 是 | 圆桌唯一 ID |
| `topic_title` | string | 是 | 议题标题 |
| `moderator` | object | 是 | 主持人信息 |
| `participants` | array | 是 | 参会同事 |
| `core_conflicts` | array | 是 | 核心争议点 |
| `positions` | array | 是 | 各角色观点摘要 |
| `current_recommendation` | string | 是 | 当前建议结论 |
| `open_questions` | array | 否 | 未决问题 |
| `next_skill` | string | 否 | 推荐回到哪个主 skill |
| `created_at` | string | 是 | ISO 时间 |

### JSON 示例

```json
{
  "project_id": "proj_ai_interview",
  "topic_id": "topic_mvp_scope",
  "roundtable_id": "rt_20260416_201500",
  "topic_title": "MVP范围是否包含语音面试",
  "moderator": {
    "agent_id": "moderator_default",
    "persona_name": "Moderator"
  },
  "participants": [
    {
      "agent_id": "product_jobs",
      "persona_name": "Steve Jobs",
      "role": "product"
    },
    {
      "agent_id": "frontend_gaearon",
      "persona_name": "Dan Abramov",
      "role": "frontend"
    }
  ],
  "core_conflicts": [
    "首版是否应该把语音体验做完整",
    "技术复杂度是否会拖慢 MVP 验证"
  ],
  "positions": [
    {
      "role": "product",
      "summary": "首版体验要有亮点，但不应压垮节奏"
    },
    {
      "role": "frontend",
      "summary": "先用文本验证核心链路更稳"
    }
  ],
  "current_recommendation": "首版先验证文本面试流程，语音能力延后。",
  "open_questions": [
    "语音能力的业务价值是否足够支撑二期优先级"
  ],
  "next_skill": "regen-material2proto",
  "created_at": "2026-04-16T20:15:00+08:00"
}
```

### Markdown 模板

```md
---
project_id: proj_ai_interview
topic_id: topic_mvp_scope
roundtable_id: rt_20260416_201500
topic_title: MVP范围是否包含语音面试
created_at: 2026-04-16T20:15:00+08:00
---

# 圆桌议题

MVP范围是否包含语音面试

# 核心争议

1. 首版是否应该把语音体验做完整
2. 技术复杂度是否会拖慢 MVP 验证

# 各角色观点

## 产品

首版体验要有亮点，但不应压垮节奏。

## 前端

先用文本验证核心链路更稳。

# 当前建议结论

首版先验证文本面试流程，语音能力延后。

# 未决问题

1. 语音能力的业务价值是否足够支撑二期优先级

# 推荐下一步

回到 `regen-material2proto` 调整原型范围。
```

---

## 四、命名规范

### 文件名前缀

必须包含：

- 时间戳
- 触发来源或话题标识

建议格式：

```text
YYYYMMDD-HHMMSS-<trigger-or-topic>
```

示例：

- `20260416-101500-before-material2proto.json`
- `20260416-120000-mvp-scope-debate.md`

---

## 五、V1 约束

- 所有工件都必须输出双份：`json + md`
- `json` 是系统主读取源
- `md` 是用户主阅读源
- 圆桌工件必须包含“当前建议结论”和“推荐下一步”
