---
layout: post
title: Một số Lý thuyết số học🐧
date: 2026-02-11 14:14 +0700
categories: [number-theory, template]
tags: [tổ hợp, hệ số nhị thức, thuyết đồng dư, nguyên tố]
math: true
---

## <b>Phương pháp phân nhóm theo số chữ số</b>

Dãy số: $1, 2, 3, \dots, 9, 10, 11, \dots, 99, 100, \dots$
- Nhóm 1: Có 9 số có 1 chữ số ($1 \to 9$) $\rightarrow$ $9 \times 1 = 9$ chữ số.
- Nhóm 2: Có 90 số có 2 chữ số ($10 \to 99$) $\rightarrow$ $90 \times 2 = 180$ chữ số.
- Nhóm 3: Có 900 số có 3 chữ số ($100 \to 999$) $\rightarrow$ $900 \times 3 = 2700$ chữ số.

$\Rightarrow \quad$ Tổng quát: Nhóm có $len$ chữ số sẽ có $9 \times 10^{len-1} \times len$ chữ số.

```c++
ll len = 1;      // Số chữ số của các số trong nhóm hiện tại
ll cnt = 9;    // Số lượng số trong nhóm hiện tại (9, 90, 900...)
ll start = 1;    // Số bắt đầu của nhóm (1, 10, 100...)

// Bước 1: Xác định xem n nằm trong nhóm có bao nhiêu chữ số
while (n > len * cnt) {
    n -= len * cnt;
    len++;
    cnt *= 10;
    start *= 10;
}

// Bước 2: Tìm số cụ thể chứa chữ số thứ n
// (n-1) để chuyển về 0-indexed trong phạm vi nhóm
ll target = start + (n - 1) / len;

// Bước 3: Tìm chữ số cụ thể trong target
string s = to_string(target);
cout << s[(n - 1) % len];
```

## <b>Floor/ceil</b>
```c++
// bit trick
// a ^ b >= 0: cùng dấu
// a ^ b < 0: trái dấu

ll floor_div(long long a, long long b) {
    return a / b - ((a % b != 0) & ((a ^ b) < 0));
}

ll ceil_div(long long a, long long b) {
    return a / b + ((a % b != 0) & ((a ^ b) >= 0));
}
```

## <b>Thuật toán Euclid mở rộng</b>
```c++
ll extended_euclid(ll a, ll b, ll &x, ll &y) {
    if(b == 0) {
        x = 1;
        y = 0;
        return a;
    }
    ll x1, y1;
    ll g = extended_euclid(b, a % b, x1, y1);
    x = y1;
    y = x1 - y1 * (a / b);
    return g;
}
```

## <b>Diophinate tìm các nghiệm</b>
```c++
bool exist_solution(ll a, ll b, ll c, ll &x, ll &y, ll &g){
    g = extended_euclid(abs(a), abs(b), x, y);
    if(c % g) return 0;
    x *= c / g;
    y *= c / g;
    if(a < 0) x = -x;
    if(b < 0) y = -y;
    return 1;
}

// x = x0 + b/g
// y = y0 - a/g
ll find_all_solutions(ll a, ll b, ll c, ll minx, ll maxx, ll miny, ll maxy) {
    ll x0, y0, g;
    if(!exist_solution(a, b, c, x0, y0, g)) return 0;
    a /= g; // chuan hoa
    b /= g;

    // x = x0 + k*b  =>  minx <= x0 + k*b <= maxx
    ll kx1, kx2;
    if(b > 0) {
        kx1 = ceil_div(minx - x0, b);
        kx2 = floor_div(maxx - x0, b);
    } else {
        kx1 = ceil_div(maxx - x0, b);
        kx2 = floor_div(minx - x0, b);
    }
    // y = y0 - k*a => miny <= y0 - k*a <= maxy
    // => -miny + y0 >= k*a >= -maxy + y0
    ll ky1, ky2;
    if(a > 0) {
        ky1 = ceil_div(-maxy + y0, a);
        ky2 = floor_div(-miny + y0, a);
    } else {
        ky1 = ceil_div(-miny + y0, a);
        ky2 = floor_div(-maxy + y0, a);
    }
    ll l = max(kx1, ky1);
    ll r = min(kx2, ky2);
    if(l > r) return 0;
    else return r - l + 1;
}
```

## <b>Nghịch đảo modulo</b>
#### TH1: M là số nguyên tố
```c++
ll bin_pow(ll a, ll b, ll m) {
    ll res = 1;
    a = a % m;
    while(b) {
        if(b & 1) res = res * a % m;
        a = a * a % m;
        b >>= 1;
    }
    return res;
}
bin_pow(a, m - 2, m)
```
#### TH2: M là hợp số (Euclid mở rộng)
```c++
ll modinv(ll a, ll m) {
    ll x, y;
    ll g = extended_euclid(a, m, x, y);
    if(g != 1) return -1;
    return (x % m + m) % m;
}
```

