# 配色系统（可由用户自定义）

目标：让书籍的“视觉风格”从内容生成逻辑中解耦。用户可以只改这一份主题配置，就让全书统一换肤。

> 说明：TraeBook 默认支持 `light/dark` 两套主题。它们是“写死的主题变量集合”，但集合本身允许被替换。

---

## 1）推荐的主题变量（CSS 变量）

建议所有章节页使用同一套变量名（不要在章节内乱起变量名）：

### 通用变量（两套主题共用）

```css
:root{
  --radius-xl: 32px;
  --radius-lg: 24px;
  --radius-md: 18px;
  --radius-sm: 12px;
  --transition: 220ms ease;
}
```

### 日间主题（light）

```css
[data-theme="light"]{
  --app-bg: /* 页面背景渐变 */;
  --shell-bg: rgba(248, 243, 233, 0.82);
  --shell-border: rgba(85, 93, 74, 0.18);
  --shell-highlight: rgba(255, 255, 255, 0.58);
  --topbar-bg: rgba(248, 243, 233, 0.76);

  --text-primary: #12150f;
  --text-secondary: #415342;
  --text-muted: #6f7668;

  --line: rgba(59, 70, 56, 0.24);
  --line-strong: rgba(34, 45, 36, 0.34);

  --panel-bg: rgba(255, 251, 244, 0.58);
  --panel-bg-strong: rgba(251, 247, 239, 0.72);
  --panel-stroke: rgba(71, 89, 67, 0.18);

  --chip-bg: rgba(232, 239, 221, 0.92);
  --chip-text: #52714f;

  --accent: #1f5f45;
  --accent-soft: #8fc59a;
  --accent-strong: #246a4c;

  --highlight: #d7ebbc;
  --highlight-text: #183323;

  --node-bg: rgba(255,255,255,0.68);
  --input-bg: rgba(255,255,255,0.86);
  --input-text: #172117;

  --shadow: 0 18px 60px rgba(0,0,0,0.18);
}
```

### 夜间主题（dark）

```css
[data-theme="dark"]{
  --app-bg: /* 页面背景渐变 */;
  --shell-bg: rgba(4, 16, 13, 0.78);
  --shell-border: rgba(158, 255, 201, 0.12);
  --shell-highlight: rgba(182, 255, 204, 0.06);
  --topbar-bg: rgba(4, 14, 12, 0.72);

  --text-primary: #eef4e8;
  --text-secondary: #cfdfd0;
  --text-muted: #8ca496;

  --line: rgba(151, 216, 168, 0.14);
  --line-strong: rgba(179, 240, 194, 0.22);

  --panel-bg: rgba(12, 27, 22, 0.46);
  --panel-bg-strong: rgba(15, 35, 28, 0.64);
  --panel-stroke: rgba(158, 255, 201, 0.12);

  --chip-bg: rgba(42, 80, 57, 0.34);
  --chip-text: #b6fcb3;

  --accent: #9df08e;
  --accent-soft: #56b887;
  --accent-strong: #c2ff9b;

  --highlight: #c8ff9b;
  --highlight-text: #07130d;

  --node-bg: rgba(17, 32, 28, 0.82);
  --input-bg: rgba(15, 27, 23, 0.9);
  --input-text: #ebf5ea;

  --shadow: 0 28px 80px rgba(0,0,0,0.42);
}
```

---

## 2）用户如何自定义风格（推荐方式）

你可以让用户以“覆盖变量”的方式自定义，而不是改每个章节：

1. 新建 `book/theme.css`
2. 把本文件里的变量粘贴进去并改色
3. 每个章节页只需要 `<link rel="stylesheet" href="/book/theme.css">`

---

## 3）主题切换按钮与进入状态（强制）

TraeBook 必须保留原 UI 的 light/dark 双主题体验，且日间主题必须保持绿色系配色，不得改成蓝紫、米黄、灰黑等无关风格。

状态一致性规则：
- 页面根节点只能用 `data-theme="light"` 或 `data-theme="dark"` 表示当前主题。
- 进入页面时，优先级为：URL 参数 `theme` > localStorage 中的 TraeBook 主题 > 系统偏好 > 默认 `light`。
- 主题切换按钮必须和实际主题对应：
  - 当前是 `light`：页面呈现绿色日间配色，按钮可显示“夜间”或月亮图标，表示点击后进入夜间。
  - 当前是 `dark`：页面呈现夜间配色，按钮可显示“日间”或太阳图标，表示点击后进入日间。
- 切换后必须同时更新 `data-theme`、按钮 `aria-pressed`/可访问名称、localStorage 和可复现分享状态（如 URL 中的 `theme`）。
- 不能出现按钮显示夜间但页面仍是日间、按钮显示日间但页面仍是夜间，或刷新后按钮状态与实际颜色不一致。

推荐按钮语义：

```html
<button class="tb-theme-toggle" type="button" aria-label="切换到夜间模式" data-next-theme="dark">
  <span aria-hidden="true">☾</span>
</button>
```

## 4）原则（避免高级感被破坏）

- 强调色只用于：焦点、进度、步骤当前项、关键结论（不要全屏泛绿）
- 折叠/卡片统一用同一层级：背景、描边、圆角、阴影一致
- 字体层级固定：标题/小标题/正文/注释
- 主题切换按钮只负责主题状态，不混入章节进度、分享或练习答案行为
- 所有按钮 hover/active/focus 状态必须使用当前主题变量，不能硬编码成另一套颜色
