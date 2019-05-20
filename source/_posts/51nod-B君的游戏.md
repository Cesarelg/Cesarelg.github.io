---
title: 51nod B君的游戏
date: 2019-05-20 16:16:53
tags: [打表, game theory]
---

# $B$ 君的游戏



## $Description$

![](https://s2.ax1x.com/2019/05/20/Ex12C9.png)



## $Solution$

~~打表好题*2~~

这道题使我对博弈论~~打表~~的理解加深了一些。

首先我们考虑对于一个数所造成的影响，只与他在二进制下 $1$ 的个数有关。

所以状态总数只有 $64$ 种。

我们根据[上一题]([https://cesarelg.github.io/2019/05/20/51nod-%E5%B0%8FC%E7%9A%84%E6%B8%B8%E6%88%8F/](https://cesarelg.github.io/2019/05/20/51nod-小C的游戏/))的经验，可以知道本题的转移就是枚举一个数的所有子集并 $xor$ 起来。

但这题不能直接得到一个局面是否合法，所以需要结合 $SG$ 函数进行求解。

定义 $f(S) = \operatorname{mex}\{g(\mathrm{S})\}$ 表示最小的不在 $S$ 这个集合当中的自然数。

然后就得到了我们的打表程序。

```cpp
inline void Init () {
    For (i, 1, 64) {
        For (O1, 1, i - 1) For (O2, O1, i - 1) For (O3, O2, i - 1) 
            For (O4, O3, i - 1) For (O5, O4, i - 1) For (O6, O5, i - 1) 
            For (O7, O6, i - 1) {
                ull A = SG[O1] ^ SG[O2] ^ SG[O3] ^ 
                    SG[O4] ^ SG[O5] ^ SG[O6] ^ SG[O7];
                Vis[A] = i;
            }
        for (int j = 1; ; ++j) if (Vis[j] ^ i) { SG[i] = j; break; } // 暴力求 mex 
        cout << SG[i] << ", ";
    }
}
```



最后，我们异或上所有的一的个数的 $SG$ 值即可。



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
#define re register
#define For(i, j, k) for(re int i = (j); i <= (k); ++i)
#define foR(i, j, k) for(re int i = (j); i >= (k); --i)
#define Cross(i, j, k) for(re int i = (j); i; i = (k))
using namespace std;
typedef long long ll;
typedef unsigned long long ull;
const ll N = 100011;
const ll inf = 0x3f3f3f3f3f3f;
const ll SG[] = 
{
    0, 1, 2, 4, 8, 16, 32, 64, 128, 
    255, 256, 512, 1024, 2048, 3855, 4096, 
    8192, 13107, 16384, 21845, 27306, 32768, 
    38506, 65536, 71576, 92115, 101470, 131072, 
    138406, 172589, 240014, 262144, 272069, 380556, 
    524288, 536169, 679601, 847140, 1048576, 1072054, 
    1258879, 1397519, 2005450, 2097152, 2121415, 2496892, 
    2738813, 3993667, 4194304, 4241896, 4617503, 5821704, 
    7559873, 8388608, 8439273, 8861366, 11119275, 11973252, 
    13280789, 16777216, 16844349, 17102035, 19984054, 21979742
};

ull n, res, a[N];

namespace IO {

    inline char gc() {
        static char buf[100000], *p1 = buf, *p2 = buf;
        return (p1 == p2) && (p2 = (p1 = buf) +
            fread (buf, 1, 100000, stdin), p1 == p2)? EOF: *p1++; 
    }

    #define dd ch = getchar()
    inline ull read() {
        ull x = 0; bool f = 0; char dd;
        for (; !isdigit (ch); dd) f ^= (ch == '-');
        for (; isdigit (ch); dd)  x = x * 10 + (ch ^ 48);
        return f? -x: x;
    }
    #undef dd

    inline void write( ull x ) {
        if (x < 0) putchar ('-'), x = -x;
        if (x > 9) write (x / 10); putchar (x % 10 | 48);
    }

    inline void wrn ( ull x ) { write (x); putchar (' '); }

    inline void wln ( ull x ) { write (x); putchar ('\n'); }

    inline void wlnn ( ull x, ull y ) { wrn (x), wln (y); }

}

using namespace IO;

inline ll Devide (ll b) {
    ll res = 0;
    for (; b; b &= b - 1) ++res; return res;
}

int main()
{
//  freopen(".in", "r", stdin);
//  freopen("biao.out", "w", stdout);
    n = read();
    For ( i, 1, n ) 
        res ^= SG[Devide (read())];
    return puts ( !res? "L": "B" ), 0;
}

/*
9
1
3
7
15
31
63
127
255
511

L
*/

```

[题目链接](<http://www.51nod.com/Challenge/Problem.html#!#problemId=1549>)