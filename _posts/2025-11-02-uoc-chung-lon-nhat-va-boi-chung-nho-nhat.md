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
Giả sử $u = \gcd(a, X)$ và $v = \gcd(b, Y)$.

Khi đó $u \mid a$ và $v \mid b$.
Suy ra $\gcd(u, v) \mid \gcd(a, b)$.

Đặc biệt, nếu $\gcd(a, b) = 1$ thì $\gcd(u, v) = 1$.

Áp dụng với <b>$\quad a = n,\quad b = n-1\quad $</b> ta có
$\gcd(n, n - 1) = 1 \Rightarrow \gcd(u, v) = 1$.

> [https://codeforces.com/problemset/problem/1732/A](https://codeforces.com/problemset/problem/1732/A)
{: .prompt-info }

## Ghi chép
{% include pdfviewer.html file="/assets/files/uclnvabcnn.pdf" %}