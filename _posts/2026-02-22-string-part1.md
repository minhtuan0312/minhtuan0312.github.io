---
layout: post
title: Các thuật toán về xâu kí tự🔐 (Phần 1) (🚧)
date: 2026-02-22 12:43 +0700
math: true
categories: [string, template]
tags: [hash]
---

> <b> Đây là phần 1 của `Các thuật toán về xâu kí tự`, xem phần 2 ở đây</b>: [Các thuật toán về xâu kí tự🔐 (Phần 2)](/posts/string-part2/)
{: .prompt-info}

## <b>A. Z-Function & KMP</b>

### <b>I. Z-Function (Hàm Z) </b>

#### <b> 1. Khái niệm</b>

Cho một chuỗi $S$ có độ dài $n$ (chỉ số từ $1$ đến $n$).

> Mảng $Z$ của chuỗi $S$ là một mảng cỡ $n$, trong đó $Z[i]$ là <b>độ dài tiền tố chung dài nhất (Longest Common Prefix - LCP)</b> giữa chính chuỗi $S$ và hậu tố của $S$ bắt đầu tại vị trí $i$.
{: .prompt-info}

<i>Nói cách khác:</i> $Z[i]$ là số $k$ lớn nhất sao cho $S[1 \dots k] = S[i \dots i+k-1]$.

<b>Ví dụ:</b> Cho chuỗi $S = \text{abacaba}$ (1-index).

- $Z[1] = 7$ (Toàn bộ chuỗi, đôi khi người ta gán bằng 0 tùy bài toán, nhưng mặc định là $n$).
- $Z[2] = 0$ (Hậu tố bacaba so với abacaba $\rightarrow$ không có tiền tố chung).
- $Z[3] = 1$ (Hậu tố acaba so với abacaba $\rightarrow$ chung chữ a).
- $Z[5] = 3$ (Hậu tố aba so với abacaba $\rightarrow$ chung chuỗi aba).

#### <b>2. Thuật toán tối ưu $O(n)$ </b>

Cách ngây thơ tốn $O(n^2)$, điều này là quá chậm cho CP. Để tối ưu xuống $O(n)$, chúng ta sử dụng một khái niệm gọi là Z-box (Hộp Z).

<b>Khái niệm Z-box $[L, R]$ </b>

Z-box tận dụng việc "<b>chúng ta đã từng thấy đoạn này ở đâu đó phía trước rồi</b>" để bỏ qua các bước so sánh thừa.

Khi tính toán từ trái sang phải, ta duy trì một đoạn $[L, R]$ <b>với $R$ lớn nhất có thể</b> sao cho đoạn $S[L \dots R]$ khớp hoàn toàn với một tiền tố của $S$. Tức là $S[L \dots R] = S[1 \dots R - L + 1]$.

> Có thể hiểu <b>Z-box</b> như là một cái gương <b>sliding window</b>. Cửa sổ $[L, R]$ bao phủ vị trí $i$, khi bước vào window này ta chỉ đơn giản là soi gương nhìn về phía đầu xâu (vị trí $k$) để <b>xem kết quả có sẵn chưa</b>. Nếu gương không soi hết được đoạn khớp dài hơn, thì ta mới phải đẩy cạnh $R$ của cửa sổ sang phải để khám phá tiếp.
{: .prompt-tip}

Từ đó khi tính $Z[i]$, ta suy ra có <b>2 trường hợp</b>:

> <b>Trường hợp 1: $i > R$ (Vị trí $i$ nằm ngoài Z-box)</b>: <br>- Chúng ta không có thông tin gì từ quá khứ. Bắt buộc phải tính ngây thơ bằng cách so sánh từng ký tự: $S[i]$ với $S[1]$, $S[i+1]$ với $S[2]$,... Sau khi tính được $Z[i]$, nếu đoạn khớp mới này ($i$ đến $i + Z[i] - 1$) nằm xa hơn $R$ cũ, ta cập nhật $L = i$ và $R = i + Z[i] - 1$. Đây là cách chúng ta đẩy Z-box về phía bên phải. <b>(ĐK1)</b><br><b>- Tuy nhiên</b>, ngay cả khi đã lấy giá trị từ $Z[k]$, nếu đoạn khớp đó chạm đến biên $R$, chúng ta vẫn phải dùng while để xem nó có thể dài hơn nữa không.<b>(ĐK2)</b>
- <b>Điều kiện 1:</b> Ký tự tại `s[1 + z[i]]` <b>(prefix)</b> khớp với `s[i + z[i]]` <b>(suffix)</b>
- <b>Điều kiện 2:</b> `i + z[i]` <b>(suffix)</b> không vượt quá $N$.
{: .prompt-info}


> <b>Trường hợp 2: $i \le R$ (Vị trí $i$ nằm gọn trong Z-box)</b>: <br>- Vì $S[L \dots R]$ giống hệt $S[1 \dots R-L+1]$, nên vị trí $i$ trong đoạn $[L, R]$ có một vị trí tương ứng ở đoạn tiền tố $[1, R-L+1]$. <br> <b>- Mirror:</b> Khoảng cách từ $L$ đến $i$ chính bằng khoảng cách từ $1$ đến $k$. $\to$ Do đó: `k = 1 + (i - L)` <br><b>- Công thức tổng quát:</b> `Z[i] = min(R - i + 1, Z[k])`. <br>*( `R - i + 1`: Số lượng ký tự còn lại nằm trong của Z-box)
{: .prompt-info}

<b>Giải thích công thức: $Z[i] = \min(1 + R - i, Z[k])$</b>

<b>TH1: $Z[k]$ ngắn, nằm gọn trong Z-box ($Z[k] < R - i + 1$)</b>
- Nếu tại $k$ chỉ khớp được một đoạn ngắn, thì tại $i$ bạn cũng chỉ khớp được đúng bấy nhiêu đó, không cần làm gì thêm. $Z[i] = Z[k]$.

<b>TH2: $Z[k]$ dài, vượt quá Z-box ($Z[k] \ge R - i + 1$)</b>
- Chúng ta chỉ biết chắc chắn là $S[i \dots R]$ khớp với tiền tố. <b>Còn phần sau $R$ thì chưa biết</b> (vì Z-box chỉ bảo kê đến $R$).
- Do đó, ta tạm thời gán $Z[i] = R - i + 1$ (đoạn còn lại trong box), sau đó nhảy ra ngoài $R$ để so sánh thủ công tiếp (giống Trường hợp 1).

```c++
vector<int> buildZ(const string& s, int n) {
    vector<int> z(n + 1);
    z[1] = n; // tiền tố chung với chính nó là n
    int l = 1, r = 1;
    FOR(i, 2, n + 1) {
        // nếu i nằm trong Z-box, tái sử dụng kết quả
        int k = 1 + i - l; // chiếu i vào đoạn trước đó cần copy sang
        if (i <= r) {
            z[i] = min(r - i + 1, z[k]); // min(số lượng ký tự còn lại trong Z-box, z[k])
        }
        // mở rộng Z-box (kiểm tra phần còn lại)
        // điều kiện 1: s[1 + z[i]] khớp với s[i + z[i]]
        // điều kiện 2: i + z[i] không vượt quá N
        while (s[1 + z[i]] == s[i + z[i]] && i + z[i] <= n) {
            z[i]++;
        }
        if (i + z[i] - 1 > r) { // cập nhật lại Z-box nếu vượt qua r hiện tại
            l = i;
            r = i + z[i] - 1;
        }
    }
    return z;
}
```

