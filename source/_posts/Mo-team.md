---
title: Mo team
date: 2019-05-07 14:45:28
tags: [Moteam]
---

# $MoTeam$



莫队是一种暴力算法，用于求解一类区间计数问题。



## 普通莫队

给你一个有 $n$ 个元素的序列， $m$ 个询问，每个询问有 $l,\ r$ ，询问 $l - r$ 这段区间不同数字的出现个数。

先考虑暴力，每次 $l - r$ 循环一次统计答案，复杂度 $\Theta (nm)$ 。

然后考虑优化。

发现可以把每次区间的左右端点记录下来，由已知的信息推出未知的信息。

但复杂度上界并没有改变，还是 $\Theta (nm)$ 。

考虑离线把所有左右端点记录下来，以左端点为第一关键字，右端点为第二关键字排序，如此使得移动次数最少。

还有优化就是按照奇偶性排序，据说快一倍。

给出一种方式：

```cpp
inline bool cmp ( MoTeam a, Moteam b ) {
    return (a.l / len) ^ (b.l / len)?
        a.l < b.l: ( (a.l / len & 1)? a.r < b.r: a.r > b.r );
}
```

然后就暴力，根据题目要求更改 $add$ 与 $del$ 函数即可。

给出实现：

```cpp
	while ( R < Q[i].r ) add ( a[++R] );
	while ( R > Q[i].r ) del ( a[R--] );
	while ( L < Q[i].l ) del ( a[L++] );
	while ( L > Q[i].l ) add ( a[--L] );
```



## 带修莫队

带修莫队顾名思义，就是有单点修改的莫队，此时我们考虑再记一个 $Time$ 表示距离本次查询最近的修改，如果我们莫队改的少了就再改几次，改的多了就改回去，以此来避免修改操作对答案造成的影响。

同时我们多记一个 $Update$ 数组，来存储修改操作，再在问题数组中也记一个 $Time$ 记录当前查询发生在哪次修改之后，然后回答时把序列改成此次修改之后的序列再操作。

其他的就与莫队本身无区别了。

