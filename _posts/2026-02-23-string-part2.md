---
layout: post
title: Các thuật toán về xâu kí tự🔐 (Phần 2) (🚧)
date: 2026-02-23 21:11 +0700
math: true
categories: [data structure, string, template]
tags: [hash]
---

> <b> Đây là phần 2 của `Các thuật toán về xâu kí tự`, xem phần 1 ở đây</b>: [Các thuật toán về xâu kí tự🔐 (Phần 1)](/posts/string-part1/)
{: .prompt-info}

## <b>A. Trie + Trie XOR + Aho–Corasick</b>

### <b>I, Trie</b>

#### <b>1. Khái niệm</b>

Trie là một cấu trúc dữ liệu dạng cây, được thiết kế đặc biệt để lưu trữ và tìm kiếm các xâu ký tự (strings). Điểm đặc biệt của Trie là <b>các xâu có chung tiền tố sẽ chia sẻ chung một đường đi từ gốc xuống</b>.

<b>Tại sao lại cần Trie? (Độ phức tạp)</b>

- Thay vì tìm kiếm một xâu trong mảng $N$ phần tử mất $\mathcal{O}(N \times len)$ (hoặc $\mathcal{O}(len \log N)$ nếu dùng chặt nhị phân), Trie cho phép chúng ta thêm/xóa/tìm kiếm một xâu chỉ trong độ phức tạp $\mathcal{O}(len)$, với $len$ là độ dài của xâu đó. Khối lượng dữ liệu $N$ hoàn toàn không ảnh hưởng đến tốc độ tìm kiếm.

- Biến `cntPass` đếm số xâu đi qua nút $u$, và `cntEnd` đếm số xâu kết thúc tại $u$. Đây là hai trạng thái cực mạnh cho các bài toán đếm tiền tố.

```c++
struct trie {
    static const int limNode = 1e4 * 55; // tùy vào N và số kí tự
    int nxt[limNode][26]; // node tiếp theo từ node u bằng ký tự c
    int cntEnd[limNode]; // có bao nhiêu string kết thúc tại node u
    int cntPass[limNode]; // có bao nhiêu string đi qua u
    int cnt; // số node hiện tại trong trie
    trie() {
        cnt = 1; // 1-index
        memset(nxt, 0, sizeof nxt);
        memset(cntEnd, 0, sizeof cntEnd);
        memset(cntPass, 0, sizeof cntPass);
    }
    void upd(const string &s) { // cập nhật xâu s vào cây trie
        int u = 1;
        cntPass[u]++;
        for(const char &c: s) {
            int k = c - 'a';
            if(!nxt[u][k]) nxt[u][k] = ++cnt; // nếu chưa có tiền tố c thì cập nhật
            u = nxt[u][k];
            cntPass[u]++;
        }
        cntEnd[u]++;
    }
    bool del(const string &s) { // xóa xâu s ra khỏi cây trie
        // *lưu ý: ktra xâu s tồn tại trong cây trie chưa cái đã, nếu ko thì bị sai do âm
        if(!cntStr(s)) return 0;
        // còn lại là hàm ngược của hàm upd thôi nên có thể copy paste cho lẹ
        int u = 1;
        cntPass[u]--;
        for(const char &c: s) {
            int k = c - 'a'; 
            u = nxt[u][k];
            cntPass[u]--;
        }
        cntEnd[u]--;
        return 1;
    }
    //mấy hàm dưới đây copy paste cho lẹ :v
    int cntStr(const string &s) { // đếm số lượng xâu s trong cây trie
        int u = 1;
        for(const char &c: s) {
            int k = c - 'a';
            if(!nxt[u][k]) return 0;
            u = nxt[u][k];
        }
        return cntEnd[u];
    }
    int cntPre(const string &s) { // đếm số lượng tiền tố s trong cây trie
        int u = 1;
        for(const char &c: s) {
            int k = c - 'a';
            if(!nxt[u][k]) return 0;
            u = nxt[u][k];
        }
        return cntPass[u];
    }
};
```

> <b>Để kiểm tra 1 xâu có tồn tại hay không, ta có thể sử dụng hàm `cntStr`.</b>
{: .prompt-info}

> <b>Luôn khai báo Trie ở ngoài hàm main (biến toàn cục) hoặc dùng từ khóa static.</b>
{: .prompt-info}

#### <b>2. Các bài toán kinh điển (Standard Trie)</b>

##### <b>a, Bài toán 1: Tìm kiếm tiền tố (Dạng cơ bản nhất)</b>

<b>Mô tả:</b> Cho một từ điển $N$ từ. Trả lời $Q$ truy vấn, mỗi truy vấn cho một xâu $P$, hỏi có bao nhiêu từ trong từ điển nhận $P$ làm tiền tố?

