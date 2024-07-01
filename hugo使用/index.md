# Hugo使用


#### 以下是创建第一篇文章的方法:
`hugo new posts/first_post.md`

注意
默认情况下, 所有文章和页面均作为草稿创建. 如果想要渲染这些页面, 请从元数据中删除属性 draft: true, 设置属性 draft: false

#### 在本地启动网站
`hugo serve`

请访问 [http://localhost:1313](http://localhost:1313)

<img src="/images/hugo使用.png" alt="示例图片" width="500" height="300">

#### 预览尚未正式发布的内容
`hugo serve -D`

#### Hugo 的运行环境

这个命令会启动一个本地服务器，生成并展示包含草稿文章的整个网站。这在开发阶段非常有用，因为它允许你预览尚未正式发布的内容。

`hugo serve` 的默认运行环境是 `development`, 而 `hugo` 的默认运行环境是 `production`.

由于本地 `development` 环境的限制, 评论系统, CDN 和 fingerprint 不会在 `development` 环境下启用.

你可以使用 `hugo serve -e production` 命令来开启这些特性.

### 构建网站
`hugo`

会生成一个 public 目录, 其中包含你网站的所有静态内容和资源. 现在可以将其部署在任何 Web 服务器上.
