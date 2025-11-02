---
layout: post
title: Bài 4. Nửa nguyên tố (SEMIPRIME)
date: 2025-11-03 01:12 +0700
categories: [practice, 28tech_number_theory]
tags: [nguyên tố]
comments: false
math: true
hidden: true
---

<div class="problem-link">
  🔗 <strong>Problem:</strong>
  <a href="https://oj.28tech.com.vn/problem/hsgsohoc04" target="_blank">
    [HSG_Số Học]. Bài 4. Nửa nguyên tố (SEMIPRIME)
  </a>
</div>

## Ý tưởng

Ta có $n = x*y$ với x và y là số nguyên tố, giả sử $x \leq y$ thì:

$$
x^2 \leq xy \leq n
$$

Qua đó, ta suy ra:

$$
\begin{align*}
x &\leq \sqrt{n} \\[1mm]
y &\ge x
\end{align*}
$$

Lồng for thì độ phức tạp tổng thể chỉ mất $O(n)$, hoàn toàn AC được

## Mã giải (C++)
<div class="code-link">
  <a href="https://github.com/minhtuan0312/competitive-programming/tree/27f9022bdaf0da007898a08f2da037cb83d6f0d0/practice/number_theory/28tech_contest_chuyen_de_so_hoc_phan_1/problems/bai4_SEMIPRIME" target="_blank">
    main.cpp
  </a>
</div>
