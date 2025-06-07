---
layout: post
title: Sốc!!🤯 Thuật toán chuyển đổi thời gian hiệu quả trong O(1)⌛
date: 2025-06-04 09:42 +0700
categories: [thuật toán]
tags: [thời gian, lịch, date]
math: true
---
Xin chào các bạn!!! Đây là blog đầu tiên của mình nên có thể mình viết chưa được chuyên nghiệp cho lắm, mọi góp ý, sai sót sẽ được mình tiếp nhận để mình cải thiện trong việc viết blog ^^

![Lịch Gregorius](/assets/img/Gregorian Calendar.jpg)
_Lịch Gregorius (hệ thống lịch của thế giới ngày nay)_

Và sau đây mình xin được giới thiệu với các bạn một thuật toán rất độc lạ, song mạnh mẽ và hiệu quả trong việc xử lý ngày tháng mà hầu như mình chưa thấy nguồn tiếng Việt nào nhắc tới. Với tên gọi <b>"Thuật toán GDate"</b> - Thuật toán ban đầu được gợi ý bởi Gary Katch và đã được sử dụng rộng rãi trong các thư viện xử lý ngày tháng.

| Ưu điểm                                                                           |
| :-------------------------------------------------------------------------------- |
| Xử lý nhanh các bài toán liên quan đến lịch, thời gian, ngày, tháng, ... trong $O(1)$         |
| Rất mạnh mẽ và mang tính ứng dụng thực tiễn cao (lập trình, game, hệ thống, mô phỏng...)     |

| Nhược điểm |
| :----------|
| Rất khó để cài đặt nếu không hiểu bản chất |

## Bối cảnh

Để thực hiện các phép tính ngày tháng, dầu tiên ta phải biến đổi lịch $(d, m, y)$ thành một số nguyên đại diện cho ngày. Nếu chúng ta chọn <b>ngày 0</b> làm ngày cơ sở hợp lý thì việc tính toán sẽ trở nên rất dễ dàng. Trong thuật toán GDate, ngày cơ sở được chọn là <b>ngày 1 tháng 3 của năm 0</b>.

> Lưu ý rằng năm 0 này không phải là năm 0 trong lịch Gregorius thực, mà chỉ là <b>một mốc tham chiếu</b>. Tuy nhiên, các phép tính liên quan đến những ngày sau và trước khi thiết lập lịch Gregorius vẫn sẽ trả vể kết quả chính xác.
{: .prompt-warning }

## Tính toán năm

Độ dài trung bình của một năm chí tuyến được đo lường <b>$\approx$365.2422 ngày</b>. Nhiều các loại lịch khác như lịch Julius (trước 10/1582) đã cố gắng đơn giản hóa việc xử lý phần lẻ bằng cách làm tròn thành 365.25 ngày theo quy tắc "năm nào chia hết cho 4 thì có năm nhuận".

Tuy nhiên, điều này sẽ dẫn đến sai số $\approx$0,0078 ngày (0,25 − 0,2422 = 0,0078).
- 1 năm dư 0,0078 ngày
- 100 năm dư 0,78 ngày
- 128 năm dư $\approx$1 ngày

$\Rightarrow$ Nghĩa là sau 128 năm, lịch sẽ lệch $\approx$1 ngày so với chu kỳ thiên văn thực.

#### Lịch Gregorius (10/1582)
Nhằm khắc phục việc bị “dư” 0,0078 ngày mỗi năm, Giáo hoàng Gregory XIII đã ban hành lịch Gregory vào tháng 10 năm 1582 với hai quy tắc chính:
- <b> Năm chia hết cho 4 nhưng không chia hết cho 100. </b>
- <b> Hoặc năm chia hết cho 400 (mặc dù chúng chia hết cho 100.) </b>

Qua đó, độ dài một năm theo lịch Gregorius quy ước <b>365.2425 ngày</b>, tức là lớn hơn chu kỳ thiên văn thực khoảng $3\over10000$ (365.2425 − 365.2422 = 0.0003), nhưng vẫn đủ chính xác để sử dụng ổn định trong nhiều thiên niên kỷ tới. 

<b>Năm lịch Gregorius 365.2425 có thể được viết lại như sau:</b>

$$ 365.0000 + 0.2500 - 0.0100 + 0.0025 $$

Hay:

$$ 365 + \frac{1}{4} - \frac{1}{100} + \frac{1}{400} $$

<b>Số ngày trong y năm là:</b>

$$
\begin{equation}
    365y + \frac{y}{4} - \frac{y}{100} + \frac{y}{400}
    \label{eq:dayeq1}
