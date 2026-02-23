---
layout: post
title: Kĩ thuật bắc cầu trên DAG bằng bitset và DAG hóa Pull DP🤯
date: 2026-02-23 10:00 +0700
categories: [trick]
tags: [trie, trie xor, string hashing, tarjan, graph, grid]
math: true
---

## <b>A. Kỹ thuật bắc cầu trên DAG bằng Bitset</b>

Kỹ thuật bắc cầu kết hợp với Bitset DP trên DAG là kỹ thuật giải quyết triệt để bài toán: "Cho đồ thị có hướng, hãy xác định xem từ đỉnh $u$ có thể đi đến đỉnh $v$ hay không cho mọi cặp $(u, v)$".

<div class="problem-link">
  🔗 <strong>CSES - Reachable Nodes</strong>
  <a href="https://lqdoj.edu.vn/problem/cses2138" target="_blank">
    Nút có thể đi đến được
  </a>
</div>

### <b>1. Bản chất của bắc cầu và sự tối ưu của Bitset</b>

Trong đồ thị, nếu có đường đi $A \to B$ và $B \to C$, tính bắt cầu cho ta biết có đường đi từ $A \to C$. Sự bắc cầu của một đồ thị là việc ta thêm cạnh nối trực tiếp từ $u$ đến $v$ nếu có bất kỳ đường đi nào mà từ $u$ đến $w$ và từ $w$ đến $v$.

Tại sao không dùng các thuật toán thông thường?

- <b>Floyd-Warshall</b>: Độ phức tạp $\mathcal{O}(V^3)$. Quá chậm nếu $V \ge 500$.
- <b>DFS/BFS từ mọi đỉnh:</b> Độ phức tạp $\mathcal{O}(V \times (V + E))$. Vẫn có rủi ro TLE nếu đồ thị đặc ($E \approx V^2$).

### <b>2. Giải pháp bitset DP</b>

Thay vì duyệt từng đỉnh, ta gọi `bitset<limN> reach[u]` là tập hợp tất cả các đỉnh có thể đến được từ $u$.

Nếu có cạnh $u \to v$, ta có phương trình truyền trạng thái:

$$reach[u] = reach[u] \cup reach[v]$$

Trong C++, <b>phép hợp (Union) hai tập hợp được thực hiện bằng toán tử Bitwise OR (`|`)</b>. Vì CPU xử lý song song 64 bit trong 1 chu kỳ máy, độ phức tạp lập tức giảm xuống còn $\mathcal{O}(\frac{V \times (V + E)}{64})$.

### <b>2. Trường hợp không có chu trình</b>

Để phương trình $reach[u] \cup reach[v]$ hoạt động đúng, khi ta cập nhật cho $u$, thì $reach[v]$ phải là <b>kết quả cuối cùng (không bị thay đổi nữa)</b>.

$\Rightarrow$ Ta phải duyệt các đỉnh theo Thứ tự <b>Topo ngược (Reverse Topological Order)</b>.

```c++
int n, m;
const int limN = 5e4 + 5;
vector<int> adj[limN];
int in_deg[limN];
bitset<limN> reach[limN];
vector<int> topo;
// sắp xếp topo bằng kahn
void bfs() {
    queue<int> qu;
    FOR(u, 1, n + 1) {
        if (!in_deg[u]) qu.push(u);
    }
    while (!qu.empty()) {
        int u = qu.front(); qu.pop();
        topo.eb(u);
        for (const int& v : adj[u]) {
            in_deg[v]--;
            if (!in_deg[v]) qu.push(v);
        }
    }
}
int main(void) {
    cin >> n >> m;
    FOR(i, 1, m + 1) {
        int u, v; cin >> u >> v;
        adj[u].eb(v);
        in_deg[v]++;
    }
    // lấy thứ tự topo
    bfs();
    reverse(all(topo)); // đảo ngược topo vì ta cần những vị trí v tính trước
    // truyền trạng thái trên dag bằng bitset
    for (const int& u : topo) {
        reach[u][u] = 1; // u luôn đi được đến chính nó
        for (const int& v : adj[u]) {
            reach[u] |= reach[v]; // O(N/64)
        }
    }
    FOR(u, 1, n + 1) {
        cout << reach[u].count() << ' '; // số lượng bit 1 đang bật tương ứng số đỉnh đi đến được
    }
}
```
- <b>Độ phức tạp:</b> $$O(N + M + \frac{M \times N}{64})$$