#### <b>3. Các dạng toán kinh điển và cách giải (Pattern)</b>

##### <b>Dạng 1: Tìm kiếm chuỗi (String Matching)</b>

Bài toán: Đếm số lần chuỗi $P$ (Pattern) xuất hiện trong chuỗi $T$ (Text).

Cách giải thuật toán Z:

1. Tạo một chuỗi mới: $S = P$ + # + $T$ (Ký tự # là một ký tự KHÔNG xuất hiện trong cả $P$ và $T$).

2. Tính mảng Z cho chuỗi $S$.

3. Duyệt mảng Z ở phần thuộc về chuỗi $T$. Nếu tồn tại $Z[i] = |P|$, nghĩa là $P$ xuất hiện tại vị trí đó.

<b>Tại sao cần #?</b> Để ngăn chặn Z-box "tràn" từ chuỗi $P$ sang chuỗi $T$, giới hạn giá trị tối đa của $Z[i]$ đúng bằng độ dài của $P$.

##### <b>Dạng 2: Tìm chu kỳ ngắn nhất của chuỗi (String Compression)</b>

Bài toán: Tìm độ dài $k$ ngắn nhất sao cho chuỗi $S$ có thể được tạo ra bằng cách lặp lại tiền tố độ dài $k$ nhiều lần. (Ví dụ: ababab có chu kỳ $k=2$).

Cách giải thuật toán Z:

1. Tính mảng Z cho $S$.

2. Xét các độ dài $k$ từ $1$ đến $n/2$.

3. Điều kiện để $k$ là chu kỳ:
- $n \pmod k == 0$ (Độ dài chuỗi chia hết cho $k$).
- $Z[k + 1] == n - k$ (Hậu tố bắt đầu từ $k+1$ khớp hoàn toàn với toàn bộ phần tiền tố).

##### <b>Dạng 3: Tiền tố cũng là hậu tố (Prefix-Suffix / Border)</b>

Bài toán: Tìm tất cả các độ dài $L$ sao cho tiền tố độ dài $L$ của $S$ giống hệt hậu tố độ dài $L$ của $S$. (Ví dụ: abacaba có các $L$ thỏa mãn là $1$ (a), $3$ (aba), và $7$ (abacaba)).

Cách giải thuật toán Z:

1. Tính mảng Z cho $S$.

2. Duyệt các độ dài $L$ từ $1$ đến $n$. Vị trí bắt đầu của hậu tố độ dài $L$ sẽ là $i = n - L + 1$.

3. Nếu $Z[i] = L$, thì $L$ là một đáp án hợp lệ.

##### <b>Dạng 4: Đếm số lần xuất hiện của mọi tiền tố (Prefix Frequencies)</b>

Bài toán: Cho xâu $S$ độ dài $n$. Với mỗi $L \in [1, n]$, hãy đếm xem tiền tố $S[1 \dots L]$ xuất hiện bao nhiêu lần trong $S$ như một xâu con.

- Tại sao dùng Z-function? Theo định nghĩa, $Z[i] = k$ có nghĩa là xâu con bắt đầu tại $i$ khớp với tiền tố độ dài $k$. Điều này đồng nghĩa với việc nó cũng khớp với tất cả các tiền tố có độ dài ngắn hơn: $1, 2, \dots, k-1$.

Cách giải tối ưu:

1. Tính mảng $Z$.
2. Dùng một mảng đếm cnt: Với mỗi $i$, ta đánh dấu cnt[Z[i]]++.
3. Nhận xét: Nếu tiền tố độ dài $L$ xuất hiện, thì tiền tố độ dài $L-1$ cũng xuất hiện tại cùng vị trí đó.
4. Duyệt ngược từ $n$ về $1$: cnt[i] += cnt[i+1].
5. Kết quả: cnt[L] + 1 (cộng 1 vì bản thân tiền tố đó luôn xuất hiện ở vị trí 1).

- Độ phức tạp: $O(n)$.

##### <b>Dạng 5: Khớp chuỗi với tối đa 1 sai khác (Matching with 1 Mismatch)</b>

Bài toán: Cho xâu mẫu $P$ và văn bản $T$. Tìm tất cả vị trí trong $T$ mà $P$ xuất hiện với tối đa 1 ký tự khác biệt.

Cách giải:

1. Gọi $S_1 = P $+ # +$ T$. Tính mảng $Z_1$. $Z_1[i]$ cho biết độ dài khớp nhau dài nhất từ trái sang.

2. Gọi $S_2 = \text{reverse}(P) $+ # +$ \text{reverse}(T)$. Tính mảng $Z_2$. $Z_2[j]$ cho biết độ dài khớp nhau dài nhất từ phải sang.

3. Xét vị trí $i$ trong $T$. Đoạn con độ dài $|P|$ bắt đầu từ $i$ sẽ khớp với $P$ nếu:
- $$\text{LCP\_trái} + \text{LCP\_phải} \ge |P| - 1$$

(Trong đó $\text{LCP\_trái}$ lấy từ $Z_1$, $\text{LCP\_phải}$ lấy từ $Z_2$.)

- Tối ưu: Giải quyết trong $O(n + m)$ thay vì $O(n \cdot m)$ hay dùng Hashing + Binary Search $O(n \log m)$.

##### <b>Dạng 6: Tìm tiền tố chung dài nhất của 2 hậu tố (LCP of Suffixes)</b>

Bài toán: Cho xâu $S$. Cần trả lời nhiều truy vấn: LCP của hậu tố bắt đầu tại $1$ và hậu tố bắt đầu tại $i$ là bao nhiêu?

- Đây chính là định nghĩa gốc của Z-function.

- Sức mạnh: Trong khi Suffix Array cần $O(n \log n)$ để cài đặt phức tạp, Z-function chỉ mất vài dòng code $O(n)$ để trả lời mọi truy vấn liên quan đến "Tiền tố gốc" và "Hậu tố thứ $i$".

##### <b>Dạng 7: Xâu con đối xứng ngắn nhất/dài nhất là tiền tố (Prefix Palindrome)</b>

Bài toán: Tìm tiền tố dài nhất của xâu $S$ mà bản thân nó là một xâu đối xứng (Palindrome).

- Cách giải dùng Z-function:
1. Tạo xâu mới $S' = S $+ # +$ \text{reverse}(S)$.
2. Tính mảng $Z$ cho $S'$.
3. Duyệt các vị trí $i$ thuộc phần $\text{reverse}(S)$. Nếu $Z[i]$ bằng đúng khoảng cách từ $i$ đến cuối xâu $S'$, thì đó là một tiền tố đối xứng.

##### <b>Dạng 8: Bài toán "Border" nâng cao (Periodicity)</b>

Bài toán: Tìm tất cả các giá trị $k$ sao cho $S$ có thể được bao phủ hoàn toàn bởi các bản sao của tiền tố độ dài $k$ (các bản sao có thể đè lên nhau - overlaps). Những $k$ này gọi là các "Border" của xâu.

- Cách giải: $k$ là một border nếu và chỉ nếu $Z[n - k + 1] == k$.

- Z-function giúp tìm toàn bộ các border này trong $O(n)$, rất hữu ích cho các bài toán về nén xâu và cấu trúc lặp.

### <b>II. KMP (Knuth-Morris-Pratt)</b>

#### <b> 1. Ý tưởng cốt lõi của KMP</b>

