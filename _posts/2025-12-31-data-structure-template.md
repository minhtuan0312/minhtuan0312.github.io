---
layout: post
title: Một số template Data Structure🐧
date: 2025-12-31 16:00 +0700
categories: [data structure, template]
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

> <b>Một tính chất quan trọng cần biết để tối ưu:</b> Nút gốc st[1] quản lý toàn bộ mảng từ [1, n]
{: .prompt-info }

<b>Kinh nghiệm:</b> Với các bài toán "đảo ngược" (switch/flip/invert), hãy luôn nghĩ đến việc lưu cả giá trị hiện tại và giá trị sau khi đảo (như lis và lds) để việc cập nhật chỉ tốn một phép swap.

<div class="problem-link">
  🔗 <strong>Ví dụ:</strong>
  <a href="https://codeforces.com/problemset/problem/145/E" target="_blank">
    E. Lucky Queries
  </a>
</div>

```c++
struct Node {
    int c4, c7, lis, lds;
    Node() : c4(0), c7(0), lis(0), lds(0) {}
};
Node Merge(const Node &l, const Node &r) {
    Node res;
    res.c4 = l.c4 + r.c4;
    res.c7 = l.c7 + r.c7;
    res.lis = max(l.c4 + r.lis, l.lis + r.c7);
    res.lds = max(l.c7 + r.lds, l.lds + r.c4);
    return res;
}
const ll NO_LAZY = 0;
struct segment_tree {
    int n;
    vector<Node> st;
    vector<bool> lazy;
    segment_tree() {}
    segment_tree(int n): n(n), st(n << 2), lazy(n << 2, NO_LAZY) {}
    void apply(int v) {
        swap(st[v].c4, st[v].c7);
        swap(st[v].lis, st[v].lds);
        lazy[v] = !lazy[v];
    }
    void push(int v, int l, int r) {
        if(lazy[v] == NO_LAZY) return;
        apply(v << 1);
        apply(v << 1 | 1);
        lazy[v] = 0;
    }
    void build(int v, int l, int r, const string &A) {
        if (l == r) {
            st[v].c4 += A[l] == '4';
            st[v].c7 += A[l] == '7';
            st[v].lis = st[v].lds = 1;
            return;
        }
        int m = (l + r) >> 1;
        build(v << 1, l, m, A);
        build(v << 1 | 1, m + 1, r, A);
        st[v] = Merge(st[v << 1], st[v << 1 | 1]);
    }
    void upd(int v, int l, int r, int ql, int qr) {
        if (ql > qr) return;
        if (l == ql && r == qr) {
            apply(v);
            return;
        }
        push(v, l, r);
        int m = (l + r) >> 1;
        upd(v << 1, l, m, ql, min(qr, m));
        upd(v << 1 | 1, m + 1, r, max(ql, m + 1), qr);
        st[v] = Merge(st[v << 1], st[v << 1 | 1]);
    }
    Node query(int v, int l, int r, int ql, int qr) {
        if (ql > qr) return Node();
        if (l == ql && r == qr) return st[v];
        push(v, l, r);
        int m = (l + r) >> 1;
        Node q1 = query(v << 1, l, m, ql, min(m, qr));
        Node q2 = query(v << 1 | 1, m + 1, r, max(ql, m + 1), qr);
        return Merge(q1, q2);
    }
};
int main(void) {
    int n, m; cin >> n >> m;
    string s; cin >> s;
    s = ' ' + s;
    segment_tree seg(n);
    seg.build(1, 1, n, s);
    while(m--) {
        string op; cin >> op;
        if(op[0] == 's') { // so sánh kí tự sẽ tối ưu hơn nguyên xâu
            int l, r; cin >> l >> r;
            seg.upd(1, 1, n, l, r);
        } else {
            cout << seg.st[1].lis << nl; // thay vì "seg.query(1, 1, n, 1, n).lis"
        }
    }
}
```

## Segment Tree Walk

> Thay vì dùng chặt nhị phân (Binary Search) kết hợp với truy vấn Segment Tree (mất $O(\log^2 n)$), kỹ thuật Walk cho phép ta đi thẳng từ gốc xuống lá để tìm vị trí thỏa mãn điều kiện chỉ trong $O(\log n)$.
{: .prompt-tip}

Tư duy cốt lõi của Walk là đứng ở một nút và tự hỏi: **"Mình nên rẽ trái hay rẽ phải?"**
- Nếu bên trái có khả năng chứa đáp án (ví dụ: **tìm giá trị đầu tiên $\ge x$**, và **max của cây con trái $\ge x$**), ta rẽ trái.
- Nếu bên trái không thỏa mãn, ta rẽ phải.
- Nếu cả nút hiện tại không thỏa mãn điều kiện hoặc nằm ngoài vùng truy vấn, ta quay đầu (trả về giá trị báo lỗi như $-1$).

