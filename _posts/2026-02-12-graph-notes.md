---
layout: post
title: Một số template và note Lý thuyết đồ thị🐧
date: 2026-02-12 20:25 +0700
categories: [graph, template]
tags: [lý thuyết đồ thị]
math: true
---

## <b>Đồ thị đầy đủ (Complete graph)</b>

### 1. Định nghĩa

Đồ thị đầy đủ (ký hiệu là $K_n$ với $n$ là số đỉnh) là một đồ thị vô hướng đơn giản mà giữa <b>bất kỳ hai đỉnh phân biệt nào cũng có đúng một cạnh nối</b>. Nói cách khác, mọi đỉnh đều kết nối trực tiếp với tất cả các đỉnh còn lại.

### 2. Các tính chất Toán học

- <b>Số lượng cạnh:</b> Do mỗi đỉnh nối với $n-1$ đỉnh còn lại, tổng số cạnh là $E = \frac{n(n-1)}{2}$. Điều này có nghĩa là số cạnh $E$ xấp xỉ $O(n^2)$ (đồ thị cực kỳ dày đặc).

- <b>Bậc của đỉnh:</b> Bậc của mọi đỉnh đều bằng $n-1$.

- <b>Đường đi / Chu trình Euler:</b> Đồ thị $K_n$ có chu trình Euler khi và chỉ khi $n$ là số lẻ (vì khi đó bậc của mọi đỉnh là $n-1$ sẽ là số chẵn).

- <b>Chu trình Hamilton:</b> Luôn tồn tại chu trình Hamilton với mọi $n \ge 3$.

- <b>Số lượng cây khung (Spanning Trees):</b> Theo <b>Công thức Cayley</b>, một đồ thị đầy đủ $K_n$ có đúng $n^{n-2}$ cây khung phân biệt. (Thường gặp trong các bài toán đếm modulo).

### 3. Một số bẫy gà

#### <b>A. Biểu diễn đồ thị</b>

Vì số cạnh $E = O(V^2)$, cách biểu diễn tối ưu nhất là <b>ma trận kề</b> (Adjacency Matrix) với độ phức tạp $O(V^2)$.

#### <b>B. Tìm Cây Khung Nhỏ Nhất (MST)</b>

- <b>Vấn đề:</b> Dùng thuật toán Kruskal. Kruskal có độ phức tạp $O(E \log E)$. Với $K_n$, $E \approx n^2$, nên Kruskal sẽ chạy mất $O(n^2 \log n)$.

- <b>Tối ưu:</b> Dùng Prim cơ bản (Mảng 1 chiều, không dùng Priority Queue). Độ phức tạp là $O(V^2)$. Nhanh hơn Kruskal và tiết kiệm bộ nhớ rõ rệt.

#### <b>C. Đường đi ngắn nhất (Shortest Path)

- <b>Vấn đề:</b> Dùng Dijkstra với `priority_queue` (như cách code mẫu thông thường). Độ phức tạp sẽ là $O((V + E) \log V) \approx O(n^2 \log n)$.

- <b>Tối ưu:</b> Dùng Dijkstra cơ bản (Tìm min bằng vòng lặp for thường). Độ phức tạp giảm xuống đúng $O(n^2)$. Với $n \le 5000$, Dijkstra $O(n^2)$ sẽ AC, trong khi Dijkstra + priority_queue có thể bị TLE.

#### <b>D. Bài toán Người chào hàng (Traveling Salesperson Problem - TSP)

- Thường xuất hiện trên đồ thị đầy đủ có trọng số nhỏ ($n \le 20$).
- Thuật toán: Quy hoạch động trạng thái (Bitmask DP).
- Độ phức tạp: $O(n^2 \cdot 2^n)$.
- Trạng thái: `dp[mask][i]` là chi phí nhỏ nhất để đi qua tập các đỉnh trong `mask` và đang đứng tại đỉnh `i`.

#### <b>E. Clique (Cái nêm / Tập đài)

- Clique là một đồ thị con đầy đủ. Bài toán tìm Clique lớn nhất trong một đồ thị bất kỳ là NP-Hard.

- Nếu đề bài yêu cầu tìm Maximum Clique, thường $n$ rất nhỏ ($n \le 40$), phải dùng <b>thuật toán Bron-Kerbosch</b> hoặc Meet-in-the-Middle.

## <b>Đồ thị 2 phía (Bipartite Graph)</b>

### 1. Định nghĩa

Đồ thị 2 phía là đồ thị mà tập các đỉnh có thể chia thành 2 tập rời rạc $U$ và $V$ sao cho:

- Mọi cạnh của đồ thị chỉ nối một đỉnh thuộc $U$ với một đỉnh thuộc $V$.
- Không có bất kỳ cạnh nào nối 2 đỉnh nằm trong cùng một tập.
- Một đồ thị là đồ thị 2 phía khi và chỉ khi nó <b>không có chu trình độ dài lẻ.</b>

### 2. Các bài toán kinh điển
#### <b>Bài toán 1: Kiểm tra đồ thị 2 phía (Bipartite Checking)</b>