Thuật toán trâu khi tìm xâu mẫu `P` trong xâu văn bản `T` sẽ duyệt từng vị trí, nếu sai thì quay lại từ đầu. KMP giải quyết sự lãng phí này bằng cách: <b>"Khi xảy ra sai khác, ta đã biết trước một phần của xâu khớp nhau. KMP tận dụng thông tin này để không bao giờ lùi con trỏ trên xâu văn bản `T`."

Để làm được điều này, KMP sử dụng <b>Mảng Tiền Tố (Prefix Array)</b>, thường gọi là mảng `pi` (hoặc `lps` - Longest Proper Prefix which is also Suffix).

#### <b> 2. Mảng $\pi(i)$ </b>

> <b>Định nghĩa:</b> `pi[i]` là <i>độ dài lớn nhất của một tiền tố</i> (không bao gồm toàn bộ xâu) và đồng thời là <i>hậu tố</i> của xâu con P[1...i].
{: .prompt-info}

<b>Ví dụ:</b> Cho xâu `P = " a b a b a c a"` (1-index).
- `pi[1] ('a') = 0` (Tiền tố thực sự không được là chính nó).
- `pi[2] ('ab') = 0` (Không có tiền tố nào giống hậu tố).
- `pi[3] ('aba') = 1` (Tiền tố 'a' giống hậu tố 'a').
- `pi[4] ('abab') = 2` (Tiền tố 'ab' giống hậu tố 'ab').
- `pi[5] ('ababa') = 3` (Tiền tố 'aba' giống hậu tố 'aba').

<b>Kỹ thuật nhảy lùi:</b>

Tiếp tục ví dụ trên. Khi đang so sánh `P[i]` với `P[j+1]` mà chúng khác nhau, thay vì cho `j` về 0, ta lùi `j` về `pi[j]`. Điều này có nghĩa là: "Nếu không khớp độ dài `j+1`, thử xem độ dài tiền tố-hậu tố dài nhất tiếp theo là bao nhiêu để khớp tiếp".

```c++
int main(void) {
    int n; cin >> n;
    string s; cin >> s;
    s = ' ' + s;
    vector<int> pi(n + 1);
    int j = 0; // j là độ dài tiền tố khớp hiện tại
    FOR(i, 2, n + 1) { // bắt đầu từ 2 vì pi[1] luôn bằng 0\
        // Nếu không khớp, lùi j về pi[j] cho đến khi khớp hoặc j = 0
        while (j > 0 && s[i] != s[j + 1]) {
            j = pi[j];
        }
        // nếu khớp, tăng độ dài j lên 1
        if (s[i] == s[j + 1]) {
            j++;
        }
        pi[i] = j;
    }
}
```
#### <b> 3. Thuật toán tìm kiếm KMP </b>

Khi đã có mảng `pi` của xâu mẫu `P`, ta áp dụng logic y hệt để duyệt qua xâu văn bản `T`.

```c++
void KMP(const string &P, int n, const string &T, int m, vector<int> pi) {
    int j = 0; // Số ký tự của P đã khớp với T
    for (int i = 1; i <= n; i++) {
        // nhảy lùi nếu có sự sai khác
        while (j > 0 && T[i] != P[j + 1]) {
            j = pi[j];
        }
        // nếu khớp, tăng j
        if (T[i] == P[j + 1]) {
            j++;
        }
        // nếu đã khớp toàn bộ xâu mẫu P
        if (j == m) {
            cout << "Tìm thấy P trong T tại vị trí: " << i - m + 1 << "\n";
            // tiếp tục nhảy lùi để tìm các vị trí xuất hiện khác (có thể giao nhau)
            j = pi[j];
        }
    }
}
```

#### <b>4. Kỹ thuật nối chuỗi</b>

Thay vì phải viết thêm một hàm riêng biệt thì ta có thể sử dụng <b>kỹ thuật nối chuỗi (Concatenation Trick).</b>

> $S = P$ + # + $T$ (Ký tự # là một ký tự không bao giờ xuất hiện trong cả P và T).
{: .prompt-tip}

- <b>Logic:</b> Nếu tại vị trí $i$ nào đó trong chuỗi mới $S$, mà $pi[i] = \|P\|$ (độ dài chuỗi mẫu), thì ta biết chắc chắn chuỗi mẫu xuất hiện kết thúc tại vị trí đó trong $T$.

```c++
string p, t; cin >> p >> t;
string s = p + '#' + t;
int n = sz(s), m = sz(p);
s = ' ' + s;
vector<int> pi(n + 1);
int j = 0;
FOR(i, 2, n + 1) {
    while (j && s[i] != s[j + 1]) {
        j = pi[j];
    }
    if (s[i] == s[j + 1]) j++;
    pi[i] = j;
    // nếu đã khớp toàn bộ xâu mẫu P
    // vì i chạy trên chuỗi s = P + # + T, nên vị trí trong T sẽ là i - (m + 1)
    if (pi[i] == m) {
        cout << "Tìm thấy P trong T tại vị trí: " << i - 2 * m << nl;
    }
}
```

#### <b>5. Các dạng toán kinh điển</b>

Mảng pi của KMP cực kỳ quyền năng và thường được biến tấu vào nhiều dạng bài khác nhau:

##### <b>Dạng 1: Đếm / Tìm vị trí xuất hiện của xâu P trong xâu T:</b>

Chính là thuật toán KMP nguyên bản ở trên.

##### <b>Dạng 2: Tìm chu kỳ ngắn nhất (Shortest Period) của xâu:</b>

Bài toán: Xâu S độ dài N có thể được tạo ra bằng cách lặp lại một xâu con ngắn nhất bao nhiêu lần? (ví dụ "ababab" tạo từ "ab" lặp 3 lần).

Giải pháp: Tính mảng pi cho toàn bộ xâu S. Nếu N % (N - pi[N]) == 0 thì N - pi[N] chính là độ dài chu kỳ ngắn nhất.

##### <b>Dạng 3: Đếm số lần xuất hiện của tất cả các tiền tố:</b>

Bài toán: Đếm xem mỗi tiền tố của S xuất hiện bao nhiêu lần trong chính S.

Giải pháp: Khởi tạo mảng count[i] = 1 cho mọi i. Duyệt i từ N lùi về 1: count[pi[i]] += count[i]. Cách này quy hoạch động trực tiếp trên cây tiền tố ảo (KMP Automaton tree) với O(N).

##### <b>Dạng 4: Nén xâu (String Compression):</b>

Bài toán: Tìm phần nối thêm ngắn nhất để xâu trở thành xâu đối xứng, hoặc ghép hai xâu A và B sao cho phần chung (hậu tố A giống tiền tố B) là dài nhất.

##### <b>Dạng 5: Bài toán "Password" (Codeforces 126B):</b>

Mô tả: Cho một xâu S. Hãy tìm xâu con dài nhất vừa là tiền tố, vừa là hậu tố, và xuất hiện ít nhất một lần ở giữa xâu (không phải ở hai đầu).

Vì sao dùng KMP: * Theo định nghĩa, `pi[N]` cho ta độ dài của phần <b>vừa là tiền tố vừa là hậu tố dài nhất</b>. Ký hiệu `L = pi[N]`.

- Để xâu này xuất hiện ở giữa, độ dài `L` này phải từng xuất hiện trong mảng `pi` từ vị trí `2` đến `N-1`.
- Nếu không tìm thấy `L` ở giữa, ta tiếp tục lùi về `L = pi[L]` và kiểm tra lại.

