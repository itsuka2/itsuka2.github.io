---
title: "分块思想"
description: ""
date: 2023-07-17
lastmod: 2025-02-12
tags:
- 思想
- 数据结构
- 阶段总结
heroStyle: background
---

**update: 2023/12/12 fixed pictures**

**update: 2025/02/12 fixed KaTeX**

**本文中所有图片均使用Power Point制作**

## 基本分块

俗话说得好：

{{<katex>}}\\(\Huge 暴力+暴力=分块\\)

分块总的来说就是线段树的阉割版，其时间复杂度基本维持在 {{<katex>}}\\(\sqrt N\\) 数量级。一般的分块支持单点/区间查询以及单点/区间修改.

分块的块长**一般**是 {{<katex>}}\\(\sqrt N\\) （ {{<katex>}}\\(N\\) 是数组大小），例如，对于一个数组 {{<katex>}}\\(A\\) ，{{<katex>}}\\(|A|=13\\) ，每一块的长度便是 {{<katex>}}\\(\sqrt {13}\approx3\\) ，但是分块完成后我们会发现最后一块的大小是 {{<katex>}}\\(1\\) ，不足其他块的大小（如下图）.

![https://www.z4a.net/images/2023/12/12/1-4.png](https://www.z4a.net/images/2023/12/12/1-4.png)

### 区间修改区间查询

分了块后我们便可以干很多事了，单点查询/修改就不说了，直接访问数组下标进行修改.我们来说说区间查询/修改，例如还是那个数组 {{<katex>}}\\(A\\) ，现在我们要修改下标为 {{<katex>}}\\(2\\) 到 {{<katex>}}\\(7\\) 的区间，这个时候我们要对每一块生成一个懒标记，于是我们的思路便很明确了，对于这段区间里整块的部分直接修改懒标记，剩下的不足一整块暴力修改（如下图）.同理，查询也是一样，只是要注意统计时要把所有懒标记加上，不管是整块还是不足整块.下面也会给出一份区间修改（增加）区间查询（最大值）的代码.

![https://www.z4a.net/images/2023/12/12/3-2.png](https://www.z4a.net/images/2023/12/12/3-2.png)

```cpp
#include <bits/stdc++.h>
using namespace std;

const int inf = 0x3fffffff;
int n, m, s;
int a[100100], chunk[100100], lazy[100100];

void modify(int x, int y, int z) {
	int i = (x-1)/s+1;
	int j = (y-1)/s+1;
	chunk[i] = -inf;
	chunk[j] = -inf;
	if (i == j) {
		for (int k = x; k <= y; k++) {
			a[k] += z;
		}
		for (int k = (i-1)*s+1; k <= j*s; k++) {
			if (a[k] > chunk[i]) chunk[i] = a[k];
		}
	} else {
		for (int k = x; k <= i*s; k++) {
			a[k] += z;
		}
		for (int k = (i-1)*s+1; k <= i*s; k++) {
			if (a[k] > chunk[i]) chunk[i] = a[k];
		}
		for (int k = (j-1)*s+1; k <= y; k++) {
			a[k] += z;
		}
		for (int k = (j-1)*s+1; k <= j*s; k++) {
			if (a[k] > chunk[j]) chunk[j] = a[k];
		}
		for (int k = i+1; k < j; k++) {
			lazy[k] += z;
		}
	}
}

int search(int x, int y) {
	int i = (x-1)/s+1;
	int j = (y-1)/s+1;
	int ans = -inf;
	if (i == j) {
		for (int k = x; k <= y; k++) {
			if (a[k]+lazy[i] > ans) ans = a[k]+lazy[j];
		}
	} else {
		for (int k = x; k <= i*s; k++) {
			if (a[k]+lazy[i] > ans) ans = a[k]+lazy[i];
		}
		for (int k = (j-1)*s+1; k <= y; k++) {
			if (a[k]+lazy[j] > ans) ans = a[k]+lazy[j];
		}
		for (int k = i+1; k < j; k++) {
			if (chunk[k]+lazy[k] > ans) ans = chunk[k]+lazy[k];
		}
	}
	return ans;
}

signed main() {
	scanf("%d", &n);
	s = sqrt(n);
	for (int i = 1; i <= n; i++) {
		scanf("%d", a+i);
		if (i%s == 1 || a[i] > chunk[(i-1)/s+1]) chunk[(i-1)/s+1] = a[i];
	}
	cin >> m;
	while (m--) {
		char op[3];
		int x, y, z;
		scanf("%s%d%d", op, &x, &y);
		if (op[2] == 'D') {
			cin >> z;
			modify(x, y, z);
		} else {
			cout << search(x, y) << endl;
		}
	}
}

/*
Input:
5
1 2 3 2 5
5
ADD 1 4 3
ASK 2 3
ASK 3 5
ADD 2 4 2
ASK 2 5

Output:
6
6
8
*/
```

{{<katex>}}
### 动态区间第 \\(K\\) 小

分块的大部分题目并不像这样简单，例如下面的动态区间第 {{<katex>}}\\(K\\) 小数例题.

> 给定一个由 {{<katex>}}\\(N\\) 个数组成的序列 {{<katex>}}\\(\{A_1,A_2,...,A_N\}\\)
每次可以将 {{<katex>}}\\(A_k\\) 的值改为 {{<katex>}}\\(t\\) ，或者提问序列中 {{<katex>}}\\(\{A_l,..,A_r\}\\) 中第 {{<katex>}}\\(k\\) 小的数的值.
> 

对于这道题，我们需要使用另一个数组 {{<katex>}}\\(B\\) 用来将每个块内的元素排序，需要查询时先使用二分答案，再在 `check()` 内对于整块的统计答案直接使用排序的数组进行二分，正所谓一个分块套二分再套二分，细节很多，详见下码.

```cpp
void modify(int k, int t) {  // 单点修改，需要排序
	int F = (k-1)/s+1;
	a[k] = t;
	b[F].clear();
	for (int i = (F-1)*s+1; i <= F*s; i++) {
		b[F].push_back(a[i]);
	}
	sort(b[F].begin(), b[F].end());
}

int check(int x, int y, int f) {  // 统计信息
	int i = (x-1)/s+1;
	int j = (y-1)/s+1;
	int ans = 0;
	if (i == j) {
		for (int k = x; k <= y; k++) {
			if (a[k] < f) ans++;
		}
	} else {
		for (int k = x; k <= i*s; k++) {
			if (a[k] < f) ans++;
		}
		for (int k = (j-1)*s+1; k <= y; k++) {
			if (a[k] < f) ans++;
		}
		for (int k = i+1; k < j; k++) {
			ans += lower_bound(b[k].begin(), b[k].end(), f) -b[k].begin();  // 直接二分统计整块内的答案
		}
	}
	return ans;
}

int query(int x, int y, int k) {  // 二分答案
	int l = 0, r = 50005, mid;
	while (l+1 < r) {
		mid = (l+r)>>1;
		if (check(x, y, mid) >= k) r = mid;
		else l = mid;
	}
	return l;
}
```

### 带多个懒标记的分块

除此之外，还有要同时开两个 `lazy` 的题目——[数列分块入门7](https://loj.ac/p/6283)，关键在于处理两个运算法则之间的优先级.

[数列分块入门5](https://loj.ac/p/6281)——在根号开到一定程度时块内的元素会全部变为 {{<katex>}}\\(1\\)，这时可以不用暴力统计，直接计算块长.

### 块状链表

另外，还有一类分块题，算是属于半个块状数组（或者说块状链表）.

众所周知，数组访问时间复杂度 {{<katex>}}\\(O(1)\\) ，修改 {{<katex>}}\\(O(n)\\) ，而链表访问 {{<katex>}}\\(O(n)\\) ，修改 {{<katex>}}\\(O(1)\\) .

而块状链表，就是两者的结合体，它查询 {{<katex>}}\\(O(\sqrt n)\\) ，修改 {{<katex>}}\\(O(\sqrt n)\\) ，两者时间复杂度相差不大，是个折中方案，具体长什么样见下图.

![https://www.z4a.net/images/2023/12/12/5a4959f4f5a7a29a2.png](https://www.z4a.net/images/2023/12/12/5a4959f4f5a7a29a2.png)

那我们应该如何维护来保证它们的时间复杂度呢？很简单，我们只需要在一个块的大小大于一定的值后暴力拆分成两块接在原块后面，具体来说，我们要在一块的大小大于 {{<katex>}}\\(2\sqrt N\\) 时分裂（注意：此处的 {{<katex>}}\\(N\\) 指的是所有元素，包括了后面新增的元素，也就是说每次添加元素时要重新计算阈值长度）.理清思路后，代码实现很简单了（见下，[例题](https://loj.ac/p/6282)）.

```cpp
int cnt;  // 总块数，用于给新块编号
int nc;  // 元素总数

typedef pair<int, int> pii;  // 不会吧不会吧，都3202年了不会还有人不会用typedef吧

pii locate(int x) {  // 定位元素
	int i = 1;
	while (x > block[i].siz) {
		x -= block[i].siz;
		i = block[i].to;
	}
	return {i, x};
}
void split(int x, int y) {
	cnt++;
	for (int i = y; i <= block[x].siz; i++) {
		block[cnt].a[++block[cnt].siz] = block[x].a[i];
		block[x].a[i] = 0;
	}
	block[cnt].to = block[x].to;
	block[x].to = cnt;
}

// 临时定义一下（不要问我为什么不开变量
#define x p.first
#define y p.second
void insert(int t, int v) {
	pii p = locate(t);
    // 暴力插入
	for (int i = block[x].siz; i >= y; i--) {
		block[x].a[i+1] = block[x].a[i];
	}
	block[x].siz++;
	block[x].a[y] = v;
	nc++;
	s = sqrt(nc);
	if (block[x].siz >= 2*s) {
		split(x, block[x].siz/2+1);
		block[x].siz = block[x].siz/2;
	}
}
#undef x
#undef y

int query(int x) {  // 极简的查询的函数
	pii p = locate(x);
	return block[p.first].a[p.second];
}
```

## 莫队

除了一般的分块题和块状链表外，分块还有一个重要作用——莫队，它可以解决一部分的区间查询题目(尤其是查询某种区间有几种值的).

先说本质，本质并不是分块，而是双指针.莫队在运行时会用到两个数组，一个是原来的数组，另一个是 `cnt` 数组，`cnt` 数组是用来记录每个数出现的次数(在某些时候可能需要配合离散化食用).下面是算法基本的运行过程.

首先我们有两个指针 `i` 和 `j` ，先不考虑为什么他们在这个位置上，现在我们要把他们移到既定位置.我们有一种很简单的思路，先把左（ `i` ）移到既定位置的左端点，再把右（ `j` ）移到既定位置的右端点上.而在这个期间，我们可以顺便统计 `cnt` 数组，并且维护 `tot` 变量（统计种类，具体来讲是在 `cnt` 某个值从 {{<katex>}}\\(0\\) 到 {{<katex>}}\\(1\\) 或是从 {{<katex>}}\\(1\\) 到 {{<katex>}}\\(0\\) 的过程中修正）.于是我们就有了一个基本的模型了，就是不断通过移动双指针来处理每个询问，但是很明显，这样移来移去，`i` 最多会移动 {{<katex>}}\\(MN\\) 次（ {{<katex>}}\\(M\\) 是询问次数，{{<katex>}}\\(N\\) 是数组长度），`j` 也同理，这样的时间复杂度是不行的.而莫队算法就是在此处做了优化.

### 不带修版本

莫队首先会把询问离线，并把数组的点进行分块，而排序询问的第一关键字是每个询问左端点**所在块的编号**，而第二关键字是右端点.具体实现如下：

```cpp
#include <bits/stdc++.h>
using namespace std;

int n, m, s;
int a[50050], cnt[1000010];
int ans[200020], tot = 0;  // ans是将答案还原的数组

struct node {
    int l, r, id;
} q[200020];

bool cmp(node a, node b) {
    if ((a.l-1)/s+1 == (b.l-1)/s+1) return a.r < b.r;  // 排序
    return a.l < b.l;
}

void add(int p) {
    if (++cnt[a[p]] == 1) tot++;  // 对应左端点左移与右端点右移
}

void del(int p) {
    if (--cnt[a[p]] == 0) tot--;  // 对应左端点右移与右端点左移
}

signed main() {
    cin >> n;
    s = sqrt(n);
    for (int i = 1; i <= n; i++) {
        cin >> a[i];
    }
    cin >> m;
    for (int i = 1; i <= m; i++) {
        cin >> q[i].l >> q[i].r;
        q[i].id = i;
    }
    sort(q+1, q+m+1, cmp);  // 处理询问
    int l = 0, r = 0;
    for (int i = 1; i <= m; i++) {
        while (r < q[i].r) add(++r);
        while (r > q[i].r) del(r--);
        while (l < q[i].l) del(l++);
        while (l > q[i].l) add(--l);
        ans[q[i].id] = tot;
    }
    for (int i = 1; i <= m; i++) {
        cout << ans[i] << endl;
    }
    return 0;
}
```

### 带修版本

除此之外，莫队还有一种带修改版本.

> * 不是说了将询问离线了吗，离线处理时怎么修改啊.
> 
> 
> * 还是可以修改的，现在带修改功能得将修改的点还原到询问前的状态.
> 
> * 现在时间也得移来移去了.
> 
> * 嗯？时间移来移去？像 `i` 和 `j` 那样？
> 
> * 那这就好实现了啊.新增一维来处理时间.
> 
> * 具体怎么讲？
> 
> * 记录一下每次修改前后的状态，每次将时间移动时便一步步更换状态，直到回溯到指定时间，同时 `cmp` 函数也要改一下.
> 

于是，我们就有了这个代码：

```cpp
#include <bits/stdc++.h>
// 回避关键词检测的打包函数
#define pack1 {x, y, ccnt, qcnt}
#define pack2 {x, y}
using namespace std;

int l, r;
int n, m, s, tcnt;
int a[133344], cnt[1000010];
int ans[133344], tot;
int chunk[133344];

struct node{  // 记录询问
	int l, r, t, i;
}q[133344];
int qcnt;

bool cmp(node a, node b) {  // 新的排序函数
	if (chunk[a.l] == chunk[b.l] && chunk[a.r] == chunk[b.r]) return a.t < b.t;
	if (chunk[a.l] == chunk[b.l]) return a.r < b.r;
	return a.l < b.l;
}

struct cg{  // 记录修改
	int i, v;
}c[133344];
int ccnt;

void add(int p) {
	tot += !cnt[p]++;  // 用于l与r的修改，意义同上
}

void del(int p) {
	tot -= !--cnt[p];  // 同上
}

void timem(int i, int p) {
	if (q[i].l <= c[p].i && c[p].i <= q[i].r) {  // 时间更改器（雾
		del(a[c[p].i]);
		add(c[p].v);
	}
	swap(a[c[p].i], c[p].v);  // 因为有去必有回，所以下一次使用这个位置一定是把它更改回来
}

int main() {
	cin >> n >> m;
	s = pow(n*1.0, 2.0/3.0);  // 适用于莫队的特殊分块大小
	for (int i = 1; i <= n; i++) {
		cin >> a[i];
		chunk[i] = (i-1)/s+1;  // 记录块号（也可以不记）
	}
	for (int i = 1; i <= m; i++) {
		char op;
		int x, y;
		cin >> op >> x >> y;
		if (op == 'Q') {
			q[++qcnt] = pack1;
		} else {
			c[++ccnt] = pack2;
		}
	}
	sort(q+1, q+qcnt+1, cmp);
	int l, r, t;
	l = r = t = 0;
	for (int i = 1; i <= qcnt; i++) {  // 将三个维度移至指定位置
		while (l < q[i].l) del(a[l++]);
		while (l > q[i].l) add(a[--l]);
		while (r < q[i].r) add(a[++r]);
		while (r > q[i].r) del(a[r--]);
		while (t < q[i].t) timem(i, ++t);
		while (t > q[i].t) timem(i, t--);
		ans[q[i].i] = tot;  // 记录答案
	}
	for (int i = 1; i <= qcnt; i++) {
		cout << ans[i] << endl;
	}
    return 0;
}
```