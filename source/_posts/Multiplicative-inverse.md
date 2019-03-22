---
title: Multiplicative inverse
date: 2019-03-22 7:22:07
tags: [math, Study Notes]
---

# $linear$ $inverse$ $element$

## $Descirption$

给出 $k​$ ，求它的逆元。

## $Solution$

设
$$
a \times k + b = p,{b \times inv[b]}\equiv1\pmod p
$$
变式，得
$$
b = p - a \times k,{(p - a \times k) \times inv[b]}\equiv1\pmod p
$$

拆括号，得
$$
{p \times inv[b] - a \times k \times inv[b]}\equiv1\pmod p
$$
即
$$
{-a \times k \times inv[b]}\equiv1\pmod p
$$
观察
$$
a \times k + b = p
$$
两边同是对 $k$ 取模，得
$$
b = p \bmod k
$$
故有
$$
a = p \div k
$$
代入原式，得
$$
{-(p / k) \times inv[p \bmod k] \times k}\equiv 1 \pmod p
$$
即
$$
{-(p / k) \times inv[p \bmod k]} \equiv inv[k] \pmod p
$$

也就是
$$
{(p-p / k) \times inv[p \bmod k]} \equiv inv[k] \pmod p
$$

## $Code:$

```cpp
#include<bits/stdc++.h>
//#include"Bignum/bignum.h"
//#define lll bignum
#define ls(x) ( x << 1 )
#define rs(x) ( x << 1 | 1 )
//#define mid ( ( l + r ) >> 1 )
#define lowbit(x) ( x & -x )
#define debug(x) cout << "#x = " << x << endl
#define re register
#define For(i, j, k) for(re int i = j; i <= k; i++)
#define foR(i, j, k) for(re int i = j; i >= k; i--)
using namespace std;
typedef long long ll;
const ll N = 200011;
const ll inf = 0x3f3f3f3f3f3f;

ll n, p, f[N];

namespace IO {

    inline ll read() {
        ll x = 0; bool f = 0; char ch = getchar();
        for(; !isdigit( ch ); ch = getchar()) f^=( ch == '-' );
        for(; isdigit( ch ); ch = getchar()) x = ( x << 3 ) + ( x << 1 ) + ( ch ^ 48 );
        return f? -x: x;
    }

    inline void write( ll x ) {
        if( x < 0 ) putchar( '-' ), x = -x;
        if( x > 9 ) write( x / 10 );
        putchar( x % 10 | 48 );
    }

    inline void wln( ll x ) { write( x ); putchar( '\n' ); }

}

using namespace IO;

int main() {
//  freopen(".in", "r", stdin);
//  freopen(".out", "w", stdout);
    n = read(), p = read(); f[1] = 1;
    For ( i, 2, n ) f[i] = ( p - p / i ) * f[p % i] % p;
    For ( i, 1, n ) wln ( f[i] ); return 0;
}

/*

*/

```

