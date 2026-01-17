---
layout: post
title: Ước chung lớn nhất (UCLN) và Bội chung nhỏ nhất (BCNN)📝
date: 2025-11-02 06:19 +0700
categories: [number-theory]
tags: [handwriting, ucln, bcnn]
math: true
---

> thông tin trong bài blog này có thể chưa hoàn thiện và sẽ cập nhật ở tương lai

## Một số tính chất hữu ích trong CP
#### Tính chất 1
$$
\begin{aligned}
&u = \gcd(a, X) \quad &\Rightarrow\quad u \mid a\\
&v = \gcd(b, Y) \quad &\Rightarrow\quad v \mid b
\end{aligned}
$$
$$
\quad \Rightarrow \quad \gcd(u, v) \mid \gcd(a, b).
$$

Áp dụng với

$$
\begin{aligned}
&a = n \\
&b = n - 1
\end{aligned}
$$
$$
\quad \Rightarrow \quad \gcd(u, v) \mid \gcd(n, n-1) = 1
\;\Rightarrow\;
\gcd(u, v) = 1.
$$

(2 số nguyên liên tiếp luôn nguyên tố cùng nhau)

> [https://codeforces.com/problemset/problem/1732/A](https://codeforces.com/problemset/problem/1732/A)
{: .prompt-info }

## Ghi chép
{% include pdfviewer.html file="/assets/files/uclnvabcnn.pdf" %}