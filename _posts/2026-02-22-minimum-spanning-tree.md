---
layout: post
title: Cây khung cực tiểu (MST)🌲 (🚧)
date: 2026-02-22 10:31 +0700
math: true
categories: [graph, tree]
tags: [cây khung cực tiểu, mst, kruskal, prim]
---

## <b>Thuật toán Kruskal</b>
- Dựa trên tư tưởng tham lam (Greedy) và Cấu trúc dữ liệu các tập hợp rời rạc (DSU - Disjoint Set Union). 
- Thuật toán sắp xếp các cạnh tăng dần theo trọng số và thêm vào cây nếu không tạo thành chu trình. 
- Độ phức tạp: $O(E \log E)$. Rất dễ cài đặt, thường được ưu tiên sử dụng.

```c++
int n, m;
const int limN = 1e5 + 5;
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
    if (n == 0) return 0; // case đặc biệt
    Kruskal();
}
```

## <b>Thuật toán Prim</b>

- Xuất phát từ một đỉnh, luôn chọn cạnh có trọng số nhỏ nhất nối một đỉnh đã thuộc cây với một đỉnh chưa thuộc cây (thường cài bằng Hàng đợi ưu tiên - Priority Queue). 
- Độ phức tạp: $O(E \log V)$. Thường dùng khi đồ thị dày (dense graph) với số cạnh $E \approx V^2$.

```c++
typedef pair<int, int> ii;
int n, m;
const int limN = 1e5 + 5;
int visited[limN];
vector<ii> adj[limN];
void prim(int s) {
    priority_queue<ii, vector<ii>, greater<ii>> Q;
    visited[s] = 1;
    for(const auto &[u, w]: adj[s]) {
        Q.push({w, u});
    }
    ll d = 0;
    int sz = 0;
    while(!Q.empty()) {
        auto[w, u] = Q.top(); Q.pop();
        if(!visited[u]) {
            visited[u] = 1;
            d += w;
            sz++;
            for(const auto &[v, w]: adj[u]) {
                if(!visited[v]) {
                    Q.push({w, v});
                }
            }
        }
    }
    // một cây có n đỉnh thì phải có n-1 cạnh
    if(sz == n - 1 || n == 1) {
        cout << total_weight << endl;
    } else {
        cout << "IMPOSSIBLE" << endl;
    }
}
int main(void) {
    cin >> n >> m;
    FOR(i, 1, m + 1){
        int u, v, w; cin >> u >> v >> w;
        adj[u].eb(v, w);
        adj[v].eb(u, w);
    }
    if (n == 0) return 0; // case đặc biệt
    prim(1);
}
```

## <b>Các bài toán kinh điển về MST</b>

### <b>Dạng 1: MST Cơ bản & Cây khung lớn nhất (Maximum Spanning Tree)</b>

Đây là dạng nền tảng, yêu cầu tìm chi phí nhỏ nhất để kết nối tất cả các đỉnh.

- Biến thể 1 - Đã có sẵn một số cạnh: Đề bài cho một số đỉnh đã được nối sẵn (chi phí 0). Ta chỉ cần khởi tạo DSU, Union các đỉnh này lại với nhau trước khi chạy Kruskal.

- Biến thể 2 - Cây khung lớn nhất: Yêu cầu nối các mạng lưới sao cho tổng trọng số là lớn nhất (ví dụ: bài toán giữ lại các tuyến cáp có băng thông lớn nhất, phá bỏ các tuyến cáp khác). Ta chỉ cần <b>sắp xếp các cạnh theo chiều giảm dần của trọng số</b> trước khi chạy Kruskal, hoặc <b>đổi dấu tất cả trọng số thành âm</b> và tìm MST.

### <b>Dạng 2: Đường đi Minimax / Maximin (Bottleneck Spanning Tree)</b>

<b>Bài toán:</b> Cho đồ thị, tìm đường đi từ đỉnh $u$ đến đỉnh $v$ sao cho <b>cạnh có trọng số lớn nhất trên đường đi đó là nhỏ nhất (Minimax) - hoặc ngược lại (Maximin)</b>.

- <b>Tính chất kinh điển:</b> Mọi đường đi trên Cây khung cực tiểu (MST) giữa $u$ và $v$ chính là đường đi có cạnh lớn nhất nhỏ nhất giữa $u$ và $v$ trên toàn bộ đồ thị.

- <b>Cách giải:</b> Dùng Kruskal để dựng MST. Sau đó dùng thuật toán tìm Tổ tiên chung gần nhất (LCA) kết hợp Nâng bậc nhị phân (Binary Lifting) để tìm max trọng số cạnh trên đường đi từ $u$ đến $v$ trong $O(\log V)$.

### <b>Dạng 3: Cây khung nhỏ thứ hai (Second Best Minimum Spanning Tree)</b>

<b>Bài toán:</b> Tìm cây khung có tổng trọng số nhỏ nhất nhưng phải <b>khác biệt ít nhất 1 cạnh</b> so với MST ban đầu.

