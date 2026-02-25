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

## <b>A. Thuật toán Z & KMP</b>

### <b>I. Thuật toán Z </b>

#### <b> 1. Khái niệm Z-function</b>

Cho một chuỗi $S$ có độ dài $n$ (chỉ số từ $1$ đến $n$).

> Mảng $Z$ của chuỗi $S$ là một mảng cỡ $n$, trong đó $Z[i]$ là <b>độ dài tiền tố chung dài nhất (Longest Common Prefix - LCP)</b> giữa chính chuỗi $S$ và hậu tố của $S$ bắt đầu tại vị trí $i$.
{: .prompt-info}

<i>Nói cách khác:</i> $Z[i]$ là số $k$ lớn nhất sao cho $S[1 \dots k] = S[i \dots i+k-1]$.

<b>Ví dụ:</b> Cho chuỗi $S = \text{abacaba}$ (1-index).

- $Z[1] = 7$ (Toàn bộ chuỗi, đôi khi người ta gán bằng 0 tùy bài toán, nhưng mặc định là $n$).
- $Z[2] = 0$ (Hậu tố bacaba so với abacaba $\rightarrow$ không có tiền tố chung).
- $Z[3] = 1$ (Hậu tố acaba so với abacaba $\rightarrow$ chung chữ a).
- $Z[5] = 3$ (Hậu tố aba so với abacaba $\rightarrow$ chung chuỗi aba).

#### <b>2. Tối ưu bằng Z-box </b>

Cách ngây thơ tốn $O(n^2)$, điều này là quá chậm cho CP. Để tối ưu xuống $O(n)$, chúng ta sử dụng một khái niệm gọi là Z-box (Hộp Z).

<b>Khái niệm Z-box $[L, R]$ </b>

Z-box tận dụng việc "<b>chúng ta đã từng thấy đoạn này ở đâu đó phía trước rồi</b>" để bỏ qua các bước so sánh thừa.

Khi tính toán từ trái sang phải, ta duy trì một đoạn $[L, R]$ <b>với $R$ lớn nhất có thể</b> sao cho đoạn $S[L \dots R]$ khớp hoàn toàn với một tiền tố của $S$. Tức là $S[L \dots R] = S[1 \dots R - L + 1]$.

> Có thể hiểu <b>Z-box</b> như là một cái gương <b>sliding window</b>. Cửa sổ $[L, R]$ bao phủ vị trí $i$, khi bước vào window này ta chỉ đơn giản là soi gương nhìn về phía đầu xâu (vị trí $k$) để <b>xem kết quả có sẵn chưa</b>. Nếu gương không soi hết được đoạn khớp dài hơn, thì ta mới phải đẩy cạnh $R$ của cửa sổ sang phải để khám phá tiếp.
{: .prompt-tip}

Từ đó khi tính $Z[i]$, ta suy ra có <b>2 trường hợp</b>:

> <b>Trường hợp 1: $i > R$ (Vị trí $i$ nằm ngoài Z-box)</b>: <br>- Chúng ta không có thông tin gì từ quá khứ. Bắt buộc phải tính ngây thơ bằng cách so sánh từng ký tự: $S[i]$ với $S[1]$, $S[i+1]$ với $S[2]$,... Sau khi tính được $Z[i]$, nếu đoạn khớp mới này ($i$ đến $i + Z[i] - 1$) nằm xa hơn $R$ cũ, ta cập nhật $L = i$ và $R = i + Z[i] - 1$. Đây là cách chúng ta đẩy Z-box về phía bên phải.<br><b>- Tuy nhiên</b>, trong trường hợp lấy giá trị từ $Z[k]$, nếu đoạn khớp đó chạm đến biên $R$, chúng ta vẫn phải dùng while để xem nó có thể dài hơn nữa không.
- <b>Điều kiện 1:</b> `i + z[i]` <b>(suffix)</b> không vượt quá $N$.
- <b>Điều kiện 2:</b> Ký tự tại `s[1 + z[i]]` <b>(prefix)</b> khớp với `s[i + z[i]]` <b>(suffix)</b>
{: .prompt-info}

> <b>Trường hợp 2: $i \le R$ (Vị trí $i$ nằm gọn trong Z-box)</b>: <br>- Vì $S[L \dots R]$ giống hệt $S[1 \dots R-L+1]$, nên vị trí $i$ trong đoạn $[L, R]$ có một vị trí tương ứng ở đoạn tiền tố $[1, R-L+1]$. <br> <b>- Mirror:</b> Khoảng cách từ $L$ đến $i$ chính bằng khoảng cách từ $1$ đến $k$. $\to$ Do đó: `k = 1 + (i - L)` <br><b>- Công thức tổng quát:</b> `Z[i] = min(R - i + 1, Z[k])`. <br>*( `R - i + 1`: Số lượng ký tự còn lại nằm trong của Z-box)
{: .prompt-info}

<b>Giải thích công thức: $Z[i] = \min(1 + R - i, Z[k])$</b>

