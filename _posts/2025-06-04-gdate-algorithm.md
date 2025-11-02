---
layout: post
title: Thuật toán chuyển đổi thời gian hiệu quả trong O(1)⌛
date: 2025-06-04 09:42 +0700
categories: [algorithms, time]
tags: [thời gian, lịch, date]
math: true
---
> Mọi góp ý, sai sót sẽ được mình tiếp nhận để mình cải thiện trong việc viết blog orz

![Lịch Gregorian](/assets/img/Gregorian Calendar.jpg)
_Lịch Gregorian (hệ thống lịch của thế giới ngày nay)_

<!-- Sau đây mình xin được giới thiệu với các bạn <b>thuật toán GDate</b> của <i>Gary Katch</i>. Một thuật toán rất mạnh mẽ, độc đáo và hiệu quả trong việc xử lý ngày tháng, lịch mà hầu như mình chưa thấy nguồn tiếng Việt nào nhắc tới.

| Ưu điểm                                                                           |
| :-------------------------------------------------------------------------------- |
| Xử lý nhanh các bài toán liên quan đến lịch, thời gian, ngày, tháng, ... trong $O(1)$         |
| Rất mạnh mẽ và mang tính ứng dụng thực tiễn cao (lập trình, game, hệ thống, mô phỏng...)     |

| Nhược điểm |
| :----------|
| Rất khó thấm | -->

## Đặt vấn đề

#### <b>Đề bài</b>
![Desktop View](/assets/img/Jailproblem.png){: width="200" height="200" .w-60 .right}

Vậy là kì thi HSG khốc liệt đã kết thúc, Tuấn mạnh dạn rủ crush đi Aeon chơi và bất ngờ thay, cô ấy đồng ý! 

Tuấn vui sướng đến sớm, đứng ngay trước cửa CGV từ ngày $d$, tháng $m$, năm $y$ và bắt đầu đếm từng giây chờ người thương đến.

Nhưng một tiếng... rồi một ngày... rồi một tuần...
Cô ấy vẫn chưa tới.
Tuấn vẫn kiên nhẫn, mỗi ngày đánh dấu thêm một gạch trên tường.....

#### <b>Nhiệm vụ</b>
Sau $x$ ngày chờ đợi (vẫn chưa thấy bóng dáng cô ấy đâu), hãy <b>xác định ngày Tuấn nhận ra sự thật phũ phàng</b> — tức là ngày thứ $x$ kể từ khi bắt đầu đợi.

#### <b>Input</b>
 - Một dòng gồm bốn số nguyên $T, x, d, m, y$.
 - Trong đó: 
 + $T$ — số lượng test cases ( $0 < T \le 10^6$ )
 + $x$ — số ngày Tuấn đã chờ ở Aeon ( $0 < x \le 10^{18}$ ) 
 + $d, m, y$ — ngày, tháng, năm Tuấn bắt đầu chờ. ( $0 < y \le 10^{6}$ ) 

#### <b>Output</b>
 - In ra <b>thứ, ngày, tháng, năm</b> hiện tại, tức là <b>ngày Tuấn kết thúc $x$ ngày chờ đợi</b> (và có lẽ… quay về nhà :c).

#### <b>Ghi chú</b>
- Thứ được in ra bằng tiếng Anh, viết hoa chữ cái đầu: <i>Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday</i>
- Ngày tháng năm ngăn được cách bởi dấu '/'
- Sử dụng lịch quốc tế <b>(lịch Gregorian)</b>.

#### <b>Subtasks</b>

| Subtask                      | Giới hạn                                    | Điểm |
| :--------------------------- | :------------------------------------------ | :--- |
| 1                            | $T \le 5$, $x \le 10^3$, $y \le 2025$       |  10  |
| 2                            | $T \le 5$, $x \le 10^9$, $y \le 2026$       |  20  |
| 3                            | $T \le 5$, $x \le 10^{18}$, $y \le 10^6$    |  30  |
| 4                            | $T \le 10^6$, $x \le 10^{18}$, $y \le 10^6$ |  40  |

## Ý tưởng

#### Subtask 1
> Ta có thể dễ dàng AC subtask 1 thông qua vòng lặp cơ bản và xử lý trường hợp năm nhuận với độ phức tạp tổng thể là $O(x)$.
{: .prompt-tip }

#### Subtask 2
> Nhận thấy $x$ có thể lên tới $10^9$ nên việc giải bằng vòng lặp hoàn toàn <b>gãy cánh</b>. Ta cần công thức hóa bằng cách chuyển ngày tháng năm thành một số nguyên đại diện cho <b>số ngày trôi qua ngày kể từ mốc $1/1/0$ kết hợp với ánh xạ</b>. Kết quả là bài toán có thể được tối ưu xuống độ phức tạp $O(logx)$
{: .prompt-tip }

