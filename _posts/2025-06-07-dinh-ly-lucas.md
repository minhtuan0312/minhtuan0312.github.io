---
layout: post
title: Xử lý tổ hợp lớn với định lý Lucas🚀
date: 2025-06-07 13:24 +0700
categories: [số học, định lý]
tags: [tổ hợp, hệ số nhị thức, số học, thuyết đồng dư, nguyên tố]
math: true
---

`#note: thông tin trong bài blog này có thể chưa hoàn thiện và sẽ cập nhật ở tương lai`

Xin chào! chắc hẳn các bạn cũng đều biết cách tính toán tổ hợp trong một số bài toán rồi nhỉ, thường bài toán ấy sẽ yêu cầu chúng ta <b>đếm số cách để chọn/di chuyển đến/ đặt $\dots$</b> rồi sau đó chia dư cho $10^9 + 7$. Nhưng, trong trường hợp bài toán cho giới hạn $n$ và $k$ <b>rất lớn</b> $(n, k \leq10^{18})$ thì việc tính tổ hợp trở nên rất phức tạp nếu ta làm theo thông thường. Tuy nhiên, thường các bài toán ấy sẽ chỉ yêu cầu chúng ta <b>chia dư đáp án cho một module nhỏ</b> (ví dụ $10^5 + 3$) thì việc tính toán tổ hợp sẽ được đơn giản hóa thông qua <b>định lý Lucas</b> (Édouard Lucas, 1878).

![Édouard Lucas](/assets/img/Elucas.png)
_Édouard Lucas (4/4/1842 - 3/10/1891)_

## Phát biểu

Cho $p$ là một số nguyên tố, $n$ và $k$ thuộc số nguyên không âm, khi đó:

$$
 \binom{n}{k} \equiv \prod_{i = 0}^{m} \binom{n_i}{k_i}  \text{     (mod p)}
$$

Hay

$$
 C_{n}^{k} \equiv \prod_{i = 0}^{m} C_{n_i}^{k_i}  \text{     (mod p)}
$$

Trong đó:

$$ n = n_mp^m + \dots + n_2p^2 + n_1p^1 + n_0p^0 $$ 

$$ k = k_mp^m + \dots + k_2p^2 + k_1p^1 + k_0p^0 $$ 

Là biểu diễn của n và k trên hệ cơ số p.

Ngược lại, nếu có chỉ số $i$ nào đó mà $k_i > n_i$ thì $ \binom{n_i}{k_i} = 0.$ Vì tích trong công thức của định lý Lucas chứa thừa số $\binom{n_i}{k_i}$ nên toàn bộ tích sẽ bằng $0$ $\Rightarrow \binom{n}{k} \equiv 0 \text{ (mod m)}$.

## Tính hiệu quả

Ưu điểm lớn nhất của định lý Lucas là nó <b>chia bài toán lớn thành các bài toán nhỏ hơn</b>, do đó rất dễ quản lý.

Cụ thể, thay vì ta phải tính với các giai thừa của những con số khổng lồ như $10^{18}$, ta chỉ cần tính toán các tổ hợp $\binom{n_i}{k_i}$ với $n_i$ và $k_i$ luôn $< p$. Việc biểu diễn $n$ (và $k$) trên hệ cơ số p chỉ mất $O(log_pn)$, cùng với việc ta có thể tính nhanh $\binom{n_i}{k_i}$ bằng công thức $C_{n_i}^{k_i} = \frac{n!}{k!(n - k)!}$ (với giai thừa và nghịch đảo module đã được tính trước).

## Chứng minh (<https://wiki.vnoi.info/translate/he/Lucas-theorem>)

## Hệ quả

$\binom{n}{k}$ chia hết cho số nguyên tố $p$ khi và chỉ khi có ít nhất một chữ số $k_i$ của $k$ trong biểu diễn cơ số p lớn hơn chữ số tương ứng $n_i$ của n.

- <b>Giải thích</b>: $k_i > n_i$ thì $\binom{n_i}{k_i} = 0.$ $\Rightarrow  \binom{n}{k} \equiv 0 \text{ (mod m)}$ $\Rightarrow$ $\binom{n}{k}$ $\vdots$    $p$

$\binom{n}{k}$ là một số lẻ khi và chỉ khi trong biểu diễn nhị phân, bất cứ khi nào bit thứ $i$ của $k$ là $1$, thì bit thứ $i$ của $n$ cũng phải là $1$.

- <b>Giải thích</b>: Xét trường hợp $p = 2$. Các chữ số trong biểu diễn cơ số 2 là 0 hoặc 1. Tích $\prod \binom{n_i}{k_i}$ sẽ không phải là 0 (tức bằng 1) khi và chỉ khi các thừa số $\binom{n_i}{k_i} = 1$. Điều này sẽ xảy ra khi không có trường hợp $n_i = 0$ và $k_i = 1$ và ngược lại. Nói cách khác, nếu $k_i = 1$ thì $n_i$ cũng phải bằng 1. Theo logic bit, ta có thể kiểm tra dễ dàng với phép toán `(n & k) == k` hoặc `(k & n) == k`.

## Nguồn tham khảo
- [VNOI, "Định lý Lucas"](https://wiki.vnoi.info/translate/he/Lucas-theorem){:target="_blank"}