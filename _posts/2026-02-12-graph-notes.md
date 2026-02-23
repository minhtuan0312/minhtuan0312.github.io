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

## <b>Quy hoạch động trên DAG</b>

### <b>Push DP sử dụng thuật toán Kahn</b>

```c++
int n, m;
const int limN = 2e5 + 5;
vector<int> adj[limN];
int in_deg[limN], dp[limN];
int kahn() {
    int indeg[limN];
    memcpy(indeg, in_deg, sizeof in_deg);
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
        in_deg[v]++;
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

## <b>Thuật toán Tarjan</b>

Phần này đã được tách thành blog mới

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
    for(const int &v: adj[s]) {
        Q.push({v.se, v.fi});
    }
    ll d = 0, dem = 0;
    while(!Q.empty()) {
        auto[w, u] = Q.top(); Q.pop();
        if(used[u] == 0) {
            d += w;
            dem++;
            used[u] = 1;
            for(const int &v: adj[u]) {
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

## <b>Cây</b>

