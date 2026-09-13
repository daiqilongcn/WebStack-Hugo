# WebStack-Hugo 网址导航

一个基于 [Hugo](https://gohugo.io/) 的静态网址导航站点。本仓库包含主题、站点配置、导航数据和图标资源，可部署到 Cloudflare Pages 或其他静态网站托管服务。

本项目基于 [shenweiyan/WebStack-Hugo](https://github.com/shenweiyan/WebStack-Hugo) 部署使用；开源许可证见 [LICENSE](LICENSE)。

## 目录说明

- `exampleSite/config.toml`：站点名称、域名、Logo、页脚及功能开关
- `exampleSite/data/webstack.yml`：导航分类和网址条目
- `exampleSite/data/headers.yml`：顶部导航内容
- `exampleSite/data/friendlinks.yml`：友情链接
- `static/assets/images/logos/`：网址图标
- `layouts/`：Hugo 模板

## 修改站点内容

### 修改站点地址

发布到正式域名后，编辑 `exampleSite/config.toml` 的第一行：

```toml
baseURL = "https://你的域名/"
```

例如：

```toml
baseURL = "https://example.com/"
```

### 增加网址

在 `exampleSite/data/webstack.yml` 中增加条目：

```yaml
- title: GitHub
  logo: github.png
  url: https://github.com/
  description: 全球开发者协作平台。
```

将对应图标放到 `static/assets/images/logos/`；没有图标时可使用 `default.webp`。

## Cloudflare Pages 部署

在 Cloudflare Dashboard 中选择 **Workers 和 Pages → 创建 → Pages → 连接 Git 仓库**，选择本仓库和 `main` 分支。

构建设置：

| 项目 | 值 |
| --- | --- |
| 框架预设 | 无 |
| 构建命令 | `mkdir -p themes/WebStack-Hugo && cp -R layouts static themes/WebStack-Hugo/ && hugo --source exampleSite --themesDir ../themes --minify --baseURL https://你的项目.pages.dev/` |
| 构建输出目录 | `exampleSite/public` |
| 根目录 | 留空 |
| 环境变量 | `HUGO_VERSION` = `0.122.0` |

将命令中的 `https://你的项目.pages.dev/` 替换为 Cloudflare 分配给项目的 Pages 地址。例如：

```sh
mkdir -p themes/WebStack-Hugo && cp -R layouts static themes/WebStack-Hugo/ && hugo --source exampleSite --themesDir ../themes --minify --baseURL https://your-project.pages.dev/
```

主题使用绝对资源链接。若仍保留原项目的 `baseURL`，浏览器会到原作者域名加载 CSS、JavaScript 和图片，导致页面没有样式或图片无法显示。

### 绑定自定义域名

先确认 `*.pages.dev` 地址显示正常，再在 Pages 项目的“自定义域”中添加域名并完成 DNS 设置。绑定成功后，将构建命令中的 `--baseURL` 改为：

```sh
--baseURL https://你的正式域名/
```

然后重新部署一次。

### 触发重新部署

Cloudflare Pages 已连接 Git 仓库时，每次推送到 `main` 都会自动部署：

```sh
git add README.md
git commit -m "docs: update deployment guide"
git push origin main
```

仅修改 Cloudflare 的构建设置不会重新构建旧版本。修改设置后，请在“部署”页面选择最新生产部署并点击“重试部署”，或向 `main` 推送一次新提交。

## 本地预览

安装 Hugo `0.122.0` 后，在仓库根目录执行：

```sh
mkdir -p themes/WebStack-Hugo
cp -R layouts static themes/WebStack-Hugo/
hugo --source exampleSite --themesDir ../themes
hugo server --source exampleSite --themesDir ../themes
```

浏览器打开终端显示的本地地址即可预览。

## 常见问题

### 页面显示 `{{ partial ... }}`

仓库源码被当作静态文件发布，Hugo 没有运行。检查 Pages 的构建命令和输出目录是否为上文的命令与 `exampleSite/public`。

### 页面只有文字、没有样式和图片

HTML 已生成，但资源链接指向了错误域名。检查构建命令中的 `--baseURL`，然后重新部署。

### 推送后没有自动部署

确认 Cloudflare Pages 的生产分支是 `main`、Git 集成状态正常，且本次推送确实进入了 `origin/main`。可在 Pages 的“部署”页面查看构建日志。
