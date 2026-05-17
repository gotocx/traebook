---
name: traebook
description: 创建、继续、修订和发布 TraeBook 可拔插教学电子书。Use when user needs to turn a learning topic into a shareable interactive ebook, chapter HTML, concept graph, manifest/index/chapter files, exercises, learning-path design, AlgoBook/TraeBook style chapter output, or when the user asks to continue or patch an existing chapter. Enforces one chapter per turn, research-backed sequencing, meaningful HTML interactivity, shareable state, original green light/dark UI theme parity, question-card answer disclosure, and no-illustration dynamic fallback.
version: 1.0.2
---

# TraeBook

<!-- @类型: 标准操作流程(SOP) -->
<!-- @目的: 将学习主题编排成可拔插、可分享、可逐章迭代的互动电子书 -->
<!-- @场景: 创建新书、继续下一章、修订当前章节、生成可嵌入 HTML 章节与分享状态 -->
<!-- @触发条件: 用户要求制作学习书、互动章节、教学路径、TraeBook/AlgoBook 书系或继续/修订已有章节 -->

> **一句话**: TraeBook 是教学编排器，不是普通写作器；先设计学习路径，再逐章产出可运行、可分享、可复现的书籍页面。
> **版本**: v1.0.2
> **用途**: 生成同一视觉/信息风格的可拔插教学电子书

## @工作流: TraeBook 教学编排

<!-- @类型: 主工作流 -->
<!-- @目的: 从极简用户输入出发，完成书系状态、知识地图、单章 HTML、练习与分享状态的编排 -->
<!-- @场景: 用户只说“我想学习 X”“继续”“修一下这一章”或要求生成可分享互动课程书 -->
<!-- @前置条件: 已获得一个学习主题，或已有 book root/manifest/上一章状态可延续 -->
<!-- @后置验证: 每次最多交付一章，章节通过质量清单，manifest/index/share 状态可复现 -->
<!-- @触发条件: 创建、继续、修订或发布 TraeBook 书系 -->
<!-- @ID: wf-traebook-orchestration -->

### @步骤0: 用决策矩阵命中当前任务

<!-- @类型: 决策步骤 -->
<!-- @优先级: 必须 -->
<!-- @验证点: 已把用户请求路由到新建、继续、修订或发布检查路径 -->
<!-- @验证方式: 根据用户原话匹配矩阵行，并记录即将读取的参考文件 -->
<!-- @ID: step-route-traebook-task -->

| 场景 | 命中信号 | 跳转到 |
|---|---|---|
| 新建书系 | “我想学习 X”“做一本书”“生成课程书”“TraeBook/AlgoBook” | `step-establish-book-state` |
| 继续下一章 | “继续”“下一章”“开始下一节”且已有上一章/manifest | `step-build-concept-map` |
| 修订当前章 | “共读修订”“我卡住的点”“补一个误区/例子/自测” | `step-revise-existing-chapter` |
| 发布与检查 | “可分享”“打包”“检查质量”“能不能嵌入” | `step-final-quality-gate` |

- @动作: 若用户输入极简，先用已有 manifest、上一章桥接、书系主题和默认学习时长补全，不要因为缺少细节而停止。
- @动作: 若输入不足以决定主题或书系根目录，采用 `references/traebook/naming.md` 的主题 slug 规则给出保守默认值。

### @步骤1: 强规则摘要

<!-- @类型: 规则步骤 -->
<!-- @优先级: 必须 -->
<!-- @验证点: 已应用 TraeBook 的硬约束，未退化成普通长文写作 -->
<!-- @验证方式: 对照 one-turn one-chapter、知识地图、交互、调研、分享和插图降级规则逐项检查 -->
<!-- @ID: step-traebook-hard-rules -->

- @动作: 始终把 TraeBook 当作“教学编排器”：先建知识结构、学习目标和依赖关系，再写章节内容。
- @动作: 每次对话最多产出 1 个章节页面；允许同步增量更新 `manifest.json`、`index.html` 和本章练习页，但禁止一次生成多章正文。
- @动作: 每章必须包含学习目标、直觉解释、规则/边界、最小示例、误区或反例、至少 2 题练习、本章总结、可复述测试和下一章桥接。
- @动作: 每章至少使用 2 种有意义的 HTML 交互模式；交互必须回答 Step、Why、What-if、Check 或 Pitfall 中至少一个学习问题。
- @动作: 每章必须有可复现分享策略，最低可用 URL 参数或 `share/*.json` 保存章节状态。
- @动作: `index.html` 必须保留章节目录链接；对 manifest 中所有已知章节，以及本轮计划中的下一章占位，都要有可点击或明确禁用的章节入口。
- @动作: 追加新章节时必须回写前一章导航；生成 `ch2.html` 后，`ch1.html` 顶部或底部必须出现指向 `ch2.html` 的“下一章”链接。
- @动作: 插图必须服务理解；当没有可用插图 URL 时，立即执行“无插图动态降级”，生成结构化 HTML/CSS 视觉模块，不留空白图位、不伪造远端 URL、不使用 base64。
- @动作: 保留 TraeBook 原 UI 配色体系：日间使用绿色系 light 变量，夜间使用 dark 变量；主题切换按钮的图标、文案、选中态和进入页面后的 `data-theme` 必须一致。
- @动作: 练习题卡的标题必须是“问题本身”，展开区域才显示提示、答案和解析；禁止把“练习1/答案”作为卡片标题再把问题藏在展开区。
- @动作: 详细规则只按需读取 `references/traebook/` 下的原始说明文件；不要把所有参考一次性加载进上下文。

