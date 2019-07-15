---
title: ABC 132F
date: 2019-07-15 21:04:04
tags: [dp, 分块]
---

# $ABC\ 132F$ 



## $Description$

给出 $n, k$ ，求由正整数组成的长度为 $k$ 的序列的个数，并使任意两个相邻元素的乘积最多为 $<= n$ ，答案对 $10 ^ 9 + 7$ 取模。



## $Solution$

首先很容易看出与除法分块相关。

$yy$ 一下发现第二层答案就是 $\sum_{i = 1}^{n}\dfrac{n}{i}$ ，然后后面的答案要根据这个拓展出去。

设  $f_{i, j}$ 表示做到第 $i$ 层，第 $i$ 层做到第 $j$ 个块的答案。

转移从 $f_{i, j - 1}$ 继承，然后考虑第 $j$ 个块元素答案均为 $\dfrac{l}{n}$ ，考虑这个块的元素的贡献就是 $\dfrac{n}{n/l} \times (r - l + 1)$  ，就有转移方程
$$
f_{i, j} = f_{i, j - 1} + f_{i - 1, cnt - j + 1} \times (r_j - l_j + 1)
$$
初始化 $f_{1, i} = r_i$ 表示起始答案就是到这个块的最大能取的元素个数，直接转移即可。



## $Code:$

```cpp
#include <bits/stdc++.h>
//#include <tr1/unordered_map>
//#include"Bignum/bignum.h"
//#define big bignum
#define lowbit(x) (x & -x)
#define debug(x) (cout << "#x = " << (x) << endl)
#define Set(x, i) memset (x, i, sizeof(x))
#define re register
#define For(i, j, k) for(re int i = (j), ED = (k); i <= ED; ++i)
#define Rep(i, j, k) for(re int i = (j), ED = (k); i >= ED; --i)
#define Cross(i, j, k) for(re int i = (j); i; i = (k))
using namespace std;
typedef long long ll;
const ll N = 111, M = 31624;
const ll INF = 5e16;
const ll P = 1e9 + 7;

namespace IO {

    inline char gc() {
        static char buf[100000], *p1 = buf, *p2 = buf;
        return (p1 == p2) && (p2 = (p1 = buf) +
            fread (buf, 1, 100000, stdin), p1 == p2)? EOF: *p1++;
    }

    #define dd ch = getchar()
    inline ll read() {
        re ll x = 0; re int f = 0; re char dd;
        for (; !isdigit (ch); dd) f ^= (ch == '-');
        for (; isdigit (ch); dd)  x = x * 10 + (ch ^ 48);
        return f? -x: x;
    }
    #undef dd

    inline void write( ll x ) {
        if (x < 0) putchar ('-'), x = -x;
        if (x > 9) write (x / 10); putchar (x % 10 | 48);
    }

    inline void wrn ( ll x ) { write (x); putchar (' '); }

    inline void wln ( ll x ) { write (x); putchar ('\n'); }

    inline void wlnn ( ll x, ll y ) { wrn (x), wln (y); }

}

using IO::wln;
using IO::read;

namespace Cesare {
    
    ll n, k, cc = 0, L[M << 1], R[M << 1], f[N][M << 1];
    
    /*
        n/1 + n/2 + n/3 + n/4 + ... + n/(n-1) + n/n
    */
    
    inline ll power ( ll a, ll b, ll res = 1 ) {
        for (; b; b >>= 1, a = a * a % P) 
            res = (b & 1)? res * a % P: res; return res;
    }
    
    inline ll Inv ( ll a ) { return power(a, P - 2); }
    
    inline ll dfs ( ll x, ll y, ll res = 0 ) {
        if (x == k) return y;
        for (ll l = 1, r; l <= y; l = r + 1) 
            r = min (n, n / (n / l)), 
            res = (res + dfs(x + 1, n / l) * (r - l + 1) % P) % P;
        return res;
    }
    
    inline void Add ( ll &a, ll b ) { (a += b) >= P && (a -= P); }
    
    int main() {
        n = read(), k = read(); 
        for (ll l = 1, r; l <= n; l = r + 1) 
            L[++cc] = l, R[cc] = r = min (n, n / (n / l));
        For ( i, 1, cc ) f[1][i] = R[i];
        For ( i, 2, k ) For ( j, 1, cc ) 
            f[i][j] = f[i][j - 1], 
            Add(f[i][j], f[i - 1][cc - j + 1] * (R[j] - L[j] + 1) % P);
        return wln (f[k][cc]), 0;
    }
    
}

int main()
{
    return Cesare::main();
}

/*
10 2

10 3
*/


```

[题目链接](https://atcoder.jp/contests/abc132/tasks/abc132_f)