---
title: exCrt
date: 2019-04-02 20:05:32
tags: [math, Crt, exgcd]
---

# $exCrt$



### 前言

十几天前写的板子...然而当时就不会证以及打，只是抄了个板子，今天准备把坑填上。



## $Description$

给定 $n$ 组非负整数 $a_i, b_i$ ，求解关于 $x$ 的方程组
$$
\begin{cases} x \equiv b_1\ ({\rm mod}\ a_1)\\{x} \equiv b_2\ ({\rm mod}\ a_2)\\...\\{x}\equiv b_n ({\rm mod}\ a_n) \end{cases}
$$
的最小非负整数解。



## $Solution$

先从简单的入手，现在有
$$
\begin{cases} x \equiv b_1\ ({\rm mod}\ a_1)\\{x} \equiv b_2\ ({\rm mod}\ a_2) \end{cases}
$$
将其转换成
$$
\begin{cases} x = b_1k_1\\{x} = b_2k_2 \end{cases}
$$
联立一下，就有
$$
b1k1 = a_2 - a_1 + b_2k_2
$$
根据 $exgcd$ 可知，满足有解的条件是 $gcd (b_1, b_2) | (a_2 - a_1)$

设
$$
d = gcd(b_1, b_2)
$$
两边同除以 $d$ ，得
$$
\frac{b_1}{d}k_1 = \frac{a_2 - a_1}{d} + \frac{b_2}{d}k_2
$$
两边同时对 $\dfrac{b_2}{d}$ 取模，得
$$
\frac{b_1}{d}k_1 = \frac{a_2 - a_1}{d} \pmod { \frac{b_2}{d} }
$$
即
$$
k_1 \equiv (\frac{b_1}{d})^{-1} \times \frac{a_2 - a_1}{d} \pmod {\frac{b_2}{d}}
$$
也即
$$
k_1 = (\frac{b_1}{d})^{-1} \times \frac{a_2 - a_1}{d} + y\frac{b_2}{d}
$$
带回原式，得
$$
x = b_1 \times (\frac{b_1}{d})^{-1} \times \frac{a_2 - a_1}{d} + y\frac{b_1b_2}{d} + a_1
$$
即
$$
x = b_1 \times (\frac{b_1}{d})^{-1} \times \frac{a_2 - a_1}{d} + y\frac{b_1b_2}{d}
$$
设 $c = b_1 \times (\dfrac{b_1}{d})^{-1} \times \dfrac{a_2 - a_1}{d}, m = \dfrac{b_1b_2}{d}$

就做出了这个简单的式子。

然后我们考虑通解。

设已经求出前 $k - 1$ 个方程组成的同余方程组得解为 $Ans$ ， $M = \operatorname{LCM}_{i - 1}^{k - 1}a[i]$ 。

所以前 $k - 1$ 个方程的通解为 $Ans + i * M$ 。

然后我们考虑加入第 $k$ 个方程后的方程组，也就是要我们求
$$
Ans + Mx \equiv b_k \pmod {a_k}
$$
也就是求
$$
Mx \equiv b_k - Ans \pmod {a_k}
$$
即
$$
Mx + a_ky = b_k - Ans
$$
然后我们考虑用 $exgcd$ 求出 $t$ ，重复 $n$ 遍即可。



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
const ll N = 100011;
const ll inf = 0x3f3f3f3f3f3f;

ll n, B[N], A[N];

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
		if ( x > 9 ) write ( x / 10 );
		putchar ( x % 10 | 48 );
	}

	inline void wrn ( ll x ) { write (x); putchar ( ' ' ); }

	inline void wln ( ll x ) { write (x); putchar ( '\n' ); }

	inline void wlnn ( ll x, ll y ) { wrn (x), wln (y); }

}

using namespace IO;

inline ll mul ( ll a, ll b, ll p ) {
	ll res = 0;
	for (; b; b >>= 1, a = (a + a) % p) res = (b & 1)? (res + a) % p: res;
	return res;
}

inline void exgcd ( ll a, ll b, ll &d, ll &x, ll &y ) {
    if (b) exgcd ( b, a % b, d, y, x ), y -= a / b * x;
    else x = 1, y = 0, d = a; return ;
}

inline ll exCrt() {
    ll x, y, M = 1, ans = 0;
    For ( i, 1, n ) {
        ll b = ((B[i] - ans) % A[i] + A[i]) % A[i];
        ll d; exgcd ( M, A[i], d, x, y );
        if (b % d) return -1;
        x = mul ( x, b / d, A[i] );
        ans += x * M; M *= A[i] / d, ans = (ans + M) % M;
    } return (ans % M + M) % M;
}

int main() {
//	freopen("testdata (6).in", "r", stdin);
//	freopen(".out", "w", stdout);
    n = read(); For ( i, 1, n ) 
		A[i] = read(), B[i] = read();
    return wln ( exCrt() ), 0;
}

/*

*/

```

[题目链接](<https://www.luogu.org/problemnew/show/P4777>)