### @步骤2: 建立书系状态与输入默认值

<!-- @类型: 操作步骤 -->
<!-- @优先级: 必须 -->
<!-- @依赖: step-route-traebook-task -->
<!-- @验证点: 已确定 book root、主题 slug、学习者画像、每章时长和本次章节范围 -->
<!-- @验证方式: 能列出本次将创建或更新的文件路径，并说明默认值来源 -->
<!-- @ID: step-establish-book-state -->

- @动作: 按需读取 `references/traebook/README.md` 理解“教学编排器”定位。
- @动作: 按需读取 `references/traebook/naming.md` 确定 `<book_root>/`，默认使用 `books/<topic_slug>/`。
- @动作: 按需读取 `references/traebook/prompts/staged-input.md` 处理“启动 / 继续 / 共读修订”三种输入。
- @动作: 创建或更新的最小文件组遵循 `references/traebook/spec.md`：`manifest.json`、`index.html`、`chapters/<id>.html`、可选 `exercises/`、`assets/`、`share/`。
- @动作: 若用户未提供学习者画像，建立最低可用画像：学习目标、基础水平、每章学习时长；默认 10 分钟/章，低于 5 分钟需确认。

### @步骤3: 构建知识地图与章节目标

<!-- @类型: 操作步骤 -->
<!-- @优先级: 必须 -->
<!-- @依赖: step-establish-book-state -->
<!-- @验证点: 已形成 Concept Graph、章节覆盖节点和 3 条以内可验收学习目标 -->
<!-- @验证方式: 本章能说明 prerequisites、covers、next，并且每章核心概念不超过 5 个 -->
<!-- @ID: step-build-concept-map -->

- @动作: 按 `references/traebook/spec.md` 生成或延续 Concept Graph，包含 nodes、edges、chapters 三部分。
- @动作: 若已有上一章，优先继承上一章“下一章桥接”、manifest tags 和用户未解决问题。
- @动作: 对每个核心概念至少安排两层解释：直觉层和规则层；工程层按主题需要补充。
- @动作: 涉及事实性、学科性或可能更新的内容时，先调研至少 2 个高质量公开来源，并在章节中加入“调研摘录与来源（折叠）”。

### @步骤4: 产出本章可拔插页面

<!-- @类型: 操作步骤 -->
<!-- @优先级: 必须 -->
<!-- @依赖: step-build-concept-map -->
<!-- @验证点: 本章 HTML 或同等章节数据具备统一结构、交互闭环和分享状态 -->
<!-- @验证方式: 对照章节模板、交互模式、主题变量和分享规则检查页面结构 -->
<!-- @ID: step-produce-one-chapter -->

- @动作: 按 `references/traebook/templates/chapter.md` 填充章节骨架，再转换为目标项目需要的 HTML/组件形态。
- @动作: 交互模式优先从 `references/traebook/components/patterns.md` 选择：Tabs、Collapse、Stepper、Checklist、Quiz、Share Bar。
- @动作: UI 视觉遵循 `references/traebook/theme.md`：保留原绿色日间配色与夜间配色，统一 CSS 变量，保证进入页面后的主题状态与切换按钮显示对应。
- @动作: 练习题卡遵循 `references/traebook/components/patterns.md`：卡片 header/summary 写问题，展开后显示 Hint、答案和解析。
- @动作: 页面导航遵循 `references/traebook/workflow.md`：home 目录保留所有章节链接，章节页保留上一章/下一章/返回目录链接，追加章节必须回写相邻章节。
- @动作: 分享机制遵循 `references/traebook/share.md`：优先提供公开链接、相对路径和可复现状态参数。
- @动作: UI 优化时优先解决可读性和学习闭环：明确当前位置、章节进度、练习反馈、折叠答案、移动端不重叠；不要加入无学习意义的装饰动效。

### @步骤5: 执行无插图动态降级