## <b>Phi hàm Euler</b>
#### Công thức
$\varphi(p^k)=p^k - p^{k-1}$
```c++
int phi = primes_pw[i] - primes_pw[i] / primes[i];
```
#### Sàng phi
```c++
int phi[limN];
void init() {
    FOR(i, 1, limN) {
        phi[i] = i;
    }
    FOR(i, 2, limN) {
        if(phi[i] == i) { // i là số nguyên tố
            for(int j = i; j < limN; j += i) {
                phi[j] -= phi[j] / i;
            }
        }
    }
}
```


## <b>Định lý Thặng dư Trung Hoa CRT</b>
Giả sử bạn có một hệ gồm $k$ phương trình đồng dư:

$$
\begin{cases}
x \equiv a_1 \pmod{m_1} \\
x \equiv a_2 \pmod{m_2} \\
\dots \\
x \equiv a_k \pmod{m_k}
\end{cases}
$$

Điều kiện bắt buộc: Các module $m_1, m_2, \dots, m_k$ phải <b>đôi một nguyên tố cùng nhau</b>.

Phương trình này luôn có <b>một nghiệm duy nhất modulo $M = m_1 \cdot m_2 \cdots m_k$</b>

Ta có:

$N_i=\frac{M}{m_i}$

$\xrightarrow{\text{    lấy nghịch đảo modulo     } m_i}
N_i^{-1}\pmod{m_i}$

Vậy nghiệm thõa mãn là:

$$x = \left( \sum_{i=1}^{k} N_i \cdot N_i^{-1} \cdot a_i \right) \pmod M$$

<div class="problem-link">
  🔗 <strong>Ví dụ:</strong>
  <a href="https://www.hackerrank.com/challenges/ncr/problem" target="_blank">
    Tính $C_n^k \bmod 142857$ với $0 \le k \le n \le 10^9.$
  </a>
</div>

$142857 = 3 ^ 3 \cdot 11 \cdot 13 \cdot 17$

```c++
const int mod = 142857; // 142857 = 3 ^ 3 * 11 * 13 * 17
int n_primes = 4;
int primes[] = {3, 11, 13, 37};
int primes_pw[] = {27, 11, 13, 37};
int rem[n_primes];

void init(){ // tính trước N_i * N_i^(-1)
    FOR(i, 0, n_primes) {
        int tmp = mod / primes_pw[i]; // N_i
        int phi = primes_pw[i] - primes_pw[i] / primes[i]; // phi Euler
        // tính N_i^(-1) sử dụng định lý Euler
        rem[i] = tmp * bin_pow(tmp, phi - 1, primes_pw[i]) % mod; 
    }
}

ll CRT(ll n, ll k) {
    ll res = 0;
    FOR(i, 0, n_primes) {
        // a_i ở đây là C(n, k, primes_pw[i]): hàm tính nCk modulo primes_pw[i]
        res += C(n, k, primes_pw[i]) * rem[i] % mod;
        res %= mod;
    }
    return res;
}
```

## <b>Công thức Legendre</b>
```c++
// Hàm đếm số mũ của p trong n! (Công thức Legendre)
ll legendre_(ll n, ll p) {
    ll res = 0;
    while (n) { 
        res += n / p; 
        n /= p; 
    }
    return res;
}
```

## <b>Tổ hợp</b>

### Tam giác Pascal (Quy hoạch động)

$C(n, k) = C(n - 1, k - 1) + C(n - 1, k)$

```c++
for (int i = 0; i <= n; i++) {
    C[i][0] = C[i][i] = 1;
    for (int j = 1; j < i; j++) {
        C[i][j] = C[i - 1][j - 1] + C[i - 1][j];
    }
}
```

### Precompute giai thừa
```c++
const int mod = 1e9 + 7;
const int limN = 1e6 + 5;

ll fact[limN], inv_fact[limN];

ll bin_pow(ll a, ll b, ll m) {
    ll res = 1;
    a = a % m;
    while(b) {
        if(b & 1) res = res * a % m;
        a = a * a % m;
        b >>= 1;
    }
    return res;
}

void init() {
    fact[0] = 1;
    FOR(i, 1, limN) {
        fact[i] = fact[i - 1] * i % mod;
    }
    inv_fact[limN - 1] = bin_pow(fact[limN - 1], mod - 2, mod);
    for(int i = limN - 2; i >= 0; i--) {
        inv_fact[i] = inv_fact[i + 1] * (i + 1) % mod; 
    }
}

ll comb(ll n, ll k) {
    if(n < k) return 0;
    return fact[n] * inv_fact[k] % mod * inv_fact[n - k] % mod;
}
```

