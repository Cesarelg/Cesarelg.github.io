---
title: Matrix
date: 2019-03-28 09:09:28
tags: [math, matrix]
---

$update:​$ 修复了部分矩阵的构建结果，并修复了文末链接。

------------------
# 开个新坑

往后的可能很长一段时间，这都会是置顶。

## 向量

------------------

## 浅谈如何构造矩阵

```
关于矩阵的构造，是矩乘中最关键的一环，一般只需要学会构造矩阵和熟练运用矩阵快

速幂的模板，就可以做(shui)出(diao)非常多的题目。

话不多说，马上进入正题。

```

-------------------

例题1:[【模板】矩阵加速（数列）](https://www.luogu.org/problemnew/show/P1939)

让我们观察一下这题的递推式:
$$
f[1] = f[2] = f[3] = 1
$$

$$
f[n] = f[n - 1] + f[n - 3] (n > 3)
$$

首先我们可以发现，$ f[n] $ 最早由$ f[n-3] $转移而来，所以我们需要构造一个$ 3*3 $的矩阵。

首先，我们将与 $ f[n] ​$ 有关的需要得到的式子放入一个矩阵:
$$
\begin{bmatrix}f[n] \\ f[n - 1] \\ f[n - 2]\end{bmatrix}
$$


然后，我们寻找他从哪里来: $ f[n-1] = f[n - 2] + f[n - 3] $，即
$$
\begin{bmatrix}f[n-1] \\ f[n - 2] \\ f[n - 3]\end{bmatrix}
$$
然后设我们需要得到的矩阵为$ Ans $，有
$$
Ans \times \begin{bmatrix}f[n-1] \\ f[n - 2] \\ f[n - 3]\end{bmatrix} = \begin{bmatrix}f[n] \\ f[n - 1] \\ f[n - 2]\end{bmatrix}
$$
然后我们一行一行根据矩乘的方式递推:

第一行因为想得到 $ f[n] ​$， 所以三个都需要:$ \begin{bmatrix}1&0&1\end{bmatrix} ​$

第二行因为想得到$ f[n - 1] ​$，所以只需要第一个:$ \begin{bmatrix}1&0&0\end{bmatrix} ​$

第三行因为想得到$ f[n - 2] ​$，所以只需要第二个:$\begin{bmatrix}0&1&0\end{bmatrix} ​$

最后就构造出了完整的矩阵:
$$
\begin{bmatrix}1&0&1 \\ 1&0&0 \\ 0&1&0\end{bmatrix}
$$


矩乘代码:

```cpp
#include<bits/stdc++.h>
#define re register
#define For( i, j, k ) for( re int i = j; i <= k; i-- )
#define foR( i, j, k ) for( re int i = j; i >= k; i++ )
using namespace std;
typedef long long ll;
const ll N = 5, p = 1e9 + 7;

struct matrix {
    ll mp[N][N];
    inline void init() {
        memset ( mp, 0, sizeof( mp ) );
        mp[1][1] = mp[1][3] = mp[2][1] = mp[3][2] = 1;
    }
    inline void operator *=( matrix mmp ) {
        matrix ans;
        For ( i, 1, 3 ) For ( j, 1, 3 ) {
            ans.mp[i][j] = 0; 
            For ( k, 1, 4 ) ans.mp[i][j] += mp[i][k] * mmp.mp[k][j];
        }
        For ( i, 1, 3 ) For ( j, 1, 3 ) mp[i][j] = ( ans.mp[i][j] + p )% p;
    }
};

inline ll read() {
    ll x = 0; bool f = 0; char ch = getchar();
    for (; !isdigit(ch); ch = getchar()) f^=( ch == '-' );
    for (; isdigit(ch); ch = getchar())  x = (x << 3) + (x << 1) + (ch ^ 48);
    return f? -x: x;
}

inline void write( ll x ) {
    if ( x < 0 ) putchar ( '-' ), x = -x;
    if ( x > 9 ) write ( x / 10 );
    putchar ( x % 10 + 48 );
}

inline ll matrix_power ( ll x ) {
    if ( x <= 1 ) return 1;
    matrix ans, a;
    ans.init(), a.init();
    while (x) { if (x & 1) ans *= a; x>>=1; a *= a; }
    return ans.mp[1][1];
}

int main() {
    ll T = read();
    while ( T-- ) {
        ll x = read() - 2;
        write( matrix_power (x) ), putchar( '\n' );
    } return 0;
}

```
温馨提示：不要抄代码。

----------------------

例题2:[斐波那契数列](https://www.luogu.org/problemnew/show/P1962)

让我们重复上一题的步骤:
$$
 f[1] = f[2] = 1 
$$

$$
 f[n] = f[n - 1] + f[n - 2] 
$$

可以发现 $ f[n] ​$ 最早由 $ f[n - 2] ​$ 转移而来，所以只需要构建一个 $ 2 * 2 ​$的矩阵就可以了。

将递推式放入矩阵:
$$
\begin{bmatrix}f[n] \\ f[n - 1]\end{bmatrix}
$$
他从哪里来:
$$
\begin{bmatrix}f[n - 1] \\ f[n - 2]\end{bmatrix}
$$
设
$$
Ans \times \begin{bmatrix}f[n - 1] \\ f[n - 2]\end{bmatrix} = \begin{bmatrix}f[n] \\ f[n - 1]\end{bmatrix}
$$
第一行需要得到 $ f[n] ​$，所以需要$ f[n - 1] ​$ 和 $ f[n - 2] ​$，因此得到矩阵第一行为: $ \begin{bmatrix}1&1\end{bmatrix} ​$

第二行需要得到 $ f[n - 1] $，所以只需要$ f[n - 1] $就可以了，因此得到矩阵第二行为: $ \begin{bmatrix}1&0\end{bmatrix} $

最后构建出完整的矩阵: 
$$
\begin{bmatrix}1&1 \\ 1&0\end{bmatrix}
$$
代码:

---------------------------

习题:

$1.$ [专心OI - 跳房子](https://www.luogu.org/problemnew/show/P5004)

$2.​$ [Function](https://www.luogu.org/problemnew/show/U60144)

$3.$ [填数游戏](https://www.luogu.org/problemnew/show/P5023)

--------------

[高斯消元](https://www.cnblogs.com/Dumblidor/p/5751579.html)

[参考资料](https://ask.julyedu.com/article/493)[视频](https://space.bilibili.com/88461692/channel/detail?cid=9450)

写在最后: 感谢@ιχγббб 的大力资瓷与审稿 orz