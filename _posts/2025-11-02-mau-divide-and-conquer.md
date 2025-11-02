---
layout: post
title: Template chia để trị siêu cơ bản🔥
date: 2025-11-02 15:45 +0700
---

## Template

```c++
void dnc(int l, int r) {
    if (l >= r) return;
    int mid = (l + r) >> 1;
    dnc(l, mid);
    dnc(mid + 1, r);

    // combine 2 nửa [l..mid] và [mid+1..r]
}
```

## Một số bài toán sử dụng chia để trị
<div class="code-link">
  <a href="https://github.com/minhtuan0312/competitive-programming/tree/3fef9016e364a37e28fae4e49ea42022b5b665b4/practice/algorithms/divide_and_conquer" target="_blank">
    algorithms/divide_and_conquer
  </a>
</div>