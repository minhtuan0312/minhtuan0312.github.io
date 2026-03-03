---
layout: post
title: Một số dạng Sweepline🧹
date: 2026-02-20 06:24 +0700
math: true
---

<b>Khi nào nghĩ đến Sweeping Line đầu tiên?</b>

- <b>Dữ liệu đầu vào là các "Khoảng không gian" hoặc "Tọa độ":</b> Đề bài cho các đoạn thẳng $[L, R]$, các hình chữ nhật $[x_1, y_1, x_2, y_2]$, các hình tròn, hoặc các tập hợp điểm trên mặt phẳng.
- <b>Từ khóa về sự chồng lấn/Giao nhau:</b> "Phủ (cover)", "Giao nhau (intersect)", "Chồng lên nhau (overlap)", "Liên tiếp", "Nhiều nhất/Ít nhất".
- <b>Yêu cầu tính toán tổng thể:</b> "Tổng chiều dài", "Tổng diện tích", "Điểm lớn nhất/nhỏ nhất", "Khoảng cách ngắn nhất".
- <b>Tính độc lập của trục tọa độ:</b> Bài toán có thể được giải quyết bằng cách "cắt lát" không gian theo một chiều (thường là trục Ox hoặc trục thời gian). Nếu biết trạng thái tại mặt cắt hiện tại, ta có thể dễ dàng tính toán cho không gian giữa 2 mặt cắt liên tiếp.

Bất kể là bài quét 1D hay 2D, ta luôn cần đi qua 4 bước chuẩn mực sau:

1. <b>Định nghĩa Sự kiện (Events):</b> Mỗi đối tượng hình học sinh ra các "sự kiện" khi đường quét chạm vào nó (Bắt đầu) và khi đường quét rời đi (Kết thúc).
2. <b>Sắp xếp Sự kiện:</b> Sắp xếp tất cả sự kiện theo tọa độ của đường quét. Lưu ý cực kỳ quan trọng: Xử lý điều kiện phá vỡ hòa (Tie-breaking) khi nhiều sự kiện trùng tọa độ.
3. <b>Cấu trúc dữ liệu trạng thái (State Data Structure):</b> Dùng một cấu trúc dữ liệu (Biến đếm, Set, Segment Tree, Fenwick Tree) để duy trì thông tin của các đối tượng đang giao cắt với đường quét hiện tại.
4. <b>Xử lý tuần tự:</b> Duyệt qua mảng sự kiện đã sort, cập nhật Cấu trúc dữ liệu và trích xuất kết quả.

## <b>I. Quét 1D</b>

Dạng này thường gặp ở các bài toán quản lý phòng họp, lịch trình, hoặc tính độ dài đoạn thẳng bị phủ. Đây thực chất là kỹ thuật <b>Mảng hiệu (Difference Array)</b> nhưng được áp dụng trên tọa độ lớn, bắt buộc phải sắp xếp thay vì dùng mảng tĩnh.

- Độ phức tạp tổng quát: $O(N \log N)$ cho bước sắp xếp, $O(N)$ cho bước duyệt. Tổng thời gian là $O(N \log N)$.

> <b>Ý tưởng:</b>
1. Mỗi đoạn $[L, R]$ tạo ra 2 sự kiện: Sự kiện "Mở" tại $L$ (giá trị +1) và sự kiện "Đóng" tại $R$ (giá trị -1).
2. Sắp xếp tất cả sự kiện theo tọa độ $x$ tăng dần.
- Nếu đề bài tính "chạm mép là chồng lên nhau": Xử lý sự kiện "Mở" trước "Đóng".
- Nếu đề bài tính "chạm mép KHÔNG chồng lên nhau": Xử lý sự kiện "Đóng" trước "Mở".
3. Quét qua mảng sự kiện, cộng dồn giá trị để biết tại mỗi điểm có bao nhiêu đoạn đang phủ lên.
{: .prompt-tip }

