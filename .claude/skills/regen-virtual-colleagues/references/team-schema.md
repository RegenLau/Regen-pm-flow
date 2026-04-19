# 虚拟团队配置 Schema

本文定义 `regen-virtual-colleagues` 在 V1 中使用的核心配置结构，用于项目级和话题级虚拟团队。

## 一、设计目标

- 让项目级团队和话题级团队都能被结构化读取
- 让其他主 skill 能够判断当前应该读取哪一层配置
- 让人物映射、岗位、触发策略、模式切换都有明确字段

---

## 二、项目级团队配置

建议文件路径：

```text
.regen/virtual-teams/project-team.json
```

### 字段定义

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `project_id` | string | 是 | 项目唯一 ID |
| `team_name` | string | 是 | 项目级团队名称 |
| `industry` | string | 是 | 行业标签，如 `AI工具`、`企业SaaS` |
| `product_type` | string | 是 | 产品类型，如 `传统应用`、`AI增强应用`、`AI Agent应用` |
| `mode` | string | 是 | 当前模式，`key_milestone` 或 `high_frequency` |
| `default_topic_policy` | string | 是 | 新话题默认策略，建议为 `inherit` |
| `members` | array | 是 | 当前项目已绑定成员列表 |
| `created_at` | string | 是 | ISO 时间 |
| `updated_at` | string | 是 | ISO 时间 |

### `members` 子字段

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `agent_id` | string | 是 | 虚拟同事唯一 ID |
| `persona_name` | string | 是 | 对外显示名称 |
| `mapped_real_person` | string | 是 | 映射的真实人物名 |
| `role` | string | 是 | 岗位，如 `product`、`frontend` |
| `industry` | string | 否 | 所属行业标签 |
| `mapping_type` | string | 是 | 固定为 `real_persona` |
| `core_stance` | string | 否 | 核心立场摘要 |
| `trigger_domains` | array | 否 | 适合触发的话题域 |
| `status` | string | 是 | `active` / `inactive` |

### 示例

```json
{
  "project_id": "proj_ai_interview",
  "team_name": "AI面试助手项目团队",
  "industry": "AI工具",
  "product_type": "AI增强应用",
  "mode": "key_milestone",
  "default_topic_policy": "inherit",
  "members": [
    {
      "agent_id": "product_jobs",
      "persona_name": "Steve Jobs",
      "mapped_real_person": "Steve Jobs",
      "role": "product",
      "industry": "消费电子/产品",
      "mapping_type": "real_persona",
      "core_stance": "强调产品体验、完整性和极致表达",
      "trigger_domains": ["定位", "产品结构", "核心流程", "MVP"],
      "status": "active"
    }
  ],
  "created_at": "2026-04-16T20:00:00+08:00",
  "updated_at": "2026-04-16T20:00:00+08:00"
}
```

---

## 三、话题级团队配置

建议文件路径：

```text
.regen/topics/<topic-id>/virtual-team.json
```

### 字段定义

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `topic_id` | string | 是 | 话题唯一 ID |
| `project_id` | string | 是 | 所属项目 ID |
| `topic_title` | string | 是 | 话题标题 |
| `policy` | string | 是 | `inherit` / `inherit_plus` / `replace_members` / `replace_all` |
| `inherits_project_team` | boolean | 是 | 是否继承项目级团队 |
| `extra_members` | array | 否 | `inherit_plus` 时的追加成员 |
| `override_members` | array | 否 | `replace_members` / `replace_all` 时的成员列表 |
| `mode` | string | 否 | 话题级模式，未填则继承项目级 |
| `created_at` | string | 是 | ISO 时间 |

### 示例

```json
{
  "topic_id": "topic_mvp_scope",
  "project_id": "proj_ai_interview",
  "topic_title": "MVP范围是否包含语音面试",
  "policy": "inherit_plus",
  "inherits_project_team": true,
  "extra_members": [
    {
      "agent_id": "growth_hoffman",
      "persona_name": "Reid Hoffman",
      "mapped_real_person": "Reid Hoffman",
      "role": "growth",
      "mapping_type": "real_persona",
      "status": "active"
    }
  ],
  "mode": "high_frequency",
  "created_at": "2026-04-16T20:10:00+08:00"
}
```

---

## 四、注册表

建议文件路径：

```text
.regen/virtual-teams/registry.json
```

用途：

- 保存项目中所有已绑定人物索引
- 支持按 `agent_id`、`role`、`mapped_real_person` 查找
- 给其他主 skill 做快速读取

### 最低字段建议

```json
{
  "project_id": "proj_ai_interview",
  "active_team_id": "project_default",
  "members": [
    {
      "agent_id": "product_jobs",
      "persona_name": "Steve Jobs",
      "mapped_real_person": "Steve Jobs",
      "role": "product",
      "status": "active"
    }
  ],
  "updated_at": "2026-04-16T20:15:00+08:00"
}
```

---

## 五、读取规则

其他主 skill 读取时按以下优先级：

1. 当前话题的 `virtual-team.json`
2. 项目级 `project-team.json`
3. `registry.json` 仅用于补充索引，不单独决定策略

---

## 六、V1 约束

- 所有配置必须是 JSON，便于系统读取
- 每个成员都必须带 `role`
- 真实人物映射必须放在 `mapped_real_person`
- 没有团队配置时，不能伪造默认团队，应回到推荐流程
