---
title: Sakura
date: 2019-03-21 10:37:30
tags: math
---

# $Sakura$

## $Description$

求 $\frac{1}{x} + \frac{1}{y} = \frac{1}{n!}​$ 的答案方案个数。

## $Solution$

设 
$$
x = n! - a, y = n! - b, n = n!
$$
代入，得
$$
\frac{2n - a - b}{(n - a)(n - b)} = \frac{1}{n}
$$
化简，移项，得
$$
2n^2 - (a + b)n = n^2 - (a + b)n + ab
$$
故有
$$
ab = n^2
$$
筛出 $n^2$ 的因子即可。

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
#define For(i, j, k) for(re ll i = (j); i <= (k); i++)
#define foR(i, j, k) for(re ll i = (j); i >= (k); i--)
#define cross(i, k) for(re int i = head[k]; i; i = e[i].next)
using namespace std;
typedef long long ll;
const ll N = 2000011;
const ll p = 1e9 + 7;
const ll inf = 0x3f3f3f3f3f3f;

ll n;

namespace IO {

    inline ll read() {
        ll x = 0; bool f = 0; char ch = getchar();
        for (; !isdigit (ch); ch = getchar()) f^= (ch == '-');
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

}

using namespace IO;

ll ans = 1, cnt, c[N], pr[N], vis[N];

inline void Init () {
    For ( i, 2, n ) {
        if ( !vis[i] ) vis[i] = pr[++cnt] = i;
        for ( re int j = 1; j <= cnt && i * pr[j] <= n; j++ ) {
            if ( pr[j] > vis[i] ) break;
            vis[i * pr[j]] = pr[j];
        }
    }
}

int main() {
//  freopen(".in", "r", stdin);
//  freopen(".out", "w", stdout);
    n = read(); Init ();
    For ( i, 1, cnt ) {
        ll P = pr[i];
        for ( re ll j = P; j <= n; j *= P ) 
            c[i] += n / j; c[i] %= p; // 直接分解质因数。
    }
    For ( i, 1, cnt ) ans = ans * (c[i] * 2 + 1) % p;
    return wln ( ans ), 0;
}

/*
1439
*/

```

[题目链接](https://www.lydsy.com/JudgeOnline/problem.php?id=2721)