<b>TH1: $Z[k]$ ngắn, nằm gọn trong Z-box ($Z[k] < R - i + 1$)</b>
- Nếu tại $k$ chỉ khớp được một đoạn ngắn, thì tại $i$ ta cũng chỉ khớp được đúng bấy nhiêu đó, không cần làm gì thêm. $Z[i] = Z[k]$.

<b>TH2: $Z[k]$ dài, vượt quá Z-box ($Z[k] \ge R - i + 1$)</b>
- Chúng ta chỉ biết chắc chắn là $S[i \dots R]$ khớp với tiền tố. <b>Còn phần sau $R$ thì chưa biết</b> (vì Z-box chỉ bảo kê đến $R$).
- Do đó, ta tạm thời gán $Z[i] = R - i + 1$ (đoạn còn lại trong box), sau đó nhảy ra ngoài $R$ để so sánh thủ công tiếp (giống Trường hợp 1).

```c++
int main(void) {
    string s; cin >> s;
    int n = sz(s);
    s = ' ' + s;
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
        // điều kiện 1: i + z[i] không vượt quá N
        // điều kiện 2: s[1 + z[i]] khớp với s[i + z[i]]
        while (i + z[i] <= n && s[1 + z[i]] == s[i + z[i]]) {
            z[i]++;
        }
        if (i + z[i] - 1 > r) { // cập nhật lại Z-box nếu vượt qua r hiện tại
            l = i;
            r = i + z[i] - 1;
        }
    }
}
```

### <b>II. KMP (Knuth-Morris-Pratt)</b>

#### <b> 1. Ý tưởng cốt lõi của KMP</b>

Khi ta tìm xâu `T` trong xâu `S` bằng cách duyệt trâu thì ta sẽ duyệt từng vị trí, nếu sai thì quay lại từ đầu. KMP giải quyết sự lãng phí này bằng cách: <b>"Khi xảy ra sai khác, ta tận dụng phần đã khớp để biết nên tiếp tục so khớp từ đâu, thay vì quay lại từ đầu."

Để làm được điều này, KMP sử dụng <b>Mảng Tiền Tố (Prefix Array)</b>, thường gọi là mảng `pi` (hoặc `lps` - Longest Proper Prefix which is also Suffix).

#### <b> 2. Mảng $\pi(i)$ </b>

> <b>Định nghĩa:</b> `pi[i]` là <b>độ dài lớn nhất của một xâu vừa là tiền tố vừa là hậu tố</b> của xâu con $T[1...i]$, <b>không tính toàn bộ xâu</b>.
{: .prompt-info}

<b>Ví dụ:</b> Cho xâu `T = " a b a b a c a"` (1-index).
- `pi[1] ('a') = 0` (Tiền tố thực sự không được là chính nó).
- `pi[2] ('ab') = 0` (Không có tiền tố nào giống hậu tố).
- `pi[3] ('aba') = 1` (Tiền tố 'a' giống hậu tố 'a').
- `pi[4] ('abab') = 2` (Tiền tố 'ab' giống hậu tố 'ab').
- `pi[5] ('ababa') = 3` (Tiền tố 'aba' giống hậu tố 'aba').

> <b>Kỹ thuật nhảy lùi:</b> Khi đang so sánh `T[i]` với `T[j+1]` mà chúng khác nhau, thay vì cho `j` về 0, ta lùi `j` về `pi[j]` cho đến khi khớp, nếu vẫn không khớp thì mới cho `j` về 0. 
{: .prompt-info}

- Chúng ta biết rằng hậu tố của đoạn $T[1...j]$ vừa khớp cũng chính là tiền tố của $T$.
- Trong 1-index, `pi[j]` vừa là độ dài, vừa là <b>vị trí kết thúc của tiền tố đó</b>. Do đó, ký tự tiếp theo ta cần so sánh chính là `T[pi[j] + 1]`. Nếu lùi về `j = pi[j]` mà vẫn không khớp ($T[i] \neq T[j+1]$), ta lại tiếp tục lùi về `j = pi[pi[j]]` cho đến khi `j = 0` thì ngừng.

$\Rightarrow$ Vì thế, ta <b>giữ nguyên phần tiền tố dài pi[j] đã chắc chắn đúng</b>, và chỉ việc kiểm tra ký tự tiếp theo ngay sau đó.

```c++
int main(void) {
    string s; cin >> s;
    int n = sz(s);
    s = ' ' + s;
    vector<int> pi(n + 1);
    int j = 0; // j là độ dài tiền tố khớp hiện tại
    FOR(i, 2, n + 1) { // bắt đầu từ 2 vì pi[1] luôn bằng 0
        // nếu không khớp, lùi j về pi[j] cho đến khi khớp hoặc j = 0
        while (j && s[i] != s[j + 1]) j = pi[j];    
        if (s[i] == s[j + 1]) j++; // nếu khớp, tăng độ dài j lên 1
        pi[i] = j;
    }
}
```
#### <b> 3. Thuật toán KMP </b>

Khi đã có mảng `pi` của xâu mẫu `P`, ta áp dụng logic y hệt để duyệt qua xâu văn bản `T`.