<b>Ý tưởng:</b> Sử dụng thuật toán <b>Tô màu đồ thị</b> (Graph Coloring). Ta sẽ tô màu các đỉnh bằng 2 màu (ví dụ: 1 và 2).

- Bắt đầu ở một đỉnh bất kỳ, tô màu 1.
- Tất cả các đỉnh kề với nó phải được tô màu 2.
- Nếu trong quá trình duyệt (BFS hoặc DFS), ta phát hiện 2 đỉnh kề nhau mà có cùng màu, thì đồ thị đó KHÔNG phải là đồ thị 2 phía.

```c++
const int limN = 1005;
vector<int> adj[limN];
int color[limN];
bool isBipartite(int u, int c) {
    color[u] = c;
    for(const int &v: adj[u]) { // nếu đỉnh v chưa được tô màu
        if(!color[v]) {
            if(!isBipartite(v, 3-c)) return 0; // 1 -> 2, 2 -> 1
        } else if(color[v] == c) {
            return 0;
        }
    }
    return 1;
}
int main(void) {
    int n, m; cin >> n >> m;
    FOR(i, 1, m + 1) {
        int u, v; cin >> u >> v;
        adj[u].eb(v);
        adj[v].eb(u);
    }
    FOR(i, 1, n + 1) {
        if(!color[i] && !isBipartite(i, 1)) {
            return cout << "NO", 0;
        }
    }
    return cout << "YES", 0;
}
```

#### <b>Bài toán 2: Cặp ghép cực đại (Maximum Bipartite Matching)</b>

<b>Ví dụ:</b> Có $N$ thợ và $M$ công việc, mỗi thợ chỉ biết làm một số việc nhất định. Hãy phân công sao cho số công việc được làm là nhiều nhất

<b>Định nghĩa:</b>

- <b>Cặp ghép (Matching):</b> Là một tập các cạnh sao cho không có 2 cạnh nào chung đỉnh.
- <b>Đường mở (Augmenting Path):</b> Là đường đi bắt đầu từ một đỉnh chưa ghép, luân phiên đi qua các cạnh chưa ghép - đã ghép - chưa ghép..., và kết thúc ở một đỉnh chưa ghép khác. Việc "đảo ngược" trạng thái các cạnh trên đường mở sẽ giúp ta tăng số lượng cặp ghép lên 1.

#### <b>Thuật toán đường mở (Thuật toán Kuhn / DFS):</b>

Thuật toán này cực kỳ dễ cài đặt, độ phức tạp $O(V \cdot E)$, dư sức vượt qua các bài toán có số đỉnh $\le 1000$.

### 3. Định lý König

Đôi khi đề bài không hỏi "Cặp ghép cực đại" mà hỏi những thứ khác. Đây là lúc ta cần nhớ các công thức sau trên đồ thị 2 phía:

| Khái niệm | Mối liên hệ trên Đồ thị 2 phía |
|-----------|--------------------------------|
| **Tập đỉnh phủ cực tiểu** (Minimum Vertex Cover)<br>Chọn ít đỉnh nhất sao cho phủ mọi cạnh | = Kích thước cặp ghép cực đại |
| **Tập độc lập cực đại** (Maximum Independent Set)<br>Chọn nhiều đỉnh nhất sao cho không có 2 đỉnh nào kề nhau | = Tổng số đỉnh - Kích thước cặp ghép cực đại |

Trong đó `Kích thước cặp ghép cực đại` (size of maximum matching) là số lượng cạnh trong một cặp ghép cực đại của đồ thị

Ví dụ: Nếu đề bài yêu cầu "Tìm số lượng học sinh lớn nhất sao cho không có 2 học sinh nào từng quen nhau", thực chất đó là bài toán tìm <b>Tập độc lập cực đại</b>. Bạn chỉ cần tìm Cặp ghép cực đại, sau đó lấy tổng số đỉnh trừ đi là xong

## <b>Khớp và cầu</b>

### <b>0. Định nghĩa</b>

- Trong đồ thị vô hướng, một đỉnh được gọi là đỉnh khớp nếu như loại bỏ đỉnh này và các cạnh liên thuộc với nó ra khỏi đồ thị thì số thành phần liên thông của đồ thị tăng lên.

- Trong đồ thị vô hướng, một cạnh được gọi là cạnh cầu nếu như loại bỏ cạnh này ra khỏi đồ thị thì số thành phần liên thông của đồ thị tăng lên.

### <b>1. Khớp</b>

<b>Nguyên lý hoạt động:</b>

- <b>disc[u]</b> (Discovery time): Thời điểm bắt đầu thăm đỉnh $u$.
- <b>low[u]:</b> Thời điểm thăm nhỏ nhất của một đỉnh mà từ $u$ (hoặc từ con cháu của $u$ trong cây DFS) có thể đi tới qua <b>tối đa một cạnh ngược</b>.
- <b>Điều kiện 1:</b> Nếu $u$ là gốc của cây DFS và có nhiều hơn 1 nhánh con độc lập `(child > 1)`, $u$ là khớp.
- <b>Điều kiện 2:</b> Nếu $u$ không phải gốc và tồn tại một nhánh con $v$ sao cho `disc[u] <= low[v]` (tức là từ $v$ không có cách nào vòng ngược lên được tổ tiên của $u$), thì $u$ là khớp.