<b>Tối ưu:</b> Thuật toán KMP giải bài này hoàn hảo trong độ phức tạp $O(N)$ chỉ bằng vài dòng `if-else` duyệt mảng `pi`, trong khi Hashing phải kết hợp chặt nhị phân sẽ mất $O(N \log N)$ và dễ bị Time Limit Exceeded (TLE).

##### <b>Dạng 6: Đếm số lần xuất hiện của MỌI tiền tố (Quy hoạch động trên mảng pi):</b>

Mô tả: Cho xâu S độ dài N. Hãy in ra tần số xuất hiện của tất cả các tiền tố từ độ dài 1 đến N bên trong chính xâu S.

Vì sao dùng KMP: Bạn không thể đếm trâu bò từng tiền tố. Mảng `pi` hình thành một "Cây tiền tố ảo" (KMP Automaton tree).

Cách KMP tối ưu: * Khởi tạo mảng `count[i] = 1` cho mọi i từ 1 đến N.

- Duyệt ngược `i` từ `N` về 1, ta cập nhật: `count[pi[i]] = count[pi[i]] + count[i]`.
- Logic: Nếu một tiền tố độ dài `i` xuất hiện, thì tiền tố lớn nhất giống hậu tố của nó (độ dài `pi[i]`) cũng CHẮC CHẮN xuất hiện ngay tại đó. Code chỉ mất đúng $O(N)$ thao tác cộng, cực kỳ ma thuật và thanh lịch.

##### <b>Dạng 7: Tìm kiếm chuỗi Online (Dữ liệu vào liên tục / Streaming):</b>

Mô tả: Bạn cần đếm số lần xâu `P` xuất hiện, nhưng xâu văn bản `T` lớn tới hàng Terabyte hoặc được nhập vào liên tục qua mạng từng ký tự một (không thể lưu toàn bộ `T` vào mảng).

Vì sao chỉ KMP làm được: KMP là thuật toán Online. Con trỏ `j` lưu trạng thái khớp hiện tại chỉ cần biết ký tự tiếp theo của `T` để quyết định đi tiếp hay "nhảy lùi" trên mảng `pi`.

Tối ưu: Bộ nhớ duy nhất bạn tốn là $O(M)$ để lưu mảng `pi` của xâu mẫu `P` (rất nhỏ). Hashing hay Z-Algorithm bắt buộc phải có toàn bộ xâu T từ đầu, hoặc phải dùng kỹ thuật trượt cửa sổ rất cồng kềnh.


## <b>B. String Hashing</b>
### <b>I. Bản chất</b>

Đôi khi ta phải so sánh hai đoạn văn bản rất dài xem có giống nhau không. Nếu so sánh từng chữ cái, độ phức tạp sẽ là $O(N)$, rất chậm.

<b>Ý tưởng của Hash:</b> Biến mỗi xâu ký tự thành một <b>con số duy nhất</b>. Khi đó, để so sánh hai xâu, ta chỉ cần so sánh hai con số trong thời gian $O(1)$. Kỹ thuật phổ biến nhất trong CP là <b>Polynomial Rolling Hash</b> (Hash đa thức).

Hãy tưởng tượng xâu của bạn giống như một con số ở <b>hệ cơ số $base$</b> (với $base$ là một số nguyên dương, dùng $base = 911$ cho bảng mã ASCII vì nó dễ nhớ :v).

Ví dụ xâu $S = \text{"abc"}$ ở hệ cơ số $base$ sẽ có giá trị:

$$H(\text{"abc"}) = (\text{'a'} \cdot base^2 + \text{'b'} \cdot base^1 + \text{'c'} \cdot base^0)$$

Vì độ dài xâu có thể lên tới $10^5$, con số này sẽ cực kỳ khổng lồ và tràn mọi kiểu dữ liệu. Do đó, ta cần thực hiện phép tính chia lấy dư cho một số modulo $m$ đủ lớn (thường là $10^9+7$).

### <b>II. Xây dựng mảng Prefix Hash (Hash tiền tố)

Để lấy mã Hash của bất kỳ đoạn con nào trong $O(1)$, ta cần xây dựng mảng $h$ lưu giá trị hash của các tiền tố từ $1$ đến $i$. (Nên dùng chỉ số bắt đầu từ $1$ để dễ code).

> <b>Công thức quy hoạch động tính Prefix Hash:</b> <br> $$h[i] = (h[i-1] \cdot base + S[i]) \pmod m$$
{: .prompt-info}

### <b>III. Hash đoạn con</b>

Giả sử chúng ta có mảng $h$, làm sao để lấy mã Hash của đoạn con từ vị trí $l$ đến $r$?

> <b>Công thức:</b> $$Hash(l, r) = h[r] - h[l-1] \cdot base^{r-l+1} \pmod m$$
{: .prompt-info}

<b>*Lưu ý: phải xử lý modulo âm.</b>

<b>Giải thích:</b>

Hãy nghĩ về hệ thập phân (cơ số 10). Ta có số $12345$.

- $h[r]$ (tương ứng tiền tố $1 \dots 5$) là số $12345$.
- $h[l-1]$ (tương ứng tiền tố $1 \dots 2$) là số $12$.
- Ta muốn lấy đoạn từ vị trí 3 đến 5 (tương ứng đoạn $345$).
- Làm sao từ $12345$ và $12$ lấy ra được $345$? Lấy $12345 - 12000$.
- Mà $12000$ chính là $12 \cdot 10^3$. Số $3$ ở đây chính là độ dài của đoạn cần lấy: $(5 - 3 + 1)$.

$\Rightarrow \quad$ Suy rộng ra cơ số $base$: Ta lấy $h[r]$ trừ đi $h[l-1]$ được dịch trái $r-l+1$ bước (tức là nhân với $base^{r-l+1}$).

### <b>IV. Double Hash</b>

Vấn đề: Trong các kỳ thi lớn, người ta thường tạo ra các test <b>Anti-Hash</b> (Nghịch lý ngày sinh - Birthday Paradox). Nếu ta chỉ dùng 1 bộ $(base, m)$ như $base=311, m=10^9+7$, chắc chắn sẽ có 2 xâu khác nhau nhưng <b>sinh ra cùng 1 số Hash</b> $\rightarrow$ Code chạy sai nộp lên bị WA.

Giải pháp: Dùng <b>Double Hash</b>. Ta tính Hash đồng thời trên 2 modulo khác nhau (ví dụ $m_1 = 10^9+7$ và $m_2 = 10^9+9$). Hai xâu chỉ được coi là giống nhau nếu <b>cả hai giá trị Hash đều bằng nhau</b>. Xác suất đụng độ lúc này giảm xuống chỉ còn khoảng $\frac{1}{m_1 \cdot m_2} \approx 10^{-18}$, an toàn tuyệt đối.

### <b>V. Template</b>

