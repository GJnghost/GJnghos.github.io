---
layout:     post
title:      『ACM-XCPC | Data Structure』数据结构梳理
subtitle:   就是负责的数据结构部分啦
date:       2020-07-25
author:     GJ
header-img: img/Others_four.jpg
catalog: true
tags:
    - ACM-XCPC
    - Data Structure
---

## STL

> \<list>链表：插入删除操作为O(1)，不能使用[]访问

### \<vector>可变长度数组

|  **一般数**  |                                                              |
| :----------: | ------------------------------------------------------------ |
|    front     | a.front(); `返回a的第一个元素  `                             |
|     back     | a.back(); `返回a的最后一个元素`                              |
|              | a.size(); `返回a中元素的个数；`  <br>a.max_size() `vector最大可以为多大`  <br>a.end() `得到数组的最后一个单元+1的指针` <br>a.rbegin() `将vector反转后的开始指针返回（其实就是原来的end-1）`<br>a.rend() `将vector反转后的结束指针返回（其实就是i原来的begin+1） ` |
| **成员函数** |                                                              |
|    assign    | a.assign(b.begin(),  b.begin()+3); `b为向量，将b的0~2个元素构成的向量赋给a`  <br>a.assign(4,2);`是a只含4个元素，且每个元素为2  ` |
|  push_back   | a.push_back(5); `在a的最后一个向量后插入一个元素，其值为5  ` **O(n)** |
|   pop_back   | a.pop_back(); `删除a向量的最后一个元素  ` **O(n)**           |
|    insert    | a.insert(a.begin()+1,5); `在a的第1个元素（从第0个算起）的位置插入数值5，如a为1,2,3,4，插入元素后为1,5,2,3,4`  <br>a.insert(a.begin()+1,3,5);  `在a的第1个元素（从第0个算起）的位置插入3个数，其值都为5`  a.insert(a.begin()+1,b+3,b+6);  `b为数组，在a的第1个元素（从第0个算起）的位置插入b的第3个元素到第5个元素（不包括b+6），如b为1,2,3,4,5,9,8，插入元素后为1,4,5,9,2,3,4,5,9,8  ` |
|    erase     | a.erase(a.begin()+1,a.begin()+3); `删除a中第1个（从第0个算起）到第2个元素，也就是说删除的元素从a.begin()+1算起（包括它）一直到a.begin()+3（不包括它）  ` |
|     swap     | a.swap(b); `b为向量，将a中的元素和b中的元素进行整体性交换  ` |
|    clear     | a.clear(); `清空a中的元素，大小变为0  `                      |
|    empty     | a.empty(); `判断a是否为空，空则返回ture,不空则返回false  `   |
|    resize    | a.resize(10);  `将a的现有元素个数调至10个，多则删，少则补，其值随机 `<br>a.resize(10,2);  `将a的现有元素个数调至10个，多则删，少则补，其值为2  ` |
|   capacity   | a.capacity(); `返回a在内存中总共可以容纳的元素个数  `        |
|   reserve    | a.reserve(100);`将a的容量（capacity）扩充至100，也就是说现在测试a.capacity();的时候返回值是100.这种操作只有在需要给a添加大量数据的时候才显得有意义，因为这将避免内存多次容量扩充操作（当a的容量不足时电脑会自动扩容，当然这必然降低性能）   ` |
|              | a==b; `b为向量，向量的比较操作还有!=,>=,<=,>,<  `            |

### \<stack>栈

| 成员函数  |                                                           |
| :-------: | --------------------------------------------------------- |
| q.push(a) | 入队。将一个元素a置入队列q中                              |
|  q.pop()  | 出队。从队列q中移除第一个元素 //并不返回该元素            |
|  q.top()  | 返回队列q内的第一个元素（也就是第一个被置入的元素）       |
| q.empty() | 判断队列q是否为空，当队列q为空时，返回ture；否则返回false |
| q.size()  | 访问队列q中的元素个数。                                   |

#### 单调栈

- **复杂度：$$O(n)$$**

- **应用**
  - 对于某个元素i：
  - 左边区间第一个比它小的数，第一个比它大的数
  - 确定这个元素是否是区间最值
  - 右边区间第一个大于它的值
  - 到 右边区间第一个大于它的值 的距离
  - 确定以该元素为最值的最长区间

- **代码**

  ```c++
  //在“尾部”添加元素
  while (r != 0 || ms[r] <= x) r--;
  ms[++r] = x;
  
  //查询栈顶元素
  if (r != 0) printf("%d\n", ms[r]);
  else printf("-1");
  ```

