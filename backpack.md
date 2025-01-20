---
layout: default
title: 背包九讲
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
f[0][0]=0;初始化
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

## 2.完全背包问题
每件物品可以选无限次

## 3.多重背包问题
每件物品选的次数是不同且有限次

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