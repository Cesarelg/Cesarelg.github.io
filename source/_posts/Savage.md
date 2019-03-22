---
title: Savage
date: 2019-03-22 14:55:16
tags: [math, exgcd]
---

# $Savage$

## $Description$

给出 $n$ 个野人，初始在第 $C_i$ 个洞穴，每个人一年能走 $P_i$ 个洞穴，寿命是 $L_i$ 年，所有人在一个环上走，求最小满足所有人互不碰到的环的长度。

## $Solution$

由题意得，我们需要使 $C_{i} + P_i \times x \equiv C_j + P_j \times x \bmod M$ $(x <=1, min(L_i, L_j))$ $\|$ $x > min(L_i, L_j)$ 无解。

考虑化简式子：
$$
(P_i - P_j) \times x \equiv C_j - C_i \pmod M
$$
移项，变成 $exgcd$ 方程组：
$$
(P_i - P_j) \times x + M \times y = C_j - C_i
$$
枚举 $M​$ ，解方程即可。

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
#define debug(x) cout << "#x = " << x << endl
#define re register
#define For(i, j, k) for(re int i = (j); i <= (k); i++)
#define foR(i, j, k) for(re int i = (j); i >= (k); i--)
#define Cross(i, j, k) for(re int i = (j); i; i = (k))
using namespace std;
typedef long long ll;
const ll N = 111;
const ll inf = 0x3f3f3f3f3f3f;

ll n, mx, C[N], P[N], L[N];

namespace IO {

    inline ll read() {
        ll x = 0; bool f = 0; char ch = getchar();
        for (; !isdigit (ch); ch = getchar()) f ^= (ch == '-');
        for (; isdigit (ch); ch = getchar()) x = (x << 3) + (x << 1) + (ch ^ 48);
        return f? -x: x;
    }

    inline void write( ll x ) {
        if ( x < 0 ) putchar ('-'), x = -x;
        if ( x > 9 ) write (x / 10);
        putchar (x % 10 | 48);
    }

    inline void wrn ( ll x ) { write (x); putchar ( ' ' ); }

    inline void wln ( ll x ) { write (x); putchar ( '\n' ); }

    inline void wlnn ( ll x, ll y ) { wrn (x), wln (y); }

}

using namespace IO;

inline ll gcd ( ll a, ll b ) { return !b? a: gcd (b, a % b); }

inline void exgcd ( ll a, ll b, ll &d, ll &x, ll &y ) {
    if (b) { exgcd (b, a % b, d, y, x); y -= a / b * x; }
    else d = a, x = 1, y = 0; return ;
}

inline bool check ( ll M ) {
    For ( i, 1, n ) For ( j, i + 1, n ) {
        ll d, a = P[i] - P[j], b = M, 
            v = C[j] - C[i], x = 0, y = 0, GCD = gcd ( a, b );
        if ( v % GCD == 0 ) {
            a /= GCD, b /= GCD, v /= GCD;
            exgcd ( a, b, d, x, y ); b = b < 0? -b: b;
            x = (x * v % b + b) % b; x = !x? x + b: x;
            if ( x <= min ( L[i], L[j] ) ) return false;
        }
    } return 1;
}

int main() {
//  freopen(".in", "r", stdin);
//  freopen(".out", "w", stdout);
    n = read(); For ( i, 1, n ) 
        mx = max (mx, C[i] = read()), P[i] = read(), L[i] = read();
    for (re int i = mx; ; i++)
        if ( check (i) ) return wln (i), 0;
    return 0;
}

/*
3
1 3 4
2 7 3
3 2 1

6
*/
```

[题目链接](https://www.luogu.org/problemnew/show/P2421)