\end{equation}
$$

<b>Nếu ta rút gọn thành hàm rời rạc[^hamroirac], ta có:</b>

$$
\begin{equation}
    D(y) = 365y + \lfloor\frac{y}{4}\rfloor - \lfloor\frac{y}{100}\rfloor + \lfloor\frac{y}{400}\rfloor
    \label{eq:dayeq2}
\end{equation}
$$

Cứ mỗi bốn năm, $\lfloor\frac{y}{4}\rfloor$ sẽ được cộng thêm 1 ngày nhuận, nhưng việc này sẽ bị hủy bỏ sau mỗi thế kỷ $\lfloor\frac{y}{100}\rfloor$. Song, mỗi $\lfloor\frac{y}{400}\rfloor$ sẽ cộng lại 1 ngày nhuận. [(nguyên tắc bao hàm - loại trừ.)](https://wiki.vnoi.info/translate/he/Number-Theory-7){:target="_blank"}

Hàm rời rạc[^hamroirac] này là nguồn gốc của quy tắc năm nhuận trong lịch Gregorius. Hàm \eqref{eq:dayeq2} xấp xỉ rất gần với hàm thực \eqref{eq:dayeq1}, và dấu "$=$" xảy ra khi y chia hết 400. Trong khoảng 400 năm này, sai số lớn nhất là 1.4775 ngày vào năm 303 của chu kỳ, và −0.72 ngày vào năm 96 của chu kỳ. Do sai số này, việc tìm năm khi biết số ngày (hàm ngược) không hoàn toàn chính xác, nhưng ta có thể tìm một xấp xỉ rất gần. 

<b>Cho trước d ngày, số năm y có thể xấp xỉ bằng:</b>

$$
\begin{equation}
    y_{approx} = \frac{d}{365.2425}
    \label{eq:approxy}
\end{equation}
$$

> Điều này sẽ đúng cho hầu hết các ngày, <b>NGOẠI TRỪ</b> những trường hợp mà d nằm trong khoảng sai số so với ranh giới của một năm. <b>Ví dụ với một trường hợp dưới đây:</b>
{: .prompt-danger }
<!-- markdownlint-restore -->

Giả sử ta có $d = 36524$ ngày và muốn tìm y sao cho $y_{approx} \approx d$.

$$
y_{approx} = \frac{d}{365.2425} = \frac{36524}{365.2425} \approx 99.9993.
$$

Nếu lấy phần nguyên, ta sẽ cho rằng $y = 99$ năm. Nhưng thực tế, $100$ năm lịch Gregorius mới chính xác là 36524 ngày (do năm thế kỷ không phải năm nhuận). 

$$

\begin{align}

D(100) &= 365*100 + \lfloor\frac{100}{4}\rfloor - \lfloor\frac{100}{100}\rfloor + \lfloor\frac{100}{400}\rfloor \nonumber\\&= 36500 + 25 - 1 + 0 \nonumber\\&= 36524 \nonumber

\end{align}

$$

Đây chính là trường hợp d nằm trong khoảng sai số so với ranh giới của năm thứ 100, nếu dùng công thức \eqref{eq:approxy} để “đảo ngược” ngày thành năm, ta sẽ thu được kết quả sai lệch một năm.

#### Cách khắc phục

Để khắc phục sai lệch trên, đầu tiên ta đưa công thức \eqref{eq:approxy} về dạng <b>tính toán với số nguyên để tránh sai số do tính toán với số dấu phẩy động</b> ([floating-point numbers](https://en.wikipedia.org/wiki/Floating-point_arithmetic){:target="_blank"}):

$$
y_{approx} = \frac{d}{365.2425} = \frac{d * 10000}{365.2425 * 10000} = \frac{10000d}{3652425}
$$

Tuy nhiên, công thức trên vẫn cho ra kết quả sai lệch nên ta phải bù vào một hằng số $C$ (offset) để chắc chắn không bị trượt sai một đơn vị khi rời rạc hóa. Một lựa chọn quan trọng cho $C$ là sử dụng $C_{max}$. Việc đặt $C = C_{max}$ mang lại một ưu điểm quan trọng $\Rightarrow$ <b>Nó đảm bảo rằng $y_{approx}$ sẽ không bao giờ nhỏ hơn $y$ ($y_{approx} \geq y$).</b> Cụ thể, $y_{approx}$ = $[y, y + 1]$. Nhờ vậy, ta có thể đảm bảo năm chính xác.

Đâu tiên ta tìm độ sai lệch lớn nhất giữa $10000D(y)$ và $y(3652425)$ trên một chu kỳ 400 năm (vì cứ 400 năm sai số tái lặp):

$$

\Delta(y) = y(3652425) - 10000D(y).

$$

$$

\Rightarrow C_{max} = \max\limits_{0\leq y < 400}(\Delta(y)) = 14775 \text{ (Khi y = 303)}

$$

Vậy công thức chuyển ngày thành năm của chúng ta trở thành:

$$
\begin{equation}
    y = \lfloor\frac{10000d + 14775}{3652425}\rfloor
    \label{eq:yearsfromdays}
\end{equation}
$$

## Tính toán tháng

Vì độ dài các tháng không giống nhau, đặc biệt là vì độ dài của <b>tháng 2 không cố định</b>, ban đầu việc tính toán hệ số ngày trong một năm rất bất khả thi nếu không tra bảng và kiểm tra các năm nhuận. Tuy nhiên, ta có thể dễ dàng vượt qua vấn đề này bằng cách <b>định nghĩa đầu năm của lịch là tháng 3</b>. 

| Chỉ số tháng (month_index)       | Tháng         | Số ngày trong tháng   | Vị trí ngày (day_index)    |
| :--------             | :--------     | :-------------        | :-------------    |
|         0             |       3       | 31                    | 0                 |
|         1             |       4       | 30                    | 31                |
|         2             |       5       | 31                    | 61                |
|         3             |       6       | 30                    | 92                |
|         4             |       7       | 31                    | 122               |
|         5             |       8       | 31                    | 153               | 
|         6             |       9       | 30                    | 184               |
|         7             |       10      | 31                    | 214               |
|         8             |       11      | 30                    | 245               |
|         9             |       12      | 31                    | 275               |
|         10            |       1       | 31                    | 306               |
|         11            |       2       | 28 hoặc 29            | 337               |

> Nếu năm nhuận thì số ngày tháng 2 là 29, tức tháng 2 lúc đó lệch +1, nhưng không làm thay đổi offset các tháng trước vì kết thúc ở $\text{month_index} = 11$.
{: .prompt-info }

Việc đánh số tháng theo cách này có ưu điểm đặc biệt là <b>ngày nhuận luôn được thêm vào cuối năm ($\text{month_index} = 11$)</b>, và không làm thay đổi hệ số ngày của đầu các tháng trong năm. Bằng cách sử dụng hồi quy tuyến tính cơ bản trên <b>chỉ số tháng ($\text{month_index}$)</b> và <b>vị trí ngày ($\text{day_index}$)</b>, ta có thể tìm các hàm để ánh xạ lẫn nhau. 

Ta có tổng số ngày của 10 tháng (từ tháng 3 đến tháng 12) là 306 ngày. Nên sau mỗi 10 bước $\text{month_index}$ (từ 0 đến 9), ta đã dịch chuyển đúng 306 ngày. <b>Nói cách khác, “trung bình” cứ mỗi bước tăng $\text{month_index}$ thì số ngày dời đi khoảng $\frac{306}{10} = 30.6$ ngày.</b>

Ta muốn công thức cho kết quả nguyên chính xác bằng “tổng số ngày đã trôi qua kể từ đầu tháng 3 của năm đó” khi $\text{month_index}$ là một số nguyên 0, 1, 2, … Cho ví dụ:
- Với $\text{month_index} = 0$ (tháng 3) $\Rightarrow \text{day_index} = 0$
- Với $\text{month_index} = 1$ (tháng 4) $\Rightarrow \text{day_index} = 31$
- Với $\text{month_index} = 2$ (tháng 5) $\Rightarrow \text{day_index} = 61$
- ...
- Với $\text{month_index} = 10$ (tháng 1) $\Rightarrow \text{day_index} = 306$

Nếu ta chỉ dùng thẳng công thức "$30.6 * \text{month_index}$" rồi làm tròn xuống $\lfloor30.6 * \text{month_index}\rfloor$, thì sẽ bị lệch 1 ngày do phần thập phân. Để khắc phục, cần biểu diễn "$30.6 * \text{month_index}$" dưới dạng phân số có mẫu 10 rồi cộng một hằng số $C = 5$ bù để khi làm tròn luôn ra số nguyên gần nhất đảm bảo đúng kết quả.

Qua đó, ta có thể tính số ngày kể từ đầu năm <b>(ngày 1 tháng 3 được coi là ngày 0)</b> của ngày mùng 1 của tháng đó bằng phần nguyên của hàm:

$$
\begin{equation}
    \text{day_index} = \text{getDayIndex}(\text{month_index}) = \lfloor\frac{306*\text{month_index} + 5}{10}\rfloor
    \label{eq:getdayindex}
\end{equation}
$$

<b>Ngược lại</b>, cho trước hệ số ngày day_index tính từ đầu năm (bắt đầu từ month_index = 0), ta muốn tìm chỉ số tháng month_index, ta có:

$$
\text{day_index} = \lfloor\frac{306*\text{month_index} + 5}{10}\rfloor
$$

$$
\Longleftrightarrow \text{day_index} \leq \frac{306*\text{month_index} + 5}{10} < \text{day_index} + 1
$$

$$
\Longleftrightarrow \frac{10*\text{day_index} - 5}{306} \leq \text{month_index} < \frac{10*\text{day_index} + 5}{306}
$$

Vì $\text{month_index}$ là số nguyên (từ 0 đến 11), điều này cho ta:

$$
\begin{equation}
    \text{month_index} = \text{getMonthIndex}(\text{day_index})= \lfloor\frac{10*\text{day_index} + 5}{306}\rfloor
    \label{eq:getmonthindex}
\end{equation}
$$

> Lưu ý: Tháng 1 và tháng 2 ($\text{month_index} = 10$ và $\text{month_index} = 11$) sẽ được xem như là <b>năm trước</b> theo thuật toán
{: .prompt-warning }

> Lưu ý: Trong lịch Gregorius, ngày 1 tháng 3 của năm 0 thực chất rơi vào thứ Tư ([Zeller’s Congruence](https://www.geeksforgeeks.org/zellers-congruence-find-day-date/){:target="_blank"}).
{: .prompt-warning }

## Mã giải (C++)

```c++
#define ll long long
struct gdate {

    ll d, m, y; // Lưu ngày, tháng, năm
    gdate() : d(0), m(0), y(0) {} 
    gdate(ll d_, ll m_, ll y_) : d(d_), m(m_), y(y_) {} // Constructor

    friend ostream& operator << (ostream& out, const gdate& x) { // in nhanh
        return out << x.d << ' ' << x.m << ' ' << x.y, out;
    }

};

// month_index: 0 (Tháng 3) ... 11 (Tháng 2 năm sau)
ll getDayIndex(int month_index) {
    return (306 * month_index + 5) / 10; // (5)
}

ll D(ll y) { 
    return 365 * y + y / 4 - y / 100 + y / 400; // (2)
}

// Chuyển đổi (d, m, y) sang tổng số ngày (tính từ mốc ngày 1 tháng 3 của năm 0) 
ll toDays(gdate date) {

    // Chuyển tháng hiện tại (1-12) sang month_index (0-11), với tháng 3 là 0
    // Tháng 1 -> month_index = 10
    // Tháng 2 -> month_index = 11
    // Tháng 3 -> month_index = 0
    // ...
    // Tháng 12 (Dec) -> month_index = 9
    int month_index = (date.m + 9) % 12;

    // Nếu tháng là tháng 1 hoặc 2 (month_index = 10 hoặc 11) thì năm tính toán (fixedy) sẽ là năm hiện tại trừ 1.
    // Ví dụ: 1/1/2025 (m=1, y=2025) -> month_index=10. fixedy = 2025 - 10/10 = 2024.
    // Điều này là do tháng 1, 2 của năm Y được coi là năm trước.
    ll fixedy = date.y - month_index / 10;

    // Tổng số ngày = (số ngày từ mốc đến đầu năm fixedy)
    //               + (số ngày từ đầu năm fixedy đến đầu tháng month_index)
    //               + (số ngày trong tháng hiện tại - 1) (vì ngày 1 được coi là day index 0)
    return D(fixedy) + getDayIndex(month_index) + (date.d - 1);

}

// Chuyển đổi tổng số ngày (tính từ mốc ngày 1 tháng 3 của năm 0) sang (d, m, y)
gdate toDate(ll total_days) {

    ll y = (total_days * 10000 + 14775) / 3652425; // (4)
    // day_index là số ngày thứ bao nhiêu trong "năm thuật toán" y.
    ll day_index = total_days - D(y);
    // Nếu day_index < 0, nghĩa là total_days thực tế nằm ở cuối năm năm trước.
    // Ta giảm y đi 1 và tính lại day_index.
    if (day_index < 0) {
        y--;
        day_index = total_days - D(y);
    }

    // Từ ddd và month_index, tính ra ngày trong tháng (final_d)
    // final_d = ddd - (số ngày từ ngày 1 tháng 3 đến đầu month_index) + 1 (vì ngày trong lịch bắt đầu từ 1)
    int month_index = (10 * day_index + 5) / 306; // (6)
    ll final_d = day_index - getDayIndex(month_index) + 1;

    // Chuyển month_index (0-11) về tháng chuẩn (1-12) (final_m)
    // month_index 0 (Mar) -> (0+2)%12+1 = 3
    // month_index 9 (Dec) -> (9+2)%12+1 = 12
    // month_index 10 (Jan) -> (10+2)%12+1 = 1
    // month_index 11 (Feb) -> (11+2)%12+1 = 2
    ll final_m = (month_index + 2) % 12 + 1;

    // Điều chỉnh lại năm (final_y)
    // Nếu month_index là 10 (tháng 1) hoặc 11 (tháng 2), thì năm thực tế (final_y) phải là y + 1.
    // Ví dụ: nếu month_index = 10 (tháng 1), (10+2)/12 = 1. Năm sẽ được cộng thêm 1.
    ll final_y = y + (month_index + 2) / 12;

    return gdate(final_d, final_m, final_y);

}

```

Độ phức tạp của các thao tác tính toán trên tổng thể chỉ mất $O(1)$, do đó rất hiệu quả.

## Ứng dụng

> In ra sự khác biệt giữa hai (d, m, y)
{: .prompt-info }

```c++
gdate lich1(d1, m1, y1);
gdate lich2(d2, m2, y2);
cout << abs(toDays(lich1) - toDays(lich2));
```

> (d, m, y) sau khi tăng / giảm x ngày
{: .prompt-info }

```c++
gdate lich(d, m, y);
cout << toDate(toDays(lich) + x); // toDate(toDays(lich) - x);
```

> Kiểm tra ngày hợp lý
{: .prompt-info }

```c++
gdate lich(d, m, y);
gdate check = toDate(toDays(lich));
cout << ((d == check.d && m == check.m && y == check.y) ? "ok" : "no");
```

> In ra thứ của ngày
{: .prompt-info }

```c++
string day[7] = { "wednesday", "thursday", "friday", "saturday", "sunday", "monday", "tuesday" };
gdate lich(d, m, y);
cout << day[(toDays(lich)) % 7]; // ngày 1 tháng 3 năm 0 là thứ 4
```

> Tips xử lý DDMMYYYY :3
{: .prompt-tip }
```c++
// xuất nhanh DDMMYYYY 
printf("%02lld/%02lld/%04lld\n", d, m, y);
```
```c++
// (d,m,y) -> DDMMYYYY string 
char buf[20];
sprintf(buf, "%02lld%02lld%04lld", date.d, date.m, date.y);
string s = buf;
```

> Tính chất thú vị: Nếu một ngày (DD/MM/YYYY) được xếp thành ab/cd/YYYY, để nó là một ngày palindrome thì ngày đó phải có dạng ab/cd/dcba (a b c d d c b a) 🧐
{: .prompt-tip }

## Bài tập rèn luyện
<https://www.spoj.com/problems/CODEIT03>{:target="_blank"}

<https://www.spoj.com/problems/NXTDAY>{:target="_blank"}

<https://www.spoj.com/problems/MOZSATDOW>{:target="_blank"}

<https://www.spoj.com/SHORTEN/problems/GREGCAL/>{:target="_blank"}

<https://www.spoj.com/problems/TEASTER/>{:target="_blank"}

<https://www.hackerrank.com/contests/iiti-league-contest-2/challenges/days-shift/problem>{:target="_blank"}

<https://codeforces.com/contest/304/problem/B>{:target="_blank"}

<https://marisaoj.com/problem/531>{:target="_blank"}

<https://onlinejudge.org/external/8/p893.pdf>{:target="_blank"}

<https://lqdoj.edu.vn/problem/semester>{:target="_blank"}

<https://oj.vnoi.info/problem/coci1920_r6_datum>{:target="_blank"}

## Nguồn tham khảo
- [Gary Katch, “GDate method" (Archived)](https://web.archive.org/web/20161203020157/http://alcor.concordia.ca/~gpkatch/gdate-method.html){:target="_blank"}
- [Gary Katch, “GDate algorithm" (Archived)](https://web.archive.org/web/20170507133619/https://alcor.concordia.ca/~gpkatch/gdate-algorithm.html){:target="_blank"}

#### Ghi chú
[^hamroirac]: Hàm rời rạc: Hàm lấy phần nguyên và bỏ qua phần dư
