---
layout: post
title: Bài 3. Đếm cặp nguyên tố (PAIRPRIME)
date: 2025-11-02 12:12 +0700
categories: [practice, 28tech_number_theory]
tags: [nguyên tố]
comments: false
math: true
hidden: true
---

<div class="problem-link">
  🔗 <strong>Problem:</strong>
  <a href="https://oj.28tech.com.vn/problem/hsgsohoc03" target="_blank">
    [HSG_Số Học]. Bài 3. Đếm cặp nguyên tố (PAIRPRIME)
  </a>
</div>

## Ý tưởng

Trường hợp 1: $x$ hoặc $y$ là số nguyên tố $\quad \Rightarrow \quad$ Số cách chọn: $\text{prime_count} * (n - \text{prime_count})$

Trường hợp 2: $x$ và $y$ là số nguyên tố $\quad \Rightarrow \quad$ Số cách chọn: $\binom{n}{2} = \frac{\text{prime_count} * (\text{prime_count} + 1)}{2}$

$\quad \Rightarrow \quad$ Vậy số cách chọn là: $\text{prime_count} * (n - \text{prime_count})$ + $\frac{\text{prime_count} * (\text{prime_count} + 1)}{2}$ 

## Mã giải (C++)
<div class="code-link">
  <a href="https://github.com/minhtuan0312/competitive-programming/tree/f67abf0e77950bb20a8082116670023723d7b0cf/practice/number_theory/28tech_contest_chuyen_de_so_hoc_phan_1/problems/B%C3%A0i%201.%20S%E1%BB%91%20nguy%C3%AAn%20t%E1%BB%91%20(CHECKNT6K)" target="_blank">
    main.cpp
  </a>
</div>