### Lặp gộp chung tử và mẫu

Ta có: $C_n^i = \frac{n!}{i!(n-i)!}$ và $C_n^{i-1} = \frac{n!}{(i-1)!(n-i+1)!}$

$$\frac{C_n^i}{C_n^{i-1}} = \frac{n!}{i!(n-i)!} \cdot \frac{(i-1)!(n-i+1)!}{n!}$$

$$\frac{C_n^i}{C_n^{i-1}} = \frac{(i-1)!}{i!} \cdot \frac{(n-i+1)!}{(n-i)!} = \frac{1}{i} \cdot (n-i+1)$$

$$C_n^i = \frac{n-i+1}{i} C_n^{i-1}$$

Base case: $C_n^0 = 1$

```c++
ll res = 1;
FOR(i, 1, k + 1) {
    res = res * (n - i + 1) % mod;
    res = res * bin_pow(i, mod - 2, mod) % mod;
}
cout << res;
```

### Định lý Lucas
#### Phát biểu

Cho $p$ là một số nguyên tố, $n$ và $k$ thuộc số nguyên không âm, khi đó:

$$
 \binom{n}{k} \equiv \prod_{i = 0}^{m} \binom{n_i}{k_i}  \text{     (mod p)}
$$

Trong đó:

$$ n = n_mp^m + \dots + n_2p^2 + n_1p^1 + n_0p^0 $$ 

$$ k = k_mp^m + \dots + k_2p^2 + k_1p^1 + k_0p^0 $$ 

Là biểu diễn của n và k trên hệ cơ số p.

Công thức đệ quy:

$$\binom{n}{k} \equiv \binom{\lfloor n/p \rfloor}{\lfloor k/p \rfloor} \cdot \binom{n \bmod p}{k \bmod p} \pmod p$$

```c++
int C(ll n, ll k){...} // hàm tính nCk sử dụng định nghĩa lũy thừa nhị phân
int comb(ll n, ll k){
    if (n < k) return 0;
    ll res = 1;
    while (n > 0 || k > 0){
        ll ni = n % mod, ki = k % mod;
        if(ki > ni) return 0;
        res = res * C(ni, ki) % mod;
        n /= mod; k/= mod;
    }
    return res;
}
```

#### Hệ quả

$\binom{n}{k}$ chia hết cho số nguyên tố $p$ khi và chỉ khi có ít nhất một chữ số $k_i$ của $k$ trong biểu diễn cơ số p lớn hơn chữ số tương ứng $n_i$ của n.

- <b>Giải thích</b>: $k_i > n_i$ thì $\binom{n_i}{k_i} = 0.$ $\Rightarrow  \binom{n}{k} \equiv 0 \text{ (mod m)}$ $\Rightarrow$ $\binom{n}{k}$ $\vdots$    $p$

$\binom{n}{k}$ là một số lẻ khi và chỉ khi trong biểu diễn nhị phân, bất cứ khi nào bit thứ $i$ của $k$ là $1$, thì bit thứ $i$ của $n$ cũng phải là $1$.

- <b>Giải thích</b>: Xét trường hợp $p = 2$. Các chữ số trong biểu diễn cơ số 2 là 0 hoặc 1. Tích $\prod \binom{n_i}{k_i}$ sẽ không phải là 0 (tức bằng 1) khi và chỉ khi các thừa số $\binom{n_i}{k_i} = 1$. Điều này sẽ xảy ra khi không có trường hợp $n_i = 0$ và $k_i = 1$ và ngược lại. Nói cách khác, nếu $k_i = 1$ thì $n_i$ cũng phải bằng 1. Theo logic bit, ta có thể kiểm tra dễ dàng với phép toán `(n & k) == k` hoặc `(k & n) == k`.

### Định lý Mở Rộng Lucas (Kết hợp định lý Wilson)

Khi bạn muốn tính tổ hợp chập $k$ của $n$:

$$\binom{n}{k} = \frac{n!}{k!(n-k)!} \pmod{M}$$

- Nếu <b>$M$ là số nguyên tố</b>: Dễ dàng sử dụng Định lý Lucas nêu trên.

- Nếu <b>$M = p^q$ (lũy thừa của số nguyên tố)</b>: Trong mẫu số $k!(n-k)!$ có thể chứa các thừa số $p$. Khi một số chia hết cho $p$ thì $\gcd(x,p^q) \ne 1 \quad \Rightarrow \quad$ <b>không có nghịch đảo modulo theo modulo $p^q$ </b>. 

Vậy một giải pháp được đề xuất: 

