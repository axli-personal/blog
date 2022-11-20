---
title: "Master Theorem"
date: 2022-06-14T15:00:00+08:00
categories: ["Algorithm"]
---

Master theorem can solve common divide-and-conquer recurrences.

<!--more-->

## Master Theorem

Recurrence: T(n) = aT($\frac{n}{b}$) + f(n).

$$
T(n)=\begin{cases}
    \Theta(n^{log_b a}) & f(n)=\Omicron(n^c) & c < \log_b a \\\
    \Theta(n^c log^{k+1} n) & f(n)=\Theta(n^c log^k n) & c = \log_b a \\\
    \Theta(f(n)) & f(n)=\Omega(n^c) & c > \log_b a
\end{cases}
$$
