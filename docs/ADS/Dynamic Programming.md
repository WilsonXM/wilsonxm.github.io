## [1.8.x] Lesson 8——Dynamic Programming
> 如果一个问题的最优解是通过**组合对子问题的最优解**以及**从这些组合中选择Min/Max**, 那么这种范式就是**动态规划**的范式；
>
> <font style="color:#DF2A3F;">本质是 </font>**<font style="color:#DF2A3F;">K规模 问题</font>**<font style="color:#DF2A3F;">完全依赖于调用 </font>**<font style="color:#DF2A3F;">(K-1)规模 问题</font>**<font style="color:#DF2A3F;">；</font>

### [1.8.1.x] DP——Theory

DP的两个元素
- **optimal sub-structure-最优子结构**
- **overlapping sub-problem-重叠子问题**

DP的关键在于确定"**状态**"和"**状态转移方程**", 其基本流程是：
1. 找到最优子结构，确定重叠子问题
2. 用$sub-solution$递归地设计解决方法
3. 将递归范式转化为$DP$范式
4. 如果需要的话，记录一些额外信息



Tip 1：先确定解决问题的第一步或者最后一步，再倒推前一步的思路；

Tip 2：DP的一般趋势是从小规模到大规模，因此可以定义一些从小变大的量；

### [1.8.2.x] DP——Instants
#### 【Fibonacci Numbers:  F(N) = F(N – 1) + F(N – 2)】
![alt](./assets/image.png)

递归复杂度：$O(2^N)$

原因：递归中有大量重复计算；

1. DP法一：用<u>表记录</u>下已经计算过的斐波那契数，后面需要重复用时直接查表；
2. DP法二：如左图，不用递归，类似<u>左脚踩右脚</u>的方法，记录最近的两个数，自底向上；

#### 【Ordering Matrix Multiplying】
![](https://cdn.nlark.com/yuque/0/2023/png/34330502/1683285558181-421a1823-5944-4a97-85d9-7b5fea21d0fb.png)

不同计算顺序下，矩阵乘法的计算量有显著差异；

如何找出最优的矩阵乘法顺序？

##### （1）N个矩阵相乘有多少种order
![](https://cdn.nlark.com/yuque/0/2023/png/34330502/1683286117130-496f0464-facb-474a-bb37-aa33f2e8962e.png)

##### （2）N个矩阵相乘化为两个两个最优子矩阵相乘

🌹_**<u><font style="color:#DF2A3F;">最优子结构：全局最优解依赖于各自独立的局部最优解</font></u>**_

![](https://cdn.nlark.com/yuque/0/2023/png/34330502/1683286568007-77a0728e-e498-45f4-9296-f59c9262bbc1.png)

$m_{ij}$表示原问题的一个泛化，称为”状态“

具体代码实现，时间复杂度为![image](https://cdn.nlark.com/yuque/__latex/3ac63bab7086fb68e706e936a7ecabf4.svg)

```c
/* r contains number of columns for each of the N matrices */ 
/* r[ 0 ] is the number of rows in matrix 1 */ 
/* Minimum number of multiplications is left in M[ 1 ][ N ] */ 
void OptMatrix( const long r[ ], int N, TwoDimArray M ) 
{   int  i, j, k, L; 
    long  ThisM; 
    for( i = 1; i <= N; i++ )   M[ i ][ i ] = 0; 
    for( k = 1; k < N; k++ ) /* k = j - i */ 
        for( i = 1; i <= N - k; i++ ) { /* For each position */ 
    j = i + k;    M[ i ][ j ] = Infinity; 
    for( L = i; L < j; L++ ) { 
        ThisM = M[ i ][ L ] + M[ L + 1 ][ j ] 
            + r[ i - 1 ] * r[ L ] * r[ j ]; 
        if ( ThisM < M[ i ][ j ] )  /* Update min */ 
        M[ i ][ j ] = ThisM; 
    }  /* end for-L */
        }  /* end for-Left */
}
```

#### 【Optimal Binary Search Tree】
> 最优二叉搜索树，最优的静态搜索（没有插入和删除）
>

![](https://cdn.nlark.com/yuque/0/2023/png/34330502/1683289681731-71a46324-10b6-407e-8e0b-aaa9ed79bb3c.png)

![](https://cdn.nlark.com/yuque/0/2023/png/34330502/1683290384957-eda3118a-2f4b-4509-b133-9cc76dbb1758.png)

从左图可以看出：

1. DP过程应该使用二维数组：
    1. 存储当前规模下的最优解和最优解的根节点；
    2. 寻找最优解的过程需要遍历 i j 之间的所有点；
2. 构建OBST需要倒推所有根节点；

#### 【All-Pairs Shortest Path】
##### （1）Single-Source Algorithm
用Dijkstra算法等单源最短路径算法，对每个节点用一次，时间复杂度为![image](https://cdn.nlark.com/yuque/__latex/9a9b070e4eea2580e65ccf1849a41081.svg)，稀疏图更快；

##### （2）DP