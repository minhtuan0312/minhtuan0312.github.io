---
layout: post
title: Template debug đi thi HSG🔥
date: 2025-10-26 14:39 +0700
categories: [template]
tags: [debug]
---

> Template debug này được mình thiết kế dễ học thuộc để đi thi và dễ dàng code nó hỗ trợ thêm stl bằng cách copy and paste. 
{: .prompt-info }

>Tuy nhiên mình vẫn khuyến khích các bạn hiểu cách hoạt động của nó chứ không thuần chép template
{: .prompt-warning }



## Template

```c++
//declarations
template<typename T1, typename T2>
void __print(const pair<T1, T2> &p);

template<typename T, typename ...V>
void __print(const vector<T, V...> &v);

template<typename T1, typename T2, typename ...V>
void __print(const map<T1, T2, V...> &m);

//generic fallback
template<typename T>
void __print(const T &t) {cerr << t;}

//definitions
template<typename T1, typename T2>
void __print(const pair<T1, T2> &p) {
    cerr << '(';
    __print(p.fi);
    cerr << ", ";
    __print(p.se);
    cerr << ')';
}

template<typename T, typename ...V>
void __print(const vector<T, V...> &v) {
    cerr << '[';
    FOR(i, 0, sz(v)) {
        if(i) cerr << ", ";
        __print(v[i]);
    }
    cerr << ']';
}

template<typename T1, typename T2, typename ...V>
void __print(const map<T1, T2, V...> &m) {
    cerr << '{';
    bool first = 1;
    for(const auto &x: m) {
        if(!first) cerr << ", ";
        first = 0;
        __print(x);
    }
    cerr << '}';
}

//debug
template<typename T>
void _print(const T &t) {__print(t);}
void _print() {}
template<typename T, typename ...V>
void _print(const T &t, const V &...v) {
  __print(t); 
  if(sizeof...(v)) cerr << ", "; 
  _print(v...); 
}

#ifdef LOCAL
#define deb(...) do {\
    cerr << "[In " <<  __func__ << "(): Line " << __LINE__ << "] [" << #__VA_ARGS__ << "] = [";\
    _print(__VA_ARGS__);\
    cerr << ']' << nl;\
} while(0);
#else
#define deb(...)
#endif
```
> Về "LOCAL": Trong Codeblocks, Vào Settings->Compiler/Compiler Settings, ở mục #defines nhập LOCAL vào 
{: .prompt-info }

## Testing:
{% raw %}
```c++
int a = 42;
double b = 3.1415;
string s = "Hello";
char c = 'Z';
bool ok = true;

pair<int, string> p = {7, "Seven"};
vector<int> v = {1, 2, 3, 4};
set<string> st = {"apple", "banana", "cherry"};
map<string, int> mp = {{"Alice", 10}, {"Bob", 20}};

vector<pair<int, int>> vp = {{1,2}, {3,4}, {5,6}};
map<int, vector<int>> mv = {{1,{2,3}}, {4,{5,6,7}}};
set<pair<int,int>> sp = {{1,2}, {3,4}};

deb(a);
deb(b);
deb(s);
deb(c);
deb(ok);
deb(p);
deb(v);
deb(st);
deb(mp);
deb(vp);
deb(mv);
deb(sp);

deb(a, b, s, p, v);
```
{% endraw %}