```c++
// truy vấn: u có đến được v không?
int q; cin >> q;
while (q--) {
    int u, v; cin >> u >> v;
    cout << (reach[u][v]? "YES": "NO") << nl;
}
```

### <b>3. Trường hợp có chu trình</b>

<div class="problem-link">
  🔗 <strong>CSES - Reachability Queries</strong>
  <a href="https://lqdoj.edu.vn/problem/cses2143" target="_blank">
    Truy vấn khả năng đi đến được
  </a>
</div>

Nếu có chu trình, sắp xếp Topo sẽ thất bại nên ta sẽ thực hiện thao tác <b>Nén đồ thị</b> để đưa nó về DAG.

> Kỹ thuật nén đồ thị sử dụng Tarjan tham khảo `dạng 2` ở blog: [Tarjan Algorithm](/posts/tarjan-algorithm/)
{: .prompt-info}

Sau khi nén thì ta vẫn xử lý bằng bitset tương tự trên.

```c++
int n, m, q;
const int limN = 5e4 + 5;
vector<int> adj[limN];
int timer = 0;
int disc[limN], low[limN];
stack<int> st;
bool onStack[limN];
int scc = 0;
int scc_id[limN];
void dfs(int u) {
    disc[u] = low[u] = ++timer;
    st.push(u);
    onStack[u] = 1;
    for(const int &v: adj[u]) {
        if(!disc[v]) {
            dfs(v);
            minimize(low[u], low[v]);
        } else if(onStack[v]) {
            minimize(low[u], disc[v]);
        }
    }
    if(disc[u] == low[u]) {
        scc++;
        while(1) {
            int v = st.top(); st.pop();
            scc_id[v] = scc;
            onStack[v] = 0;
            if(u == v) break;
        }
    }
}
vector<int> dag_adj[limN];
bitset<limN> reach[limN];
int main(void) {
    cin >> n >> m >> q;
    FOR(i, 1, m + 1) {
        int u, v; cin >> u >> v;
        adj[u].eb(v);
    }
    FOR(u, 1, n + 1) {
        if(!disc[u]) dfs(u);
    }
    FOR(u, 1, n + 1) {
        if(!scc_id[u]) continue;
        for(const int &v: adj[u]) {
            if(scc_id[v] && scc_id[u] != scc_id[v]) {
                dag_adj[scc_id[u]].eb(scc_id[v]);
            }
        }
        auto tmp = dag[scc_id[u]]; // loại bỏ các cạnh trùng để tối ưu thời gian
        tmp.erase(unique(all(tmp)), tmp.end());
    }
    FOR(u, 1, scc + 1) {
        reach[u][u] = 1;
        for(const int &v: dag_adj[u]) {
            reach[u] |= reach[v];
        }
    }
    while(q--) {
        int u, v; cin >> u >> v;
        u = scc_id[u];
        v = scc_id[v];
        cout << (reach[u][v] ? "YES": "NO") << nl;
    }
}
```

### <b>4. Lưu ý</b>
- Giới hạn của $N$: Kỹ thuật Bitset DP hoạt động tuyệt vời nhất khi $N \le 5000$ (Thậm chí $N \le 10^4$ nếu Time Limit xông xênh). Nếu bài toán cho $N = 10^5$, thì mảng bitset sẽ tốn hơn 1 GB RAM (MLE ngay lập tức) và $\mathcal{O}(N^2/64)$ sẽ sinh ra khoảng $1.5 \times 10^8$ phép tính (có thể TLE). Nếu $N$ lớn, thường bài toán sẽ yêu cầu một cấu trúc dữ liệu khác (như <b>DSU trên cây</b>) hoặc là một dạng đồ thị đặc thù.

- Khi nén SCC, rất dễ sinh ra <b>trường hợp có nhiều cạnh cùng nối từ scc_id[v] sang scc_id[v]</b>. Việc OR bitset nhiều lần cùng một giá trị không làm sai kết quả, nhưng gây tốn thời gian vô ích. Ta nên dùng `unique` hoặc mảng đánh dấu để <b>lược bỏ các cạnh trùng lặp</b> khi dựng `dag_adj`.

## <b>B. Ngầm định đồ thị DAG (Directed Acyclic Graph) trong Push DP (Forward DP)</b>

