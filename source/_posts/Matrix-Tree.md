---
title: Matrix Tree
date: 2019-04-08 19:50:31
tags: [matrix tree, math]
---

# $Matrix$ $Tree$ $theorem$

[参考资料](<https://www.cnblogs.com/zj75211/p/8039443.html>)



## 定理

```
对于一个无向图 G, 他的生成树个数等于其基尔霍夫矩阵的任意一个 N - 1 阶主子式的行列式的绝对值。
N - 1 阶主子式就是对于一个任意的一个 r ，将矩阵的第 r 行和第 r 列同时删去得到的新矩阵。
基尔霍夫矩阵的一种求法：
K = 度数矩阵 D - 邻接矩阵 A
```



## 构造方式

度数矩阵 $D$ :

在一个 $N \times N$ 矩阵中 $D[i][i] = i的度数$ ，其余为 $0$ 。

邻接矩阵 $A$ :

在一个 $N \times N$ 矩阵中 $D[i][j] = i \to j 的边数$ ，其余为 $0$ 。

基尔霍夫矩阵 $K = D - A$ 。



## 行列式求法

已求出基尔霍夫矩阵，随意去掉一行一列计算新的行列式，绝对值即生成树个数，有公式
$$
det(K) = \sum_T(-1)^{\tau(T)}\times K_{1, T_1}\times K_{2, T_2}\times \ldots \times K_{n, T_n}
$$
复杂度 $O(N\times N!)$

思考优化求解的方式。



## 性质

### $1.$ 互换矩阵两行 （列），行列式变号。

~~证明在参考资料里~~

### $2.$ 矩阵若有两行 （列)完全相同，行列式为 $0$ 。

简单说明一下，若有两行（列）都相同，那么可以都被消去，$K[i][i]$ 当中有两个会变 $0$ ，导致行列式归 $0$ 。

也可以通过性质 $1$ 证明，~~具体看资料~~ 。

### $3.$ 如果矩阵的某一行(列)中的所有元素都乘以同一个数k，新行列式的值等于原行列式的值乘上数k。

把原求和式每一项提出一个 $k$ 即可。

#### $ps:$ 推论  如果矩阵的某一行(列)中的所有元素都有一个公因子k，则可以把这个公因子k提到行列式求和式的外面。

### $4.$  如果把矩阵的某一行(列)加上另一行(列)的k倍，则行列式的值不变。

~~懒得打证明了，去看资料去~~



## 优化求解方式

首先，对于一个上（下）三角矩阵，其行列式是 $\prod_{i = 1}^{n}K[i][i]$ ，根据性质四可知，我们可以采用高斯消元的方式把矩阵消成一个上（下）三角矩阵，然后即可直接求解行列式的值。

为什么是这样？

我们可以观察最初的求和公式，我们发现对于一个上（下）三角矩阵，他的逆序对个数为 $0$ ，所以我们的答案就变成了 $\prod_{i = 1}^{n} K[i][i]$ 。



[例题](<https://www.luogu.org/problemnew/show/P3317>)



## $Solution$

套上矩阵树定理，有
$$
Sum = \sum_{Tree} \prod_{(u, v)\in E} P_{u, v}
$$
由题意得
$$
Ans = Sum \times \prod_{u, v}(1 - P_{u, v})
$$
把式子拆出来，再设一个 $K[i][j]$
$$
Ans = Sum \times \prod_{(u, v)\in E}K_{u, v} \times (1 - P_{u, v}) \prod_{(u, v)\notin E}(1 - P_{u, v})
$$
而答案是
$$
Ans = Sum \times \prod_{(u, v)\in E}P_{u, v} \prod_{(u, v)\notin E}(1 - P_{u, v})
$$
所以只要使
$$
K_{u, v} = \frac{P_{u, v}}{1 - P_{u, v}}
$$
即可。

然后我们再把 $\prod_{u, v}(1 - P_{u, v})$ 拆出来，剩下的部分通过高消求出行列式，乘积即为答案。

另外，我们发现当 $P_{i, j} = 1$ 时，会输出 $nan$ ，所以如果我们判断 $P_{i, j} = 1$ ，就将 $P_{i, j}$ 减去 $eps$ 。



## $Code:$

```cpp
#include<bits/stdc++.h>
//#include<tr1/unordered_map>
//#include"Bignum/bignum.h"
//#define lll bignum
#define ls(x) ( x << 1 )
#define rs(x) ( x << 1 | 1 )
//#define mid ( ( l + r ) >> 1 )
#define lowbit(x) ( x & -x )
#define debug(x) (cout << "#x = " << x << endl)
#define re register
#define For(i, j, k) for(re int i = (j); i <= (k); i++)
#define foR(i, j, k) for(re int i = (j); i >= (k); i--)
#define Cross(i, j, k) for(re int i = (j); i; i = (k))
using namespace std;
typedef long long ll;
const ll N = 71;
const ll inf = 0x3f3f3f3f3f3f;
const double eps = 0.000000001;

ll n;
double ans = 1, K[N][N];

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
        if ( x > 9 ) write ( x / 10 ); putchar ( x % 10 | 48 );
    }

    inline void wrn ( ll x ) { write (x); putchar ( ' ' ); }

    inline void wln ( ll x ) { write (x); putchar ( '\n' ); }

    inline void wlnn ( ll x, ll y ) { wrn (x), wln (y); }

}

using namespace IO;

inline void Gauss () {
    For ( i, 1, n - 1 ) {
        ll pos = i;
        For ( j, i + 1, n - 1 )
            if ( fabs ( K[pos][i] - K[j][i] ) <= eps ) pos = i;
        if ( i != pos ) 
            For ( j, i, n - 1 ) swap ( K[i][j], K[pos][j] );
        For ( j, i + 1, n - 1 ) {
            double t = K[j][i] / K[i][i];
            For ( k, i, n - 1 ) K[j][k] -= t * K[i][k];
        }
        if ( fabs (K[i][i]) < eps ) return (void) (ans = 0);
    }
    For ( i, 1, n - 1 ) ans *= K[i][i]; // 求行列式的值。
    ans = fabs (ans);
    /*
        利用矩阵树定理做生成树计数时， 
        传统基尔霍夫矩阵中，如果 x -> y 有一条边，
        则看做是边权为 1 ，那么就使 K[x][y]--, K[y][x]--
        此处若边权为 w ，正常操作应是 K[x][y] -= w, K[y][x] -= w ，
        但前面却直接赋值成 K[x][y] = K[y][x] = w ，因为这样有可能有
        正有负，所以用了 fabs 。 
    */
} 

int main()
{
//  freopen(".in", "r", stdin);
//  freopen(".out", "w", stdout);
    double t = 1; n = read(); 
    For ( i, 1, n ) For ( j, 1, n ) scanf ("%lf", &K[i][j]);
    For ( i, 1, n ) For ( j, 1, n ) {
        if ( fabs ( K[i][j] ) < eps ) K[i][j] = eps;
        if ( fabs ( 1.0 - K[i][j] ) < eps ) K[i][j] = 1 - eps;
        if ( i < j ) t *= (1.0 - K[i][j]); K[i][j] /= (1.0 - K[i][j]);
    }
    For ( i, 1, n ) K[i][i] = 0;
    For ( i, 1, n ) For ( j, 1, n ) K[i][i] -= (i == j)? 0: K[i][j];
    Gauss (); return printf ("%.7lf", ans * t), 0;
}

/*

*/

```