Để mô phỏng chân thực nhất, giả sử cây Segment Tree của ta đang lưu Max (tìm phần tử lớn nhất). Bài toán phổ biến nhất của dạng này là: "Tìm vị trí $i$ **nhỏ nhất** trong đoạn [ql, qr] sao cho $A[i] \ge val$".

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
        st[v] = max(st[v << 1], st[v << 1 | 1]);
    }
    // tìm vị trí đầu tiên trong đoạn [ql, qr] có giá trị >= val
    int walk(int v, int l, int r, int ql, int qr, ll val) {
        if(ql > qr || st[v] < val>) return -1;
        if(l == r) return l;
        int m = (l + r) >> 1;
        // ưu tiên rẽ TRÁI vì ta cần tìm vị trí NHỎ NHẤT (đầu tiên) 
        int res = walk(v << 1, l, m, ql, min(m, qr), val);
        if(res == -1) { // nếu bên trái không có đáp án, bắt buộc rẽ PHẢI
            res = walk(v << 1 | 1, m + 1, r, max(ql, m + 1), qr, val);
        }
        return res;
    }
};
```

## Segment Tree Beats

Bài toán kinh điển nhất sinh ra Segment Tree Beats là:
- **Truy vấn 1:** Cập nhật đoạn $[l, r]$: $a_i = \min(a_i, x)$ (gọi tắt là **Range chmin**).
- **Truy vấn 2:** Tính tổng đoạn $[l, r]$.

Với phép cộng đoạn (Range Add), khi bạn cộng $x$ vào đoạn độ dài $len$, tổng đoạn tăng lên $x \times len$. Rất dễ để cập nhật $O(1)$ tại một nút và lưu Lazy.

Nhưng với phép $a_i = \min(a_i, x)$, ta **không biết** trong đoạn đó có bao nhiêu phần tử lớn hơn $x$, và chúng lớn hơn $x$ bao nhiêu đơn vị để trừ đi khỏi tổng `sum`. Nếu cố tình đi xuống tận lá để cập nhật thì độ phức tạp sẽ bị suy biến thành $O(N)$.

> Ý tưởng cốt lõi là **lưu thêm thông tin để biết khi nào có thể cập nhật nhanh**, khi nào **bắt buộc phải đệ quy sâu hơn**.
{: .prompt-tip}

Tại mỗi nút, thay vì chỉ lưu `sum`, ta lưu thêm 3 giá trị:
- `max1`: Giá trị lớn nhất **nghiêm ngặt** trong đoạn.
- `max2`: Giá trị lớn thứ hai **nghiêm ngặt** trong đoạn.
- `maxc`: Số lần xuất hiện của `max1` trong đoạn.

Khi thực hiện cập nhật `chmin(v, x)` tại nút `v`, ta đối mặt với 3 trường hợp (đây là "nhịp đập - beats" của thuật toán):

1. **Trường hợp 1 (Break condition):** $x \ge max_1$.
- Phép $\min(a_i, x)$ không làm thay đổi bất cứ phần tử nào trong đoạn này. **$\rightarrow$ Dừng lại ngay (Return).**
2. **Trường hợp 2 (Tag condition):** $max_2 < x < max_1$.
- Phép $\min$ chỉ tác động lên **đúng những phần tử đang bằng $max_1$**. Những phần tử này sẽ bị giảm xuống thành $x$.
- Ta có thể cập nhật $O(1)$ ngay tại nút này: `$sum = sum - (max_1 - x) \times max_c$ ; $max_1 = x$` Và đánh dấu Lazy (thực chất chính max1 đóng vai trò là Lazy tag). **$\rightarrow$ Cập nhật và Dừng lại**.
3. **Trường hợp 3 (Recurse condition):** $x \le max_2$.
- Phép $\min$ không chỉ ảnh hưởng đến $max_1$ mà còn ảnh hưởng đến $max_2$, $max_3$, v.v. Ta không thể tính nhanh được tổng thay đổi. **$\rightarrow$ Bắt buộc Pushdown và đi tiếp xuống 2 con**.

Dù có vẻ như Trường hợp 3 sẽ làm thuật toán chậm đi, nhưng người ta đã chứng minh được độ phức tạp khấu hao (amortized) của toàn bộ quá trình chỉ là $O(Q \log^2 N)$ hoặc $O(Q \log N)$ tùy cấu trúc bài.

```c++
struct segment_tree_beats {
    int n;
    vector<ll> sum, max1, max2, maxc;

