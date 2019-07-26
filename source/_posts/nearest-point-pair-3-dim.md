---
title: 至多三维最近点问题的分治解法
tags:
  - divide&conquer
  - original
  - algorithm
url: 162.html
id: 162
categories:
  - algorithms
date: 2019-05-07 18:57:47
---

### 问题描述

给出`num`个`n`维点`(A11,A12,...,A1n)`，`(A21,A22,...,A2n)`，……，`(Am1,Am2,...,Amn)`，请找出距离最近的两个点的坐标并按字典序输出。

### 输入

第`1`行有两个数`num`和`n`，分别代表点的个数和维数。

之后第`2`行至第`num+1`行中，每一行都有n个数，代表`An`在n维坐标系中的坐标。其中`num`，`n`，`An`满足

    1 <= n <= 3, 1 <= num <= 10e5, -10e9 <= An <= 10e9

### 输出

输出最近点对的坐标，每行输出一个点的坐标。保证字典序较前的点先输出。

### 样例

样例输入1

    6 2
    342865007 -691265808
    168900467 -744748163
    -440434063 -184293515
    -691848451 -399926019
    609366198 724389818
    537557125 559390437

样例输出1

    537557125 559390437
    609366198 724389818

样例输入2

    6 3
    -136905867 883290928 667747376
    -160060934 957773808 -251430337
    -144533471 -470375553 701658111
    -929571734 4727839 -831028267
    -837100600 368786495 -752700652
    614613476 -813960031 -838790640

样例输出2

    -929571734 4727839 -831028267
    -837100600 368786495 -752700652

### 限制

时间限制1s，内存限制256MB。

### AC代码

~~~c++
#include <iostream>
#include <cmath>
#include <algorithm>
#include <vector>

using namespace std;

struct node {
    int x, y = 0, z = 0;

```
node(int x, int y, int z) {
    this->x = x;
    this->y = y;
    this->z = z;
}

node() {
    x = 0;
}

} p\[100005\];

struct mergeRes {
    double result;
    int a, b;

mergeRes() {
    a = b = result = 0;
};

};

int c\[100005\], d\[100005\];

int n = 1, num;

bool x_cmp(node a, node b) {
    return a.x < b.x;
}

double min(double a, double b) {
    return a < b ? a : b;
}

mergeRes min(mergeRes a, mergeRes b) {
    return a.result < b.result ? a : b;
}

bool x_cmp1(int a, int b) {
    return p\[a\].x < p\[b\].x;
}

bool y_cmp(int a, int b) {
    return p\[a\].y < p\[b\].y;
}

bool yz_cmp(int a, int b) {
    if (p\[a\].y == p\[b\].y) return p\[a\].z < p\[b\].z;
    else return p\[a\].y < p\[b\].y;
}

double dis(int a, int b) {
    return pow(pow(p\[b\].x - p\[a\].x, 2) + pow(p\[b\].y - p\[a\].y, 2) + pow(p\[b\].z - p\[a\].z, 2), 0.5);
}

int dis1(int a, int b) {
    return abs(p\[a\].x - p\[b\].x);
}

bool isZero = false;

mergeRes merge(int lo, int hi) {
    mergeRes res;
    int i, j, k, l, cnt = 0;
    double tempdis;
    if (hi - lo == 2) {
        res.result = n == 1 ? dis1(lo, lo + 1) : dis(lo, lo + 1);
        res.a = lo;
        res.b = lo + 1;
        return res;
    }
    if (hi - lo == 3) {
        double dis01 = dis(lo, lo + 1);
        double dis02 = dis(lo + 1, lo + 2);
        double dis03 = dis(lo, lo + 2);
        if (dis01 < dis02 && dis01 < dis03) {
            res.a = lo;
            res.b = lo + 1;
            res.result = dis01;
        } else if (dis02 < dis01 && dis02 < dis03) {
            res.a = lo + 1;
            res.b = lo + 2;
            res.result = dis02;
        } else {
            res.a = lo;
            res.b = lo + 2;
            res.result = dis03;
        }
        return res;
    }
    int mid = lo + (hi - lo) / 2;
    double ans;
    mergeRes ans1=merge(lo, mid),ans2=merge(mid, hi);
    if(ans1.result>ans2.result) {
        res.result=ans2.result;
        res.a=ans2.a;
        res.b=ans2.b;
    } else {
        res.result=ans1.result;
        res.a=ans1.a;
        res.b=ans1.b;
    }
    ans=res.result;
    for (i = lo; i < hi; i++)
        if (p\[i\].x > p\[mid\].x - ans && p\[i\].x < p\[mid\].x + ans) c\[cnt++\] = i;
    if (n == 1) sort(c, c + cnt, x_cmp1);
    else if (n == 2) sort(c, c + cnt, y_cmp);
    else if (n == 3) sort(c, c + cnt, yz_cmp);
    for (i = 0; i < cnt; i++) {
        if (n > 1) {
            for (j = i + 1; j < cnt; j++) {
                if (p\[c\[j\]\].y - p\[c\[i\]\].y >= ans) break;
                if (n > 2) {
                    for (k = j; k < cnt; k++) {
                        if (p\[c\[j\]\].y != p\[c\[k\]\].y) {
                                //|| p\[c\[k\]\].z - p\[c\[i\]\].z >= ans) {
                            j = k - 1;
                            break;
                        }
                        //if (p\[c\[i\]\].z - p\[c\[k\]\].z >= ans) continue;
                        tempdis = dis(c\[i\], c\[k\]);
                        if (ans > tempdis) {
                            ans = tempdis;
                            res.a = c\[i\];
                            res.b = c\[k\];
                        }
                    }
                } else {
                    tempdis = dis(c\[i\], c\[j\]);
                    if (ans > tempdis) {
                        ans = tempdis;
                        res.a = c\[i\];
                        res.b = c\[j\];
                    }
                }
            }
        } else {
            if (i != 0) {
                tempdis = dis(c\[i\], c\[i - 1\]);
                if (ans > tempdis) {
                    ans = tempdis;
                    res.a = c\[i\];
                    res.b = c\[i - 1\];
                }
            }
        }
    }
    res.result = ans;
    return res;
}

void print(node x) {
    printf("%d", x.x);
    if (n > 1) {
        printf(" %d", x.y);
        if (n > 2) {
            printf(" %d\\n", x.z);
        } else printf("\\n");
    } else printf("\\n");
}

int main() {
    scanf("%d%d", &num, &n);
    node nnode;
    for (int i = 0; i < num; i++) {
        scanf("%d", &nnode.x);
        if (n > 1) {
            scanf("%d", &nnode.y);
            if (n > 2) {
                scanf("%d", &nnode.z);
            }
        }
        p\[i\] = nnode;
    }
    sort(p, p + num, x_cmp);
    mergeRes mr = merge(0, num);
    node na = p\[mr.a\];
    node nb = p\[mr.b\];
    if(na.x>nb.x||(na.x==nb.x&&na.y>nb.y)||(na.x==nb.x&&na.y==nb.y&&na.z>nb.z)) {
        print(nb);
        print(na);
    }else {
        print(na);
        print(nb);
    }
}
~~~