下面给出一道[例题](<https://www.luogu.org/problemnew/show/P1903>)的代码

### $Code:$

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
const ll N = 1000011;
const ll inf = 0x3f3f3f3f3f3f;

ll n, m, len, res, cnt[N], col[N], Ans[N];

ll QNum;

struct MoTeam {
    ll l, r, id, Time;
} Q[N];

inline bool cmp ( MoTeam a, MoTeam b ) {
    return (a.l / len) ^ (b.l / len)?
        a.l < b.l: ( (a.r / len) ^ (b.r / len)? 
            ( (a.l / len & 1)? a.r < b.r: a.r > b.r ): a.Time < b.Time );
}

ll UNum;

struct Node {
    ll x, col;
} Up[N];

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

inline void add ( ll x ) { res += !cnt[x]++; }

inline void del ( ll x ) { res -= !--cnt[x]; }

inline void upd ( ll x, ll i ) {
    if ( Up[x].x >= Q[i].l )
        if ( Up[x].x <= Q[i].r ) 
            del (col[Up[x].x]), add (Up[x].col);
    swap (col[Up[x].x], Up[x].col);
}

int main()
{
//	freopen(".in", "r", stdin);
//	freopen(".out", "w", stdout);
    n = read(), m = read();
    len = pow (n, 2.0 / 3.35);
    For ( i, 1, n ) col[i] = read();
    For ( i, 1, m ) {
        char opt; cin >> opt;
        if ( opt == 'Q' ) {
            ++QNum;
            Q[QNum].l = read(), Q[QNum].r = read();
            Q[QNum].Time = UNum, Q[QNum].id = QNum;
        } else Up[++UNum].x = read(), Up[UNum].col = read();
    }
    ll L = 1, R = 0, Time = 0;
    sort (Q + 1, Q + QNum + 1, cmp);
    For ( i, 1, QNum ) {
        while ( L < Q[i].l ) del (col[L++]);
        while ( L > Q[i].l ) add (col[--L]);
        while ( R < Q[i].r ) add (col[++R]);
        while ( R > Q[i].r ) del (col[R--]);
        while ( Time < Q[i].Time ) upd (++Time, i);
        while ( Time > Q[i].Time ) upd (Time--, i);
        Ans[Q[i].id] = res;
    }
    For ( i, 1, QNum ) wln (Ans[i]);
    return 0;
}

/*

*/


```





## 树上莫队

~~其实就是把莫队求的东西换了一换，核心代码不变。~~

用于解决求 $x \to y$ 的路径上的问题。

我们考虑有什么东西可以把树上的问题转化到序列上。

这时候就要用到一种东西，欧拉序。

就是访问一个点 $u$ ，把他加进序列，然后遍历他的子树，然后再把他加进序列。

我们维护一个 $st$ 数组和一个 $ed$ 数组， $st_i$ 表示刚访问到点 $i$ 加入序列的时间， $ed_i$ 表示回溯时访问到点 $i$ 的时间。

对于一对 $x,\ y$ 我们钦定 $st_x < st_y$ ，然后分类讨论。

$1.$ $LCA(x, y) = x$ 。

此时 $x$ 是 $y$ 的祖先，即 $x$ 与 $y$ 在同一条链上，所以只需要统计出现过只一次的点即可。

$2.$ $LCA (x, y) \neq x$ 

此时 $x,\ y$ 位于不同的子树内，那么我们只需要统计 $ed_x \to st_y$ 这条路径上的点。

注意还要特判 $LCA$ ，因为之前统计的时候并没有算进去，~~手摸一下就知道了~~。

就没了，然后就是代码实现问题了。



给出[例题](<https://www.luogu.org/problemnew/show/SP10707>)完整代码

### $Code:$

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
const ll N = 100011;
const ll inf = 0x3f3f3f3f3f3f;

ll St[N], Ed[N];
ll n, m, len, res, a[N], b[N], P[N], cnt[N], Ans[N];

struct MoTeam {
    ll l, r, id, add;
} Q[N];

inline bool cmp ( MoTeam a, MoTeam b ) {
    return (a.l / len) ^ (b.l / len)?
        a.l < b.l: ( (a.l / len & 1)? a.r < b.r: a.r > b.r );
}

struct Edge {
    ll To, Next;
} e[N << 1];

ll Cnt = 0, head[N];

inline void add ( ll u, ll v ) {
    e[++Cnt].To = v, e[Cnt].Next = head[u], head[u] = Cnt;
    e[++Cnt].To = u, e[Cnt].Next = head[v], head[v] = Cnt;
}

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

namespace Tree {

    ll tot = 0, dep[N], f[21][N];
    
    inline void dfs ( ll u, ll fa ) {
        dep[u] = dep[fa] + 1, 
        P[++tot] = u, St[u] = tot;
        For ( i, 0, 15 ) f[i + 1][u] = f[i][f[i][u]];
        Cross ( i, head[u], e[i].Next ) 
            if ( fa ^ e[i].To ) f[0][e[i].To] = u, dfs (e[i].To, u);
        P[++tot] = u, Ed[u] = tot;
    } // 预处理倍增数组、欧拉序， dfs 序。 
    
    inline ll LCA ( ll x, ll y ) {
        if ( dep[x] < dep[y] ) swap (x, y);
        foR ( i, 16, 0 ) 
            if ( dep[f[i][x]] >= dep[y] ) x = f[i][x];
        if ( x == y ) return x;
        foR ( i, 16, 0 )
            if ( f[i][x] ^ f[i][y] ) x = f[i][x], y = f[i][y];
        return f[0][x];
    } // 倍增求 LCA 。 

}

using namespace Tree;

ll Vis[N];

inline void Add ( ll x ) {
    if ( Vis[x] ) res -= (--cnt[a[x]] == 0);
    else res += (++cnt[a[x]] == 1); Vis[x] ^= 1;
}

int main()
{
//  freopen(".in", "r", stdin);
//  freopen(".out", "w", stdout);
    ll u, v;
    n = read(), m = read();
    len = pow (n, 2.0 / 3.35);
    For ( i, 1, n ) a[i] = b[i] = read();
    sort (b + 1, b + n + 1);
    ll Len = unique (b + 1, b + n + 1) - b - 1;
    For ( i, 1, n ) 
        a[i] = lower_bound (b + 1, b + Len + 1, a[i]) - b;
    For ( i, 2, n ) u = read(), add ( u, read() );
    
    dfs (1, 0);
    For ( i, 1, m ) {
        ll l = read();
        ll r = read(), lca = LCA (l, r);
        if ( St[r] < St[l] ) swap (l, r);
        if ( l == lca ) Q[i] = (MoTeam) { St[l], St[r], i, 0 };
        if ( l != lca ) Q[i] = (MoTeam) { Ed[l], St[r], i, lca };
    }
    
    ll L = 1, R = 0;
    sort ( Q + 1, Q + m + 1, cmp );
    For ( i, 1, m ) {
        while ( L < Q[i].l ) Add (P[L++]);
        while ( L > Q[i].l ) Add (P[--L]);
        while ( R < Q[i].r ) Add (P[++R]);
        while ( R > Q[i].r ) Add (P[R--]);
        if ( Q[i].add ) Add ( Q[i].add );
        Ans[Q[i].id] = res; 
        if ( Q[i].add ) Add ( Q[i].add );
    }
    For ( i, 1, m ) wln (Ans[i]); return 0;
}

/*

*/

```

