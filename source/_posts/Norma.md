---
title: Norma
date: 2019-06-20 18:40:22
tags: [分治, CDQ 分治]
---

# $Norma$

![](https://s2.ax1x.com/2019/06/20/Vv4lNj.png)



## $Description$

三种情况。

$1. j \in [mid + 1, p)$ 

区间最值就是当前求出来的最值，所以直接累计高斯公式求和就好了。
$$
min \times max \times \sum_{j = mid + 1}^{p - 1} j - i + 1
$$
$2. j \in [p, q)$

区间最大值还是当前的 $max$ ，只是当前的区间最小值变了，因为你的 $j$ 包含了比 $min$ 更小的值。

所以要预处理一个前缀最小值 $mns$ ，并记录 $mns \times j$ 的前缀和 $mnp$ 。

如此可以得到式子
$$
max \times \sum_{j = p}^{q - 1} mns_j \times (q - p + 1)
$$
化简得
$$
max \times \sum_{j = p}^{q - 1} mns_j \times q - max \times \sum_{j = p}^{q - 1} mns_j \times (i - 1)
$$
$3. j \in [q, r]$ 

对于这种情况，区间的最大值和最小值都改变了，所以要处理 $max \times min$ 以及 $max \times min \times j$ 的前缀和，然后再做。

这种情况与第二种情况其实类似，所以记 $max \times min$ 的前缀和为 $mnx$ ， $max \times min \times j$ 的前缀和为 $sum$ ，就有
$$
max \times \sum_{j = p}^{q - 1} mnx_j \times (q - p + 1)
$$
一样的化简套路，得
$$
max \times \sum_{j = p}^{q - 1} mnx_j \times q - max \times \sum_{j = p}^{q - 1} mnx_j \times (i - 1)
$$
考虑右边区间对于左边的影响，令 $p, q$ 指针单调移动， $CDQ$ 分治即可，复杂度 $O(n\ log\ n)$ 。



## $Code:$

```cpp
#include <bits/stdc++.h>
//#include <tr1/unordered_map>
//#include"Bignum/bignum.h"
//#define lll bignum
#define lowbit(x) (x & -x)
#define debug(x) (cout << "#x = " << (x) << endl)
#define Set(x, i) memset (x, i, sizeof(x))
#define R register
#define For(i, j, k) for(R int i = (j); i <= (k); ++i)
#define foR(i, j, k) for(R int i = (j); i >= (k); --i)
#define Cross(i, j, k) for(R int i = (j); i; i = (k))
using namespace std;
typedef long long ll;
const ll N = 500011;
const ll INF = 5e16;
const ll P = 1e9;

ll n, a[N];

namespace IO {

    inline char gc() {
        static char buf[100000], *p1 = buf, *p2 = buf;
        return (p1 == p2) && (p2 = (p1 = buf) +
            fread (buf, 1, 100000, stdin), p1 == p2)? EOF: *p1++;
    }

    #define dd ch = getchar()
    inline ll read() {
        ll x = 0; bool f = 0; char dd;
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

namespace Subtask1 {
    
    ll Ans = 0;
    
    void main () {
        For ( l, 1, n ) {
            ll Min = INF, Max = -INF;
            For ( r, l, n ) {
                Min = min (Min, a[r]);
                Max = max (Max, a[r]);
                Ans = (Ans + Min * Max * (r - l + 1)) % P;
            }
        } wln (Ans), exit (0);
    }
    
}

namespace Subtask2 {
    
    ll Ans = 0, Mxs[N], Mns[N], Mxp[N], Mnp[N], Mnx[N], Sum[N];
    
    inline void Add ( ll x ) { Ans = (Ans + x) % P; }
    
    inline ll S ( ll l, ll r ) 
        { return (l + r) * (r - l + 1) / 2 % P; }
    
    inline void CDQ ( ll l, ll r ) {
        if (l == r) return (void) (Add (a[l] * a[l]));
        ll Mid = (l + r) >> 1; CDQ (l, Mid); CDQ (Mid + 1, r);
        ll Min = INF, Max = -INF, p = Mid + 1, q = Mid + 1, i, j;
        Mxs[Mid] = Mns[Mid] = Mxp[Mid] = Mnp[Mid] = Mnx[Mid] = Sum[Mid] = 0;
        
        for (j = Mid + 1; j <= r; ++j) {
            Min = min (Min, a[j]);
            Max = max (Max, a[j]);
            Mns[j] = (Mns[j - 1] + Min) % P;
            Mxs[j] = (Mxs[j - 1] + Max) % P;
            Mnp[j] = (Mnp[j - 1] + Min * j) % P;
            Mxp[j] = (Mxp[j - 1] + Max * j) % P;
            Mnx[j] = (Mnx[j - 1] + Min * Max) % P;
            Sum[j] = (Sum[j - 1] + Min * Max % P * j) % P;
        } // 预处理本段的前缀和。 
        Min = INF, Max = -INF;
        for (i = Mid; i >= l; --i) {
            Min = min (Min, a[i]);
            Max = max (Max, a[i]);
            while (p <= r && Min < a[p]) ++p;
            while (q <= r && Max > a[q]) ++q;
            if (p < q) {
                Add (Min * Max % P * S(Mid - i + 2, p - i));
                Add (Max * (Mnp[q - 1] - Mnp[p - 1] + P) + 
                     Max * (Mns[q - 1] - Mns[p - 1] + P) % P * (1 - i + P));
                Add (Sum[r] - Sum[q - 1] + 
                    (Mnx[r] - Mnx[q - 1] + P) * (1 - i + P) % P);
            } else {
                Add (Min * Max % P * S(Mid - i + 2, q - i));
                Add (Min * (Mxp[p - 1] - Mxp[q - 1] + P) + 
                     Min * (Mxs[p - 1] - Mxs[q - 1] + P) % P * (1 - i + P));
                Add (Sum[r] - Sum[p - 1] + 
                    (Mnx[r] - Mnx[p - 1] + P) * (1 - i + P) % P);
            }
        }
    }
    
    void main () {
        CDQ (1, n); 
        wln ((Ans + P) % P); exit (0);
    }
    
}

int main()
{
//  freopen("3745.in", "r", stdin);
//  freopen("3745.out", "w", stdout);
    n = read();
    For ( i, 1, n ) a[i] = read();
    if (n <= 3000) Subtask1::main ();
    else Subtask2::main (); return 0;
}

/*

*/

```

[题目链接](<https://www.spoj.com/problems/NORMA2/>)

