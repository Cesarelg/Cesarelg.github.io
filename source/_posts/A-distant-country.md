---
title: A distant country
date: 2019-03-15 17:56:27
tags: Tree chain partition
---

## 遥远的国度题解

### $Description$

给出一棵树，需要支持换根、区间查询、区间修改等操作。

### $Solution$

除了换根操作，其他都是树链剖分板子。

对于换根操作，我们考虑先以一号节点为根节点做一次树剖，然后对于换根的操作分类讨论。

接下来以 $ root ​$ 表示目前的根节点， $ x ​$ 表示 $ x ​$ 子树。

$1.​$ $x == root​$

对于这种情况，根就是全局最小值，直接输出根即可。

$2.$ $x \in root \bigcap root \in 1$ $\|$ $x \notin root \bigcap x \ne root \bigcap x \notin (1 \to root)$   

对于这种情况，哪个为根对于 $ x ​$ 子树没有影响，所以直接查询。

$3.$ $x \in (1 \to root)$

当 $ x $ 在它们所属的链上时，$ x $ 的子树就是除去往 $ root $ 方向的子树以外的所有子树。

所以把这些不去要的节点找出并去掉，就是答案了。

![](https://s2.ax1x.com/2019/03/20/AlIeMD.png)

看下图，感性理解一下。



## $ Code :$

```cpp
#include<bits/stdc++.h>
#include<tr1/unordered_map>
//#include"Bignum/bignum.h"
//#define lll bignum
#define ls(x) ( x << 1 )
#define rs(x) ( x << 1 | 1 )
#define mid ( ( l + r ) >> 1 )
#define lowbit(x) ( x & -x )
#define debug(x) cout << "#x = " << x << endl
#define re register
#define For(i, j, k) for(re int i = j; i <= k; i++)
#define foR(i, j, k) for(re int i = j; i >= k; i--)
using namespace std;
typedef long long ll;
const ll N = 100011;
const ll inf = 0x3f3f3f3f3f3f;

ll n, T, ID, lg[N], def[N];

struct edge {
    ll to, next;
} e[N << 1];

ll cnt, head[N];

inline void add ( ll u, ll v ) {
    e[++cnt].to = v, e[cnt].next = head[u], head[u] = cnt;
    e[++cnt].to = u, e[cnt].next = head[v], head[v] = cnt;
}

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

namespace Chain_Tree {
    
    ll fa[25][N], dep[N], size[N], Bigson[N];
    
    inline void dfs1 ( ll u, ll fath ) {
        fa[0][u] = fath;
        for ( re int i = 1; ( 1 << i ) <= dep[u]; i++ )
            fa[i][u] = fa[i - 1][fa[i - 1][u]];
        // 预处理倍增数组。 
        
        dep[u] = dep[fath] + 1, size[u] = 1; 
        for ( re int i = head[u]; i; i = e[i].next )
            if ( e[i].to != fath ) {
                ll v = e[i].to;
                dfs1 ( v, u ); size[u] += size[v];
                if ( size[v] > size[Bigson[u]] ) Bigson[u] = v;
            }
    }
    
    ll tot = 0, id[N], rk[N], top[N];
    
    inline void dfs2 ( ll u, ll now_top ) {
        id[u] = ++tot, rk[tot] = u, top[u] = now_top;
        if ( !Bigson[u] ) return ; dfs2 ( Bigson[u], now_top );
        for ( re int i = head[u]; i; i = e[i].next )
            if ( e[i].to != fa[0][u] && e[i].to != Bigson[u] ) dfs2 ( e[i].to, e[i].to );
    }
    
} // 树剖板子。 

using namespace Chain_Tree;

namespace Segment_Tree {
        
    ll ans[N << 2], tag[N << 2];
    
    inline void push_up ( ll p ) { ans[p] = min ( ans[ls(p)], ans[rs(p)] ); }
    
    inline void push_down ( ll p ) {
        if ( tag[p] ) {
            tag[ls(p)] = tag[rs(p)] = tag[p];
            ans[ls(p)] = ans[rs(p)] = tag[p];
            tag[p] = 0;
        } // 修改元素答案为 tag 。 
    }
    
    inline void build ( ll p, ll l, ll r ) {
        if ( l == r ) { ans[p] = def[rk[l]]; return ; }
        build ( ls(p), l, mid ); build ( rs(p), mid + 1, r ); push_up (p);
    }
    
    inline void update ( ll p, ll l, ll r, ll ul, ll ur, ll k ) {
        if ( l >= ul && r <= ur ) { ans[p] = tag[p] = k; return ; } 
        push_down (p);
        if ( mid >= ul ) update ( ls(p), l, mid, ul, ur, k );
        if ( mid < ur )  update ( rs(p), mid + 1, r, ul, ur, k );
        push_up (p);
    }
    
    inline ll query ( ll p, ll l, ll r, ll ul, ll ur ) {
        if ( l >= ul && r <= ur ) return ans[p];
        push_down (p); ll res = inf;
        if ( mid >= ul ) res = min ( res, query ( ls(p), l, mid, ul, ur ) );
        if ( mid < ur )  res = min ( res, query ( rs(p), mid + 1, r, ul, ur ) );
        return res;
    }
    
}

using namespace Segment_Tree;

inline void modify ( ll x, ll y, ll k ) {
    while ( top[x] != top[y] ) {
        if ( dep[top[x]] < dep[top[y]] ) swap ( x, y );
        update ( 1, 1, n, top[id[x]], id[x], k ); x = fa[0][top[x]];
    }
    if ( id[x] > id[y] ) swap ( x, y );
    update ( 1, 1, n, id[x], id[y], k );
}

inline ll Find ( ll x, ll y ) {
    For ( i, lg[y], 0 ) 
        if ( y >= ( 1 << i ) ) 
            y -= 1 << i, x = fa[i][x];
    return x;
} // 倍增找祖先。 

ll u, v;

int main() {
//  freopen(".in", "r", stdin);
//  freopen(".out", "w", stdout);
    n = read(), T = read(); lg[0] = -1;
    For ( i, 1, n ) lg[i] = lg[i >> 1] + 1;
    For ( i, 1, n - 1 ) u = read(), add ( u, read() );
    For ( i, 1, n ) def[i] = read(); ID = read();
    
    dfs1 ( 1, 0 ); dfs2 ( 1, 1 ); build ( 1, 1, n );
    
    while ( T-- ) {
        ll opt = read();
        switch ( opt ) {
            case 1: ID = read(); break;
            case 2: u = read(), v = read(), modify ( u, v, read() ); break;
            case 3:
                u = read();
                if ( u == ID ) { wln ( ans[1] ); break; }
                if ( Find ( ID, dep[ID] - dep[u] ) == u ) {
                    v = Find ( ID, dep[ID] - dep[u] - 1 );
                    ll aa = query ( 1, 1, n, 1, id[v] - 1 );
                    ll aaa = ( id[u] + size[v] <= n )? 
                        query ( 1, 1, n, id[v] + size[v], n ): inf;
                    wln ( min ( aa, aaa ) );
                }
                else wln ( query ( 1, 1, n, id[u], id[u] + size[u] - 1 ) );
                // 此处分类讨论三种情况。 
        } // 重点。 
    } return 0;
}

/*

*/


```

[题目链接](https://www.luogu.org/problemnew/show/P3979)