```c++
const int limN = 1005;
vector<int> adj[limN];
int n, m;
int disc[limN], low[limN];
set<int> ap; // lưu các khớp, dùng set để tránh trùng lặp
int time_ = 0;
void dfs(int u, int par) {
    disc[u] = low[u] = ++time_;
    int child = 0;

    for(const auto &v : adj[u]) {
        if(v == par) continue;
        if(disc[v] == 0) {
            child++;
            dfs(v, u);
            minimize(low[u], low[v]); // cập nhật low[u] từ con v
            if(disc[u] <= low[v] && par != -1) { // điều kiện khớp cho các đỉnh không phải là gốc của cây DFS
                ap.insert(u);
            }
        } else {
            // cập nhật low[u] qua cạnh ngược (back-edge)
            minimize(low[u], disc[v]);
        }
    }

    if(par == -1 && child > 1) { // điều kiện khớp cho đỉnh gốc của cây DFS
        ap.insert(u);
    }
}
int main(void) {
    cin >> n >> m;
    FOR(i, 1, m + 1) {
        int u, v; cin >> u >> v;
        adj[u].eb(v);
        adj[v].eb(u);
    }
    FOR(i, 1, n + 1) {
        if(disc[i] == 0) dfs(i, -1);
    }
    cout << sz(ap);
}
```

### <b>2. Cầu</b>

<b>Nguyên lý hoạt động:</b>

- Cạnh $(u, v)$ là cầu khi và chỉ khi từ nhánh con $v$ không có bất kỳ cạnh ngược nào nối về $u$ hoặc các tổ tiên của $u$. Điều này tương đương với điều kiện `disc[u] < low[v]`.

```c++
const int limN = 1005;
int n, m;
vector<int> adj[limN];
int disc[limN], low[limN];
int time_ = 0;
vector<pair<int, int>>bridges;

void dfs(int u, int par) {
    disc[u] = low[u] = ++time_;
    for(const auto &v: adj[u]) {
        if(v == par) continue;
        if(disc[v] == 0) {
            dfs(v, u);
            minimize(low[u], low[v]);
            if(disc[u] < low[v]) {
                bridges.eb(u, v);
            }
        } else {
            minimize(low[u], disc[v]);
        }
    }
}
int main(void) {
    cin >> n >> m;
    FOR(i, 1, m + 1) {
        int u, v; cin >> u >> v;
        adj[u].eb(v);
        adj[v].eb(u);
    }

    FOR(i, 1, n + 1) {
        if(disc[i] == 0) {
            dfs(i, -1);
        }
    }
    cout << sz(bridges);
}
```

## <b>Cây khung cực tiểu (Minimum Spanning Tree)</b>

### <b>Thuật toán Kruskal</b>
```c++
int n, m;
struct edge{
    int u, v, w;
};
vector<edge> edges;

void Kruskal(){

    sort(all(edges), [&](const edge &x, const edge &y){
        return x.w < y.w;
    });

    vector<edge> tree;
    ll d = 0;
    disjoint_set_union dsu(n + 1);
    FOR(i, 0, m) {
        if(sz(tree) == n - 1) break;
        if(dsu.Unite(edges[i].u, edges[i].v)) {
            tree.pb(edges[i]);
            d += edges[i].w;
        }
    }
    if(sz(tree) != n - 1) cout << "IMPOSSIBLE";
    else cout << d;

}
int main(void) {
    cin >> n >> m;
    FOR(i, 1, m + 1) {
        int u, v, w; cin >> u >> v >> w;
        edges.pb({u, v, w});
    }

    Kruskal();
}
```

### <b>Thuật toán Prim</b>

```c++
const int limN = 1e5 + 5;
int n, m;
int used[limN];
vector<pair<int, int>> adj[limN];

void prim(int s) {
    priority_queue<pair<int, int>, vector<pair<int, int>>, greater<pair<int, int>>> Q;
    used[s] = 1;
    for(const auto &v: adj[s]) {
        Q.push({v.se, v.fi});
    }
    ll d = 0, dem = 0;
    while(!Q.empty()) {
        auto[w, u] = Q.top(); Q.pop();
        if(used[u] == 0) {
            d += w;
            dem++;
            used[u] = 1;
            for(const auto &v: adj[u]) {
                if(used[v.fi] == 0) {
                    Q.push({v.se, v.fi});
                }
            }
        }
    }
    if(dem == n - 1) return cout << d, void();
    return cout << "IMPOSSIBLE", void();
}
int main(void) {
    cin >> n >> m;
    FOR(i, 1, m + 1){
        int u, v, w; cin >> u >> v >> w;
        adj[u].eb(v, w);
        adj[v].eb(u, w);
    }
    prim(1);
}
```

## <b>Tìm đường đi ngắn nhất</b>
### <b>Thuật toán Dijkstra</b>

- Độ phức tạp thời gian: $O(E \log V)$

