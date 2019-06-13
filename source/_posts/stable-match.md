---
title: Stable Match 稳定婚姻问题
date: 2019-06-10 14:50:00
mathjax: true
categories:
- ffuck
tags:
- algorithms
---

## 问题描述

稳定婚姻问题是说，在n男n女之间，根据每个人对异性对象的喜好程度安排男女结婚，使得对于任意一位男士$M_1$（其配偶为$W_1$）和一位女士$W_2$（其配偶为$M_2$），**以下两种情况不会同时发生：**

- **$M_1$比起$W_1$更喜欢$W_2$**
- **$W_2$比起$M_2$更喜欢$M_1$**

也就是说，任意两对情侣间，一对中的男士和另一对中的女士不得互相更喜欢对方（否则他们就会私奔）。我们称这种情形为【**不稳定情形**】。

1962年，美国数学家 David Gale 和 Lloyd Shapley 发明了一种寻找稳定婚姻的策略。不管男女各有多少人，也不管他们的偏好如何，应用这种策略后总能得到一个稳定的搭配。换句话说，他们证明了稳定的婚姻搭配总是存在的。

按照 Gale-Shapley 算法，第一步是在猪蹄子堆里拎出来一个单身男人$m$（比如我）。之后，让这个男士按照他的喜好列表一个一个地跟女士求婚。对于每一个女士$w$，均存在三种情况：

1. 她没有npy，即没人跟她求过婚；
2. 她的npy是$m'$，但她喜欢$m$胜过$m'$；
3. 她的npy是$m'$，且她更喜欢$m'$。

对于情况1，很明显我们应该让$(m,w)$成为一对新人。
对于情况2，我们要先无情地拆散$(m',w)$，然后才能促成$(m,w)$。这时，$m'$失去了配偶，失落地回到猪蹄子堆里找个地方坐下了。
对于情况3，$m$只能愤恨地吐口唾沫，然后接着跟列表的其他女士求婚。

对于男士来说，每次求婚的对象只能越来越糟，而女士的配偶却会越来越好。对于上述【**不稳定情形**】中提到的两对情侣，$M_1$一定曾经向$W_2$求过婚，而$W_2$拒绝了他。这说明$W_2$已经与比$M_1$更好的大猪蹄子成对了！这意味着【**不稳定情形**】不可能存在。

可以证明，这样的过程是有穷尽的。由于告白次数随着算法运行是严格单增的，而这一指标的上界为（男士数$\times$女士数）。稳定婚姻问题需要满足男士数至少多于女士数（在一般的问题中，这两个数量是相等的），否则无法形成一一映射的关系。

以上就是稳定婚姻问题的 Gale-Shapley 算法。

## G-S 算法的伪代码实现[^1]

````
function stableMatching {
    Initialize all m ∈ M and w ∈ W to free
    while ∃ free man m who still has a woman w to propose to {
        w = first woman on m’s list to whom m has not yet proposed
        if w is free
            (m, w) become engaged
        else some pair (m', w) already exists
            if w prefers m to m'
                m' becomes free
            (m, w) become engaged 
            else
                (m', w) remain engaged
    }
}
````

## 一道例题——寻找学助[^2]

现在有$N$个学助和$N$个学生。每个学助都要一对一辅导一个学生。但是，每个学助都有一个最想帮助的学生排名表，每个学生也有一个最想找的学助排名表。

现在，qhurc 想通过 Stable Matching 解决这个问题，他跑来向你寻求帮助。写一个程序来输出对应关系。

### 输入

第一行包含一个整数$N$，这是学生（和学助）的个数。
第二行包含$N$个字符串，以空格分隔。第$i$个字符串代表第$i$个学助的名字。
第三行包含$N$个字符串，以空格分隔。第$i$个字符串代表第$i$个学生的名字。
之后$N$行中，每一行包含$N$个字符串 $S_1,\ldots,S_N$ 。第$i$行表示第$i$个**助教**的**学生**喜好列表。这个助教相对于 $S_{i+1},\ldots,S_N$ 来说更喜欢**学生**$S_i$。
之后$N$行中，每一行包含$N$个字符串 $S_1,\ldots,S_N$ 。第$i$行表示第$i$个**学生**的**助教**喜好列表。这个学生相对于 $S_{i+1},\ldots,S_N$ 来说更喜欢**助教**$S_i$。
每个字符串的长度均小于$10$。

### 输出

输出$N$个字符串，以空格分隔。第$i$个字符串代表分配给第$i$个学助的**学生的名字**。

### 样例输入1

````
3
adam bale campbell
alice bob calvin
bob calvin alice
calvin bob alice
bob alice calvin
adam bale campbell
adam campbell bale
bale adam campbell
````

### 样例输出1

````
bob calvin alice
````

### 样例输入2

````
5
adam bale campbell daisy eddy
alice bob calvin david emily
david calvin alice emily bob
bob alice calvin david emily
alice emily david calvin bob
alice calvin david bob emily
alice bob calvin david emily
campbell bale adam daisy eddy
eddy daisy bale adam campbell
daisy bale adam campbell eddy
campbell daisy adam eddy bale
bale adam eddy campbell daisy
````

### 样例输出2

````
david emily alice calvin bob
````

### 限制

时间限制：$1s/$测试样例
内存限制：$256\text{MB}$
100% 的测试样例中，$N\leq1000$。

### 示例代码

````c++
#include <iostream>
#include <string.h>
#include <queue>
#include <map>
#include <algorithm>

using namespace std;

int main() {
    int n = 1;
    scanf("%d", &n);
    map<string, int> saTag;
    for (int i = 0; i < n; i++) {
        string tmp;
        cin >> tmp;
        saTag.insert(pair<string, int>(tmp, i));
    }
    map<string, int> stuTag;
    vector<string> stuName;
    for (int i = 0; i < n; i++) {
        string tmp;
        cin >> tmp;
        stuTag.insert(pair<string, int>(tmp, i));
        stuName.push_back(tmp);
    }
    map<string, int>::iterator loc;
    int saFav[n][n];
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            string tmp;
            cin >> tmp;
            saFav[i][j] = stuTag[tmp];
        }
    }
    int stuFav[n][n];
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            string tmp;
            cin >> tmp;
            stuFav[i][j] = saTag[tmp];
        }
    }
    int inverse[n][n];
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            inverse[i][stuFav[i][j]] = j;
        }
    }
    int sa[n], stu[n];
    memset(sa, -1, sizeof(sa));
    memset(stu, -1, sizeof(stu));
    queue<int> free;
    for (int i = 0; i < n; i++) free.push(i);
    int count[n];
    memset(count, 0, sizeof(count));
    while (!free.empty()) {
        int tmp = free.front();
        free.pop();
        for (; count[tmp] <= n; count[tmp]++) {
            int targetStu = saFav[tmp][count[tmp]];
            if (sa[targetStu] < 0) {
                stu[tmp] = targetStu;
                sa[targetStu] = tmp;
                break;
            } else if (inverse[targetStu][sa[targetStu]]
                       > inverse[targetStu][tmp]) {
                free.push(sa[targetStu]);
                stu[tmp] = targetStu;
                sa[targetStu] = tmp;
                break;
            }
        }
    }
    for (int i = 0; i < n; i++) {
        printf("%s%s", stuName[stu[i]].c_str(), i == n - 1 ? "" : " ");
    }
}
````

[^1]: 来自[维基百科](https://en.wikipedia.org/wiki/Stable_marriage_problem)。
[^2]: 题目来自 SUSTech ADOJ。
