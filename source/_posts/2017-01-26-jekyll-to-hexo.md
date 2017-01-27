---
title: 从Jekyll迁移到Hexo
date: 2017-01-26 19:06:32
categories: hexo
---

我的博客以前由Jekyll搭建，但从 Jekyll 2升级到 Jekyll 3后，我发现自己开始关注于Jekyll及其模板本身而不是博文内容本身。故转而使用Hexo来搭建博客，并更换了更加简洁明快的next主题模板。如果想从Jekyll转到Hexo可以参考本文。

<!-- more -->

# 安装Hexo

## 安装

```
npm install -g hexo-cli
```

node与hexo环境版本：

```bash
➜  ~ node -v
v7.2.1
➜  ~ hexo -v
hexo-cli: 1.0.2
os: Darwin 16.3.0 darwin x64
http_parser: 2.7.0
node: 7.2.1
v8: 5.4.500.44
uv: 1.10.1
zlib: 1.2.8
ares: 1.10.1-DEV
modules: 51
openssl: 1.0.2j
icu: 58.1
unicode: 9.0
cldr: 30.0.2
tz: 2016g
```

## 部署

```
hexo init quxionglie.com
cd quxionglie.com
npm install
```

安装后目录

```
.
├── _config.yml
├── node_modules
├── package.json
├── scaffolds
├── source
└── themes

4 directories, 2 files
```

<!-- more -->

# Hexo常用命令
## new - 生成新的blog

```
hexo new [layout] <title>
```

执行new命令，生成指定名称的文章至 source/posts/title.md。

其中layout是可选参数，默认值为post。scaffolds目录下的文件名称就是layout名称。你也可以添加自己的layout，方法就是添加一个文件即可，同时你也可以编辑现有的layout，比如post的layout默认是scaffolds/post.md

```
➜  ls -l scaffolds
total 24
-rw-r--r--  1 quxl  staff  33 Jan 26 19:18 draft.md
-rw-r--r--  1 quxl  staff  44 Jan 26 19:18 page.md
-rw-r--r--  1 quxl  staff  50 Jan 26 19:18 post.md

➜  cat scaffolds/post.md
---
title: {{ title }}
date: {{ date }}
tags:
---
```