### \<queue>队列

| 成员函数  |                                                           |
| :-------: | --------------------------------------------------------- |
| q.push(a) | 入队。将一个元素a置入队列q中                              |
|  q.pop()  | 出队。从队列q中移除第一个元素 //并不返回该元素            |
| q.front() | 返回队列q内的第一个元素（也就是第一个被置入的元素）       |
| q.back()  | 返回队列q中最后一个元素（也就是最后被插入的元素）         |
| q.empty() | 判断队列q是否为空，当队列q为空时，返回ture；否则返回false |
| q.size()  | 访问队列q中的元素个数。                                   |

#### \<priority_queue>优先队列

- 升序队列：priority_queue <int,vector\<int>,greater\<int> > q;  

  降序队列：priority_queue <int,vector\<int>,less\<int> >q;  

- 插入元素的复杂度：**$$O(logn)$$**，删除元素的复杂度：**$$O(1)$$**

|   成员函数   |                                                           |
| :----------: | --------------------------------------------------------- |
|  q.push(a)   | 入队。将一个元素a置入队列q中                              |
|   q.pop()    | 出队。从队列q中移除第一个元素 //并不返回该元素            |
|   q.top()    | 返回队列q内的第一个元素（也就是第一个被置入的元素）       |
|   q.back()   | 返回队列q中最后一个元素（也就是最后被插入的元素）         |
|  q.empty()   | 判断队列q是否为空，当队列q为空时，返回ture；否则返回false |
|   q.size()   | 访问队列q中的元素个数。                                   |
|   create()   | 创建一个空的优先队列                                      |
|    size()    | 返回队列中的元素数目                                      |
|    max()     | 返回具有最大优先权的元素                                  |
|  insert(x)   | 将x插入队列                                               |
| deleteMax(x) | 从队列中删除具有最大优先权的元素，并将该元素返回至x       |

#### \<deque>双端队列

|        构造函数        |                                |
| :--------------------: | ------------------------------ |
|     deque\<elem>c      | 创建一个空的deque              |
|   deque\<elem>c1(c2)   | 复制一个deque                  |
|    deque\<elem>c(n)    | 创建一个deque，含n个数据       |
| deque\<elem>c(n,elem)  | 创建一个含有n个elem拷贝的deque |
| deque\<elem>c(beg,end) | 创建一个以[beg;end)区间的deque |
|    ~deque\<elem>()     | 销毁所有数据，释放内存         |

|       成员函数        |                                                              |
| :-------------------: | ------------------------------------------------------------ |
|       c.begin()       | 返回第一个元素的迭代器                                       |
|        c.end()        | 返回指向最后一个元素下一个位置的迭代器                       |
|      c.rbegin()       | 返回指向反向队列的第一个元素的迭代器                         |
|       c.rend()        | 返回指向反向队列的最后一个元素的下一个位置                   |
|    c.assign(n,num)    | 将n个num拷贝复制到容器c                                      |
|   c.assign(beg,end)   | 将[beg,end)区间的数据拷贝复制到容器c                         |
|       c.at(pos)       | 返回索引为pos的位置的元素，会执行边界检查，如果越界抛出out_of_range异常 |
|     c.operator[]      | 下标运算符重载                                               |
|       c.empty()       | 判断c容器是否为空                                            |
|       c.front()       | 返回c容器的第一个元素                                        |
|       c.back()        | 返回c容器的最后一个元素                                      |
|       c.size()        | 返回c容器中实际拥有的元素个数                                |
|     c.max_size()      | 返回c容器可能存放元素的最大数量                              |
|       c.clear()       | 清除c容器中拥有的所有元素                                    |
|   c.insert(pos,num)   | 在pos位置插入元素num                                         |
|  c.insert(pos,n,num)  | 在pos位置插入n个元素num                                      |
| c.insert(pos,beg,end) | 在pos位置插入区间为[beg,end)的元素                           |
|     c.erase(pos)      | 删除pos位置的元素                                            |
|   c.erase(beg,end)    | 删除区间为[beg,end)的元素                                    |
|   c.push_back(num)    | 在末尾位置插入元素                                           |
|     c.pop_back()      | 删除末尾位置的元素                                           |
|   c.push_front(num)   | 在开头位置插入元素                                           |
|     c.pop_front()     | 删除开头位置的元素                                           |
|     c.resize(num)     | 重新定义容器大小                                             |
|      c1.swap(c2)      | 交换容器c1，c2                                               |
|      swap(c1,c2)      | 交换容器c1，c2                                               |

