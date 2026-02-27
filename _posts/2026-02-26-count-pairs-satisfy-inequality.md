---
layout: post
title: Đếm cặp thỏa mãn bất đẳng thức⚖️ & Rời rạc hóa kép🧩
date: 2026-02-26 22:39 +0700
math: true
---

## <b>1. Tư duy cốt lõi</b>

<div class="problem-link">
  🔗 <strong>Bài toán:</strong>
  <a href="https://oj.vnoi.info/problem/NKINV#submit" target="_blank">
    Dãy nghịch thế
  </a>
</div>

Giả sử ta cần đếm số cặp $(i, j)$ sao cho $i < j$ và $A_i > A_j$. Cách làm ngây thơ là 2 vòng lặp $O(N^2)$. Để tối ưu xuống $O(N \log N)$, ta tư duy như sau:

- Duyệt $j$ từ $1$ đến $N$. Tại mỗi bước $j$, ta đã có các phần tử $A_1, A_2, \dots, A_{j-1}$ nằm trong một "tập hợp".
- Ta cần hỏi: Trong "tập hợp" này, có bao nhiêu phần tử <b>lớn hơn</b> $A_j$?
- Sau khi đếm xong, ta thêm $A_j$ vào "tập hợp" đó để chuẩn bị cho bước $j+1$.

Để làm điều này, ta dùng một mảng tần số (Frequency Array) được quản lý bởi BIT.

- `BIT[v]` lưu số lần xuất hiện của phần tử có giá trị là `v`.
- Tổng các phần tử trong khoảng `[L, R]` trên BIT chính là số lượng các số có giá trị nằm trong đoạn `[L, R]` đã xuất hiện.

## <b>2. Rời rạc hóa</b>

Vì giá trị $A_i$ có thể rất lớn (lên tới $10^9$) hoặc âm, ta không thể dùng nó làm chỉ số mảng cho `BIT[A_i]` được (sẽ bị tràn bộ nhớ). Trong khi đó, số lượng phần tử lại nhỏ (thường $N \le 2 \cdot 10^5$).

Rời rạc hóa giúp ánh xạ các giá trị lớn này về các số nguyên nhỏ từ $1$ đến $N$ mà vẫn <b>giữ nguyên quan hệ lớn/bé</b>.

<b>Quy trình chuẩn cho mọi bài toán:</b>

1. Đẩy tất cả các giá trị cần Update và cần Query vào chung một std::vector.
2. Sort và xóa trùng (std::unique) vector đó.
3. Thay thế giá trị ban đầu bằng vị trí của nó trong vector (dùng `lower_bound`).

```c++
struct fenwick_tree{
    int n;
    vector<ll> BIT;
    fenwick_tree() {}
    fenwick_tree(int n): n(n), BIT(n + 1) {}
    void upd(int idx, ll val) {
        for(; idx <= n; idx += idx & -idx) {
            BIT[idx] += val;
        }
    }
    ll query(int idx) {
        ll res = 0;
        for(; idx; idx -= idx & -idx) {
            res += BIT[idx];
        }
        return res;
    }
};
int main(void) {
    int n; cin >> n;
    int A[n + 1];
    vector<int> compress;
    FOR(i, 1, n + 1) {
        cin >> A[i];
        compress.eb(A[i]);
    }
    sort(all(compress));
    compress.erase(unique(all(compress)), compress.end());
    FOR(i, 1, n + 1) {
        A[i] = lower_bound(all(compress), A[i]) - compress.begin() + 1;
    }
    // (i, j) i < j && a[i] > a[j]
    fenwick_tree fw(sz(compress));
    ll res = 0;
    // CÁCH 1
    // -> x < i && a[x] > a[i]
    FOR(i, 1, n + 1) {
        res += i - 1 - fw.query(A[i]);
        fw.upd(A[i], 1);
    }
    // CÁCH 2;
    for(int i = n; i >= 1; i--) {
        res += fw.query(A[i]);
        fw.upd(A[i], 1);
    }
    cout << res;
}
```

## <b>3. Rời rạc hóa kép (Double Coordinate Compression)</b>

