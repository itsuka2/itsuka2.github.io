---
title: "数论总结"
description: ""
date: 2025-02-11
lastmod: 2025-02-12
tags:
- 数学
- 阶段总结
heroStyle: background
---

我个人比较偏向于把数论和组合的卷积和反演分开理解.

~~实际上是组合数太难了（逃……~~

## 常见名词

{{<katex>}}
### 单位元 \\(\varepsilon(n)\\)

$$\varepsilon(n)=[n=1]$$

显然完全积性.

{{<katex>}}
### 逆元 \\(f^{-1}\\)

当 {{<katex>}}\\(f*f^{-1}=\varepsilon\\) 时，称 {{<katex>}}\\(f^{-1}\\) 是 {{<katex>}}\\(f\\) 的逆.

{{<katex>}}
### 幂函数 \\(ID_k(n)\\)

{{<katex>}}\\(ID_k(n)=n^k\\) ，特别地，{{<katex>}}\\(ID(n)=n\\) .

显然完全积性.

{{<katex>}}
### 除数函数 \\(\sigma_k(n)\\)

$$\sigma_k(n)=\sum_{d|n}d^k$$

感性理解 {{<katex>}}\\(\sigma_k(n)\\) 表示 {{<katex>}}\\(n\\) 的所有因子 {{<katex>}}\\(k\\) 次幂之和.

显然积性.

比如，{{<katex>}}\\(\sigma_0(n)\\) 表示 {{<katex>}}\\(n\\) 的因数个数，{{<katex>}}\\(\sigma_1(n)\\) 表示 {{<katex>}}\\(n\\) 的因数和.

{{<katex>}}
### 欧拉函数 \\(\varphi(n)\\)

$$\varphi(n)=\sum_{i=1}^{n}[(i,n)=1]$$

说人话就是在 {{<katex>}}\\(1\sim n\\) 中和 {{<katex>}}\\(n\\) 互质的数的个数.

四个式子：

1. 这是个积性函数，即当 {{<katex>}}\\((i,j)=1\\) 时，{{<katex>}}\\(\varphi(ij)=\varphi(i)\times\varphi(j)\\) .

2. 这里有欧拉反演的一个看起来比较正常的形式：{{<katex>}}\\(n=\sum_{d|n}\varphi(d)\\) .

3. 求单个欧拉函数：{{<katex>}}\\(\varphi(n)=n\times\prod_{i=1}^k\frac{p_i-1}{p_i}\\) ，此处 {{<katex>}}\\(n=\prod_{i=1}^kp_i^{c_i}\\) .

4. 若 {{<katex>}}\\(n=p^k\\) ，则 {{<katex>}}\\(\varphi(n)=p^k-p^{k-1}\\) .

{{<katex>}}
### 莫比乌斯函数 \\(\mu(n)\\)

$$\mu(n)=\begin{cases}1&& n=1\\\\ 0 && n\ 含有平方因子 \\\\ (-1)^k && k\ 为\ n\ 的本质不同质因子个数\end{cases}$$

三个式子：

1. 这也是个积性函数，即当 {{<katex>}}\\((i,j)=1\\) 时，{{<katex>}}\\(\mu(ij)=\mu(i)\times\mu(j)\\) .

2. {{<katex>}}\\(\sum_{d|n}\mu(d)=[n=1]=\varepsilon(n)\\) ，这个会拿去做反演和卷积.

3. 据说有拓展形式，日后再补.

## 常见卷积

### 狄利克雷（Dirichlet）卷积

$$(f*g)(n)=\sum_{d|n}f(d)\times g(\frac{n}{d})$$

这个定义式在有些题目中可以化为卷积形式，然后用莫比乌斯反演推式子求解.

几个性质：

- 若 {{<katex>}}\\(f\\)、{{<katex>}}\\(g\\) 都是积性函数，则 {{<katex>}}\\(f\ast g\\) 也是积性函数.

- 交换律：{{<katex>}}\\(f\ast g=g\ast f\\)

- 结合律：{{<katex>}}\\(f\ast (g\ast h)=(f\ast g)\ast h\\)

