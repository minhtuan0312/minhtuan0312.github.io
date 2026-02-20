---
layout: post
title: Một số dạng Sweepline🧹
date: 2026-02-20 06:24 +0700
math: true
---

## <b>I. Quét 1D</b>
- Tìm số khoảng chồng chéo nhau lớn nhất (Max overlaps)
- Đếm số phòng họp cần thiết
- Tính tổng độ dài phần hợp của các đoạn thẳng

> <b>Ý tưởng:</b>
{: .prompt-info }

1. Mỗi đoạn $[L, R]$ tạo ra 2 sự kiện: Sự kiện "Mở" tại $L$ (giá trị +1) và sự kiện "Đóng" tại $R$ (giá trị -1).
2. Sắp xếp tất cả sự kiện theo tọa độ $x$ tăng dần. Trong trường hợp tọa độ $x$ trùng nhau và đề bài có tính <b>"chồng lên nhau"</b> thì sort sự kiện "Mở" trước "Đóng". Nếu không, xếp "Đóng" trước "Mở".
3. Quét qua mảng sự kiện, cộng dồn giá trị để biết tại mỗi điểm có bao nhiêu đoạn đang phủ lên.

<div class="problem-link">
  🔗 <strong>Ví dụ:</strong>
  <a href="https://cses.fi/problemset/task/1619/" target="_blank">
    Restaurant Customers
  </a>
</div>
```c++
struct Event{
    int x;
    int type; // 1: open, -1: close
    bool operator<(const Event &other) {
        if(x == other.x) return type > other.type;
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
    sort(all(events));
    int cur = 0, best = 0;
    for(const auto &[x, t]: events) {
        cur += t;
        maximize(best, cur);
    }
    cout << best;
}
```

## <b>II. Quét 2D</b>
### 1. Cặp điểm gần nhất (Closest Point Pair)

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
{: .prompt-info }

1. Sắp xếp các điểm theo tọa độ $x$.
2. Gọi $d$ là khoảng cách nhỏ nhất tìm được cho đến hiện tại. Đường quét di chuyển từ trái sang phải qua từng điểm.
3. Sử dụng $set$ (sắp xếp theo tọa độ $y$) để lưu các điểm nằm trong "cửa sổ" có khoảng cách $x$ tới điểm hiện tại $\le d$.
4. <b>Kỹ thuật 2 con trỏ:</b> Khi xét điểm hiện tại $(x_i, y_i)$, ta chỉ cần dùng lower_bound trên set để tìm các điểm có tọa độ $y$ nằm trong khoảng $[y_i - d, y_i + d]$. Sau đó cập nhật lại $d$. Loại bỏ các điểm cũ lọt ra khỏi cửa sổ khoảng cách $d$ theo trục $x$.

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

> <b>Ý tưởng:</b>
{: .prompt-info }

1. Quét một đường thẳng đứng từ trái sang phải. Mỗi hình chữ nhật tạo ra 2 sự kiện: Cạnh trái (thêm đoạn $[y_{bottom}, y_{top}]$ vào) và Cạnh phải (xóa đoạn $[y_{bottom}, y_{top}]$ đi).
2. Sắp xếp các cạnh này theo tọa độ $x$.
3. Sử dụng Segment Tree quản lý chiều dài các đoạn trên trục $y$. Mỗi nút của Segment Tree lưu độ dài đoạn đang bị phủ bởi ít nhất một hình chữ nhật trên đoạn $y$ tương ứng.
4. Diện tích = $\sum (x_{i} - x_{i-1}) \times (\text{Độ dài đoạn } y \text{ đang được phủ từ Segment Tree})$.

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