<b>Tại sao phải dùng Trie?</b> Nếu dùng Bảng băm, ta chỉ có thể tìm kiếm xâu khớp hoàn toàn. Nếu duyệt trâu thì mất $\mathcal{O}(N \times Q)$. Với Trie, ta chỉ cần gọi hàm CountPrefix(P) trong template là xong trong $\mathcal{O}(\|P\|)$.

##### <b>b, Bài toán 2: Điện thoại di động (Auto-complete)</b>

<b>Mô tả:</b> Khi bạn gõ phím, hệ thống cần gợi ý từ dài hơn dựa trên những gì bạn đã gõ. Bài toán yêu cầu tìm từ có thứ tự từ điển nhỏ nhất có tiền tố là $S$.

<b>Giải pháp:</b> Đi theo xâu $S$ xuống một nút $u$ trên Trie. Từ nút $u$, cứ ưu tiên đi xuống nhánh có ký tự nhỏ nhất (`a` đến `z`) mà `cntPass > 0` cho đến khi gặp `cntEnd > 0`.

##### <b>c. Bài toán 3: Quy hoạch động trên Trie (Word Break)</b>

<b>Mô tả:</b> Cho một xâu rất dài $S$ và một bộ từ điển. Hỏi có bao nhiêu cách chia $S$ thành các xâu con sao cho mỗi xâu con đều nằm trong từ điển?

<b>Giải pháp:</b> Gọi $dp[i]$ là số cách chia xâu từ vị trí $i$ đến cuối. Để tính $dp[i]$, thay vì dùng hàm `substr` và `string hashing` (rất chậm), ta có thể duyệt từ vị trí $i$ trên xâu $S$, đồng thời đi xuống trên Trie. Nếu tại một nút có `cntEnd > 0`, ta cộng $dp[i+len]$ vào $dp[i]$. Trie giúp quá trình chuyển trạng thái DP giảm xuống chỉ còn $\mathcal{O}(L_{max})$.

> <b>Cách tư duy push dp (forward dp) cực hay chung cho dạng DP + Cây: <i>Ngầm định đồ thị DAG</i>.</b>
{: .prompt-tip}

Tại mỗi vị trí bắt đầu $i$, bạn đang xây dựng một <b>Đồ thị có hướng không chu trình (DAG)</b>:

- <b>Các nút:</b> Là các vị trí $j$ từ $0$ đến $n-i$ (tượng trưng cho độ dài xâu con).
- <b>Cạnh:</b> Có một cạnh từ nút $j$ đến nút $j + step$ nếu xâu con của $S$ bắt đầu tại $i+j$ có độ dài $step$ nằm trong tập $T$ (Trie giúp ta tìm tất cả các $step$ này cực nhanh).
- <b>Mục tiêu:</b> Tìm nút xa nhất (giá trị $j$ lớn nhất) có thể đi tới được từ nút $0$.

<b>Ví dụ:</b> Cho từ điển {"app", "apple", "let"} và xâu $S = \text{"apple"}$.

- Tại $i=0$: Trie tìm được "app" (nhảy tới $i=3$) và "apple" (nhảy tới $i=5$).
- Tại $i=3$: Trie không tìm thấy từ nào bắt đầu bằng "le...".
- Tại $i=5$: Đạt tới cuối xâu -> Kết quả: `5`.

### <b>II, Trie XOR</b>

#### <b>1. Khái niệm</b>

Trie XOR là một biến thể của Trie. Thay vì mỗi cạnh là một chữ cái (`a` - `z`), thì mỗi cạnh bây giờ là một bit (`0` hoặc `1`). Một số nguyên sẽ được biểu diễn thành một chuỗi nhị phân có độ dài cố định (thường là 31 bit nếu `int` hoặc 60 bit nếu `long long`) và chèn vào Trie từ <b>Bit cao nhất (MSB) xuống Bit thấp nhất (LSB)</b>.

```c++
struct trie_xor {
    static const int limNode = 1e5 * 31; // tùy vào N và số bit
    int nxt[limNode][2];
    int cntPass[limNode];
    int cnt;
    trie_xor() {
        cnt = 1;
        memset(nxt, 0, sizeof nxt);
        memset(cntPass, 0, sizeof cntPass);
    }
    // val = 1 để thêm số, val = -1 để xóa số
    void upd(int x, int val) {
        int u = 1;
        cntPass[u] += val;
        for (int i = 30; i >= 0; i--) {
            int k = bit(x, i); // macro bit(mask, i) (mask >> i) & 1 :)
            if (!nxt[u][k]) nxt[u][k] = ++cnt;
            u = nxt[u][k];
            cntPass[u] += val;
        }
    }
    int getMax(int x) {
        int u = 1;
        int res = 0;
        for (int i = 30; i >= 0; i--) {
            int k = bit(x, i);
            // ưu tiên đi nhánh ngược (k^1) nhưng phải còn phần tử
            if (nxt[u][k ^ 1] && cntPass[nxt[u][k ^ 1]] > 0) { // x ^ 1: trick flip bit
                res |= (1 << i); // bật bit thứ i
                u = nxt[u][k ^ 1];
            } else u = nxt[u][k];
            if(!u) return res;
        }
        return res;
    }
};
```

