---
title: 两棵树（可持久线段树+Hash+高精度数）
categories: 笔记
tags: 
- ACM
- csdn
---
​[csdn](https://blog.csdn.net/m0_61322309/article/details/123586007?spm=1001.2014.3001.5502)
Problem Description

给定两棵n个点的有根树，每棵 树的根节点都是1，点的编号为1到n。 定义d(x)为x在第一棵树上到1号点的最短路加上x在 第二棵树上到1号点的最短路，请将1到n按照d从小到 大排序。如果两个点的d相等，则编号小的排在前面。

Input

第一行一个正整数T(1 ≤ T ≤ 5)，表示测试数 据的数量。

每组数据第一行一个正整数n(2 ≤ n ≤ 100000)。 接下来n − 1行，第i行4个正整数a, x, b, y(1 ≤ a, b ≤ i, 1 ≤ x, y ≤ n)，表示i + 1号点在第一棵树上的父亲 是a，它到它父亲的距离为100000000x，i+1号点在第二 棵树上的父亲是b，它到它父亲的距离为100000000y。

Output

每组数据输出n行，第i行输出排序结果中 第i小的点的编号。

输入样例
```txt
1
5
1 2 1 3
2 1 1 5
1 2 3 1
4 2 2 4
```

输出样例
```txt
1
2
5
3
4
```
题意很简单，就是给定两棵有n个节点的树，以1作为树根，求某一个点在两棵树中到1号树根节点的距离之和，按照距离之和从小到大输出节点的编号。若不考虑数据范围，可以通过链式前向星建立两个有向图，从根节点两遍深度优先搜索记录每一个点到根节点的距离之和，然后利用sort函数排序输出。但是考虑到树的边权很大，都是100000000的x次方，无法用正常的变量存储距离的值。

于是使用高精度数处理。将每条边的值看作100000000进制下的数，则长为100000000的x次方的边可表示为100*****00（1后面x个零）。在一棵树中，一个点到树根的距离等于其父亲节点到树根的距离，加上该节点到其父亲节点的距离。由于这个高精度数距离很大，所以不会发生进位（发生进位需要加100000000次1，而数据范围最多到十万），从父亲节点到该节点，只是在某一位加上了1，由此可联想到可持久线段树的单点修改。

维护这样一个可持久线段树，线段树中的元素为高精度数的其中一位。每一个版本的线段树，表示一个n位的点到树根距离的高精度数。因为x,y范围小于十万，所以高精度数的位数不会超过十万，因此线段树中维护的元素不会超过十万。在网上查了下可持久线段树的内存，一般说开到三十二倍数据范围，但是这题其实开到lg100000/lg2+1,差不多20倍就可以了，开到32倍100000kb都容易MLE。

解决了单棵树的建图和存储，接下来的问题是比较两棵树节点距离之和的大小关系。直接去从高到低一位一位相加再比较大小的话，每比较两个数，复杂度都是n，加上sort的复杂度，n^2lgn很明显会超时。这时候想到用字符串hash实现。由于不太会字符串hash，开始写WA了好多次，在大佬的帮助下才找到正确的hash算法。

在线段树二分结构和hash的帮助下，通过递归判断两棵树的子树hash值相加是否相等，可以在lgn的复杂度下判断高精度数的前n位是否相等，直到找到第n位不一样的位数值，判断大小即可。这个hash函数，首先要满足可加性，其次是冲突的概率要非常的小，以正确的去维护两棵树的和。最开始为了减小冲突，想到用普通的双hash，但是这样的话一来减小冲突的作用有限，二来要存两遍hash值很容易MLE。问大佬告诉使用字符串hash，先预处理生成一个用于取模的数的数组。这样能尽可能避免冲突。

下面是AC代码
```cpp
#include<stdio.h>
#include<set>
#include<iostream>
#include<string.h>
#include<string>//getline的头文件，与string.h不同
#include<stack>
#include<vector>
#include<algorithm>
#include<map>
#include<queue>
#include<math.h>
#define P1 233
#define P 1000000007
#define maxn 100005
#define si maxn*20
#define ll long long
using namespace std;
 
int l[si][2],r[si][2],root[maxn][2],tot[2];
ll ha[maxn],val[si][2];
int n;
int head[maxn][2],ne[maxn<<1],len[maxn<<1],to[maxn<<1],cnt;
 
void hash1(){
    ha[0]=1;
    for(int i=1;i<maxn;i++){
        ha[i]=ha[i-1]*10007;
        ha[i]%=P;
    }
}
 
void add(int a,int b,int lenth,int now){
    ne[cnt]=head[a][now];
    len[cnt]=lenth;
    to[cnt]=b;
    head[a][now]=cnt;
    cnt++;
}
 
inline int initia(int left,int right,int now){
    int y=tot[now]++;
    if(left==right)return y;
    int m=(left+right)>>1;
    l[y][now]=initia(left,m,now);
    r[y][now]=initia(m+1,right,now);
    return y;
}
 
void Hash(int y,int L,int R,int now){
    int num=R-(R+L)/2;
    val[y][now]=(val[l[y][now]][now]*ha[num]+val[r[y][now]][now])%P;
}
 
inline int build(int od,int num,int lf,int ri,int now){
    int y=tot[now]++;
    if(lf==ri){
        val[y][now]=val[od][now]+1;
        return y;
    }
    int m=(lf+ri)>>1;
    if(num<=m){
        l[y][now]=build(l[od][now],num,lf,m,now);
        r[y][now]=r[od][now];
    }
    else{
        l[y][now]=l[od][now];
        r[y][now]=build(r[od][now],num,m+1,ri,now);
    }
    Hash(y,lf,ri,now);
    return y;
}
 
bool cmp(int a,int b){
    int lf=1,ri=n,x1=root[a][0],x2=root[a][1],y1=root[b][0],y2=root[b][1];
    while(lf!=ri){
        int m=(lf+ri)>>1;
        bool an=(val[r[x1][0]][0]+val[r[x2][1]][1])%P==(val[r[y1][0]][0]+val[r[y2][1]][1])%P;//一定要将hash值相加后取余数再判断是否相等！！！
        if(an){
            ri=m;
            x1=l[x1][0];
            x2=l[x2][1];
            y1=l[y1][0];
            y2=l[y2][1];
        }
        else{
            lf=m+1;
            x1=r[x1][0];
            x2=r[x2][1];
            y1=r[y1][0];
            y2=r[y2][1];
        }
    }
    if((val[x1][0]+val[x2][1])%P==(val[y1][0]+val[y2][1])%P)return a<b;
    else  return val[x1][0]+val[x2][1]<val[y1][0]+val[y2][1];
}
 
void dfs(int node,int fa,int lenth,int now){
    root[node][now]=build(root[fa][now],lenth,1,n,now);
    for(int i=head[node][now];~i;i=ne[i])dfs(to[i],node,len[i],now);
}
 
int main(){
    int T,a,x,b,y;
    hash1();
    scanf("%d",&T);
    for(int t=0;t<T;t++){
        cnt=0,tot[0]=0,tot[1]=0;
        memset(val,0,sizeof(val));
        memset(head,-1,sizeof(head));
        scanf("%d",&n);
        for(int i=2;i<=n;i++){
            scanf("%d%d%d%d",&a,&x,&b,&y);
            add(a,i,x,0);
            add(b,i,y,1);
        }
        root[0][0]=initia(1,n,0);
        root[0][1]=initia(1,n,1);
        dfs(1,0,0,0);
        dfs(1,0,0,1);
        int ans[maxn];
        for(int i=0;i<=n;i++)ans[i]=i;
        sort(ans+1,ans+n+1,cmp);
        for(int i=1;i<=n;i++)printf("%d\n",ans[i]);
    }
    //system("pause");
}
/*
第一步，输入，链式前向星分别对两棵树建立有向图
第二步，从一号节点开始，分别对两棵树进行深度搜索，并用可持久线段树记录相应节点到一号节点距离的高精度数
注意到对于树中的每个节点，其到一号点的距离等于他父亲节点的距离大小，在高精度进制下某一位加一，故可以用节点的编号作为可持久线段树的版本树根，对于每一个节点到一号点的距离，
可通过其父亲节点的对应版本的线段树进行单点修改
第三步，利用cmp函数排序，递归判断线段树左右子树的Hash值，若右子树的的hash相等，说明高位相等，递归搜索左子树，直到l==r，判断两树节点到一号点的距离和是否相等，
若相等，则按序号大小排序，否则按该位的大小排序
*/
```