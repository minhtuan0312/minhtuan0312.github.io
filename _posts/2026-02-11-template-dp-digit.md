---
layout: post
title: Template Quy hoạch động chữ số🔥
date: 2026-02-11 13:18 +0700
categories: [template]
tags: [debug]
math: true
---

> 1 index nhé🐧
{: .prompt-info }

```c++
ll dp[MAX_DIGIT][2][2][...]

ll f(int idx, bool tight, bool lz, int ..., const string &s, int n) {
    if(idx == n + 1) {
        return ...; // Thay đổi điều kiện dừng tùy bài toán
    }
    auto &res = dp[idx][tight][lz][...];
    if(res != -1) return res;

    ll ans = 0;
    int limit = tight ? s[idx] - '0': 9;
    FOR(digit, 0, limit + 1) {
        bool new_tight = tight && (digit == limit);
        bool new_lz = lz && (digit == 0);

        int new_val = ...;
        if(new_lz) {
            new_val = 0;
        } else {

            // Logic transition của bài toán
            // Ví dụ:
            // new_val = (val * 10 + digit) % K;
            // new_val = val + digit;

        }
        ans += f(idx + 1, new_tight, new_lz, new_val, s, n);
    }
    return res = ans;
}

ll solve(const string& s, int n) {
    memset(dp, -1, sizeof dp);
    return f(1, 1, 1, ..., s, n); // Lưu ý: Truyền lz = true ở vị trí bắt đầu
}
```

> Lùi xâu đi 1 đơn vị:
{: .prompt-info }

```c++
for (int i = n; i >= 1; i--) {
    if (s[i] == '0') s[i] = '9';
    else {
        s[i]--;
        break;
    }
}
```