Trong DP truyền thống <b>Pull DP (Backward DP)</b>, khi đứng tại trạng thái $i$, ta thường tự hỏi: "Để đạt được $dp[i]$, mình phải lấy kết quả từ những trạng thái $j$ nào trong quá khứ?"

- <b>Công thức:</b> $dp[i] = \min/\max/\sum (dp[j])$ với $j < i$.

- <b>Nhược điểm:</b> Đôi khi việc tìm lại các trạng thái $j$ hợp lệ ở quá khứ <b>rất khó</b>, đặc biệt khi độ dài bước nhảy không cố định.

Ngược lại, với <b>Push DP (Forward DP)</b>, khi đứng tại trạng thái $i$, ta giả định rằng $dp[i]$ <b>đã mang giá trị tối ưu hoặc chính xác nhất</b>. Ta tự hỏi: "Từ $i$, mình có thể đi đến những trạng thái tương lai $j$ nào, và đóng góp giá trị của $dp[i]$ cho $dp[j]$ ra sao?"

- <b>Công thức:</b> $dp[j] = \min/\max/\sum (dp[j], f(dp[i]))$ với mọi $j > i$ đi tới được từ $i$.
- <b>Bản chất DAG:</b> Đỉnh là các trạng thái (states). Cạnh có hướng $i \to j$ là các bước chuyển đổi (transitions). Việc duyệt for i từ 1 đến N chính là duyệt <b>Topological Sort</b> trên cái DAG ngầm định đó.

<b>Tại sao Push DP lại mạnh?</b>

- Dễ prune (cắt tỉa): Nếu $dp[i]$ là trạng thái không hợp lệ (vd: không thể đi tới được), ta chỉ cần `if (!valid[i]) continue;`, tiết kiệm cực nhiều thời gian thay vì bắt đỉnh $j$ tương lai phải check lại $i$.
- <b>Khớp hoàn hảo với cấu trúc dữ liệu:</b> Khi kết hợp với Trie, Hashing, Segment Tree, việc "đẩy" trạng thái đi xa rất tự nhiên.

## <b>C. Chuyển hóa Push DP thành bắc cầu trên DAG bằng bitset</b>

<div class="problem-link">
  🔗 <strong>MARISAOJ - 171</strong>
  <a href="https://marisaoj.com/problem/171" target="_blank">
    Kết hợp xâu (Biến thể bài toán <b>Word Break</b> trên leetcode)
  </a>
</div>


Lấy <b>bài toán trên</b> làm hệ quy chiếu. Giả sử chúng ta đang đứng tại index $u (1 \le u \le \|S\|)$ và có các từ hợp lệ độ dài `len`.

- <b>Ta định nghĩa:</b> `dp[len] = true` nếu ta có thể ghép một xâu $T$ độ dài `len` vào vị trí sau $u$.
- <b>Base case:</b> Với mọi $u$ nằm trong khoảng, `dp[0] = 1`. (Vì luôn luôn có thể ghép xâu rỗng)

```c++
// pseudocode
int res = 0;
FOR(u, 1, n + 1) {
    bool dp[n - u + 2];
    dp[0] = 1; // xâu rỗng luôn ghép được
    for (int len = 0; len <= n - u + 1; len++) {
        if (!dp[len]) continue;
        // push dp: lúc này len là độ dài mà xâu ghép được, ta cần đi ghép tiếp
        maximize(res, len); // cập nhật kết quả luôn
        // u + len là vị trí tiếp theo trong xâu S cần khớp
        // ta tìm các xâu T[v] khớp với S bắt đầu từ S[u + len]
        // giả sử tìm được các xâu T[v] với độ dài len_T ghép vào được vị trí u + len thì
        // dp[len + len_T] = 1
    }
}
```

Trong các bài toán biến thể của <b>Word Break</b>, cách tiếp cận DP thông thường đôi khi gặp khó khăn về mặt thời gian hoặc yêu cầu xử lý nhiều truy vấn. Hôm nay, chúng ta sẽ phân tích một kỹ thuật cực mạnh: <b>Chuyển đổi DP sang bài toán Reachability trên DAG và tối ưu bằng Bitset</b>.

### <b>1. Phân tích chuyển hóa Push DP $\to$ DAG + Bitset (Word Break)</b>

Ta coi mỗi vị trí ngăn cách giữa các ký tự trong xâu $S$ (độ dài $N$) là một nút trên đồ thị.

