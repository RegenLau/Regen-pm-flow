# Figma 设计系统 — 组件展示页布局规范

参考文件：Design System - AIR（`qdXdLzBOsnurFCTzx46s8U`），Button frame（`4323:7118`）

---

## 一、页面层级结构

```
顶层 Frame（如 "Button"）
│
├── 大英文标题                        y = 128
├── 大分类 section-header             y = 288
│
├── 子组件 section-header/xxx         y = 288（与大分类同高）
└── 子组件 frame                      y = section-header.y + 128
```

---

## 二、大英文标题

页面最顶部的分类名称，使用设计系统中的 **`Title` 组件实例**，不要手动创建文字 frame。

| 属性 | 值 |
|------|----|
| 节点类型 | 组件实例（instance） |
| 组件名称 | `Title` |
| 位置 | x = 128, y = 128 |
| 尺寸 | 宽度自适应内容 × 高度 77px |

> 之前曾用过手动 frame（`Frame 1000005458`，Inter Bold 64px，tracking 0.96px），现已统一改为 `Title` 组件实例。新增页面时直接引用该组件。

示例：`"Button"`、`"Icon"`、`"Form"`

---

## 三、Section Header（子组件标题块）

每个子组件展示区上方都有一个 section-header，包含中文名 + 英文名两行。

> **强制规则：必须直接引用现有 Section Header 组件，禁止新建、克隆后转组件、或在页面里额外创建 source 组件。**
>
> 统一引用：
> - 组件名：`section-header/button`
> - 组件节点：`4811:1144`
>
> 使用方式：
> 1. 直接从 `4811:1144` 创建实例（instance）
> 2. 在实例中覆写中文名与英文名
> 3. 命名实例为 `section-header/{component-name}`
>
> 不允许的做法：
> - 复制一个现有 section-header frame 后在页面里转成新的 component
> - 在当前页面创建 `section-header/.../source`
> - 手动重画两行文字替代组件实例

### 尺寸与间距

| 属性 | 值 |
|------|----|
| 节点类型 | 组件实例（instance），来源固定为 `4811:1144` |
| Frame 高度 | 60px |
| 两行文字间距（gap） | 2px |
| 距下方组件 frame 的距离 | 68px（header 60px + gap 68px = 128px 总距离） |

**定位规则：**
- `section-header.x` = 对应组件 frame 的 x（左对齐）
- `section-header.y` = 对应组件 frame 的 y **− 128**

### 第一行：中文名

| 属性  | 值                       |
| --- | ----------------------- |
| 字体  | PingFang SC             |
| 字号  | 26px                    |
| 行高  | 36px                    |
| 颜色  | `#262833`（`文字/T4-文字主色`） |
| 内容  | 组件的中文描述名                |

示例：`按钮`、`图标按钮`、`拆分按钮`、`文字按钮`、`选项按钮`

### 第二行：英文名

| 属性 | 值 |
|------|----|
| 字体 | Noto Sans Regular |
| 字号 | 14px |
| 行高 | 22px |
| 颜色 | `#70748C`（`文字/T3-文字辅色深`） |
| 内容 | 组件的技术名称（kebab-case） |

示例：`button`、`icon-button`、`split-button`、`font-button`、`option-button`

### Frame 命名规则

```
section-header/{component-name}
```

示例：`section-header/icon-button`、`section-header/split-button`

---

## 四、子组件 Frame

实际展示变体的 ComponentSet 容器。

| 属性 | 值 |
|------|----|
| 命名 | 小写 kebab-case，如 `icon-button`、`split-button` |
| 内边距 | 48px（四边） |
| 变体间距 | 48px |
| x 位置 | 与 section-header 左对齐 |
| y 位置 | section-header.y + 128 |

---

## 四-A、变体属性命名规范

### 属性名（Property Name）

使用中文，如 `类型`、`状态`、`尺寸`、`操作入口`、`数字`。

### 属性值（Property Value）

**一律使用小写英文**，不使用中文或大写：

| 属性 | ✅ 正确 | ❌ 错误 |
|------|---------|---------|
| 状态 | `default` `hover` `focus` `disabled` `selected` `filled` | `Default` `Hover` `Default` |
| 类型 | `success` `error` `warning` `info` `primary` | `成功` `错误` `Success` |
| 操作入口 | `true` `false` | `是` `否` |
| 数字 | `true` `false` | `显示` `隐藏` |
| 尺寸 | `small（12px）` `medium（16px）` `large（20px）` | `small` `medium` `large` |