参考：[writing](https://hexo.io/docs/writing.html)

## server - 启动服务器

```
hexo server
```

参考：[server](https://hexo.io/docs/server.html)

运行后，可以在浏览器上打开`http://localhost:4000`查看效果。启动后，hexo会监听文件变化并自动更新，所以没必要重启服务器。

## generate - 生成静态站点文件

```
hexo generate
```
生成静态文件并放于public目录(如果没有则生成)下。

参考：[generate](https://hexo.io/docs/commands.html#generate)

## deploy - 发布

将生成的静态文件发布到远程服务器上。

```
hexo deploy 
```

修改站点配置文件`_config.yml`，
```
deploy:
  type: git
  repo:
```

目前支持的类型有：
* Git

需安装[hexo-deployer-git](https://github.com/hexojs/hexo-deployer-git)插件。

```
npm install hexo-deployer-git --save
```

配置：

```
deploy:
  type: git
  repo: <repository url>
  branch: [branch]
  message: [message]   #Default to `Site updated: {{ now('YYYY-MM-DD HH:mm:ss') }}`
```

* Heroku
* Rsync
* OpenShift
* FTPSync

或直接调用下面命令自动生成部署:

```
#下面命令是同样的效果
hexo generate --deploy
hexo deploy --generate
```

参考：
[deploy](https://hexo.io/docs/commands.html#deploy)
[deployment](https://hexo.io/docs/deployment.html)


# 安装Next

参考：[next getting-started](http://theme-next.iissnan.com/getting-started.html)


## 下载Next主题

克隆最新版本。
在终端窗口下，定位到 Hexo 站点目录下。使用 Git checkout 代码：

```
cd quxionglie.com
git clone https://github.com/iissnan/hexo-theme-next themes/next
```

## 应用Next主题

与所有 Hexo 主题启用的模式一样。在根目录下找到`_config.yml`配置文件，找到 theme 字段，并将其值更改为 next ：

```
theme: next
```


## 选择 Scheme

Scheme 是 NexT 提供的一种特性，借助于 Scheme，NexT 为你提供多种不同的外观。同时，几乎所有的配置都可以 在 Scheme 之间共用。目前 NexT 支持三种 Scheme，他们是：

- `Muse` - 默认 Scheme，这是 NexT 最初的版本，黑白主调，大量留白
- `Mist` - Muse 的紧凑版本，整洁有序的单栏外观
- `Pisces` - 双栏 Scheme，小家碧玉似的清新

Scheme 的切换通过更改主题配置文件`themes/next/_config.yml`，搜索 scheme 关键字。 你会看到有三行 scheme 的配置，将你需用启用的 scheme 前面注释 # 即可。

选择 Mist Scheme

```
#scheme: Muse
scheme: Mist
#scheme: Pisces   
```

## 设置 语言

编辑 站点配置文件`_config.yml`， 将 language 设置成你所需要的语言。建议明确设置你所需要的语言，例如选用简体中文，配置如下：

```
language: zh-Hans
```

## 设置 菜单

菜单配置包括三个部分，第一是菜单项（名称和链接），第二是菜单项的显示文本，第三是菜单项对应的图标。 NexT 使用的是 Font Awesome 提供的图标， Font Awesome 提供了 600+ 的图标，可以满足绝大的多数的场景，同时无须担心在 Retina 屏幕下 图标模糊的问题。

编辑主题配置文件`themes/next/_config.yml`，修改以下内容：

* 设定菜单内容，对应的字段是 menu。 菜单内容的设置格式是：item name: link。其中 item name 是一个名称，这个名称并不直接显示在页面上，她将用于匹配图标以及翻译。
  
```yml
menu:
  home: /
  categories: /categories
  archives: /archives
  tags: /tags
  about: /about
  #sitemap: /sitemap.xml
  #commonweal: /404.html
```

# 主题设定

参考：[next 主题配置](http://theme-next.iissnan.com/theme-settings.html)

## 设置RSS

NexT 中 RSS 有三个设置选项，满足特定的使用场景。 更改 主题配置文件`themes/next/_config.yml`，设定 rss 字段的值：

- false：禁用 RSS，不在页面上显示 RSS 连接。
- 留空：使用 Hexo 生成的 Feed 链接。 你可以需要先安装 [hexo-generator-feed](https://github.com/hexojs/hexo-generator-feed) 插件。

```nodejs
npm install hexo-generator-feed --save
```

站点配置文件`_config.yml`添加如下内容：

```
feed:
  type: atom
  path: atom.xml
  limit: 20
  hub:
  content:
```

  

- 具体的链接地址：适用于已经烧制过 Feed 的情形。

## 添加标签页面

```
cd quxionglie.com
hexo new page tags
```

关闭tags页面的评论,设置comments为false：

vi source/tags/index.md

```
title: 标签
date: 2017-01-26 22:23:01
type: "tags"
comments: false
```

新建「标签」页面，并在菜单中显示「标签」链接。「标签」页面将展示站点的所有标签，若你的所有文章都未包含标签，此页面将是空的。 底下代码是一篇包含标签的文章的例子：

```
title: 标签测试文章
tags:
  - Testing
  - Another Tag
---
```




## 添加分类页面

新建「分类」页面，并在菜单中显示「分类」链接。「分类」页面将展示站点的所有分类，若你的所有文章都未包含分类，此页面将是空的。 底下代码是一篇包含分类的文章的例子：

```
hexo new page categories
```

vi source/categories/index.md

```
title: 分类测试文章
categories: Testing
---
```

## 添加about页面

```
hexo new page about
```

## 添加404页面

新建一个404.html文件，放到themes/next/source目录下，内容自定。

## 代码高亮

NexT 使用 `Tomorrow Theme` 作为代码高亮，共有5款主题供你选择。 NexT 默认使用的是 白色的 normal 主题，可选的值有 normal，night， night blue， night bright， night eighties：

更改 highlight_theme 字段，将其值设定成你所喜爱的高亮主题，例如：

```
# Code Highlight theme
# Available value:
#    normal | night | night eighties | night blue | night bright
# https://github.com/chriskempson/tomorrow-theme
highlight_theme: normal
```

## 站点建立时间

这个时间将在站点的底部显示，例如 © 2013 - 2015。 编辑 主题配置文件`themes/next/_config.yml`，新增字段 since。

```
# Specify the date when the site was setup
since: 2013
```

# 第三方服务
## 多说评论
修改主题配置文件`themes/next/_config.yml`中的duoshuo_shortname成你自己的。
```
# Duoshuo ShortName
duoshuo_shortname: quxionglie
```

## 百度统计
修改主题配置文件`themes/next/_config.yml`中的baidu_analytics成你自己的。

```
# Baidu Analytics ID
baidu_analytics: c687eb847b3d4c33885670e6d25d63e7 
```

# 集成
## Jekyll
移动所有 Jekyll `_posts` 目录文件到 `source/_posts` 目录.

修改站点配置文件`_config.yml`的`new_post_name`配置为:

```
new_post_name: :year-:month-:day-:title.md
```

```
# 修改前配置： new_post_name: :title.md
# 执行命令：
➜  quxionglie.com git:(master) ✗ hexo new page categories
INFO  Created: ~/Documents/quxionglie.com/source/categories/index.md

# 修改后配置： new_post_name: :year-:month-:day-:title.md
# 执行命令：
➜  quxionglie.com git:(master) ✗ hexo new "test1"        
INFO  Created: ~/Documents/quxionglie.com/source/_posts/2017-01-26-test1.md
```

# 完整配置
## 站点配置文件`_config.yml`

```yml
# Hexo Configuration
## Docs: https://hexo.io/docs/configuration.html
## Source: https://github.com/hexojs/hexo/

# Site
title: 阿烈的博客
subtitle:
description: Yesterday, you said tomorrow！
author: 阿烈
language: zh-Hans
timezone: Asia/Shanghai

# URL
## If your site is put in a subdirectory, set url as 'http://yoursite.com/child' and root as '/child/'
url: http://www.quxionglie.com
root: /
permalink: :year/:month/:day/:title/
permalink_defaults:

# Directory
source_dir: source
public_dir: public
tag_dir: tags
archive_dir: archives
category_dir: categories
code_dir: downloads/code
i18n_dir: :lang
skip_render:

# Writing
new_post_name: :year-:month-:day-:title.md   # migrate Jekyll
#new_post_name: :title.md # File name of new posts
default_layout: post
titlecase: false # Transform title into titlecase
external_link: true # Open external links in new tab
filename_case: 0
render_drafts: false
post_asset_folder: false
relative_link: false
future: true
highlight:
  enable: true
  line_number: true
  auto_detect: false
  tab_replace:

# Category & Tag
default_category: uncategorized
category_map:
tag_map:

# Date / Time format
## Hexo uses Moment.js to parse and display date
## You can customize the date format as defined in
## http://momentjs.com/docs/#/displaying/format/
date_format: YYYY-MM-DD
time_format: HH:mm:ss

# Pagination
## Set per_page to 0 to disable pagination
per_page: 10
pagination_dir: page

# Extensions
## Plugins: https://hexo.io/plugins/
## Themes: https://hexo.io/themes/
theme: next

# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type:

feed:
  type: atom
  path: atom.xml
  limit: 20
  hub:
  content:
```

## 主题配置文件`themes/next/_config.yml`

```yml
# ---------------------------------------------------------------
# Site Information Settings
# ---------------------------------------------------------------

# Put your favicon.ico into `hexo-site/source/` directory.
favicon: /favicon.ico

# Set default keywords (Use a comma to separate)
keywords: "Hexo, NexT"

# Set rss to false to disable feed link.
# Leave rss as empty to use site's feed link.
# Set rss to specific value if you have burned your feed already.
rss:

# Specify the date when the site was setup
since: 2012

# icon between year and author @Footer
authoricon: heart

# Footer `powered-by` and `theme-info` copyright
copyright: true

# Canonical, set a canonical link tag in your hexo, you could use it for your SEO of blog.
# See: https://support.google.com/webmasters/answer/139066
# Tips: Before you open this tag, remeber set up your URL in hexo _config.yml ( ex. url: http://yourdomain.com )
canonical: true

# Change headers hierarchy on site-subtitle (will be main site description) and on all post/pages titles for better SEO-optimization.
seo: false

# ---------------------------------------------------------------
# Menu Settings
# ---------------------------------------------------------------

# When running the site in a subdirectory (e.g. domain.tld/blog), remove the leading slash (/archives -> archives)
menu:
  home: /
  categories: /categories
  archives: /archives
  tags: /tags
  about: /about
  #sitemap: /sitemap.xml
  #commonweal: /404.html

# Enable/Disable menu icons.
# Icon Mapping:
#   Map a menu item to a specific FontAwesome icon name.
#   Key is the name of menu item and value is the name of FontAwsome icon. Key is case-senstive.
#   When an question mask icon presenting up means that the item has no mapping icon.
menu_icons:
  enable: true
  #KeyMapsToMenuItemKey: NameOfTheIconFromFontAwesome
  home: home
  about: user
  categories: th
  schedule: calendar
  tags: tags
  archives: archive
  sitemap: sitemap
  commonweal: heartbeat




# ---------------------------------------------------------------
# Scheme Settings
# ---------------------------------------------------------------

# Schemes
#scheme: Muse
scheme: Mist
#scheme: Pisces


# ---------------------------------------------------------------
# Font Settings
# - Find fonts on Google Fonts (https://www.google.com/fonts)
# - All fonts set here will have the following styles:
#     light, light italic, normal, normal intalic, bold, bold italic
# - Be aware that setting too much fonts will cause site running slowly
# - Introduce in 5.0.1
# ---------------------------------------------------------------
font:
  enable: true

  # Uri of fonts host. E.g. //fonts.googleapis.com (Default)
  host:

  # Global font settings used on <body> element.
  global:
    # external: true will load this font family from host.
    external: true
    family: Lato

  # Font settings for Headlines (h1, h2, h3, h4, h5, h6)
  # Fallback to `global` font settings.
  headings:
    external: true
    family:

  # Font settings for posts
  # Fallback to `global` font settings.
  posts:
    external: true
    family:

  # Font settings for Logo
  # Fallback to `global` font settings.
  # The `size` option use `px` as unit
  logo:
    external: true
    family:
    size:

  # Font settings for <code> and code blocks.
  codes:
    external: true
    family:
    size:




# ---------------------------------------------------------------
# Sidebar Settings
# ---------------------------------------------------------------


# Social Links
# Key is the link label showing to end users.
# Value is the target link (E.g. GitHub: https://github.com/iissnan)
#social:
  #LinkLabel: Link


# Social Links Icons
# Icon Mapping:
#   Map a menu item to a specific FontAwesome icon name.
#   Key is the name of the item and value is the name of FontAwsome icon. Key is case-senstive.
#   When an globe mask icon presenting up means that the item has no mapping icon.
social_icons:
  enable: true
  # Icon Mappings.
  # KeyMapsToSocalItemKey: NameOfTheIconFromFontAwesome
  GitHub: github
  Twitter: twitter
  Weibo: weibo


# Sidebar Avatar
# in theme directory(source/images): /images/avatar.jpg
# in site  directory(source/uploads): /uploads/avatar.jpg
avatar: /images/avatar.png


# Table Of Contents in the Sidebar
toc:
  enable: true

  # Automatically add list number to toc.
  number: true


# Creative Commons 4.0 International License.
# http://creativecommons.org/
# Available: by | by-nc | by-nc-nd | by-nc-sa | by-nd | by-sa | zero
#creative_commons: by-nc-sa
#creative_commons:


sidebar:
  # Sidebar Position, available value: left | right
  position: left
  #position: right

  # Sidebar Display, available value:
  #  - post    expand on posts automatically. Default.
  #  - always  expand for all pages automatically
  #  - hide    expand only when click on the sidebar toggle icon.
  #  - remove  Totally remove sidebar including sidebar toggler.
  display: post
  #display: always
  #display: hide
  #display: remove


# Blogrolls
#links_title: Links
#links_layout: block
#links_layout: inline
#links:
  #Title: http://example.com/


# ---------------------------------------------------------------
# Post Settings
# ---------------------------------------------------------------

# Automatically scroll page to section which is under <!-- more --> mark.
scroll_to_more: true

# Automatically excerpt description in homepage as preamble text.
excerpt_description: true

# Automatically Excerpt. Not recommand.
# Please use <!-- more --> in the post to control excerpt accurately.
auto_excerpt:
  enable: false
  length: 150

# Post meta display settings
post_meta:
  item_text: true
  created_at: true
  updated_at: false
  categories: true


# Wechat Subscriber
#wechat_subscriber:
  #enabled: true
  #qcode: /path/to/your/wechatqcode ex. /uploads/wechat-qcode.jpg
  #description: ex. subscribe to my blog by scanning my public wechat account



# ---------------------------------------------------------------
# Misc Theme Settings
# ---------------------------------------------------------------

# Custom Logo.
# !!Only available for Default Scheme currently.
# Options:
#   enabled: [true/false] - Replace with specific image
#   image: url-of-image   - Images's url
custom_logo:
  enabled: false
  image:


# Code Highlight theme
# Available value:
#    normal | night | night eighties | night blue | night bright
# https://github.com/chriskempson/tomorrow-theme
highlight_theme: normal


# ---------------------------------------------------------------
# Third Party Services Settings
# ---------------------------------------------------------------

# MathJax Support
mathjax:
  enable: false
  per_page: false
  cdn: //cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML


# Swiftype Search API Key
#swiftype_key:

# Baidu Analytics ID
baidu_analytics: 36175c8b1958d080e840f34fc69cf52d

# Duoshuo ShortName
duoshuo_shortname: quxionglie

# Disqus
#disqus_shortname:

# Hypercomments
#hypercomments_id:

# Gentie productKey
#gentie_productKey:

# Support for youyan comments system.
# You can get your uid from http://www.uyan.cc
#youyan_uid: your uid

# Baidu Share
# Available value:
#    button | slide
# Warning: Baidu Share does not support https.
#baidushare:
##  type: button

# Share
#jiathis:
# Warning: JiaThis does not support https.
#add_this_id:

# Share
#duoshuo_share: true

# Google Webmaster tools verification setting
# See: https://www.google.com/webmasters/
#google_site_verification:


# Google Analytics
#google_analytics:

# CNZZ count
#cnzz_siteid:

# Application Insights
# See https://azure.microsoft.com/en-us/services/application-insights/
# application_insights:

# Make duoshuo show UA
# user_id must NOT be null when admin_enable is true!
# you can visit http://dev.duoshuo.com get duoshuo user id.
duoshuo_info:
  ua_enable: true
  admin_enable: false
  user_id: 0
  #admin_nickname: Author


# Facebook SDK Support.
# https://github.com/iissnan/hexo-theme-next/pull/410
facebook_sdk:
  enable: false
  app_id:       #<app_id>
  fb_admin:     #<user_id>
  like_button:  #true
  webmaster:    #true

# Facebook comments plugin
# This plugin depends on Facebook SDK.
# If facebook_sdk.enable is false, Facebook comments plugin is unavailable.
facebook_comments_plugin:
  enable: false
  num_of_posts: 10  # min posts num is 1
  width: 100%       # default width is 550px
  scheme: light     # default scheme is light (light or dark)


# Show number of visitors to each article.
# You can visit https://leancloud.cn get AppID and AppKey.
leancloud_visitors:
  enable: false
  app_id: #<app_id>
  app_key: #<app_key>

# Show PV/UV of the website/page with busuanzi.
# Get more information on http://ibruce.info/2015/04/04/busuanzi/
busuanzi_count:
  # count values only if the other configs are false
  enable: false
  # custom uv span for the whole site
  site_uv: true
  site_uv_header: <i class="fa fa-user"></i>
  site_uv_footer:
  # custom pv span for the whole site
  site_pv: true
  site_pv_header: <i class="fa fa-eye"></i>
  site_pv_footer:
  # custom pv span for one page only
  page_pv: true
  page_pv_header: <i class="fa fa-file-o"></i>
  page_pv_footer:


# Tencent analytics ID
# tencent_analytics:


# Enable baidu push so that the blog will push the url to baidu automatically which is very helpful for SEO
baidu_push: false

# Google Calendar
# Share your recent schedule to others via calendar page
#
# API Documentation:
# https://developers.google.com/google-apps/calendar/v3/reference/events/list
calendar:
  enable: false
  calendar_id: <required>
  api_key: <required>
  orderBy: startTime
  offsetMax: 24
  offsetMin: 4
  timeZone:
  showDeleted: false
  singleEvents: true
  maxResults: 250

# Algolia Search
algolia_search:
  enable: false
  hits:
    per_page: 10
  labels:
    input_placeholder: Search for Posts
    hits_empty: "We didn't find any results for the search: ${query}"
    hits_stats: "${hits} results found in ${time} ms"



#! ---------------------------------------------------------------
#! DO NOT EDIT THE FOLLOWING SETTINGS
#! UNLESS YOU KNOW WHAT YOU ARE DOING
#! ---------------------------------------------------------------

# Motion
use_motion: true

# Fancybox
fancybox: true

# Canvas-nest
canvas_nest: false

# Script Vendors.
# Set a CDN address for the vendor you want to customize.
# For example
#    jquery: https://ajax.googleapis.com/ajax/libs/jquery/2.2.0/jquery.min.js
# Be aware that you should use the same version as internal ones to avoid potential problems.
# Please use the https protocol of CDN files when you enable https on your site.
vendors:
  # Internal path prefix. Please do not edit it.
  _internal: lib

  # Internal version: 2.1.3
  jquery:

  # Internal version: 2.1.5
  # See: http://fancyapps.com/fancybox/
  fancybox:
  fancybox_css:

  # Internal version: 1.0.6
  # See: https://github.com/ftlabs/fastclick
  fastclick:

  # Internal version: 1.9.7
  # See: https://github.com/tuupola/jquery_lazyload
  lazyload:

  # Internal version: 1.2.1
  # See: http://VelocityJS.org
  velocity:

  # Internal version: 1.2.1
  # See: http://VelocityJS.org
  velocity_ui:

  # Internal version: 0.7.9
  # See: https://faisalman.github.io/ua-parser-js/
  ua_parser:

  # Internal version: 4.6.2
  # See: http://fontawesome.io/
  fontawesome:

  # Internal version: 1
  # https://www.algolia.com
  algolia_instant_js:
  algolia_instant_css:

  # Internal version: 1.0.0
  # https://github.com/hustcc/canvas-nest.js
  canvas_nest:



# Assets
css: css
js: js
images: images

# Theme version
version: 5.1.0


```

