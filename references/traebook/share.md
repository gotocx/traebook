# 分享机制（可配置 baseUrl）

目标：分享链接必须“外部可访问、可复现、可长期稳定”。

## 1）baseUrl（解决“内部链接”问题）

章节页的 `location.href` 往往是预览域名或内部网关域名。TraeBook 必须支持一个可配置的 `baseUrl`：

- 若用户提供 `baseUrl`：分享链接使用 `baseUrl + location.pathname + location.search`
- 若用户不提供：默认使用 `location.origin`

推荐提供三种复制能力：
1. 复制公开链接（使用 baseUrl）
2. 复制相对路径（path + query，便于你粘到自己的项目路由）
3. 复制状态参数（例如 `theme/tab/step`），用于复现读到哪一步

## 2）分享“可复现”的最小策略（无后端）

在无后端情况下，建议用 URL 参数表达最小状态：

- `theme=light|dark`
- `tab=intuition|rules|engineering`
- `step=3`
- `minutes=10`

这样别人打开同一链接，就能看到同一状态。

> 更高级的“短链接/分享码”需要后端或对象存储支持（把 state 存到远端并返回 id）。