- 分配律：{{<katex>}}\\((f+g)\ast h=f\ast h+g\ast h\\)

基本上可以把这个卷积视作定义在函数之间的新运算，除此之外，一般题目的式子大概率是不会把定义式化过来化过去的.

### 一些卷积式

1. {{<katex>}}\\(\varepsilon=\mu*1\\) ，直接按定义式展开易证，不难发现 {{<katex>}}\\(\mathrm{RHS}=\sum_{d|n}\mu(d)=[n=1]=\varepsilon(n)\\) 也就是莫比乌斯函数的性质2.

	由上式，可以知道 {{<katex>}}\\(\mu=1^{-1}\\) ，所以有 {{<katex>}}\\(g=f*\pmb 1 \Leftrightarrow f=g*\mu\\) ，即

    $$g(n)=\sum_{d|n}f(d)\Leftrightarrow f(n)=\sum_{d|n}\mu(d)g(\frac{n}{d})$$

	这就是常见的莫比乌斯反演的形式. 更进一步的，还有

    $$\sum_{d|(i,j)}\mu(d)=\varepsilon\left((i,j)\right)$$

2. {{<katex>}}\\(\sigma_0=1*1\\)

3. {{<katex>}}\\(\sigma_1=ID*1\\)

4. {{<katex>}}\\(\sigma_2=ID*ID\\)

5. {{<katex>}}\\(ID=\varphi*1\\) ，这就是欧拉反演的卷积式形式.

6. {{<katex>}}\\(\varphi=\mu*ID\\)

7. {{<katex>}}\\((ID_n\cdot \mu)*ID_n=\varepsilon\\)

8. {{<katex>}}\\((ID_n\cdot \varphi)*ID_n=ID_n\\)

## 常见定理

### 欧拉定理

若 {{<katex>}}\\(n\\)、{{<katex>}}\\(a\\) 互素，则 {{<katex>}}\\(a^{\varphi(n)}\equiv 1 \pmod n\\) .

### 拓展欧拉定理

$$a^b\equiv \begin{cases}a^{b\bmod \varphi(p)}&(a,p)=1\\\\a^b&(a,p)\ne 1,b<\varphi(p)\\\\a^{b\bmod \varphi(p)+\varphi(p)}&(a,p)\ne 1,b\geq\varphi(p)\\\\\end{cases} \mod m$$

常用于解决“幂的幂次”以及“幂的递归”一类问题.

在有些数论题目中会客串，要注意模数的不同.

## 筛子

### 线性筛

可以线性递推积性函数的值，最常见的比如 {{<katex>}}\\(\varphi(i)\\) 和 {{<katex>}}\\(\mu(i)\\) .

### 埃氏筛

很少用. 可以分块递推大质数表，但这好像是另外一个算法了.

