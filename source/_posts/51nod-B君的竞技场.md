---
title: 51nod B君的竞技场
date: 2019-05-08 21:27:01
tags: [math, integral, expectation, probability]
---

# $B$ 君的竞技场



## $Description$

![](https://s2.ax1x.com/2019/05/08/EcEBE4.png)



## $Solution$

设 $f_{i, j}$ 表示胜场到 $i$ 场，败场 $j$ 场的概率。
$$
f_{i, j} = f_{i - 1, j} \times p + f_{i, j - 1} \times (1 - p)
$$
然后难点就在于输赢的概率怎么求。。。

~~我开始下意识以为都是 0.5~~

结果发现答案死活不对，看了题解发现是积分。。。

~~然后就去学了一下午积分, ***~~

我们设竞技场人数为 $n$ ，有
$$
Ans = \lim_{n \to \infty} \sum_{i = 0}^{n - 1} \frac{1}{n} \times E(\frac{i}{n})
$$
即
$$
\lim_{n \to \infty} \int_{0}^{n - 1}f(x) \,dx
$$
就先用递推式得到一个关于 $p$ 的多项式，然后求导。

枚举所有 $f_{x, i}$ 与 $f_{i, y}$ ，求一下关于他们的定积分并累加即可。

可以发现此时所要求的的函数只与 $x^n$ 有关，直接求导公式 $(x^n)^\prime = nx^{n - 1}$ 一套就行了。



## $Code:$

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
typedef double db;
typedef long long ll;
typedef unsigned long long ull;
const ll N = 51;
const ll inf = 0x3f3f3f3f3f3f;

ll x, y;
double Ans = 0;

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

struct Solve {
    db a[N + 3];
    Solve () { Set (a, 0); }
    
    friend Solve operator + ( Solve A, Solve B ) {
        Solve res; 
        For ( i, 0, N ) 
            res.a[i] = A.a[i] + B.a[i];
        return res;
    }
    
    friend Solve operator * ( Solve A, Solve B ) {
        Solve res; 
        For ( i, 0, N ) For ( j, 0, i )    
            res.a[i] += A.a[i - j] * B.a[j]; return res;
    } // 多项式板子。 
    
    inline void Derivation () {
        foR ( i, N, 1 ) 
            a[i] = a[i - 1] / i; a[0] = 0;
    } // 求导逆过程。 
    
    inline db Integral () {
        Derivation (); db Ans = 0; 
        For ( i, 1, N ) Ans += a[i]; return Ans;
    } // 把所有答案累加即可。 
} Win, Lose, f[N + 3][N + 3];

int main()
{
//  freopen(".in", "r", stdin);
//  freopen(".out", "w", stdout);
    x = read(), 
    y = read();
    f[0][0].a[0] = 1; // 没赢没输概率为 1 。 
    Win.a[0] = 0, Win.a[1] = 1; // 赢一场 +1 。 
    Lose.a[0] = 1, Lose.a[1] = -1; // 输一场相反。 
    For ( i, 0, x - 1 ) For ( j, 0, y - 1 ) {
        if (i) f[i][j] = f[i][j] + f[i - 1][j] * Win;
        if (j) f[i][j] = f[i][j] + f[i][j - 1] * Lose;
    }
    For ( i, 0, y - 1 ) 
        f[x][i] = f[x - 1][i] * Win, Ans += f[x][i].Integral() * x;
    For ( i, 0, x - 1 ) 
        f[i][y] = f[i][y - 1] * Lose, Ans += f[i][y].Integral() * i;
    return printf ("%.12lf", Ans), 0;
}

/*

*/

```

