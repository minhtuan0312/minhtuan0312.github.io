---
layout: post
title: Kĩ thuật hai con trỏ 🐭 & Sliding Window 🪟
date: 2026-02-25 20:21 +0700
math: true
categories: [general]
tags: [two pointers, sliding window]
---

## <b>Dạng 1: Hai con trỏ trên hai mảng</b>

Dạng này dùng để trộn, so sánh hoặc ghép cặp các phần tử giữa hai mảng khác nhau (thường cả 2 mảng đã được sắp xếp).

> Con trỏ $i$ chạy trên mảng $A$, con trỏ $j$ chạy trên mảng $B$. Tùy thuộc vào điều kiện so sánh giữa $A[i]$ và $B[j]$ mà quyết định con trỏ nào được tăng lên.
{: .prompt-tip}

```c++
int i = 1, j = 1;
// lặp khi cả hai con trỏ còn nằm trong mảng
while (i <= n && j <= m) {
    if (A[i] < B[j]) {
        // làm gì đó với A[i]
        i++;
    } else if (A[i] > B[j]) {
        // làm gì đó với B[j]
        j++;
    } else {
        // xử lý khi A[i] == B[j] (Ví dụ: đếm phần tử chung)
        i++;
        j++;
    }
}
// Note: đôi khi cần dùng thêm vòng lặp while phụ để quét nốt các phần tử còn dư 
// của mảng A hoặc mảng B (nếu bài toán yêu cầu gộp mảng).
```

## <b>Dạng 2: Hai con trỏ cùng chiều (Sliding Window)</b>

Dạng này áp dụng trên mảng (không bắt buộc phải sắp xếp) khi ta cần tìm một <b>đoạn con liên tiếp (subarray)</b> thỏa mãn một điều kiện nhất định. Cả $L$ và $R$ đều xuất phát từ 1.

> Con trỏ $R$ luôn tiến về phía trước để mở rộng "cửa sổ" và thu thập dữ liệu. Khi cửa sổ vi phạm điều kiện, con trỏ $L$ bắt đầu tiến lên để "nhả" bớt dữ liệu cho đến khi cửa sổ hợp lệ trở lại.
{: .prompt-tip}

```c++
int l = 1, ans = 0; // ans lưu kết quả tốt nhất
ll cur = 0; // Trạng thái của cửa sổ
for (int r = 1; r <= n; r++) {
    cur += a[r]; // bước 1: R mở rộng, nạp a[r] vào trạng thái
    // bước 2: L thu hẹp nếu trạng thái bị vi phạm (VD: tổng vượt quá S)
    while (l <= r && cur > S) { 
        cur -= a[l];
        l++; 
    }
    // bước 3: Cập nhật kết quả với cửa sổ [l, r] đang hợp lệ
    ans = max(ans, r - l + 1); 
}
```

## <b>Dạng 3: Hai con trỏ ngược chiều</b>

Dạng này thường dùng khi <b>mảng đã được sắp xếp</b>. Một con trỏ bắt đầu từ đầu mảng (`l = 1`), con trỏ kia bắt đầu từ cuối mảng (`r = n`). Chúng sẽ chạy ngược chiều nhau và gặp nhau ở giữa.

> Nếu tổng/giá trị hiện tại quá nhỏ, tăng con trỏ trái để lấy giá trị lớn hơn. Nếu quá lớn, giảm con trỏ phải.
{: .prompt-tip}

```c++
int l = 1, r = n;
while (l < r) { // chú ý: thường là < chứ không phải <= để tránh trùng phần tử
    int sum = a[l] + a[r];
    if (sum == target) {
        // xử lý khi thỏa mãn (in kết quả, đếm số lượng...)
        // nếu chỉ cần 1 cặ p, break tại đây.
        // nếu cần tìm nhiều cặp, nhớ l++; r--;
        break; 
    } else if (sum < target) {
        l++; // cần tăng giá trị
    } else {
        r--; // cần giảm giá trị
    }
}
```

## <b>Dạng 4: Hai con trỏ kết hợp Tham lam</b>