```c++
typedef pair<ll, int> ii;
const int limN = 1005;
int n, m, s;
vector<ii> adj[limN];
ll dist[limN];
void dijkstra(int s) {
    memset(dist, 0x3f, sizeof dist);
    priority_queue<ii, vector<ii>, greater<ii>> pq;
    dist[s] = 0;
    pq.push({0, s});
    while(!pq.empty()) {
        auto [d, u] = pq.top(); pq.pop();
        if(d > dist[u]) continue; // pruning
        for(auto [v, w]: adj[u]) {
            if(minimize(dist[v], d + w)) { // relax
                pq.push({dist[v], v});
            }
        }
    }
}
int main(void) {
    cin >> n >> m >> s;
    FOR(i, 1, m + 1) {
        int u, v, w; cin >> u >> v >> w;
        adj[u].eb(v, w);
        adj[v].eb(u, w);
    }
    dijkstra(s);
    FOR(i, 1, n + 1) {
        cout << dist[i] << ' ';
    }
}
```
### <b>Thuật toán Dijkstra cơ bản cho đồ thị dày.</b>

- Độ phức tạp thời gian: $O(V^2)$

```c++
typedef pair<ll, int> ii;
const int limN = 1005;
int n, m, s;
vector<ii> adj[limN];
ll dist[limN];
bool visited[limN];
const ll INF = 1e18;
void dijkstra(int s) {
    fill(dist + 1, dist + 1 + n, INF);
    memset(visited, 0, sizeof visited);
    dist[s] = 0;
    FOR(i, 1, n + 1) {
        int u = -1;
        // tìm đỉnh u chưa được thăm có khoảng cách ngắn nhất
        FOR(j, 1, n + 1) {
            if (!visited[j] && (u == -1 || dist[j] <= dist[u])) u = j;
        }
        if (u == -1 || dist[u] == INF) break;
        visited[u] = 1;
        // relax
        for (const auto& [v, w] : adj[u]) {
            if (visited[v]) continue;
            minimize(dist[v], dist[u] + w);
        }

    }
}
int main(void) {
    cin >> n >> m >> s;
    FOR(i, 1, m + 1) {
        int u, v, w; cin >> u >> v >> w;
        adj[u].eb(v, w);
        adj[v].eb(u, w);
    }
    dijkstra(s);
    FOR(i, 1, n + 1) {
        cout << dist[i] << ' ';
    }
}
```

### <b>Thuật toán Dijkstra trên lưới 2D</b>

<div class="problem-link">
  🔗 <strong>Ví dụ:</strong>
  <a href="https://oj.vnoi.info/problem/hcm_thpt_21_c" target="_blank">
    HSG THPT TPHCM 2021 - Tìm đường
  </a>
</div>

```c++
const int limN = 505;
typedef pair<ll, pair<int, int>> iii;
int dx[4] = {0, 0, -1, 1};
int dy[4] = {1, -1, 0, 0};
ll A[limN][limN], dist[limN][limN];
int n;
void dijkstra(int s, int t) {
    memset(dist, 0x3f, sizeof dist);
    priority_queue<iii, vector<iii>, greater<iii>> pq;
    pq.push({0, {s, t}});
    dist[s][t] = 0;
    while(!pq.empty()) {
        iii u = pq.top(); pq.pop();
        ll d = u.fi;
        auto [x, y] = u.se;
        if(d > dist[x][y]) continue;
        FOR(k, 0, 4) {
            int nx = x + dx[k];
            int ny = y + dy[k];
            if(nx > 0 && ny > 0 && nx <= n && ny <= n) {
                // relax
                ll nd = max(dist[x][y], abs(A[nx][ny] - A[x][y]));
                if(minimize(dist[nx][ny], nd)) {
                    pq.push({nd, {nx, ny}});
                }
            }
        }
    }
}
int main(void) {
    cin >> n;
    FOR(i, 1, n + 1) {
        FOR(j, 1, n + 1) {
            cin >> A[i][j];
        }
    }
    dijkstra(1, 1);
    cout << dist[n][n];
}
```

> <b>Note:</b> Đề bài là dạng `min(max...)` nên có thể chặt nhị phân trên kết quả.
{: .prompt-info }

```c++
int dx[4] = {0, 0, 1, -1};
int dy[4] = {1, -1, 0, 0};
typedef pair<int, int> ii;
int n;
const int limN = 505;
int A[limN][limN];
int visited[limN][limN];
int session = 0;
inline bool check(int M) {
    session++;
    queue<ii> qu;
    qu.push({1, 1});
    visited[1][1] = session;
    while(!qu.empty()) {
        auto [x, y] = qu.front(); qu.pop();
        if(x == n && y == n) return 1;
        FOR(k, 0, 4) {
            int nx = x + dx[k];
            int ny = y + dy[k];
            if(nx >= 1 && ny >= 1 && nx <= n && ny <= n && visited[nx][ny] != session && abs(A[nx][ny] - A[x][y]) <= M) {
                visited[nx][ny] = session;
                qu.push({nx, ny});
            }
        }
    }
}
int main(void) {
    cin >> n;
    FOR(i, 1, n + 1) {
        FOR(j, 1, n + 1) {
            cin >> A[i][j];
        }
    }
    int l = 0, r = 1e6, res;
    while(l <= r) {
        int m = (l + r) >> 1;
        if(check(m)) {
            res = m;
            r = m - 1;
        } else {
            l = m + 1;
        }
    }
    cout << res;
}
```


