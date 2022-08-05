---
title: "Master Theorem"
date: 2022-06-14T15:00:00+08:00
categories: ["Algorithm"]
summary: "递归问题时间复杂度分析"
---

## Master Theorem

$$
f(x)=\begin{cases}
    \Theta(n^{log_b a}) & f(n)=\Omicron(n^c) & c < \log_b a \\\
    \Theta(n^c log^{k+1} n) & f(n)=\Theta(n^c log^k n) & c = \log_b a \\\
    \Theta(f(n)) & f(n)=\Omega(n^c) & c > \log_b a
\end{cases}
$$

## 视频资料

[MIT Open Course](https://www.youtube.com/watch?v=09vU-wVwW3U)
