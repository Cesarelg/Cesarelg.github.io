---
title: SmartYanzi
date: 2019-03-21 18:42:43
tags: math
---

# $Smart$ $Yanzi$

## $Description$  

给你一个数 $S$ ，求有多少数的约数和等于 $S$ 。

## $Solution$

对于一个数 $S$ ，根据算数基本定理，有分解式：
$$
S = p_1^{a_1}p_2^{a_2}p_3^{a_3}...p_n^{a_n}
$$
所以约数和就是
$$
sum = \prod_{i = 1}^{n}\sum_{j=0}^{a_i}p_i^j
$$
由于 $S<=2*10^9$ ，考虑枚举 $S$ 的所有因子，先筛出 $\sqrt{S}$ 以内的质数，然后枚举 $p_i$ ，对于每个 $p_i$ 枚举 $a_i$ ，爆搜，如果将 $S$ 分解成功，答案 $ + 1$ 。

同时，如果 $S - 1$ 是一个质数，且 $>=$ 当前搜的 $p_i$ ，答案也要 $ + 1$ ，因为 $S - 1$ 也是此时构造出的数的因子。

其实我们是先把质因子全部取出来，然后找所有的乘起来的可能，对于每种可能都枚举它是否成立。

就是把 $S$ 拆掉，然后再拼回去。

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
const ll N = 100011;
const ll inf = 0x3f3f3f3f3f3f;

ll S, tot = 0, di[N << 2];

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

    inline void wlnn ( ll x, ll y ) { wrn (x), wln (y); }

}

using namespace IO;

ll cnt, pr[N], vis[N];

inline void Init () {
    For ( i, 2, N ) {
        if ( !vis[i] ) pr[++cnt] = i;
        for ( re int j = 1; j <= cnt && i * pr[j] <= N; j++ ) {
            vis[i * pr[j]] = 1;
            if ( i % pr[j] == 0 ) break;
        }
    }
} // 预处理质数。 

inline bool check ( ll n ) {
    if ( n == 1 ) return 0;
    if ( n <= N ) return !vis[n];
    for ( re int i = 1; pr[i] * pr[i] <= n; i++ )
        if ( n % pr[i] == 0 ) return 0;
    return 1;
} // 判断质数。 

inline void dfs ( ll now, ll x, ll y ) { // x 表示当前用到第几个质数，y 表示当前搜出的数。
    if ( now == 1 ) { di[++tot] = y; return ; } // 满足答案。
    if ( now - 1 >= pr[x] && check ( now - 1 ) ) di[++tot] = y * ( now - 1 );
    /* 
        如果当前数能表示成一个质数加一与一的乘积，那么就会是答案，只需要将
        当前算出的答案乘上它就是满足条件的答案。
    */ 
    ll p, temp;
    for ( re int i = x; pr[i] * pr[i] <= now; i++ ) {
        temp = pr[i], p = pr[i] + 1;
        for (; p <= now; temp *= pr[i], p += temp)
            if ( now % p == 0 ) dfs ( now / p, i + 1, y * temp );
    } // 枚举符合题意的因子。
}

int main() {
//  freopen(".in", "r", stdin);
//  freopen(".out", "w", stdout);
    Init ();
    while ( cin >> S ) {
        tot = 0; memset ( di, 0, sizeof ( di ) );
        dfs ( S, 1ll, 1ll ); sort ( di + 1, di + tot + 1 ); // 从小到大输出。 
        wln (tot); For ( i, 1, tot - 1 ) wrn ( di[i] ); if (tot) wln ( di[tot] );
    } return 0;
}

/*
42

20 26 41
*/

```



[题目链接](https://www.luogu.org/problemnew/show/P4397)