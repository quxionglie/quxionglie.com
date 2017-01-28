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
## 评论系统
### 多说评论
修改主题配置文件`themes/next/_config.yml`中的duoshuo_shortname成你自己的。
```
duoshuo_shortname: your-duoshuo-shortname
```

备注：多说官方对头像和表情的 HTTPS 的支持并不完美。选择需谨慎！

### Disqus
修改主题配置文件`themes/next/_config.yml`中的 disqus_shortname 成你自己的。

```
disqus_shortname: your-disqus-shortname
```


## 数据统计与分析
### 百度统计
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

