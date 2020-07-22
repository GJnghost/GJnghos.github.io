---
layout:     post
title:      『Class Note』算法综合训练一——线性结构算法训练
subtitle:   算法设计实训
date:       2020-06-20
author:     GJ
header-img: img/Texas.jpg
catalog: true
tags:
    - Class Note
---

> ……STL没改，大概思路就是这样，然后……然后滚去改STL了
>
> //看了看好像就栈用了STL，那就，用数组或者结构体改改就行了
>
> //改完了NICE :cherry_blossom:



### A  银行业务队列简单模拟 

---

设某银行有A、B两个业务窗口，且处理业务的速度不一样，其中A窗口处理速度是B窗口的2倍 —— 即当A窗口每处理完2个顾客时，B窗口处理完1个顾客。给定到达银行的顾客序列，请按业务完成的顺序输出顾客序列。假定不考虑顾客先后到达的时间间隔，并且当不同窗口同时处理完2个顾客时，A窗口顾客优先输出。

#### Input

输入为一行正整数，其中第1个数字N(≤1000)为顾客总数，后面跟着N位顾客的编号。编号为奇数的顾客需要到A窗口办理业务，为偶数的顾客则去B窗口。数字间以空格分隔。

#### Output

按业务处理完成的顺序输出顾客的编号。数字间以空格分隔，但最后一个编号后不能有多余的空格。

#### Sample Input

> ```in
> 8 2 1 3 9 4 11 13 15
> ```

#### Sample Output

> ```out
> 1 3 2 9 11 4 13 15
> ```

#### Solution

建立两个队列（Queue），根据题目规则模拟入队出队，就好了

#### Code

```c++
#include<bits/stdc++.h>
using namespace std;
#define TRUE 1
#define FALSE 0
#define MAXSIZE 1000
typedef int ElemType;
typedef struct{
	ElemType data[MAXSIZE];
	int re,ft;
}Queue, *QueuePtr;
int sum=0,n;

QueuePtr CreateQueue(){//创建队列
    QueuePtr q;
    q=(QueuePtr)malloc(sizeof(Queue));
    q->ft=q->re=0;
    return q;
}

bool addQueue(QueuePtr q,ElemType e){//向队列中添加元素，即入队
    if((q->re+1)%MAXSIZE==q->ft){
        printf("Queue Full\n");
        return false;
    }
    q->data[q->re]=e;
    q->re=(q->re+1)%MAXSIZE;
    return TRUE;
}

ElemType delQueue(QueuePtr q){//删除队列中的元素，即出队
    ElemType e;
    if(q->ft==q->re){
        printf("Empty Queue\n");
        return FALSE;
    }
    e=q->data[q->ft];
    q->ft=(q->ft+1)%MAXSIZE;
    return e;
}

bool emptyQueue(QueuePtr q){//判断队列是否为空
    return q->ft==q->re;
}

void printQueue(ElemType e){//输出，注意输出格式末尾无空格
    sum++;
    if(sum<n) printf("%d ",e);
    else printf("%d",e);
}

int main(){
	QueuePtr qa,qb;//qa——A窗口
	qa=CreateQueue();//创建队列
	qb=CreateQueue();
	scanf("%d",&n);
	for(int i=0;i<n;i++){
        int u;
        scanf("%d",&u);
        if(u%2) addQueue(qa,u);//若为奇数，则压入qa中
        else addQueue(qb,u);
	}
	int x;
	while(!emptyQueue(qa)&&!emptyQueue(qb)){//当A窗口与B窗口都有人排队时
        x=delQueue(qa);//A窗口处理第一个人
        printQueue(x);
        if(!emptyQueue(qa)){//A窗口处理第二个人
            x=delQueue(qa);
            printQueue(x);
        }
        x=delQueue(qb);//B窗口处理一个人
        printQueue(x);
	}
	while(!emptyQueue(qa)){//若A窗口仍有人排队，而B窗口无
        x=delQueue(qa);
        printQueue(x);
	}
	while(!emptyQueue(qb)){//若B窗口仍有人排队，而A窗口无
        x=delQueue(qb);
        printQueue(x);
	}
	return 0;
}
```



### B  优美的括号序列

---

