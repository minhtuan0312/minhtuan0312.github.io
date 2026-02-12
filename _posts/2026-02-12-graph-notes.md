---
layout: post
title: Một số template Lý thuyết đồ thị🐧
date: 2026-02-12 20:25 +0700
categories: [graph, template]
tags: [lý thuyết đồ thị]
math: true
---

## Khớp và cầu

### 1. Khớp
Setup
```c++
const int limN = 1005;
vector<int> adj[limN];
int n, m;
int disc[limN], low[limN];
set<int> ap;
int time_ = 0;
void dfs(int u, int par) {
    disc[u] = low[u] = ++time_;
    int child = 0;

    for(const auto &v : adj[u]) {
        if(v == par) continue;
        if(disc[v] == 0) {
            child++;
            dfs(v, u);
            minimize(low[u], low[v]);
            if(disc[u] <= low[v] && par != -1) {
                ap.insert(u);
            }
        } else {
            minimize(low[u], disc[v]);
        }
    }

    if(par == -1 && child > 1) { // đỉnh dfs tree
        ap.insert(u);
    }
}
```
Main
```c++
cin >> n >> m;
FOR(i, 1, m + 1) {
    int u, v; cin >> u >> v;
    adj[u].eb(v);
    adj[v].eb(u);
}
FOR(i, 1, n + 1) {
    if(disc[i] == 0) dfs(i, -1);
}
cout << sz(ap);
```

### 2. Cầu

Setup
```c++
const int limN = 1005;
int n, m;
vector<int> adj[limN];
int disc[limN], low[limN];
int time_ = 0;
vector<pair<int, int>>bridges;

void dfs(int u, int par) {
    disc[u] = low[u] = ++time_;
    for(const auto &v: adj[u]) {
        if(v == par) continue;
        if(disc[v] == 0) {
            dfs(v, u);
            minimize(low[u], low[v]);
            if(disc[u] < low[v]) {
                bridges.eb(u, v);
            }
        } else {
            minimize(low[u], disc[v]);
        }
    }
}
```

Main
```c++
cin >> n >> m;
FOR(i, 1, m + 1) {
    int u, v; cin >> u >> v;
    adj[u].eb(v);
    adj[v].eb(u);
}

FOR(i, 1, n + 1) {
    if(disc[i] == 0) {
        dfs(i, -1);
    }
}
cout << sz(bridges);
```

## Cây khung cực tiểu (Minimum Spanning Tree)

### Thuật toán Kruskal
Setup
```c++
int n, m;
struct edge{
    int u, v, w;
};
vector<edge> edges;

void Kruskal(){

    sort(all(edges), [&](const edge &x, const edge &y){
        return x.w < y.w;
    });

    vector<edge> tree;
    ll d = 0;
    disjoint_set_union dsu(n + 1);
    FOR(i, 0, m) {
        if(sz(tree) == n - 1) break;
        if(dsu.Unite(edges[i].u, edges[i].v)) {
            tree.pb(edges[i]);
            d += edges[i].w;
        }
    }
    if(sz(tree) != n - 1) cout << "IMPOSSIBLE";
    else cout << d;

}
```

Main
```c++
cin >> n >> m;
FOR(i, 1, m + 1) {
    int u, v, w; cin >> u >> v >> w;
    edges.pb({u, v, w});
}

Kruskal();
```

### Thuật toán Prim

Setup
```c++
const int limN = 1e5 + 5;
int n, m;
int used[limN];
vector<pair<int, int>> adj[limN];

void prim(int s) {
    priority_queue<pair<int, int>, vector<pair<int, int>>, greater<pair<int, int>>> Q;
    used[s] = 1;
    for(const auto &v: adj[s]) {
        Q.push({v.se, v.fi});
    }
    ll d = 0, dem = 0;
    while(!Q.empty()) {
        auto[w, u] = Q.top(); Q.pop();
        if(used[u] == 0) {
            d += w;
            dem++;
            used[u] = 1;
            for(const auto &v: adj[u]) {
                if(used[v.fi] == 0) {
                    Q.push({v.se, v.fi});
                }
            }
        }
    }
    if(dem == n - 1) return cout << d, void();
    return cout << "IMPOSSIBLE", void();
}
```

Main
```c++
cin >> n >> m;
FOR(i, 1, m + 1){
    int u, v, w; cin >> u >> v >> w;
    adj[u].eb(v, w);
    adj[v].eb(u, w);
}

prim(1);
```

## Tìm đường đi ngắn nhất
### Thuật toán Dijkstra
Setup
```c++
typedef pair<ll, int> ii;
const int limN = 1005;
int n, m, s;
vector<ii> adj[limN];
ll dist[limN];

void dijkstra(int s) {
    memset(dist, 0x3f, sizeof dist);
    priority_queue<ii, vector<ii>, greater<ii>> pq;
    dist[s] = 0;
    pq.push({0, s});
    while(!pq.empty()) {
        auto [d, u] = pq.top(); pq.pop();
        if(d > dist[u]) continue;
        for(auto [v, w]: adj[u]) {
            if(dist[v] > d + w) {
                dist[v] = d + w;
                pq.push({dist[v], v});
            }
        }
    }
}
```
Main
```c++
cin >> n >> m >> s;
FOR(i, 1, m + 1) {
    int u, v, w; cin >> u >> v >> w;
    adj[u].eb(v, w);
    adj[v].eb(u, w);
}

dijkstra(s);
FOR(i, 1, n + 1) {
    cout << dist[i] << ' ';
}
```

### Thuật toán Bellman Ford
Setup
```c++
int const INF = 1e9 + 7;
int const limN = 1005;
vector<tuple<int, int, ll>> edges;
int n, m, s;
ll dist[limN];

void bellmanford(int s) {
    memset(dist, 0x3f, sizeof dist);
    dist[s] = 0;
    FOR(i, 1, n) {
        for(auto [u, v, w]: edges) {
            if(dist[u] < INF) {
                minimize(dist[v], dist[u] + w);
            }
        }
    }
}
```

Main
```c++
cin >> n >> m >> s;
FOR(i, 1, m + 1) {
    int u, v, w; cin >> u >> v >> w;
    edges.pb({u, v, w});
    edges.pb({v, u, w});
}

bellmanford(s);

FOR(i, 1, n + 1) {
    cout << dist[i] << ' ';
}
```

### Thuật toán Floyd Warshall
```c++
const int limN = 105;
ll dist[limN][limN];

memset(dist, 0x3f, sizeof dist);
FOR(i, 1, limN) {
    dist[i][i] = 0;
}

int n, m, s; cin >> n >> m >> s;
FOR(i, 1, m + 1) {
    int u, v, w; cin >> u >> v >> w;
    dist[u][v] = dist[v][u] = w;
}
FOR(k, 1, n + 1) {
    FOR(i, 1, n + 1) {
        FOR(j, 1, n + 1) {
            minimize(dist[i][j], dist[i][k] + dist[k][j]);
        }
    }
}
FOR(i, 1, n + 1) {
    cout << dist[s][i] << ' ';
}
```