Dạng này thường xuất hiện khi ta cần phủ một không gian (thời gian, trục số, số lượng công việc) bằng cách chọn ra một tập hợp con tối ưu từ các phần tử cho trước.

> Thay vì dùng hai con trỏ để tạo ra một "khung cửa sổ" cố định, ta dùng:
- Con trỏ $i$: Để "quét" qua danh sách các lựa chọn (đã được sắp xếp).
- Biến biên `current_limit`: Xác định phạm vi mà các lựa chọn hiện tại phải tuân thủ.
- Biến biên `next_limit`: Lưu lại lựa chọn "tốt nhất/xa nhất" mà ta có thể đạt được trong bước tiếp theo.
{: .prompt-tip}

```c++
// 1. luôn bắt đầu bằng việc Sắp xếp (thường theo mốc bắt đầu hoặc vị trí)
sort(A + 1, A + 1 + n, cmp);

int i = 1;
int count = 0;
ll current_limit = START_POS; // điểm bắt đầu cần xử lý

while (current_limit < TARGET_POS) {
    long long best_reach = -1; 
    bool found = false;

    // con trỏ i đóng vai trò "quét" tất cả các ứng viên hợp lệ thỏa mãn điều kiện nằm trong current_limit
    while (i <= n && A[i].start <= current_limit) {
        // tham lam: Trong các ứng viên hợp lệ, chọn cái "tốt nhất" 
        // (thường là cái có thể vươn xa nhất)
        if (A[i].end > best_reach) {
            best_reach = A[i].end;
            // lưu id hoặc thực hiện logic bổ trợ tại đây
        }
        i++; // i chỉ tăng, không bao giờ quay lại -> O(N)
        found = true;
    }

    // kiểm tra xem có tiến triển được không (tránh lặp vô hạn)
    if (!found || best_reach <= current_limit) {
        // không thể đi tiếp được nữa -> thất bại
        return FAILURE; 
    }

    // thực hiện "bước nhảy" tham lam
    current_limit = best_reach;
    count++; // tăng số bước/số phần tử đã chọn
}
```

## <b>Dạng 5: Hai con trỏ Nhanh - Chậm (Thuật toán Rùa và Thỏ / Floyd's Cycle Finding)</b>

Hai con trỏ xuất phát cùng một điểm nhưng di chuyển với tốc độ khác nhau (thường là chậm đi 1 bước, nhanh đi 2 bước).

> Nếu trong cấu trúc dữ liệu (như mảng chứa chỉ số trỏ lung tung, hoặc danh sách liên kết) có một "vòng lặp" (cycle), con trỏ Nhanh chắc chắn sẽ bắt kịp và chạy vòng quanh để đụng mặt con trỏ Chậm.
{: .prompt-tip}

```c++
// ví dụ: Tìm phần tử trùng lặp trong mảng n+1 phần tử chứa các số từ 1..n
// khởi tạo (giả sử mảng a trỏ các index cho nhau)
int slow = a[1];
int fast = a[a[1]];

// bước 1: Tìm điểm gặp nhau trong chu trình
while (slow != fast) {
    slow = a[slow];       // Rùa đi 1 bước
    fast = a[a[fast]];    // Thỏ đi 2 bước
}

// bước 2: Tìm điểm bắt đầu của chu trình
slow = 1; // đưa rùa về vạch xuất phát
while (slow != fast) {
    slow = a[slow]; // lúc này cả hai đi cùng tốc độ 1 bước
    fast = a[fast];
}
// lặp xong thì 'slow' (hoặc 'fast') chính là phần tử gây ra chu trình
```

## <b>Dạng 6: Hai con trỏ Đọc - Ghi (In-place Partitioning / Lọc mảng tại chỗ)</b>

Đây cũng là một biến thể của "nhanh - chậm", nhưng dùng trên mảng thẳng. Một con trỏ `i` (nhanh/đọc) quét qua mọi phần tử, con trỏ `j` (chậm/ghi) đi sau để lưu lại những phần tử thỏa mãn điều kiện, đè lên các phần tử rác.