某日，小明特别无聊，就想找点东西玩，于是他发现括号()特别好玩，而且新学会了一项技能，将一对小括号()，插入到一个括号序列中，其中插入的规则是，左括号'('的位置要小于右括号')'的位置，不要求插入的左右括号相邻，例如以下，为了方便区分，我们拿ab代表原括号序列将()插入到()中可形成()ab、(a)b、(ab)、a()b、a(b)、ab()等等序列，其中a代表原括号序列的左括号,b代表原括号序列的右括号。小明认为一个括号序列是一个优美的序列当且仅当这个括号序列可以被如下方法构造出来：一开始有一个空串，然后执行0次或者若干次操作，每次操作将()插入到当前的括号序列中。

根据上面的定义：() , (()) , (()())都是优美的括号序列，(() , )( , ()))都不是优美的括号序列。

#### Input

多组输入

每行输入给定一个仅由'(',')'组成的括号序列,长度小于等于1000

题目保证没有空串

#### Output

对于每个输入输出一行，若当前的括号序列是优美的，则输出"YES"（不含引号），否则输出"NO"（不含引号）

#### Sample Input

> ```in
> ()
> (())
> (()())
> ()()
> (()
> )(
> ()))
> ```

#### Sample Output

>```out
>YES
>YES
>YES
>YES
>NO
>NO
>NO
>```

#### Solution

使用栈存储括号，若为左括号'('，则加入栈；若为右括号')'，则消去栈顶字符。

**注意**：讨论非优美括号情况

1. 检测到右括号时')'，栈中不存在左括号于其匹配消除
2. 右括号全部处理完之后，栈中仍留存左括号'('

#### Code

```c++
#include<stdio.h>
#include<stdlib.h>
#include<string.h>
#define STACK_INIT_SIZE 10000
#define STACKINCREMENT 10
#define TURE 1
#define FALSE 0
#define INFEASIBLE -1
#define OVERFLOW -2
#define OK 1
#define ERROR 0
typedef int Selemtype;
typedef int Status;
typedef struct {
    Selemtype* base;
    Selemtype* top;
    int stacksize;
}Sqstack;

Status initstack(Sqstack& s) {
    s.base = (Selemtype*)malloc(STACK_INIT_SIZE * sizeof(Selemtype));
    if (!s.base) exit(OVERFLOW);
    s.top = s.base;
    s.stacksize = STACK_INIT_SIZE;
    return 0;
}

Status travelstack(Sqstack s) {
    Selemtype* p;
    p = s.base;
    printf("%c", *p++);
    while (p != s.top) printf(" %c", *p++);
    return 0;
}

Status Gettop(Sqstack s) {
    if (s.base == s.top) return ERROR;
    return *(s.top - 1);
}

Status push(Sqstack& s, Selemtype e) {
    if (s.top - s.base >= s.stacksize) {
        s.base = (Selemtype*)realloc(s.base, (s.stacksize + STACKINCREMENT) * sizeof(Selemtype));
        if (!s.base) exit(OVERFLOW);
        s.top = s.base + s.stacksize;
        s.stacksize += STACKINCREMENT;
    }
    *s.top = e;
    s.top++;
    return OK;
}

Status pop(Sqstack& s, Selemtype& e) {
    if (s.base == s.top) return ERROR;
    s.top--;
    e = *s.top;
    return OK;
}

int main() {
    char s[10000];
    while (~scanf("%s",s)) {
        char t[10000];
        int top = 0;
        int flag = 0;
        int len = strlen(s);
        for (int i = 0; i < len; i++) {
            if (s[i] == '(') t[top]='(',top++;
            else {
                if (top == 0) { flag = 1; break; }
                else top--;
            }
        }
        if (top != 0) flag = 1;
        if (flag) printf("NO\n");
        else printf("YES\n");
    }
    return 0;
}

```



### C  约瑟夫环

---

N个人围成一圈顺序编号，从1号开始按1、2、3......顺序报数，报p者退出圈外，其余的人再从1、2、3开始报数，报p的人再退出圈外，以此类推。 请按退出顺序输出每个退出人的原序号。

#### Input

输入只有一行，包括一个整数N(1<=N<=3000)及一个整数p(1<=p<=5000)。

#### Output

按退出顺序输出每个退出人的原序号，数据间以一个空格分隔，但行尾无空格。

#### Sample Input

> ```in
> 7 3
> ```

#### Sample Output