### <b>Thuật toán Bellman Ford</b>
```c++
int const INF = 1e9 + 7;
int const limN = 1005;
vector<tuple<int, int, ll>> edges;
int n, m, s;
ll dist[limN];
void bellmanford(int s) {
    memset(dist, 0x3f, sizeof dist);
    dist[s] = 0;
    FOR(i, 1, n) {
        for(auto [u, v, w]: edges) {
            if(dist[u] < INF) {
                minimize(dist[v], dist[u] + w);
            }
        }
    }
}
int main(void) {
    cin >> n >> m >> s;
    FOR(i, 1, m + 1) {
        int u, v, w; cin >> u >> v >> w;
        edges.pb({u, v, w});
        edges.pb({v, u, w});
    }
    bellmanford(s);
    FOR(i, 1, n + 1) {
        cout << dist[i] << ' ';
    }
}
```

### <b>Thuật toán Floyd Warshall</b>
```c++
const int limN = 105;
ll dist[limN][limN];

int main(void) {
    memset(dist, 0x3f, sizeof dist);
    FOR(i, 1, limN) {
        dist[i][i] = 0;
    }

    int n, m, s; cin >> n >> m >> s;
    FOR(i, 1, m + 1) {
        int u, v, w; cin >> u >> v >> w;
        dist[u][v] = dist[v][u] = w;
    }
    FOR(k, 1, n + 1) {
        FOR(i, 1, n + 1) {
            FOR(j, 1, n + 1) {
                minimize(dist[i][j], dist[i][k] + dist[k][j]);
            }
        }
    }
    FOR(i, 1, n + 1) {
        cout << dist[s][i] << ' ';
    }
}
```

## <b>Quy hoạch động trên DAG</b>

### <b>Push DP sử dụng thuật toán Kahn</b>

```c++
int n, m;
const int limN = 2e5 + 5;
vector<int> adj[limN];
int deg_in[limN], dp[limN];
int kahn() {
    int indeg[limN];
    memcpy(indeg, deg_in, sizeof deg_in);
    queue<int> qu;
    FOR(u, 1, n + 1) {
        if (!indeg[u]) {
            qu.push(u);
            dp[u] = 0; // Base case: Đường đi ngắn nhất từ u là 0 (chính nó)
        }
    }
    int res = 0;
    while (!qu.empty()) {
        int u = qu.front(); qu.pop();
        for (const int& v : adj[u]) {
            // Push: Đỉnh u đã tối ưu, update giá trị cho đỉnh con v
            maximize(dp[v], dp[u] + 1);
            maximize(res, dp[v]);
            if (--indeg[v] == 0) qu.push(v);
        }
    }
    return res;
}
int main(void) {
    cin >> n >> m;
    FOR(i, 1, m + 1) {
        int u, v; cin >> u >> v;
        adj[u].eb(v);
        deg_in[v]++;
    }
    cout << kahn();
}
```

### <b>Pull DP sử dụng DFS</b>

- Không an toàn nếu $n \ge 10^5$, thay vào đó sử dụng `push DP`.

```c++
int n, m;
const int limN = 2e5 + 5;
vector<int> adj[limN];
int dp[limN];
int dfs(int u) {
    if (dp[u] != -1) return dp[u];
    dp[u] = 0; // Base case: Đường đi ngắn nhất từ u là 0 (chính nó)
    for (const int& v : adj[u]) {
        // Pull: Lấy giá trị tốt nhất từ các đỉnh con v
        maximize(dp[u], dfs(v) + 1);
    }
    return dp[u];
}
int main(void) {
    cin >> n >> m;
    FOR(i, 1, m + 1) {
        int u, v; cin >> u >> v;
        adj[u].eb(v);
    }
    memset(dp, -1, sizeof(dp));
    int res = 0;
    FOR(u, 1, n + 1) {
        maximize(res, dfs(u));
    }
    cout << res;
}
```

## <b>Cây</b>

### <b>1. Khái niệm về cây</b>

1. Trong lý thuyết đồ thị, cây có các thuộc tính sau: 
- Là một đồ thị vô hướng gồm $V$ đỉnh và $V-1$ cạnh
- Liên thông 
- Không có chu trình
2. Giữa hai đỉnh bất kỳ trong cây luôn có <b>duy nhất một đường đi đơn</b>.
3. Nếu thêm một cạnh bất kỳ vào cây, ta sẽ tạo ra đúng một chu trình.
4. Nếu xóa đi một cạnh bất kỳ, đồ thị sẽ mất tính liên thông trở thành một Rừng - Forest.

Trong CP, cách tối ưu và phổ biến nhất để biểu diễn cây là sử dụng <b>Danh sách kề</b> (Adjacency List).

### <b>2. Duyệt cây và Tính toán thông số cơ bản</b>

Thuật toán Tìm kiếm theo chiều sâu (DFS) là "xương sống" của hầu hết các bài toán về cây. Thông qua một lần chạy DFS, ta có thể lấy được các thông tin quan trọng của mỗi đỉnh:

