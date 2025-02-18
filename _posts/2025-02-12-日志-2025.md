---
layout:     post
title:      "git,github和vscode"
subtitle:   " \"Hello World, Hello 2025\""
date:       2025-02-18 12:00:01
author:     "Hux"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Meta
---


> “新的一年，新的开始. ”




## 正文

接下来说说搭建这个博客的技术细节。  

正好之前就有关注过 [GitHub Pages](https://pages.github.com/) + [Jekyll](http://jekyllrb.com/) 快速 Building Blog 的技术方案，非常轻松时尚。

其优点非常明显：

* **Markdown** 带来的优雅写作体验
* 非常熟悉的 Git workflow ，**Git Commit 即 Blog Post**
* 利用 GitHub Pages 的域名和免费无限空间，不用自己折腾主机
	* 如果需要自定义域名，也只需要简单改改 DNS 加个 CNAME 就好了 
* Jekyll 的自定制非常容易，基本就是个模版引擎


本来觉得最大的缺点可能是 GitHub 在国内访问起来太慢，所以第二天一起床就到 GitCafe(Chinese GitHub Copy，现在被 Coding 收购了) 迁移了一个[镜像](http://huxpro.coding.me)出来，结果还是巨慢。

哥哥可是个前端好嘛！ 果断开 Chrome DevTool 查了下网络请求，原来是 **pending 在了 Google Fonts** 上，页面渲染一直被阻塞到请求超时为止，难怪这么慢。  
忍痛割爱，只好把 Web Fonts 去了（反正超时看到的也只能是 fallback ），果然一下就正常了，而且 GitHub 和 GitCafe 对比并没有感受到明显的速度差异，虽然 github 的 ping 值明显要高一些，达到了 300ms，于是用 DNSPOD 优化了一下速度。





## 后记

博客框架源自github开源博客项目，感谢黄玄的分享和开源。

回顾这个博客的诞生，纯粹是出于个人兴趣。在知乎相关问题上回答并获得一定的 star 后，我决定把这个博客主题当作一个小小的开源项目来维护。

在经历 v1.0 - v1.5 的蜕变后，这个博客主题愈发完整，不但增加了诸多 UI 层的优化（opinionated）；在代码层面，更加丰富的配置项也使得这个主题拥有了更好的灵活性与可拓展性。而作为一个开源项目，我也积极的为其完善文档与解决 issue。

如果你恰好逛到了这里，希望你也能喜欢这个博客主题。

—— Nickwei 后记于 2025.02

