---
layout: post
title: DP trên đoạn & DP phân hoạch🧠
date: 2026-03-01 16:54 +0700
math: true
---

## <b>1. Interval DP (DP trên đoạn / Range DP)</b>

Dạng này dùng khi bạn cần giải quyết bài toán trên một mảng bằng cách **gộp (merge) đáp án từ các đoạn con ngắn hơn** lại thành đoạn dài hơn.

**Dấu hiệu nhận biết:**

- **Đề bài yêu cầu thao tác trên một mảng:** gộp 2 phần tử kề nhau, xóa các phần tử đối xứng (Palindrome), hoặc các trò chơi hai người (Game Theory) bốc phần tử từ 2 đầu.
- Cập nhật từ đoạn nhỏ lên đoạn lớn.
- Giới hạn mảng thường nhỏ: $N \le 500$ (nếu độ phức tạp $O(N^3)$) hoặc $N \le 5000$ (nếu độ phức tạp $O(N^2)$).

**Mô hình Trạng thái (State):**

> `dp[i][j]:` Kết quả tối ưu/số cách/... cho đoạn con từ vị trí $i$ đến vị trí $j$.
{: .prompt-info}

**Công thức chuyển trạng thái (Transition):**

Có 2 kiểu chuyển trạng thái phổ biến nhất:

> 1. Kiểu tách điểm k (Chia để trị trên đoạn): <br>$$dp[i][j] = \min_{i \le k < j} (dp[i][k] + dp[k+1][j] + \text{cost}(i, j))$$
{: .prompt-tip}

> 2. Kiểu Bao hàm - Loại trừ: <br>$$dp[i][j] = dp[i+1][j] + dp[i][j-1] - dp[i+1][j-1] + \text{Unique}(i, j)$$
{: .prompt-tip}

```c++
// khởi tạo dp cho các đoạn độ dài 1 (hoặc 2)
FOR(i, 1, n + 1) {
    dp[i][i] = ...
}
// duyệt độ dài đoạn từ nhỏ đến lớn
FOR(len, 2, n + 1) {
    // duyệt điểm bắt đầu i
    FOR(i, 1, n - len + 1 + 1) {
        int j = i + len - 1; // điểm kết thúc j
        // cập nhật dp[i][j] dựa trên bài toán
        // ví dụ: Duyệt điểm cắt k
        dp[i][j] = LLONG_MAX;
        FOR(k, i, j) {
            dp[i][j] = min(dp[i][j], dp[i][k] + dp[k + 1][j] + cost(i, j));
        }
    }
}
```

## <b>2. Partition DP (DP Phân hoạch / Chia K đoạn)</b>

Dạng này dùng khi đề bài yêu cầu **cắt một mảng độ dài N thành chính xác (hoặc tối đa) K đoạn liên tiếp** sao cho tổng chi phí của các đoạn là lớn nhất/nhỏ nhất.

**Dấu hiệu nhận biết:**

- Từ khóa: "Chia mảng thành $K$ phần liên tiếp", "Đặt $K$ vách ngăn".
- Giới hạn thường là: $N \le 10^5$ và $K \le 100$ (Cần dùng tối ưu) hoặc $N \le 2000, K \le 2000$ (Code $O(K \cdot N^2)$ thuần).

**Mô hình Trạng thái (State):**

> `dp[k][i]`: Chi phí tối ưu khi chia $i$ phần tử đầu tiên thành $k$ đoạn.
{: .prompt-info}

**Công thức chuyển trạng thái (Transition):**

Ta xét đoạn cuối cùng kết thúc tại $i$, giả sử đoạn cuối cùng đó bắt đầu từ $j+1$ đến $i$. Nghĩa là trước đó, ta đã chia $j$ phần tử thành $k-1$ đoạn.

> $$dp[k][i] = \min_{0 \le j < i} (dp[k-1][j] + \text{cost}(j+1, i))$$
{: .prompt-tip}

## <b>3. Khi nào thì "đưa đáp án về n+1"?</b>

1. **Ràng buộc về khoảng cách (Gap constraint):** "Không được quá $k$ bước không làm gì" hoặc "Không được chọn quá $k$ cái liên tiếp".
2. **Bài toán bước nhảy (Jumping):** Ta đứng ở vị trí $i$ có thể nhảy đến bất kỳ vị trí nào trong đoạn $[i+1, i+k]$. "Về đích" nghĩa là ta nhảy đến bất kỳ vị trí nào $\ge n$. Lúc này, hãy coi $n+1$ là cái đích duy nhất.
3. **Hành động cuối cùng không bắt buộc tại $n$:** Khi phần tử cuối cùng của mảng không nhất thiết phải tham gia vào cấu trúc đáp án tối ưu.