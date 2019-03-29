---
title: C Looooops
date: 2019-03-26 17:35:00
tags: [math, exgcd]
---

# $C$ $Looooops$

## $Description$

求
$$
Cx + A \equiv B \pmod {2^k}
$$

## $Solution$

化简本式：
$$
Cx + 2^{k}y = B - A
$$
所以：
$$
a = C, b = 2^k, c = B - A, d = gcd(a, b)
$$
代入求解即可。

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
const ll N = 100011;
const ll inf = 0x3f3f3f3f3f3f;

ll A, B, C, k;

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

inline void exgcd ( ll a, ll b, ll &d, ll &x, ll &y ) {
    if (b) exgcd ( b, a % b, d, y, x ), y -= a / b * x;
    else d = a, x = 1, y = 0; return ;
}

int main() {
//  freopen("c.in", "r", stdin);
//  freopen("test.out", "w", stdout);
    while ( cin >> A >> B >> C >> k && (A || B || C || k) ) {
        ll a = C, b = 1ll << k, c = B - A;
        ll d, x, y; exgcd ( a, b, d, x, y );
        if (c % d) { puts("FOREVER"); continue; }
        x = x * (c / d); b /= d; x = (x % b + b) % b; wln (x);
    } return 0;
}

/*
3 3 2 16
3 7 2 16
7 3 2 16
3 4 2 16
0 0 0 0
*/


```

[题目链接](<http://ybt.ssoier.cn:8088/problem_show.php?pid=1640>)

