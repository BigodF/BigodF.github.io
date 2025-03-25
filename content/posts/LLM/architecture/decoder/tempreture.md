---
title: "tempreture"
date: 2025-03-20T22:31:10+08:00
lastmod: 2025-03-20T22:31:10+08:00
author: ["Bigodf"]

tags:
- LLM
- decoder
- tempreture

description: "" # 文章描述，与搜索优化相关
summary: "" # 文章简单描述，会展示在主页
weight: # 输入1可以顶置文章，用来给文章展示排序，不填就默认按时间排序
slug: ""
draft: false # 是否为草稿
comments: true
showToc: true # 显示目录
TocOpen: true # 自动展开目录
autonumbering: true # 目录自动编号
hidemeta: false # 是否隐藏文章的元信息，如发布日期、作者等
disableShare: true # 底部不显示分享栏
searchHidden: false # 该页面可以被搜索到
showbreadcrumbs: true #顶部显示当前路径
mermaid: true
cover:
    image: ""
    caption: ""
    alt: ""
    relative: false
---

<!-- more -->

在decode阶段，使用softmax计算每个token的输出概率，如下

$$ 
    p_i=softmax(\frac{logit_i}{T}) 
$$

假设某一组$ logit $共有$ N $个元素，其中$ l_i、l_j $为任意两个元素，其原始概率记为$ p_i、p_j $，经过温度调整后的概率记为$ \hat{p}_i、\hat{p}_j $，以百分比的方式比较调整前后的概率差异。

$$ \begin{align}
        r_{i,j} = \dfrac{p_i}{p_j} &= \dfrac
            {\frac{e^{l_i}} {\sum^N_{k=0} {e^{l_k} } } } 
            {\frac{e^{l_j}} {\sum^N_{k=0} {e^{l_k} } } } \\
        &=\frac{e^{l_i}}{e^{l_j}} \\
        &=e^{l_i - l_j}
    \end{align}
$$

同理，调整后的概率比$ \hat{r}_{i,j} $如下：

$$ \hat{r}_{i,j} = e^{\frac{l_i - l_j} {T} } $$

讨论$ r_{i,j}、\hat{r}_{i,j} $的大小情况：

$$ \begin{aligned}
l_i > l_j &\& T>1 \implies \frac{l_i - l_j} {T} < l_i - l_j \implies \hat r_{i,j} < r_{i,j} \\
l_i > l_j &\& T<1 \implies \frac{l_i - l_j} {T} > l_i - l_j \implies \hat r_{i,j} > r_{i,j} \\
l_i < l_j &\& T>1 \implies \frac{l_i - l_j} {T} > l_i - l_j \implies \hat r_{i,j} > r_{i,j} \\
l_i < l_j &\& T<1 \implies \frac{l_i - l_j} {T} < l_i - l_j \implies \hat r_{i,j} < r_{i,j} \\
\end{aligned} $$

另一种解释，对于$ f(x)=e^x $来说，对$ x $进行放缩$ T $倍：

$$ \begin{align}
T>1 & \implies {x \over T} \text{相比}x更趋近于0 \\
    & \implies f(x)\text{更趋近于}e^0=1 \\
T<1 & \implies {x \over T} \text{相比}x更趋近于-\infty、+\infty \\
    & \implies f(x)\text{更趋近于}e^{-\infty}、e^{+\infty} \\
\end{align} $$

由此可以得出：

+ $ T>1 $对元素差异有缩小效果，对于模型来说，输出更随机。
+ $ T<1 $对元素差异有放大效果，对于模型来说，输出更确定。



