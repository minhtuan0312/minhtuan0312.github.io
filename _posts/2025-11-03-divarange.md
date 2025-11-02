---
layout: post
title: Bài 5. Bội trong đoạn (DIVARANGE)
date: 2025-11-03 01:39 +0700
categories: [practice, 28tech_number_theory]
tags: [bao hàm - loại trừ]
comments: false
math: true
hidden: true
---

<div class="problem-link">
  🔗 <strong>Problem:</strong>
  <a href="https://oj.28tech.com.vn/problem/hsgsohoc05" target="_blank">
    [HSG_Số Học]. Bài 5. Bội trong đoạn (DIVARANGE)
  </a>
</div>

## Ý tưởng

Cho dãy số $a_1, a_2, a_3$, giả sử:

$$
\begin{aligned}
A &: \text{Tập các số chia hết cho } a_1,\\
B &: \text{Tập các số chia hết cho } a_2,\\
C &: \text{Tập các số chia hết cho } a_3.
\end{aligned}
$$

Khi đó ta định nghĩa phép giao như sau:

$A \cap B:$ tập các số chia hết cho cả $a_1$ và $a_2 =$ tập các số chia hết cho bcnn$(a_1, a_2)$

$A \cap B \cap C:$ tập các số chia hết cho cả $a_1$, $a_2$ và $a_3 =$ tập các số chia hết cho bcnn$(a_1, a_2)$

Tương tự với phép hợp:

$A \cup B:$ tập các số chia hết cho $a_1$ hoặc $a_2$

$A \cup B \cup C:$ tập các số chia hết cho $a_1$ hoặc $a_2$ hoặc $a_3$

$\Rightarrow \quad$ Rõ ràng, đề bài yêu cầu chúng ta làm việc với phép giao $\quad \Rightarrow \quad$ <b>Nguyên lý bù trừ</b>

$$
\begin{align*}
|A \cup B \cup C| &= |A| + |B| + |C| - |A \cap B| - |A \cap C| - |B \cap C| + |A \cap B \cap C| \\[1mm]
\Rightarrow |A \cap B \cap C| &= |A \cup B \cup C| - |A| - |B| - |C| + |A \cap B| + |A \cap C| + |B \cap C|
\end{align*}
$$

$$
\begin{align*}
|A| &= \lfloor \frac{R}{a_1} \rfloor - \lfloor \frac{L - 1}{a_1} \rfloor
\end{align*}
$$

...

$$
\begin{align*}
|A \cap B| &= \lfloor \frac{R}{\text{bcnn}(a_1, a_2)} \rfloor - \lfloor \frac{L - 1}{\text{bcnn}(a_1, a_2)} \rfloor
\end{align*}
$$

## Mã giải (C++)
<div class="code-link">
  <a href="https://github.com/minhtuan0312/competitive-programming/tree/27f9022bdaf0da007898a08f2da037cb83d6f0d0/practice/number_theory/28tech_contest_chuyen_de_so_hoc_phan_1/problems/bai4_SEMIPRIME" target="_blank">
    main.cpp
  </a>
</div>