- <b>Cách giải:</b>

1. Tìm MST ban đầu với tổng trọng số $W$.
2. Xét mọi cạnh $e = (u, v)$ có trọng số $w$ <b>không thuộc MST</b>. Nếu thêm $e$ vào MST, nó sẽ tạo ra một chu trình.
3. Để phá chu trình và tạo cây khung mới, ta phải bỏ đi một cạnh $e'$ nằm trên đường đi từ $u$ đến $v$ trong MST ban đầu. Để cây mới tối ưu nhất, cạnh $e'$ phải là cạnh có trọng số lớn nhất trên đường đi này (gọi là $w_{max}$).
4. Chi phí của cây mới khi thêm $e$ là: $W_{new} = W + w - w_{max}$.
5. Duyệt qua tất cả các cạnh không thuộc MST, tính $W_{new}$ và lấy giá trị nhỏ nhất. (Cần dùng LCA để tìm $w_{max}$ trong $O(\log V)$).

### <b>Dạng 4: Cây Kruskal (Kruskal Reconstruction Tree / Reachability Tree)</b>

<b>Bài toán:</b> Thường xuất hiện trong các bài truy vấn dạng: "Bắt đầu từ đỉnh $u$, chỉ được phép đi qua các cạnh có trọng số $\le X$. Hỏi có thể đi đến những đỉnh nào? (hoặc tìm đỉnh có giá trị lớn nhất có thể đến được)".

- <b>Kỹ thuật:</b> Thay vì dùng DSU thông thường, mỗi khi Union(u, v) thông qua một cạnh có trọng số $w$, ta <b>tạo ra một đỉnh mới $P$ đại diện cho cạnh đó</b>. Đỉnh $P$ sẽ có trọng số là $w$, và có 2 con là đại diện (root) hiện tại của tập hợp chứa $u$ và tập hợp chứa $v$.

- <b>Kết quả:</b> Ta thu được một cây (forest) gồm $2V - 1$ đỉnh. Đặc điểm là khi truy vấn từ $u$ với giới hạn $X$, ta chỉ cần nhảy lên tổ tiên cao nhất của $u$ có trọng số $\le X$ (dùng Binary Lifting). Toàn bộ cây con gốc đó chính là tập hợp các đỉnh có thể đến được!

### <b>Dạng 5: Bài toán dỡ đường (Reverse Kruskal / Offline Queries)</b>

<b>Bài toán:</b> Đồ thị ban đầu liên thông. Có các truy vấn xóa một cạnh khỏi đồ thị, và sau mỗi lần xóa yêu cầu tính tổng chi phí MST hoặc kiểm tra tính liên thông.

- <b>Cách giải:</b> DSU chỉ hỗ trợ thao tác `Union` (thêm cạnh) chứ không hỗ trợ xóa cạnh. Do đó, ta phải lưu toàn bộ truy vấn lại (Offline), làm từ dưới lên trên.

1. Giả sử tất cả các cạnh bị xóa đã biến mất. Dựng MST với các cạnh còn lại.
2. Đi ngược từ truy vấn cuối lên truy vấn đầu. Thao tác "xóa cạnh" bây giờ trở thành "thêm cạnh" vào MST bằng Kruskal.

### <b>Dạng 6: Cây khung trên đồ thị dày đặc (Dense Graph / Lưới tọa độ)</b>

<b>Bài toán:</b> Cho $N$ điểm trên mặt phẳng tọa độ ($N \le 10^5$). Cạnh nối giữa 2 điểm bất kỳ có chi phí là khoảng cách Manhattan $|x_1 - x_2| + |y_1 - y_2|$. Tìm MST.

<b>Cách giải:</b> Đồ thị đầy đủ có $O(N^2)$ cạnh, không thể dùng Kruskal thông thường. Phải dùng tính chất hình học để giảm số cạnh xuống còn $O(N)$ (chia mặt phẳng thành 8 góc phần tư và chỉ nối điểm gần nhất trong mỗi góc), sau đó mới chạy Kruskal.

## <b>Các motip kinh điển thường gặp</b>

1. <b>Bài toán Xây dựng trạm phát sóng / Sửa đường:</b> Có $N$ thành phố. Một số thành phố đã có đường nối. Đề bài cho danh sách các đường có thể xây với chi phí tương ứng. Hỏi chi phí nhỏ nhất để tất cả các thành phố liên thông (hoặc nối vào trung tâm). -> Kruskal cơ bản.

2. <b>Khu rừng (Forest):</b> Thay vì yêu cầu liên thông tất cả $N$ đỉnh, đề bài cho phép đồ thị có đúng $K$ thành phần liên thông (ví dụ: chia $N$ ngôi làng thành $K$ cụm). -> Chạy Kruskal và dừng lại khi số tập hợp (components) trong DSU giảm xuống đúng bằng $K$.