```c++
// "abc" = ('a' * base^2 + 'b' * base^1 + 'c' * base^0) % mod
const int limN = 1e6 + 5;
const int base = 911;
const int mod1 = 1e9 + 7;
const int mod2 = 1e9 + 9;
typedef pair<ll, ll> ii;
ll pow1[limN], pow2[limN];
void init() {
    pow1[0] = pow2[0] = 1;
    FOR(i, 1, limN) {
        pow1[i] = pow1[i - 1] * base % mod1;
        pow2[i] = pow2[i - 1] * base % mod2;
    }
}
struct string_hashing {
    int n;
    string s;
    vector<ll> dp1, dp2;
    string_hashing() {}
    string_hashing(string s, int n): n(n), s(s), dp1(n + 1), dp2(n + 1) {
        FOR(i, 1, n + 1) {
            dp1[i] = dp1[i - 1] * base % mod1 + s[i];
            dp1[i] %= mod1;
            dp2[i] = dp2[i - 1] * base % mod2 + s[i];
            dp2[i] %= mod2;
        }
    }
    ii query(int l, int r) {
        if(l > r) return {0, 0};
        ll v1 = dp1[r] - dp1[l - 1] * pow1[r - l + 1] % mod1;
        v1 += mod1;
        v1 %= mod1;
        ll v2 = dp2[r] - dp2[l - 1] * pow2[r - l + 1] % mod2;
        v2 += mod2;
        v2 %= mod2;
        return {v1, v2};
    }
    ii operator()(int l, int r) {
        return query(l, r);
    }
};
int main(void) {
    init();
    string s, t; cin >> s >> t;
    int n = sz(s), m = sz(t);
    s = ' ' + s;
    t = ' ' + t;
    string_hashing hash_s(s, n), hash_t(t, m);
    int res = 0;
    FOR(i, 1, n - m + 1 + 1) {
        int j = i + m - 1;
        if(hash_s(i, j) == hash_t(1, m)) res++;
    }
    cout << res;
}
```

### <b>VI. LCP (Longest Common Prefix)</b>

#### <b>1. Khái niệm</b>

LCP (Longest Common Prefix) là <b>Tiền tố chung dài nhất</b>.

<b>Ví dụ:</b> `S1 = "aabac"` và `S2 = "aabxy"`.

Phần giống nhau tính từ đầu của 2 xâu là `"aab"`, có độ dài là 3. Vậy LCP của chúng bằng 3.

<b>Quy tắc vàng trong so sánh từ điển:</b>

Để biết xâu nào đứng trước trong từ điển (như tra từ điển tiếng Anh), ta không cần nhìn cả xâu mà chỉ cần:

1. Bỏ qua phần giống nhau ở đầu (chính là đoạn LCP).
2. Nhìn vào <b>ký tự đầu tiên khác biệt</b> ngay sau đoạn LCP đó. <b>Ký tự nào nhỏ hơn thì xâu đó nhỏ hơn</b>.
3. (Ngoại lệ: Nếu một xâu là tiền tố của xâu kia, tức là <b>chưa tìm thấy ký tự khác biệt mà một xâu đã hết, thì xâu ngắn hơn sẽ nhỏ hơn</b>).

Trong ví dụ trên, sau đoạn LCP `"aab"`, ta so sánh ký tự tiếp theo: `S1` có chữ `a`, `S2` có chữ `x`. Vì `a < x` nên `S1 < S2`.

#### <b> 2. LCP sử dụng Hash + Chặt nhị phân</b>

##### <b>a. LCP của 2 đoạn con</b>

Giả sử ta cần tìm LCP của 2 đoạn con $S[a \dots b]$ và $S[c \dots d]$. Ký hiệu $K$ là độ dài của đoạn LCP.

<b>Tính đơn điệu (Monotonicity) - Lý do ta dùng được Chặt nhị phân:</b>
- Nếu 2 đoạn con giống nhau ở độ dài $len$, chắc chắn chúng cũng sẽ giống nhau ở mọi độ dài $< len$.
- Nếu 2 đoạn con khác nhau ở độ dài $len$, chắc chắn chúng cũng sẽ khác nhau ở mọi độ dài $> len$.

<b>Cách làm:</b>
1. Phạm vi độ dài LCP ban đầu có thể từ $1$ đến `min(độ dài đoạn 1, độ dài đoạn 2)`. Gọi khoảng này là `[l, r]`.
2. Lấy điểm giữa `m = (l + r) >> 1`.
3. Dùng <b>Hash $O(1)$</b> để kiểm tra xem tiền tố độ dài `m` của 2 đoạn con có giống nhau không:
- Nếu <b>Giống</b>: Ta lưu lại `m` là kết quả tạm thời, và thử tìm LCP dài hơn bằng cách dịch trái: `l = m + 1`
- Nếu <b>Khác</b>: LCP thực sự chắc chắn phải ngắn hơn, dịch phải: `r = m - 1`.

```c++
struct string_hashing{
    //...

    // tìm chiều dài lcp của 2 đoạn con s[a..b] và s[c..d]
    int lcp(int a, int b, int c, int d) {
        int l = 1, r = min(b - a + 1, d - c + 1), res = 0;
        while(l <= r) {
            int m = (l + r) >> 1;
            if(Get(a, a + m - 1) == Get(c, c + m - 1)) {
                res = m;
                l = m + 1;
            } else r = m - 1;
        }
        return res;
    }
};
```

- <b>Độ phức tạp:</b> $O(\log N)$

##### <b>b. LCP của 2 xâu</b>

1. <b>Chặt nhị phân theo độ dài xâu con chung:</b> * Nếu có một xâu con chung độ dài $L$, thì chắc chắn cũng tồn tại xâu con chung độ dài $L-1$.
- Dải giá trị của độ dài là từ $0$ đến $\min(\|S\|, \|T\|)$.

2. `check(int x)`: Với một độ dài $x$ cố định, làm sao để biết $S$ và $T$ có xâu con chung nào dài đúng bằng $x$ không?
- <b>Bước 1:</b> Duyệt qua tất cả các đoạn con dài $x$ của xâu $S$. Tính mã Hash của chúng và đẩy vào một danh sách.
- <b>Bước 2:</b> Duyệt qua tất cả các đoạn con dài $x$ của xâu $T$. Tính mã Hash của từng đoạn và kiểm tra xem mã đó có tồn tại trong danh sách của xâu $S$ hay không.

```c++
struct string_hashing{
    //...
};
int main(void) {
    init();
    string s, t; cin >> s >> t;
    int n = sz(s), m = sz(t);
    s = ' ' + s;
    t = ' ' + t;
    string_hashing hash_s(s, n), hash_t(t, m);
    auto check = [&](int x) -> bool {
        vector<ii> A;
        // bước 1: thu thập toàn bộ hash độ dài x của xâu s
        FOR(i, 1, n - x + 1 + 1) {
            int j = i + x - 1;
            A.pb(hash_s(i, j));
        }
        sort(all(A)); // sort để tknp
        // bước 2: kiểm tra từng hash độ dài x của xâu t
        FOR(i, 1, m - x + 1 + 1) {
            int j = i + x - 1;
            ii ht = hash_t(i, j);
            if(binary_search(all(A), ht)) return 1;
        }
        return 0;
    };
    int l = 1, r = min(n, m), lcp = 0;
    while(l <= r){
        int mid = (l + r) >> 1;
        if(check(mid)) {
            lcp = mid;
            l = mid + 1;
        } else r = mid - 1;
    }
    cout << lcp;
}
```

- <b>Độ phức tạp:</b> $O((N+M) \log (\min(N, M)))$

### <b>VII. Một số bài toán kinh điển sử dụng Hashing</b>

#### <b>1. Khớp chuỗi (String Matching)</b>
<div class="problem-link">
  🔗 <strong>MARISAOJ - 165</strong>
  <a href="https://marisaoj.com/problem/165" target="_blank">
    Đếm xâu
  </a>
</div>
Tìm số lần xuất hiện của xâu $T$ trong xâu $S$.

> Lấy `hash_T = H_T(1, |T|)`. Sau đó duyệt mọi đoạn con độ dài bằng $\|T\|$ trên xâu $A$, kiểm tra xem hash có bằng `hash_T` không.
{: .prompt-info}
- Độ phức tạp: $O(N + M)$
- Tối ưu: `KMP` $O(N + M)$

