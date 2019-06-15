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

mathjax与Tex语法基本相同。具体可以参数\[kramdown syntax](https://kramdown.gettalong.org/syntax.html#math-blocks)。

## 2.1 行内公式实例

公式出现在一段文字当中，欧拉公式$$e^{i\pi}+1=0$$是世界上最完美的公式。

## 2.2 行间公式实例

“&”对齐符号，“\\”换行显示，\array显示矩阵等。

```
$$
\begin{align*}
  & \phi(x,y) = \phi \left(\sum_{i=1}^n x_ie_i, \sum_{j=1}^n y_je_j \right)
  = \sum_{i=1}^n \sum_{j=1}^n x_i y_j \phi(e_i, e_j) = \\
  & (x_1, \ldots, x_n) \left( \begin{array}{ccc}
      \phi(e_1, e_1) & \cdots & \phi(e_1, e_n) \\
      \vdots & \ddots & \vdots \\
      \phi(e_n, e_1) & \cdots & \phi(e_n, e_n)
    \end{array} \right)
  \left( \begin{array}{c}
      y_1 \\
      \vdots \\
      y_n
    \end{array} \right)
\end{align*}
$$
```

$$
\begin{align*}
  & \phi(x,y) = \phi \left(\sum_{i=1}^n x_ie_i, \sum_{j=1}^n y_je_j \right)
  = \sum_{i=1}^n \sum_{j=1}^n x_i y_j \phi(e_i, e_j) = \\
  & (x_1, \ldots, x_n) \left( \begin{array}{ccc}
      \phi(e_1, e_1) & \cdots & \phi(e_1, e_n) \\
      \vdots & \ddots & \vdots \\
      \phi(e_n, e_1) & \cdots & \phi(e_n, e_n)
    \end{array} \right)
  \left( \begin{array}{c}
      y_1 \\
      \vdots \\
      y_n
    \end{array} \right)
\end{align*}
$$