```c++

int main(void) {
    string s, t; cin >> s >> t;
    int n = sz(s), m = sz(t);
    s = ' ' + s, t = ' ' + t;
    vector<int> pi(m + 1);
    int j = 0;
    FOR(i, 2, m + 1) {
        while (j && t[i] != t[j + 1]) j = pi[j];
        if (t[i] == t[j + 1]) j++;
        pi[i] = j;
    }
    j = 0;
    int res = 0;
    FOR(i, 1, n + 1) {
        if (j && s[i] != t[j + 1]) j = pi[j];
        if (s[i] == t[j + 1]) j++;
        if (j == m) {
            res++; // tìm thấy T trong S tại vị trí i - m + 1;
            j = pi[j]; // vì có thể giao nhau, tiếp tục nhảy
        }
    }
    cout << res;
}
```

##### <b>Kỹ thuật nối chuỗi</b>

Thay vì phải viết thêm một hàm riêng biệt thì ta có thể sử dụng <b>kỹ thuật nối chuỗi.</b>

> $C = T$ + # + $S$ (Ký tự # là một ký tự không bao giờ xuất hiện trong cả T và S).
{: .prompt-tip}

- Bởi vì # không xuất hiện trong $T$ và $S$, nên giá trị `pi[i]` <b>không bao giờ vượt quá $\|T\|$</b>.
- Nếu không có #, khi ta tính `pi` trên chuỗi $T+S$, tiền tố có thể <b>tràn</b> từ đoạn $T$ sang đoạn $S$, làm kết quả bị sai lệch. Với #, mọi nỗ lực khớp tiền tố dài hơn $\|T\|$ đều sẽ thất bại ngay lập tức tại vị trí của ký tự #.

```c++

int main(void) {
    string s, t; cin >> s >> t;
    string c = t + '#' + s;
    int n = sz(c);
    c = ' ' + c;
    vector<int> pi(n + 1);
    int j = 0;
    int res = 0;
    FOR(i, 2, n + 1) {
        while (j && c[i] != c[j + 1]) j = pi[j];
        if (c[i] == c[j + 1]) j++;
        pi[i] = j;
        if (j == sz(t)) {
            res++;
        }
    }
    cout << res;
}
```

<div class="problem-link">
  🔗 <strong>CSES - String Functions</strong>
  <a href="https://lqdoj.edu.vn/problem/cses2107" target="_blank">
    Các hàm của xâu
  </a>
</div>

## <b>B. String Hashing và Rabin-karp</b>
### <b>I. Bản chất String Hashing</b>

Đôi khi ta phải so sánh hai đoạn văn bản rất dài xem có giống nhau không. Nếu so sánh từng chữ cái, độ phức tạp sẽ là $O(N)$, rất chậm. Đặc biệt khi kết hợp với Quy hoạch động, nếu ta phải thực hiện việc so sánh này lặp đi lặp lại bên trong các vòng lặp, độ phức tạp sẽ bị nhân lên rất lớn. 

<b>Ý tưởng của Hash:</b> Biến mỗi xâu ký tự thành một <b>con số duy nhất</b>. Khi đó, để so sánh hai xâu, ta chỉ cần so sánh hai con số trong thời gian $O(1)$. Kỹ thuật phổ biến nhất trong CP là <b>Polynomial Rolling Hash</b> (Hash đa thức).

Hãy tưởng tượng xâu của chúng ta giống như một con số ở <b>hệ cơ số $base$</b> (với $base$ là một số nguyên dương, dùng $base = 911$ cho bảng mã ASCII vì nó dễ nhớ :v).

Ví dụ xâu $S = \text{"abc"}$ ở hệ cơ số $base$ sẽ có giá trị:

$$H(\text{"abc"}) = (\text{'a'} \cdot base^2 + \text{'b'} \cdot base^1 + \text{'c'} \cdot base^0)$$

Vì độ dài xâu có thể lên tới $10^5$, con số này sẽ cực kỳ khổng lồ và tràn mọi kiểu dữ liệu. Do đó, ta cần thực hiện phép tính chia lấy dư cho một số modulo $m$ đủ lớn (thường là $10^9+7$).

#### <b>a. Xây dựng mảng Prefix Hash (Hash tiền tố)

Để lấy mã Hash của bất kỳ đoạn con nào trong $O(1)$, ta cần xây dựng mảng $h$ lưu giá trị hash của các tiền tố từ $1$ đến $i$. (Nên dùng chỉ số bắt đầu từ $1$ để dễ code).

> <b>Công thức quy hoạch động tính Prefix Hash:</b> <br> $$h[i] = (h[i-1] \cdot base + S[i]) \pmod m$$
{: .prompt-info}

#### <b>b. Hash đoạn con</b>

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

### <b>II. Double Hash</b>

Vấn đề: Trong các kỳ thi lớn, người ta thường tạo ra các test <b>Anti-Hash</b> (Nghịch lý ngày sinh - Birthday Paradox). Nếu ta chỉ dùng 1 bộ $(base, m)$ như $base=311, m=10^9+7$, chắc chắn sẽ có 2 xâu khác nhau nhưng <b>sinh ra cùng 1 số Hash</b> $\rightarrow$ Code chạy sai nộp lên bị WA.