> ```out
> 3 6 2 7 5 1 4
> ```

#### Solution

使用静态链表，即数组，进行存储，存储下一个结点的下标。之后按题目步骤模拟即可。

#### Code

```c++
#include<bits/stdc++.h>
#define O_O ios::sync_with_stdio(false);cin.tie(0);cout.tie(0);
using namespace std;
typedef long long ll;
const int maxn = 1e6 + 10;
int a[maxn];

int main(){
    O_O;
    int  n, m, j;
    cin >> n >> m;
    for (int i = 1; i < n; i++) a[i] = i + 1;//此时当i报完数，是i+1报数，即a[i]存储下一个报数的人的下标i+1
    a[n] = 1; j = n;//当第n个人报完数后，是第1个人报数//第一个报数的人是1号，而此时还未开始报数，因此指向n
    int sum = 0, k = 1;//sum用来记录淘汰的人数 k记录每p个人各自的数字
    while (sum < n){
        while (k < m){
            j = a[j];
            k++;
        }//当k==m时，退出循环，即循环得出将被淘汰的人，第k/m/p个人
        sum++; //淘汰人数++
        if (sum == n) cout << a[j];
        else cout << a[j] << " ";
        a[j] = a[a[j]];//a[j]被淘汰，因此其指向下一个。例如,1->2->3->4，a[1]=2,a[2]=3,a[3]=4,a[4]=1,此时k=3,那么第一轮j=a[2]=3被淘汰，sum++，输出a[2]=3，之后令a[2]=a[a[2]]=a[3]=4
        k = 1;
    }
    return 0;
}
```



### D  两个有序链表序列的交集

---

已知两个非降序链表序列S1与S2，设计函数构造出S1与S2的交集新链表S3。

#### Input

输入分两行，分别在每行给出由若干个正整数构成的非降序序列，用−1表示序列的结尾（−1不属于这个序列）。数字用空格间隔。

#### Output

在一行中输出两个输入序列的交集序列，数字间用空格分开，结尾不能有多余空格；若新链表为空，输出`NULL`。

#### Sample Input

> ```in
> 1 2 5 -1
> 2 4 5 8 10 -1
> ```

#### Sample Output

> ```out
> 2 5
> ```

#### Solution

（1）可以令指针p1指向S1的首结点，P2指向S2的首结点，不断比较P1与P2所指向结点的值；若两个结点值相等，则创建新结点将这个值储入到新链表S3的尾部，并将P1和P2分别往前移动（p=p->next）；若不相等，将较小结点的对应结点指针往前移动。循环执行直到某个链表遍历完。
（2）创建结点用malloc申请结点空间，由于每次总是插入到S3的尾部，因此，可以用如r作为S3的尾指针，添加新结点时插入到r结点之后并更新r3。
（3）使用带头结点的链表结构，可以简化程序。注意边界情况，立如当某链表序列为空的情况。

#### Code