- depth[u] hay dist[u]: Độ sâu của đỉnh u (khoảng cách từ gốc).
- sz[u]: Kích thước cây con gốc u (số lượng đỉnh nằm trong nhánh của u).
- parent[u]: Cha trực tiếp của u.

```c++
const int limN = 1e5 + 5;
vector<int> adj[limN];
int dist[limN];  // khởi tạo -1 (unvisited)
int sz[limN]; 
int parent[limN]; // khởi tạo -1 nghĩa chưa có cha

// Gọi hàm: dfs(root, 0)
void dfs(int u, int p) {
    dist[u] = (p == 0 ? 0 : dist[p] + 1);   // Tính dist dựa trên cha
    sz[u] = 1;                              // Bản thân u là 1 đỉnh
    parent[u] = p;                          // Lưu lại cha của u
    for (const int &v : adj[u]) {
        if (v == p) continue; // Không quay ngược lại cha
        dfs(v, u);
        sz[u] += sz[v]; // Cộng dồn kích thước cây con
    }
}
```

### <b>3. Đường kính của Cây (Tree Diameter)</b>

Đường kính của cây là đường đi dài nhất giữa hai đỉnh bất kỳ. Thuật toán tối ưu nhất là dùng 2 lần DFS/BFS:

1. Bắt đầu DFS từ đỉnh bất kỳ (ví dụ đỉnh 1), tìm đỉnh $A$ xa nhất.
2. Bắt đầu DFS từ đỉnh $A$, tìm đỉnh $B$ xa nhất. Khoảng cách từ $A$ đến $B$ chính là đường kính của cây.

```c++
const int limN = 1e5 + 5;
vector<int> adj[limN];
int dist[limN];
void dfs(int u, int p) {
    for (int v : adj[u]) {
        if (v == p) continue;
        dist[v] = dist[u] + 1;
        dfs(v, u);
    }
}
int get_diameter(int n) {
    // Lần 1: Tìm đỉnh xa đỉnh 1 nhất
    dist[1] = 0;
    dfs(1, 0);
    int nodeA = 1;
    for (int i = 1; i <= n; i++) {
        if (dist[i] > dist[nodeA]) nodeA = i;
    }
    // Lần 2: Tìm đỉnh xa đỉnh nodeA nhất
    dist[nodeA] = 0;
    dfs(nodeA, 0);
    int nodeB = nodeA;
    for (int i = 1; i <= n; i++) {
        if (dist[i] > dist[nodeB]) nodeB = i;
    }
    return dist[nodeB]; // dist[nodeB] là đường kính
}
```

### <b>4. Trải phẳng cây (DFS Order / Euler Tour)</b>
Kỹ thuật biến đổi một cấu trúc cây thành một mảng 1 chiều để áp dụng các cấu trúc dữ liệu như Segment Tree hay Fenwick Tree (BIT).

- <b>Ý tưởng:</b> Dùng bộ đếm `timer`, lưu lại thời điểm bắt đầu vào đỉnh `(tin[u])` và thời điểm thoát khỏi đỉnh `(tout[u])`.
- <b>Tính chất:</b> Toàn bộ các đỉnh nằm trong cây con của $u$ sẽ có chỉ số tin nằm trong đoạn $[tin[u], tout[u]]$. Mọi bài toán truy vấn/cập nhật trên cây con lúc này trở thành truy vấn/cập nhật trên một đoạn mảng 1 chiều.

```c++
int timer = 0;
int tin[MAXN];
int tout[MAXN];
int flat_array[MAXN]; // Mảng 1 chiều lưu lại giá trị các đỉnh theo thứ tự thăm
void dfs_tour(int u, int p) {
    tin[u] = ++timer;       // Ghi nhận thời điểm vào
    flat_array[timer] = u;  // (Tùy chọn) Lưu lại đỉnh tại thời điểm này
    for (int v : adj[u]) {
        if (v == p) continue;
        dfs_tour(v, u);
    }
    tout[u] = timer;        // Ghi nhận thời điểm ra
    // Lưu ý: Có 2 cách cài tout. 
    // Cài tout[u] = timer như trên thì đoạn của cây con u là [tin[u], tout[u]].
    // Nếu cài tout[u] = ++timer thì đoạn sẽ là [tin[u], tout[u] - 1].
}
```

Giả sử có bài toán:

1. Cộng thêm giá trị $X$ vào toàn bộ đỉnh thuộc cây con gốc $u$.
2. Tính tổng giá trị hiện tại của một đỉnh $v$.

Sau khi gọi dfs_tour(root, 0), bài toán trên cây con gốc $u$ trở thành thao tác cộng đoạn $[tin[u], tout[u]]$ trên mảng 1 chiều. Ta có thể dùng BIT để xử lý gọn gàng với độ phức tạp $O(\log N)$:

```c++
// Thao tác 1: Cộng X vào toàn bộ cây con gốc u
// Tương đương update mảng hiệu số (Difference Array)
void update_subtree(int u, int X, int n) {
    fenwick_tree.update(tin[u], X, n);             // Cộng X vào đầu đoạn
    fenwick_tree.update(tout[u] + 1, -X, n);       // Trừ X ở ngay sau cuối đoạn
}
// Thao tác 2: Lấy giá trị hiện tại của đỉnh v
int get_node_value(int v) {
    return fenwick_tree.query(tin[v]);
}
```

