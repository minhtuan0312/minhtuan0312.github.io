---
layout: post
title: Tối ưu Quy hoạch động bằng nhân ma trận🔥
date: 2026-02-17 19:58 +0700
math: true
categories: [quy hoạch động]
---

## I. Tổng quan

Trong lập trình thi đấu, đôi khi ta gặp các bài toán tìm số hạng thứ $N$ của dãy số hoặc đếm số cách thực hiện một công việc với $N$ bước.

- Nếu $N \le 10^6$: Dùng dp thông thường với độ phức tạp $O(N)$.
- Nếu $N \le 10^{18}$: Cách $O(N)$ sẽ bị TLE. Ta cần phương pháp $O(K^3 \log N)$ bằng cách sử dụng nhân ma trận (với $K$ là kích thước ma trận).

## II. Các bước thực hiện

### Bước 1: Tìm công thức truy hồi (Subtask nhỏ)
Trước khi nghĩ đến ma trận, phải giải được bài toán bằng dp thường (với $N$ nhỏ). Mục tiêu là tìm ra mối quan hệ tuyến tính giữa trạng thái hiện tại và các trạng thái trước đó.

Công thức thường có dạng:

$$f_i = c_1 \cdot f_{i-1} + c_2 \cdot f_{i-2} + \dots + c_k \cdot f_{i-k}$$

Lưu ý: Kỹ thuật này chỉ áp dụng hiệu quả khi số lượng trạng thái phụ thuộc $k$ là nhỏ (thường $k \le 100$).

### Bước 2: Xây dựng Ma trận chuyển trạng thái (Transition Matrix)

Ta cần tìm một ma trận $T$ (<b>ma trận chuyển vị</b>) và một vector cột $V$ (<b>ma trận trạng thái</b>) sao cho:

$$V_i = T \times V_{i-1}$$

Từ đó suy ra công thức tổng quát:

$$V_n = T^{n-k} \times V_k$$

Cách xây dựng:

1. Xác định Vector trạng thái ($V_{i-1}$): Liệt kê các giá trị cần thiết để tính ra trạng thái tiếp theo.
2. Xác định Vector kết quả ($V_i$): Là vector $V$ ở bước tiếp theo.
3. Điền ma trận $T$: Dựa vào công thức truy hồi để điền các hệ số vào ma trận $T$.

### Bước 3: Lũy thừa ma trận (Binary Exponentiation)

Sử dụng thuật toán lũy thừa nhị phân (tương tự như tính $a^b$) nhưng áp dụng cho phép nhân ma trận để tính $T^{n-k}$ với độ phức tạp $O(K^3 \log N)$.

## III. Ví dụ kinh điển: Dãy Fibonacci

<b>Bài toán:</b> Tính số Fibonacci thứ $N$ ($N \le 10^{18}$), chia dư cho $10^9 + 7$.

Công thức: $f_i = f_{i-1} + f_{i-2}$.

### Phân tích theo các bước:

#### <b>1. Vector trạng thái ($V_{i-1}$):</b>

Để tính $f_i$, ta cần $f_{i-1}$ và $f_{i-2}$. 

Vậy vector cột sẽ là:

$$V_{i-1} = \begin{bmatrix} f_{i-1}\\ f_{i-2} \end{bmatrix}$$

#### <b>2. Vector mục tiêu ($V_i$):</b>

Khi tăng lên một bước, ta cần có:

$$V_{i} = \begin{bmatrix} f_i \\ f_{i-1} \end{bmatrix}$$

#### <b>3. Tìm ma trận chuyển vị $T$:</b>

Ta cần tìm ma trận $2 \times 2$ sao cho:

$$\begin{bmatrix} f_i \\ f_{i-1} \end{bmatrix} = \begin{bmatrix} ? & ? \\ ? & ? \end{bmatrix} \times \begin{bmatrix} f_{i-1} \\ f_{i-2} \end{bmatrix}$$

<b>Dựa vào công thức truy hồi:</b>

- Hàng 1: $f_i = 1 \cdot f_{i-1} + 1 \cdot f_{i-2}$ $\Rightarrow$ Hệ số là 1 và 1.
- Hàng 2: $f_{i-1} = 1 \cdot f_{i-1} + 0 \cdot f_{i-2}$ $\Rightarrow$ Hệ số là 1 và 0.

Vậy ma trận chuyển vị $T$ là:

$$T = \begin{bmatrix} 1 & 1 \\ 1 & 0 \end{bmatrix}$$

#### <b>Công thức cuối cùng:</b>

$$\begin{bmatrix} f_n \\ f_{n-1} \end{bmatrix} = \begin{bmatrix} 1 & 1 \\ 1 & 0 \end{bmatrix}^{n-1} \times \begin{bmatrix} f_1 \\ f_0 \end{bmatrix}$$

(Giả sử $f_1=1, f_0=0$).

#### <b>Mã giải (c++)</b>

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
    matrix operator *(const matrix& other) {
        matrix res(n);
        FOR(i, 0, n) {
            FOR(k, 0, n) {
                if (!f[i][k]) continue;
                FOR(j, 0, n) {
                    res.f[i][j] += f[i][k] * other.f[k][j] % mod;
                    res.f[i][j] %= mod;
                }
            }
        }
        return res;
    }
    matrix operator ^(ll b) {
        matrix res = identity();
        matrix a = (*this);
        while (b) {
            if (b & 1) res = res * a;
            a = a * a;
            b >>= 1;
        }
        return res;
    }
};

int main(void) {
    ll n; cin >> n;
    matrix base(2);
    base.f = { {1, 0}, {0, 0} };
    matrix transit(2);
    transit.f = { {1, 1}, {1, 0} };
    matrix res = (transit ^ (n - 1)) * base;
    cout << res.f[0][0];
}
```

## IV. Các mô hình nâng cao

### Dạng 1: Có hằng số cộng thêm

$$f_i = f_{i-1} + f_{i-2} + C$$

Ta thêm con số $1$ vào vector trạng thái để giữ giá trị hằng số $C$.

- Vector: $[f_i, f_{i-1}, 1]^T$
- Ma trận chuyển:

$$\begin{bmatrix} f_i \\ f_{i-1} \\ 1 \end{bmatrix} = \begin{bmatrix} 1 & 1 & C \\ 1 & 0 & 0 \\ 0 & 0 & 1 \end{bmatrix} \times \begin{bmatrix} f_{i-1} \\ f_{i-2} \\ 1 \end{bmatrix}$$

### Dạng 2: Tính tổng dồn (Prefix Sum)

Tính $S_n = \sum_{j=1}^n f_j$.

Ta có $S_i = S_{i-1} + f_i$. Ta thêm $S_i$ vào vector trạng thái.

- Vector: $[S_i, f_i, f_{i-1}]^T$
- Ma trận chuyển (kết hợp logic của $f_i$):

$$\begin{bmatrix} S_i \\ f_i \\ f_{i-1} \end{bmatrix} = \begin{bmatrix} 1 & 1 & 1 \\ 0 & 1 & 1 \\ 0 & 1 & 0 \end{bmatrix} \times \begin{bmatrix} S_{i-1} \\ f_{i-1} \\ f_{i-2} \end{bmatrix}$$

(Giải thích hàng 1: $S_i = S_{i-1} + f_i = S_{i-1} + (f_{i-1} + f_{i-2})$).