```c++
#include<bits/stdc++.h>
using namespace std;
#define TRUE 1
#define FALSE 0
#define OK 1
#define ERROR 0	//-1
#define INFEASIBLE -1
#define OVERFLOW -2
#define MAXSIZE 1000
typedef int ElemType;
typedef int Status;
typedef struct Lnode {
	ElemType data;
	struct Lnode *next;
}Lnode, *LinkList;
int a[1000000];

LinkList CreateList_L(LinkList &L){
	LinkList p,r,tmp;
	L = (LinkList)malloc(sizeof(Lnode));
	r=L;
	int n;
	while(cin>>n){
        if(n==-1) break;
        tmp=(LinkList)malloc(sizeof(struct Lnode));
        tmp->data=n;
        r->next=tmp;
        r=tmp;
	}
	r->next=NULL;
	L=L->next;
	return L;
}
//虽然从这里开始的一大段好像并没有什么用的亚子
Status ListInsert_L(LinkList &L, int i, ElemType e){
	LinkList p = L;
	int j = 0;
	while (p&&j < i - 1) {
		p = p->next;
		++j;
	}
	if (!p || j > i - 1) return ERROR;
	LinkList s = (LinkList)malloc(sizeof(Lnode));		//生成新结点
	s->data = e;
	s->next = p->next;
	p->next = s;
	return OK;
}

Status ListDelete_L(LinkList &L, int i, ElemType &e){
	LinkList p = L, q;
	int j = 0;
	while (p->next&&j < i - 1)
	{
		p = p->next;
		++j;
	}
	if (!(p->next) && j > i - 1) return ERROR;
	q = p->next;
	p->next = q->next;
	e = q->data;
	free(q);
	return OK;
}

Status GetElem_L(LinkList &L, int i, ElemType &e){
	LinkList p;
	p = L->next;
	int j = 1;
	while (p&&j < i)
	{
		p = p->next;
		++j;
	}
	if (!p || j > i) return ERROR;
	e = p->data;
	return OK;
}

int LocateElem_L(LinkList &L, ElemType e){
	LinkList p;
	int j = 1;
	p = L->next;
	while (p)
	{
		if (p->data == e) return j;
		p = p->next;
		++j;
	}
	return ERROR;
}

void ClearList_L(LinkList &L){//Clear链表
	LinkList p;
	ElemType e;
	p = L->next;
	while (p)
	{
		ListDelete_L(L, 1, e);
		p = L->next;
	}
	/*
		LinkList p=L;
		p->next = NULL;
	*/
}

LinkList inter(LinkList L1,LinkList L2){//链表合并
    LinkList p1,p2,r,L;
    L=(LinkList)malloc(sizeof(Lnode));
    p1=L1;
    p2=L2;
    r=L;
    while(p1&&p2){
        if(p1->data==p2->data){
            r->next=p1;
            r=r->next;
            p1=p1->next;
            p2=p2->next;
            r->next=NULL;
        }
        else if(p1->data<p2->data) p1=p1->next;
        else if(p1->data>p2->data) p2=p2->next;
    }
    r->next=NULL;
    return L->next;
}

int Leng(LinkList L){//链表长度
    int sum=0;
    while(L){
        sum++;
        L=L->next;
    }
    return sum;
}

int main(){
	LinkList L1,L2,L3;
	CreateList_L(L1);
	CreateList_L(L2);
	L3=inter(L1,L2);
	int len=Leng(L3),res=0;
	if(len==0) cout<<"NULL\n";
	else{
        while(L3){
            res++;
            if(res==len) cout<<L3->data;
            else cout<<L3->data<<" ";
            L3=L3->next;
        }
	}
	return 0;
}

```



### E  串的模式匹配

---

给定两个由英文字母组成的字符串 String 和 Pattern，要求找到 Pattern 在 String 中第一次出现的位置，并将此位置后的 String 的子串输出。如果找不到，则输出“Not Found”。

本题旨在测试各种不同的匹配算法在各种数据情况下的表现。各组测试数据特点如下：

- 数据0：小规模字符串，测试基本正确性；
- 数据1：随机数据，String 长度为 105，Pattern 长度为 10；
- 数据2：随机数据，String 长度为 105，Pattern 长度为 102；
- 数据3：随机数据，String 长度为 105，Pattern 长度为 103；
- 数据4：随机数据，String 长度为 105，Pattern 长度为 104；
- 数据5：String 长度为 106，Pattern 长度为 105；测试尾字符不匹配的情形；
- 数据6：String 长度为 106，Pattern 长度为 105；测试首字符不匹配的情形。

#### Input

输入第一行给出 String，为由英文字母组成的、长度不超过 106 的字符串。第二行给出一个正整数 *N*（≤10），为待匹配的模式串的个数。随后 *N* 行，每行给出一个 Pattern，为由英文字母组成的、长度不超过 105 的字符串。每个字符串都非空，以回车结束。

#### Output

对每个 Pattern，按照题面要求输出匹配结果。

#### Sample Input

> ```in
> abcabcabcabcacabxy
> 3
> abcabcacab
> cabcabcd
> abcabcabcabcacabxyz
> ```

#### Sample Output

> ```out
> abcabcacabxy
> Not Found
> Not Found
> ```

#### Solution

正常的KMP思路。

**注意**：对于strlen函数，单独使用一个int将其赋值为strlen()。不然有可能会T`血泪教训QAQ`

#### Code

