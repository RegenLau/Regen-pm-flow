# 视觉设计规范

本文件包含原型生成时应遵循的完整视觉规范。在生成或修改原型时读取此文件。

## 配色体系

### 主色调

| 用途       | 色值      | Tailwind Class    | 使用场景                         |
|------------|-----------|-------------------|----------------------------------|
| 主色       | #003eb3   | `primary`         | 主按钮、选中状态、侧边栏激活项   |
| 主色浅     | #e6edf9   | `primary-light`   | 主色背景、hover 状态、选中行背景 |
| 成功       | #34a853   | `success`         | 成功提示、已发布/已生效状态标签  |
| 警告       | #fbbc04   | `warning`         | 警告提示、待处理状态标签         |
| 危险       | #ea4335   | `danger`          | 错误提示、删除按钮、已停用标签   |

### 中性色

| 用途         | 色值      | 使用场景                           |
|--------------|-----------|--------------------------------------|
| 标题文字     | #1f2937   | 页面标题、表头、重要信息 (gray-800)  |
| 正文文字     | #374151   | 正文内容、表格数据 (gray-700)        |
| 辅助文字     | #6b7280   | 说明文字、时间戳、占位符 (gray-500)   |
| 禁用文字     | #9ca3af   | 禁用按钮文字、不可操作项 (gray-400)   |
| 边框         | #e5e7eb   | 输入框边框、分割线、卡片边框 (gray-200)|
| 浅背景       | #f9fafb   | 页面背景、表格斑马纹 (gray-50)       |
| 深背景       | #f3f4f6   | 侧边栏背景、区块底色 (gray-100)      |

### 60-30-10 配色规则

