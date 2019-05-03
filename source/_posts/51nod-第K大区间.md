---
title: 51nod 第K大区间
date: 2019-05-03 21:12:57
tags: [two pointers, two points answer]
---

# 第 K 大区间



## $Description$

![](https://s2.ax1x.com/2019/05/03/EU6bdI.png)



## $Solution$

挺妙的一道想法题。

只想到了要离散化去重、桶，二分真的没想到，看了题解发觉十分精妙，便记录一下。

观察数据范围，可以发现要离散化，然后求区间众数，可以考虑用桶。

关键在于如何优化求解第 $k$ 大区间的过程。

可以二分区间众数的值，判断是否有 $>= k$ 个大于 $mid$ 值的区间众数，有的话就满足要求。

这其实就是把本来要求的第 $k$ 大的值 $>=mid$ 转化为有 $k - 1$ 个区间众数值 $<= mid$ ，从而使本题能够二分求解。

同时，在二分答案的过程中，我们用到了尺取法求出区间众数值 $>= mid$ 的区间个数，因为本题所求的东西满足单调性，答案不会减小，因此可以采用尺取法，做到 $\Theta(n)\ Check$ 。



## $Code:$

```cpp
#include <bits/stdc++.h>
#include<tr1/unordered_map>
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
typedef unsigned long long ull;
const ll N = 200011;
const ll inf = 0x3f3f3f3f3f3f;

ll n, k, a[N], b[N]; 

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

ll Cnt[N];

inline bool Check ( ll Limit ) {
	Set (Cnt, 0); ll res = 0, r = 0;
	For ( l, 1, n ) {
		while ( r <= n && 
			Cnt[a[r]] < Limit ) Cnt[a[++r]]++;  // 尺取法。
		if ( r > n ) break; res += n - r + 1; --Cnt[a[l]]; 
	} return res >= k;
}

int main()
{
//	freopen(".in", "r", stdin);
//	freopen(".out", "w", stdout);
	n = read(), k = read();
	For ( i, 1, n ) a[i] = b[i] = read(); 
	
	sort (b + 1, b + n + 1); 
	ll len = unique (b + 1, b + n + 1) - b - 1;
	For ( i, 1, n ) a[i] = upper_bound (b + 1, b + len + 1, a[i]) - b;
	
	ll l = 0, r = 1ll << 30;
	while ( l < r ) {
		ll Mid = (l + r + 1) >> 1;
		Check (Mid)? l = Mid: r = Mid - 1;
	} return wln (l), 0;
}

/*

*/

```

[题目链接](<https://www.51nod.com/Challenge/Problem.html#!#problemId=1686>)





