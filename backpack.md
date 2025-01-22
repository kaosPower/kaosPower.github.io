---
layout: default
title: 背包九讲
---

# 背包九讲
注意当空间优化成1维后,只有完全背包和多重背包单调队列优化体积是从小到大循环的,其余所有背包问题都是从大到小循环的

```c++
for 物品
	for 体积
		for 决策
```
## 1.01背包问题
每件物品只能选一次
```c++
/*
f[i][j]:前i个物品当前使用的总体积不超过j的情况下,所能获得的最大价值
result=max{f[n][0~v]}
1.不选第i个物品,f[i][j]=f[i-1][j];
2.选第i个物品,f[i][j]=f[i-1][j-v[i]]+val[i];
f[i][j]=max{1,2}
*/
#include <iostream>
#include <cstring>
#include <algorithm>
using namespace std;

const int N=1010;

int n,m;
int f[N][N]; //c++默认初始化成0
int v[N],w[N]; //v代表体积,w代表价值
int main()
{
    cin>>n>>m;
    for (int i=1;i<=n;i++) cin>>v[i]>>w[i];
    
    for (int i=1;i<=n;i++)
        for (int j=0;j<=m;j++)
        {
            f[i][j]=f[i-1][j];
            if (j>=v[i]) f[i][j]=max(f[i-1][j],f[i-1][j-v[i]]+w[i]);
        }
    cout<<f[n][m]<<endl;
    return 0;
}
```

```c++
//空间压缩
#include <iostream>
#include <cstring>
#include <algorithm>
using namespace std;

const int N=1010;

int n,m;
//如果把所有f[i]都初始化成0,就是<=j的最大价值
//如果把f[0]初始化成0,其余初始化成负无穷,就是恰好=j的最大价值
int f[N];
int v[N],w[N];
int main()
{
    cin>>n>>m;
    for (int i=1;i<=n;i++) cin>>v[i]>>w[i];
    
    for (int i=1;i<=n;i++)
        //从大到小枚举
        for (int j=m;j>=v[i];j--)
        	f[j]=max(f[j],f[j-v[i]]+w[i]);
    cout<<f[m]<<endl;
    return 0;
}
```

## 2.完全背包问题
每件物品可以选无限次
```c++
#include <iostream>
#include <cstring>
#include <algorithm>
using namespace std;

const int N=1010;

int n,m;
int f[N];
int v[N],w[N];
int main()
{
    cin>>n>>m;
    for (int i=1;i<=n;i++) cin>>v[i]>>w[i];
    
    for (int i=1;i<=n;i++)
        //从小到大枚举
        for (int j=v[i];j<=m;j++)
        	f[j]=max(f[j],f[j-v[i]]+w[i]);
    cout<<f[m]<<endl;
    return 0;
}
```

## 3.多重背包问题
每件物品选的次数是不同且有限次
```c++
/*
f[i]总体积是i的情况下,最大价值是多少
两种初始化方法
1.f[i]=0;
f[m]
2.f[0]=0,f[i]=-INF,i!=0;
max{f[0...m]}
*/
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;

const int N=110;

int n,m;
int f[N];

int main()
{
    cin>>n>>m;
    for (int i=0;i<n;i++)
    {
        int v,w,s;
        cin>>v>>w>>s;
        for (int j=m;j>=0;j--)
            for (int k=1;k<=s && k*v<=j;k++)
                f[j]=max(f[j],f[j-k*v]+k*w);
    }
    cout<<f[m]<<endl;
    return 0;
}
```
```c++
//多重背包二进制分组优化
/*
将多重背包转化成01背包,复制s份,变成01背包问题
7,
*/
#include <cstdio>
#include <cstring>
#include <iostream>
#include <algorithm>
#include <vector>
using namespace std;

const int N=2010;
int n,m;
int f[N];

struct Good
{
    int v,w;
};

int main()
{	
    vector<Good> goods;
    cin>>n>>m;
    for (int i=0;i<n;i++)
    {
        int v,w,s;
        cin>>v>>w>>s;
        for (int k=1;k<=s;k*=2)
        {
            s-=k;
            goods.push_back({v*k,w*k});
        }
        if (s>0) goods.push_back({v*s,w*s});
    }
    for (auto good:goods)
        for (int j=m;j>=good.v;j--)
            f[j]=max(f[j],f[j-good.v]+good.w);
    cout<<f[m]<<endl;
    return 0;
}
```
```c++
//单调队列优化+空间压缩(滚动数组实现)
#include <cstring>
#include <iostream>
#include <algorithm>

using namespace std;
const int N=20010;
int n,m;
int f[N],g[N],q[N];
int main()
{
    cin>>n>>m;
    for (int i=0;i<n;i++)
    {
        int v,w,s;
        cin>>v>>w>>s;
        memcpy(g,f,sizeof f);
        for (int j=0;j<v;j++)
        {
            //单调队列
            int hh=0,tt=-1;
            for (int k=j;k<=m;k+=v)
            {
                if (hh<=tt && q[hh]<k-s*v) hh++;
                if (hh<=tt) f[k]=max(f[k],g[q[hh]]+(k-q[hh])/v*w);
                while (hh<=tt && g[q[tt]]-(q[tt]-j)/v*w<=g[k]-(k-j)/v*w) tt--;
                q[++tt]=k;
            }
        }
    }
    cout<<f[m]<<endl;
    return 0;
}
```

