# upvote-rss-lzcapp

[johnwarne/upvote-rss](https://github.com/johnwarne/upvote-rss) 的懒猫微服打包，
仅发布到**喵喵（私有）商店**，镜像模式（不复制镜像、只改运行时地址）。

**Upvote RSS** 把 Reddit、Hacker News、Lemmy、Lobsters、PieFed、Mbin 和 GitHub
上的高热度帖子生成 RSS 订阅源，支持按分数、月度阈值或每日平均条数过滤，
可附带正文、AI 摘要和热门评论。

## 打包内容

| 项目 | 值 |
|------|----|
| 包名 | `community.lazycat.app.upvote-rss` |
| 上游镜像 | `ghcr.io/johnwarne/upvote-rss`（镜像模式） |
| 运行时加速 | `ghcr.1ms.run`（Action 默认 GHCR 加速地址） |
| 版本来源 | 上游 `v*.*.*` 标签，去 `v` 前缀后作为包版本 |
| 容器端口 | 80 |
| 持久化 | `/lzcapp/var/cache`（缓存正文/AI 摘要）、`/lzcapp/var/logs` |
| 健康检查 | `curl --fail http://localhost:80`（沿用上游 Dockerfile） |

## 部署参数

安装向导里的可选参数，留空即关闭对应功能：

- **Reddit**：用户名、Client ID、Client Secret 三项都填才会启用 Reddit。
  在 [reddit.com/prefs/apps](https://www.reddit.com/prefs/apps) 创建 web app 获取。
- **AI 摘要**：Ollama 地址/模型，或 OpenAI API Key/模型。启用后生成订阅源会变慢。
- 其他平台（Hacker News、Lemmy、Lobsters、PieFed、Mbin、GitHub）无需任何凭据。

## 访问方式说明

应用本身没有账号体系，而 RSS 阅读器需要直接抓取订阅地址，所以
`lzc-manifest.yml` 把 `/` 放进了 `public_path`：**整个站点公开访问**。

- 订阅地址就是访问凭据，知道地址的人都能生成订阅源。
- 如果填写了 Reddit 凭据，别人也能借这个实例消耗你的 Reddit API 配额。
- 不需要外部阅读器时，可以删掉 `public_path`，让平台入口登录鉴权兜底，
  但这样 RSS 阅读器将无法抓取。

## 版本与发布

- `version_source` 指向 `upvote-rss` 镜像，每天定时检查上游新标签。
- 新版本会更新 `package.yml` 的 `version`、`lzc-manifest.yml` 的镜像地址，
  构建 LPK、创建带版本号的 Release 资产，再提交到喵喵商店。
- 自动更新只取 `^v\d+\.\d+\.\d+$`，忽略 `latest`。
- 官方商店关闭（`stores.official.enabled: false`），不会触发官方审核。

### GHCR 加速地址

首发当天 `ghcr.1ms.run` 整体不可用（所有 GHCR 镜像返回
`上游服务暂不可用`），曾用仓库变量 `LAZYCAT_GHCR_MIRROR` 临时指向
`ghcr.dockerproxy.net`。该变量已删除，加速地址回到默认的
`ghcr.1ms.run`，镜像摘要与上游一致。

以后再遇到加速站故障，处理方式相同：

1. 设置仓库变量 `LAZYCAT_GHCR_MIRROR`（GHCR）或 `LAZYCAT_DOCKER_MIRROR`（Docker Hub）
2. 手动把 `lzc-manifest.yml` 的 `image:` 改成新地址（同一 tag）
3. 重新触发一次工作流校验摘要

注意：镜像摘要没变时，Action 不会仅因为加速地址变化而改写运行时地址，
所以第 2 步必须手改。

## 版权

上游 [johnwarne/upvote-rss](https://github.com/johnwarne/upvote-rss)，
MIT License。本仓库只做懒猫打包，不修改上游代码。

本包尚未在懒猫实机安装验证。
