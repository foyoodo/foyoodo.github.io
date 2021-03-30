---
title: "图的数组（邻接矩阵）储存表示"
date: 2020-07-19T22:07:43+08:00
lastmod: 2020-07-20T16:00:43+08:00
draft: false
tags: ["图", "数据结构"]
---

本文完整实现了 **《数据结构（C语言版）》7.2.1 数组表示法** 部分的代码。

<!--more-->

> 我们可以使用两个数组来分别储存数据元素（顶点）信息和数据元素之间的关系（边或弧）的信息。

## 储存结构

```cpp
#include <climits>  // 引入 INT_MAX 表示最大值
#include <iostream>

#define MAX_VERTEX_NUM 20  // 最大顶点的个数

using namespace std;

typedef int VRType;
typedef int InfoType;
typedef int VertexType;
typedef enum {
  DG = 1,
  DN,
  UDG,
  UDN
} GraphKind;  // 1~4 分别表示有向图、有向网、无向图和无向网

typedef struct {
  VRType adj;      // 顶点关系类型，对于无权图，用 1 或 0
                   // 表示是否关联；对于带权图，代表权值
  InfoType *info;  // 该弧相关信息的指针
} ArcCell, AdjMatrix[MAX_VERTEX_NUM][MAX_VERTEX_NUM];

typedef struct {
  VertexType vexs[MAX_VERTEX_NUM];  // 顶点向量
  AdjMatrix arcs;                   // 邻接矩阵
  int vexnum, arcnum;               // 图当前的顶点和弧的数量
  GraphKind kind;                   // 图的种类
} MGraph;
```

## 构造函数

```cpp
int CreateGraph(MGraph &G) {
  scanf("%d", &G.kind);
  switch (G.kind) {
    case DG:
      return CreateDG(G);
    case DN:
      return CreateDN(G);
    case UDG:
      return CreateUDG(G);
    case UDN:
      return CreateUDN(G);
    default:
      return 0;
  }
}
```

## 构造无向网

```cpp
int CreateUDN(MGraph &G) {
  int IncInfo, i, j;
  scanf("%d %d %d", &G.vexnum, &G.arcnum, &IncInfo);
  for (i = 0; i < G.vexnum; ++i) {
    cin >> G.vexs[i];
  }
  for (i = 0; i < G.vexnum; ++i) {  // 初始化邻接矩阵
    for (j = 0; j < G.vexnum; ++j) {
      G.arcs[i][j] = {INT_MAX, NULL};
    }
  }
  VertexType v1, v2;
  VRType w;
  for (int k = 0; k < G.arcnum; ++k) {
    cin >> v1 >> v2 >> w;  // 顶点和权值
    i = LocateVex(G, v1);
    j = LocateVex(G, v2);
    G.arcs[i][j].adj = w;
    if (IncInfo) {  // IncInfo 为 0 时，弧不保存其他信息
      G.arcs[i][j].info = new InfoType;
      Input(*G.arcs[i][j].info);
    }
    G.arcs[j][i] = G.arcs[i][j];
  }
  return 1;
}
```

## 主函数

```cpp
int main() {
  MGraph *G = new MGraph;
  CreateGraph(*G);
  PrintMatrix(*G);
  return 0;
}
```

### 输入样例（无向网）

```
4
4 4 0
1 2 3 4
1 2 5
1 3 6
2 4 8
3 4 7
```

### 输出样例

```
$ 5 6 $
5 $ $ 8
6 $ $ 7
$ 8 7 $
```

## 完整代码

