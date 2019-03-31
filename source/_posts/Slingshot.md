---
title: Slingshot
date: 2019-03-18 10:03:21
tags: [category talk, segment tree]
---

# $Slingshot$

对于本题，我们进行分类讨论：

$ 1. $ 

![](https://s2.ax1x.com/2019/03/20/Al5IKg.png)

对于这种情况，$x \to y$ 包含了 $s \to t$ ，所以此时的选择有两种：

① 先从 $s$ 走到 $x$ ，然后穿到 $y$ ，再从 $y$ 走到 $t$ 。

② 直接从 $s$ 走到 $t$ 。

然后我们 $yy$ 一下，如何用线段树维护多个 $x \to y$ 只选一个最优的 $x \to y$ 的情况。

显然，这是个线段树维护区间最小值裸题。

所以，由于我们根据 $x$ 端点排序，所以只需要将 ① 情况的路程插入线段树中的 $ y $ 节点的位置即可。

对于这种情况，路程就是 
$$
ti + (s - x) + (y - t)
$$
在线段树中插入这个东东即可。

$ 2. $ 

![](https://s2.ax1x.com/2019/03/20/AlIkPx.png)

对于这种情况， $ s \to t $ 包含了 $ x \to y $ 的情况，所以路程就是
$$
ti + (x - s) + (t - y)
$$
$ 3. $

![](https://s2.ax1x.com/2019/03/20/AlIERK.png)

对于这种情况， $ s \to t $ 包含了 $ x $ ，不包含 $ y $ ，所以路程就是
$$
ti + (s - x) + (t - y)
$$
$ 4. $

![](https://s2.ax1x.com/2019/03/20/AlIVxO.png)

与 $ 3 $ 同理，只是包含情况反一下，也就是
$$
ti + (x - s) + (y - t)
$$
离散化一下，四种情况各建一棵线段树就完了。

## $ Code: $

```cpp
#include<bits/stdc++.h>
#include<tr1/unordered_map>
#define debug(x) cout << "#x = " << x << endl
#define ls(x) (x << 1)
#define rs(x) (x << 1 | 1)
#define mid ((l + r) >> 1)
#define re register
#define For(i, j, k) for(re int i = j; i <= k; i++)
#define foR(i, j, k) for(re int i = j; i >= k; i--)
using namespace std;
typedef long long ll;
const ll N = 200011;
const ll inf = 0x3f3f3f3f3f3f3f;

ll n, m, cnt, rep[N];

struct node {
    ll x, y, w, id;
} p[N];

inline bool cmp ( node a, node b ) { return a.x == b.x? a.x < b.y: a.x < b.x; }

namespace IO {

    inline ll read() {
        ll x = 0; bool f = 0; char ch = getchar();
        for (; !isdigit (ch); ch = getchar()) f ^= ( ch == '-' );
        for (; isdigit (ch); ch = getchar()) x = ( x << 1 ) + ( x << 3 ) + ( ch ^ 48 );
        return f? -x: x;
    }
    
    inline void write ( ll x ) {
        if ( x < 0 ) x = -x, putchar ( '-' );
        if ( x > 9 ) write ( x / 10 );
        putchar ( x % 10 | 48 );
    }
    
    inline void wln ( ll x ) { write (x); putchar ( '\n' ); }

}

using namespace IO;

namespace Segment_Tree {
    
    struct Tree {
        ll mn;
    } ans[N << 2];
    
    inline void push_up ( ll p ) { ans[p].mn = min ( ans[ls(p)].mn, ans[rs(p)].mn ); }
    
    inline void build ( ll p, ll l, ll r ) {
        if ( l == r ) { ans[p].mn = inf; return ; }
        build ( ls(p), l, mid ); build ( rs(p), mid + 1, r ); push_up (p);
    }
    
    inline void Insert ( ll p, ll l, ll r, ll x, ll k ) {
        if ( l == r ) { ans[p].mn = min ( k, ans[p].mn ); return ; }
        if ( mid >= x ) Insert ( ls(p), l, mid, x, k );
        if ( mid < x )  Insert ( rs(p), mid + 1, r, x, k );
        push_up (p);
    }
    
    inline ll Query ( ll p, ll l, ll r, ll ul, ll ur ) {
        if ( l >= ul && r <= ur ) return ans[p].mn;
        if ( mid >= ur ) return Query ( ls(p), l, mid, ul, ur ); else 
        if ( mid < ul )  return Query ( rs(p), mid + 1, r, ul, ur ); else 
        return min ( Query ( ls(p), l, mid, ul, ur ), Query ( rs(p), mid + 1, r, ul, ur ) );
    }
    
}

using namespace Segment_Tree;

ll u, v, w, s, t, _x[N], _y[N];

int main () {
    freopen ("ztn.in", "r", stdin);
    freopen ("ztn.out", "w", stdout);
    n = read(), m = read();
    For ( i, 1, n ) {
        u = read(), v = read(), w = read();
        p[++cnt].x = u, p[cnt].y = v, p[cnt].w = w, p[cnt].id = 0;
        _x[cnt] = u, _y[cnt]  =v;
    }
    
    For ( i, 1, m ) {
        s = read(), t = read();
        p[++cnt].x = s, p[cnt].y = t, p[cnt].w = 0, p[cnt].id = i;
        _x[cnt] = s, _y[cnt]  =t; rep[i] = abs ( s - t );
    }
    
    /* -----------------离散化----------------- */
    
    sort ( p + 1, p + cnt + 1, cmp );
    
    sort ( _x + 1, _x + cnt + 1 );
    ll len = unique ( _x + 1, _x + cnt + 1 ) - _x - 1;
    
    For ( i, 1, cnt )
        p[i].x = lower_bound ( _x + 1, _x + len + 1, p[i].x ) - _x;
    
    sort ( _y + 1, _y + cnt + 1 );
    len = unique ( _y + 1, _y + cnt + 1 ) - _y - 1;
    
    For ( i, 1, cnt ) 
        p[i].y = lower_bound ( _y + 1, _y + len + 1, p[i].y ) - _y;
        
    /* ---------------------------------- */
    
    /* ----------------分类讨论------------------ */
    
    build ( 1, 1, cnt );
    For ( i, 1, cnt )
        if ( !p[i].id )
            Insert ( 1, 1, cnt, p[i].y, p[i].w -_x[p[i].x] - _y[p[i].y] );
        else 
            rep[p[i].id] = min ( rep[p[i].id], 
                Query ( 1, 1, cnt, 1, p[i].y ) + _x[p[i].x] + _y[p[i].y] );
    // x -> y 包含 s ，不包含 t 。

    build ( 1, 1, cnt );
    For ( i, 1, cnt )
        if ( !p[i].id )
            Insert ( 1, 1, cnt, p[i].y, p[i].w -_x[p[i].x] + _y[p[i].y] );
        else 
            rep[p[i].id] = min ( rep[p[i].id], 
                Query ( 1, 1, cnt, p[i].y, cnt ) + _x[p[i].x] - _y[p[i].y] );
    // s -> t 包含 x -> y
    
    build ( 1, 1, cnt );
    foR ( i, cnt, 1 )
        if ( !p[i].id )
            Insert ( 1, 1, cnt, p[i].y, p[i].w + _x[p[i].x] + _y[p[i].y] );
        else 
            rep[p[i].id] = min ( rep[p[i].id], 
                Query ( 1, 1, cnt, p[i].y, cnt ) - _x[p[i].x] - _y[p[i].y] );
    // x -> y 包含 t ， 不包含 s 。        
    
    build ( 1, 1, cnt );
    foR ( i, cnt, 1 )
        if ( !p[i].id )
            Insert ( 1, 1, cnt, p[i].y, p[i].w + _x[p[i].x] - _y[p[i].y] );
        else 
            rep[p[i].id] = min ( rep[p[i].id], 
                Query ( 1, 1, cnt, 1, p[i].y ) - _x[p[i].x] + _y[p[i].y] );
    // x -> y 包含 s -> t
    
    /* ---------------------------------- */
    
    For ( i, 1, m ) wln ( rep[i] ); return 0;
}

/*
2 3
0 10 1
13 8 2
1 12
5 2
20 7
*/

```



另外，关于循环的问题为什么有两种正序，两种倒序呢？

让我们再看一遍先前的例图：

前两个正序的：

![](https://s2.ax1x.com/2019/03/20/AlIERK.png)

![](https://s2.ax1x.com/2019/03/20/AlIkPx.png)

后两个倒序的：

![](https://s2.ax1x.com/2019/03/20/Al5IKg.png)

![](https://s2.ax1x.com/2019/03/20/AlIVxO.png)

发现了吗，两者之间的奥秘。

留给读者自行证明。



从前面开始扫，由于 $ y $ 是排过序的，你插入的点就会在 $ s \to t $ 之间，否则有可能在外面，就是不优的，是错的。

[题目链接](https://www.luogu.org/problemnew/show/P4088)