3. <b>Bài toán Nâng cấp mạng điện:</b> Cho đồ thị là một cây (hoặc MST có sẵn). Nếu một đường dây bị đứt (cạnh bị xóa), hỏi phải dùng cạnh thay thế nào (trong các cạnh dự phòng) để đồ thị vẫn liên thông với chi phí rẻ nhất. -> Ứng dụng của thuật toán Cây khung nhỏ thứ hai.

## <b>Các kỹ thuật tối ưu</b>

### <b>1. Kỹ thuật giảm thiểu số cạnh (Edge Reduction)</b>

Khi đồ thị là đồ thị đầy đủ (mọi đỉnh đều có thể nối với nhau) hoặc đồ thị dạng lưới khổng lồ, số lượng cạnh $E$ có thể lên tới $O(V^2)$. Chạy Kruskal trực tiếp sẽ bị quá thời gian (TLE) hoặc quá bộ nhớ (MLE). Bạn phải tìm cách loại bỏ những cạnh chắc chắn không thuộc MST.

1. <b>Tối ưu trên lưới (Grid MST):</b> Thay vì xét từng ô nhỏ, ta gom nhóm các đường cắt ngang/dọc có cùng bản chất lại và sắp xếp chúng. Giảm độ phức tạp từ $O(NM \log(NM))$ xuống $O(N \log N + M \log M)$.
2. <b>Cây khung khoảng cách Manhattan (Manhattan MST):</b> Cho $N$ điểm trên mặt phẳng. Trọng số cạnh là khoảng cách Manhattan $|x_1 - x_2| + |y_1 - y_2|$. Số cạnh tối đa là $O(N^2)$.
- <b>Kỹ thuật:</b> Từ mỗi điểm, ta chia mặt phẳng ra làm 8 góc phần tư (mỗi góc 45 độ). Trong mỗi góc phần tư, ta chỉ cần nối điểm đang xét với <b>điểm gần nhất</b>. Bằng cách này, số lượng cạnh giảm từ $O(N^2)$ xuống còn $O(N)$. Sau đó mới chạy Kruskal trong thời gian $O(N \log N)$.

### <b>2. Thuật toán Borůvka</b>

Nếu gặp các bài toán mà trọng số cạnh được tính bằng <b>một công thức toán học hoặc phép toán bit</b> (đặc biệt là XOR) thì thuật toán Borůvka sẽ hoạt động tốt.

<b>Cách hoạt động:</b> 

1. Bắt đầu với $N$ đỉnh (tương đương $N$ thành phần liên thông).
2. Với mỗi thành phần liên thông, tìm cạnh có trọng số nhỏ nhất nối nó với một thành phần khác.
3. Thêm tất cả các cạnh tìm được vào MST (và gộp các thành phần lại).
4. Lặp lại bước 2. Do mỗi bước số lượng thành phần liên thông giảm đi ít nhất một nửa, thuật toán chỉ mất tối đa $O(\log V)$ bước lặp.

<b>Dấu hiệu nhận biết (Bài toán kinh điển: XOR MST)</b>: Cho mảng $A$ gồm $N$ phần tử. Trọng số cạnh nối giữa $i$ và $j$ là $A[i] \oplus A[j]$ (phép XOR). Tìm MST.

<b>Cách giải:</b> Dùng Borůvka kết hợp với cấu trúc dữ liệu <b>Trie</b>. Ở mỗi bước, để tìm cạnh XOR nhỏ nhất từ một thành phần liên thông ra bên ngoài, ta đẩy các đỉnh vào Trie và truy vấn trong $O(\log(\max A))$.

### <b>3. Lựa chọn thuật toán theo mật độ đồ thị (Dense vs Sparse)</b>

1. <b>Đồ thị thưa (Sparse Graph - số cạnh $E$ xấp xỉ số đỉnh $V$)</b>: Dùng <b>Kruskal</b> với cấu trúc dữ liệu DSU. Đảm bảo bạn luôn cài đặt DSU chuẩn với 2 kỹ thuật: Nén đường (Path Compression) và Gộp theo kích thước/hạng (Union by Size/Rank). Độ phức tạp là $O(E \log E)$.

2. <b>Đồ thị cực đặc (Dense Graph - số cạnh $E \approx V^2$)</b>: Đừng cố dùng Kruskal vì sắp xếp $V^2$ cạnh sẽ mất $O(V^2 \log V)$. Hãy dùng Prim $O(V^2)$.

- <b>Cách cài</b>: Không dùng Hàng đợi ưu tiên (Priority Queue). Hãy dùng một mảng 1 chiều `min_weight[i]` lưu khoảng cách nhỏ nhất từ đỉnh $i$ đến cây khung hiện tại. Mỗi lần lặp, duyệt tuyến tính để tìm đỉnh có `min_weight` nhỏ nhất.

- Đây là cái bẫy khi lạm dụng Priority Queue khiến thuật toán Prim thành $O(V^2 \log V)$ và bị TLE.