> Nhớ: Nếu `cmp(x, y)` trả về `true`, thì <b>`x` sẽ được xếp đứng trước `y`</b> trong mảng đã sắp xếp.
{: .prompt-info}

### <b>Dạng 1: Rời rạc hóa Cặp Giá Trị (Pair Compression - Đưa 2D về 1D)</b>

<b>Bản chất:</b> Ta có một tập hợp các trạng thái/đối tượng được định nghĩa bởi 2 tiêu chí (ví dụ: `(số_bài, điểm_phạt)` hoặc `(trọng_lượng, giá_trị)`). Bạn cần xếp hạng chúng và gán cho mỗi cặp một số ID nguyên duy nhất (từ $1$ đến $K$). Ta cần xếp hạng chúng và gán cho mỗi cặp một số ID nguyên duy nhất (từ $1$ đến $K$).

<b>Cách làm:</b>

1. Gom tất cả các cặp `pair<T, T>` có thể sinh ra vào một `vector<pair>`.
2. Sắp xếp `vector` này theo <b>quy tắc của đề bài</b> (viết hàm cmp nếu cần).
3. Dùng `unique` để xóa các cặp trùng lặp.
4. Tìm ID của một cặp bất kỳ bằng `lower_bound`.

```c++
typedef pair<ll, ll> ii;
// hàm so sánh tùy chỉnh (tùy đề bài)
// ví dụ: Ưu tiên X tăng dần, X bằng nhau thì Y giảm dần
bool cmp(const ii& a, const ii& b) {
    if (a.fi != b.fi) return a.fi < b.fi;
    return a.se > b.se;
}
int main(void) {
    int n; cin >> n;
    ii A[n + 1];
    int id[n + 1];
    vector<ii> compress;
    FOR(i, 1, n + 1) {
        cin >> A[i].first >> A[i].second;
        compress.pb(A[i]); // bước 1
    }
    // bước 2 nén mảng
    sort(all(compress), cmp);
    compress.erase(unique(all(compress)), compress.end()); // bước 3
    FOR(i, 1, n + 1) {
        id[i] = lower_bound(all(compress), A[i], cmp) - compress.begin() + 1; // bước 4 lấy id
    }
    // lúc này id[i] chính là thứ hạng "chuẩn" của cặp A[i] trong tập dữ liệu
}
```

$\Rightarrow$ Nếu bạn phải nén nhiều hơn 2 giá trị (ví dụ: $a, b, c$) thì dùng `struct` (thậm chí 2 giá trị dùng cũng được), còn lại y chang như code trên

### <b>Dạng 2: Rời rạc hóa Tọa Độ 2 Chiều (2D Grid Compression - Đưa Tọa độ lớn về Tọa độ nhỏ)</b>

<b>Bản chất:</b> Đề bài cho các điểm trên mặt phẳng tọa độ $Oxy$, nhưng $X, Y$ có thể lên tới $10^9$. Số lượng điểm chỉ khoảng $N \le 10^5$. Ta cần đưa các điểm này về một lưới nhỏ gọn hơn (kích thước tối đa $N \times N$) để có thể áp dụng Segment Tree 2D, BIT 2D, hoặc kỹ thuật Sweep Line (Đường quét).

> <b>Cách làm:</b> Thay vì nén chung vào một cục, ta <b>nén độc lập trục X và trục Y</b>. Mảng $X$ và mảng $Y$ không liên quan đến nhau.
{: .prompt-tip}

```c++
int n; cin >> n;
Point points[n + 1];
int id[n + 1];
vector<int> compress_x, compress_y;
FOR(i, 1, n + 1) {
    cin >> points[i].x >> points[i].y;
    compress_x.eb(points[i].x);
    compress_y.eb(points[i].y);
}
sort(all(compress_x));
compress_x.erase(unique(all(compress_x)), compress_x.end());
sort(all(compress_y));
compress_y.erase(unique(all(compress_y)), compress_y.end());
FOR(i, 1, n + 1) {
    points[i].x = lower_bound(all(compress_x), points[i].x) - compress_x.begin() + 1;
    points[i].y = lower_bound(all(compress_y), points[i].y) - compress_y.begin() + 1;
}
```