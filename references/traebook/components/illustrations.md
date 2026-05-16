# TraeBook 插图机制（实时生成 + URL 引用）

目标：插图必须服务于理解（直觉/结构/步骤），并且在 HTML 中以 **URL** 方式加载（`<img src="...">`），禁止 base64 内嵌。

---

## 1) 章节内占位符（作者写作阶段）

在章节草稿中用占位符声明插图位置与意图：

```html
<!-- TRAEBOOK:ILLUSTRATION
id: hero
purpose: 本章主插图（建立直觉/激发兴趣/帮助粗读）
style_ref: teacherhelper_green_glass
size: landscape_16_9
alt: 本章核心概念示意图
-->
```

> 约束：每章最多 1–2 个插图占位符。

---

## 2) “实时生成”规则（TraeBook 在对话中执行）

当 TraeBook 在**实时回答**中遇到 `TRAEBOOK:ILLUSTRATION`：

1. **根据本章内容实时编写图片生成提示词**（必须包含 purpose + 主题关键词 + 视觉风格）
2. **向云端插图服务发起请求**，得到图片 URL（推荐 https CDN 链接）。  
   - 请求：`POST {ILLUSTRATION_API_URL}`  
   - body：`{ prompt, styleRef, size, seed? }`  
   - 响应：`{ url, alt? }`
3. 把占位符替换为 HTML 片段（URL 引用，不内嵌）：

```html
<figure class="tb-figure" data-figure-id="<id>">
  <img class="tb-img" src="<url>" alt="<alt>" loading="lazy" />
  <figcaption class="tb-caption">图：本章核心直觉示意</figcaption>
</figure>
```

---

## 3) URL 请求要求（必须是“远端 URL”）

插图必须来自“云端服务返回的 URL”。  
本地文件路径只能作为开发调试手段，正式机制必须依赖云端 URL（以满足跨端分享、脱离当前沙箱仍可访问）。

---

## 4) 禁止无意义插图（硬规则）

插图必须至少满足一条：

- 10 秒内让读者知道“这一章到底在讲什么结构”
- 展示一个“流程/步骤/状态变化”（如 main.tsx 启动链）
- 展示“误区 vs 正解”对比

禁止：
- 纯装饰背景图
- 与本章学习目标无关的图

---

## 5) 无插图动态降级（新增强制规则）

目标：插图服务不可用时，章节仍然完整、可理解、可分享；不能留下坏图、空白占位或伪造 URL。

触发条件：
- `ILLUSTRATION_API_URL` 未配置
- 插图服务请求失败、超时或返回空 URL
- 用户明确要求不使用插图
- 当前运行环境无法上传、托管或公开访问图片

降级策略：
1. 不生成 `<img>`，不写 base64，不虚构 CDN 链接。
2. 用同等教学目的的 HTML/CSS 模块替代插图：
   - 概念地图：适合“结构/层级/依赖关系”
   - Stepper：适合“流程/状态变化/启动链”
   - 误区 vs 正解对照：适合“容易混淆的概念”
   - 参数小实验：适合“What-if/改参数会怎样”
   - 关键术语矩阵：适合“术语多、关系密”的章节
3. 降级模块必须复用 TraeBook 主题变量和既有组件层级，不能像错误提示一样破坏版面。
4. 在 HTML 注释里记录降级原因，便于后续恢复插图：

```html
<!-- TRAEBOOK:ILLUSTRATION_FALLBACK
id: hero
reason: illustration_api_unavailable
original_purpose: 本章主插图（建立直觉/帮助粗读）
replacement: concept-map
-->
```

推荐替代片段：

```html
<section class="tb-visual-fallback" data-figure-id="hero" aria-label="本章核心概念结构图">
  <div class="tb-vf-node is-root">核心概念</div>
  <div class="tb-vf-row">
    <div class="tb-vf-node">前置依赖</div>
    <div class="tb-vf-node">本章规则</div>
    <div class="tb-vf-node">常见误区</div>
  </div>
  <p class="tb-caption">无插图模式：用结构化概念图替代原主插图。</p>
</section>
```

验收标准：
- 页面没有 broken image 图标
- 读者不看图片也能理解本章核心结构
- 降级模块至少回答 Step、Why、What-if、Check、Pitfall 中一个问题
- 分享链接打开后降级状态仍可复现
