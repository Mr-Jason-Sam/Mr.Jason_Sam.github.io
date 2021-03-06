# 算法-时间复杂度和空间复杂度


时间和空间复杂度的分析是编制程序的一个基本能力，不过平时基本都是脑子里简单的估计，本篇文章打算回忆一下大 O 表示法和具体的时间复杂度的推导过程。

<!--more-->

## 1. 时间复杂度

算法的时间复杂度指的是算法运行所需要的时间的量，这个时间量不应该依赖外部因素，而应当只和问题规模 N、输入 I 以及算法 A 本身相关。如果用 T 表示时间复杂度，那么表达式为

$$
T = T(N,I,A)
$$
由于算法是通过一个或几个函数实现的，算法 A 本身通常就会隐含在函数名中，这样表达式可以简化为
$$
T = T(N,I)
$$
如果算法连输入都不依赖，那么表达式就可以进一步简化为 $T(N)$，但我们通常用 N 的小写形式，写作 $T(n)$

那么我们如何来计算这个时间的量呢，通常，我们假设算法中每条语句的执行时间均为单位时间，这样，算法运行的总时间就是所有语句执行时间之和。以顺序查找为例

```c
// 返回第一个与 k 相同的元素的下标，否则返回-1
Search(A[0...n], k)
    i = 0                         // c1=1
    while i < n and A[i] != k do  // c2
        i = i + 1                 // c3
    if i < n 
        return i             	  // c4=1
    else 
        return -1                 // 或c5=1
```

注释中列出了每条语句的执行时间 $c_i$，那么 $\sum_{i=1}^{n}c_i$ 就是算法运行的总时间。

但是，算法运行的总时间并不是算法的时间复杂度，我们通常说的时间复杂度完整的叫法应该是算法的渐进时间复杂度，是考虑当问题规模充分大时，算法运行时间的表达式在渐进意义下的阶。定义如下

>设 $T(n)$ 是关于算法 A 的复杂性函数，如果存在 t(n)，使得 $\lim\limits_{N\to\infty} \frac{T(n)-t(n)}{T(n)} = 0 $，则 t(n) 是 T(n) 是当 $n\to\infty$ 时的渐进表达式，也可以直接称作其渐进时间复杂度

具体而言，对于给定的 $T(n)$ 的表达式，算法的渐进时间复杂度是略去低阶项和常数因子留下的主项。例如，给定 $T(n) = 3n^3 + 90n^2 - 5n + 6040$，略去低阶项和常数因子，得到渐进时间复杂度 $t(n) = n^3$

### 1.1 大O表示法