```c++
struct Event{
    int x;
    int type; // 1: open, -1: close
    bool operator<(const Event &other) {
        if(x != other.x) return x < other.x;
        return x < other.x;
    }
};
typedef pair<int, int> ii;
int main(void) {
    int n; cin >> n;
    ii A[n + 1];
    vector<Event> events;
    FOR(i, 1, n + 1) {
        cin >> A[i].fi >> A[i].se;
        events.pb({A[i].fi, 1});
        events.pb({A[i].se, -1});
    }
    // 2. sắp xếp tất cả sự kiện trên trục tọa độ
    sort(all(events));
    // 3. quét trạng thái
    ll active = 0;       // đếm số đoạn đang mở tại vị trí hiện tại
    ll prev_x = events[0].x;     // tọa độ của sự kiện trước đó
    ll res = 0;          // biến lưu kết quả
    for(const auto &e: events) {
        // --- BƯỚC A: Xử lý KHOẢNG CÁCH từ prev_x đến vị trí hiện tại ---
        // (Thường dùng cho bài toán tính tổng độ dài)
        if (active > 0) { 
            res += (e.x - prev_x);
        }
        // --- BƯỚC B: Cập nhật trạng thái tại điểm hiện tại ---
        active += e.type;
        prev_x = e.x;
        // --- BƯỚC C: Cập nhật ĐỈNH (Max/Min) ---
        // (Thường dùng cho bài toán tìm số điểm chồng lấp lớn nhất)
        // res = max(res, active);
    }
    cout << res;
}
```

### <b>Dạng 1: Tính tổng độ dài bị phủ bởi ít nhất 1 đoạn</b>
>
- Dùng **Bước A**
- Điều kiện: `if (active > 0) res += (e.x - prev_x);`
{: .prompt-tip}

### <b>Dạng 2: Tìm điểm bị chồng lấp nhiều nhất (Ví dụ: Cần bao nhiêu phòng họp, Lượng khách đông nhất)</b>
>
- Bỏ qua Bước A. Chỉ dùng **BƯỚC C**.
- Điều kiện: `res = max(res, active);` (Lưu ý: Khởi tạo `res = 0`).
{: .prompt-tip}

### <b>Dạng 3: Tính tổng độ dài bị phủ bởi ĐÚNG K đoạn (Hoặc ÍT NHẤT K đoạn) </b>

> 
- Dùng BƯỚC A.
- Điều kiện (Đúng K): `if (active == K) ans += (e.pos - prev_pos);`
- Điều kiện (Ít nhất K): `if (active >= K) ans += (e.pos - prev_pos);`
{: .prompt-tip}


<div class="problem-link">
  🔗 <strong>Ví dụ:</strong>
  <a href="https://cses.fi/problemset/task/1619/" target="_blank">
    Restaurant Customers
  </a>
</div>

## <b>II. Quét 2D</b>
### 1. Cặp điểm gần nhất (Closest Point Pair)

Bài toán kinh điển yêu cầu tìm cặp điểm có khoảng cách Euclid nhỏ nhất trong không gian 2D. Thuật toán ngây thơ duyệt mọi cặp điểm mất $O(N^2)$. Sweepline giúp giảm xuống $O(N \log N)$.

#### Phân tích toán học

Giả sử khoảng cách ngắn nhất ta hiện có là $d$.

Công thức bình phương khoảng cách giữa 2 điểm là:

$$D^2 = (x_i - x_j)^2 + (y_i - y_j)^2$$

Chúng ta muốn tìm được điểm $P_j$ sao cho $D < d$, tức là $D^2 < d^2$:

$$(x_i - x_j)^2 + (y_i - y_j)^2 < d^2$$

- Giả sử $\lvert y_i - y_j \rvert \ge d$ hay $(y_i - y_j)^2 \ge d^2$ và ta biết rằng $(x_i - x_j)^2 \ge 0$. Chắc chắn $(x_i - x_j)^2 + (y_i - y_j)^2 \ge d^2$. Tổng khoảng cách thực tế sẽ $\ge d$, và điểm $P_j$ đó <b>không thể nào tạo ra kỷ lục mới được</b>. 

- Ta cũng có điều này tương tự với $\lvert x_i - x_j \rvert \ge d$ và $(y_i - y_j)^2 \ge 0$

$\Rightarrow \quad$ Để có cơ hội tạo ra khoảng cách nhỏ hơn $d$, khoảng cách trên trục $x$ và $y$ bắt buộc phải nhỏ hơn $d$:

