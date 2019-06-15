---
layout: post
title: Github Page上显示公式
categories: [Tips]
description: 基于Kramdown在Github Page上显示公式
keywords: Kramdown, Math formula
---

Github page上写博文实际上是利用Jekell自动显示Kramdown文件。在_config.yml文件中，明确指定了使用kramdown.
```
markdown: kramdown
kramdown:
    input: GFM
```
kramdown是对原生的markdown语法进行扩展的轻量化语言。在写博文的时候，不可避免需要书写数学公式。原生的markdown实际上是不支持公式显示的，但是kramdown确实支持的。

# 1. 配置head

如何不对相关的html配置文件进行修改的话，实际上网页上是不会按照kramdown的语法显示数学公式的，需要告诉浏览器用什么东西来编译这段公式块。因此，需要对相关文件进行配置。

配置\_includes\header.html文件，在**其<head>...<head>内的最后一行**，添加上下列程序。

```
<script type="text/javascript" src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>
```
这样，就可以用mathjax对公式进行显示。

# 2. 常用的mathjax公式格式

mathjax与Tex语法基本相同。

## 2.1 行内公式

公式出现在一段文字当中，欧拉公式$$e^{i\pi}+1=0$$世界上最完美的公式。