<!-- @类型: 操作步骤 -->
<!-- @优先级: 必须 -->
<!-- @依赖: step-produce-one-chapter -->
<!-- @验证点: 插图不可用时页面仍完整、可理解、可分享，且没有坏图、空 figure 或伪造 URL -->
<!-- @验证方式: 模拟无 ILLUSTRATION_API_URL、请求失败、URL 为空或用户禁用插图四种情况检查输出 -->
<!-- @失败信号: 页面出现空白图位、broken image、base64 图片、与主题无关的装饰图或虚构 CDN 链接 -->
<!-- @ID: step-no-illustration-fallback -->

- @动作: 遇到 `TRAEBOOK:ILLUSTRATION` 时先尝试按 `references/traebook/components/illustrations.md` 获取远端 URL。
- @动作: 若没有可用远端 URL，立即把插图占位符替换为“文字驱动视觉模块”：概念地图、步骤推演、误区对照、参数小实验或关键术语矩阵。
- @动作: 降级模块必须使用语义 HTML、CSS 变量和已有组件风格；它承担原插图的教学目的，而不是声明“图片加载失败”。
- @动作: 在 HTML 注释中记录降级原因和原始插图意图，便于后续有插图服务时恢复。

### @步骤6: 共读修订当前章节

<!-- @类型: 操作步骤 -->
<!-- @优先级: 必须 -->
<!-- @依赖: step-route-traebook-task -->
<!-- @验证点: 修订只补当前章节，不误生成新章或改写全书结构 -->
<!-- @验证方式: 输出补丁式变更，并在章节内保留“修订记录/变更说明”折叠区 -->
<!-- @ID: step-revise-existing-chapter -->

- @动作: 当用户说“卡住的点”或“希望补充形式”时，按 `references/traebook/prompts/staged-input.md` 的阶段 2 执行。
- @动作: 只修改当前章节相关 HTML/练习/状态，不生成下一章正文。
- @动作: 修订必须补进学习闭环：误区、更正、例子、自测、来源或 UI 反馈中至少一种。

### @步骤7: 发布前质量闸门

<!-- @类型: 验证步骤 -->
<!-- @优先级: 必须 -->
<!-- @依赖: step-produce-one-chapter -->
<!-- @验证点: 章节结构、深度、练习、交互、分享和插图降级都达标 -->
<!-- @验证方式: 按 `references/traebook/checklists/quality.md` 全量检查；若不达标，缩小章节范围后重做 -->
<!-- @ID: step-final-quality-gate -->

- @动作: 对照 `references/traebook/checklists/quality.md` 执行发布前检查。
- @动作: 确认页面首屏能看出本章主题、学习目标、进度与可操作入口。
- @动作: 确认 `index.html` 中存在 manifest 所列每章的链接，且从 home 可直接进入 `ch1` 与已生成的 `ch2`。
- @动作: 确认除末章外，每个章节页都有指向下一章的链接；追加章节后要回测前一章链接是否已更新。
- @动作: 确认移动端和桌面端文字不溢出、不遮挡、不依赖插图才能理解。
- @动作: 确认主题切换按钮、页面 `data-theme`、localStorage/URL 状态和实际颜色一致，日间必须是绿色系 light 方案。
- @动作: 确认所有练习题卡都是“标题=问题，展开=答案/解析”，读者不用展开才能知道题目。
- @动作: 若章节没有达到 5-10 分钟深读密度，优先增加规则、例子、误区、练习或交互反馈，而不是堆重复段落。

## 参考入口

- 原始总览与核心定位：`references/traebook/README.md`
- 实现规格与数据结构：`references/traebook/spec.md`
- 逐章生成流程：`references/traebook/workflow.md`
- 输出限制与密度标准：`references/traebook/constraints.md`
- 命名、分享、主题：`references/traebook/naming.md`、`references/traebook/share.md`、`references/traebook/theme.md`
- 交互与组件：`references/traebook/components/patterns.md`、`references/traebook/components/library-recommendations.md`
- 插图与无插图降级：`references/traebook/components/illustrations.md`
- 章节模板与质量清单：`references/traebook/templates/chapter.md`、`references/traebook/checklists/quality.md`

## 版本历史

- **v1.0.2** (2026-05-17) - 修复章节导航生成规则：home 必须保留所有章节链接，追加章节时必须回写前一章的下一章链接
- **v1.0.1** (2026-05-16) - 强化原 UI 绿色日间/夜间主题保留、主题切换按钮状态一致性，以及题卡“标题=问题、展开=答案/解析”规则
- **v1.0.0** (2026-05-16) - 将原 TraeBook 资料封装为可触发 Skill，补齐语义化工作流、按需参考入口、UI 检查点与无插图动态降级规则