### <b>5. Tổ tiên chung gần nhất (LCA - Binary Lifting)</b>

Hầu hết các bài toán thao tác trên đường đi giữa 2 đỉnh $(u, v)$ đều cần dùng LCA.

<b>Kỹ thuật: Nhảy nhị phân (Binary Lifting)</b>

- Độ phức tạp: Khởi tạo $\mathcal{O}(N \log N)$, Truy vấn $\mathcal{O}(\log N)$.
- Ý tưởng: Mảng up[u][j] lưu tổ tiên thứ $2^j$ của đỉnh $u$.
-  Công thức truy hồi: 

$$up[u][j] = up[up[u][j-1]][j-1]$$

- Khoảng cách giữa 2 đỉnh u và v trên cây có thể tính nhanh bằng công thức:

$$dist(u, v) = dist[u] + dist[v] - 2 * dist[\text{get_lca}(u, v)]$$

(Kết hợp Mảng cộng dồn trên cây (Tree Prefix Sum) để tính tổng/max/min trên đường đi từ $u$ đến $v$.)

```c++
const int limN = 1e5 + 5;
vector<int> adj[limN];
int dist[limN];
const int LOG = 20; // log2(2e5) ~ 18, chọn 20 cho an toàn
int up[limN][LOG];
// Bước 1: Tiền xử lý mảng 'up' thông qua DFS
// Gọi hàm: dfs_lca(root, root)
void dfs_lca(int u, int p) {
    up[u][0] = p;
    for (int i = 1; i < LOG; i++) {
        up[u][i] = up[up[u][i - 1]][i - 1];
    }
    for (int v : adj[u]) {
        if (v == p) continue;
        dist[v] = dist[u] + 1;
        dfs_lca(v, u);
    }
}
// Bước 2: Truy vấn LCA(u, v)
int get_lca(int u, int v) {
    if (dist[u] < dist[v]) swap(u, v);
    // Đưa u và v về cùng độ sâu
    int diff = dist[u] - dist[v];
    for (int i = 0; i < LOG; i++) {
        if ((diff >> i) & 1) {
            u = up[u][i];
        }
    }
    if (u == v) return u;
    // Nhảy cả u và v lên cao nhất có thể mà chưa chạm mặt nhau
    for (int i = LOG - 1; i >= 0; i--) {
        if (up[u][i] != up[v][i]) {
            u = up[u][i];
            v = up[v][i];
        }
    }
    return up[u][0]; // Cha trực tiếp của điểm dừng là LCA
}
```

### <b>6. Quy hoạch động trên cây (Tree DP)</b>

#### <b>Tree DP cơ bản (Từ dưới lên)</b>

Trạng thái của đỉnh $u$ được tính dựa trên các đỉnh con $v$ của nó.

<b>Bài toán kinh điển:</b> Tìm kích thước của Tập con độc lập lớn nhất (Maximum Independent Set - MIS). Không có bất kỳ 2 đỉnh nào kề nhau được chọn.

- dp[u][0]: Kết quả tối ưu trong cây con gốc $u$ nếu không chọn đỉnh $u$.
- dp[u][1]: Kết quả tối ưu trong cây con gốc $u$ nếu chọn đỉnh $u$.

```c++
int dp[MAXN][2];
void dfs_basic_dp(int u, int p) {
    dp[u][0] = 0;
    dp[u][1] = 1; // Chọn chính u thì ban đầu có kích thước 1
    for (int v : adj[u]) {
        if (v == p) continue;
        dfs_basic_dp(v, u);
        // Nếu không chọn u, ta có thể chọn hoặc không chọn v (lấy Max)
        dp[u][0] += max(dp[v][0], dp[v][1]);
        // Nếu chọn u, ta bắt buộc KHÔNG ĐƯỢC chọn v
        dp[u][1] += dp[v][0];
    }
}
// Đáp án cho toàn bộ cây là: max(dp[root][0], dp[root][1])
```

#### <b>Re-rooting DP (Quy hoạch động thay đỉnh gốc / In-Out DP)</b> 

Dạng bài yêu cầu tính toán một giá trị cho mọi đỉnh làm gốc với độ phức tạp $\mathcal{O}(N)$:

1. Bước 1: DFS từ dưới lên để tính đáp án cho nhánh con (In-DP).
2. Bước 2: DFS từ trên xuống để tính phần đóng góp của nhánh ngoài (Out-DP) và cập nhật đáp án cuối cùng.

<b>Bài toán kinh điển:</b> Tính tổng khoảng cách từ đỉnh $u$ đến tất cả các đỉnh còn lại trong cây, áp dụng cho mọi đỉnh $u$.

Thay vì chạy DFS từ mỗi đỉnh mất $\mathcal{O}(N^2)$, ta sẽ làm trong $\mathcal{O}(N)$ với 2 lần DFS:

1. DFS 1 (Bottom-up): Tính tổng khoảng cách trong cây con của $u$ (dp_in[u]) và kích thước cây con (sz[u]).