Giải pháp: Dùng <b>Double Hash</b>. Ta tính Hash đồng thời trên 2 modulo khác nhau (ví dụ $m_1 = 10^9+7$ và $m_2 = 10^9+9$). Hai xâu chỉ được coi là giống nhau nếu <b>cả hai giá trị Hash đều bằng nhau</b>. Xác suất đụng độ lúc này giảm xuống chỉ còn khoảng $\frac{1}{m_1 \cdot m_2} \approx 10^{-18}$, an toàn tuyệt đối.

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
            dp1[i] = dp1[i - 1] * base % mod1 + (s[i] - 'a' + 1);
            dp1[i] %= mod1;
            dp2[i] = dp2[i - 1] * base % mod2 + (s[i] - 'a' + 1);
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

### <b>III. Thuật toán Rabin-karp</b>

Về bản chất, <b>Rabin-Karp</b> không phải là một cách Hash mới, mà nó là <b>thuật toán tìm kiếm xâu ứng dụng kỹ thuật Rolling Hash</b> mà chúng ta đã được xem ở trên.

<b>Mục tiêu cơ bản nhất của Rabin-Karp:</b> Tìm số lần xuất hiện (hoặc các vị trí xuất hiện) của một xâu mẫu (Pattern - độ dài $M$) trong một xâu văn bản (Text - độ dài $N$).

>Thay vì so sánh từng ký tự của xâu mẫu với từng đoạn con của xâu văn bản ($O(N \cdot M)$), Rabin-Karp sử dụng một <b>sliding window có kích thước bằng với độ dài xâu mẫu $M$</b>.
1. Tính mã Hash của xâu mẫu $P$.
2. Tính mã Hash của cửa sổ trượt đầu tiên trên xâu $T$ (từ vị trí $1$ đến $M$).
3. Trượt cửa sổ đi $1$ đơn vị. Sử dụng Rolling Hash để lấy/cập nhật mã Hash của cửa sổ mới trong thời gian $O(1)$.
4. So sánh mã Hash của cửa sổ hiện tại với mã Hash của xâu mẫu. Nếu bằng nhau $\rightarrow$ Cập nhật kết quả.
{: .prompt-tip}

```c++
int main(void) {
    init();
    string text, pattern; cin >> text >> pattern;
    int n = sz(text), m = sz(pattern);
    text = ' ' + text;
    pattern = ' ' + pattern;
    // nếu xâu mẫu dài hơn xâu gốc thì chắc chắn không xuất hiện
    if (n < m) return cout << 0, 0;
    // khởi tạo bảng Hash cho text và pattern
    string_hashing hash_text(text, n);
    string_hashing hash_pattern(pattern, m);
    // lấy mã Hash của toàn bộ xâu Pattern
    ii target_hash = hash_pattern.query(1, m);
    int cnt = 0;
    vector<int> pos;
    // trượt cửa sổ độ dài m trên xâu text
    // i chạy từ 1 đến vị trí cuối cùng có thể đặt vừa xâu Pattern
    FOR(i, 1, n - m + 1 + 1) {
        int j = i + m - 1;
        if (hash_text.query(i, j) == target_hash) {
            cnt++;
            pos.eb(i);
        }
    }
    cout << cnt << nl; // in số lượng khớp
    if (cnt) for (const int& x : pos) cout << x << ' '; // in các vị trí
}
```

Đôi khi Rabin-karp được ưu chuộng hơn cả thuật toán KMP, nó tối ưu và ứng dụng cho các dạng bài sau:

- <b>String Matching cơ bản:</b> Tìm xâu con, đếm số lần xuất hiện của xâu mẫu trong xâu gốc ($O(N+M)$).

- <b>Tìm kiếm nhiều xâu mẫu cùng lúc:</b> Nếu ta có nhiều xâu mẫu cùng độ dài $M$, ta chỉ cần băm tất cả chúng bỏ vào một `unordered_set` (hoặc mảng rồi sort/binary search). Sau đó trượt cửa sổ trên xâu Text để check xem Hash của đoạn hiện tại có nằm trong Set không.

- <b>Tìm xâu con chung dài nhất (Longest Common Substring):</b> Kết hợp Hash với Tìm kiếm nhị phân (Binary Search) độ dài đáp án. Độ phức tạp $O(N \log N)$, cực kỳ bá đạo mà code lại siêu ngắn so với Suffix Automaton hay Suffix Array.

- <b>Xâu đối xứng (Palindrome):</b> Hash xâu gốc từ trái sang phải, và Hash xâu đảo ngược từ phải sang trái. Một đoạn $(l, r)$ là đối xứng nếu Hash xuôi bằng Hash ngược. Chạy trong $O(1)$ cho mỗi truy vấn.

- <b>2D Rabin-Karp (Hash Ma trận):</b> Nâng cấp lên không gian 2 chiều để tìm ma trận con $M \times M$ trong ma trận lớn $N \times N$. Đây là thứ mà KMP không thể làm dễ dàng được.

### <b>IV. LCP (Longest Common Prefix)</b>

#### <b>1. Khái niệm</b>

LCP (Longest Common Prefix) là <b>Tiền tố chung dài nhất</b>.

<b>Ví dụ:</b> `S1 = "aabac"` và `S2 = "aabxy"`.

