---
title: 51nod 七星剑
date: 2019-05-07 08:59:48
tags: [math, probability, expectation]
---

# 七星剑



## $Description$

![](https://s2.ax1x.com/2019/05/07/Er8FG4.png)



## $Solution$

设 $f_i$ 表示镶 $i$ 颗魔法石的期望。
$$
f_i = C_j + f_{i - 1} + (f_i - f_{i - 1 - Lose[i][j]}) \times (1 - Prod_{i, j})
$$

$$
f_i \times Prod_{i, j} = C_j + f_{i - 1} - (1 - Prod_{i, j}) \times f_{i - 1 - Lose[i][j]}
$$

$$
f_i = \frac{C_j + f_{i - 1} - f_{i - 1 - Lose[i][j]} \times (1 - Prod_{i, j})}{Prod_{i, j}}
$$



## $Code:$

```cpp
#include <bits/stdc++.h>
//#include<tr1/unordered_map>
//#include"Bignum/bignum.h"
//#define lll bignum
#define ls(x) ( x << 1 )
#define rs(x) ( x << 1 | 1 )
//#define mid ( ( l + r ) >> 1 )
#define lowbit(x) ( x & -x )
#define debug(x) (cout << "#x = " << (x) << endl)
#define Set(x, i) memset (x, i, sizeof(x))
#define re register
#define For(i, j, k) for(re int i = (j); i <= (k); ++i)
#define foR(i, j, k) for(re int i = (j); i >= (k); --i)
#define Cross(i, j, k) for(re int i = (j); i; i = (k))
using namespace std;
typedef long long ll;
typedef unsigned long long ull;
const ll N = 111;
const double eps = 1e-7;
const double inf = 1e52;

ll n, C[N], Lose[11][N];
double f[N], Prod[11][N];

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
        if ( x < 0 ) putchar ('-'), x = -x;
        if ( x > 9 ) write ( x / 10 ); putchar ( x % 10 | 48 );
    }

    inline void wrn ( ll x ) { write (x); putchar (' '); }

    inline void wln ( ll x ) { write (x); putchar ('\n'); }

    inline void wlnn ( ll x, ll y ) { wrn (x), wln (y); }

}

using namespace IO;

int main()
{
//  freopen(".in", "r", stdin);
//  freopen(".out", "w", stdout);
    n = read();
    For ( i, 1, 7 ) f[i] = inf;
    For ( i, 1, n ) C[i] = read();
    For ( i, 1, 7 ) For ( j, 1, n ) cin >> Prod[i][j];
    For ( i, 1, 7 ) For ( j, 1, n ) cin >> Lose[i][j];
    For ( i, 1, 7 ) {
        ll f = 0;
        For ( j, 1, n ) f |= Prod[i][j] > eps;
        if (!f) return puts ("-1"), 0;
    }
    For ( i, 1, 7 ) For ( j, 1, n ) {
        f[i] = min ( f[i], ( f[i - 1] + C[j] - 
            f[i - 1 - Lose[i][j]] * (1.0 - Prod[i][j]) ) / Prod[i][j] );
    } 
    return printf ("%.12lf", f[7]), 0;
}

/*

*/

```

[题目链接](<http://www.51nod.com/Challenge/Problem.html#!#problemId=1705>)