2. DFS 2 (Top-down): Cập nhật đáp án khi dời gốc từ cha $u$ xuống con $v$. Khi dời gốc xuống $v$, các đỉnh trong cây con của $v$ sẽ gần lại 1 bước (giảm sz[v]), còn các đỉnh ngoài cây con của $v$ sẽ xa ra 1 bước (tăng N - sz[v]).

```c++
long long dp_in[MAXN]; // Tổng khoảng cách tới các đỉnh trong cây con
int sz[MAXN];          // Kích thước cây con
long long ans[MAXN];   // Đáp án cuối cùng cho mỗi đỉnh
int N;                 // Tổng số đỉnh của cây
// Bước 1: DFS từ dưới lên để tính In-DP
void dfs_in(int u, int p) {
    sz[u] = 1;
    dp_in[u] = 0;
    for (int v : adj[u]) {
        if (v == p) continue;
        dfs_in(v, u);
        sz[u] += sz[v];
        dp_in[u] += dp_in[v] + sz[v]; // Mỗi đỉnh trong cây con v sẽ cách u thêm 1 cạnh
    }
}
// Bước 2: DFS từ trên xuống để tính Out-DP (Re-rooting)
void dfs_out(int u, int p) {
    // Với gốc ban đầu (thường là 1), đáp án chính là dp_in
    // ans[1] = dp_in[1] đã được gán trước khi gọi dfs_out
    for (int v : adj[u]) {
        if (v == p) continue;
        // Công thức dời gốc từ u xuống v:
        // ans[v] = ans[u] - (số đỉnh lại gần 1 bước) + (số đỉnh ra xa 1 bước)
        ans[v] = ans[u] - sz[v] + (N - sz[v]);
        dfs_out(v, u);
    }
}
void solve_rerooting() {
    // Giả sử đã đọc cây và N
    dfs_in(1, 0);
    ans[1] = dp_in[1]; // Đỉnh 1 làm gốc chuẩn
    dfs_out(1, 0);
    // In ra đáp án
    for (int i = 1; i <= N; i++) {
        cout << ans[i] << " ";
    }
}
```


### <b>7. DSU on Tree (Sack / Small to Large Merging)</b>

Kỹ thuật này (còn gọi là thuật toán Sack) dùng để giải các bài toán <b>truy vấn offline trên cây con</b> (ví dụ: đếm số màu phân biệt trong cây con của đỉnh $u$) với độ phức tạp $O(N \log N)$ thay vì $O(N^2)$.

Ý tưởng cốt lõi: Khi gộp kết quả của các cây con lên đỉnh cha, ta luôn giữ lại mảng đánh dấu của <b>thằng con lớn nhất</b> (heavy child - đỉnh có kích thước cây con lớn nhất) và chỉ tính lại thông tin cho các thằng con nhỏ (light children).

```c++
const int limN = 1e5 + 5;
vector<int> adj[limN];
int dist[limN];
int sz[limN];
int is_big[limN], color[limN];
int cnt[limN];      // Mảng đếm tần số (hoặc cấu trúc dữ liệu lưu trạng thái)
int bigChild[limN]; // Lưu thằng con lớn nhất của mỗi đỉnh
int ans[limN];      // Lưu đáp án cho mỗi đỉnh
// Bước 1: DFS để tính sz và tìm bigChild
void dfs(int u, int p) {
    sz[u] = 1;
    int max_sub = 0;
    for (int v : adj[u]) {
        if (v == p) continue;
        dfs(v, u);
        sz[u] += sz[v];
        if (sz[v] > max_sub) {
            max_sub = sz[v];
            bigChild[u] = v;
        }
    }
}
// Hàm thêm/xóa dữ liệu của cây con gốc u
void add_subtree(int u, int p, int val) {
    cnt[color[u]] += val; // Giả sử đỉnh u có màu color[u]
    // Cập nhật biến kết quả toàn cục ở đây nếu cần
    for (int v : adj[u]) {
        if (v != p && !is_big[v]) { // Không tính lại bigChild
            add_subtree(v, u, val);
        }
    }
}
// Bước 2: DFS Sack tính kết quả
// keep = 1 nếu u là bigChild của cha nó (cần giữ lại dữ liệu), ngược lại = 0
void dfs_sack(int u, int p, bool keep) {
    // Duyệt qua các con nhỏ (light children) trước
    for (int v : adj[u]) {
        if (v != p && v != bigChild[u]) {
            dfs_sack(v, u, 0);
        }
    }
    // Duyệt thằng con lớn (heavy child) và giữ lại dữ liệu của nó
    if (bigChild[u]) {
        dfs_sack(bigChild[u], u, 1);
        is_big[bigChild[u]] = 1; // Đánh dấu để hàm add_subtree bỏ qua
    }
    // Thêm bản thân đỉnh u và các con nhỏ vào cấu trúc dữ liệu
    add_subtree(u, p, 1);
    // Ghi nhận đáp án cho đỉnh u
    ans[u] = /* Giá trị hiện tại */;
    // Dọn dẹp đánh dấu
    if (bigChild[u]) {
        is_big[bigChild[u]] = 0;
    }
    // Nếu u không phải là con lớn của cha nó, xóa toàn bộ dữ liệu vừa thêm
    if (!keep) {
        add_subtree(u, p, -1);
    }
}
```