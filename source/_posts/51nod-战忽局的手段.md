---
title: 51nod 战忽局的手段
date: 2019-05-05 21:28:41
tags: [math, probability, expectation, matrix]
---

# 战忽局的手段



## $Description$

![](https://s2.ax1x.com/2019/05/05/E0bznJ.png)



## $Solution$

~~套路题~~。

根据期望的线性，我们可以设 $f_i$ 表示 $m$ 次演讲忽悠成了 $i$ 次的概率。
$$
Ans = \sum f_i
$$

$$
f_i = f_{i - 1} + (1 - \frac{ f_{i - 1} }{n})
$$

忽悠成 $i - 1$ 次，有 $\dfrac{f_{i - 1}}{n}$ 的概率书被拿过，会失败。

化简就是
$$
f_i = \frac{n - 1}{n} f_{i - 1} + 1
$$
上个矩阵优化一下就是 $\Theta(T \log\ n)$ 的了。

转移矩阵就是 
$$
\begin{bmatrix} 
\frac{n - 1}{n} & 1
\\\
0 & 0
\end{bmatrix}
$$
注意精度，要用 $float128$ ，~~毒瘤~~。



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
typedef long long ll;
typedef __float128 fl;
const ll N = 2;
const ll inf = 0x3f3f3f3f3f3f;

struct Matrix {
    fl mp[N + 1][N + 1];
    
    Matrix () { Set (mp, 0); }
    
    inline void Init ( ll n ) { 
        mp[1][1] = (fl) (n - 1) / n;
        mp[1][2] = 0, mp[2][1] = mp[2][2] = 1;
    }
    
    friend Matrix operator *( Matrix a, Matrix b ) {
        Matrix res;
        For ( i, 1, N ) For ( k, 1, N ) For ( j, 1, N )
            res.mp[i][j] += a.mp[i][k] * b.mp[k][j]; return res;
    }
    
    friend Matrix operator ^( Matrix a, ll b ) {
        Matrix res; For ( i, 1, N ) res.mp[i][i] = 1;
        for (; b; b >>= 1, a = a * a) res = (b & 1)? res * a: res; return res;
    }
};

ll T, n, m;

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

int main()
{
//  freopen(".in", "r", stdin);
//  freopen(".out", "w", stdout);
    for (T = read(); T; --T) {
        n = read(), 
        m = read();
        Matrix A; A.Init (n); 
        Matrix Ans = A ^ (m - 1);
        printf ( "%.12Lf\n", (long double) (Ans.mp[1][1] + Ans.mp[2][1]) );
    } return 0;
}

/*
10
19 1
6 10
10 1
3 9
13 1
18 14
6 16
2 7
13 6
1 19

1.000000000000
5.030966502661
1.000000000000
2.921963115379
1.000000000000
9.913839952879
5.675472642406
1.984375000000
4.957876932773
1.000000000000
*/

```

[题目链接](<http://www.51nod.com/Challenge/Problem.html#!#problemId=1836>)