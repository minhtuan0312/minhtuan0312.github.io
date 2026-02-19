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
    easy_segment_tree(int n) : n(n), st(n << 1 | 1) {}

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
    segment_tree(int n): n(n), st(n << 2) {}
    void build(int v, int l, int r, int A[]) {
        if(l == r) {
            st[v] = A[l];
            return;
        }
        int m = (l + r) >> 1;
        build(v << 1, l, m, A);
        build(v << 1 | 1, m + 1, r, A);
        st[v] = st[v << 1] + st[v << 1 | 1];
    }
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

## Segment Tree Lazy Update

```c++
const ll NO_LAZY = 0; 
struct segment_tree {
    int n;
    vector<ll> st, lazy;
    segment_tree() {}
    segment_tree(int n): n(n), st(n << 2), lazy(n << 2, NO_LAZY) {}
    void build(int v, int l, int r, int A[]) {
        if(l == r) {
            st[v] = A[l];
            return;
        }
        int m = (l + r) >> 1;
        build(v << 1, l, m, A);
        build(v << 1 | 1, m + 1, r, A);
        st[v] = st[v << 1] + st[v << 1 | 1];
    }
    //đẩy nợ từ cha xuống 2 con
    void push(int v, int l, int r) {
        if (lazy[v] == NO_LAZY) return;
        int m = (l + r) >> 1;
        // cập nhật giá trị cho 2 con
        st[v << 1] += lazy[v] * (m - l + 1);
        st[v << 1 | 1] += lazy[v] * (r - m);
        // truyền nợ xuống 2 con
        lazy[v << 1] += lazy[v];
        lazy[v << 1 | 1] += lazy[v];
        // trả hết nợ ở hiện tại
        lazy[v] = NO_LAZY;
    }
    void upd(int v, int l, int r, int ql, int qr, ll val) {
        if (ql > qr) return;
        if (l == ql && r == qr) {
            st[v] += val * (r - l + 1); // cập nhật giá trị cho nốt cha
            lazy[v] += val; // ghi nợ cho nốt cha để lúc đó truyền xuống cho nốt con
            return;
        }
        push(v, l, r);
        int m = (l + r) >> 1;
        upd(v << 1, l, m, ql, min(qr, m), val);
        upd(v << 1 | 1, m + 1, r, max(ql, m + 1), qr, val);
        st[v] = st[v << 1] + st[v << 1 | 1];
    }
    ll query(int v, int l, int r, int ql, int qr) {
        if (ql > qr) return 0;
        if (l == ql && r == qr) return st[v];
        push(v, l, r);
        int m = (l + r) >> 1;
        ll q1 = query(v << 1, l, m, ql, min(m, qr));
        ll q2 = query(v << 1 | 1, m + 1, r, max(ql, m + 1), qr);
        return q1 + q2;
    }
};
```

| Tính chất truy vấn | Logic cập nhật trọn vẹn | Logic hàm `push(v, l, r)` |
|-------------------|-----------------------------------------------|----------------------------|
| **Cộng đoạn, tìm Tổng** | `st[v] += val * len;`  <br>`lazy[v] += val;` | Cần nhân độ dài đoạn: <br>`st[child] += lazy[v] * len;` <br>`lazy[child] += lazy[v];` |
| **Cộng đoạn, tìm Max/Min** | `st[v] += val;` <br>`lazy[v] += val;` | Max/Min của đoạn tăng bao nhiêu thì<br> đoạn đó tăng bấy nhiêu<br>(không nhân độ dài → không truyền l, r): <br>`st[child] += lazy[v];` <br>`lazy[child] += lazy[v];` |
| **Gán đoạn bằng giá trị mới, <br>tìm Max/Min/Tổng** | `st[v] = val * len; (tổng)`<br>`st[v] = val; (max/min)` <br>`lazy[v] = val;` | Xóa nợ cũ hoàn toàn, <br>ghi đè nợ mới: <br>`st[child] = lazy[v] * len; (tổng)`<br>`st[child] = lazy[v]; (min/max)`<br>`lazy[child] = lazy[v];`<br> *Đối với min/max thì xóa nợ phải sử dụng<br>`const ll NO_LAZY = -1e18 - 7;` |

## Fenwick tree (1-based)
```c++
struct fenwick_tree {
    int n;
    vector<ll> BIT;
    fenwick_tree() {}
    fenwick_tree(int n): n(n), BIT(n + 1) {}
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
    suffix_fenwick_tree(int n): n(n), BIT(n + 1) {}
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

## Disjoint set union (1-based)
```c++
struct disjoint_set_union{
    vector<int> parent, sz;
    int comps;
    disjoint_set_union() {}
    disjoint_set_union(int n) : parent(n + 1) {
        sz.assign(n + 1, 1);
        iota(all(parent), 0);
        comps = n;
    }

    int Find(int u) {
        if(u == parent[u]) return u;
        return parent[u] = Find(parent[u]);
    }

