# E4-EX 晾衣服
## 题目描述
小明在一个很长的晾衣架上挂了$N$件等待被晾干的衣服，相邻的两件衣服之间若距离过近会影响衣服的晾干时间。例如，我们可以用相邻两件衣服的距离的最小值来衡量全部衣服晾干的速度。

现在小明选择将其中的$M$件衣服撤走，以使得剩余的衣服可以更快晾干。

给定$N$件衣服在晾衣架上的位置，请设计算法使得撤走$M$件衣服后相邻衣服距离的最小值最大。

## 输入格式
第一行两个整数$N,M$表示共有$N$件衣服，其中可以撤走$M$件。

接下来的$N$行，每行一个整数$X_i$表示衣服的坐标。

## 输出格式
输出一个整数表示撤走$M$件衣服后最大的相邻衣服距离的最小值。

## 数据规模：
$ M < N \leq 200000$

$ X_i \leq 20000000$

# 解题报告
## 初始思路
本题**贪心**的目标较为明显，对于给定的衣服序列，每次对于相邻最近的两件衣服$X_{i}$与$X_{i + 1}$，若有$X_{i} - X_{i - 1} < X_{i + 2} - X_{i + 1}$，则撤去$X_{i}$，否则撤去$X_{i + 1}$。

如果每撤去一件衣服就进行一次循环以查找相邻最近的衣服，这样的时间复杂度是不太能被接受的。观察最优解，对于撤走$M$件衣服后最大的相邻衣服距离的最小值$d$，可以认为任意两件相邻衣服的间距都大于$d$，为了确定$d$的值，可以在$[0, X_max - X_min]$这个范围内进行枚举，对每一个$d$，依序地将衣服晾到衣架上，保证与前一件衣服的间距不小于$d$，若能够放上不少于$N - M$件衣服，则这样的$d$是满足最优解条件的。

进一步地，为了将衣服依序地晾到衣架上，对于$X_i$的排序的是必须的，且对于$d$的枚举可以优化为**二分查找**
```c++
while (left <= right)
{
    int mid = (left + right) / 2; // 二分查找确定最大的 d
    int lastClothes = X[0];       // 上一件晾到衣架上的衣服
    int number = 1;               // 已在衣架上的衣服数，第一件衣服已晾

    for (int i = 1; i < n; i++)
    {
        if (X[i] >= lastClothes + mid) // 间距不小于 d
        {
            number++;
            lastClothes = X[i];
        }
    }

    if (number >= n - m)
        left = mid + 1; // 满足最优解条件，可能会更大
    else
        right = mid - 1; // 不满足最优解条件，向小查找
}
```
## 提交与修改过程
首次提交即[Accepted](https://202.38.86.171/status/e3b60a70a8b74b92ca74c98aeb10f602)

## 算法分析
### 时间复杂度
算法首先需要对输入数据进行排序，消耗$O( n \lg{n} )$的时间，随后进行的是对$d$的二分查找，二分查找执行的次数是$O( \lg{x} )$的，查找过程内部`for`循环中，每件衣服最多晾到衣架上1次，因此这部分消耗$O( n \lg{x} )$的时间，从数据规模上来看，平均情况下，$x \gg n$，因此算法总的时间复杂度是$O(n \lg{x} )$的。

### 空间复杂度
除了存储衣服坐标外，算法消耗的空间是常数的。

## 总结
本题贪心的目的较为明确，使用到的优化技术主要是利用最优解条件，通过预排序和二分查找降低时间复杂度。