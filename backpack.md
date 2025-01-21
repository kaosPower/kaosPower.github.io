---
layout: default
title: 背包九讲
favicon: images/ak.png
---
# 背包九讲

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

## 4.混合背包问题
物品一共有很多种,有各种信息

## 5.二维费用的背包问题
有两个维度的限制

## 6.分组背包问题
把各种物品分成若干组,组内每种物品只能选一件

## 7.背包问题求方案数

## 8.求背包问题方案

##  9.有依赖的背包问题
选某个物品必须先选它依赖的物品