```c++
#include<bits/stdc++.h>
#define O_O ios::sync_with_stdio(false);cin.tie(0);cout.tie(0);
using namespace std;
typedef long long ll;
const int maxn = 1e6 + 10;
char a[maxn], b[maxn];
int nt[maxn];//最长的相同前缀后缀的长度

void Next(char* T) {
    int i = 1, j = 0;
    nt[1] = 0;
    int lent=strlen(T + 1);
    while (i < lent ) {
        if (j == 0 || T[i] == T[j]) {
            i++;
            j++;
            if (T[i] != T[j]) nt[i] = j;
            else nt[i] = nt[j];
        }
        else  j = nt[j];
    }
}

void KMP(char* S, char* T) {
    Next(T);
    int i = 1;
    int j = 1;
    int lens=strlen(S + 1);
    int lent=strlen(T + 1);
    while (i <= lens && j <= lent ) {
        if (j == 0 || S[i] == T[j]) {
            i++;
            j++;
        }
        else j = nt[j];
    }
    if (j > lent) {
        cout << b + 1;
        for (; i <= lens; i++) cout << S[i];
        cout << "\n";
    }
    else cout<<"Not Found\n";
}

int main() {
    O_O;
    int i, n;
    cin >> a + 1;
    cin >> n;
    for (i = 0; i < n; i++) {
        cin >> b + 1;
        KMP(a, b);
    }
    return 0;
}
```



### F  表达式转换

---

算术表达式有前缀表示法、中缀表示法和后缀表示法等形式。日常使用的算术表达式是采用中缀表示法，即二元运算符位于两个运算数中间。请设计程序将中缀表达式转换为后缀表达式。

#### Input

输入在一行中给出不含空格的中缀表达式，可包含`+`、`-`、`*`、`\`以及左右括号`()`，表达式不超过20个字符

#### Output

在一行中输出转换后的后缀表达式，要求不同对象（运算数、运算符号）之间以空格分隔，但结尾不得有多余空格。

#### Sample Input

> ```in
> 2+3*(7-4)+8/4
> ```

#### Sample Output

> ```out
> 2 3 7 4 - * + 8 4 / +
> ```

#### Solution

就就就就栈嘛

**注意**：多位数

#### Code

```c++
#include<bits/stdc++.h>
#define O_O ios::sync_with_stdio(false);cin.tie(0);cout.tie(0);
using namespace std;

int IsDig(char ch){
    return ch>='0'&&ch<='9'||ch=='.';
}

int Zhengfu(char ch){
    return ch=='+'||ch=='-';
}

int compare(char a,char b){//比较判断 0-b优先 1-a优先
    if(b==')') return 1;
    if(a=='('||b=='(') return 0;
    if(b=='+'||b=='-') return 1;
    else if(b=='*'||b=='/'){
        if(a=='+'||a=='-') return 0;
        else if(a=='*'||a=='/') return 1;
    }
}

void F(char *a,int len){
    char stack[25];
    int top=0;
    int space = 0;
    for(int i=0;i<len;i++){
        if(IsDig(a[i])){//是否为数字或小数点
            if(space){
                printf(" ");
                space = 0;
            }
            printf("%c",a[i]);
        }
        else if(Zhengfu(a[i])&&(i?!IsDig(a[i-1])&&a[i-1]!=')':1)){//是否为正负号
            if(a[i]=='-'){
                if(space){
                    printf(" ");
                    space=0;
                }
                printf("%c",a[i]);
            }
        }
        else{//其余符号
            if(top){//栈中有留存
                if(a[i]==')')//当前为')'，那么消去，并输出括号中的符号，即输出栈顶符号直至碰到左括号
                   while(top--){
                      if(stack[top]=='(') break;
                      printf(" %c",stack[top]);
                   }
                else{
                    while(top){//对比栈顶元素与当前元素，如若当前元素优先级低，则输出栈顶元素
                        if(compare(stack[top-1],a[i])) printf(" %c",stack[--top]);
                        else break;
                    }
                    stack[top++]=a[i];
                }
            }
            else stack[top++]=a[i];//没有留存则直接压入
            for(int j=0;j<top;j++)
                if(stack[j]!='('){
                    space = 1;
                    break;
                }
        }
    }
    while(top) printf(" %c",stack[--top]);
}

