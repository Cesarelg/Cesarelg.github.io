---
title: Park
date: 2019-03-26 12:43:00
tags: [two points answer]
---

# $Park$



## $Description$

```
游乐园里新推出一个游戏——飞机驾驶体验。有*N*-1 个小朋友来玩这个游戏，他们排成长长的一列。
这个游戏一共有 M 架“飞机”可供驾驶，每架飞机的使用时间都是有限制的，第 i 架“飞机”的使用时
间是 si 分钟。 

一开始所有“飞机”都是闲置着的，然后排队等候着的小朋友依次上“飞机”。如果有飞机闲置，则小朋
友一定会上去玩(小朋友不会在意飞机使用的时间)；如果同时有多架“飞机”闲置着，小朋友一定会选
择标号最小的那架。 

现在你带着你的弟弟来到了游乐园，排到了最后一个位置。你想知道你弟弟会在哪架“飞机”上体验。
```



## $Solution$

考虑二分弟弟上飞机的时间。

$check$ 函数很好写，只需要将所有的 $s_i$ 在这么长时间内上去几个人算出来就好了。

二分出时间后，取时间减一， $n$ 与时间减一的差就是弟弟在这分钟上去飞机次序。

把所有在这一分钟满足条件的飞机取出，第 $n - res$ 即为答案。



## $Code:$

```cpp
#include<bits/stdc++.h>
#define debug(x) (cout << "#x = " << x << endl)
#define re register
#define For(i, j, k) for(re int i = j; i <= k; i++)
#define foR(i, j, k) for(re int i = j; i >= k; i--)
#define Cros(i, j, k) for(re int i = j; i; i = k)
using namespace std;
typedef long long ll;
const ll N = 100011;
const ll inf = 0x3f3f3f3f3f3f3f;

/*
    二分真**难调。 
*/

ll n, m, s[N];
ll t = 1, zz = 0, bei[N];

namespace IO {
    
    #define dd ch = getchar()
    inline ll read() {
        ll x = 0; bool f = 0; char dd;
        for (; !isdigit (ch); dd) f ^= (ch == '-');
        for (; isdigit (ch); dd) x = (x << 1) + (x << 3) + (ch ^ 48);
        return f? -x: x;
    } 
    #undef dd
    
    inline void write ( ll x ) {
        if ( x < 0 ) x = -x, putchar ('-');
        if ( x > 9 ) write ( x / 10 );
        putchar ( x % 10 | 48 );
    }
    
    inline void wrn ( ll x ) { write (x); putchar (' '); }
    
    inline void wln ( ll x ) { write (x); putchar ('\n'); }
    
    inline void wlnn ( ll x, ll y ) { wrn (x); wln (y); }
    
}

using namespace IO;

namespace Subtask1 {
    
    ll mn = inf;
    
    inline void main () { 
        For ( i, 1, m ) s[i] = read(), mn = min ( mn, s[i] );
        zz = m; if ( n - 1 <= m ) { wln (n - 1); exit (0); } 
        while (1) {
            t ++;
            For ( i, 1, m ) {    
    //            debug (t % s[i]);
                if ( t % s[i] == 0 ) zz++;
                if ( zz == n ) { wln (i); exit (0); }
            }
        }
    }
    
}

namespace Subtask {
    
    ll mx = 0, cnt = 0, alb[N];
    
    inline bool check ( ll ss ) {
        ll res = 0;
        For ( i, 1, m ) res += ss / s[i] + 1;
        return res >= n;
    }
    
    inline void main () {
        For ( i, 1, m ) s[i] = read(), mx = max ( mx, s[i] );
        ll l = 1, r = mx * n;
        while ( l <= r ) {
            ll Mid = ( l + r ) >> 1;
            check (Mid)? r = Mid - 1: l = Mid + 1;
        } // 二分在第几分钟上飞机。
//        wln (l);
        ll aa = l, res = 0, ress = 0, albb;
        For ( i, 1, m ) res += aa / s[i] + 1;
        For ( i, 1, m ) ress += (aa - 1) / s[i] + 1;
        albb = n - ress;
//        debug (ress);
//        ress -= res;
        For ( i, 1, m ) if ( aa % s[i] == 0 ) alb[++cnt] = i;
//        For ( i, 0, cnt - 1 ) wln ( alb[i] );
        wln ( alb [albb] );
    }
    
}

int main () {
    freopen("park.in", "r", stdin);
    freopen("park.out", "w", stdout);
    n = read(), m = read(); 
//    ( n <= 1000000 )? 
//    Subtask1::main (); 
    Subtask::main (); return 0;
    // Sherry, 
}

/*
22 5
1 2 3 4 5

4

31 5
1 2 3 4 5

3

32 5
1 2 3 4 5

4

33 5
1 2 3 4 5

1
*/

```



 