Code tham khảo ở phần `Template`

#### <b>2. Tìm chu kỳ (period) nhỏ nhất của xâu</b>
<div class="problem-link">
  🔗 <strong>MARISAOJ - 167</strong>
  <a href="https://marisaoj.com/problem/167" target="_blank">
    Xâu lặp lại
  </a>
</div>
Giả sử xâu $T$ có độ dài là `len`. Khi lặp lại $T$ để tạo ra $S$, điều này có nghĩa là ký tự tại vị trí $i$ phải giống ký tự tại vị trí $i + len$:

$$S[i] = S[i+len] \text{ với mọi } 1 \le i \le n-len$$

Hãy nhìn vào các cặp ký tự sau:

- $S[1] = S[1+len]$
- $S[2] = S[2+len]$
...
- $S[n-len] = S[(n-len)+len] = S[n]$

Gom các vế trái và vế phải lại:

- Vế trái: $\{S[1], S[2], \dots, S[n-len]\}$. Đây chính là <b>Tiền tố</b> độ dài $n-len$.
- Vế phải: $\{S[1+len], S[2+len], \dots, S[n]\}$. Đây chính là <b>Hậu tố</b> độ dài $n-len$.

> <b>$\Rightarrow$ Định lý quan trọng:</b> Xâu $S$ có một chu kỳ độ dài $len$ khi và chỉ khi `tiền tố độ dài (n - len) bằng hậu tố độ dài (n - len)`.
{: .prompt-info}
Ví dụ với sussussu ($n=8$):
- Thử với $len=3$ (xâu $T$ là sus):
- Độ dài tiền tố/hậu tố cần check: $K = n - len = 8 - 3 = 5$.
- Tiền tố độ dài 5: sussu
- Hậu tố độ dài 5: sussu
- Vì Tiền tố == Hậu tố nên $len=3$ là một chu kỳ hợp lệ.

```c++
int main(void) {
    init();
    string s; cin >> s;
    int n = sz(s);
    s = ' ' + s;
    string_hashing hash_s(s, n);
    FOR(len, 1, n + 1) {
        int k = n - len;
        if (k == 0) return cout << s, 0;
        if(hash_s(1, k) == hash_s(n - k + 1, n)) return cout << s.substr(1, len), 0;
    }
}
```

#### <b>3. So sánh thứ tự từ điển 2 đoạn con:</b>

<div class="problem-link">
  🔗 <strong>MARISAOJ - 168</strong>
  <a href="https://marisaoj.com/problem/168" target="_blank">
    So sánh xâu con
  </a>
</div>

Cho 2 đoạn $S[a \dots b]$ và $S[c \dots d]$, hỏi đoạn nào đứng trước theo từ điển?

> Chặt nhị phân tìm chiều dài `tiền tố chung dài nhất (LCP - Longest Common Prefix)` của 2 đoạn con này bằng Hash. Sau khi có LCP, chỉ cần so sánh ký tự ngay sau đoạn LCP là biết xâu nào lớn hơn.
{: .prompt-info}
- Độ phức tạp: $O(\log N)$
- Tối ưu: `Suffix Array/Automaton` ($O(1)$) * ko khuyến khích code suffix cho dạng này

```c++
struct string_hashing{
    //...

    // hàm so sánh thứ tự từ điển: -1 (nhỏ hơn), 0 (bằng), 1 (lớn hơn)
    int compare(int a, int b, int c, int d) {
        int pre = lcp(a, b, c, d); // lcp 2 đoạn con
        int len1 = b - a + 1, len2 = d - c + 1;
        if(pre == len1 && pre == len2) return 0; // 2 xâu y hệt nhau
        if(pre == len1) return -1; // xâu 1 là tiền tố của xâu 2 -> nhỏ hơn
        if(pre == len2) return 1; // xâu 2 là tiền tố của xâu 1 -> lớn hơn
        return s[a + pre] < s[c + pre]? -1: 1; // // nếu không phải tiền tố, so sánh ký tự đầu tiên khác biệt
    }
};

```

#### <b>4. Tìm xâu con đối xứng (Palindrome) dài nhất</b>

<div class="problem-link">
  🔗 <strong>CSES - Longest Palindrome</strong>
  <a href="https://lqdoj.edu.vn/problem/cses1111" target="_blank">
    Xâu đối xứng dài nhất
  </a>
</div>

##### <b>a. Cơ chế đối xứng và ánh xạ Index </b>

> Tạo `string_hashing hash_forward(S, |S|)` và `string_hashing hash_reverse(reverse(S), |S|)`.
- <b>Ý tưởng:</b> Với mỗi vị trí $i$ trong xâu $S$, ta coi $i$ là tâm và dùng Chặt nhị phân để tìm <b>bán kính $R$ lớn nhất</b> sao cho đoạn xâu lan ra từ tâm đó vẫn là Palindrome.
- Điều kiện để đoạn $S[l \dots r]$ là Palindrome: `hash_forward(l, r) == hash_reverse(l, r)`
{: .prompt-info}

<b>Lưu ý quan trọng nhất khi xét xâu con:</b>
Nếu ta có một xâu con trên xâu gốc chạy từ trái qua phải là $[L, R]$. Khi đối chiếu trên xâu đảo ngược, ký tự $R$ (bên phải gốc) sẽ chạy lên làm ký tự đầu tiên bên trái, và ký tự $L$ sẽ bị đẩy xuống cuối.

> <b>Công thức ánh xạ (1-index):</b> Nếu xâu $S$ có độ dài $n$, đoạn $S[l \dots r]$ khi đảo ngược lại sẽ nằm ở vị trí sau đây trong xâu ngược $S_{rev}$: <br>$$l_{rev} = n - r + 1$$ <br>$$r_{rev} = n - l + 1$$
{: .prompt-info}

##### <b>b. Trường hợp palindrome độ dài lẻ (Tâm tại vị trí $i$)</b>

Trong trường hợp này, xâu đối xứng có dạng: `[bên trái] [ký tự i] [bên phải]`

Gọi $mid$ là bán kính (số lượng ký tự mở rộng sang mỗi bên). Đoạn xâu sẽ kéo dài từ $i - mid$ đến $i + mid$.
- Điều kiện bên trái: $i - mid \ge 1 \implies mid \le i - 1$
- Điều kiện bên phải: $i + mid \le n \implies mid \le n - i$

$\Rightarrow \quad$Do đó, giá trị tối đa mà $mid$ có thể đạt được là: $R = \min(i - 1, n - i)$

$\Rightarrow \quad$Tối thiểu $m = 0$ (tức là chỉ có 1 ký tự, chính nó là palindrome). Nên $L = 0$.

- rev_l sinh ra từ biên phải gốc ($i+m$): `rev_l = n - (i + m) + 1`
- rev_r sinh ra từ biên trái gốc ($i-m$): `rev_r = n - (i - m) + 1`

##### <b>c. Trường hợp palindrome độ dài chẵn (Trick chèn ký tự ảo)</b>

Thay vì ta xử lý trường hợp palindrome độ dài chẵn (siêu nản) thì ta có thể sử dụng kỹ thuật <b>Chèn ký tự ảo</b> (thường dùng dấu `#`) để ép mọi loại palindrome về dạng lẻ. Từ đó, ta chỉ cần code <b>một hàm duy nhất</b> mà không phải lo phân chia trường hợp.

<b>Ý tưởng cốt lõi</b>
Giả sử xâu gốc là $S$. Ta tạo xâu mới $S'$ bằng cách chèn ký tự # vào giữa mỗi ký tự và ở hai đầu.

