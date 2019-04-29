---
title: 51nod 最后的机会
date: 2019-04-29 19:36:08
tags: [two points answer, segment tree]
---

# $51nod$ 最后的机会



## $Description$

![](https://s2.ax1x.com/2019/04/29/E117C9.png)



## $Solution$

首先可以~~套路的~~将把元音字母变成 $-1$ ，辅音字母变成 $2$ ，然后做一遍前缀和，这样只要 $sum_j - sum_{i - 1} >= 0$ 即可判断该字符串满足条件。

然后考虑如何优化这个枚举找最大的 $j - i + 1$ 的过程。

可以发现对于一个 $i$ ，我们只关心距离他最远的满足条件的 $j$ ，所以我们只要每次取出这个 $j$ ，然后对最终长度取 $MAX$ 即可。

如果暴力的取 $j$ ，复杂度是 $\Theta(n^2)$ 的，对于 $2 \times 10^5$ 的数据范围显然不能接受。

我们发现，如果只用取出一个 $sum_j$ ，使他尽可能大，那么我们可以用线段树来优化这个查找的过程。

可以构建一棵维护区间最大值的线段树，然后在线段树上从右往左找第一个满足条件的端点，找到即退出。

注意题目要求最大，所以从右往左找。



## $Code:$

```cpp
#include<bits/stdc++.h>
//#include<tr1/unordered_map>
//#include"Bignum/bignum.h"
//#define lll bignum
#define ls(x) ( x << 1 )
#define rs(x) ( x << 1 | 1 )
#define mid ( ( l + r ) >> 1 )
#define lowbit(x) ( x & -x )
#define debug(x) (cout << "#x = " << (x) << endl)
#define Set(x, i) memset (x, i, sizeof(x))
#define re register
#define For(i, j, k) for(re int i = (j); i <= (k); ++i)
#define foR(i, j, k) for(re int i = (j); i >= (k); --i)
#define Cross(i, j, k) for(re int i = (j); i; i = (k))
using namespace std;
typedef long long ll;
const ll N = 200011;
const ll inf = 0x3f3f3f3f3f3f;

string s;
ll Ans = 0, pos = 0, a[N], Sum[N];

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

namespace Segment_Tree {
    
    ll ans[N << 2];
    
    inline void pushUp ( ll p ) { ans[p] = max (ans[ls(p)], ans[rs(p)]); }
    
    inline void Build ( ll p, ll l, ll r ) {
        if ( l == r ) return (void) (ans[p] = Sum[l]);
        Build ( ls(p), l, mid ); Build ( rs(p), mid + 1, r ); pushUp (p);
    }
    
    inline void Query ( ll p, ll l, ll r, ll ul, ll ur, ll k, ll &Ans ) {
        if ( Ans != -1 ) return ; 
        if ( l >= ul && r <= ur ) { 
            if ( l == r ) Ans = (ans[p] >= k)? l: Ans; 
            else {
                if ( ans[rs(p)] >= k ) 
                    Query ( rs(p), mid + 1, r, ul, ur, k, Ans );
                if ( ans[ls(p)] >= k ) Query ( ls(p), l, mid, ul, ur, k, Ans );
            } return ;
        } 
        if ( mid < ur )  
            Query ( rs(p), mid + 1, r, ul, ur, k, Ans );
        if ( mid >= ul ) Query ( ls(p), l, mid, ul, ur, k, Ans );
    }
    
}

using namespace Segment_Tree;

int main()
{
//  freopen(".in", "r", stdin);
//  freopen(".out", "w", stdout);
    cin >> s; For ( i, 0, s.size() - 1 )
        a[i + 1] = (s[i] == 'a' || s[i] == 'e' || 
            s[i] == 'i' || s[i] == 'o' || s[i] == 'u')? -1: 2;
    ll n = s.size();
    For ( i, 1, n ) Sum[i] = Sum[i - 1] + a[i];

    Build (1ll, 1, n); 

    ll Pos;
    For ( i, 1, n ) {
        Pos = -1;
        Query ( 1, 1, n, i, n, Sum[i - 1], Pos );
        if ( Pos != -1 ) Ans = max (Ans, Pos - i + 1);
    }
//    foR ( i, n, 1 ) For ( j, 1, i )
//        if ( Sum[i] - Sum[j - 1] >= 0 ) Ans = max (Ans, (ll) i - j + 1);
//    foR ( i, n, 1 ) For ( j, 1, i ) 
//        if ( Sum[i] - Sum[j - 1] >= 0 && Ans == i - j + 1 ) ++pos;
    if ( !Ans ) 
        return puts("No solution"), 0; 
    For ( i, 1, n - Ans + 1 )
        if ( Sum[i + Ans - 1] - Sum[i - 1] >= 0 ) ++pos;
    return wlnn (Ans, pos), 0;
}

/*

*/

```

[题目链接](<https://www.51nod.com/Challenge/Problem.html#!#problemId=1701>)



