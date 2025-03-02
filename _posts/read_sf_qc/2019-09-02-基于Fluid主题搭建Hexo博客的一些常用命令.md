---
title: "基于Fluid主题搭建Hexo博客的一些常用命令"
subtitle: "基于Fluid主题搭建Hexo博客的一些常用命令"
layout: post
author: "Hux"
header-style: text
hidden: true
tags:
  - SF (软件基础)
  - QC (Quickcheck)
  - Coq
  - 笔记
---



## 1、安装主题

### 1.1 获取最新版本

Hexo 5.0.0 版本以上，通过 npm 直接安装，进入博客目录执行命令：

```
$ npm install --save hexo-theme-fluid

BASH
```

然后在博客目录下创建 `_config.fluid.yml`，将主题的 `config.yml `内容复制过去。

### 1.2 指定主题

修改 Hexo 博客目录中的 `_config.yml`：

```
theme: fluid  # 指定主题

language: zh-CN  # 指定语言，会影响主题显示的语言，按需修改

YAML
```

### 1.3 创建关于页

首次使用主题的「关于页」需要手动创建：

```
$ hexo new page about

BASH
```

创建成功后修改 `/source/about/index.md`，添加 `layout` 属性。

修改后的文件示例如下：

```
---
title: 标题
layout: about
---

这里写关于页的正文，支持 Markdown, HTML

MARKDOWN
```

WARNING

`layout: about` 必须存在，并且不能修改成其他值，否则不会显示头像等样式。

### 1.4 更新主题

在博客目录下执行命令：

```
$ npm update --save hexo-theme-fluid

BASH
```

## 2、快速开始

### 创建一篇新的文章

```
$ hexo new "My New Post"

BASH
```

### 运行服务

```
$ hexo server

BASH
```

### 生成静态页面

```
$ hexo generate

BASH
```

### 部署到远程服务器

```
$ hexo deploy

BASH
```

## 3、常用语法

### 3,1 脚注

```
这是一句话[^1]
[^1]: 这是对应的脚注

MARKDOWN
```

### 3.2 便签

在 markdown 中加入如下的代码来使用便签：

```
{% note success %}
文字 或者 `markdown` 均可
{% endnote %}

MARKDOWN
```

可选便签：

[![可选便签](https://typora2007.oss-cn-beijing.aliyuncs.com/image_for_typora/image-20250217152817426.png)](https://nail-clipper.oss-cn-beijing.aliyuncs.com/img/image-20250217152817426.png)

可选便签



WARNING

使用时 `<div class="note note-primary"> <p><code>和</code></p> </div>` 需单独一行，否则会出现问题

### 3.3 行内标签

在 markdown 中加入如下的代码来使用 Label：

```
{% label primary @text %}

MARKDOWN
```

可选 Label：

[![可选Label](https://typora2007.oss-cn-beijing.aliyuncs.com/image_for_typora/image-20250217153049230.png)](https://nail-clipper.oss-cn-beijing.aliyuncs.com/img/image-20250217153049230.png)

可选Label



### 3.4 折叠块

使用折叠块，可以折叠代码、图片、文字等任何内容，可以在 markdown 中按如下格式：

```
{% fold info @title %}
需要折叠的一段内容，支持 markdown
{% endfold %}

MARKDOWN
```

info: 和行内标签类似的可选参数 title: 折叠块上的标题

### 3.5 勾选框

在 markdown 中加入如下的代码来使用 Checkbox：

```
{% cb text, checked?, incline? %}

MARKDOWN
```

text：显示的文字
checked：默认是否已勾选，默认 false
incline: 是否内联（可以理解为后面的文字是否换行），默认 false

### 3.6 按钮

你可以在 markdown 中加入如下的代码来使用 Button：

```
{% btn url, text, title %}

MARKDOWN
```

url：跳转链接
text：显示的文字
title：鼠标悬停时显示的文字（可选）

### 3.7 组图

如果想把多张图片按一定布局组合显示，你可以在 markdown 中按如下格式：

```
{% gi total n1-n2-... %}
  ![](url)
  ![](url)
  ![](url)
  ![](url)
  ![](url)
{% endgi %}

MARKDOWN
```

total：图片总数量，对应中间包含的图片 url 数量
n1-n2-…：每行的图片数量，可以省略，默认单行最多 3 张图，求和必须相等于 total，否则按默认样式

如下图为 `{% gi 5 3-2 %}` 示例，代表共 5 张图，第一行 3 张图，第二行 2 张图。

[![image-20250217153534894](https://typora2007.oss-cn-beijing.aliyuncs.com/image_for_typora/image-20250217153534894.png)](https://nail-clipper.oss-cn-beijing.aliyuncs.com/img/image-20250217153534894.png)

image-20250217153534894



1. 参考资料：https://hexo.fluid-dev.com/docs/guide/ [↩](https://wxwdaydayup.top/2025/02/05/基于Fluid主题搭建Hexo博客的一些常用命令/#fnref:1)