- Xâu gốc: ABBA (độ dài 4 - chẵn) $\to$ $S'$: #A#B#B#A# (độ dài 9 - lẻ).
- Xâu gốc: ABA (độ dài 3 - lẻ) $\to$ $S'$: #A#B#A# (độ dài 7 - lẻ).

Khi ta biến đổi $S \rightarrow S'$ (ví dụ: aba $\rightarrow$ #a#b#a#), mỗi vị trí $i$ trong $S'$ sẽ đại diện cho một "tâm" đối xứng:
- Nếu $S'[i]$ là một ký tự (như a, b): Đó là tâm của xâu đối xứng có độ dài lẻ.
- Nếu $S'[i]$ là ký tự #: Đó là tâm của xâu đối xứng có <b>độ dài chẵn</b>.

> <b>Quy luật quan trọng:</b> Nếu xâu $S'$ có một palindrome bán kính $m$ (tâm tại $i$, trải về mỗi bên $m$ đơn vị), thì độ dài xâu palindrome tương ứng trong xâu gốc $S$ luôn luôn bằng $m$.
{: .prompt-info}

##### <b>d. Mã giải</b>
```c++
const int limN = 2e5 + 5; // lưu ý: sau khi chèn '#' thì chuỗi mới sẽ có độ dài là 2N
//...
struct string_hashing{
    //...
};
int main(void) {
    init();
    string s; cin >> s;
    string tmp = "#"; // trick chèn ký tự ảo
    for(const char &c: s) {
        tmp.pb(c);
        tmp.pb('#');
    }
    s = tmp;
    int n = sz(s);
    string rev_s = s;
    reverse(all(rev_s));
    s = ' ' + s;
    rev_s = ' ' + rev_s;
    string_hashing hash_forward(s, n), hash_reverse(rev_s, n);
    ll res = -1;
    int best_center = -1;
    FOR(i, 1, n + 1) {
        int l = 0, r = min(i - 1, n - i), best = 0;
        while(l <= r) {
            int m = (l + r) >> 1;
            int rev_l = n - (i + m) + 1;
            int rev_r = n - (i - m) + 1;
            if(hash_forward(i - m, i + m) == hash_reverse(rev_l, rev_r)) {
                best = m;
                l = m + 1;
            } else r = m - 1;
        }
        // độ dài xâu palindrome gốc chính bằng bán kính 'best' trong xâu T
        if(maximize(res, best)) {
            res = best;
            best_center = i;
        };
    }
    if(res != -1) {
        cout << res << nl;
        for(const char &c: s.substr(best_center - res, (res << 1 | 1))) {
            if(c != '#') cout << c;
        }
    }
}
```
- Độ phức tạp: $O((2 \times N) \log (2 \times N))$ $\Rightarrow$ Chỉ phù hợp cho $N \le 10^5$.

- Với bài toán $N$ lên đến hơn $10^6$ thì cần phải dùng <b>thuật toán Manacher</b>.

#### <b>5. Đếm số lượng xâu con đối xứng (Palindrome)</b>

`*Tương tự dạng trước nhưng sự khác biệt ở đây là đếm số lượng xâu con.`

<div class="problem-link">
  🔗 <strong>MARISAOJ - 166</strong>
  <a href="https://marisaoj.com/problem/166" target="_blank">
    Xâu con palindrome 2
  </a>
</div>

Nếu $P[i] = 3$ (ví dụ: `#a#b#a#` tâm b), các bán kính con là 1, 2, 3. Trong đó chỉ có các bán kính ứng với <b>ký tự thật (không phải `#`)</b> mới tạo ra xâu đối xứng ở xâu gốc.

$\Rightarrow \quad$ Cứ mỗi 2 đơn vị bán kính trong $S'$, ta có thêm 1 xâu đối xứng thực sự trong $S$.

> <b>Công thức đếm số lượng xâu con Palindrome (đây cũng là công thức đếm của Manacher)</b>:
{: .prompt-info}

$$\text{Tổng số lượng xâu con Palindrome} = \sum_{i=1}^{\|S'\|} \left\lceil \frac{P[i]}{2} \right\rceil$$

(Với `P[i] = (best trong cnp của mỗi i)` là mảng bán kính xâu đối xứng cho từng vị trí $i$ trong xâu $S'$)

```c++
const int limN = 2e5 + 5; // lưu ý: sau khi chèn '#' thì chuỗi mới sẽ có độ dài là 2N
//...
struct string_hashing{
    //...
};
inline ll ceil_div(ll a, ll b) {
    return a / b + ((a % b != 0) & ((a ^ b) >= 0)); // trick:)
}
int main(void) {
    init();
    string s; cin >> s;
    string tmp = "#"; // trick chèn ký tự ảo
    for(const char &c: s) {
        tmp.pb(c);
        tmp.pb('#');
    }
    s = tmp;
    int n = sz(s);
    string rev_s = s;
    reverse(all(rev_s));
    s = ' ' + s;
    rev_s = ' ' + rev_s;
    string_hashing hash_forward(s, n), hash_reverse(rev_s, n);
    ll res = 0;
    FOR(i, 1, n + 1) {
        int l = 0, r = min(i - 1, n - i), best = 0;
        while(l <= r) {
            int m = (l + r) >> 1;
            int rev_l = n - (i + m) + 1;
            int rev_r = n - (i - m) + 1;
            if(hash_forward(i - m, i + m) == hash_reverse(rev_l, rev_r)) {
                best = m;
                l = m + 1;
            } else r = m - 1;
        }
        // cứ mỗi 2 đv bán kính là 1 xâu đx
        res += ceil_div(best, 2); // or (best + 1) / 2
    }
    cout << res;
}
```
- Như đã nói ở bài toán trên, với bài toán $N$ lên đến hơn $10^6$ thì cần phải dùng <b>thuật toán Manacher</b>.

- Một ưu thế là nếu hiểu tư tưởng của String Hashing trong việc tìm xâu đối xứng thì việc học thuật toán Manacher sẽ hiệu quả hơn.

#### <b>5. Xâu $S$ có chia được thành các từ trong từ điển hay không (Word break)</b>

Thay vì chỉ kiểm tra xem một xâu $S$ có chia được thành các từ trong từ điển hay không, ta phải tìm xâu con dài nhất ở bất kỳ vị trí nào có thể ghép được.


### <b>VIII. Tối ưu quy hoạch động</b>

Thông thường, việc so sánh hai xâu con (substring) độ dài $L$ sẽ tốn thời gian $O(L)$. Khi kết hợp với Quy hoạch động (DP), nếu ta phải thực hiện việc so sánh này lặp đi lặp lại bên trong các vòng lặp, độ phức tạp sẽ bị nhân lên rất lớn. 

$\Rightarrow$ Rolling Hash (Mã băm cuốn) giúp bạn đưa phép so sánh này về $O(1)$.

## <b>C. Thuật toán Manacher</b>

<b>Thuật toán Manacher</b> giúp tìm chuỗi con đối xứng dài nhất với độ phức tạp thời gian là $O(N)$, vượt trội hoàn toàn so với cách mở rộng từ tâm $O(N^2)$ hay String Hashing + Binary Search $O(N \log N)$. Đồng thời cũng là kĩ thuật hỗ trợ tối ưu của mấy bài toán phức tạp liên quan đến xâu đối xứng.

### <b>I. Xử lý đối xứng chẵn lẻ</b>

Vấn đề lớn nhất khi tìm chuỗi đối xứng là sự khác biệt giữa <b>đối xứng chẵn</b> (tâm nằm giữa 2 ký tự, ví dụ `abba`) và <b>đối xứng lẻ</b> (tâm là 1 ký tự, ví dụ `abcba`).

- <b>Như đã đề cập ở `String Hashing`:</b> Thay vì ta xử lý trường hợp palindrome độ dài chẵn (siêu nản) thì ta có thể sử dụng kỹ thuật <b>Chèn ký tự ảo</b> (thường dùng dấu `#`) để ép mọi loại palindrome về dạng lẻ. Từ đó, ta chỉ cần code <b>một hàm duy nhất</b> mà không phải lo phân chia trường hợp.
- <b>Tuy nhiên</b>, để hạn chế bị sai cho out-of-bounds thì ta sẽ tràn thêm 1 kí tự đặc biệt '@' vào cuối xâu, phải khác '#', và kí tự bắt đầu (' ' nếu 1-index)

### <b>II. Bản chất thuật toán</b>

Thuật toán Manacher duy trì một mảng $P$, trong đó $P[i]$ là <b>bán kính</b> của chuỗi đối xứng dài nhất có tâm tại $i$ trên chuỗi $T$.

Thay vì tính $P[i]$ từ con số 0 cho mọi $i$, Manacher tận dụng thông tin từ các bước trước:

- Gọi $C$ (Center) là tâm của chuỗi đối xứng có biên phải $R$ xa nhất mà ta từng tìm được. ($R = C + P[C]$).
- Khi đang tính tại vị trí $i$ ($i > C$), điểm đối xứng của $i$ qua tâm $C$ là $i' = 2C - i$.

<b>Trường hợp 1:</b> Nếu $i < R$, do tính chất đối xứng qua $C$, chuỗi tại $i$ sẽ giống hệt chuỗi tại $i'$. Do đó, khởi tạo $P[i] = \min(R - i, P[i'])$.

