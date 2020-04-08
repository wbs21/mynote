[Hugo-框架学习](https://segmentfault.com/a/1190000016078760)

[golang](https://segmentfault.com/t/golang)[hugo](https://segmentfault.com/t/hugo)

## 概念

hugo是由Go语言实现的静态网站生成器。简单、易用、高效、易扩展、快速部署。

主要用于个人Blog、项目文档、初创公司站点构建。

hugo的下载安装很简单，可以参考官方的[quickstart](https://gohugo.io/getting-started/quick-start/)，本文主要介绍一下hugo框架的**基本概念**和**工作原理**。

## 目录结构

命令：hugo new site blog

```
blog
├── archetypes
├── config.toml
├── content
├── data
├── layouts
├── static
├── themes
└── public
```

**archetypes**

在通过hugo new xxx 创建内容页面的时候，默认情况下hugo会创建date、title等front matter，可以通过在archetypes目录下创建文件，设置自定义的front matter。

**config.toml**

所有的hugo站点都有一个全局配置文件，用来配置整个站点的信息，hugo默认提供了跟多配置指令。

**content**

站点下所有的内容页面，也就是我们创建的md文件都在这个content目录下面。

**data**

data目录用来存储网站用到一些配置、数据文件。文件类型可以是yaml|toml|json等格式。

**layouts**

存放用来渲染content目录下面内容的模版文件，模版.html格式结尾，layouts可以同时存储在项目目录和themes/<THEME>/layouts目录下。

**static**

用来存储图片、css、js等静态资源文件。

**themes**

用来存储主题，主题可以方便的帮助我们快速建立站点，也可以方便的切换网站的风格样式。

**public**

hugo编译后生成网站的所有文件都存储在这里面，把这个目录放到任意web服务器就可以发布网站成功。

## 页面绑定

### Page bundles

hugo中的内容组织是依赖Page Bundles来管理的。Page Bundles包括Leaf Bundle（没有子节点）和Branch Bundle（home page, section, taxonomy terms, taxonomy list）两类。

|          | Leaf Bundle | Branch Bundle               |
| -------- | ----------- | --------------------------- |
| 索引文件 | index.md    | _index.md                   |
| 布局类型 | single      | list                        |
| 嵌套     | 不允许嵌套  | 允许Leaf或Branch Bundle嵌套 |

### Section

section是基于content/目录下的组织结构定义的页面集合。

content/ 下的第一级子目录都是一个section。如果想让一个子目录成为section，需要在目录下面定义_index.md文件。 所有的section构成一个section tree。

```
content
└── blog        <-- Section, 因为是content的子目录
    ├── funny-cats
    │   ├── mypost.md
    │   └── kittens         <-- Section, 因为包含_index.md
    │       └── _index.md
    └── tech                <-- Section, 因为包含 _index.md
        └── _index.md
```

如果section tree 需要可导航，至少最底层的部分需要定义一个_index.md文件。

### front matter

front matter 用来配置文章的标题、时间、链接、分类等元信息，提供给模板调用

```
+++
title = "post title"
description = "description."
date = "2018-08-20"
tags = [ "tag1", "tag2", "tag3"]
categories = ["cat1","cat2"]
weight = 20
+++
```

## 模版

Hugo以go语言的html/template库作为模版引擎，将内容通过template渲染成html，模版作为内容和显示视图之间的桥梁。

hugo由三种类型的模版：single、list and partial。

Hugo有一套自己的模版查找机制，如果找不到与内容完全匹配的模板，它将向上移动一级并从那里搜索。直到找到匹配的模板或用完模板来尝试。如果找不到模板，它将使用该站点的默认模板。

### Single Template

single template 用于渲染页面内容。

### List Template

list template 用于渲染一组相关内容，例如一个站点下所有内容，一个目录下的内容；

homepage 也就是_index.md，是一个特殊类型的list template，homepage实际上就是一个站点所有内容的入口。

### partial Template

partial template 可以被其他模版引用，实际上可以理解为模版级别的组件，例如页面头部、页面底部等。

### 基础模版查询规则

基本模版是指baseof.html的查找规则

```
1. /layouts/section/<TYPE>-baseof.html
2. /themes/<THEME>/layouts/section/<TYPE>-baseof.html
3. /layouts/<TYPE>/baseof.html
4. /themes/<THEME>/layouts/<TYPE>/baseof.html
5. /layouts/section/baseof.html
6. /themes/<THEME>/layouts/section/baseof.html
7. /layouts/_default/<TYPE>-baseof.html
8. /themes/<THEME>/layouts/_default/<TYPE>-baseof.html
9. /layouts/_default/baseof.html
10. /themes/<THEME>/layouts/_default/baseof.html
```

### 页面模版查询规则

#### kind

判断页面是single page 还是 list page？

如果是single page，会选择模版_default/single.html

如果是list page（section listings, home page, taxonomy lists, taxonomy terms），会选择模版_default/list.html

#### Output Format

根据输出格式的名称和后缀，选择匹配的模版。例如输出格式是rss，后缀是.html,首先看有没有匹配的index.rss.html格式的模版。

#### Language

根据站点设置的语言选择匹配的模版，比如，站点的语言为fr，模版匹配的优先级是：index.fr.amp.html > index.amp.html > index.fr.html

#### Layout

可以在页面头部设置front matter：layout，设置页面用指定的模版进行渲染，例如，页面在目录posts（默认section）下面，layout=custom，查找规则如下：

> - layouts/posts/custom.html
> - layouts/posts/single.html
> - layouts/_default/custom.html
> - layouts/_default/single.html

#### type

如果在页面的头部设置了属性type属性，例如type=event，查找规则如下：

> - layouts/event/custom.html
> - layouts/event/single.html
> - layouts/events/single.html
> - layouts/_default/single.html

## 语法

### 基础语法

[go-template 基础语法](https://themes.gohugo.io/theme/minimage/post/goisforlovers/)

### block

在父模版中通过`{{ block "xxx"}}` 定义一个块，在子模块中可以通过`{{define "xxx"}}` 定义的内容进行替换。

{{< button theme="info">}} 模版定义 {{< /button >}}

```
{{ define "chrome/header.html" }}
  <div>{{ .Site.Params.author}}</div>
{{ end }}
```

### 模版引用

*方法一* ：partial（推荐使用）

用于引入定义的局部模版，局部模版位置必须在themes/layouts/partials 或者layouts/partials目录下。

```
{{ partial "chrome/header.html" . }}
```

*方法二* ：template

template 在一些比较老的版本中用于引入定义的局部模版，现在在内部模版中仍然使用。

```
{{ template "chrome/header.html" . }}
```

### {{- xxx -}}

-用于去除空格，例如：

1. {{- xxx}用于去除{{- 前边的空格
2. {{ xxx -}用于去除-}}后边的空格
3. {{- xxx -}}用于去除两边的空格

### Paginator

.Paginator主要用来构建菜单，只能在homePage、listPage中使用。

```
{{ range .Paginator.Pages }}
   {{ .Title }}
{{ end }}
```

## 短代码

短代码（shortcodes）相当于一些自定义模版，通过在md文档中引用，生成代码片段，类似于一些js框架中的组件。

短代码必须在themes/layouts/partials 或者layouts/partials目录下定义。

短代码在模版文件中引用是无效的，只能在content目录下的*.md文件中引用。

*引用方式*

```
{{%/* msshortcodes params1="xxx" */%}}**this** is a text{{%/* /msshortcodes */%}}
{{</* msshortcodes params1="xxx"  */>}} **Yeahhh !** is a text {{</* /msshortcodes */>}}
```

1. % 代表短代码中的内容需要进一步渲染
2. < 代表短代码中间的内容项不需要进一步渲染

## taxonomy

hugo中通过taxonomy来实现用户对内容的分组。taxonomy需要在站点配置文件中定义：

```
[taxonomies]
  category = "categories"
  tag = "tags"
  series = "series"
```

默认情况下，hugo提供了category、tag两个分类，不需要用户在配置文件中定义，但如果还有其他的taxonomy定义，则默认的tag、category也需要定义；

*使用方式*

1. 在站点配置文件中添加taxonomy,例如：series
2. 定义taxonomy list template；例如，在layouts/taxonomy/series.html
3. 在内容文件的front matter中设置term；例如，series = ["s1","s2"]
4. 访问taxonomy列表，例如，localhost:1313/series/s1

## 变量

Hugo提供了很多不同类型变量用于创建模版，构建站点。

### Site

大部分站点变量定义在站点配置文件中（config.[yaml|toml|json] ）

.Site.Menus:站点下的所有菜单

.Site.Pages:站点下的所有页面

.Site.Params: 站点下的参数

### Page

页面级参数都定义在页面头部的front matter中，例如：

```
+++
title = "Hugo"
date = 2018-08-13T18:29:20+08:00
description = ""
draft = false
weight = 10
+++
```

*使用方式*

```
{{ .Params.xxx }} 或者是 {{ .Site.Params.xxx }}
```

阅读 3.6k更新于 2018-08-20