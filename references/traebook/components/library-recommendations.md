# 组件库推荐（用于 TraeBook 书籍插件）

推荐组件使用Ant Design，或[fonted-skill。](https://www.skills.sh/anthropics/skills/frontend-design)中组件进行设计。

## A. Web React 项目（推荐优先级）

### 1) Ant Design（推荐）
适合：企业后台、表单/弹窗/步骤条/折叠面板/标签页等齐全，生态成熟。  
在 TraeBook 里最适合用来做：
- `Tabs`（层级切换：直觉/规则/工程）
- `Collapse`（折叠：误区/答案/扩展）
- `Steps`（步骤推演：启动链/流程图）
- `Checkbox` + `List`（可复述测试/自检）
- `Tag`（概念标签）
- `Popover/Tooltip`（术语解释）

### 2) Semi Design / Arco Design / TDesign（备选）
适合：更现代的视觉，组件同样齐全。  
用于 TraeBook：同上（Tabs/Collapse/Steps/Tag/Tooltip/Drawer）。

## B. 无框架/纯 HTML（你当前 algobook 形态）

如果我们坚持“无构建、可直接 script 引入”，推荐走：

1) **Headless UI 思想**（不是库，是模式）
- 组件外观自定义、交互语义固定（可访问性更好）
- 用原生 `<details>`/`<summary>` 实现折叠
- 用 `aria-*` + 键盘支持实现 tabs/stepper

2) **轻量 icon 库**
- Lucide / Heroicons（SVG，适合暗色高端风格）

## C. TraeBook 的推荐默认组件集（最小集合）

为了保持“好看 + 不过度复杂”，TraeBook 默认只允许这些交互组件（足够覆盖 80% 章节需求）：

1. `Tabs`：分层（直觉/规则/工程）
2. `Collapse`：折叠（误区、答案、扩展）
3. `Stepper`：流程推演（步骤 1..N）
4. `Checklist`：自检（我是否掌握）
5. `Quiz`：练习（提示/答案折叠）
6. `CopyButton`：复制关键代码/命令（需要真实用途）
7. `Share`：分享（复制链接/导出状态）

> 任何新增组件都必须说明它解决哪个学习问题，否则禁止加入。
