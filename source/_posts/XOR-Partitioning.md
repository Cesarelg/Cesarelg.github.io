---
title: XOR Partitioning
date: 2019-06-25 19:37:50
tags: [dp]
---

# $XOR\ Partitioning$



## $Description$

给一个序列，问有多少种方法能够把这个序列分成若干块使得每一块的异或和都相同。答案对 $1e9+7$ 取模。



## $Solution$

观察本题，可以发现几个性质：

对于两段异或和 $1 - i,\ 1 - j$ ，规定 $i < j$ ，记 $1 - i$ 的异或和为 $s_i$ ，两段异或和相等当且仅当 $s_j == 0$ ，那么显然对于所有 $s_j == 0$ ，只会有选与不选两种情况，即他对答案的贡献为使答案乘 $2$ 。

我们设 $f_i$ 表示异或和为 $s_i$ 的方案数。

那么对于一个异或和，我们可以发现他可以转移到所有异或和为 $0$ 的 $s_i$ 中，首先就有了一种暴力的转移：
$$
f_i = \sum_{j = 1}^{i} f_j \times (i \to j)\ 中\ 0\ 的个数 \ (s_i == s_j)
$$
考虑如何优化这个东西。

显然 $i - j$ 中 $0$ 的个数可以通过前缀和来优化，然后我们对于所有满足 $S_i == S_j$ 的条件的考虑一步步去找相邻两个的方案数。

可以记 $g_i$ 表示所有 $f_i$ 的和，记 $Lst_i$ 表示异或和为 $i$ 的上一个编号。

然后就有转移
$$
f_{s_i} += (Z_i - Z_{Lst_{s_i}}) \times g_{s_i}
$$

$$
g_{s_i} += f_{s_i}
$$

就是前面的所有 $f_{s_i}$ 都可以转移到当前的 $f_{s_i}$ ，并且要乘上这一个 $i$ 遇上一个之间所有 $0$ 的个数，因为都可以转移到。

然后做前缀和即可。

最后答案需要判断 $f_{s_n}$ 是否为 $0$ ，不为 $0$ 就直接取 $f_{s_n}$ 作为答案。

否则考虑加入后面的没取的 $0$ 造成的贡献，注意最后一个 $0$ 必须取才可以转移。

最后累加所有 $g_i$ 即可。



## $Code;$

```cpp
#include <bits/stdc++.h>
#define Set(x, i) memset (x, i, sizeof x)
#define debug(x) (cout << "#x = " << (x) << endl)
#define R register
#define For(i, j, k) for(R int i = (j); i <= (k); ++i)
#define foR(i, j, k) for(R int i = (j); i >= (k); --i)
#define Cross(i, j, k) for(R int i = (j); i; i = (k))
using namespace std;
typedef long long ll;
const ll N = 500011;
const ll INF = 5e16;
const ll P = 1e9 + 7;

/*
    设 f[i] 表示每块异或和为 1 - i 的异或和的方案数。
    f[i] = Calc (i)
*/

ll n, a[N];

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
    
    inline void write ( ll x ) {
        if (x < 0) x = -x, putchar ('-');
        if (x > 9) write (x / 10); putchar (x % 10 | 48);
    }
    
    inline void wrn ( ll x ) { write (x); putchar (' '); }
    
    inline void wln ( ll x ) { write (x); putchar ('\n'); }
    
    inline void wlnn ( ll x, ll y ) { wrn (x); wln (y); }
    
}

using IO::wln;
using IO::read;

ll F = 0, fac[N], inv[N];

inline ll power ( ll a, ll b ) {
    ll res = 1;
    for (; b; b >>= 1, a = a * a % P) 
        res = (b & 1)? res * a % P: res; return res;
}

inline ll Inv ( ll x ) { return power (x, P - 2); }

inline ll C ( ll n, ll m ) {
//    debug (fac[n]);
//    cout << "!" << n << " " << m << endl;
    if (n < m) return 0;
    if (!m || !n) return 1;
    return fac[n] * inv [n - m] % P * inv[m] % P;
}

inline void Init() {
    fac[0] = 1;
    For ( i, 1, N - 5 ) fac[i] = fac[i - 1] * i % P;
    inv[0] = 1; For ( i, 1, N - 5 ) inv[i] = Inv (fac[i]);
}

namespace Subtask1 {

    inline void main () {
        ll Ans = 0;
        For ( i, 0, n - 1 ) Ans = (Ans + C(n - 1, i)) % P;
        wln (Ans); exit (0);
    }
    
}

namespace Subtask2 {
    
    const ll M = 5011;
    
    ll Ans = 0, f[M][1 << 11], S[N];
    
    inline void main () {
        For ( i, 1, n ) S[i] = S[i - 1] ^ a[i];
        For ( i, 1, n ) {
            f[i][S[i]] = 1;
            For ( j, 1, i - 1 ) 
                (f[i][S[i] ^ S[j]] += f[j][S[i] ^ S[j]]) % P;
        } 
        For ( i, 0, 1024 ) Ans = (Ans + f[n][i]) % P;
        wln (Ans), exit (0);
    }
    
}

namespace Subtask {
    
    const ll M = 1ll << 20;
    
    ll Ans = 1, g[M + 5], f[M + 5], S[M], Lst[M], Z[M];
    
    inline void Add ( ll &a, ll b ) { a = (a + b) > P? a + b - P: a + b; } 
    
    inline void main () {
        For ( i, 0, M ) f[i] = 1;
        For ( i, 1, n ) S[i] = S[i - 1] ^ a[i];
        For ( i, 1, n ) Z[i] = Z[i - 1] + (S[i] == 0);
        
        For ( i, 1, n ) if (S[i]) {
            Add (f[S[i]], (Z[i] - Z[Lst[S[i]]]) * g[S[i]] % P);
            g[S[i]] = (g[S[i]] + f[S[i]]) % P; Lst[S[i]] = i;
        }
        
        if (S[n]) wln (f[S[n]]), exit (0);
        
        For ( i, 1, Z[n] - 1 ) Ans = (Ans << 1) % P;
        For ( i, 1, M - 1 ) Add (Ans, g[i]); wln (Ans), exit (0);
    }
    
}

int main()
{
    freopen("b.in", "r", stdin);
    freopen("b.out", "w", stdout);
    n = read(); Init();
    For ( i, 1, n ) a[i] = read();
    For ( i, 1, n ) if (a[i]) F = 1;
    if (!F) Subtask1::main ();
    if (n <= 5000) Subtask2::main (); 
    else Subtask::main (); return 0;
}

/*
3
0 0 0
3
1 2 3
*/

```

[题目链接](<https://atcoder.jp/contests/diverta2019/tasks/diverta2019_e>)