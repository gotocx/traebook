# TraeBook.skill 规格（面向实现）

## 1) 核心产出对象

TraeBook 的“最小可交付物”不是一段文字，而是一组文件：

> **重要：必须先确定书系根目录（book root）**，推荐按主题命名，详见 `naming.md`。  
> 下面所有路径均以 `<book_root>/` 为前缀。

- `manifest.json`：章节、路径、标签、依赖、版本号
- `index.html`：入口页（目录/推荐路线/搜索/继续学习）
- `chapters/<id>.html`：章节页（统一布局 + 可插拔）
- `exercises/<id>.html`：练习页（题目/提示/答案/评分，可选）
- `assets/`：插图/配色/通用 UI 资源（可选）
- `share/*.json`：分享状态（可选）

## 2) 章节数据结构（抽象）

每章的抽象模型：

```json
{
  "id": "ch1",
  "title": "第一章：从 main.tsx 开始",
  "topic": "源码解读",
  "level": "beginner",
  "prerequisites": [],
  "learningObjectives": ["…", "…", "…"],
  "concepts": [
    {"name": "入口文件", "definition": "...", "intuition": "...", "pitfalls": ["..."], "examples": ["..."]}
  ],
  "exercises": [
    {"id": "ex1", "type": "check", "prompt": "...", "hint": "...", "answer": "..."}
  ],
  "summary": "...",
  "next": "ch2"
}
```

## 3) 知识地图（Concept Graph）

强制产出一个知识地图（即使不展示给用户，也要用于内部编排）：

```json
{
  "nodes": [{"id":"n1","label":"…","level":1},{"id":"n2","label":"…","level":2}],
  "edges": [{"from":"n1","to":"n2","reason":"…"}],
  "chapters": [{"id":"ch1","covers":["n1","n2"]}]
}
```

作用：
- 解决“整理”难题：先结构化，再写作
- 解决“深度”难题：层级决定深度
- 解决“章节整齐”难题：每章绑定覆盖节点

## 4) 讲解深度机制（Depth Ladder）

对每个概念，强制至少两层解释：

- L1（直觉）：一个类比 + 一个图景
- L2（规则）：明确条件/步骤/边界
- L3（工程）：实现、复杂度、坑、变体（可选）

拒绝策略：
- 如果只能写 L1，说明知识图不够细或学习者画像不明确
- 这时应回退到 Discovery 阶段补信息，而不是“硬写一章”

## 4.1) 交互编排机制（HTML Interactivity）

每章至少包含以下交互中的 2 种（且必须有意义）：

- Tabs：直觉/规则/工程分层
- Collapse：误区/答案/扩展折叠
- Stepper：流程/推演逐步
- Checklist：可复述自检
- Quiz：题目→提示→答案

交互的“意义校验”：
- 每个交互控件必须能回答：我在哪一步 / 为什么这么做 / 改参数会怎样 / 我是否掌握 / 我哪里会错

禁止纯装饰交互。

## 4.1.1) “看得见的现象”迁移（推荐强制）

为了避免章节停留在“抽象名词”，章节中建议增加一个固定模块：
- 标题建议：`把本章迁移到“看得见的现象”`
- 内容要求：至少 3 条“现实/可观察现象 → 用本章模型解释它”的映射

这类模块通常能显著提升理解与记忆的稳定性（尤其在生物/化学/物理/工程类主题）。

## 4.2) 插图机制（实时生成 + URL 引用）

插图不是静态素材库，而是**在实时回答中生成**，并在 HTML 里用 URL 引用（禁止 base64）。

规范见：`components/illustrations.md`

## 5) 编排机制（Sequencing）

章节排序遵循：
1. 依赖优先（先学 prerequisites）
2. 认知负荷控制（每章最多 5 个核心概念）
3. 交替节奏（概念章 ↔ 练习章 ↔ 复盘章）
4. 定期复现（每 3 章回顾一次）

## 6) 分享机制（Share）

分享必须“可复现”：

- 分享链接携带 `bookVersion + chapterId + stateId`
- stateId 对应 `share/<stateId>.json`（包含交互状态/参数/题目答案）

最低实现（无后端）：
- URL hash：`#/book/ch1?state=abc`
- state 用 JSON 存本地或导出文件

## 6.1) 单次产出限制（One-turn One-chapter）

每次对话最多产出 1 章。  
章节必须达到：粗读 1 分钟 + 深读 5–10 分钟的信息密度（详见 `constraints.md`）。

## 7) 多品类覆盖机制（Domain Kits）

用“领域包”解决不同主题的结构差异：

- `domain/algorithm.json`：概念/练习类型偏代码
- `domain/sourcecode.json`：概念偏架构，练习偏“找文件/画流程/解释顺序”
- `domain/product.json`：概念偏框架，练习偏“写 PRD/拆需求/写用户故事”

领域包只负责：
- 推荐章节模板
- 推荐练习类型
- 深度阶梯的最低要求