通常用渐进上界 O 来表示渐进时间复杂度，也就是我们常说的大 O 表示法，其定义是
$$
O(g(n)) = \{f(n) | 存在正常数 c 和 n_0使得对所有 n \ge n_0 有：0 \le f(n) \le cg(n)\}
$$
![](https://picped-1301226557.cos.ap-beijing.myqcloud.com/Snipaste_2020-09-23_15-57-57.png)

举个例子，$f(n) = 2n+3 = O(n)$，因为当 $n\ge 3$ 时，$2n+3 \le 3n$，这里的含义就是，当 $n \ge 3$ 时，算法步骤不会超过 $3n$

第二个例子，$f(n) = 10n^2 + 4n + 2 = O(n^2)$，因为当 $n \ge 5$ 时，$10n^2 + 4n + 2 \le 11n^2$

最后给一个矩阵乘法的实例，总时间 $T(n) = 2n^3 + n^2 + 2n$，这样渐进时间复杂度就是 $O(n^3)$

```go
for i := 0; i < n; i++ {                // n
    for j := 0; j < n; j++ {            // n
        c[i][j] = 0                     // n^2
        for k := 0; k < n; k++ {        // n^3
            c[i][j] += a[i][k]*b[k][j]  // n^3
        }
    }
}
```

最后声明两个大 O 的运算规则

1. $O(f) + O(g) = O(max(f,g))$
2. $O(f)O(g) = O(fg)$

此外，有时候也会见到渐进下界 $\Omega$ 和 渐进准确界 $\Theta$，但很少或几乎用不到

### 1.2 对输入实例的依赖

有些时候需要考虑某类有代表性的输入实例，比如最坏情况、最好情况、平均情况，这几种情况的复杂度是不一样的，通常用如下符号表示

- 最好情况 $T_{min}(n)$
- 最坏情况 $T_{max}(n)$
- 平均情况 $T_{avg}(n)$

对于前面提到的查找算法，第一个与 k 相同的元素可能是最后一个元素，这时循环执行 n 次，是最坏情况；也可能是第一个元素，这时循环只执行 1 次，是最好情况。

理解这三种情况下的算法性能都很重要，但应注意：

1. 很多算法最好情况下的性能都差不多，O(1) 或者 O(n)，没有太大的影响，不能说明哪种算法更好；
2. 算法的平均情况往往不是那么容易分析；
3. 最坏情况可以告诉我们算法性能的上限，从而保证任何情况都不会比它差。

### 1.3 复杂度分析的步骤

复杂度分析的步骤如下

1. 确定表示问题规模的变量；
2. 确定占运行时间最大比例的语句；
3. 确定算法是否依赖输入实例（决定了是否分最坏、最好、平均三种情况考虑）；
4. 计算语句执行总次数；
5. 确定时间复杂度；

以选择排序为例，用伪代码描述如下

```c
SelectionSort(A[0...n-1]) 
    for i = 0 to n-2 do 
	    min = i
	    for j = i + 1 to n-1 do 
	        if A[j] < A[min] {
		        min = j	
    swap A[i] and A[min]
```

1. 问题规模为数组长度 n
2. 占运行时间最大比例的语句是 $min = j$
3. 算法只依赖问题规模，不依赖输入实例
4. 语句执行总次数 $T(n) = \sum_{i=1}^{n}i = \frac{n(n-1)}{2}$
5.  时间复杂度为 $O(n^2)$

一个依赖输入实例的例子是插入排序

```c
InsertSort(A[0...n-1])
    for i = 1; i < n; i++
        a = A[i]
        j = i - 1
        while j >= 0 && A[j] > a
            A[j+1] = A[j]
            j--
    A[j+1] = a
```

1. 问题规模为数组长度 n
2. 占运行时间最大比例的语句是 $A[j] > a$
3. 算法依赖输入实例
4. 语句执行总次数：
   - 最坏情况：$T(n) = \frac{n(n-1)}{2}$
   - 最好情况：$T(n) = n-1$
5. 时间复杂度
   - 最坏情况：$T(n)=O(n^2)$
   - 最好情况：$T(n) = O(n)$

### 1.4 常见时间复杂度

常见时间复杂度总结如下表

| 复杂度 | 出现情况                   |
| ------ | -------------------------- |
| l      | 几乎不存在                 |
| logn   | 不能考虑全部输入           |
| n      | 遍历、扫描全部输入         |
| nlogn  | 许多分治算法               |
| $n^2$  | 两层循环                   |
| $n^3$  | 三层循环                   |
| $2^n$  | 一个集合的所有子集         |
| $n!$   | 一个集合中的元素的所有排列 |

我们可以按这些时间复杂度把算法分为两类

- 多项式时间算法
  - 用多项式对运行时间限界的算法
  - $O(1) < O(logn) < O(n) < O(nlogn) < O(n^2) < O(n^3)$
- 指数时间算法
  - 用指数函数对运行时间限界的算法
  - $O(2^n) < O(n!) < O(n^n)$

对指数时间的算法应尽可能优化。

## 2. 空间复杂度

空间复杂度是对算法所消耗的额外存储单元进行计数，这里注意是**额外**的存储单元，不包括

1. 为容纳输入数据而分配的存储空间，比如输入数组 A[0...n] 占用的空间
2. 实现该算法的程序代码和常数占用的空间

额外的意思是算法所需要的工作空间，比如交换两个变量值时分配了一个临时变量，这个变量占用的空间就属于空间复杂度分析的范围，再比如实现栈时为栈申请的空间也属于空间复杂度的分析范围，最后，递归函数使用的内部栈也属于空间复杂度的分析范围。

在很多问题中，时间和空间是一个对立面。为算法分配更多的空间，可以使算法运行的更快，反之，当空间是一个重要因素时，需要用算法的运行时间去换取空间。一个运行时间短、所需存储空间小、其它性能也好的算法是很难做到的。因此我们要根据具体情况进行算法设计。 

- 对于使用次数较少的程序，力求算法简单易懂；
- 对于反复多次使用的程序，尽可能选用快速的算法；
- 若待解决的问题数据量极大，机器的存储空间较小，则相应算法主要考虑如何节省空间。