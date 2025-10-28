删除博客目录下的 `hugo.toml`，然后将主题附带的的 `hugo.yaml` 复制到博客根目录

```bash
rm hugo.toml && cp themes/stack/exampleSite/hugo.yaml ./
```

以下是我随便改的配置文件，其中头像放在了 `assets/img/avatar.jpg`，配置文件写了 `img/avatar.jpg`，相对于 `assets` 文件夹的

改完之后要使用 hugo 重新生成静态页面

```bash
hugo -D -d /srv/http/yumeka.blog 
```

```yaml
baseurl: https://yumeka.blog
languageCode: zh-cn
theme: stack
title: "Yumeka ✨"
copyright: "© 2025 Yumeka"

DefaultContentLanguage: zh-cn
hasCJKLanguage: true

services:
  disqus:
    shortname: "hugo-theme-stack"
  googleAnalytics:
    id:

pagination:
  pagerSize: 3

permalinks:
  post: /p/:slug/
  page: /:slug/

params:
  mainSections:
    - post
  featuredImageField: image
  rssFullContent: true
  favicon: /favicon.ico

  footer:
    since: 2020
    customText:

  dateFormat:
    published: Jan 02, 2006
    lastUpdated: Jan 02, 2006 15:04 MST

  sidebar:
    emoji: 🍥
    subtitle: "这里是魔法少女梦花酱！"
    avatar:
      enabled: true
      local: true
      src: img/avatar.jpg

  article:
    math: false
    toc: true
    readingTime: true
    license:
      enabled: true
      default: "Licensed under CC BY-NC-SA 4.0"

  comments:
    enabled: true
    provider: disqus

    disqusjs:
      shortname:
      apiUrl:
      apiKey:
      admin:
      adminLabel:

    utterances:
      repo:
      issueTerm: pathname
      label:

    beaudar:
      repo:
      issueTerm: pathname
      label:
      theme:

    remark42:
      host:
      site:
      locale:

    vssue:
      platform:
      owner:
      repo:
      clientId:
      clientSecret:
      autoCreateIssue: false

    # Waline 评论配置（如不使用可留空或注释）
    waline:
      serverURL:
      lang: zh-CN
      pageview: true
      emoji:
        - https://unpkg.com/@waline/emojis@1.0.1/weibo
      requiredMeta:
        - name
        - email
        - url
      locale:
        admin: 管理员
        placeholder: 说点什么吧…

    twikoo:
      envId:
      region:
      path:
      lang:

    cactus:
      defaultHomeserverUrl: "https://matrix.cactus.chat:8448"
      serverName: "cactus.chat"
      siteName: ""

    giscus:
      repo:
      repoID:
      category:
      categoryID:
      locale: zh-CN
      mapping: pathname
      lightTheme: light
      darkTheme: dark_dimmed
      reactionsEnabled: 1
      emitMetadata: 0

    gitalk:
      owner:
      admin:
      repo:
      clientID:
      clientSecret:
      proxy:

    cusdis:
      host:
      id:

  widgets:
    homepage:
      - type: search
      - type: archives
        params:
          limit: 5
      - type: categories
        params:
          limit: 10
      - type: tag-cloud
        params:
          limit: 10
    page:
      - type: toc

  opengraph:
    twitter:
      site:
      card: summary_large_image

  defaultImage:
    opengraph:
      enabled: false
      local: false
      src:

  colorScheme:
    toggle: true
    default: auto

  imageProcessing:
    cover:
      enabled: true
    content:
      enabled: true

menu:
  main:
    - identifier: home
      name: "miniyu157.github.io"
      url: "https://miniyu157.github.io"
      weight: 0
    - identifier: about
      name: 关于
      url: /about/
    - identifier: archives
      name: 归档
      url: /archives/
    - identifier: tags
      name: 标签
      url: /tags/

  social:
    - identifier: github
      name: GitHub
      url: https://github.com/KlxPiao
      params:
        icon: brand-github
    - identifier: twitter
      name: Twitter
      url: https://twitter.com
      params:
        icon: brand-twitter

related:
  includeNewer: true
  threshold: 60
  toLower: false
  indices:
    - name: tags
      weight: 100
    - name: categories
      weight: 200

markup:
  goldmark:
    extensions:
      passthrough:
        enable: true
        delimiters:
          block:
            - - \[
              - \]
            - - $$
              - $$
          inline:
            - - \(
              - \)
    renderer:
      unsafe: true
  tableOfContents:
    startLevel: 2
    endLevel: 4
    ordered: true
  highlight:
    noClasses: false
    codeFences: true
    guessSyntax: true
    lineNoStart: 1
    lineNos: true
    lineNumbersInTable: true
    tabWidth: 4
```