> ⚠️ 历史组件中存在中文或首字母大写的属性值（如 `状态=Default`、`类型=成功`），新增或修改变体时统一按此规范改为小写英文。
>
> ⚠️ 尺寸属性不能只写语义名，必须带具体尺寸值，格式统一为：
> `small（12px）` / `medium（16px）` / `large（20px）`

---

## 四-B、ComponentSet 内变体排列规则

### 多维变体的排列方式

当 ComponentSet 有两个属性维度时（如 `类型` × `操作入口`），排列规则如下：

**列 = 主分类维度**（如 `类型`：success / error / warning / info）
**行 = 次分类维度**（如 `操作入口`：false / true）

```
         success      error        warning      info
false  [ 变体 ]     [ 变体 ]     [ 变体 ]     [ 变体 ]
true   [ 变体 ]     [ 变体 ]     [ 变体 ]     [ 变体 ]
```

### 对齐规则

- 同一列内所有变体 **x 坐标相同**（左对齐）
- 列宽取该列内最宽变体的宽度
- 列间距、行间距统一 **48px**
- 四周内边距统一 **48px**

### Plugin API 排列示例

```js
var typeOrder = ["success", "error", "warning", "info"];
var pad = 48, gap = 48;
var colX = pad;

for (var i = 0; i < typeOrder.length; i++) {
  var noAction  = // 该类型 操作入口=false 的变体
  var hasAction = // 该类型 操作入口=true 的变体

  var colW = Math.max(noAction.width, hasAction.width);

  noAction.x  = colX; noAction.y  = pad;
  hasAction.x = colX; hasAction.y = pad + noAction.height + gap;

  colX += colW + gap;
}
```

> 结论：同一类型（列）内的变体 x 坐标必须一致，不同操作状态（行）只有 y 坐标不同。

---

## 五、实际坐标示例（Button 页）

| 组件 | section-header 位置 | 组件 frame 位置 |
|------|---------------------|-----------------|
| 按钮（button） | x=128, y=288 | x=128, y=416 |
| 图标按钮（icon-button） | x=1920, y=288 | x=1920, y=416 |
| 选项按钮（option-button） | x=2944, y=288 | x=2944, y=416 |
| 拆分按钮（split-button） | x=128, y=1228 | x=128, y=1322 |
| 文字按钮（font-button） | x=128, y=1580 | x=128, y=1676 |

> 规律验证：1322 − 1228 = 94 ≠ 128？实际上 split-button header 高度为 60px，1322 − 1228 = 94px。
> 注意：y 间距并不严格是 128px，实际 gap = 34px（60 + 34 = 94）。以实测为准，建议统一使用 **header bottom + 34px** 作为组件 frame 的 top。

---

## 六、颜色变量规范

**所有颜色必须绑定设计系统变量，禁止硬编码 RGB / Hex 值。**

### 常用颜色变量速查（Collection: Color）

| 变量名 | Hex | 用途 |
|--------|-----|------|
| `Text/text-1（标题）` | `#262833` | 主文字、图标 |
| `Text/text-2（次标题）` | `#70748c` | 辅助文字 |
| `Text/text-4（置灰信息）` | `#c5c8ce` | 禁用状态文字 |
| `Fill Neutral/fill-1（白色）` | `#ffffff` | 默认背景 |
| `Fill Neutral/fill-7（白色背景 hover）` | `#eef0f2` | Hover 背景 |
| `Border/border-2（控件）` | `#dcdee2` | 默认边框 |
| `Brand/brand-6（default）` | `#5c6cff` | Focus/激活边框 |

### 颜色绑定方式（Plugin API）

```js
// fill
const variable = figma.variables.getVariableById("VariableID:xxx");
const paint = figma.variables.setBoundVariableForPaint(node.fills[0], "color", variable);
node.fills = [paint];

// stroke 同理
const stroke = figma.variables.setBoundVariableForPaint(node.strokes[0], "color", variable);
node.strokes = [stroke];
```

> 注意：不要直接写 `node.fills = [{ type: "SOLID", color: { r, g, b } }]`，这样无法绑定变量。

### 圆角绑定方式（Plugin API）

