---
title: freeway
date: 2019-04-08 08:45:40
tags: [math, probability, expectation, segment tree, 经典套路]
---

# $freeway$

$ps:$ 有点毒的线段树加概率期望，感觉大致有点理解了概率期望的含义，跟平均数有点像？计数就是把所有答案的和除以方案个数。

还有一个小套路？下面讲。



## $Description$

![](https://s2.ax1x.com/2019/04/03/Ag1AYR.png)



## $Solution$

区间求和，区间查询，显然是线段树题。

然后我们考虑如何进行区间查询。

先考虑求 $l \to r$ 的花费，用一个前缀和数组 $sum$ 记一下，显然花费就是 $sum_r - sum_{l + 1}$ 。

所以期望就是
$$
\dfrac{\sum_{i = l}^{r}\sum_{j = i}^{r}(sum_j - sum_i)}{C_{r - l + 1}^{2}}
$$
然后我们考虑如何求出上面这个式子。

分母显然就是 $(r - l + 1) \times (r - l) / 2$ ，然后我们考虑如何求分子。

然后就用到一个推导套路 (?) ，对于区间 $[l, r]$ ，考虑枚举区间内每个数被包含了多少次。

比如
$$
l \in [l, l], [l, l + 1] \ldots ,[l, r]
$$
所以分子就是
$$
\sum_{i = l}^{r}a_i \times (r - i + 1) \times (i - l + 1)
$$
然后把他拆开，再化简
$$
\sum_{i = l}^{r}a_i\times(i\times(l + r) + (-i^2) + (r - l + 1 - rl))
$$
即
$$
\sum_{i = l}^{r}a_i\times(r - l + 1 - rl) + \sum_{i = l}^{r}i \times (l + r) + \sum_{i = l}^{r}(-i^2)
$$
线段树维护 $\sum_{i = l}^{r}a_i, \sum_{i = l}^{r}a_i \times i, \sum_{i = l}^{r} \times a_i \times i^2$ 这三个东西就好了。

然后考虑如何维护。

假如现在对于一段区间加上一个 $v$ ，那么
$$
sum_1 += (r - l + 1) \times v
$$

$$
sum_2 += ((l + r) \times (r - l + 1) / 2) \times v
$$

$$
sum_3 += \frac{r(r + 1)(2r + 1) - l(l - 1)(2l - 1)}{6} \times v
$$

线段树即可。

$ps:$ 第三个就是用到了求 $\sum_{i  = 1}^{n}i^2$ 的公式，即 $\dfrac{n(n + 1)(2n + 1)}{6}$ 。



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
#define debug(x) (cout << "#x = " << x << endl)
#define re register
#define For(i, j, k) for(re int i = (j); i <= (k); i++)
#define foR(i, j, k) for(re int i = (j); i >= (k); i--)
#define Cross(i, j, k) for(re int i = (j); i; i = (k))
using namespace std;
typedef long long ll;
const ll N = 100011;
const ll inf = 0x3f3f3f3f3f3f;

ll n, T;

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

    inline void wrn ( ll x ) { write (x); putchar ( '/' ); }

    inline void wln ( ll x ) { write (x); putchar ( '\n' ); }

    inline void wlnn ( ll x, ll y ) { wrn (x), wln (y); }

}

using namespace IO;

inline ll gcd ( ll a, ll b ) { return !b? a: gcd ( b, a % b ); }

namespace Segment_Tree {
    
    struct Tree {
        ll tag, sum[6];
        Tree() { memset ( sum, 0, sizeof (sum) ); }
    } ans[N << 2];
    
    inline void push_up ( ll p ) {
        For ( i, 4, 5 )
            ans[p].sum[i] = ans[ls(p)].sum[i] + ans[rs(p)].sum[i];
    }
    
    inline void pushUp ( ll p ) { 
        For ( i, 1, 3 )
            ans[p].sum[i] = ans[ls(p)].sum[i] + ans[rs(p)].sum[i]; 
    }
    
    inline void pushDown ( ll p, ll l, ll r ) {
        if ( ans[p].tag ) {
            ans[rs(p)].tag += ans[p].tag;
            ans[ls(p)].tag += ans[p].tag, 
            ans[rs(p)].sum[1] += (r - mid) * ans[p].tag; 
            ans[ls(p)].sum[1] += (mid - l + 1) * ans[p].tag;
            
            ans[ls(p)].sum[2] += ans[ls(p)].sum[4] * ans[p].tag;
            ans[rs(p)].sum[2] += ans[rs(p)].sum[4] * ans[p].tag;
            ans[ls(p)].sum[3] += ans[ls(p)].sum[5] * ans[p].tag;
            ans[rs(p)].sum[3] += ans[rs(p)].sum[5] * ans[p].tag;
            
            ans[p].tag = 0;
        }
    }
    
    inline void build ( ll p, ll l, ll r ) {
        if ( l == r ) 
            return (void) (ans[p].sum[4] = l, ans[p].sum[5] = l * l);
        build ( ls(p), l, mid ); build ( rs(p), mid + 1, r ); push_up (p);
    }
    
    inline void Update ( ll p, ll l, ll r, ll ul, ll ur, ll k ) {
        if ( l >= ul && r <= ur ) 
            return (void) (ans[p].tag += k, 
                ans[p].sum[1] += (r - l + 1) * k, 
                    ans[p].sum[2] += ans[p].sum[4] * k,
                        ans[p].sum[3] += ans[p].sum[5] * k);
        pushDown ( p, l, r );
        if ( mid >= ul ) Update ( ls(p), l, mid, ul, ur, k );
        if ( mid < ur )  Update ( rs(p), mid + 1, r, ul, ur, k ); 
        pushUp (p);
    }
    
    inline Tree Query ( ll p, ll l, ll r, ll ul, ll ur ) {
        if ( l >= ul && r <= ur ) return ans[p];
        Tree res; 
        if ( ans[p].tag ) pushDown ( p, l, r );
        if ( mid >= ul ) {
            Tree Ans = Query ( ls(p), l, mid, ul, ur );
            res.sum[1] += Ans.sum[1], 
                res.sum[2] += Ans.sum[2], res.sum[3] += Ans.sum[3];
        } if ( mid < ur ) {
            Tree Ans = Query ( rs(p), mid + 1, r, ul, ur );
            res.sum[1] += Ans.sum[1], 
                res.sum[2] += Ans.sum[2], res.sum[3] += Ans.sum[3];
        } return res;
    }
    
}

using namespace Segment_Tree;

string opt;
ll x, y, k;

int main() {
//  freopen("2752.in", "r", stdin);
//  freopen("2752.out", "w", stdout);
    n = read(), T = read();
    
    build ( 1, 1, n );
    
    while ( T-- ) {
        cin >> opt;
        x = read(), y = read(); 
        switch ( opt[0] ) {
            case 'C': 
                k = read();
                if ( x == y ) break;
                Update ( 1, 1, n, x + 1, y, k ); break;
            case 'Q': 
                Tree res = Query ( 1, 1, n, x + 1, y );
                ll fz = 
                    (y - (x + 1) - (x + 1) * y + 1) 
                        * res.sum[1] + (x + y + 1) * res.sum[2] - res.sum[3];
                ll fm = (y - x + 1) * (y - x) >> 1;
                ll d = gcd ( fz, fm );
//                debug (d);
                wlnn ( fz / d, fm / d ); break;
        }
    } return 0;
}

/*
4 5
C 1 4 2
C 1 2 -1
Q 1 2
Q 2 4
Q 1 4

1/1
8/3
17/6
*/

```

[题目链接](<https://www.luogu.org/problemnew/show/P2221>)