#### Subtask 3
> Với $x = 10^{18}$ thì chắc chắn ta cần phải tìm được một giải pháp chạy trong $O(1)$ thông qua việc tối ưu rất rất nhiều. Mà để đạt được độ phức tạp tưởng như không thể này thì ta cần phải tìm ra <b>tính chất đặc biệt nằm trong lịch Gregorian</b>. Nhận thấy <b>lịch Gregorian tuần hoàn chu kỳ trong 400 năm</b>, ta có thể giải quyết subtask 3 với độ phức tạp chỉ: $O(146105)$.
{: .prompt-tip }

#### Subtask 4
> Tuy nhiên, với $T = 10^{18}$ thì mọi chuyện không còn đơn giản hơn nữa... Lúc này, ta cần khai thác thêm các biến đổi và tính chất toán học để tối ưu. <b>Cụ thể phải phân tích như thế nào và vì sao lại cần làm thế, mình sẽ giải thích chi tiết trong blog hôm nay nhé:)</b>
{: .prompt-tip }

## Tổng quát về lịch thế giới
Độ dài trung bình của một năm chí tuyến được đo lường <b>$\approx$365.2422 ngày</b>. Nhiều các loại lịch khác như [lịch Julius](https://vi.wikipedia.org/wiki/L%E1%BB%8Bch_Julius){:target="_blank"} (trước 10/1582) đã cố gắng đơn giản hóa việc xử lý phần lẻ bằng cách làm tròn thành 365.25 ngày theo quy tắc:

 - <b>năm nào chia hết cho 4 thì có năm nhuận.</b>

Tuy nhiên, việc làm tròn này tạo ra <b>sai số $\approx$0,0078 ngày/năm</b> so với độ dài năm chí tuyến, tương đương khoảng 1 ngày lệch sau mỗi 128 năm.

$\Rightarrow$ Điều đó có nghĩa là theo thời gian, ngày xuân phân và hạ chí dần trôi khỏi mốc thiên văn thực tế, dẫn đến một số mùa màng “đến sớm” hơn lịch ghi nhận.

#### Lịch Gregorian (10/1582)
Nhằm khắc phục việc bị dư 0,0078 ngày mỗi năm, <b>Giáo hoàng Gregory XIII</b> đã ban hành lịch Gregorian vào tháng 10 năm 1582 với quy tắc như sau:
- <b> Năm chia hết cho 4 là năm nhuận.</b>
- <b> Năm chia hết cho 100 không là năm nhuận. </b>
- <b> Năm chia hết cho 400 là năm nhuận.</b>

Qua đó, độ dài một năm theo lịch Gregorian quy ước <b>365.2425 ngày</b>, tức là lớn hơn độ dài trung bình của một năm chí tuyến <b>$\approx 0,0003$ ngày/năm</b>. Tức xấp xỉ chỉ 26 giây mỗi năm và tương đương 1 ngày sau khoảng 3300 năm.

## Tính toán số ngày trôi qua kể từ 1/1/0

#### Tính chất đặc biệt của Lịch Gregorian

<b>Ta thực hiện phép biến đổi như sau:</b>

$$
\begin{align*}
365.2425 
&\Longleftrightarrow 365.0000 + 0.2500 - 0.0100 + 0.0025 \\
&\Longleftrightarrow 365 + \frac{1}{4} - \frac{1}{100} + \frac{1}{400}
\end{align*}

$$

<b>Tham số hóa nó thì ta có số ngày trong y năm là:</b>

$$
D(y) = 365y + \frac{y}{4} - \frac{y}{100} + \frac{y}{400}
$$

(Với $D(y)$ là hàm đếm số ngày trôi qua kể từ $1/1/0$ và y là số năm đã qua)

<b>Nếu xét trong không gian rời rạc</b>, tức là chỉ đếm số ngày thực tế, ta được hàm tương ứng:

$$
\begin{equation}
    \tilde{D}(y) = \lfloor 365y\rfloor + \lfloor\frac{y}{4}\rfloor - \lfloor\frac{y}{100}\rfloor + \lfloor\frac{y}{400}\rfloor
    \label{eq:dayeq1}
\end{equation}
$$

(Với $\tilde{D}(y)$ là hàm rời rạc của $D(y)$)

<b>Qua công thức trên thì ta có nhận xét như sau:</b> Cứ mỗi bốn năm, $\lfloor\frac{y}{4}\rfloor$ sẽ được cộng thêm 1 ngày nhuận, nhưng việc này sẽ bị hủy bỏ sau mỗi thế kỷ $\lfloor\frac{y}{100}\rfloor$. Song, mỗi $\lfloor\frac{y}{400}\rfloor$ sẽ cộng lại 1 ngày nhuận. [(nguyên tắc bao hàm - loại trừ.)](https://wiki.vnoi.info/translate/he/Number-Theory-7){:target="_blank"} <b>

$\Rightarrow$ Nhờ đó, ta nhận thấy <b>lịch Gregorian lặp lại chính xác sau mỗi 400 năm và tuần hoàn theo chu kỳ này.</b> Nên ta chỉ cần xét và tối ưu trong khung 400 năm đầu tiên là đủ.

#### Vấn đề sai số

Vì hàm $\tilde{D}(y)$ nằm trong không gian rời rạc nên <b>chắc chắn</b> sẽ có sai số so với hàm gốc (vì hàm này luôn trả về giá trị nguyên với $\forall y \in \mathbb{R}$).

Sai số này được biểu diễn bằng hàm sau:

$$
\varepsilon(y) = D(y) - \tilde{D}(y), \quad \forall\, y \in [0, 400]
$$

Ta có nhận xét như sau: 

$$
\begin{align}
    &\textbf{(1)} \quad \varepsilon(y) = 0, 
        &&\text{khi } y \mid 400. 
        \label{eq:error_zero} \\[6pt]
    &\textbf{(2)} \quad \varepsilon_{\max} = 1.4775, 
        &&y = 303.
        \label{eq:error_max} \\[6pt]
    &\textbf{(3)} \quad \varepsilon_{\min} = -0.72, 
        &&y = 96.
        \label{eq:error_min}
\end{align}

$$


Do tồn tại sai số này, việc tìm năm sau khi biết tổng số ngày kể từ 1/1/0 <b>rất khó khăn và không thể chính xác tuyệt đối</b>, dù vẫn cho xấp xỉ cực kỳ gần với thực tế.

## Tính toán năm từ tổng số ngày trôi qua

Cho $D$ là số ngày trôi qua kể từ 1/1/0, ta có thể tính số năm y dựa vào công thức sau:

$$
\begin{equation}
    y \approx \frac{D}{365.2425} \quad \Rightarrow \quad \tilde{y} = \lfloor \frac{D}{365.2425} \rfloor
    \label{eq:approxy}
\end{equation}
$$

Công thức trên đúng với độ chính xác cao trong hầu hết các trường hợp, ngoại trừ khi $D$ nằm gần mốc chuyển giao năm (tức là khi $D$ xấp xỉ một giá trị $\tilde{D}(y)$  nào đó — tổng số ngày tính đến hết năm thứ $y$). Tại những mốc này, sai số do sự xuất hiện hoặc biến mất của ngày nhuận trở nên rõ rệt, khiến phép chia xấp xỉ \eqref{eq:approxy} không còn chính xác.

<b>Ví dụ:</b>
Giả sử ta có $d = 36524$ ngày và muốn tìm năm $y$ tương ứng.

$$
\tilde{y} = \lfloor \frac{36524}{365.2425} \rfloor = 99.
$$

Nếu lấy phần nguyên thì ta sẽ cho rằng $y = 99$ năm, ta kiểm tra lại như sau:

$$

\begin{align}

\tilde{D}(99) &= \lfloor 365*99\rfloor + \lfloor\frac{99}{4}\rfloor - \lfloor\frac{ 99}{100}\rfloor + \lfloor\frac{99}{400}\rfloor \nonumber\\&= 36135 + 24 - 0 + 0 \nonumber\\&= 36159 \neq 36524 \nonumber

\end{align}

$$

$\Rightarrow$ Thực tế, $100$ năm lịch Gregorian mới chính xác là 36524 ngày (do năm thế kỷ không phải năm nhuận). 

Đây chính là trường hợp D nằm trong khoảng sai số so với ranh giới của năm thứ 100, nếu dùng công thức \eqref{eq:approxy} để đảo ngược ngày thành năm, ta sẽ thu được kết quả sai lệch một năm.

#### Cách khắc phục sai số từ ngày

Vì làm việc trong C++ nên trước hết cần phải đưa công thức \eqref{eq:approxy} về dạng tính toán hoàn toàn bằng số nguyên, nhằm tránh sai số khi thao tác với <b>dấu phẩy động</b> ([floating-point numbers](https://en.wikipedia.org/wiki/Floating-point_arithmetic){:target="_blank"}):

$$
\tilde{y} = \lfloor \frac{D}{365.2425} \rfloor \le \frac{D}{365.2425} \quad \Rightarrow \quad \tilde{y} = \lfloor \frac{D * 10000}{3652425} \rfloor \le \frac{D * 10000}{3652425}
$$

Nhận thấy thường khi bị lệch $1$ năm thì y thực của chúng ta sẽ ở dạng $\tilde{y} = k + 0.999$... Để né hiện tượng này thì ta cần bù thêm một hằng số $C$ sao cho $\tilde{y} \geq \frac{D * 10000}{3652425}$, mục đích là để khi floor thì giá trị bằng $y$ hoặc $y + 1$. Lựa chọn chính xác và an toàn nhất đó là sử dụng sai số cực đại mà ta tìm thấy ở $\eqref{eq:error_max}$

$$
C = \varepsilon_{\max} = 1.4775
$$

Vậy công thức chuyển ngày thành năm của chúng ta trở thành:

$$
\begin{equation}
    \tilde{y} = \lfloor \frac{(D + 1.4775) * 10000}{3652425} \rfloor = \lfloor \frac{D * 10000 + 14775}{3652425} \rfloor
    \label{eq:yearsfromdays}
\end{equation}
$$

Vì giá trị $\tilde{y}$ bằng $y$ hoặc $y + 1$ nên khi ta cần tìm lại năm chính xác thì cần phải có bước <b>lùi năm</b>. Điều này có thể thực hiện dễ dàng thông qua việc so sánh $D(\tilde{y})$ với $D(y)$.

## Tính toán tháng từ tổng số ngày trôi qua

Vì độ dài các tháng <b>không đồng nhất</b>, đặc biệt là <b>tháng 2 có độ dài thay đổi tùy theo năm nhuận</b>, nên việc xác định số ngày kể từ đầu năm trở nên phức tạp nếu chỉ dựa vào bảng tra thông thường.

Để khắc phục điều này, ta <b>định nghĩa lại điểm bắt đầu của năm là ngày 1 tháng 3 của năm 0 (thay vì ngày 1 tháng 1)</b>, sao cho tháng 1 và tháng 2 của năm kế tiếp được xem như phần cuối của năm hiện tại.

| month_index           | Tháng         | Số ngày trong tháng   | day_index         |
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

* <b>Định nghĩa:</b>
+ <b>month_index</b> là chỉ số tháng bắt đầu từ tháng 3.
+ <b>day_index</b> là chỉ số ngày thể hiện số ngày đã trôi qua kể từ tháng 3.

> Việc đánh số tháng bắt đầu từ tháng 3 mang lại một ưu điểm nổi bật đó là <b>ngày nhuận luôn được thêm vào cuối năm (month_index=11)</b>, nhờ đó, vị trí bắt đầu của các tháng còn lại không bị thay đổi giữa năm thường và năm nhuận.
{: .prompt-info }

$\Rightarrow$ Nhờ đặc tính này, ta có thể ánh xạ giữa chỉ số tháng và vị trí ngày mà không cần phải lo đến việc xử lý ngoại lệ riêng cho năm nhuận.

Ta có tổng số ngày từ tháng 3 đến tháng 12 là <b>306 ngày</b>. Tức cứ sau mỗi 10 bước, ta đã dịch chuyển đúng 306 ngày. <b>Nói cách khác, “trung bình” cứ mỗi 1 bước tăng $\text{month_index}$ thì số ngày dời đi khoảng $\frac{306}{10}$ ngày.</b>

Ta muốn một công thức nào đó có thể cho kết quả nguyên chính xác bằng “tổng số ngày đã trôi qua kể từ đầu tháng 3 của năm đó” khi $\text{month_index}$ là một số nguyên 0, 1, 2, … Cụ thể
- Với $\text{month_index} = 0$ (tháng 3) $ \quad \Rightarrow \quad \text{day_index} = 0$
- Với $\text{month_index} = 1$ (tháng 4) $\quad \Rightarrow \quad \text{day_index} = 31$
- Với $\text{month_index} = 2$ (tháng 5) $\quad \Rightarrow \quad \text{day_index} = 61$
- ...
- Với $\text{month_index} = 10$ (tháng 1) $\quad \Rightarrow \quad \text{day_index} = 306$

Qua đó, ta có thể tính số ngày kể từ đầu năm <b>(ngày 1 tháng 3 được coi là ngày 0)</b> của ngày mùng 1 của tháng đó bằng phần nguyên của hàm:

$$
\begin{equation}
    \begin{aligned}
        \text{day_index} &= \text{round}\!\left(\frac{306 \times \text{month_index}}{10}\right) \\
                          &= \left\lfloor \frac{306 \times \text{month_index} + 5}{10} \right\rfloor
                          
    \end{aligned}
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

Vì <b>month_index</b> là số nguyên nên để luôn trả về kết quả đúng thì ta sẽ lấy vế phải, điều này cho ta:

$$
\begin{equation}
    \text{month_index} = \lfloor\frac{10*\text{day_index} + 5}{306}\rfloor
    \label{eq:getmonthindex}
\end{equation}
$$

> Tháng 1 và tháng 2 ($\text{month_index} = 10$ và $\text{month_index} = 11$) sẽ được xem như là <b>năm trước</b> theo thuật toán
{: .prompt-warning }

> Trong lịch Gregorian, ngày 1 tháng 3 của năm 0 thực chất rơi vào thứ Tư (có thể kiểm tra sử dụng thuật toán [Zeller’s Congruence](https://www.geeksforgeeks.org/zellers-congruence-find-day-date/){:target="_blank"}).
{: .prompt-warning }

## Mã giải (C++)

```c++
#define ll long long
struct date {
    ll d, m, y; // Lưu ngày, tháng, năm
    date() : d(0), m(0), y(0) {} 
    date(ll a, ll b, ll c) : d(a), m(b), y(c) {} // Constructor

    friend ostream& operator << (ostream& out, const date& x) { // in nhanh
        return out << x.d << ' ' << x.m << ' ' << x.y;
    }
};

ll gregorian(ll y) {
    return y * 365 + y / 4 - y / 100 + y / 400; // (1)
}

ll reversedGregorian(ll D) {
    return (10000 * D + 14775) / 3652425; // (6)
}

int getDayIndex(int month_index) {
    return (306 * month_index + 5) / 10; // (7)
}

int getMonthIndex(int day_index) {
    return (10 * day_index + 5) / 306; // (8)
}

// Chuyển đổi lịch sang tổng số ngày tính từ 1/3/0
ll toDays(date x) {

    // Chuyển tháng hiện tại (1-12) sang month_index (0-11), với tháng 3 là 0
    int month_index = (x.m + 9) % 12;

    // Tháng 1 và tháng 2 (month_index = 10 hoặc 11) theo thuật toán sẽ là của năm trước
    int adjusted_y = x.y - month_index / 10;

    // Tổng số ngày = số ngày kể từ 1/1/0 đến 1/1/adjusted_y
    //               + số ngày kể từ tháng 3 đến month_index
    //               + số ngày trong tháng hiện tại - 1 (vì ngày đầu tiên trong tháng là 1 nhưng trong tính toán là 0)
    return gregorian(adjusted_y) + getDayIndex(month_index) + (x.d - 1);
    
}

// Chuyển đổi tổng số ngày từ 1/1/0 đến 1/1/y sang lịch
date toDate(ll D) {
    ll yyyy = reversedGregorian(D);
    ll day_index = D - gregorian(yyyy);

    // Nếu day_index < 0, tức là chúng ta đang ở năm trước, cần giảm y đi 1 và tính lại day_index.
    if(day_index < 0) yyyy--, day_index = D - gregorian(yyyy);

    int month_index = getMonthIndex(day_index);
    int mm = (month_index + 2) % 12 + 1;
    int dd = day_index - getDayIndex(month_index) + 1;
    yyyy += month_index / 10;
    return  { dd, mm, yyyy };
}
```

Độ phức tạp của các thao tác tính toán trên tổng thể chỉ mất $O(1)$, do đó rất hiệu quả và đấm AC được bài toán đặt vấn đề orz

## Ứng dụng

> In ra sự khác biệt giữa hai (d, m, y)
{: .prompt-info }

```c++
date lich1(d1, m1, y1);
date lich2(d2, m2, y2);
cout << abs(toDays(lich1) - toDays(lich2));
```

> (d, m, y) sau khi tăng / giảm x ngày
{: .prompt-info }

```c++
date lich(d, m, y);
cout << toDate(toDays(lich) + x); // toDate(toDays(lich) - x);
```

> Kiểm tra ngày hợp lý
{: .prompt-info }

```c++
date lich(d, m, y);
date check = toDate(toDays(lich));
cout << ((d == check.d && m == check.m && y == check.y) ? "ok" : "no");
```

> In ra thứ của ngày
{: .prompt-info }

```c++
string day[7] = { "Wednesday", "Thursday", "Friday", "Saturday", "Sunday", "Monday", "Tuesday" };
date lich(d, m, y);
cout << day[(toDays(lich)) % 7]; // ngày 1 tháng 3 năm 0 là thứ 4
```

> Tips xử lý DDMMYYYY
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