Phần giống nhau tính từ đầu của 2 xâu là `"aab"`, có độ dài là 3. Vậy LCP của chúng bằng 3.

<b>Quy tắc so sánh từ điển:</b>

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
            if(query(a, a + m - 1) == query(c, c + m - 1)) {
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

1. <b>Chặt nhị phân theo độ dài xâu con chung:</b> * Nếu có một xâu con chung độ dài $len$, thì chắc chắn cũng tồn tại xâu con chung độ dài $L-1$.
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


## <b>C. Thuật toán Manacher</b>

<b>Thuật toán Manacher</b> giúp tìm chuỗi con đối xứng dài nhất với độ phức tạp thời gian là $O(N)$, vượt trội hoàn toàn so với cách mở rộng từ tâm $O(N^2)$ hay String Hashing + Binary Search $O(N \log N)$. Đồng thời cũng là kĩ thuật hỗ trợ tối ưu của mấy bài toán phức tạp liên quan đến xâu đối xứng.

### <b>I. Xử lý đối xứng chẵn lẻ</b>

Vấn đề lớn nhất khi tìm chuỗi đối xứng là sự khác biệt giữa <b>đối xứng chẵn</b> (tâm nằm giữa 2 ký tự, ví dụ `abba`) và <b>đối xứng lẻ</b> (tâm là 1 ký tự, ví dụ `abcba`).

- <b>Như đã đề cập ở `String Hashing`:</b> Thay vì ta xử lý trường hợp palindrome độ dài chẵn (siêu nản) thì ta có thể sử dụng kỹ thuật <b>Chèn ký tự ảo</b> (thường dùng dấu `#`) để ép mọi loại palindrome về dạng lẻ. Từ đó, ta chỉ cần code <b>một hàm duy nhất</b> mà không phải lo phân chia trường hợp.
- <b>Tuy nhiên</b>, để hạn chế bị sai cho out-of-bounds thì ta sẽ tràn thêm 1 kí tự đặc biệt '@' vào cuối xâu, phải khác '#', và kí tự bắt đầu (' ' nếu 1-index)

### <b>II. Bản chất thuật toán</b>

> Duy trì một mảng $P[i]$ là <b>bán kính của chuỗi đối xứng dài nhất có tâm tại $i$</b> trên chuỗi $T$. <br>Thay vì tính $P[i]$ từ con số 0 cho mọi $i$, <b>Manacher tận dụng thông tin từ các bước trước</b>:
- Gọi $C$ (Center) là tâm của chuỗi đối xứng có biên phải $R$ xa nhất mà ta từng tìm được. ($R = C + P[C]$).
- Khi đang tính tại vị trí $i$ ($C < i$), điểm đối xứng của $i$ qua tâm $C$ là $i' = 2C - i$.
{: .prompt-info}

> <b>Trường hợp 1:</b> Nếu $i < R$, do tính chất đối xứng qua $C$, chuỗi tại $i$ sẽ giống hệt chuỗi tại $i'$. Do đó, khởi tạo $P[i] = \min(R - i, P[i'])$.
{: .prompt-info}

Khi $i$ nằm trong phạm vi của $R$, nó đang được bao phủ bởi một chuỗi đối xứng lớn hơn có tâm $C$. Do đó, phần bên phải của $C$ (chứa $i$) phải là hình ảnh phản chiếu của phần bên trái (chứa $i'$).

- Nếu $P[i']$ nhỏ hơn khoảng cách tới biên ($P[i'] < R - i$): Chuỗi đối xứng tại $i$ nằm hoàn toàn trong chuỗi đối xứng tại $C$. Ta lấy luôn giá trị $P[i']$.

- Nếu $P[i']$ lớn hơn hoặc bằng biên ($P[i'] \ge R - i$): Ta chỉ chắc chắn được rằng chuỗi tại $i$ đối xứng ít nhất cho đến biên $R$. Phần ngoài $R$ chưa được khám phá, nên ta tạm thời dừng lại ở $R-i$ và sẽ kiểm tra tiếp bằng vòng lặp while.

> <b>Trường hợp 2:</b> Nếu $i \ge R$, ta không có thông tin gì, khởi tạo $P[i] = 0$.
{: .prompt-info}

Sau bước khởi tạo này, ta mới dùng vòng lặp `while` để mở rộng ra 2 bên của $i$. Cuối cùng, cập nhật lại $C$ và $R$ nếu biên phải mới vươn xa hơn $R$ cũ. 

$\Rightarrow$ Tư tưởng khá là giống `Thuật toán Z`.

