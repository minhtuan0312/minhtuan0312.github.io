---
layout: post
title: Bài 2. Số nguyên tố bị thiếu (MISSINGPRIME)
date: 2025-11-02 12:12 +0700
categories: [practice, number_theory, 28tech]
tags: [linear sieve, nguyên tố]
comments: false
math: true
hidden: true
---

<div class="problem-link">
  🔗 <strong>Problem:</strong>
  <a href="https://oj.28tech.com.vn/problem/hsgsohoc02" target="_blank">
    [HSG_Số Học]. Bài 2. Số nguyên tố bị thiếu (MISSINGPRIME)
  </a>
</div>

## Linear sieve

```c++
const int limN = 1e6 + 5;
int lp[limN]; // lp[i] = ước nguyên tố nhỏ nhất của i
vector<int> primes;

//lp[i] == 0 => i là nguyên tố => số nguyên tố bé nhất trong phân tích của i nó là i
//lp[i] != 0 => i là hợp số

void linear_sieve() {
    for(int i = 2; i <= limN; i++) {
        if(!lp[i]) { // khi gặp số nguyên tố mới
            lp[i] = i; // ước nguyên tố nhỏ nhất của i chính là i
            primes.pb(i); // thêm i vào danh sách số nguyên tố
        }
        for(int j = 0; j < sz(primes) && i * primes[j] <= limN; j++) { // generate ra các hợp số tạo bởi các số nguyên tố bé hơn pr[i]
            lp[i * primes[j]] = primes[j];
            if(primes[j] == lp[i]) break;
        }
    }
}
```

## Mã giải (C++)
<div class="code-link">
  <a href="https://github.com/minhtuan0312/competitive-programming/tree/542bb94011ba8fee36c15057223e2912a36a6735/practice/number_theory/28tech_contest_chuyen_de_so_hoc_phan_1/problems/bai2_MISSINGPRIME" target="_blank">
    main.cpp
  </a>
</div>