- Đỉnh: Tập gồm $N+1$ đỉnh từ $0$ đến $N$. Đỉnh $u$ đại diện cho trạng thái: "Đã khớp xong $u$ ký tự đầu tiên của xâu".
- Cạnh: Có một cạnh có hướng từ $u \to v$ ($u < v$) nếu và chỉ nếu đoạn con $S[u+1 \dots v]$ khớp với một từ trong từ điển.
- Tính chất: Vì $u$ luôn nhỏ hơn $v$, đồ thị này chắc chắn là một <b>Đồ thị có hướng không chu trình (DAG)</b>.

Ở phần trước chúng ta đã biết cách <b>tối ưu bắc cầu sử dụng bitset</b>. Ở bài toán này, thay vì chỉ dùng mảng `dp[u]` kiểu boolean để biết có đến được $u$ từ điểm bắt đầu hay không, ta sử dụng `bitset<N> reach[N]` để <b>lưu trữ tập hợp tất cả các đỉnh có thể đến được từ một đỉnh bất kỳ</b>.

<b>Công thức chuyển trạng thái</b>

Duyệt ngược từ $u = N$ về $0$ (Topo ngược) để tính trước các giá trị $v$:

$$reach[u][u] = 1 \text{ (Mọi đỉnh luôn tự đến được chính nó)}$$

$$reach[u] = reach[u] \cup \left( \bigcup_{u \to v} reach[v] \right)$$

$\Rightarrow \quad$ Qua đó, độ phức tạp ban đầu $O(N^2)$ giảm xuống còn $O(\frac{N ^ 2}{64})$.

### <b>2. Dấu hiệu nhận biết</b>

> Kỹ thuật này <b>không thay thế hoàn toàn Push DP</b> mà thường dùng khi:
{: .prompt-warning}

1. <b>Bài toán có tính chất bắc cầu</b>: Chỉ quan tâm đến việc có thể đi từ $A$ đến $B$ hay không.
2. <b>Đồ thị ẩn là DAG</b>: Các bước di chuyển luôn hướng về một phía như <b>tree edge</b> (Push DP một chiều) hoặc có các yếu tố <b>back edge</b> (chu trình: đi qua đi lại 1 ô...), ví dụ như đi tới các index lớn hơn trong mảng/xâu, nhảy trên trục tọa độ.
3. <b>Có nhiều truy vấn (Queries)</b>: Nếu đề bài hỏi $Q$ câu hỏi dạng "Đoạn $S[L \dots R]$ có hợp lệ không?", bitset cho phép trả lời trong $O(1)$ sau khi tiền xử lý: `reach[L][R]`.
4. <b>Giới hạn bộ nhớ & thời gian:</b> Khi $N \approx 10^4$, $O(N^2)$ là $10^8$ phép tính (dễ TLE), nhưng $O(\frac{N^2}{64})$ chỉ còn khoảng $1.5 \times 10^6$ phép tính (chạy cực mượt).

```c++
int main(void) {
    init(); // init string hashing
    int q; cin >> q;
    string s; cin >> s;
    set<ii> hashes;
    vector<int> lens;
    while(q--) {
        string t; cin >> t;
        int n = sz(t);
        t = ' ' + t;
        string_hashing hash_t(t, n);
        hashes.insert(hash_t(1, n));
        lens.eb(n);
    }
    sort(all(lens));
    lens.erase(unique(all(lens)), lens.end());
    int n = sz(s);
    s = ' ' + s;
    string_hashing hash_s(s, n);
    //DAG trick: chuyển hóa bài toán thành DAG với n đỉnh đại diện cho dp[len]: len tối đa ghép được bắt đầu tại u
    vector<int> adj[2005];
    FOR(u, 0, n + 1) {
        for(const int &len: lens) {
            int v = u + len;
            if(v > n) continue;
            if(hashes.count(hash_s(u + 1, v))) {
                adj[u].eb(v); // có cạnh từ u sang v
            }
        }
    }
    bitset<2005> reach[2005];
    int res = 0;
    for(int u = n; u >= 0; u--) { // duyệt ngược để tính trước v (topo ngược)
        reach[u][u] = 1; // u luôn đi được đến chính nó
        for(const int &v: adj[u]) {
            reach[u] |= reach[v]; // O(n/64)
        }
        // tìm điểm xa nhất có thể đến được từ u
        for(int v = n; v >= u; v--) {
            if(reach[u][v]) {
                maximize(res, v - u);
                break;
            }
        }
    }
    cout << res;
}
```