- **60% 主色调**：白色 (#ffffff) + 浅灰 (#f9fafb) 作为背景
- **30% 辅助色**：中性灰系列用于文字、边框、分割
- **10% 强调色**：主色 #003eb3 用于主按钮、链接、选中态；成功/警告/危险色用于状态

### 避免事项

- 不要使用靛蓝色（indigo）作为主色——除非 Regen 明确指定
- 不要同时使用超过 4 种颜色
- 不要对大面积区域使用高饱和度颜色

## 排版体系

### 文字层级

| 层级       | Tailwind Class                    | 使用场景           |
|------------|-----------------------------------|--------------------|
| 页面标题   | `text-xl font-semibold text-gray-800` | 页面顶部大标题     |
| 区块标题   | `text-lg font-medium text-gray-800`   | 卡片标题、区块标题 |
| 列表标题   | `text-base font-medium text-gray-700` | 表头、字段名       |
| 正文       | `text-sm text-gray-700`              | 表格数据、段落文字 |
| 辅助说明   | `text-xs text-gray-500`              | 字段说明、时间戳   |

### 行高与间距

- 正文行高：`leading-relaxed`（1.625）
- 标题行高：`leading-tight`（1.25）
- 段落间距：`space-y-4`（16px）
- 区块间距：`space-y-6`（24px）

## 组件规范

### 按钮

```html
<!-- 主按钮 -->
<button class="px-4 py-2 bg-primary text-white text-sm rounded-lg hover:bg-primary/90 transition-colors">
  保存
</button>

<!-- 次按钮 -->
<button class="px-4 py-2 border border-gray-300 text-gray-700 text-sm rounded-lg hover:bg-gray-50 transition-colors">
  取消
</button>

<!-- 危险按钮 -->
<button class="px-4 py-2 bg-danger text-white text-sm rounded-lg hover:bg-danger/90 transition-colors">
  删除
</button>

<!-- 文字按钮 -->
<button class="px-2 py-1 text-primary text-sm hover:text-primary/80 transition-colors">
  查看详情
</button>
```

- 按钮高度：36px（py-2 + text-sm）
- 圆角：8px（rounded-lg）
- 按钮之间间距：8px（gap-2）

### 输入框

```html
<input class="w-full px-3 py-2 text-sm border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-primary/20 focus:border-primary transition-colors" />
```

- 高度：36px
- 圆角：8px
- 聚焦时：主色边框 + 浅主色 ring

### 表格

```html
<table class="w-full text-sm">
  <thead>
    <tr class="bg-gray-50 text-left text-gray-500 text-xs uppercase tracking-wider">
      <th class="px-4 py-3 font-medium">列名</th>
    </tr>
  </thead>
  <tbody class="divide-y divide-gray-100">
    <tr class="hover:bg-gray-50 transition-colors">
      <td class="px-4 py-3 text-gray-700">数据</td>
    </tr>
  </tbody>
</table>
```

- 表头：浅灰底、小号加粗文字
- 行高：44px（py-3）
- 斑马纹：hover 时浅灰背景
- 分割线：使用 `divide-y divide-gray-100`

### 弹窗 / Modal

```html
<!-- 遮罩层 -->
<div class="fixed inset-0 bg-black/50 z-40"></div>
<!-- 弹窗主体 -->
<div class="fixed inset-0 flex items-center justify-center z-50">
  <div class="bg-white rounded-xl shadow-xl w-full max-w-lg mx-4">
    <div class="flex items-center justify-between px-6 py-4 border-b">
      <h3 class="text-lg font-medium text-gray-800">弹窗标题</h3>
      <button class="text-gray-400 hover:text-gray-600">×</button>
    </div>
    <div class="px-6 py-4 max-h-96 overflow-y-auto">
      <!-- 内容 -->
    </div>
    <div class="flex justify-end gap-2 px-6 py-4 border-t bg-gray-50 rounded-b-xl">
      <button>取消</button>
      <button>确定</button>
    </div>
  </div>
</div>
```

- 圆角：12px（rounded-xl）
- 阴影：shadow-xl
- 遮罩：50% 透明黑色
- 内容区最大高度 384px，超出滚动
- 底部按钮区浅灰底色

### 状态标签

```html
<span class="inline-flex items-center px-2 py-0.5 rounded text-xs font-medium bg-green-100 text-green-700">已发布</span>
<span class="inline-flex items-center px-2 py-0.5 rounded text-xs font-medium bg-gray-100 text-gray-600">草稿</span>
<span class="inline-flex items-center px-2 py-0.5 rounded text-xs font-medium bg-red-100 text-red-700">已停用</span>
<span class="inline-flex items-center px-2 py-0.5 rounded text-xs font-medium bg-yellow-100 text-yellow-700">待审核</span>
```

- 圆角：4px（rounded）
- 内边距：px-2 py-0.5
- 字号：text-xs

### Toast 提示

使用 Alpine.js 实现自动消失的通知：

```html
<div x-data="{ toasts: [] }" 
     @toast.window="toasts.push({...$event.detail, id: Date.now()}); setTimeout(() => toasts.shift(), 3000)"
     class="fixed top-4 right-4 z-50 space-y-2">
  <template x-for="toast in toasts" :key="toast.id">
    <div class="flex items-center gap-2 px-4 py-3 rounded-lg shadow-lg text-sm"
         :class="toast.type === 'success' ? 'bg-green-50 text-green-700 border border-green-200' : 'bg-red-50 text-red-700 border border-red-200'">
      <span x-text="toast.message"></span>
    </div>
  </template>
</div>
```

### 侧边栏

```html
<aside x-data="{ collapsed: false }" 
       :class="collapsed ? 'w-16' : 'w-60'"
       class="h-screen bg-gray-50 border-r border-gray-200 transition-all duration-300 flex flex-col">
  <!-- 折叠按钮 -->
  <button @click="collapsed = !collapsed" class="p-3 text-gray-400 hover:text-gray-600">
    <i :data-lucide="collapsed ? 'panel-left-open' : 'panel-left-close'"></i>
  </button>
  <!-- 菜单项 -->
  <nav class="flex-1 px-2 space-y-1">
    <a class="flex items-center gap-3 px-3 py-2 rounded-lg text-sm text-gray-700 hover:bg-gray-100">
      <i data-lucide="icon-name" class="w-5 h-5"></i>
      <span x-show="!collapsed" class="transition-opacity">菜单名称</span>
    </a>
  </nav>
</aside>
```

## 圆角体系

| 尺寸   | Tailwind    | 像素  | 使用场景                   |
|--------|-------------|-------|----------------------------|
| 小     | `rounded`   | 4px   | 标签、小徽章、搜索条件标签 |
| 中     | `rounded-lg`| 8px   | 按钮、输入框、卡片         |
| 大     | `rounded-xl`| 12px  | 弹窗、抽屉、大面积容器     |
| 超大   | `rounded-2xl`| 16px | 全屏卡片、Hero 区域        |

## 图标规范

使用 Lucide Icons 库，统一线性风格：

```html
<!-- 在 HTML body 底部调用 -->
<script>lucide.createIcons();</script>

<!-- 使用方式 -->
<i data-lucide="search" class="w-4 h-4"></i>     <!-- 小图标：16px -->
<i data-lucide="plus" class="w-5 h-5"></i>        <!-- 标准图标：20px -->
<i data-lucide="settings" class="w-6 h-6"></i>    <!-- 大图标：24px -->
```

常用图标清单：
- 导航：`home`, `layout-dashboard`, `users`, `settings`, `log-out`
- 操作：`plus`, `pencil`, `trash-2`, `search`, `filter`, `download`, `upload`
- 状态：`check-circle`, `alert-circle`, `info`, `x-circle`
- 箭头：`chevron-left`, `chevron-right`, `chevron-down`, `arrow-left`
- 其他：`eye`, `eye-off`, `copy`, `external-link`, `more-horizontal`

## 数据图表

如果原型中需要图表（仪表盘、数据分析页面），使用 Chart.js CDN：

```html
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
```

图表配色使用主色系列：`#003eb3`（主色）、`#3b82f6`（蓝色辅助）、`#34a853`（成功色）、`#fbbc04`（警告色）。

## WCAG AA 对比度要求

- 普通文本（<18px 或 <14px bold）：对比度 ≥ 4.5:1
- 大文本（≥18px 或 ≥14px bold）：对比度 ≥ 3:1
- 主色 #003eb3 在白色背景上的对比度约为 8.5:1 ✓
- 避免使用浅色文字在浅色背景上（如浅灰文字在白色背景上）
