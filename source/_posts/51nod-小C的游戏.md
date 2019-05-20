---
title: 51nod 小C的游戏
date: 2019-05-20 09:11:39
tags: [打表， game theory]
---

# 小 $C$ 的游戏



## $Description$



## $Solution$ 

~~打表好题~~

首先约定 $P-position$ 表示先手必败， $N-position$ 表示先手必胜。

本题并不需要用到 $SG$ 函数等知识，只需要懂得如何对博弈论的局面进行转移即可。

如此就引出了关于博弈论题目的打表技巧。

首先，我们要分析题目中的状态转移条件。

如本题，一个大小为 $n$ 的石子堆可以转移到 $n - 1$ 个石子或者是 $d_i\ (i \in n)$ ，即 $n$ 的所有约数。

所以，我们的打表程序可以先根据题意初始化前面几个状态，然后从小到大依次枚举 $i$ ，然后枚举所有 $i$ 能转移到的状态，判断他是否是 $P-position$ ，如果是那么当前的 $i$ 就为 $N-position$ ，这是根据定理得出的。

之后就根据打表程序找规律即可。

### 打表代码：

```cpp
#include <bits/stdc++.h>
//#include <tr1/unordered_map>
//#include"Bignum/bignum.h"
//#define lll bignum
#define ls(x) ( x << 1 )
#define rs(x) ( x << 1 | 1 )
#define lowbit(x) ( x & -x )
#define debug(x) (cout << "#x = " << (x) << endl)
#define Set(x, i) memset (x, i, sizeof(x))
#define re register
#define For(i, j, k) for(re int i = (j); i <= (k); ++i)
#define foR(i, j, k) for(re int i = (j); i >= (k); --i)
#define Cross(i, j, k) for(re int i = (j); i; i = (k))
using namespace std;
typedef long long ll;
const ll N = 1011;
const ll inf = 0x3f3f3f3f3f3f;

ll Pd[N + 5];

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
        if ( x < 0 ) putchar ('-'), x = -x;
        if ( x > 9 ) write ( x / 10 ); putchar ( x % 10 | 48 );
    }

    inline void wrn ( ll x ) { write (x); putchar (' '); }

    inline void wln ( ll x ) { write (x); putchar ('\n'); }

    inline void wlnn ( ll x, ll y ) { wrn (x), wln (y); }

}

using namespace IO;

inline void Init () {
    Pd[1] = 0;
    For (i, 2, N) {
        if (!Pd[i - 1]) Pd[i] = 1;
        else for (int j = 2; j <= i / 2; ++j)
            if (i % j == 0 && !Pd[i / j]) { Pd[i] = 1; break; }
    }
}

int main()
{
//  freopen(".in", "r", stdin);
//  freopen("biao.out", "w", stdout);
    Init (); ll aa = 0;
    For (i, 1, N) cout << "Pd[" << ++aa << "] = " << Pd[i] << endl;
    return 0;
}

/*

*/


```



然后我们找规律发现，除了 $2$ 和 $17$ 以外的质数都是 $P-position$ ，除了 $16,\ 34,\ 289$ 以外的合数都是 $N-position$ 。

再特判掉 $1$ ，就做完了。



## $Code:$

```cpp
#include <bits/stdc++.h>
//#include <tr1/unordered_map>
//#include"Bignum/bignum.h"
//#define lll bignum
#define ls(x) ( x << 1 )
#define rs(x) ( x << 1 | 1 )
#define lowbit(x) ( x & -x )
#define debug(x) (cout << "#x = " << (x) << endl)
#define Set(x, i) memset (x, i, sizeof(x))
#define R register
#define For(i, j, k) for(R int i = (j); i <= (k); ++i)
#define foR(i, j, k) for(R int i = (j); i >= (k); --i)
#define Cross(i, j, k) for(R int i = (j); i; i = (k))
using namespace std;
typedef long long ll;
const ll N = 100011;
const ll inf = 0x3f3f3f3f3f3f;

ll T, n, cnt = 0, m[N];

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
        if ( x < 0 ) putchar ('-'), x = -x;
        if ( x > 9 ) write ( x / 10 ); putchar ( x % 10 | 48 );
    }

    inline void wrn ( ll x ) { write (x); putchar (' '); }

    inline void wln ( ll x ) { write (x); putchar ('\n'); }

    inline void wlnn ( ll x, ll y ) { wrn (x), wln (y); }

}

using namespace IO;

inline bool Check (ll n) {
    for (R int i = 2; i * i <= n; ++i)
        if (n % i == 0) return 0; return 1;
}

int main()
{
//  freopen(".in", "r", stdin);
//  freopen("test.out", "w", stdout);
    for (T = read(); T; --T) {
        n = read();
        if (n == 1 || n == 16) puts ("NIE");
        else if (n == 2 || n == 17) puts ("TAK");
        else if (n == 34 || n == 289) puts ("NIE");
        else puts (Check (n)? "NIE": "TAK");
    } return 0;
}

/*

*/


```

[题目链接](<http://www.51nod.com/Challenge/Problem.html#!#problemId=1831>)