| Đặc điểm | Z-Algorithm | Manacher |
|---|---|---|
| **Mục đích** | Tìm khớp tiền tố dài nhất <br>giữa $S[i..n]$ và $S[1..n]$. | Tìm bán kính đối xứng <br>dài nhất tại mỗi tâm $i$. |
| **Vùng an toàn** | Một đoạn $[l, r]$ mà $S[l..r]$ <br>khớp $S[1..r-l+1]$. | Một đoạn $[c-p[c],\; c+p[c]]$ <br>là một palindrome. |
| **Cách tính biên $r$** | Inclusive (Đóng): $r$ là vị trí <br>cuối cùng thuộc vùng khớp. | Exclusive (Mở): $r$ là vị trí <br>ngay sau biên phải của palindrome. |
| **Độ dài an toàn** | $\min(r - i + 1,\; z[1+i-l])$ | $\min(r - i,\; p[2c - i])$ |
| **Gương chiếu** | $k = 1 + i - l$ (vị trí tương ứng ở tiền tố). | $i' = 2c - i$ (vị trí đối xứng qua tâm $c$). |
| **Tiền xử lý** | Tùy bài toán | Chèn `#` để đưa mọi<br> palindrome về độ dài lẻ. |
| **Lính canh** | `s = ' ' + s` (1-index) | `s= ' ' + s + '@'`|

### <b>III. Cài đặt</b>

```c++
int main(void) {
    string s; cin >> s;
    string tmp = "#";
    for (const char& c : s) {
        tmp.pb(c);
        tmp.pb('#');
    }
    s = tmp;
    int n = sz(s);
    s = ' ' + s + '@';
    vector<int> p(n + 1);
    int c = 0, r = 0;
    FOR(i, 1, n + 1) {
        if (i < r) {
            p[i] = min(r - i, p[(c << 1) - i]);
        }
        while (s[i + p[i] + 1] == s[i - p[i] - 1]) {
            p[i]++;
        }
        if (i + p[i] > r) {
            c = i;
            r = i + p[i];
        }
    }
    // in kết quả
    int res = 0, center = 0;
    FOR(i, 1, n + 1) {
        if (maximize(res, p[i])) {
            center = i;
        }
    }
    FOR(i, center - res, center + res + 1) {
        if (s[i] != '#' && s[i] != ' ' && s[i] != '@') {
            cout << s[i];
        }
    }
}
```

### <b>IV. Trick tọa độ ảo (Virtual Indexing)</b>

Nếu bài toán giới hạn bộ nhớ cực gắt, việc nhân đôi mảng string $T$ lên $2N + 3$ có thể gây TLE (Time Limit Exceeded) hoặc MLE (Memory Limit Exceeded) khi khởi tạo do cấp phát bộ nhớ động của `string`.

><b>Cách tối ưu (Virtual Indexing)</b>: Không tạo chuỗi $T$ thật, chỉ giả lập nó
Quy ước: Chuỗi ảo $T$ có chỉ số từ $1 \dots 2N + 1$.
- <b>Nếu $i$ lẻ:</b> Vị trí đó là dấu #.
- <b>Nếu $i$ chẵn:</b> Vị trí đó là $S[i / 2]$ (với $S$ là 1-index).
{: .prompt-tip}

Ta có thể viết một hàm lấy ký tự ảo:

```c++
// Giả sử S là 1-index mảng (s[1...n])
char getChar(int i, const string& S, int n) {
    if (i % 2 != 0) return '#';
    return S[i / 2];
}
```

> Lưu ý: Với cách này, vòng lặp while bên trong cần thêm điều kiện $i - 1 - P[i] >= 1$ và $i + 1 + P[i] <= 2*n + 1$ để tránh Out of Bounds, đổi lại ta tiết kiệm được $O(N)$ bộ nhớ vật lý.
{: .prompt-warning}

## <b>D. Một số bài toán xâu ký tự kinh điển</b>

### <b>Dạng 1: Đếm số lần chuỗi $T$ xuất hiện trong chuỗi $S$</b>

<div class="problem-link">
  🔗 <strong>CSES - String Matching</strong>
  <a href="https://lqdoj.edu.vn/problem/cses1753" target="_blank">
    Khớp xâu
  </a>
</div>

> <b>Bài toán:</b> Cho 2 xâu $S$ và $T$. Đếm số lần xuất hiện của $T$ trong $S$ (có bao nhiêu xâu con của $S$ bằng $T$).
{: .prompt-info}


| Thuật toán | Độ phức tạp thời gian | Không gian bộ nhớ | Đánh giá |
|------------|----------------------|-------------------| ----------|
| **Z Algorithm** | `O(N + M)` | `O(N + M)` | 🥇 |
| **KMP** | `O(N + M)` | `O(M)` | 🥈 |
| **String Hashing** | `O(N + M)` | `O(N + M)` | 🥉 |
| **Rabin–Karp** | Trung bình: `O(N + M)`<br>Tệ nhất: `O(N × M)` | `O(1)` hoặc `O(M)` | 🥉 |

### <b>Dạng 2: Tìm chu kỳ ngắn nhất của chuỗi</b>

<div class="problem-link">
  🔗 <strong>CSES - Finding Periods</strong>
  <a href="https://lqdoj.edu.vn/problem/cses1733" target="_blank">
    Tìm chu kì
  </a>
</div>

> <b>Bài toán:</b> Cho xâu $S$ độ dài $n$ chỉ chứa các chữ cái tiếng Anh viết thường. Tìm xâu $T$ ngắn nhất sao cho khi viết lặp lại $T$ cho đến khi đủ $n$ kí tự thì được xâu $S$.
{: .prompt-info}

