---
title: exLucas
date: 2019-03-29 11:01:27
tags: [Lucas, exgcd, math]
---

# $exLucas$ 

## $Description$

求 
$$
C_n^m \bmod p
$$
不保证 $p$ 为质数， $1<=n, m <= 10^{18}$  。

## $Solution$

考虑先将 $p$ 质因数分解。
$$
p = \prod_{i}p_i^{a_i}
$$
显然 $p_i^{a_i}$ 两两互质，所以只需要分别求出 $C_{n}^{m} \bmod p^k$ ，构造出多个形如 $C_{n}^{m} = b_i \bmod p_i^{a_i} $ 的方程，最后用 $Crt$ 合并求解即可。

然后考虑求
$$
C_{n}^{m} \bmod p^k
$$
根据组合数公式
$$
C_{n}^{m} = \dfrac{n!}{m!(n-m)!}
$$
由于 $m!$ 与 $(n - m)!$ 中可能含有因子 $p$ ，不能直接求对于 $p^k$ 的逆元，所以考虑先提出 $n!, m!, (n - m)!$ 中的 $p$ ，最后乘回去即可。

也就是
$$
\dfrac{\dfrac{n!}{p^{k_1}}}{\dfrac{m!}{p^{k_2}} \times \dfrac{(n - m)!}{p^{k_3}}} \times p^{k_1 - k_2 - k_3}
$$
显然 $\dfrac{m!}{p^{k_2}}, \dfrac{(n - m)!}{p^{k_3}}$ 与 $p^k$ 互质，直接求逆元即可。

之后考虑如何求形如 $\dfrac{n!}{p^{a}} \bmod p^k$ 的式子。

先举个栗子，如果
$$
n = 23, p = 3, k = 2
$$
有
$$
23! = 1 \times 2 \times 3 \ldots \times 23
$$
提出 $p$ 的所有倍数
$$
3^{7} \times 7! \times (1 \times 2 \times 4 \times 5 \times 7 \times 8 \ldots)
$$
可以看出第一部分是 $p^{\left\lfloor\frac{n}{p}\right\rfloor}​$ ，第二部分是 $\left\lfloor\frac{n}{p}\right\rfloor!​$ ，第三部分是 $\prod_{gcd(d, p) = 1}^{n!}d​$ 。

显然第一部分对于答案没有贡献，第二部分递归即可，所以现在还需要解决第三部分。

观察原式可发现
$$
1 \times 2 \times 4 \times 5 \times 7 \times 8 \equiv 10 \times 11 \times 13 \times 14 \times 16 \times 17 \pmod {p^k}
$$
显然，在 $\bmod {p^k}$ 意义下，第三部分的每一份乘积全部与 $\prod_{i = 1, gcd(i, p) = 1}^{p^k}$ 同余。

显然，这些部分有 $\frac{n}{p^k}$ 个，所以只需要暴力算出一次互质的，然后快速幂即可。

记住最后要加上不完整的块，也就是例子里的 $19 \times 20 \times 22 \times 23 $ 。

然后我们回到求组合数的过程，提出因子 $p$ ，最后乘上逆元与提出的 $p$ 即可。

对于原问题， $Crt$ 合并即可。

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
#define For(i, j, k) for(re ll i = (j); i <= (k); i++)
#define foR(i, j, k) for(re ll i = (j); i >= (k); i--)
#define Cross(i, j, k) for(re ll i = (j); i; i = (k))
using namespace std;
typedef long long ll;
const ll N = 100011;
const ll inf = 0x3f3f3f3f3f3f;

ll n, m, p;

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

namespace Solve {

    inline void exgcd ( ll a, ll b, ll &d, ll &x, ll &y ) {
        if (b) exgcd ( b, a % b, d, y, x ), y -= a / b * x;
        else d = a, x = 1, y = 0; return ;
    }
    
    inline ll power ( ll a, ll b, ll p ) {
        ll res = 1;
        for (; b; b >>= 1, a = a * a % p) res = (b & 1)? res * a % p: res;
        return res;
    }
    
    inline ll Inv ( ll a, ll p ) {
        ll d, x, y;
        exgcd ( a, p, d, x, y );
        return (x + p > p)? x: x + p;
    } // exgcd 求逆元。 
    
    inline ll Crt ( ll b, ll mod ) {
        return b * Inv ( p / mod, mod ) % p * ( p / mod ) % p;
    } // Crt 合并答案。 
    
    inline ll Devide ( ll n, ll p, ll k ) {
        if (!n) return 1;
        ll res = 1; For ( i, 2, k ) 
            if (i % p) res = res * i % k; // 计算一次第三部分 
        res = power ( res, n / k, k ); // 总共有 n / k 次第三部分。 
        For ( i, 2, n % k ) if (i % p) res = res * i % k; // 第三部分有一点没被乘进去。 
        return res * Devide ( n / p, p, k ) % k; // 乘上第二部分。 
    } // 求阶乘。 
    
    inline ll C ( ll n, ll m, ll p, ll k ) {
        if ( n < m ) return 0;
        ll cnt = 0, a = Devide (n, p, k), 
            b = Devide (m, p, k), c = Devide (n - m, p, k);
        Cross ( i, n, i / p ) cnt += i / p;
        Cross ( i, m, i / p ) cnt -= i / p;
        Cross ( i, n - m, i / p ) cnt -= i / p;
        return a * Inv (b, k) % k * Inv (c, k) % k * power (p, cnt, k) % k;
    } // 求组合数。 n! / m!(n - m)!
    
    inline ll exLucas () {
        ll res = 0, temp = p;
        for (re int i = 2; i * i <= temp; i++) 
            if (temp % i == 0) {
                ll t = 1;
                while (temp % i == 0) t *= i, temp /= i;
                res = (res + Crt ( C(n, m, i, t), t )) % p;
            }
        return (temp > 1)? (res + Crt ( C(n, m, temp, temp), temp )) % p: res;
    } // 分解质因数， Crt 合并答案。
    /*
        t 就是 p^k, i 就是 p 。 
    */ 

}

int main() {
//  freopen(".in", "r", stdin);
//  freopen(".out", "w", stdout);
    n = read(), m = read(), p = read();
    return wln ( Solve::exLucas () ), 0;
}

/*
157705804463339796 195211 821261

150052
*/

```

[题目链接](<https://www.luogu.org/problemnew/show/P4720>)

 