```js
// 圆角必须用 setBoundVariable，不能直接赋数字
var cornerVar = figma.variables.getVariableById("VariableID:337:4827"); // Coner/Small = 8px
node.setBoundVariable("cornerRadius", cornerVar);
```

> 圆角变量完整速查见 `tokens.md` 第二节。常用：`Coner/Full`（按钮/选择器）、`Coner/Small`（输入框/Toast）、`Coner/Extra-small`（Tag）。

---

## 七、文字样式规范

**所有文字节点必须应用本地文字样式（Text Style），禁止手动设置 fontSize / lineHeight / fontName。**

### 常用文字样式速查（PingFang SC）

| 样式名                 | 字体          | 字重       | 字号   | 行高   |
| ------------------- | ----------- | -------- | ---- | ---- |
| `CN/Font10/Regular` | PingFang SC | Regular  | 10px | 16px |
| `CN/Font10/Medium`  | PingFang SC | Medium   | 10px | 16px |
| `CN/Font10/Bold`    | PingFang SC | Semibold | 10px | 16px |
| `CN/Font12/Regular` | PingFang SC | Regular  | 12px | 18px |
| `CN/Font12/Medium`  | PingFang SC | Medium   | 12px | 18px |
| `CN/Font12/Bold`    | PingFang SC | Semibold | 12px | 18px |
| `CN/Font14/Regular` | PingFang SC | Regular  | 14px | 22px |
| `CN/Font14/Medium`  | PingFang SC | Medium   | 14px | 22px |
| `CN/Font14/Bold`    | PingFang SC | Semibold | 14px | 22px |
| `CN/Font16/Regular` | PingFang SC | Regular  | 16px | 24px |
| `CN/Font16/Medium`  | PingFang SC | Medium   | 16px | 24px |
| `CN/Font16/Bold`    | PingFang SC | Semibold | 16px | 24px |
| `CN/Font18/Regular` | PingFang SC | Regular  | 18px | 28px |
| `CN/Font18/Medium`  | PingFang SC | Medium   | 18px | 28px |
| `CN/Font18/Bold`    | PingFang SC | Semibold | 18px | 28px |

### 文字样式绑定方式（Plugin API）

```js
// 直接赋值 textStyleId（不需要 loadFontAsync，不改字符时）
textNode.textStyleId = "S:xxxxxxxx,";

// 如需同时修改字符，先加载字体
await figma.loadFontAsync({ family: "PingFang SC", style: "Regular" });
textNode.characters = "新内容";
textNode.textStyleId = "S:xxxxxxxx,";
```

> 注意：section-header 的英文名（14px Regular）、图标按钮标签等控件文字，统一用 `CN/Font14/Regular`。

---

## 八、修改日志规范

**每次在 Figma 中对组件页面做变更，必须在当前页面的 `changelog` frame 中追加一条日志。**

### 日志 Frame 规范

| 属性 | 值 |
|------|----|
| Frame 名称 | `changelog` |
| 位置 | 主 frame 右侧，x = 主frame.x + 主frame.width + 80，y = 主frame.y |
| 宽度 | 520px（固定） |
| 高度 | 自适应内容（AUTO） |
| 背景色 | `#f7f7fa`（浅灰） |
| 圆角 | 8px |
| 内边距 | 上下 20px，左右 24px |
| 条目间距 | 8px |

### 日志条目格式

```
YYYY-MM-DD  变更内容简述
```

- 标题行：Noto Sans SemiBold 14px，颜色绑 `Text/text-1`
- 条目行：Noto Sans Regular 12px / 行高 20px，颜色绑 `Text/text-2`

### Plugin API 写法

```js
// 查找或创建 changelog frame，再 appendChild 新条目
let logFrame = page.findOne(n => n.name === "changelog" && n.type === "FRAME");
// ... 创建 entry text node，append 到 logFrame
```

---

## 九、快速新增组件区块的步骤

1. 确定组件 frame 的目标位置 `(x, y)`
2. 直接引用 `4811:1144` 创建 section-header 实例，放在 `(x, y - 94)`
3. 在实例中覆写两行文字：
   - 第一行：中文名
   - 第二行：英文名
4. 命名实例为 `section-header/{component-name}`
5. 将组件 frame 命名为 kebab-case，如 `icon-button`
6. 禁止在当前页面新建任何 `section-header/.../source` 组件