```cpp
#include <climits>  // 引入 INT_MAX 表示最大值
#include <iostream>

#define MAX_VERTEX_NUM 20  // 最大顶点的个数

using namespace std;

typedef int VRType;
typedef int InfoType;
typedef int VertexType;
typedef enum {
  DG = 1,
  DN,
  UDG,
  UDN
} GraphKind;  // 1~4 分别表示有向图、有向网、无向图和无向网

typedef struct {
  VRType adj;      // 顶点关系类型，对于无权图，用 1 或 0
                   // 表示是否关联；对于带权图，代表权值
  InfoType *info;  // 该弧相关信息的指针
} ArcCell, AdjMatrix[MAX_VERTEX_NUM][MAX_VERTEX_NUM];

typedef struct {
  VertexType vexs[MAX_VERTEX_NUM];  // 顶点向量
  AdjMatrix arcs;                   // 邻接矩阵
  int vexnum, arcnum;               // 图当前的顶点和弧的数量
  GraphKind kind;                   // 图的种类
} MGraph;

int CreateGraph(MGraph &G);
int CreateDG(MGraph &G);
int CreateDN(MGraph &G);
int CreateUDG(MGraph &G);
int CreateUDN(MGraph &G);
int LocateVex(MGraph &G, VertexType vex);
int Input(InfoType &info);
int PrintMatrix(MGraph &G);

int main() {
  MGraph *G = new MGraph;
  CreateGraph(*G);
  PrintMatrix(*G);
  return 0;
}

int CreateGraph(MGraph &G) {
  scanf("%d", &G.kind);
  switch (G.kind) {
    case DG:
      return CreateDG(G);
    case DN:
      return CreateDN(G);
    case UDG:
      return CreateUDG(G);
    case UDN:
      return CreateUDN(G);
    default:
      return 0;
  }
}

int CreateDG(MGraph &G) {
  int IncInfo, i, j;
  scanf("%d %d %d", &G.vexnum, &G.arcnum, &IncInfo);
  for (i = 0; i < G.vexnum; ++i) {
    cin >> G.vexs[i];
  }
  for (i = 0; i < G.vexnum; ++i) {
    for (j = 0; j < G.vexnum; ++j) {
      G.arcs[i][j] = {0, NULL};
    }
  }
  VertexType v1, v2;
  for (int k = 0; k < G.arcnum; ++k) {
    cin >> v1 >> v2;
    i = LocateVex(G, v1);
    j = LocateVex(G, v2);
    G.arcs[i][j].adj = 1;
    if (IncInfo) {
      G.arcs[i][j].info = new InfoType;
      Input(*G.arcs[i][j].info);
    }
  }
  return 1;
}

int CreateDN(MGraph &G) {
  int IncInfo, i, j;
  scanf("%d %d %d", &G.vexnum, &G.arcnum, &IncInfo);
  for (i = 0; i < G.vexnum; ++i) {
    cin >> G.vexs[i];
  }
  for (i = 0; i < G.vexnum; ++i) {
    for (j = 0; j < G.vexnum; ++j) {
      G.arcs[i][j] = {INT_MAX, NULL};
    }
  }
  VertexType v1, v2;
  VRType w;
  for (int k = 0; k < G.arcnum; ++k) {
    cin >> v1 >> v2 >> w;
    i = LocateVex(G, v1);
    j = LocateVex(G, v2);
    G.arcs[i][j].adj = w;
    if (IncInfo) {
      G.arcs[i][j].info = new InfoType;
      Input(*G.arcs[i][j].info);
    }
  }
  return 1;
}

int CreateUDG(MGraph &G) {
  int IncInfo, i, j;
  scanf("%d %d %d", &G.vexnum, &G.arcnum, &IncInfo);
  for (i = 0; i < G.vexnum; ++i) {
    cin >> G.vexs[i];
  }
  for (i = 0; i < G.vexnum; ++i) {
    for (j = 0; j < G.vexnum; ++j) {
      G.arcs[i][j] = {0, NULL};
    }
  }
  VertexType v1, v2;
  for (int k = 0; k < G.arcnum; ++k) {
    cin >> v1 >> v2;
    i = LocateVex(G, v1);
    j = LocateVex(G, v2);
    G.arcs[i][j].adj = 1;
    if (IncInfo) {
      G.arcs[i][j].info = new InfoType;
      Input(*G.arcs[i][j].info);
    }
    G.arcs[j][i] = G.arcs[i][j];
  }
  return 1;
}

int CreateUDN(MGraph &G) {
  int IncInfo, i, j;
  scanf("%d %d %d", &G.vexnum, &G.arcnum, &IncInfo);
  for (i = 0; i < G.vexnum; ++i) {
    cin >> G.vexs[i];
  }
  for (i = 0; i < G.vexnum; ++i) {  // 初始化邻接矩阵
    for (j = 0; j < G.vexnum; ++j) {
      G.arcs[i][j] = {INT_MAX, NULL};
    }
  }
  VertexType v1, v2;
  VRType w;
  for (int k = 0; k < G.arcnum; ++k) {
    cin >> v1 >> v2 >> w;  // 顶点和权值
    i = LocateVex(G, v1);
    j = LocateVex(G, v2);
    G.arcs[i][j].adj = w;
    if (IncInfo) {  // IncInfo 为 0 时，弧不保存其他信息
      G.arcs[i][j].info = new InfoType;
      Input(*G.arcs[i][j].info);
    }
    G.arcs[j][i] = G.arcs[i][j];
  }
  return 1;
}

int LocateVex(MGraph &G, VertexType vex) {
  for (int i = 0; i < G.vexnum; ++i) {
    if (G.vexs[i] == vex) return i;
  }
  return -1;
}

int Input(InfoType &info) {
  cin >> info;
  return 1;
}

int PrintMatrix(MGraph &G) {
  for (int i = 0; i < G.arcnum; ++i) {
    for (int j = 0; j < G.arcnum; ++j) {
      G.arcs[i][j].adj != INT_MAX ? cout << G.arcs[i][j].adj : cout << '$';
      cout << ' ';
    }
    cout << '\n';
  }
  return 1;
}
```
