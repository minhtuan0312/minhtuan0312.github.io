---
layout: post
title: BFS trên phần dư (BFS Remainder)🕵️
date: 2026-02-20 19:18 +0700
math: true
---

<div class="problem-link">
  🔗 <strong>Bài toán kinh điển:</strong>
  <a href="https://oj.vnoi.info/problem/area#submit" target="_blank">
    ONEZERO - Ones and zeros
  </a>
</div>

### <b>I. Tóm tắt đề bài</b>  

Cho một số nguyên dương $n$. Hãy tìm số nguyên dương $X$ nhỏ nhất thỏa mãn đồng thời hai điều kiện:

- $X$ là bội số của $n$ (nghĩa là $X$ chia hết cho $n$).
- Trong hệ thập phân, $X$ chỉ gồm các chữ số 0 và 1 (và phải bắt đầu bằng chữ số 1).

<b>Input:</b>

- Dòng đầu tiên là số lượng bộ test $K$ ($K \approx 1000$).
- $K$ dòng tiếp theo, mỗi dòng chứa một số nguyên $n$ ($1 \le n \le 20000$).

<b>Output:</b>

- Với mỗi số $n$, in ra trên một dòng số $X$ nhỏ nhất tương ứng tìm được.

<b>Ví dụ:</b>

- Nếu $n = 3$, số nhỏ nhất gồm 0 và 1 chia hết cho 3 là 111.
- Nếu $n = 7$, số nhỏ nhất là 1001.
- Nếu $n = 17$, số nhỏ nhất là 11101.

### <b>II. Tại sao lại dùng BFS?</b>

Bài toán yêu cầu tìm <b>số nhỏ nhất</b> thỏa mãn điều kiện.

Thuật toán BFS duyệt các trạng thái theo từng "lớp" (từ ngắn đến dài). Do đó, khi ta sinh ra các số chỉ gồm 0 và 1 theo thứ tự: $\quad 1 \rightarrow \quad 10, 11 \quad \rightarrow \quad 100, 101, 110, 111 \quad \rightarrow \quad$ ...

<b>Số đầu tiên trong quá trình sinh này chia hết cho $n$ chắc chắn sẽ là số có giá trị nhỏ nhất.</b>

### <b>III. Vấn đề</b>

Các số sinh ra chỉ gồm '0' và '1' có thể dài đến hàng trăm chữ số, vượt qua giới hạn của mọi kiểu dữ liệu số nguyên thông thường nên ta bắt buộc phải <b>dùng string</b>.

Tuy nhiên, nếu chỉ đơn thuần sinh ra các string chỉ chứa kí tự $1, 0$ (sử dụng Queue hoặc quay lui) thì độ phức tạp có thể lên đến $O(2^k)$ với k là độ dài xâu. Với $n = 20000$, số chữ số có thể lên tới hàng chục, hàng trăm, khiến $2^{100}$ là một con số không thể tính toán nổi.

### <b>IV. Tính chất của đồng dư thức</b>

Toán học đồng dư có một tính chất cực kỳ lợi hại:

- Giả sử ta có một số $X$, và ta biết số dư của $X$ khi chia cho $n$ là $r$.
- Khi ta thêm chữ số $d$ vào sau số $X$, số mới tạo thành sẽ là $X \cdot 10 + d$.
- Số dư của số mới này khi chia cho $n$ hoàn toàn có thể được tính dựa trên $r$:

$$X \cdot 10 + d \equiv r \cdot 10 + d \pmod n$$

### <b>V. Tối ưu sử dụng mảng đánh dấu (pruning)</b>

- Vì ta chia lấy dư cho $n$, nên chỉ có tối đa $n$ trạng thái số dư (từ 0 đến $n-1$).
- Giả sử trong quá trình BFS, ta tạo ra hai chuỗi "101" và "1100" và chúng có cùng một số dư $r$ khi chia cho $n$. Ta chỉ cần <b>giữ lại chuỗi sinh ra trước (chuỗi ngắn hơn)</b>, và bỏ qua chuỗi sinh sau. Vì nếu cả hai cùng đi tiếp một chặng đường giống hệt nhau để đến số dư 0, thì chuỗi ngắn hơn chắc chắn sẽ tạo ra kết quả nhỏ hơn.
- Bằng việc sử dụng visited[$r$], ta giới hạn số lượng trạng thái trong Queue tối đa là $n$. 

$\Rightarrow$ Độ phức tạp thời gian giảm xuống chỉ còn $O(n)$ thay vì $O(2^k)$.

### <b>VI. Mã giải</b>

```c++
void bfs() {
    int n; cin >> n;
    if(1 % n == 0) return cout << 1 << nl, void(); // trường hợp cơ sở nhất
    queue<pair<int, string>> qu; // queue lưu trữ pair<số dư, chuỗi biểu diễn>
    bool visited[n + 1] = {0};
    qu.push({1 % n, "1"});
    visited[1 % n] = 1;
    while(!qu.empty()) {
        auto [r, s] = qu.front(); qu.pop();
        if(r == 0) {
            return cout << s << nl, void();
        }
        int r0 = (r * 10 + 0) % n; // thêm '0'
        if(!visited[r0]) {
            visited[r0] = 1;
            qu.push({r0, s + "0"});
        }
        int r1 = (r * 10 + 1) % n; // thêm '1'
        if(!visited[r1]) {
            visited[r1] = 1;
            qu.push({r1, s + "1"});
        }
    }
}
```