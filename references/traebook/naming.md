# TraeBook 命名与目录规范（推荐）

## 1) 为什么要规范命名？

TraeBook 的产出是“可分享/可复现/可嵌入”的网页资产。  
如果书系目录名随意（例如统一叫 `traebook`），会导致：
- 多个主题的书互相覆盖、难以并存
- 分享链接不稳定（路径重复、语义不清）
- 后续做索引/路由/部署时增加迁移成本

---

## 2) 书系根目录（Book Root）

推荐将每本书放在独立目录，使用**主题命名**：

**推荐：**
- `books/<topic_slug>/`  
  例如：
  - `books/bio-cell/`
  - `books/dsa/`
  - `books/frontend-performance/`

**不推荐：**
- 永远叫 `traebook/`（这更适合作为框架/演示名，而非“主题书系名”）

> 说明：当前仓库中的 `traebook/` 是示例书系目录（demo），用于验证模板与组件方案；实际产出时建议按主题创建新目录。

---

## 3) `<topic_slug>` 规则（强烈推荐使用 ASCII）

为了让路径在各种部署环境中最稳：
- 全小写
- `kebab-case`（用 `-` 分隔）
- 只含 `a-z 0-9 -`

从中文主题生成 slug 的建议方式：
- 优先：给出一个简短英文名（例如“生物细胞”→ `bio-cell`）
- 或：拼音（例如 `shengwu-xibao`）

---

## 4) 目录结构（每本书一致）

在 `book root` 下保持一致结构，方便嵌入与路由复用：

```
<book_root>/
  index.html
  manifest.json
  chapters/
  assets/
  share/
```