int main(){
    char a[25];
    scanf("%s",a);
    int len=strlen(a);
    F(a,len);
    return 0;
}
```



### G  求链式线性表的倒数第K项

---

给定一系列正整数，请设计一个尽可能高效的算法，查找倒数第K个位置上的数字。

#### Input

输入首先给出一个正整数K，随后是若干非负整数，最后以一个负整数表示结尾（该负数不算在序列内，不要处理）。

#### Output

输出倒数第K个位置上的数据。如果这个位置不存在，输出错误信息`NULL`。

#### Sample Output

> ```in
> 4 1 2 3 4 5 6 7 8 9 0 -1
> ```

#### Sample Output

> ```out
> 7
> ```

#### Solution

链表没啥想法……（数组或者栈的想法蹦出来太快了

#### Code

```c++
#include<stdio.h>
#include<stdlib.h>
#include<string.h>
int a[1000010];

int main() {
    int k;
    scanf("%d",&k);
    int x,i=1;
    while(~scanf("%d",&x)){
        if(x<0) break;
        a[i++]=x;
    }
    if(k>i-1) printf("NULL\n");
    else printf("%d\n",a[i-k]);
    return 0;
}
```



### H  二分查找

---

输入n值(1<=n<=1000)、n个非降序排列的整数以及要查找的数x，使用**二分查找算法**查找x，输出x所在的下标（0~n-1）及比较次数。若x不存在，输出-1和比较次数。

#### Input

输入共三行： 第一行是n值； 第二行是n个整数； 第三行是x值。

#### Output

输出x所在的下标（0~n-1）及比较次数。若x不存在，输出-1和比较次数。

#### Sample Input

> ```in
> 4
> 1 2 3 4
> 1
> ```

#### Sample Output

> ```out
> 0
> 2
> ```

#### Solution

套二分，没了。

#### Code

```c++
#include<bits/stdc++.h>
#define O_O ios::sync_with_stdio(false);cin.tie(0);cout.tie(0);
using namespace std;
typedef long long ll;
const int maxn = 1e6 + 10;
int a[maxn];

int main(){
	int n;
	cin >> n;
	for (int i = 0; i < n; i++) cin >> a[i];
	int x; cin >> x;
	int l = 0, r = n - 1, flag = -1,sum=0;
	while (l <= r) {
		sum++;
		int mid = l + r >> 1;
		if (a[mid] == x) { flag = mid; break; }
		else if (a[mid] < x) l = mid + 1;
		else r = mid - 1;
	}
	cout << flag << "\n";
	cout << sum << "\n";
}
```



### I  排序

---

给定N个（长整型范围内的）整数，要求输出从小到大排序后的结果。

本题旨在测试各种不同的排序算法在各种数据情况下的表现。各组测试数据特点如下：

- 数据1：只有1个元素；
- 数据2：11个不相同的整数，测试基本正确性；
- 数据3：103个随机整数；
- 数据4：104个随机整数；
- 数据5：105个随机整数；
- 数据6：105个顺序整数；
- 数据7：105个逆序整数；
- 数据8：105个基本有序的整数；
- 数据9：105个随机正整数，每个数字不超过1000。

#### Input

输入第一行给出正整数*N*（≤105），随后一行给出*N*个（长整型范围内的）整数，其间以空格分隔。

#### Output

在一行中输出从小到大排序后的结果，数字间以1个空格分隔，行末不得有多余空格。

#### Sample Input

> ```in
> 11
> 4 981 10 -17 0 -20 29 50 8 43 -5
> ```

#### Sample Output

> ```out
> -20 -17 -5 0 4 8 10 29 43 50 981
> ```

#### Solution

数值较多，快排套用，没了。

#### Code

```c++
#include<bits/stdc++.h>
#define O_O ios::sync_with_stdio(false);cin.tie(0);cout.tie(0);
using namespace std;
typedef long long ll;
const int maxn = 1e6 + 10;
int a[maxn];

void ssort(int left, int right){
	if (left >= right) return;
	int i = left;
	int j = right;
	int key = a[left];
	while (i < j){
		while (i < j && key <= a[j]) j--;
		a[i] = a[j];
		while (i < j && key >= a[i]) i++;
		a[j] = a[i];
	}
	a[i] = key;
	ssort(left, i - 1);
	ssort(i + 1, right);
}

int main(){
	int n;
	scanf("%d", &n);
	for (int i = 0; i < n; i++) scanf("%d", &a[i]);
	ssort(0, n - 1);
	for (int i = 0; i < n-1; i++) printf("%d ", a[i]);
	printf("%d\n", a[n - 1]);
}
```