> Ta muốn xóa phần tử thừa hoặc gộp nhóm mảng mà không được dùng thêm mảng mảng phụ (O(1) memory). Con trỏ j sẽ đánh dấu "chiều dài của mảng hợp lệ mới".
{: .prompt-tip}

```c++
// ví dụ: Xóa các phần tử trùng lặp trong mảng ĐÃ SẮP XẾP
int j = 1; // vị trí ghi mảng mới, ban đầu giữ lại phần tử đầu tiên
for (int i = 2; i <= n; i++) { // i là con trỏ đọc, chạy từ phần tử thứ 2
    if (a[i] != a[j]) { // nếu gặp phần tử mới khác với phần tử vừa lưu
        j++;            // tăng vị trí ghi lên
        a[j] = a[i];    // ghi đè phần tử mới vào
    }
}
// kết thúc: 'j' chính là số lượng phần tử của mảng sau khi lọc
```

## <b> Kĩ thuật SWAG </b>

<b>SWAG (Sliding Window Aggregation)</b> là một kỹ thuật cực kỳ thông minh trong cấu trúc dữ liệu, giúp chúng ta tính toán các giá trị tổng hợp (như min, max, gcd, sum) trên một cửa sổ trượt (sliding window) với độ phức tạp thời gian $O(1)$ cho mỗi thao tác.

Thông thường, nếu dùng cửa sổ trượt để tính sum, ta chỉ cần cộng phần tử mới và trừ phần tử cũ. Nhưng với GCD, Min, Max, XOR, Nhân ma trận, ghép chuỗi... ta không thể trừ một phần tử đi được. Đây là dấu hiệu để ta dùng SWAG.

<b>Nguyên lý hoạt động</b>

SWAG biến một hàng đợi (Queue) thành hai ngăn xếp (Stack): <b>Front Stack</b> (để lấy ra) và <b>Back Stack</b> (để thêm vào).

<b>Cơ chế duy trì:</b>

Mỗi phần tử trong Stack không chỉ lưu giá trị của chính nó, mà còn lưu <b>giá trị tổng hợp (aggregation)</b> của tất cả các phần tử nằm dưới nó trong Stack đó.
1. <b>Back Stack (Vào):</b> Khi đẩy một phần tử $x$ vào, giá trị tổng hợp mới sẽ là $f(aggregate\_current, x)$.
2. <b>Front Stack (Ra):</b> Khi cần lấy phần tử ra (pop), nếu Front Stack trống, ta đổ toàn bộ Back Stack sang Front Stack và tính toán lại các giá trị tổng hợp trên đường đi.
3. <b>Kết quả cuối cùng:</b> Kết quả của cửa sổ là sự kết hợp của `aggregate` từ Front Stack và aggregate từ Back Stack: $f(Front\_agg, Back\_agg)$.

```c++
template<class T>
struct SWAG{
    struct Node {
        T val, agg;
    };
    vector<Node> s_front, s_back;

    T op(T a, T b) {
        return __gcd(a, b);
        // return min(a, b);
        // return a + b;
    }
    void push(T x) {
        if(s_back.empty()) s_back.pb({x, x});
        else s_back.pb({x, op(s_back.back().agg, x)}); // op(tổng cũ, phần tử mới)
    }
    void pop() {
        if(s_front.empty()) {
            while(!s_back.empty()) {
                T x = s_back.back().val; s_back.pop_back();
                if(s_front.empty()) s_front.pb({x, x});
                else s_front.pb({x, op(x, s_front.back().agg)}); // op(phần tử mới, tổng cũ)
            }
        }
        if (!s_front.empty()) s_front.pop_back();
    }
    T get_all() {
        if(s_front.empty() && s_back.empty()) return 0;
        if(s_front.empty()) return s_back.back().agg;
        if(s_back.empty()) return s_front.back().agg;
        return op(s_front.back().agg, s_back.back().agg); // op(tổng front, tổng back)
    }
    int get_size() {
        return sz(s_back) + sz(s_front);
    }
};
```