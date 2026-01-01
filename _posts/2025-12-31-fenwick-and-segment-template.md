---
layout: post
title: Một số template Data Structure🐧
date: 2025-12-31 16:00 +0700
categories: [template]
tags: [debug]
---

## Easy segment tree (Iterative) (1-based)
```c++
struct easy_segment_tree{
    int n;
    vector<ll> st;
    easy_segment_tree() {}
    easy_segment_tree(int a) : n(a), st((n << 1) + 5) {}

    void update(int idx, ll val) {
        idx--;
        for(st[idx += n] = val; idx > 1; idx >>= 1) {
            st[idx >> 1] = st[idx] + st[idx ^ 1];
        }
    }

    ll query(int l, int r) { // thực chất là [l, r)
        l--;
        ll res = 0;
        for(l += n, r += n; l < r; l >>= 1, r >>= 1) {
            if(l & 1) res += st[l++];
            if(r & 1) res += st[--r];
        }
        return res;
    }
};
```

## Segment tree (Recursive) 
> chậm hơn phiên bản iterative 1,5~2 lần nhưng lại dễ điều chỉnh hơn đặc biệt khi lazy
{: .prompt-info }
```c++
struct segment_tree {
    int n;
    vector<ll> st;
    segment_tree() {}
    segment_tree(int a): n(a), st(n << 2) {}
    
    void upd(int v, int l, int r, int idx, ll val) {
        if(l == r) {
            st[v] = val;
            return;
        }
        int m = (l + r) >> 1;
        if(idx <= m) {
            upd(v << 1, l, m, idx, val);
        } else {
            upd(v << 1 | 1, m + 1, r, idx, val);
        }
        st[v] = st[v << 1] + st[v << 1 | 1];
    }
    
    ll query(int v, int l, int r, int ql, int qr) {
        if(ql > qr) return 0;
        if(l == ql && r == qr) return st[v];
        int m = (l + r) >> 1;
        ll q1 = query(v << 1, l, m, ql, min(m, qr));
        ll q2 = query(v << 1 | 1, m + 1, r, max(ql, m + 1), qr);
        return q1 + q2;
    }
};
```

## Fenwick tree (1-based)
```c++
struct fenwick_tree {
    int n;
    vector<ll> BIT;
    fenwick_tree() {}
    fenwick_tree(int a): n(a), BIT(n + 1) {}
    void upd(int idx, ll val) {
        for(; idx <= n; idx += (idx & -idx)) {
            BIT[idx] += val;
        }
    }
    ll query(int idx) {
        ll res = 0;
        for(; idx; idx -= (idx & -idx)) {
            res += BIT[idx];
        }
        return res;
    }
};
```

## Suffix fenwick tree (1-based)
```c++
struct suffix_fenwick_tree {
    int n;
    vector<ll> BIT;
    suffix_fenwick_tree() {}
    suffix_fenwick_tree(int a): n(a), BIT(n + 1) {}
    void upd(int idx, ll val) {
        for(; idx; idx -= (idx & -idx)) {
            BIT[idx] += val;
        }
    }
    ll query(int idx) {
        ll res = 0;
        for(; idx <= n; idx += (idx & -idx)) {
            res += BIT[idx];
        }
        return res;
    }
};
```

## Matrix (0-based)
```c++
struct matrix {
    int n;
    vector<vector<int>> f;
    matrix(int a) : n(a), f(n, vector<int>(n, 0)) {}
    matrix identity() const {
        matrix res(n);
        FOR(i, 0, n) res.f[i][i] = 1;
        return res;
    }
    matrix operator * (const matrix &other) const {
        matrix res(n);
        FOR(i, 0, n) {
            FOR(j, 0, n) {
                FOR(k, 0, n) {
                    res.f[i][j] += f[i][k] * other.f[k][j] % mod;
                    res.f[i][j] %= mod;
                }
            }
        }
        return res;
    }
    matrix operator ^ (ll b) const {
        matrix res = identity();
        matrix a = (*this);
        while(b) {
            if (b & 1) res = res * a;
            a = a * a;
            b >>= 1;
        }
        return res;
    }
};
```