<b>Ý nghĩa của hàm `getMax(int x)`</b>

Đây là bản chất của thuật toán <b>Tham lam</b> trên bit:

- Bit thứ $i$ có giá trị là $2^i$.
- Tổng tất cả các bit phía sau nó: $2^{i-1} + 2^{i-2} + \dots + 2^0 = 2^i - 1$.

$\Rightarrow$ <b>Kết luận:</b> Nếu tại bit thứ $i$, ta có cơ hội tạo ra bit `1` cho kết quả XOR (bằng cách đi vào nhánh `k ^ 1` - nhánh ngược lại với bit hiện tại của $x$), ta <b>BẮT BUỘC</b> phải đi. Một bit `1` ở vị trí $i$ luôn lớn hơn tất cả các bit `1` ở các vị trí phía sau cộng lại

#### <b>2. Các bài toán kinh điển (Standard Trie)</b>

##### <b>a, Bài toán 1: Cặp phần tử có XOR lớn nhất (Max XOR Pair)</b>

<b>Mô tả:</b> Cho mảng $A$ gồm $N$ phần tử. Tìm $i, j$ sao cho $A[i] \oplus A[j]$ đạt max. ($N \le 10^5$).
<b>Tại sao phải dùng Trie XOR?</b> Duyệt trâu $\mathcal{O}(N^2)$ sẽ bị TLE. Không có thuật toán nào khác tối ưu hơn Binary Trie. Ta nhét lần lượt các số vào Trie XOR, với mỗi số $A[i]$, ta dùng `getMax(A[i])` trên các số đã chèn trước đó để cập nhật đáp án. Độ phức tạp $\mathcal{O}(N \times 31)$.

##### <b>b, Bài toán 2: Đoạn con liên tiếp có XOR lớn nhất (Max XOR Subarray)</b>

<b>Mô tả:</b> Tìm một đoạn con liên tiếp có tổng XOR lớn nhất.

<b>Giải pháp:</b> Dựa vào tính chất $X \oplus X = 0$. Ta xây dựng mảng cộng dồn XOR: $Pref[i] = A[1] \oplus A[2] \dots \oplus A[i]$.
- Khi đó, XOR của đoạn con từ $L$ đến $R$ là: $Pref[R] \oplus Pref[L-1]$. $\Rightarrow$ Giải theo `Bài toán 1` :D

##### <b>b, Bài toán 3: Số cặp XOR nhỏ hơn $K$ (XOR less than K)</b>

<b>Mô tả:</b> Cho mảng $A$ và số $K$. Đếm số cặp $(i, j)$ sao cho $A[i] \oplus A[j] \le K$.

<b>Giải pháp:</b> Khó hơn getMax một chút. Khi duyệt Trie cùng với một số $X$ để xét các bit của $K$:

- Nếu bit thứ $i$ của $K$ là `1`: Ta có thể đi theo nhánh tạo ra bit `0` cho phép XOR (toàn bộ nhánh này chắc chắn tạo ra số nhỏ hơn $K$, nên ta cộng toàn bộ `cntPass` của nhánh này vào đáp án), sau đó di chuyển xuống nhánh tạo ra bit `1` để xét tiếp.

- Nếu bit thứ $i$ của $K$ là `0`: Bắt buộc phải đi theo nhánh tạo ra bit `0` cho phép XOR, không được cộng thêm gì cả.

##### <b>b, Bài toán 4: Max XOR với giới hạn độ dài</b>

<b>Mô tả:</b> Tìm đoạn con có XOR lớn nhất nhưng độ dài đoạn con không được vượt quá $K$ ($R - L + 1 \le K$).

<b>Giải pháp:</b> (Sliding Window + Trie XOR):
- Khi ta đang ở vị trí $i$, ta cần tìm $P[j]$ trong khoảng $[i-K, i-1]$.
- Dùng sliding window như sau:
1. Khi sang vị trí $i$, thêm $P[i-1]$ vào Trie: `trie.update(P[i-1], 1)`.
2. Nếu $i > K$, xóa $P[i-K-1]$ khỏi Trie: `trie.update(P[i-K-1], -1)`.
3. Truy vấn `trie.getMax(P[i])`.

## <b> III, Aho–Corasick </b>

## <b>B. Suffix Array & Thuật toán Kasai</b>

## <b>C. Suffix Tree</b>

## <b>D. Suffix Automaton (SAM)</b>