详见[Wheel Factorization 模板 - SP6488](https://www.spoj.com/problems/PRIMES2/).

### 杜教筛

可以在**亚线性**复杂度内求积性函数前缀和.

~~除此之外，还需要惊人的注意力~~

引理：对于**任意**数论函数 {{<katex>}}\\(f\\)、{{<katex>}}\\(g\\) 满足 {{<katex>}}\\(S_{f*g}(n)=\sum_{i=1}^{n}g(i)\cdot S_f([\frac{n}{i}])=g(1)S_f(n)+\sum_{i=2}^{n}g(i)S_f([\frac{n}{i}])\\)

总的来说，有了这个引理，我要是想求 {{<katex>}}\\(f\\) 函数前缀和，就只需要构造一个函数 {{<katex>}}\\(g\\) 使得 {{<katex>}}\\(f*g\\) 是易求的，再套上下面的式子递归处理就ok了.

$$\boxed{S_f(n)=\frac{1}{g(1)}\left(S_{f*g}(n)-\sum_{i=2}^{n}g(i)\cdot S_f([\frac{n}{i}])\right)}$$

那么，举个例子，假如我要求 {{<katex>}}\\(\varphi\\) 的前缀和，因为我知道 {{<katex>}}\\(\varphi*1=ID\\) ，所以有：

$$S_\varphi(n)=S_{f*g}(n)-\sum_{i=2}^{n}S_\varphi([\frac{n}{i}])\\\\S_\varphi(n)=\frac{n(n+1)}{2}-\sum_{i=2}^{n}S_\varphi([\frac{n}{i}])$$

然后把这个写到记忆化搜索里面去就ok了.

再举个例子，假如要求 {{<katex>}}\\(\mu\\) 的前缀和，同理，由 {{<katex>}}\\(\mu*1=\varepsilon\\) 可得 {{<katex>}}\\(S_\mu(n)=1-\sum_{i=2}^{n}S_\mu([\frac{n}{i}])\\) （注意 {{<katex>}}\\(S_\varepsilon(n)=1\\) ）.

代码实现参见[【模板】杜教筛](https://www.luogu.com.cn/problem/P4213).

对于不常见函数的杜教筛，后面再补充.

总的来说，一般的题目只需要记好之前提到的常见卷积式就ok啦.

### PN筛

定义（Powerful Number）：任意素因子次数均大于等于 {{<katex>}}\\(2\\) 的数.

引理：所有PN都可以写成 {{<katex>}}\\(a^2b^3\\) 的形式.

PN筛：对于积性函数 {{<katex>}}\\(f\\) ，构造积性函数 {{<katex>}}\\(g\\) ，使积性函数 {{<katex>}}\\(h=f\ast g^{-1}\\) 仅在PN处有值，从而 {{<katex>}}\\(S_f=S_g\ast h\\) .

算法（暴力搜索PN表）：直接暴力搜素数次数，时间复杂度 {{<katex>}}\\(O(\sqrt{n})\\) .

定义（素数拟合）：对于函数 {{<katex>}}\\(f\\)、{{<katex>}}\\(g\\) ，{{<katex>}}\\(\forall p\in P\\) ，{{<katex>}}\\(f(p)=g(p)\\) .

引理：若积性函数 {{<katex>}}\\(f\\)、{{<katex>}}\\(g\\) 素数拟合且 {{<katex>}}\\(f(1)=g(1)=1\\) ，则 {{<katex>}}\\(h=f*g^{-1}\\) 仅在PN处有值（PN函数）.

引理：若 {{<katex>}}\\(h\\) 为PN函数，代入 {{<katex>}}\\(f=g*h\\) 可得：

$$\boxed{S_f(n)=\sum_{d\in[1,n]\ \mathrm{and}\ d\ \mathrm{is}\ PN}h(d)S_g([\frac{n}{d}])}$$

那么整个求解过程可以分成三个板块：

1. 递归或预处理Powerful Number {{<katex>}}\\(d\\) .

2. 求解积性函数 {{<katex>}}\\(h\\) 在某个点上的值.

3. 求解 {{<katex>}}\\(S_g\\) ，也就是积性函数 {{<katex>}}\\(g\\) 的前缀和.

对于 3 ，一般使用杜教筛求解.

对于 2 ，由于 {{<katex>}}\\(h\\) 是积性函数，所以只需要求 {{<katex>}}\\(h(p^c)\\) 后递推即可，那么此时有三种方法可以选择：

- 通项公式（前提是超级强大的数学功底）.

- 逆元：通过 {{<katex>}}\\(g\ast g^{-1}=\varepsilon\\) 求逆后，用 {{<katex>}}\\(h=f_k\ast g^{-1}\\) 求解.

- 通用解法：

	由 {{<katex>}}\\(f=g*h\\) ，有 {{<katex>}}\\(f(p^c)=g(1)h(p^c)+\sum_{i=1}^cg(p^i)h(p^{c-i})\\)

	移项：{{<katex>}}\\(h(p^c)=f(p^c)-\sum_{i=1}^cg(p^i)h(p^{c-i})\\)

	接下来枚举 {{<katex>}}\\(p\\) 递推 {{<katex>}}\\(c\\) 即可.

对于 1 ，小小DFS即可将其拿下.

代码实现参见[【模板】Min_25筛](https://www.luogu.com.cn/problem/P5325).