$$
\begin{cases}
|x_i - x_j| < d \\[6pt]
|y_i - y_j| < d
\end{cases}
\Rightarrow
\begin{cases}
x \in [x_i - d, x_i + d] \\[6pt]
y \in [y_i - d, y_i + d]
\end{cases}
$$

#### Triển khai
> <b>Ý tưởng:</b>
1. Sắp xếp các điểm theo tọa độ $x$.
2. Gọi $d$ là khoảng cách nhỏ nhất tìm được cho đến hiện tại. Đường quét di chuyển từ trái sang phải qua từng điểm.
3. Sử dụng $set$ (sắp xếp theo tọa độ $y$) để lưu các điểm nằm trong "cửa sổ" có khoảng cách $x$ tới điểm hiện tại $\le d$.
4. <b>Kỹ thuật 2 con trỏ:</b> Khi xét điểm hiện tại $(x_i, y_i)$, ta chỉ cần dùng lower_bound trên set để tìm các điểm có tọa độ $y$ nằm trong khoảng $[y_i - d, y_i + d]$. Sau đó cập nhật lại $d$. Loại bỏ các điểm cũ lọt ra khỏi cửa sổ khoảng cách $d$ theo trục $x$.
{: .prompt-tip }

<div class="problem-link">
  🔗 <strong>Ví dụ:</strong>
  <a href="https://www.spoj.com/problems/CLOPPAIR/" target="_blank">
    CLOPPAIR - Closest Point Pair
  </a>
</div>

```c++
struct Point{
    double x, y;
    int id; //nếu đề bài yêu cầu in ra vị trí điểm
    bool operator<(const Point &other) const {
        if(x == other.x) return y < other.y;
        return x < other.x;
    }
};
typedef pair<int, int> ii;
double dist(Point a, Point b) {
    return sqrt((a.x - b.x) * (a.x - b.x) + (a.y - b.y) * (a.y - b.y));
}
struct cmp{
    bool operator()(const Point &a, const Point &b) const {
        if(a.y == b.y) return a.x < b.x;
        return a.y < b.y;
    }
};
int main(void) {
    int n; cin >> n;
    Point A[n + 1];
    FOR(i, 1, n + 1) {
        cin >> A[i].x >> A[i].y;
        A[i].id = i;
    }
    sort(A + 1, A + 1 + n); // sort theo hoành độ
    set<Point, cmp> se;
    double min_d = 1e18;
    int best_i = -1, best_j = -1;
    int j = 1;
    FOR(i, 1, n + 1) {
        while(j < i && A[i].x - A[j].x >= min_d) { // loại bỏ |x[i] - x[j]| >= d
            se.erase(A[j]);
            j++;
        }
        // lấy y đầu tiên thõa mãn [y[i] - d, y[i] + d]
        // x = -1e18 để đảm bảo nếu nhiều điểm cùng y thì lấy điểm đầu tiên
        Point lower_bound_pt = {-1e18, A[i].y - min_d, - 1};
        auto it = se.lower_bound(lower_bound_pt);
        while(it != se.end() && it->y <= A[i].y + min_d) {
            if(minimize(min_d, dist(A[i], *it))) {
                best_i = A[i].id;
                best_j = it->id;
            }
            it++;
        }
        se.insert(A[i]);
    }
    if(best_i > best_j) swap(best_i, best_j);
    best_i--, best_j--;
    cout << best_i << ' ' << best_j << ' ';
    cout << fixed << setprecision(6) << min_d;
}
```

### 2. Diện tích / Chu vi hợp các hình chữ nhật

Sự kết hợp hoàn hảo: Sweepline + Segment Tree + Rời rạc hóa (Coordinate Compression).

- Sweepline: Cắt không gian theo trục $Ox$. Tại mỗi sự kiện (cạnh trái/phải của hình chữ nhật), ta cập nhật trạng thái các đoạn bị phủ trên trục $Oy$.
- Segment Tree: Đóng vai trò là "Cấu trúc dữ liệu trạng thái". Giúp cập nhật một đoạn $[y_1, y_2]$ bị phủ thêm hoặc bớt đi trong thời gian $O(\log N)$ và trả về tổng độ dài các đoạn đang bị phủ trên trục $Oy$.
- Rời rạc hóa: Vì tọa độ $y$ có thể rất lớn ($10^9$), ta phải gom chúng lại thành các mốc chỉ số từ $1$ đến $2N$ để dựng cây Segment Tree.
{: .prompt-tip }