#### 单调队列

- **复杂度：$$O(n)$$**
- **应用**
  
  - 可以查询区间最值（不能维护区间k大，因为队列中很有可能没有k个元素）
  - 优化DP
- 单调队列一般是用于优化动态规划方面问题的一种特殊数据结构，且多数情况是与定长连续子区间问题相关联。
  
- **代码：**

  ```c++
  //在“尾部”添加元素x
  while (l != r && mq[r] <= x) r--;
  mq[++r] = x;
  
  //查询队首元素
  if (l != r) printf("%d\n", mq[l+1]);
  else printf("-1\n");
  
  //弹出队首元素
  if (l != r) l++;
  ```

### \<set>集合

- 每次访问**复杂度**：**$$O(logN)$$**

|    成员函数     |                                                              |
| :-------------: | ------------------------------------------------------------ |
|     begin()     | 返回指向第一个元素的迭代器                                   |
|      end()      | 返回指向最后一个元素的迭代器                                 |
|     clear()     | 删除set容器中的所有的元素                                    |
|     empty()     | 判断set容器是否为空                                          |
|    insert(x)    | 往set容器中插入x                                             |
|   max_size()    | 返回set容器包含的元素最大个数                                |
|     size()      | 返回当前set容器中的元素个数                                  |
|    erase(it)    | 删除迭代器指针it处元素                                       |
|    count(a)     | 查找set中某个元素出现的次数。这个函数在set并不是很实用，因为一个键值在set只可能出现0或1次，这样就变成了判断某一键值是否在set出现过了。 |
|     find(a)     | 查找set中某个元素出现的位置。如果找到，就返回这个元素的迭代器，如果这个元素不存在，则返回 s.end() 。 (最后一个元素的下一个位置，s为set的变量名) |
|  equal_range()  | 返回集合中与给定值相等的上下限的两个迭代器                   |
| get_allocator() | 返回集合的分配器                                             |
|  lower_bound()  | 返回指向大于（或等于）某值的第一个元素的迭代器               |
|   key_comp()    | 返回一个用于元素间值比较的函数                               |
|    rbegin()     | 返回指向集合中第一个元素的反向迭代器                         |
|     swap()      | 交换两个集合变量                                             |
|  upper_bound()  | 返回大于某个元素的迭代器                                     |
|  value_comp()   | 返回一个用于比较元素间的值的函数                             |

#### \<multiset>多重集合

### \<map>映射

- 每次访问**复杂度**：**$$O(logN)$$**​
- 红黑树//对数据自动排序

#### \<unordered_map>多重映射

- 每次访问**复杂度**：**$$O(1)$$**
- 哈希表存储

### \<string>字符串

|         成员函数          |                                                              |
| :-----------------------: | ------------------------------------------------------------ |
|         assign()          | 用于赋予新值，assign函数用于将一个字符串的部分内容赋值给另一个string对象 |
|          swap()           | 交换两个字符串的内容                                         |
| +=、append()、push_back() | 在字符串尾部追加内容,"+="可追加string对象，字符以及C风格字符串，append函数则可以追加string对象和C风格字符串，push_back函数则只能追加字符 |
|         insert()          | 用于插入字符串                                               |
|          erase()          | 用于删除字符                                                 |
|    clear()、~string()     | clear()删除str的全部字符，后者销毁所有字符，释放内存         |
|     size()、length()      | 返回字符串的字符数                                           |
|          empty()          | 判断字符串是否为空                                           |

### 其他

#### 运算符重载

```
struct node{
    string name;
    double score;
    bool operator < (const node &a) const{ // 重载“<”操作符，自定义排序规则   
        return a.score < score;//按score由大到小排序。如果要由小到大排序，使用“>”即可。
    }
};
```

## 哈希

> 哇塞！队友会欸（叉腰  `梦幻1e12+7`
>
> 那我是不是可以……

### Hash 表

### 字符串 Hash

### 矩阵 Hash

## 树状数组

- **注意树状数组无法处理0的情况**

### 一维树状数组

- 注意初始化d数组
- **复杂度：$$O(logN)$$**

```c++
const int maxn = 1e5 + 10;
int d[maxn], n;
int lowbit(int x) { return x & (-x); }  //lowbit无法处理0的情况，因为它的结果也是0，最终为一个死循环
int query(int x) { int res = 0; while (x) { res += d[x]; x -= lowbit(x); }return res; } //区间查询 O(logN)
void add(int x, int v) { while (x <= n) { d[x] += v; x += lowbit(x); } }//单点更新 O(logN)对a[x]加上v 
```