## 4.混合背包问题
物品一共有很多种,有各种信息
```c++
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;
const int N=1010;

int n,m;
int f[N];

struct Thing
{
    int kind;
    int v,w;
};
vector<Thing> things;
int main()
{
    cin>>n>>m;
    //输入时分01背包,完全背包,多重背包讨论,同时将多重背包利用二进制分组转化成01背包
    for (int i=0;i<n;i++)
    {
        int v,w,s;
        cin>>v>>w>>s;
        if (s<0) things.push_back({-1,v,w});
        else if (s==0) things.push_back({0,v,w});
        else
        {
            for (int k=1;k<=s;k*=2){
                s-=k;
                things.push_back({-1,v*k,w*k});
            }
            if (s>0) things.push_back({-1,v*s,w*s});
        }
    }
    //分01背包和完全背包两种情况求解,多重背包在输入时已经转化成了01背包
    for (auto thing:things)
    {
        if (thing.kind<0){
            for (int j=m;j>=thing.v;j--) f[j]=max(f[j],f[j-thing.v]+thing.w);
        }
        else
        {
            for (int j=thing.v;j<=m;j++) f[j]=max(f[j],f[j-thing.v]+thing.w);
        }
    }
    cout<<f[m]<<endl;
    return 0;
}
```
## 5.二维费用的背包问题
有两个维度的限制
```c++
#include <iostream>
#include <algorithm>

using namespace std;

const int N=110;

int n,v,m;
int f[N][N];

int main()
{
    cin>>n>>v>>m;
    for (int i=0;i<n;i++)
    {
        int a,b,c;
        cin>>a>>b>>c;
        for (int j=v;j>=a;j--)
            for (int k=m;k>=b;k--)
                f[j][k]=max(f[j][k],f[j-a][k-b]+c);
                
    }
    cout<<f[v][m]<<endl;
    return 0;
}
```

## 6.分组背包问题
把各种物品分成若干组,组内每种物品只能选一件
```c++
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;

const int N=110;

int n,m;
int f[N],v[N],w[N];

int main()
{
    cin>>n>>m;
    for (int i=0;i<n;i++)
    {
        int s;
        cin>>s;
        for (int j=0;j<s;j++) cin>>v[j]>>w[j];
        for (int j=m;j>=0;j--)
            for (int k=0;k<s;k++)
                if (j>=v[k])
                	f[j]=max(f[j],f[j-v[k]]+w[k]);
    }
    cout<<f[m]<<endl;
    return 0;
}
```

## 7.背包问题求方案数
```c++
//f[i][j]=max(f(i-1,j),f(i-1,j-vi)+wi),此题f[i][j]为了方便表示恰好等于v而不是<=v
//g[i][j]:f[i][j]取最大值的方案数

#include <cstring>
#include <iostream>
using namespace std;

const int N=1010,mod=1e9+7;

int n,m;
int f[N],g[N]; //g[j]保存体积恰好是j的方案数

int main()
{
    cin>>n>>m;
    
    memset(f,-0x3f,sizeof f); //将f[j]初始化成负无穷,j!=0
    f[0]=0;
    g[0]=1;
    for (int i=0;i<n;i++)
    {
        int v,w;
        cin>>v>>w;
        for (int j=m;j>=v;j--)
        {
            int maxv=max(f[j],f[j-v]+w);
            int cnt=0;
            if (maxv==f[j]) cnt+=g[j];
            if (maxv==f[j-v]+w) cnt+=g[j-v];
            g[j]=cnt%mod;
            f[j]=maxv;
        
        }
    }
    int res=0;
    for (int i=0;i<=m;i++) res=max(res,f[i]);
	int cnt=0;
    for (int i=0;i<=m;i++)
        if (res==f[i])
            cnt=(cnt+g[i])%mod;
    cout<<cnt<<endl;
    return 0;
}
```

## 8.背包问题求具体方案
```c++
#include <cstring>
#include <iostream>
#include <algorithm>
using namespace std;
const int N=1010;

int n,m;
int v[N],w[N],f[N][N];

int main()
{
    cin>>n>>m;
    for (int i=1;i<=n;i++) cin>>v[i]>>w[i];
    //从后往前枚举
    for (int i=n;i>=1;i--)
        for (int j=0;j<=m;j++)
        {
            f[i][j]=f[i+1][j];
            if (j>=v[i]) f[i][j]=max(f[i][j],f[i+1][j-v[i]]+w[i]);
        }
    int vol=m;
    for (int i=1;i<=n;i++)
        if (vol>=v[i] && f[i][vol]==f[i+1][vol-v[i]]+w[i])
        {
            cout<<i<<' ';
            vol-=v[i];
        }
    return 0;
}
```
##  9.有依赖的背包问题
选某个物品必须先选它依赖的物品
```c++
//树形dp
//转化成分组背包
//时间复杂度O(n^3)
#include <cstring>
#include <iostream>
#include <algorithm>

using namespace std;

const int N=110;

int n,m;
int v[N],w[N];
int h[N],e[N],ne[N],idx;
int f[N][N];

//邻接表基本操作
void add(int a,int b)
{
    e[idx]=b,ne[idx]=h[a],h[a]=idx++;
}
void dfs(int u)
{
    for (int i=h[u];~i;i=ne[i]) //循环物品组,~i等价于i!=-1
    {
        int son=e[i];
        dfs(e[i]);
        //分组背包
        for (int j=m-v[u];j>=0;j--) //循环体积
            for (int k=0;k<=j;k++) //循环决策
                f[u][j]=max(f[u][j],f[u][j-k]+f[son][k]);
    }
    //将物品u加进去
    for (int i=m;i>=v[u];i--) f[u][i]=f[u][i-v[u]]+w[u];
    for (int i=0;i<v[u];i++) f[u][i]=0;
}
int main()
{
    cin>>n>>m;
    memset(h,-1,sizeof h);
    int root;
    for (int i=1;i<=n;i++)
    {
        int p;
        cin>>v[i]>>w[i]>>p;
        if (p==-1) root=i;
        else add(p,i);
        
    }
    dfs(root);
    cout<<f[root][m]<<endl;
    return 0;
}
```