    segment_tree_beats(int n): n(n), sum(n << 2, 0), max1(n << 2, -INF), max2(n << 2, -INF), maxc(n << 2, 0) {}

    // Gộp thông tin từ 2 con lên cha
    void pushup(int v) {
        int l = v << 1, r = v << 1 | 1;
        sum[v] = sum[l] + sum[r];
        
        if (max1[l] == max1[r]) {
            max1[v] = max1[l];
            max2[v] = max(max2[l], max2[r]);
            maxc[v] = maxc[l] + maxc[r];
        } else if (max1[l] > max1[r]) {
            max1[v] = max1[l];
            max2[v] = max(max2[l], max1[r]);
            maxc[v] = maxc[l];
        } else {
            max1[v] = max1[r];
            max2[v] = max(max1[l], max2[r]);
            maxc[v] = maxc[r];
        }
    }

    // Hàm áp dụng phép chmin ngay tại nút v (Trường hợp 2)
    void apply_chmin(int v, ll val) {
        if (val >= max1[v]) return;
        sum[v] -= (max1[v] - val) * maxc[v];
        max1[v] = val;
    }

    // Đẩy thông tin xuống con
    void pushdown(int v) {
        int l = v << 1, r = v << 1 | 1;
        // Nếu max1 của cha nhỏ hơn max1 của con, nghĩa là cha vừa bị chmin
        // Ta dùng chính max1 của cha làm lazy tag đẩy xuống con
        apply_chmin(l, max1[v]);
        apply_chmin(r, max1[v]);
    }

    void build(int v, int l, int r, ll A[]) {
        if (l == r) {
            sum[v] = max1[v] = A[l];
            max2[v] = -INF;
            maxc[v] = 1;
            return;
        }
        int m = (l + r) >> 1;
        build(v << 1, l, m, A);
        build(v << 1 | 1, m + 1, r, A);
        pushup(v);
    }

    // Range chmin: A[i] = min(A[i], val) với i in [ql, qr]
    void upd_chmin(int v, int l, int r, int ql, int qr, ll val) {
        // Break condition (TH 1)
        if (l > qr || r < ql || val >= max1[v]) return; 
        
        // Tag condition (TH 2)
        if (ql <= l && r <= qr && val > max2[v]) {
            apply_chmin(v, val);
            return;
        }
        
        // Recurse condition (TH 3)
        pushdown(v);
        int m = (l + r) >> 1;
        upd_chmin(v << 1, l, m, ql, qr, val);
        upd_chmin(v << 1 | 1, m + 1, r, ql, qr, val);
        pushup(v);
    }

    ll query_sum(int v, int l, int r, int ql, int qr) {
        if (ql > r || qr < l) return 0;
        if (ql <= l && r <= qr) return sum[v];
        pushdown(v);
        int m = (l + r) >> 1;
        return query_sum(v << 1, l, m, ql, qr) + query_sum(v << 1 | 1, m + 1, r, ql, qr);
    }
};
```

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
    int n;
    vector<int> parent, sz;
    int comps;
    disjoint_set_union() {}
    disjoint_set_union(int n): n(n), parent(n + 1) {
        sz.assign(n + 1, 1);
        FOR(i, 1, n + 1) parent[i] = i;
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
    int n;
    vector<int> parent, sz;
    int comps;
    disjoint_set_union_rollback() {}
    disjoint_set_union_rollback(int n) : n(n), parent(n + 1) {
        sz.assign(n + 1, 1);
        FOR(i, 1, n + 1) parent[i] = i;
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

## Sparse Table (1-index)
```c++
const int limN = 1000005;
const int limLOG = 21;
int st[limN][limLOG];
struct sparse_table {
    int n, max_log;
    vector<vector<ll>> st;
    sparse_table() {}
    sparse_table(int A[], int n): n(n), max_log(__lg(n) + 1) {
        FOR(i, 1, n + 1) st[i][0] = A[i];
        FOR(j, 1, max_log) {
            for(int i = 1; i + (1 << j) - 1 <= n; i++) {
                st[i][j] = __gcd(st[i][j - 1], st[i + (1 << (j - 1))][j - 1]);
            }
        }
    }
    ll query(int l, int r) {
        if(l > r) return 0;
        int j = __lg(r - l + 1);
        return __gcd(st[l][j], st[r - (1 << j) + 1][j]);
    }
};
```