> <b>Hướng dẫn:</b> <br> <b>Tính chất chu kỳ:</b> Xâu $S$ có một chu kỳ độ dài $len$ khi và chỉ khi <b>tiền tố độ dài `n - len` bằng hậu tố độ dài `n - len` $\Rightarrow S[1..n - len] = S[len + 1...n]$</b>.
{: .prompt-tip}

<b>Chứng minh tính chất chu kỳ:</b>

Giả sử xâu $T$ có độ dài là `len`. Khi lặp lại $T$ để tạo ra $S$, điều này có nghĩa là ký tự tại vị trí $i$ phải giống ký tự tại vị trí $i + len$:

$$S[i] = S[i+len] \text{ với mọi } 1 \le i \le n-len$$

Ta xét các cặp kí tự sau:

- $S[1] = S[1+len]$
- $S[2] = S[2+len]$
...
- $S[n-len] = S[(n-len)+len] = S[n]$

Gom các vế trái và vế phải lại:

- Vế trái: $\{S[1], S[2], \dots, S[n-len]\}$. Đây chính là <b>Tiền tố</b> độ dài $n-len$.
- Vế phải: $\{S[1+len], S[2+len], \dots, S[n]\}$. Đây chính là <b>Hậu tố</b> độ dài $n-len$.

$\Rightarrow \quad$ Điều phải chứng minh.

<b>Ví dụ:</b> sussussu ($n=8$):
- Thử với $len=3$ (xâu $T$ là sus):
- Độ dài tiền tố/hậu tố cần check: $K = n - len = 8 - 3 = 5$.
- Tiền tố độ dài 5: sussu
- Hậu tố độ dài 5: sussu
- Vì Tiền tố == Hậu tố nên $len=3$ là một chu kỳ hợp lệ.

| Thuật toán | Độ phức tạp thời gian | Không gian bộ nhớ | Đánh giá |
|------------|----------------------|-------------------| ----------|
| **Z Algorithm** | $O(N)$ | $O(N)$ | 🥇 |
| **KMP** | $O(N)$ | $O(N)$ | 🥉 |
| **String Hashing** | $O(N \log N)$ hoặc $O(N)$ | $O(N)$ | 🥉 |

### <b>Dạng 3: Tìm các biên của chuỗi</b>

<div class="problem-link">
  🔗 <strong>CSES - Finding Borders</strong>
  <a href="https://lqdoj.edu.vn/problem/cses1732" target="_blank">
    Tìm biên
  </a>
</div>

> <b>Bài toán:</b> Tìm tất cả các độ dài $L$ sao cho tiền tố độ dài $L$ hoàn toàn khớp với hậu tố độ dài $L$ của chuỗi $S$.
{: .prompt-info}

| Thuật toán | Độ phức tạp thời gian | Không gian bộ nhớ | Đánh giá |
|------------|----------------------|-------------------| ----------|
| **Z Algorithm** | $O(N)$ | $O(N)$ | 🥈 |
| **KMP** | $O(N)$ | $O(N)$ | 🥇 |
| **String Hashing** | $O(N)$ | $O(N)$ | 🥈 |

### <b>Dạng 4: Chuỗi lặp lại dài nhất</b>

<div class="problem-link">
  🔗 <strong>CSES - Repeating Substring</strong>
  <a href="https://lqdoj.edu.vn/problem/cses2106" target="_blank">
    Tìm biên
  </a>
</div>

> <b>Bài toán:</b> Tìm chuỗi con dài nhất xuất hiện ít nhất 2 lần trong $S$ (các lần xuất hiện có thể đè lên nhau).
{: .prompt-info}

| Thuật toán | Độ phức tạp thời gian | Không gian bộ nhớ | Đánh giá |
|------------|----------------------|-------------------| ----------|
| **Z Algorithm** | $O(N^2)$ | $O(N)$ | 🥉 |
| **KMP** | $O(N^2)$ | $O(N)$ | 🥉 |
| **String Hashing** | $O(N \log N)$ | $O(N)$ | 🥇 |
| **(Mở rộng) Suffix Automaton** | $O(N)$ | $O(N \times \Sigma)$ | 🥇🥇 |

### <b>Dạng 5: Tìm chuỗi con đối xứng dài nhất</b>

<div class="problem-link">
  🔗 <strong>CSES - Longest Palindrome</strong>
  <a href="https://lqdoj.edu.vn/problem/cses1111" target="_blank">
    Xâu đối xứng dài nhất
  </a>
</div>

> <b>Bài toán:</b> Cho một chuỗi $S$, tìm chuỗi con liên tiếp dài nhất là một Palindrome.
{: .prompt-info}

| Thuật toán | Độ phức tạp thời gian | Không gian bộ nhớ | Đánh giá |
|------------|----------------------|-------------------| ----------|
| **Z Algorithm** | $O(N^2)$ | $O(N)$ | 🥉 |
| **KMP** | $O(N^2)$ | $O(N)$ | 🥉 |
| **String Hashing** | $O(N \log N)$ | $O(N)$ | 🥈 |
| **Manacher** | $O(N)$ | $O(N)$ | 🥇🥇 |

### <b>Dạng 6: Truy vấn Palindrome động</b>

