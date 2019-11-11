---
title: Hexo的基本使用
date: 2019-09-23 20:27:53
categories: Hexos
tags: hexo
---

## 创建博客

```
hexo new "博客标题"
```

## 发布博客

先清理缓存，一般可能会由于本地缓存而导致发布没有生效（可选）

```
hexo clean
```

发布和部署

```
hexo g -d
```

> 以上命令其实和：1. `hexo g`，2. `hexo d` 是一样的。

## 更换主题

推荐人气最佳的`next`主题，其他更换主题的方式都差不多的

* 1.安装

```
git clone https://github.com/theme-next/hexo-theme-next.git themes/next
```

* 2.修改

打开根目录下配置文件`_config.yml`, 找到`theme`项，修改为以下

```
theme: next
```

## 修改主题配置文件

### 一 修改主题布局

在根目录下

```
vi themes/next/_config.yml 
```

找到

```
# Schemes
#scheme: Muse
#scheme: Mist
scheme: Pisces
#scheme: Gemini
```

根据自己需要，打开相应的`scheme`项，即可浏览相应的效果。我比较喜欢`scheme: Pisces`，左侧是栏目，右侧显示内容。

### 二 增加`标签和分类`

在根目录下

```
vi themes/next/_config.yml 
```

找到

```
menu:
  home: / || home
  #about: /about/ || user
  tags: /tags/ || tags
  categories: /categories/ || th
  archives: /archives/ || archive
  #schedule: /schedule/ || calendar
  #sitemap: /sitemap.xml || sitemap
  #commonweal: /404/ || heartbeat
```

打开主题配置文件里面的`menu项`，接着打开相应的子配置项`tags`和`categories`。

#### 2.1 分类

- 2.1.1 新建一个页面，命名为 `categories` 。命令如下：

```
 hexo new page categories
```

- 2.1.2 编辑刚新建的页面，将页面的类型设置为 `categories` ，主题将自动为这个页面显示所有分类。

```
 title: 分类
 date: 2019-10-07 21:27:11
 type: "categories"
 ---
```

- 2.1.3 关联博客

在文章头部，添加

```
categories: iOS
```

这样就关联到了`iOS`分类了。

- 2.1.4 二级分类

有时候可能需要添加二级分类，比如`iOS`下面有`Swift`和`Objective-C`， 那么二级分类也很简单，直接在文章的头部的分类中依次添加一级分类、二级分类。比如

`iOS`分类下有一个`Swift`分类

```
---
title: Hello, swift!
date: 2019-11-04 20:01:18
categories: 
- iOS
- Swift
tags: Swift
---
```

`iOS`分类下再有一个`Objective-C`分类

```
---
title: Hello, Objective-C!
date: 2019-11-04 20:01:18
categories: 
- iOS
- Objective-C
tags: OC
---
```

#### 2.2 标签

- 2.2.1 新建一个页面，命名为`tags` 。命令如下：

```
hexo new page "tags"
```

- 2.2.2 编辑刚新建的页面，将页面的类型设置为 `tags` ，主题将自动为这个页面显示标签云。

```
 title: 标签
 date: 2019-10-07 21:27:11
 type: "tags"
 ---
```

### 三 添加头像


编辑主题的 `_config.yml`，新增字段`avatar`， 值设置成头像的链接地址。

其中，头像的链接地址可以是：
	1	完整的互联网 `URL`，例如：`https://avatars1.githubusercontent.com/u/32269?v=3&s=460`
	2	站点内的地址，例如：
	◦	`/uploads/avatar.jpg` 需要将你的头像图片放置在 站点的 `source/uploads/`（可能需要新建uploads目录）
	◦	`/images/avatar.jpg` 需要将你的头像图片放置在 主题的 `source/images/` 目录下。

在根目录下

```
vi themes/next/_config.yml 
```

找到并打开`avatar: /images/avatar.jpg`

```
# Sidebar Avatar
# in theme directory(source/images): /images/avatar.gif
# in site  directory(source/uploads): /uploads/avatar.gif
avatar: /images/avatar.jpg
```

### 四 文章截断

在`themes/next/_config.yml`下开启

```
auto_excerpt:
  enable: true
  length: 150
```


本文已涉及或未涉及的配置详情可见：[hexo-theme-next主题在github的使用配置文档说明](https://github.com/iissnan/hexo-theme-next/wiki)