> <b>Ý tưởng:</b>
1. Quét một đường thẳng đứng từ trái sang phải. Mỗi hình chữ nhật tạo ra 2 sự kiện: Cạnh trái (thêm đoạn $[y_{bottom}, y_{top}]$ vào) và Cạnh phải (xóa đoạn $[y_{bottom}, y_{top}]$ đi).
2. Sắp xếp các cạnh này theo tọa độ $x$.
3. Sử dụng Segment Tree quản lý chiều dài các đoạn trên trục $y$. Mỗi nút của Segment Tree lưu độ dài đoạn đang bị phủ bởi ít nhất một hình chữ nhật trên đoạn $y$ tương ứng.
4. Diện tích = $\sum (x_{i} - x_{i-1}) \times (\text{Độ dài đoạn } y \text{ đang được phủ từ Segment Tree})$.
{: .prompt-tip }

<b>Kỹ thuật bắt buộc theo kèm:</b> Rời rạc hóa (Coordinate Compression)

<b>*Note: Cách lưu trữ của Segment Tree (ở đây là Segment Tree On Intervals) có thể hơi khó hình dung, có thể xem ảnh sau:</b>

![Segment Tree On Intervals](/assets/img/segmenttreeonintervals.png)
_Cách lưu nốt của Segment Tree On Intervals_

<div class="problem-link">
  🔗 <strong>Ví dụ:</strong>
  <a href="https://oj.vnoi.info/problem/area#submit" target="_blank">
    Diện tích hình chữ nhật
  </a>
</div>

```c++
struct Event{
    ll x;
    int type; // 1: open (cạnh trái), -1: close (cạnh phải)
    ll y1, y2;
    bool operator <(const Event &other) const {
        if(x == other.x) return type > other.type;
        return x < other.x;
    }
};
vector<ll> compress;
struct Node{
    int cnt; // số lần bị phủ
    int len; // độ dài thực tế bị phủ
};
struct segment_tree{
    int n;
    vector<Node> st;
    segment_tree() {}
    segment_tree(int n): n(n), st(n << 2) {}
    void upd(int v, int l, int r, int ql, int qr, int val) {
        if(ql > qr) return;
        if(l == ql && r == qr) {
            st[v].cnt += val;
        } else {
            int m = (r + l) >> 1;
            upd(v << 1, l, m, ql, min(qr, m), val);
            upd(v << 1 | 1, m + 1, r, max(ql, m + 1), qr, val);
        }
        if(st[v].cnt > 0) {
            // bị phủ hoàn toàn, độ dài = khoảng cách giữa 2 đầu tọa độ
            // Y[r + 1] - Y[l] -> compress[r] - compress[l - 1]
            st[v].len = compress[r] - compress[l - 1];
        } else {
            // không bị phủ hoàn toàn, lấy tổng từ 2 con
            if (l == r) st[v].len = 0; // nốt lá thì không có con nên len = 0
            else st[v].len = st[v << 1].len + st[v << 1 | 1].len;
        }
    }
};
int main(void) {
    int n; cin >> n;
    vector<Event> events;
    FOR(i, 1, n + 1) {
        ll x1, y1, x2, y2;
        cin >> x1 >> y1 >> x2 >> y2;
        events.pb({x1, 1, y1, y2});
        events.pb({x2, -1, y1, y2});
        compress.eb(y1);
        compress.eb(y2);
    }
    sort(all(compress));
    compress.erase(unique(all(compress)), compress.end());
    sort(all(events));
    ll res = 0;
    ll m = size(compress);
    segment_tree seg(m - 1); // quản lý đoạn nên kích thước phải là m - 1
    ll last_x = events[0].x;
    for(const auto &e: events) {
        // cộng dồn diện tích: độ dài x * độ dài y đang bao phủ
        res += (e.x - last_x) * seg.st[1].len;
        int l = lower_bound(all(compress), e.y1) - compress.begin() + 1;
        int r = lower_bound(all(compress), e.y2) - compress.begin() + 1;
        if(l < r) {
            seg.upd(1, 1, m - 1, l, r - 1, e.type);
        }
        last_x = e.x;
    }
    cout << res;
}
```