- <b>Rút toàn bộ thừa số $p$ ra khỏi các giai thừa, tính toán với phần còn lại.</b> Vì phần còn lại sẽ nguyên tố cùng nhau với $p$, chúng ta có thể dễ dàng tìm nghịch đảo modulo (modular inverse).

Công thức tổng quát:

$$n! = p^{e(n)} \cdot f(n)$$

Trong đó:

$$\begin{cases} 
e(n) & \text{là số mũ của } p \text{ trong pttsnt của } n! \text{ (Legendre Formula)}\\[4pt]
f(n) & \text{là phần giai thừa sau khi đã loại bỏ tất cả các thừa số } p 
\end{cases}$$

$$C(n,k) = \frac{p^{e(n)} \cdot f(n)}{p^{e(k)} \cdot f(k) \cdot p^{e(n-k)} \cdot f(n-k)} = p^{e(n) - e(k) - e(n-k)} \cdot \frac{f(n)}{f(k) \cdot f(n-k)}$$

Nếu số mũ của $p$ lớn hơn hoặc bằng $q$, tức là:

$$e(n) - e(k) - e(n-k) \ge q$$

Khi đó, ta có thể kết luận ngay:

$$C(n,k) \equiv 0 \pmod{p^q}$$

- <b>Nhân bù số lượng $p$ đã rút ra trở lại, và xử lý dấu (+1/-1).</b>

Khi tính $f(n)$ modulo $p^q$, ta có chia các số thành các chu kỳ (blocks) độ dài $p^q$. Bởi vậy thay vì kiểm tra -1 hay 1 như công thức của Andrew Granville thì ta có thể sử dụng <b>Định lý Wilson mở rộng</b>: 

> Tích của tất cả các số nhỏ hơn $p^q$ và nguyên tố cùng nhau với $p$ sẽ đồng dư với $-1 \pmod{p^q}$ (ngoại trừ trường hợp $p=2, q \ge 3$ thì bằng $1$). 
{: .prompt-info }

$$(-1)^{\lfloor \frac{n}{p^q} \rfloor} \text{ với } \begin{cases} 1 & \text{nếu } p=2, q \ge 3 \\ -1 & \text{còn lại} \end{cases}$$

(Chi tiết này thường xuất hiện khi ta gom nhóm các phần tử để tính $f(n)$).

```c++
const int mod = 27;   // M = p^q (VD: 3^3 = 27)
const int p = 3;  // p
long long fact_exclude[mod];

void init() {
    fact_exclude[0] = 1;
    FOR(i, 1, mod) {
        if(i % p != 0) {
            fact_exclude[i] = (fact_exclude[i - 1] * i) % mod;
        } else {
            fact_exclude[i] = fact_exclude[i - 1];
        }
    }
}

ll mod_inv(ll a, ll m) {
    ll x, y;
    ll g = extended_euclid(a, m, x, y);
    if(g != 1) return -1;
    return (x % m + m) % m;
}

ll legendre_(ll n, ll p) {
    ll res = 0;
    while (n) {
        res += n / p;
        n /= p;
    }
    return res;
}

ll bin_pow(ll a, ll b, ll m) {
    ll res = 1;
    a = a % m;
    while(b) {
        if(b & 1) res = res * a % m;
        a = a * a % m;
        b >>= 1;
    }
    return res;
}

ll extended_wilson(ll n, int p, int mod) {
    if (n == 0) return 1; // 0! = 1

    // số chu kỳ (Blocks)
    ll blocks = n / mod;

    // định lý Wilson Mở rộng (xử lý dấu)
    ll res = bin_pow(fact_exclude[mod - 1], blocks, mod);

    // bù phần lẻ (n % mod) (tức chưa hoàn chỉnh 1 chu kỳ toàn vẹn)
    res = (res * fact_exclude[n % mod]) % mod;

    // bỏ p và đệ quy tiếp
    return (res * extended_wilson(n / p, p, mod)) % mod;
}

ll comb(ll n, ll k, int p) {
    if (n < k) return 0;

    // tìm q sao cho p^q = mod
    int q = 0; int tmp = mod;
    while (tmp % p == 0) {
        q++;
        tmp /= p;
    }
    ll e = legendre_(n, p) - legendre_(k, p) - legendre_(n - k, p); // lấy số lượng p
    if (e >= q) return 0;

    ll A = extended_wilson(n, p, mod);
    ll B = extended_wilson(k, p, mod);
    ll C = extended_wilson(n - k, p, mod);

    ll invB = mod_inv(B, mod);
    ll invC = mod_inv(C, mod);
    if (invB == -1 || invC == -1) return 0;

    ll res = A * invB % mod * invC % mod;
    res = (res * bin_pow(p, e, mod)) % mod; // bù vào lại số lượng p đã rút ra
    return (res + mod) % mod;
}
```