<div class="problem-link">
  🔗 <strong>CSES - Palindrome Queries</strong>
  <a href="https://lqdoj.edu.vn/problem/cses2420" target="_blank">
    Truy vấn xâu đối xứng
  </a>
</div>

> <b>Bài toán:</b> Cho chuỗi $S$ độ dài $N$ và $K$ truy vấn gồm 2 loại: Cập nhật ký tự tại vị trí $x$, hoặc Kiểm tra xem đoạn $[L, R]$ có phải là Palindrome không.
{: .prompt-info}

| Thuật toán | Độ phức tạp thời gian | Không gian bộ nhớ | Đánh giá |
|------------|----------------------|-------------------| ----------|
| **Z Algorithm** |  |  | 💀 |
| **KMP** |  |  | 💀 |
| **String Hashing** | $O((N + K) \log N)$ | $O(N)$ | 🥇🥇 |
| **Manacher** |  |  | 💀 |

### <b>Dạng 7: Tìm chuỗi dịch vòng có thứ tự từ vựng nhỏ nhất</b>

<div class="problem-link">
  🔗 <strong>CSES - Minimal Rotation</strong>
  <a href="https://lqdoj.edu.vn/problem/cses1110" target="_blank">
    Vòng quay nhỏ nhất
  </a>
</div>

> <b>Bài toán:</b> Cho chuỗi $S$, bạn có thể cắt một số ký tự ở đầu và ghép xuống cuối. Hãy tìm cách cắt sao cho chuỗi thu được có thứ tự từ vựng nhỏ nhất. Kỹ thuật chung là nhân đôi chuỗi gốc thành $S + S$ (độ dài $2N$) và tìm chuỗi con độ dài $N$ nhỏ nhất.
{: .prompt-info}

| Thuật toán | Độ phức tạp thời gian | Không gian bộ nhớ | Đánh giá |
|------------|----------------------|-------------------| ----------|
| **Z Algorithm** | $O(N \log N)$ | $O(N)$ | 🥉 |
| **KMP** | $O(N)$ | $O(N)$ | 🥇 |
| **String Hashing** | $O(N \log N)$ | $O(N)$ | 🥈 |

### <b>Dạng 8: Kiểm tra sự xuất hiện của nhiều chuỗi mẫu</b>

<div class="problem-link">
  🔗 <strong>CSES - Finding Patterns</strong>
  <a href="https://lqdoj.edu.vn/problem/cses2102" target="_blank">
    Tìm xâu con
  </a>
</div>

> <b>Bài toán:</b> Cho chuỗi văn bản $S$ độ dài $N$ và $K$ chuỗi mẫu. Tổng độ dài của $K$ chuỗi mẫu là $M$. Cần kiểm tra xem mỗi chuỗi mẫu có xuất hiện trong $S$ hay không.
{: .prompt-info}

| Thuật toán | Độ phức tạp thời gian | Không gian bộ nhớ | Đánh giá |
|------------|----------------------|-------------------| ----------|
| **Z Algorithm** | $O(K \times N + M)$ | $O(M)$ | 🥉 |
| **KMP** | $O(K \times N + M)$ | $O(M)$ | 🥉 |
| **String Hashing** | $O(N \log N + M)$ hoặc $O(K \times N)$| $O(N + M)$ | 🥉 |
| **Rabin-Karp** | $O(N + M)$ | $O(M)$ | 🥈 |
| **Aho-Corasick** |  |  | 🥇 |
| **Suffix Automaton** |  |  | 🥇 |

### <b>Dạng 9: So khớp chuỗi kết hợp Quy hoạch động (String Automaton)</b>

<div class="problem-link">
  🔗 <strong>CSES - Required Substring</strong>
  <a href="https://lqdoj.edu.vn/problem/cses1112" target="_blank">
    Xâu con bắt buộc
  </a>
</div>

> <b>Bài toán:</b> Tính số lượng chuỗi có độ dài chính xác là $N$ được tạo từ các ký tự (ví dụ A-Z), sao cho chuỗi đó chứa chuỗi $T$ (độ dài $M$) như một chuỗi con. Vì kết quả rất lớn nên in ra theo module $10^9+7$.
{: .prompt-info}

| Thuật toán | Độ phức tạp thời gian | Không gian bộ nhớ | Đánh giá |
|------------|----------------------|-------------------| ----------|
| **Z Algorithm** |  |  | 💀 |
| **KMP** | $O(N \times M \times \Sigma)$ | $O(M \times \Sigma)$ | 🥉 |
| **String Hashing** | |  | 💀 |

- Mảng Z không thể hiện tốt sự chuyển trạng thái từng bước (state transition) như mảng $\pi$ của KMP, nên rất khó xây dựng đồ thị chuyển trạng thái cho DP.

- Hashing dùng để kiểm tra tĩnh, không thể dự đoán việc thêm/bớt ký tự sẽ tạo ra chuỗi hợp lệ như thế nào trong bài toán đếm tổ hợp.

> <b> Xem phần 2 ở đây</b>: [Các thuật toán về xâu kí tự🔐 (Phần 2)](/posts/string-part2/)
{: .prompt-info}