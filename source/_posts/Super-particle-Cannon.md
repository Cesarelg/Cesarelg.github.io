---
title: Super particle Cannon
date: 2019-03-22 21:06:59
tags: [math, Lucas, 数论分块]
---

# $Super$ $Particle$ $Cannon$

## $Description$

求
$$
\sum_{i = 0}^{k} C_n^i \bmod 2333
$$

## $Solution$

设 
$$
f(n, k) = \sum_{i = 0}^{k} C_n^i
$$
即
$$
\sum_{i = 0}^{k}C_{n / p}^{i / p} \times C_{n \% p}^{i \% p}
$$
数论分块一下，此处将其分成 $p - 1$ 个块，许多数字同属一个块，就一起算掉。
$$
\sum_{i = 0}^{p - 1}C_{n \% p}^{i}C_{n / p}^{0} + \sum_{i = 0}^{p - 1}C_{n \% p}^{i}C_{n / p}^{1} + ... + \sum_{i = 0}^{p - 1}C_{n \% p}^{i}C_{n / p}^{k / p - 1} + \sum_{i = 0}^{k \% p}C_{n \% p}^{i}C_{n / p}^{k / p}
$$
把 $\sum_{i = 0}^{p - 1}C_{n \% p}^{i}$ 提出来
$$
\sum_{i = 0}^{p - 1}C_{n \% p}^{i} \sum_{i = 0}^{k}C_{n / p}^{i / p - 1}
$$
也就是
$$
f(n \% p, p - 1) \times f(n / p, k / p - 1)
$$
最后一个不完整的块单独处理
$$
\sum_{i = 0}^{k \% p}C_{n \% p}^{i}
$$
即
$$
f(n \% p,k \% p)
$$
所以
$$
f(n, k) = f(n \% p, p - 1) \times f(n / p, k / p - 1) + C_{n / p}^{k / p} \times f(n \% p, k \% p)
$$
预处理组合数，套个 $Lucas$ 就完了。

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
const ll p = 2333;
const ll N = 2345;
const ll inf = 0x3f3f3f3f3f3f;

ll T, n, k, res, C[N + 11][N + 11], f[N + 11][N + 11];

namespace IO {

    inline ll read() {
        ll x = 0; bool f = 0; char ch = getchar();
        for (; !isdigit (ch); ch = getchar()) f ^= (ch == '-');
        for (; isdigit (ch); ch = getchar()) x = ( x << 3 ) + ( x << 1 ) + ( ch ^ 48 );
        return f? -x: x;
    }

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

inline ll Lucas ( ll n, ll m ) {
    if ( n < m ) return 0;
    if ( !m || n == m ) return 1;
    return C[n % p][m % p] * Lucas ( n / p, m / p ) % p;
}

inline ll F ( ll n, ll k ) {
    if ( k < 0 ) return 0;
    if ( !n || !k ) return 1;
    if ( n < p && k < p ) return f[n][k];
    return ( F (n / p, k / p - 1) * f[n % p][p - 1] % p 
        + Lucas ( n / p, k / p ) * f[n % p][k % p] % p ) % p;
}

int main() {
//  freopen(".in", "r", stdin);
//  freopen(".out", "w", stdout);
    f[0][0] = C[0][0] = 1; For ( i, 1, N )
        C[i][i] = C[i][0] = f[i][0] = 1;
    For ( i, 1, N ) For ( j, 1, i - 1 )
        C[i][j] = (C[i - 1][j] + C[i - 1][j - 1]) % p;
    For ( i, 0, N ) For ( j, 1, N ) 
        f[i][j] = ( C[i][j] + f[i][j - 1] ) % p;
    T = read(); while ( T-- ) n = read(), wln ( F (n, read()) );
    return 0;
}

/*
3
5 5
10 7
1145 14

32
968
763
*/
```

[题目链接](https://www.luogu.org/problemnew/show/P4345)