    bool Unite(int u, int v) {
        u = Find(u);
        v = Find(v);
        if(u == v) return 0;
        if(sz[u] < sz[v]) swap(u, v);
        parent[v] = u;
        sz[u] += sz[v];
        comps--;
        return 1;
    }
};
```

## Khung rollback chung cho một số Data Structure
```c++
struct data_structure{
    vector<pair<int*, int>> st;
    void Save(int &x) {
        st.eb(&x, x);
    }
    ...
    int Snapshot() {
        return sz(st);
    }
    void Rollback(int snap) {
        while(sz(st) > snap) {
            *st.back().first = st.back().second;
            st.pop_back();
        }
    }
}
```
> Ví dụ với dsu
{: .prompt-info }

```c++
struct disjoint_set_union_rollback{
    vector<int> parent, sz;
    int comps;
    disjoint_set_union_rollback() {}
    disjoint_set_union_rollback(int n) : parent(n + 1) {
        FOR(i, 1, n + 1) parent[i] = i;
        sz.assign(n + 1, 1);
        comps = n;
    }
    vector<pair<int*, int>> st;
    void Save(int &x) {
        st.eb(&x, x);
    }
    int Find(int u) {
        while(u != parent[u]) u = parent[u];
        return u;
    }
    bool Unite(int u, int v) {
        u = Find(u);
        v = Find(v);
        if(u == v) return 0;
        if(sz[u] < sz[v]) swap(u, v);

        Save(parent[v]);
        Save(sz[u]);
        Save(comps);

        sz[u] += sz[v];
        parent[v] = u;
        comps--;
        return 1;
    }
    int Snapshot() {
        return sz(st);
    }
    void Rollback(int snap) {
        while(sz(st) > snap) {
            *st.back().first = st.back().second;
            st.pop_back();
        }
    }
};
```

## Matrix (0-based)
```c++
struct matrix {
    int n;
    vector<vector<ll>> f;
    matrix() {}
    matrix(int n) : n(n), f(n, vector<ll>(n, 0)) {}
    matrix identity() {
        matrix res(n);
        FOR(i, 0, n) res.f[i][i] = 1;
        return res;
    }
    matrix operator * (const matrix &other) {
        matrix res(n);
        FOR(i, 0, n) {
            FOR(k, 0, n) {
                if(!f[i][k]) continue;
                FOR(j, 0, n) {
                    res.f[i][j] += f[i][k] * other.f[k][j] % mod;
                    res.f[i][j] %= mod;
                }
            }
        }
        return res;
    }
    matrix operator ^ (ll b) {
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

## Trie
```c++
const int limN = 1e4 + 5;
const int limSz = 55;
struct trie {
    static const int MAXNODE = limN * limSz;
    int nxt[MAXNODE][26];
    int cntEnd[MAXNODE]; // có bao nhiêu string kết thúc đúng tại u
    int cntPass[MAXNODE]; // có bao nhiêu string đang tồn tại đi qua u
    int cnt;

    trie() {
        cnt = 0;
        memset(nxt, 0, sizeof nxt);
        memset(cntEnd, 0, sizeof cntEnd);
        memset(cntPass, 0, sizeof cntPass);
    }

    void Update(const string &s) {
        int u = 0;
        cntPass[u]++;
        for(char c : s) {
            int k = c - 'a';
            if(!nxt[u][k]) nxt[u][k] = ++cnt;
            u = nxt[u][k];
            cntPass[u]++;
        }
        cntEnd[u]++;
    }

    bool Exist(const string &s) {
        int u = 0;
        for(char c : s) {
            int k = c - 'a';
            if(!nxt[u][k]) return 0;
            u = nxt[u][k];
        }
        return cntEnd[u] > 0;
    }

    int CountString(const string &s) {
        int u = 0;
        for(char c : s) {
            int k = c - 'a';
            if(!nxt[u][k]) return 0;
            u = nxt[u][k];
        }
        return cntEnd[u];
    }

    int CountPrefix(const string &s) {
        int u = 0;
        for(char c : s) {
            int k = c - 'a';
            if(!nxt[u][k]) return 0;
            u = nxt[u][k];
        }
        return cntPass[u];
    }

    bool Delete(const string &s) {
        if(!Exist(s)) return 0;
        int u = 0;
        cntPass[u]--;
        for(char c: s) {
            int k = c - 'a';
            u = nxt[u][k];
            cntPass[u]--;
        }
        cntEnd[u]--;
        return 1;
    }
};
```


## Trie xor
```c++
const int limN = 1e5 + 5;
const int limSz = 31;
struct trie_xor {
    static const int MAXNODE = limN * limSz;
    int nxt[MAXNODE][2];
    int cnt;
    trie_xor() {
        cnt = 0;
        memset(nxt, 0, sizeof nxt);
    }
    void upd(int x) {
        int u = 0;
        for (int i = 30; i >= 0; i--) {
            int k = (x >> i) & 1;
            if (!nxt[u][k]) nxt[u][k] = ++cnt;
            u = nxt[u][k];
        }
    }
    int getMax(int x) {
        int u = 0, res = 0;
        for (int i = 30; i >= 0; i--) {
            int k = (x >> i) & 1;
            if (nxt[u][k ^ 1]) {
                res |= (1 << i);
                u = nxt[u][k ^ 1];
            } else u = nxt[u][k];
        }
        return res;
    }
};
```