### 二维树状数组

> 板子来自[LUOGU P4514 上帝造题的七分钟](https://www.luogu.com.cn/problem/P4514)
>
> 保证所有数据范围在int范围内
>
> 然后……开**ll**会**MLE**

- 差分思想

```c++
const int maxn = 3e3 + 10;
int n, m, ans, d2[4][maxn][maxn];
int lowbit(int x) { return x & (-x); }  //lowbit无法处理0的情况，因为它的结果也是0，最终为一个死循环

void add2(int x, int y, int delta) {
	if (x < 1 || n < x || y < 1 || m < y) return;
	for(int i=x;i<=n;i+=lowbit(i))
		for (int j = y; j <= m; j+=lowbit(j)) {
			d2[0][i][j] += delta;
			d2[1][i][j] += delta * y;
			d2[2][i][j] += delta * x;
			d2[3][i][j] += delta * x * y;
		}
}

int query2(int x, int y) {
	int res = 0;
	for (int i = x; i ;i-=lowbit(i))
		for (int j = y; j; j-=lowbit(j))
			res = res + (x + 1) * (y + 1)* d2[0][i][j] - (x + 1) * d2[1][i][j] - (y + 1) * d2[2][i][j] + d2[3][i][j];
	return res;
}

int main(){
	string s;
	while (cin >> s) {
		int a, b, c, d,delta;
		if (s[0] == 'X') cin >> n >> m;
		else if (s[0] == 'L') {
			cin >> a >> b >> c >> d >> delta;
			add2(a, b, delta);
			add2(a, d + 1, -delta);
			add2(c + 1, b, -delta);
			add2(c+1, d+1, delta);
		}
		else {
			cin >> a >> b >> c >> d;
			cout << query2(a-1, b-1) - query2(a-1, d) - query2(c, b-1) + query2(c, d) << "\n";
		}
	}
	
	return 0;
}
```

### N维树状数组

- 更新**$$2^n$$**个点
- 就再添上for就行啦，n重for

## 线段树

- **应用**：
  - 单点修改 询问区间
  - 区间修改 询问区间
  - 单点修改 询问区间gcd
  - 区间修改 询问区间gcd
  - 询问最短权值路径 dfs序 括号序 数据100000 卡别的做法好像是
  - 扫描线与线段树 例如：1 l r 连线 2 l r 去线 3 询问全段未被覆盖 -------区间修改  我？？？
  - 区间修改 询问连续段 例：以01段为例 分段，各小段，再合并更新
  - 网网络流？？？

> 有一个没写进来，HYSBZ2733永无乡的线段树合并//看了看自己的代码秃然有点不太懂

```c++
const int maxn = 100000 + 10;
ll tree[maxn << 2], a[maxn], lazy[maxn << 2];

void pushup(int rt) {//上传
	tree[rt] = tree[rt << 1] + tree[rt << 1 | 1];
}

void build(int rt, int L, int R){//建树
	if (L == R){
		tree[rt] = a[L];
		return;
	}
	int mid = L + R >> 1;
	build(rt << 1, L, mid);
	build(rt << 1 | 1, mid + 1, R);
	pushup(rt);
}

void pushdown(int rt, int L, int R){//下传
	if (lazy[rt]){
		int mid = L + R >> 1;
		tree[rt << 1] += lazy[rt] * (mid - L + 1);
		tree[rt << 1 | 1] += lazy[rt] * (R - mid);
		lazy[rt << 1] += lazy[rt];
		lazy[rt << 1 | 1] += lazy[rt];
	}
	lazy[rt] = 0;
}

void update(int rt, int L, int R, int p, int x){//单点更新
	if (L == R){
		tree[rt] += x;
		return;
	}
	int mid = L + R >> 1;
	if (p <= mid) update(rt << 1, L, mid, p, x);
	else update(rt << 1 | 1, mid + 1, R, p, x);
	pushup(rt);
}

void update2(int rt, int L, int R, int l, int r,ll x){//区间更新
	if (R<l || L>r) return;
	if (L >= l && R <= r){
		lazy[rt] += x;
		tree[rt] += (ll)x * (R - L + 1);
		return;
	}
	pushdown(rt, L, R);
	int mid = L + R >> 1;
	update2(rt << 1, L, mid, l, r, x);
	update2(rt << 1 | 1, mid + 1, R, l, r, x);
	pushup(rt);
}

ll query(int rt, int L, int R, int l, int r){//区间查询
	if (R<l || L>r) return 0;
	if (R <= r && L >= l) return tree[rt];
	pushdown(rt, L, R);
	int mid = L + R >> 1;
	return query(rt << 1, L, mid, l, r) + query(rt << 1 | 1, mid + 1, R, l, r);
}
```



## 平衡树

![在这里插入图片描述](https://img-blog.csdnimg.cn/20181216160637132.png)

### Treap

> Treap的本质是一颗二叉查找树，只是在每个结点上都附加了一个优先级的信息。保证每个点的优先级都比左右儿子小，利用优先级，我们可以把这颗树看成一个小根堆。

- Treap树在随机给优先级的情况下，可以在期望**O(logn)**的时间复杂度里完成：
  - 一个结点的插入
  - 一个结点的删除
  - 查询第K大的值
  - 给定一个值返回它是第几大

```c++
inline int Rand() { static ll r = 4911; return (r *= 23333) %= 2147483647; }
struct Node {
	Node* ch[2];
	int r, v, s;//s表示节点数
	Node(int v) :v(v) { ch[0] = ch[1] = NULL; r = rand(); s = 1; }//在cstdlib头声明
	int cmp(int x) { if (x == v)return -1; return x < v ? 0 : 1; }
	void maintain() { s = 1; if (ch[0] != NULL) s += ch[0]->s; if (ch[1] != NULL) s += ch[1]->s; }
}; //root全局使用的话可以在这里跟上*root
void rotate(Node*& o, int d) {//o为根节点 d=0左旋=1右旋
	Node* k = o->ch[d ^ 1];//左旋时使用根节点的右儿子，右旋时使用根节点的左二子，所以^1取反
	o->ch[d ^ 1] = k->ch[d];//根节点的d^1儿子变为根节点的d^1的d儿子
	k->ch[d] = o;//根节点的d^1儿子的d儿子指向原根节点
	o->maintain();//更新
	k->maintain();
	o = k;
}
void insert(Node*& o, int x) {//o子树中事先不存在x
	if (o == NULL) o = new Node(x);
	else {
		int d = x < (o->v) ? 0 : 1;//如这里改成int d=o->cmp(x);//就不可以插入相同的值，因为d可能为-1
		insert(o->ch[d], x);
		if (o->ch[d]->r > o->r) rotate(o, d ^ 1);
	}
	o->maintain();
}

void remove(Node*& o, int x) {
	if (o == NULL) return;//空时返回
	int d = o->cmp(x);//判断当前节点是否为要删除的节点
	if (d == -1) {//是
		Node* u = o;
		if (o->ch[0] && o->ch[1]) {
			int d2 = (o->ch[0]->r < o->ch[1]->r) ? 0 : 1;
			rotate(o, d2);
			remove(o->ch[d2], x);
		}
		else {
			if (o->ch[0] == NULL) o = o->ch[1];
			else o = o->ch[0];
			delete u;//这个要放里面
		}
	}
	else remove(o->ch[d], x);//如果当前元素大于删除元素，删除左子树；反之删除右子树
	if (o) o->maintain();//之前o存在,但是删除节点后o可能就是空NULL了,所以需要先判断o是否为空
}

//返回关键字从小到大排序时的第k个值
//若返回第K大的值，只需要把ch[0]和ch[1]全互换就可以了
int kth(Node* o, int k) {
	assert(o && k >= 1 && k <= o->s);//保证输入合法,根据实际问题返回
	int s = (o->ch[0] == NULL) ? 0 : o->ch[0]->s;
	if (k == s + 1) return o->v;
	else if (k <= s) return kth(o->ch[0], k);
	else return kth(o->ch[1], k - s - 1);
}

//返回值x在树中的排名,就算x不在o树中也能返回排名
//返回值范围在[1,o->s+1]范围内
int rank(Node* o, int x) {
	if (o == NULL) return 1;//未找到x;
	int num = o->ch[0] == NULL ? 0 : o->ch[0]->s;
	if (x == o->v) return num + 1;
	else if (x < o->v) return rank(o->ch[0], x);
	else return rank(o->ch[1], x) + num + 1;
}


int main() {
	int n = 0;
	while (scanf("%d", &n) == 1 && n) {
		Node* root = NULL; //初始化为NULL
		for (int i = 0; i < n; i++) {
			int x;
			scanf("%d", &x);
			if (root == NULL) root = new Node(x);
			else insert(root, x);
		}
		int v;
		while (scanf("%d", &v) == 1)printf("%d\n", rank(root, v));
	}
	return 0;
}

```
