---
title: Get lost
date: 2019-03-28 16:01:51
tags: [math, matrix]
---

# $Get$ $lost$ 

## $Description$

```
windy在一个有向图中迷路了。 
该有向图有 N 个节点，windy从节点 0 出发，必须恰好在 T 时刻到达节点 N-1。 现给出该有向图，请问总共有多少种不同的路径。 注意：windy不能在某个节点逗留，且通过某有向边的时间严格为给定的时间。
```

## $Solution$

先考虑矩阵边权只有 $0/1$ 的情况。

对于这种情况，直接设 $f_1$ 为给定矩阵， $f_n[i][j]$ 表示 $i \to j$ 路径长度为 $n$ 的路径条数。

此时答案就是
$$
f_n[i][j] = \sum_{k = 1}^{n} f_{n - 1}[i][k] \times f_1[k][j]
$$
由于矩阵乘法满足结合律，所以有 $f_n = f_{1}^{n}$ 。

然后考虑边权 $w \in [0, 9] \bigcap Z$ 的情况。

我们发现由于此时边权可能大于 $1$ ，所以不能直接转换成矩阵。

由于观察到 $n <= 10$ ，我们可以考虑暴力将每个点拆成 $9$ 个点 ，并按顺序连边。

所以就是如果从 $i \to j$ 上有一条长度为 $w$ 的边，就从 $i$ 的第 $w - 1$ 个点向 $j$ 连一条边。

之后做矩阵快速幂，答案就是 $f[1][n]$ 。

## $Code:$

```cpp
#include<bits/stdc++.h>
//#include<tr1/unordered_map>
//#include"Bignum/bignum.h"
//#define lll bignum
#define ls(x) ( x << 1 )
#define rs(x) ( x << 1 | 1 )
//#define mid ( ( l + r ) >> 1 )
#define lowbit(x) ( x & -x )
#define debug(x) (cout << "#x = " << x << endl)
#define re register
#define For(i, j, k) for(re int i = (j); i <= (k); i++)
#define foR(i, j, k) for(re int i = (j); i >= (k); i--)
#define Cross(i, j, k) for(re int i = (j); i; i = (k))
using namespace std;
typedef long long ll;
const ll P = 2009;
const ll N = 111;
const ll inf = 0x3f3f3f3f3f3f;

ll n, T;

struct Matrix {
    ll mp[N][N];
    Matrix () { memset ( mp, 0, sizeof (mp) ); }

    friend Matrix operator *( Matrix a, Matrix b ) {
        Matrix res;
        For ( i, 1, n * 9 ) For ( k, 1, n * 9 ) For ( j, 1, n * 9 )
            res.mp[i][j] = (res.mp[i][j] + a.mp[i][k] * b.mp[k][j]) % P;
        return res;
    }

    friend Matrix operator ^( Matrix a, ll b ) {
        Matrix res;
        For ( i, 1, n * 9 ) res.mp[i][i] = 1;
        for (; b; b >>= 1, a = a * a) res = (b & 1)? res * a: res;
        return res;
    }
} Ans;

namespace IO {

    #define dd ch = getchar()
    inline ll read() {
        ll x = 0; bool f = 0; char dd;
        for (; !isdigit (ch); dd) f ^= (ch == '-');
        for (; isdigit (ch); dd)  x = (x << 3) + (x << 1) + (ch ^ 48);
        return f? -x: x;
    }
    #undef dd

    inline void write( ll x ) {
        if ( x < 0 ) putchar ( '-' ), x = -x;
        if ( x > 9 ) write ( x / 10 );
        putchar ( x % 10 | 48 );
    }

    inline void wrn ( ll x ) { write (x); putchar ( ' ' ); }

    inline void wln ( ll x ) { write (x); putchar ( '\n' ); }

    inline void wlnn ( ll x, ll y ) { wrn (x), wln (y); }

}

using namespace IO;

int main() {
//    freopen(".in", "r", stdin);
//    freopen(".out", "w", stdout);
    n = read(), T = read();
    For ( i, 1, n ) For ( j, 1, 8 )
        Ans.mp[i + j * n][i + (j - 1) * n] = 1;
    For ( i, 1, n ) For ( j, 1, n ) {
        ll x; scanf ("%1d", &x);
        if (x) Ans.mp[i][j + (x - 1) * n] = 1;
    } 
    return wln ( (Ans ^ T).mp[1][n] ), 0;
}

/*
5 30
12045
07105
47805
12024
12345

852
*/

```

[题目链接](<https://www.luogu.org/problemnew/show/P4159>)