<b>Trường hợp 2:</b> Nếu $i \ge R$, ta không có thông tin gì, khởi tạo $P[i] = 0$.

Sau bước khởi tạo cực kỳ ngon này, ta mới dùng vòng lặp while để mở rộng ra 2 bên của $i$. Cuối cùng, cập nhật lại $C$ và $R$ nếu biên phải mới vươn xa hơn $R$ cũ.

### <b>III. Cài đặt</b>

```c++
int main(void) {
    string t; cin >> t;
    string tmp = "#";
    for (const char& c : t) {
        tmp.pb(c);
        tmp.pb('#');
    }
    t = tmp;
    int n = sz(t);
    t = ' ' + t;
    tmp.pb('@'); // ký tự kết thúc khác với ký tự bắt đầu
    vector<int> P(n + 1); // P[i] là bán kính đối xứng tâm i
    int C = 0, R = 0;
    // thuật toán Manacher (tính từ index 1 đến n-2 để bỏ qua @ và $)
    FOR(i, 1, n + 1) {
        int i_mirror = 2 * C - i; // điểm đối xứng của i qua C
        if (i < R) {
            P[i] = min(R - i, P[i_mirror]);
        }
        // mở rộng chuỗi đối xứng với tâm i
        // không sợ out of bound vì t[0] = '@' và t[n-1] = '$'
        while (t[i + 1 + P[i]] == t[i - 1 - P[i]]) {
            P[i]++;
        }
        // cập nhật C và R nếu chuỗi mới vươn xa hơn về bên phải
        if (i + P[i] > R) {
            C = i;
            R = i + P[i];
        }
    }
}
```

### <b>IV. Các dạng toán kinh điển với Manacher</b>

Mảng $P$ thu được chứa toàn bộ "ADN đối xứng" của chuỗi. Dưới đây là cách giải các bài toán kinh điển từ mảng $P$:

#### <b> Dạng 1: Tìm chuỗi con đối xứng dài nhất (Longest Palindromic Substring)</b>
- Cách làm: Tìm giá trị lớn nhất trong mảng $P$, giả sử là $P[max\_i]$.
- Độ dài: Chính là giá trị $P[max\_i]$.
- Vị trí bắt đầu trên chuỗi gốc (1-index): $start = (max\_i - P[max\_i]) / 2$.

#### <b> Dạng 2: Đếm số lượng chuỗi con đối xứng (Palindromic Substrings Count)</b>
- Cách làm: Tại mỗi tâm $i$ trên chuỗi $T$, số lượng chuỗi con đối xứng thực sự trên chuỗi $S$ sinh ra từ tâm này chính là $\lceil P[i] / 2 \rceil$.
- Công thức: $Total = \sum (P[i] / 2)$.

#### <b> Dạng 3: Truy vấn $O(1)$ đoạn $S[L \dots R]$ có phải đối xứng không?</b>
Thay vì dùng Hash, ta dùng Manacher!
- Cách làm: Chuyển đổi tọa độ $L, R$ (1-index trên $S$) sang tọa độ trên $T$.
- Tâm của đoạn $S[L \dots R]$ trên $T$ là: $C_T = L + R + 1$.
- Độ dài đoạn cần kiểm tra là: $Len = R - L + 1$.
- Điều kiện: Nếu $P[C_T] \ge Len$, thì $S[L \dots R]$ là chuỗi đối xứng.

#### <b> Dạng 4: Thêm ít ký tự nhất vào đầu/cuối để thành chuỗi đối xứng (Shortest Palindrome)</b>
- Cách làm (thêm vào đầu): Tìm chuỗi đối xứng dài nhất bắt đầu từ vị trí $S[1]$. Trên mảng $T$, tìm giá trị $P[i]$ sao cho chuỗi này chạm được viền trái của $S$ (tức là `i - P[i] == 1`). Nếu độ dài LPS này là $K$, ta chỉ cần lấy $N - K$ ký tự cuối của $S$, đảo ngược và ném lên đầu.

### <b>V. Tọa độ ảo (Virtual Indexing)</b>

Nếu bài toán giới hạn bộ nhớ cực gắt, việc nhân đôi mảng string $T$ lên $2N + 3$ có thể gây TLE (Time Limit Exceeded) hoặc MLE (Memory Limit Exceeded) khi khởi tạo do cấp phát bộ nhớ động của `string`.

<b>Cách tối ưu (Virtual Indexing)</b>: Không tạo chuỗi $T$ thật, chỉ giả lập nó!

Quy ước: Chuỗi ảo $T$ có chỉ số từ $1 \dots 2N + 1$.

- Nếu $i$ lẻ: Vị trí đó là dấu #.
- Nếu $i$ chẵn: Vị trí đó là $S[i / 2]$ (với $S$ là 1-index).

Ta có thể viết một hàm lấy ký tự ảo (cần thêm check biên thay vì dùng @ và $):

```c++
// Giả sử S là 1-index mảng (s[1...n])
char getChar(int i, const string& S, int n) {
    if (i % 2 != 0) return '#';
    return S[i / 2];
}
```

> Lưu ý: Với cách này, vòng lặp while bên trong cần thêm điều kiện i - 1 - P[i] >= 1 và i + 1 + P[i] <= 2*n + 1 để tránh Out of Bounds, đổi lại bạn tiết kiệm được $O(N)$ bộ nhớ vật lý.
{: .prompt-warning}

## <b>D. Eertree (palindromic tree)</b>

> <b> Xem phần 2 ở đây</b>: [Các thuật toán về xâu kí tự🔐 (Phần 2)](/posts/string-part2/)
{: .prompt-info}