## <b>III. Sweepline + DSU</b>

Bản chất của DSU chuẩn là **chỉ hỗ trợ thêm cạnh (Union)** chứ không hỗ trợ **xóa cạnh**. Do đó, ý tưởng cốt lõi khi kết hợp với Sweeping Line là: **Sắp xếp lại các sự kiện (thời gian, trọng số, tọa độ)** để ta chỉ cần đi một chiều (quét) và **liên tục thêm vào DSU**, hoặc **nâng cấp DSU để có thể "quay lui" (Rollback)** nếu cần thiết.


```c++
#include <bits/stdc++.h>
using namespace std;

// ---------------------------------------------------------
// 1. ĐỊNH NGHĨA SỰ KIỆN (EVENT)
// ---------------------------------------------------------
struct Event {
    ll x;       // Tọa độ trên trục quét (thường là trục Ox)
    int type;          // Phân loại sự kiện (VD: 1 = Cạnh trái/Bắt đầu, -1 = Cạnh phải/Kết thúc)
    ll y1, y2;  // Thông tin đi kèm trên trục còn lại (trục Oy)
    int id;            // ID để truy vết (nếu cần)

    // Quy tắc sắp xếp sự kiện quan trọng nhất:
    // Quét từ trái sang phải. Nếu trùng x, ưu tiên xử lý type nào trước?
    bool operator<(const Event &other) const {
        if (x == other.x) {
            return type > other.type; // Tùy chỉnh: Thường ưu tiên Mở (1) trước Đóng (-1)
        }
        return x < other.x;
    }
};

// ---------------------------------------------------------
// 2. CẤU TRÚC DỮ LIỆU TRẠNG THÁI (STATE DATA STRUCTURE)
// ---------------------------------------------------------
// Có thể là: set, multiset, Fenwick Tree (BIT), hoặc Segment Tree
struct ActiveState {
    // Khởi tạo cấu trúc dữ liệu
    void init(int n) { /* ... */ }
    
    // Hàm thêm đối tượng vào đường quét
    void add(...) { /* ... */ }
    
    // Hàm xóa đối tượng khỏi đường quét
    void remove(...) { /* ... */ }
    
    // Hàm lấy thông tin hiện tại (diện tích, khoảng cách, số lượng,...)
    ll query(...) { /* ... */ }
};

// ---------------------------------------------------------
// 3. VÒNG LẶP QUÉT (SWEEP LOOP)
// ---------------------------------------------------------
void solve() {
    int n; cin >> n;
    vector<Event> events;
    ActiveState state;
    
    // Bước 3.1: Đọc dữ liệu và tạo tập sự kiện
    for (int i = 0; i < n; i++) {
        // ... Đọc input ...
        // events.push_back({x, type, y1, y2, id});
    }
    
    // (Tùy chọn) Rời rạc hóa (Coordinate Compression) nếu mảng y quá lớn và dùng SegTree/BIT
    // ... compress code ...

    // Bước 3.2: Sắp xếp các sự kiện
    sort(events.begin(), events.end());
    
    ll result = 0; // Hoặc cực trị (1e18 cho min, -1 cho max)
    ll last_x = events.empty() ? 0 : events[0].x;

    // Bước 3.3: Quét qua từng sự kiện
    for (const auto& e : events) {
        // Cập nhật kết quả dựa trên khoảng cách (e.x - last_x) và Trạng thái cũ
        // result += (e.x - last_x) * state.query(...);

        // Xử lý sự kiện hiện tại: Thay đổi trạng thái
        if (e.type == 1) { // Mở / Thêm vào
            // state.add(...);
        } else if (e.type == -1) { // Đóng / Lấy ra
            // state.remove(...);
        } else { // Truy vấn đặc biệt (nếu có)
            // ...
        }
        
        last_x = e.x;
    }
    
    cout << result << nl;
}

int main() {
    ios_base::sync_with_stdio(false); cin.tie(NULL);
    solve();
    return 0;
}
```