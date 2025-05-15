# 一. 初识算法

## 1.3 二分查找 [^3]

二分查找算法也称折半查找，是一种非常高效的工作于有序数组的查找算法。后续的课程中还会学习更多的查找算法，但在此之前，不妨用它作为入门。

### 1) 基础版

需求：在**有序**数组 $A$ 内，查找值 $target$

* 如果找到返回索引
* 如果找不到返回 $-1$

算法描述

|      |                                                              |
| ---- | ------------------------------------------------------------ |
| 前提 | 给定一个内含 $n$ 个元素的有序数组 $A$，满足 $A_{0}\leq A_{1}\leq A_{2}\leq \cdots \leq A_{n-1}$，一个待查值 $target$ |
| 1    | 设置 $i=0$，$j=n-1$                                          |
| 2    | 如果 $i \gt j$，结束查找，没找到                             |
| 3    | 设置 $m = floor(\frac {i+j}{2})$ ，$m$ 为中间索引，$floor$ 是向下取整（$\leq \frac {i+j}{2}$ 的最小整数） |
| 4    | 如果 $target < A_{m}$ 设置 $j = m - 1$，跳到第2步            |
| 5    | 如果 $A_{m} < target$ 设置 $i = m + 1$，跳到第2步            |
| 6    | 如果 $A_{m} = target$，结束查找，找到了                      |

> ***P.S.***
>
> * 对于一个算法来讲，都有较为严谨的描述，上面是一个例子
> * 后续讲解时，以简明直白为目标，不会总以上面的方式来描述算法



java 实现


```java
public static int binarySearch(int[] a, int target) {
    int i = 0, j = a.length - 1;
    while (i <= j) {
        int m = (i + j) >>> 1;
        if (target < a[m]) {			// 在左边
            j = m - 1;
        } else if (a[m] < target) {		// 在右边
            i = m + 1;
        } else {
            return m;
        }
    }
    return -1;
}
```

* $i,j$ 对应着搜索区间 $[0,a.length-1]$（注意是闭合的区间），$i<=j$ 意味着搜索区间内还有未比较的元素，$i,j$ 指向的元素也可能是比较的目标
  * 思考：如果不加 $i==j$ 行不行？
  * 回答：不行，因为这意味着 $i,j$ 指向的元素会漏过比较
* $m$ 对应着中间位置，中间位置左边和右边的元素可能不相等（差一个），不会影响结果
* 如果某次未找到，那么缩小后的区间内不包含 $m$

### 2) 改变版

另一种写法

```java
public static int binarySearch(int[] a, int target) {
    int i = 0, j = a.length;
    while (i < j) {
        int m = (i + j) >>> 1;
        if (target < a[m]) {			// 在左边
            j = m;
        } else if (a[m] < target) {		// 在右边
            i = m + 1;
        } else {
            return m;
        }
    }
    return -1;
}
```

* $i,j$ 对应着搜索区间 $[0,a.length)$（注意是左闭右开的区间），$i<j$ 意味着搜索区间内还有未比较的元素，$j$ 指向的**一定不是**查找目标
  * 思考：为啥这次不加 $i==j$ 的条件了？
  * 回答：这回 $j$ 指向的不是查找目标，如果还加 $i==j$ 条件，就意味着 $j$ 指向的还会再次比较，找不到时，会死循环
* 如果某次要缩小右边界，那么 $j=m$，因为此时的 $m$ 已经**不是**查找目标了



## 1.4 时间复杂度

**时间复杂度**

下面的查找算法也能得出与之前二分查找一样的结果，那你能说出它差在哪里吗？

```java
public static int search(int[] a, int k) {
    for (
        int i = 0;
        i < a.length;
        i++
    ) {
        if (a[i] == k) {
            return i;
        }
    }
    return -1;
}
```

考虑最坏情况下（没找到）例如 `[1,2,3,4]` 查找 5

* `int i = 0` 只执行一次
* `i < a.length` 受数组元素个数 $n$ 的影响，比较 $n+1$ 次
* `i++` 受数组元素个数 $n$ 的影响，自增 $n$ 次
* `a[i] == k` 受元素个数 $n$ 的影响，比较 $n$ 次
* `return -1`，执行一次

粗略认为每行代码执行时间是 $t$，假设 $n=4$ 那么

* 总执行时间是 $(1+4+1+4+4+1)*t = 15t$
* 可以推导出更一般地公式为，$T = (3*n+3)t$ 

如果套用二分查找算法，还是 `[1,2,3,4]` 查找 5

```java
public static int binarySearch(int[] a, int target) {
    int i = 0, j = a.length - 1;
    while (i <= j) {
        int m = (i + j) >>> 1;
        if (target < a[m]) {			// 在左边
            j = m - 1;
        } else if (a[m] < target) {		// 在右边
            i = m + 1;
        } else {
            return m;
        }
    }
    return - 1;
}
```

* `int i = 0, j = a.length - 1` 各执行 1 次
* `i <= j` 比较 $floor(\log_{2}(n)+1)$ 再加 1 次, 比较不成立退出循环（总结规律得出）
* `(i + j) >>> 1` 计算 $floor(\log_{2}(n)+1)$ 次
* 接下来 `if() else if() else` 会执行 $3* floor(\log_{2}(n)+1)$ 次，分别为
* （以下都是考虑最坏的情况）
  * if 比较
  * else if 比较
  * else if 比较成立后的赋值语句

* `return -1`，执行一次

结果：

* 总执行时间为 $(2 + (1+3) + 3 + 3 * 3 +1)*t = 19t$
* 更一般地公式为 $(4 + 5 * floor(\log_{2}(n)+1))*t$

> **注意：**
>
> 左侧未找到和右侧未找到结果不一样，这里不做分析



两个算法比较，可以看到 $n$ 在较小的时候，二者花费的次数差不多

<img src=".\imgs\image-20221108095747933.png" alt="image-20221108095747933" style="zoom:50%;" />

但随着 $n$ 越来越大，比如说 $n=1000$ 时，用二分查找算法（红色）也就是 $54t$，而蓝色算法则需要 $3003t$

<img src=".\imgs\image-20221108100014451.png" alt="image-20221108100014451" style="zoom:50%;" />



> 画图采用的是 [Desmos | 图形计算器](https://www.desmos.com/calculator?lang=zh-CN)



计算机科学中，**时间复杂度**是用来衡量：一个算法的执行，随数据规模增大，而增长的时间成本

* 不依赖于环境因素



如何表示时间复杂度呢？

* 假设算法要处理的**数据规模是 $n$，代码总的执行行数用函数 $f(n)$** 来表示，例如：
  * 线性查找算法的函数 $f(n) = 3*n + 3$
  * 二分查找算法的函数 $f(n) = (floor(log_2(n)) + 1) * 5 + 4$

* 为了对 $f(n)$ 进行化简，应当抓住主要矛盾，找到一个变化趋势与之相近的表示法

### 1.4.1**大 $O$ 表示法[^4]**

![微信截图_20250301152801.png](https://cdn.jsdelivr.net/gh/hoo01/image_auto/%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_20250301152801.png)
其中
* $c, c_1, c_2$ 都为一个常数
* $f(n)$ 是实际执行代码行数与 n 的函数(y轴越高执行越多，算法效率越低)
* $g(n)$ 是经过化简，变化趋势与 $f(n)$ 一致的 n 的函数



####  **渐进上界**

渐进上界（asymptotic upper bound）：从某个常数 $n_0$开始，$c*g(n)$ 总是位于 $f(n)$ 上方，那么记作 $O(g(n))$

*代表算法执行的==最差情况==

例1
* $f(n) = 3*n+3$ 
* $g(n) = n$
* 取 $c=4$，在$n_0=3$ 之后，$g(n)$ 可以作为 $f(n)$ 的渐进上界，因此表示法写作 $O(n)$

例2
* $f(n) = 5*floor(log_2(n)) + 9$
* $g(n) = log_2(n)$
* $O(log_2(n))$

已知 $f(n)$ 来说，求 $g(n)$

* 表达式中相乘的常量，可以省略，如
  * $f(n) = 100*n^2$ 中的 $100$
* 多项式中数量规模更小（低次项）的表达式，如
  * $f(n)=n^2+n$ 中的 $n$
  * $f(n) = n^3 + n^2$ 中的 $n^2$
* 不同底数的对数，渐进上界可以用一个对数函数 $\log n$ 表示
  * 例如：$log_2(n)$ 可以替换为 $log_{10}(n)$，因为 $log_2(n) = \frac{log_{10}(n)}{log_{10}(2)}$，相乘的常量 $\frac{1}{log_{10}(2)}$ 可以省略
* 类似的，对数的常数次幂可省略
  * 如：$log(n^c) = c * log(n)$ 
#### **常见大 $O$ 表示法**
![微信截图_20250301155333.png](https://cdn.jsdelivr.net/gh/hoo01/image_auto/%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_20250301155333.png)

 按时间复杂度从低到高

* 黑色横线 $O(1)$，常量时间，意味着算法时间并不随数据规模而变化
* 绿色 $O(log(n))$，对数时间
* 蓝色 $O(n)$，线性时间，算法时间与数据规模成正比
* 橙色 $O(n*log(n))$，拟线性时间
* 红色 $O(n^2)$ 平方时间
* 黑色朝上 $O(2^n)$ 指数时间
* 没画出来的 $O(n!)$



#### **渐进下界**

渐进下界（asymptotic lower bound）：从某个常数 $n_0$开始，$c*g(n)$ 总是位于 $f(n)$ 下方，那么记作 $\Omega(g(n))$

*表示算法执行的最佳情况


#### **渐进紧界**

渐进紧界（asymptotic tight bounds）：从某个常数 $n_0$开始，$f(n)$ 总是在 $c_1*g(n)$ 和 $c_2*g(n)$ 之间，那么记作 $\Theta(g(n))$
*既能代表最佳情况，也能代表最差

#### **空间复杂度**

与时间复杂度类似，一般也使用大 $O$ 表示法来衡量：一个算法执行随数据规模增大，而增长的**额外**空间成本

```java
public static int binarySearchBasic(int[] a, int target) {
    int i = 0, j = a.length - 1;    // 设置指针和初值
    while (i <= j) {                // i~j 范围内有东西
        int m = (i + j) >>> 1;
        if(target < a[m]) {         // 目标在左边
            j = m - 1;
        } else if (a[m] < target) { // 目标在右边
            i = m + 1;
        } else {                    // 找到了
            return m;
        }
    }
    return -1;
}
```



**二分查找性能**

下面分析二分查找算法的性能

时间复杂度

* 最坏情况：$O(\log n)$
* 最好情况：如果待查找元素恰好在数组中央，只需要循环一次 $O(1)$ //常数都用1表示
* **在这里，并不能用渐进紧届替换渐进上界，因为在渐进紧届，最好情况和最坏情况必须有相同函数g(n)

空间复杂度

* 需要常数个指针 $i,j,m$，因此额外占用的空间是 $O(1)$ 

## 1.5 回到初版的二分查找

```python
public class BinarySearch {  
    public static int binarySearchBasic(int[] a, int target) {  
        int i = 0, j = a.length - 1;  
        //假设WHILE循环执行了L次，如果target在最左边，那么if成立，只需执行L次
        //如果元素在最右边，那么需要执行if 和 else if两项，2L次  
        while (i <= j) {  
            int m = (i + j) / 2; 
            if (a[m] < target) {  
                i = m + 1;  
            }  
            else if (target < a[m]) {  
                j = m - 1;  
            } else {  
                return m;  
            }  
        }  
        return -1;  
    }
```

### 1) 平衡版

```java
public static int binarySearchBalanced(int[] a, int target) {  
    int i = 0, j = a.length ; // i 可能指向目标，j在边界外  
    while (1 < j - i) {  
        int m = (i + j) >>> 1;  
        if (target < a[m]) {  
            j = m ;  
        } else {  
            i = m ;  
        }  
    }  
    if (a[i] == target) {  
        return i;  
    } else {  
        return -1;  
    }  
}
```
![微信截图_20250301165828.png](https://cdn.jsdelivr.net/gh/hoo01/image_auto/%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_20250301165828.png)

思考：

1. 左闭右开的区间，$i$ 指向的可能是目标，而 $j$ 指向的不是目标
2. 不奢望循环内通过 $m$ 找出目标, 缩小区间直至剩 1 个, 剩下的这个可能就是要找的（通过 $i$）
   * $j - i > 1$ 的含义是，在范围内待比较的元素个数 > 1
3. 改变 $i$ 边界时，它指向的可能是目标，因此不能 $m+1$
4. 循环内的平均比较次数减少了
5. 时间复杂度 $\Theta(log(n))$


### 2) Java 版

```java
private static int binarySearch0(long[] a, int fromIndex, int toIndex,
                                     long key) {
    int low = fromIndex;
    int high = toIndex - 1;

    while (low <= high) {//有=，因为low和high都在边界内
        int mid = (low + high) >>> 1;
        long midVal = a[mid];

        if (midVal < key)
            low = mid + 1;
        else if (midVal > key)
            high = mid - 1;
        else
            return mid; // key found
    }
    return -(low + 1);  // key not found.
}
```

* 例如 $[1,3,5,6]$ 要插入 $2$ 那么就是找到一个位置，这个位置左侧元素都比它小
  * 等循环结束，若没找到，low 左侧元素肯定都比 target 小，因此 low 即插入点
* 插入点取负是为了与找到情况区分
* -1 是为了把索引 0 位置的插入点与找到的情况进行区分
  *java 中 0 == -0, 比如以下图中，想插入5，如果直接返回-low则是 -0 ，但是实际数组中9才是索引0的数
![微信截图_20250301211255.png](https://cdn.jsdelivr.net/gh/hoo01/image_auto/%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_20250301211255.png)

#### 如何利用Arrays.binarySearch插入数？
![微信截图_20250301214822.png](https://cdn.jsdelivr.net/gh/hoo01/image_auto/%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_20250301214822.png)

```java
import java.util.Arrays;  
public class TestBinarySearch {  
  
    public static void main(String[] args) {  
        test8();  // ✅ 直接调用静态方法  
    }  
    public static void test8() {  
        int[] a = {2,5,8};  
        int target = 4;  
        
        int i = Arrays.binarySearch(a,target); //java自带的二分查找, i = -插入点 - 1       
        System.out.println(i);  
  
        if (i < 0) { // !如果i是负数，说明target不在原始数组a中  
            int insertIndex = Math.abs(i + 1); //i = -插入点 - 1  因此有 插入点 = abs(i+1)            
            int[] b = new int[a.length + 1]; //[0,0,0,0]  
            System.arraycopy(a,0,b,0,insertIndex);  
            b[insertIndex] = target;  
            System.arraycopy(a, insertIndex, b, insertIndex + 1, a.length - insertIndex);  
            System.out.println(Arrays.toString(b));  
        }  
    }  
}
```


### 3) Leftmost 与 Rightmost
#### Leftmost1

有时我们希望返回的是最左侧的重复元素，如果用 Basic 二分查找

* 对于数组 $[1, 2, 3, 4, 4, 5, 6, 7]$，查找元素4，结果是索引3

* 对于数组 $[1, 2, 4, 4, 4, 5, 6, 7]$，查找元素4，结果也是索引3，并不是最左侧的元素

```java
public static int binarySearchLeftmost1(int[] a, int target) {
    int i = 0, j = a.length - 1;
    int candidate = -1;
    while (i <= j) {
        int m = (i + j) >>> 1;
        if (target < a[m]) {
            j = m - 1;
        } else if (a[m] < target) {
            i = m + 1;
        } else {
            candidate = m; // 记录候选位置
            j = m - 1;     // 继续向左
        }
    }
    return candidate;
}
```

#### Rightmost1
如果希望返回的是最右侧元素

```java
public static int binarySearchRightmost1(int[] a, int target) {
    int i = 0, j = a.length - 1;
    int candidate = -1;
    while (i <= j) {
        int m = (i + j) >>> 1;
        if (target < a[m]) {
            j = m - 1;
        } else if (a[m] < target) {
            i = m + 1;
        } else {
            candidate = m; // 记录候选位置
            i = m + 1;	   // 继续向右
        }
    }
    return candidate;
}
```

**应用**
对于 Leftmost 与 Rightmost，可以返回一个比 -1 更有用的值
#### Leftmost2

```java
/**  
 * * @param a  
 * @param target  
 * @return 返回 >= target的最靠左索引位置  
 */
public static int binarySearchLeftmost(int[] a, int target) {
    int i = 0, j = a.length - 1;
    while (i <= j) {
        int m = (i + j) >>> 1;
        if (target <= a[m]) {
            j = m - 1;
        } else {
            i = m + 1;
        }
    }
    return i; 
}
```

* leftmost 返回值的另一层含义：$\lt target$ 的元素个数
* 小于等于中间值，都要向左找
#### Rightmost2

```java
/**  
 * * @param a  
 * @param target  
 * @return <= target的最靠右索引位置  
 */
public static int binarySearchRightmost(int[] a, int target) {
    int i = 0, j = a.length - 1;
    while (i <= j) {
        int m = (i + j) >>> 1;
        if (target < a[m]) {
            j = m - 1;
        } else {
            i = m + 1;
        }
    }
    return i - 1;//这里写j也可以
}
```

- 大于等于中间值，都要向右找
- 目标：找到最后一个 <= target 的位置。
- i 的更新逻辑：只有当 `target >= a[m]` 时，`i` 才会被推到 `m+1`。
- 这表示 `i` 始终在 **向右推进**，试图找到第一个 `> target` 的位置。
-  **循环终止时**，`i` 和 `j` 的关系是 `i = j + 1`（如示例中的 `i=3`，`j=2`）。
- **`i` 的最终位置**：在最后一次更新中，`i` 被推到了 `m+1`


- **`j` 的更新逻辑**：当 `target < a[m]` 时，`j` 回退到 `m-1`。
- 这表示 `j` 在 **向左收缩**，确保它始终指向 `<= target` 的最后一个位置。

#### =决定了代码的方向性
几个名词

![image-20221125174155058](./imgs/image-20221125174155058.png)

**范围查询**：

* 查询 $x \lt 4$，$0 .. leftmost(4) - 1$
* 查询 $x \leq 4$，$0 .. rightmost(4)$
* 查询 $4 \lt x$，$rightmost(4) + 1 .. \infty $
* 查询 $4 \leq x$， $leftmost(4) .. \infty$
* 查询 $4 \leq x \leq 7$，$leftmost(4) .. rightmost(7)$
* 查询 $4 \lt x \lt 7$，$rightmost(4)+1 .. leftmost(7)-1$

**求排名**：$leftmost(target) + 1$

* $target$ 可以不存在，如：$leftmost(5)+1 = 6$
* $target$ 也可以存在，如：$leftmost(4)+1 = 3$

**求前任（predecessor）**：$leftmost(target) - 1$

* $leftmost(3) - 1 = 1$，前任 $a_1 = 2$
* $leftmost(4) - 1 = 1$，前任 $a_1 = 2$

**求后任（successor）**：$rightmost(target)+1$

* $rightmost(5) + 1 = 5$，后任 $a_5 = 7$
* $rightmost(4) + 1 = 5$，后任 $a_5 = 7$

**求最近邻居**：

* 前任和后任距离更近者



## 习题

### 1) 时间复杂度估算

用函数 $f(n)$ 表示算法效率与数据规模的关系，假设每次解决问题需要 1 微秒（$10^{-6}$ 秒），进行估算：

1. 如果 $f(n) = n^2$ 那么 1 秒能解决多少次问题？1 天呢？
2. 如果 $f(n) = log_2(n)$  那么 1 秒能解决多少次问题？1 天呢？
3. 如果 $f(n) = n!$ 那么 1 秒能解决多少次问题？1 天呢？



**参考解答**

1. 1秒 $\sqrt{10^6} = 1000$ 次，1 天 $\sqrt{10^6 * 3600 * 24} \approx 293938$ 次
2. 1秒 $2^{1,000,000} $ 次，一天 $2^{86,400,000,000}$
3. 推算如下
   * $10! = 3,628,800$ 1秒能解决 $1,000,000$ 次，因此次数为 9 次
   * $14!=87,178,291,200$，一天能解决 $86,400,000,000$ 次，因此次数为 13 次



### 2) 耗时估算

一台机器对200个单词进行排序花了200秒(使用冒泡排序)，那么花费800秒，大概可以对多少个单词进行排序

a. 400

b. 600

c. 800

d. 1600

**答案**

* a

**解释**

* 冒泡排序时间复杂度是 $O(N^2)$
* 时间增长 4 倍，而因此能处理的数据量是原来的 $\sqrt{4} = 2$ 倍



### 3) E01. 二分查找-Leetcode 704

**要点**：减而治之，可以用递归或非递归实现

给定一个 n 个元素有序的（升序）整型数组 nums 和一个目标值 target  ，写一个函数搜索 nums 中的 target，如果目标值存在返回下标，否则返回 -1

例如

```java
输入: nums = [-1,0,3,5,9,12], target = 9
输出: 4
解释: 9 出现在 nums 中并且下标为 4
    
输入: nums = [-1,0,3,5,9,12], target = 2
输出: -1
解释: 2 不存在 nums 中因此返回 -1    
```

**参考答案**：略，可以用讲过的任意一种二分求解



### 4) E02. 搜索插入位置-Leetcode 35

**要点**：理解谁代表插入位置

给定一个排序数组和一个目标值

* 在数组中找到目标值，并返回其索引
* 如果目标值不存在于数组中，返回它将会被按顺序插入的位置

例如

```
输入: nums = [1,3,5,6], target = 5
输出: 2

输入: nums = [1,3,5,6], target = 2
输出: 1

输入: nums = [1,3,5,6], target = 7
输出: 4
```



#### 二分查找基础版
代码改写，基础版中，找到返回 m，没找到 i 代表插入点，因此有

```java
public int searchInsert(int[] a, int target) {
    int i = 0, j = a.length - 1;
    while (i <= j) {
        int m = (i + j) >>> 1;
        if (target < a[m]) {
            j = m - 1;
        } else if (a[m] < target) {
            i = m + 1;
        } else {
            return m;
        }
    }
    return i; // 二分查找最终会停在 `i > j`
}
```



#### 二分查找平衡版

* 如果 target == a[i] 返回 i 表示找到
* 如果 target < a[i]，例如 target = 2，a[i] = 3，这时就应该在 i 位置插入 2
* 如果 a[i] < target，例如 a[i] = 3，target = 4，这时就应该在 i+1 位置插入 4

```java
public static int searchInsert(int[] a, int target) {
    int i = 0, j = a.length;
    while (1 < j - i) {
        int m = (i + j) >>> 1;
        if (target < a[m]) {
            j = m; //
        } else {
            i = m;
        }
    }
    return (target <= a[i]) ? i : i + 1;
}
```



#### 用 leftmost 版本解
返回值即为插入位置（并能处理元素重复的情况）

```java
public int searchInsert(int[] a, int target) {
    int i = 0, j = a.length - 1;
    while(i <= j) {
        int m = (i + j) >>> 1;
        if(target <= a[m]) {
            j = m - 1;
        } else {
            i = m + 1;
        } 
    }
    return i;
}
```

#### 为什么不能用rightmost

| 方法                             | `target` 存在       | `target` 不存在              | 为什么？                                                                    |
| :----------------------------- | :---------------- | :------------------------ | :---------------------------------------------------------------------- |
| `leftmost` (`return i`) ✅      | 找到 `target` 的最左索引 | 找到 `target` **应该插入的位置** ✅ | `i` 终止时指向 **第一个 `>= target` 的位置**，**直接插入正确！**                           |
| `rightmost` (`return i - 1`) ✅ | 找到 `target` 的最右索引 | 可能返回比 `target` 小的索引 ❌     | `i - 1` 终止时指向 **最后一个 `<= target` 的位置**，如果 `target` 不存在，它就“顶替”前一个值，导致错误！ |


### 5) E03. 搜索开始结束位置-Leetcode 34

给你一个按照非递减顺序排列的整数数组 nums，和一个目标值 target。请你找出给定目标值在数组中的开始位置和结束位置。

如果数组中不存在目标值 target，返回 [-1, -1]。

你必须设计并实现时间复杂度为 O(log n) 的算法解决此问题



例如

```
输入：nums = [5,7,7,8,8,10], target = 8
输出：[3,4]

输入：nums = [5,7,7,8,8,10], target = 6
输出：[-1,-1]

输入：nums = [], target = 0
输出：[-1,-1]
```



**参考答案**

```java
public static int left(int[] a, int target) {
    int i = 0, j = a.length - 1;
    int candidate = -1;
    while (i <= j) {
        int m = (i + j) >>> 1;
        if (target < a[m]) {
            j = m - 1;
        } else if (a[m] < target) {
            i = m + 1;
        } else {
            candidate = m;
            j = m - 1;
        }
    }
    return candidate;
}

public static int right(int[] a, int target) {
    int i = 0, j = a.length - 1;
    int candidate = -1;
    while (i <= j) {
        int m = (i + j) >>> 1;
        if (target < a[m]) {
            j = m - 1;
        } else if (a[m] < target) {
            i = m + 1;
        } else {
            candidate = m;
            i = m + 1;
        }
    }
    return candidate;
}

public static int[] searchRange(int[] nums, int target) {
    int x = left(nums, target);
    if(x == -1) {
        return new int[] {-1, -1};
    } else {
        return new int[] {x, right(nums, target)};
    }
}
```



# 二. 基础数据结构

## 2.1 数组

### 1) 概述

**定义**

在计算机科学中，数组是由一组元素（值或变量）组成的数据结构，每个元素有至少一个索引或键来标识

>In computer science, an **array** is a data structure consisting of a collection of *elements* (values or variables), each identified by at least one *array index* or *key*



因为数组内的元素是**连续存储**的，所以数组中元素的地址，可以通过其索引计算出来，例如：

```java
int[] array = {1,2,3,4,5}
```



知道了数组的**数据**起始地址 $BaseAddress$，就可以由公式 $BaseAddress + i * size$ 计算出索引 $i$ 元素的地址

* $i$ 即索引，在 Java、C 等语言都是从 0 开始
* $size$ 是每个元素占用字节，例如 $int$ 占 $4$，$double$ 占 $8$



**小测试**

```java
byte[] array = {1,2,3,4,5}
```

已知 array 的**数据**的起始地址是 0x7138f94c8，那么元素 3 的地址是什么？

> 答：0x7138f94c8 + 2 * 1 = 0x7138f94ca，byte数组每个数字只占一个字节



**空间占用**

Java 中数组结构为

* 8 字节 markword
* 4 字节 class 指针（压缩 class 指针的情况）
* 4 字节 数组大小（决定了数组最大容量是 $2^{32}$）
* 数组元素 + 对齐字节（java 中所有对象大小都是 8 字节的整数倍[^12]，不足的要用对齐字节补足）

例如

```java
int[] array = {1, 2, 3, 4, 5};
```

的大小为 40 个字节，组成如下

```
8 + 4 + 4 + 5*4 + 4(alignment)
```



**随机访问性能**

即根据索引查找元素，时间复杂度是 $O(1)$



### 2) 动态数组

**java 版本**

```java
public class DynamicArray implements Iterable<Integer> {
    private int size = 0; // 逻辑大小
    private int capacity = 8; // 容量
    private int[] array = {};


    /**
     * 向最后位置 [size] 添加元素
     *
     * @param element 待添加元素
     */
    public void addLast(int element) {
        add(size, element);
    }

    /**
     * 向 [0 .. size] 位置添加元素
     *
     * @param index   索引位置
     * @param element 待添加元素
     */
    public void add(int index, int element) {
        checkAndGrow();

        // 添加逻辑
        if (index >= 0 && index < size) {
            // 向后挪动, 空出待插入位置
            System.arraycopy(array, index,
                    array, index + 1, size - index);
        }
        array[index] = element;
        size++;
    }

    private void checkAndGrow() {
        // 容量检查
        if (size == 0) {
            array = new int[capacity];
        } else if (size == capacity) {
            // 进行扩容, 1.5 1.618 2
            capacity += capacity >> 1;
            int[] newArray = new int[capacity];
            System.arraycopy(array, 0,
                    newArray, 0, size);
            array = newArray;
        }
    }

    /**
     * 从 [0 .. size) 范围删除元素
     *
     * @param index 索引位置
     * @return 被删除元素
     */
    public int remove(int index) { // [0..size)
        int removed = array[index];
        if (index < size - 1) {
            // 向前挪动
            System.arraycopy(array, index + 1,
                    array, index, size - index - 1);
        }
        size--;
        return removed;
    }


    /**
     * 查询元素
     *
     * @param index 索引位置, 在 [0..size) 区间内
     * @return 该索引位置的元素
     */
    public int get(int index) {
        return array[index];
    }

    /**
     * 遍历方法1
     *
     * @param consumer 遍历要执行的操作, 入参: 每个元素
     */
    public void foreach(Consumer<Integer> consumer) {
        for (int i = 0; i < size; i++) {
            // 提供 array[i]
            // 返回 void
            consumer.accept(array[i]);
        }
    }

    /**
     * 遍历方法2 - 迭代器遍历
     */
    @Override
    public Iterator<Integer> iterator() {
        return new Iterator<Integer>() {
            int i = 0;

            @Override
            public boolean hasNext() { // 有没有下一个元素
                return i < size;
            }

            @Override
            public Integer next() { // 返回当前元素,并移动到下一个元素
                return array[i++];
            }
        };
    }

    /**
     * 遍历方法3 - stream 遍历
     *
     * @return stream 流
     */
    public IntStream stream() {
        return IntStream.of(Arrays.copyOfRange(array, 0, size));
    }
}
```

* 这些方法实现，都简化了 index 的有效性判断，假设输入的 index 都是合法的



**插入或删除性能**

头部位置，时间复杂度是 $O(n)$

中间位置，时间复杂度是 $O(n)$

尾部位置，时间复杂度是 $O(1)$（均摊来说）

插入或删除时的“
移动”含义
比如你要在下标 `1` 插入一个新值 `15`，你希望得到：
`[10, 15, 20, 30, 40, 50]`
你得先把 **从下标 1 开始的所有元素**，往后搬一格：
```java
array[5] = array[4];
array[4] = array[3];
array[3] = array[2];
array[2] = array[1];
```
**搬了 5 - 1 = 4 次操作**

这些操作每一步都花费 $O(1)$，但你需要做 $n$ 次（最坏情况是整个数组搬动），所以总的时间是 **$O(n)$**。
> **数组必须保证元素在内存中是连续的，插入/删除会打破这种连续性，因此需要大量复制。

>而每次复制操作是 $O(1)$，复制了 $k$ 次，就是 $O(k)$。最坏情况下 $k = n$，所以是 $O(n)$。


### 3) 二维数组

```java
int[][] array = {
    {11, 12, 13, 14, 15},
    {21, 22, 23, 24, 25},
    {31, 32, 33, 34, 35},
};
```

内存图如下

![微信截图_20250502202257.png](https://cdn.jsdelivr.net/gh/hoo01/image_auto/%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_20250502202257.png)
cp classpointer
* 二维数组占 32 个字节，其中 array[0]，array[1]，array[2] 三个元素分别保存了指向三个一维数组的引用

* 三个一维数组各占 40 个字节

* 它们在内层布局上是**连续**的

更一般的，对一个二维数组 $Array[m][n]$

* $m$ 是外层数组的长度，可以看作 row 行
* $n$ 是内层数组的长度，可以看作 column 列
* 当访问 $Array[i][j]$，$0\leq i \lt m, 0\leq j \lt n$时，就相当于
  * 先找到第 $i$ 个内层数组（行）
  * 再找到此内层数组中第 $j$ 个元素（列）



**小测试**

Java 环境下（不考虑类指针和引用压缩，此为默认情况），有下面的二维数组

```java
byte[][] array = {
    {11, 12, 13, 14, 15},
    {21, 22, 23, 24, 25},
    {31, 32, 33, 34, 35},
};
```

已知 array **对象**起始地址是 0x1000，那么 23 这个元素的地址是什么？

> 答：
>
> * 起始地址 0x1000
> * 外层数组大小：16字节对象头 + 3元素 * 每个引用4字节 + 4 对齐字节 = 32 = 0x20
> * 第一个内层数组大小：16字节对象头 + 5元素 * 每个byte1字节 + 3 对齐字节 = 24 = 0x18
> * 第二个内层数组，16字节对象头 = 0x10，待查找元素索引为 2
> * 最后结果 = 0x1000 + 0x20 + 0x18 + 0x10 + 2*1 = 0x104a



### 4) 局部性原理

这里只讨论空间局部性

* cpu 读取内存（速度慢）数据后，会将其放入高速缓存（速度快）当中，如果后来的计算再用到此数据，在缓存中能读到的话，就不必读内存了
* 缓存的最小存储单位是缓存行（cache line），一般是 64 bytes，一次读的数据少了不划算啊，因此最少读 64 bytes 填满一个缓存行，因此读入某个数据时也会读取其**临近的数据**，这就是所谓**空间局部性**



**对效率的影响**

比较下面 ij 和 ji 两个方法的执行效率

```java
int rows = 1000000;
int columns = 14;
int[][] a = new int[rows][columns];

StopWatch sw = new StopWatch();
sw.start("ij");
ij(a, rows, columns);
sw.stop();
sw.start("ji");
ji(a, rows, columns);
sw.stop();
System.out.println(sw.prettyPrint());
```

ij 方法

```java
public static void ij(int[][] a, int rows, int columns) {
    long sum = 0L;
    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < columns; j++) {
            sum += a[i][j];
        }
    }
    System.out.println(sum);
}
```

ji 方法

```java
public static void ji(int[][] a, int rows, int columns) {
    long sum = 0L;
    for (int j = 0; j < columns; j++) {
        for (int i = 0; i < rows; i++) {
            sum += a[i][j];
        }
    }
    System.out.println(sum);
}
```

执行结果

```
0
0
StopWatch '': running time = 96283300 ns
---------------------------------------------
ns         %     Task name
---------------------------------------------
016196200  017%  ij
080087100  083%  ji
```

可以看到 ij 的效率比 ji 快很多，为什么呢？

* 缓存是有限的，当新数据来了后，一些旧的缓存行数据就会被覆盖
* 如果不能充分利用缓存的数据，就会造成效率低下

以 ji 执行为例，第一次内循环要读入 $[0,0]$ 这条数据，由于局部性原理，读入 $[0,0]$ 的同时也读入了 $[0,1] ... [0,13]$，如图所示

![image-20221104164329026](.\imgs\image-20221104164329026.png)

但很遗憾，第二次内循环要的是 $[1,0]$ 这条数据，缓存中没有，于是再读入了下图的数据

![image-20221104164716282](.\imgs\image-20221104164716282.png)

这显然是一种浪费，因为 $[0,1] ... [0,13]$ 包括 $[1,1] ... [1,13]$ 这些数据虽然读入了缓存，却没有及时用上，而缓存的大小是有限的，等执行到第九次内循环时

![image-20221104164947154](.\imgs\image-20221104164947154.png)

缓存的第一行数据已经被新的数据 $[8,0] ... [8,13]$ 覆盖掉了，以后如果再想读，比如 $[0,1]$，又得到内存去读了

同理可以分析 ij 函数则能充分利用局部性原理加载到的缓存数据

**举一反三**

1. I/O 读写时同样可以体现局部性原理

2. 数组可以充分利用局部性原理，那么链表呢？

   > 答：链表不行，因为链表的元素并非相邻存储



### 5) 越界检查

java 中对数组元素的读写都有越界检查，类似于下面的代码

```c++
bool is_within_bounds(int index) const        
{ 
    return 0 <= index && index < length(); 
}
```

* 代码位置：`openjdk\src\hotspot\share\oops\arrayOop.hpp`

只不过此检查代码，不需要由程序员自己来调用，JVM 会帮我们调用

### 习题

#### E01. 合并有序数组 - 对应 Leetcode 88

将数组内两个区间内的有序元素合并

例

```
[1, 5, 6, 2, 4, 10, 11]
```

可以视作两个有序区间

```
[1, 5, 6] 和 [2, 4, 10, 11]
```

合并后，结果仍存储于原有空间

```
[1, 2, 4, 5, 6, 10, 11]
```



**方法1**

递归

* 每次递归把更小的元素复制到结果数组

```java
merge(left=[1,5,6],right=[2,4,10,11],a2=[]){
    merge(left=[5,6],right=[2,4,10,11],a2=[1]){
        merge(left=[5,6],right=[4,10,11],a2=[1,2]){
            merge(left=[5,6],right=[10,11],a2=[1,2,4]){
                merge(left=[6],right=[10,11],a2=[1,2,4,5]){
                    merge(left=[],right=[10,11],a2=[1,2,4,5,6]){
						// 拷贝10，11
                    }
                }
            }
        }
    }
}
```

代码

```java
public static void merge(int[] a1, int i, int iEnd, int j, int jEnd,
                              int[] a2, int k) {
    if (i > iEnd) {
        System.arraycopy(a1, j, a2, k, jEnd - j + 1);
        return;
    }
    if (j > jEnd) {
        System.arraycopy(a1, i, a2, k, iEnd - i + 1);
        return;
    }
    if (a1[i] < a1[j]) {
        a2[k] = a1[i];
        merge(a1, i + 1, iEnd, j, jEnd, a2, k + 1);
    } else {
        a2[k] = a1[j];
        merge(a1, i, iEnd, j + 1, jEnd, a2, k + 1);
    }
}
```

测试

```java
int[] a1 = {1, 5, 6, 2, 4, 10, 11};
int[] a2 = new int[a1.length];
merge(a1, 0, 2, 3, 6, a2, 0);
```



**方法2**

代码

```java
public static void merge(int[] a1, int i, int iEnd,
                             int j, int jEnd,
                             int[] a2) {
    int k = i;
    while (i <= iEnd && j <= jEnd) {
        if (a1[i] < a1[j]) {
            a2[k] = a1[i];
            i++;
        } else {
            a2[k] = a1[j];
            j++;
        }
        k++;
    }
    if (i > iEnd) {
        System.arraycopy(a1, j, a2, k, jEnd - j + 1);
    }
    if (j > jEnd) {
        System.arraycopy(a1, i, a2, k, iEnd - i + 1);
    }
}
```

测试

```java
int[] a1 = {1, 5, 6, 2, 4, 10, 11};
int[] a2 = new int[a3.length];
merge(a1, 0, 2, 3, 6, a2);
```

#### leetcode 27 Remove Element
如何删除数组中的一个元素
- 数组的元素在内存地址中是连续的，不能单独删除数组中的某个元素，只能覆盖，后面的元素前移

#### leetcode59 [Spiral Matrix II](https://leetcode.com/problems/spiral-matrix-ii/)
关键点：
1.每条边处理原则都是左闭右开 -- 循环不变量
2.总共需要转几圈？ -- while (loop <=n/2)
因为转一圈四条边，平均到长度和宽度，各消耗2
3.

需要记录的：
1.每一圈的起始点
2.每一圈减去的长度
3.当前圈数

#### leetcode54 [spiral matrix](https://github.com/youngyangyang04/leetcode-master/blob/master/problems/0054.%E8%9E%BA%E6%97%8B%E7%9F%A9%E9%98%B5.md)
## 2.2 链表

### 1) 概述

**定义**

在计算机科学中，链表是数据元素的线性集合，其每个元素都指向下一个元素，元素存储上并不连续

> In computer science, a **linked list** is a linear collection of data elements whose order is not given by their physical placement in memory. Instead, each element points to the next. 



可以分类为[^5]

* 单向链表，每个元素只知道其下一个元素是谁

* 双向链表，每个元素知道其上一个元素和下一个元素

* 循环链表，通常的链表尾节点 tail 指向的都是 null，而循环链表的 tail 指向的是头节点 head

![l1.PNG](https://cdn.jsdelivr.net/gh/hoo01/image_auto/l1.PNG)

链表内还有一种特殊的节点称为哨兵（Sentinel）节点，也叫做哑元（ Dummy）节点，它不存储数据，通常用作头尾，用来简化边界判断，如下图所示

![image-20221110084611550.png](https://cdn.jsdelivr.net/gh/hoo01/image_auto/image-20221110084611550.png)

**随机访问性能**

根据 index 查找，时间复杂度 $O(n)$

**插入或删除性能**

* 起始位置：$O(1)$
* 结束位置：如果已知 tail 尾节点（双向链表）是 $O(1)$，不知道 tail 尾节点（单项链表）是 $O(n)$
* 中间位置：根据 index 查找时间 + $O(1)$

![L2.PNG](https://cdn.jsdelivr.net/gh/hoo01/image_auto/L2.PNG)
链表的插入操作，只需要改变插入部分指针的位置，和插入顺序没有关系，因此时间复杂度是O(1)

而数组的插入删除
- 数组在内存中是**连续存储**的。
- 如果你在中间插入一个元素（比如索引 2 处），那么你需要将后面的所有元素往后挪一位，时间复杂度是 $O(n)$。
- 删除元素也一样，需要把后面的元素往前挪。

| 操作位置 | 数组复杂度         | 链表复杂度               |
| ---- | ------------- | ------------------- |
| 末尾插入 | $O(1)$（有预留空间） | $O(1)$（如果有 tail）    |
| 开头插入 | $O(n)$        | $O(1)$              |
| 中间插入 | $O(n)$（元素移动）  | $O(n) + O(1)$（先找再插） |
### 2) 单向链表

根据单向链表的定义，首先定义一个存储 value 和 next 指针的类 Node，和一个描述头部节点的引用
#### addFirst
1.链表为空
![l3.PNG](https://cdn.jsdelivr.net/gh/hoo01/image_auto/l3.PNG)

2.链表非空
![l4.PNG](https://cdn.jsdelivr.net/gh/hoo01/image_auto/l4.PNG)

```java
package com.itheima.datastructure;  
  
public class SinglyLinkedList {  
    private Node head = null;// head指针  
  
    private static class Node {  
        int value;  
        Node next; //下一个节点指针  
  
        //节点类  
        private Node(int value, Node next) {  
            this.value = value;  
            this.next = next;  
        }  
    }  
  
    public void addFirst(int value) {  
        // 1.链表为空  
//        head = new Node(value, null);  
        // 2.链表非空  
        head = new Node(value, head);  
    }  
}
```

* Node 定义为内部类，是为了对外**隐藏**实现细节，没必要让类的使用者关心 Node 结构
* 定义为 static 内部类，是因为 Node **不需要**与 SinglyLinkedList 实例相关，多个 SinglyLinkedList实例能共用 Node 类定义
* **head 指的是链表的“头指针”，它本身指向第一个节点 node1。**  
    所以：
    - `head` 是 `node1`
    - `head.next` 是 `node1.next`，也就是 `node2`

#### 遍历

```java
public class SinglyLinkedList implements Iterable<Integer>{  
    private Node head = null;// head指针  
//1.迭代器遍历
    @Override  
    public Iterator<Integer> iterator() {  
        return new Iterator<Integer>() {  
            Node p = head;  
  
            @Override  
            public boolean hasNext() {  
                return p != null;  
            }  
  
            @Override  
            public Integer next() {  
                int v = p.value;  
                p = p.next;  
                return v;  
            }  
        };  
    }  
  
    private static class Node {  
        int value;  
        Node next; //下一个节点指针  
  
        //节点类  
        private Node(int value, Node next) {  
            this.value = value;  
            this.next = next;  
        }  
    }  
  
    public void addFirst(int value) {  
        // 1.链表为空  
//        head = new Node(value, null);  
        // 2.链表非空  
        head = new Node(value, head);  
    }  
//2.while遍历
    public void loop1(Consumer<Integer> consumer) {  
        Node p = head;  
        while (p != null) {  
            consumer.accept(p.value);  
            p = p.next;  
        }  
    }  

//3.for遍历
    public void loop2(Consumer<Integer> consumer) {  
        for(Node p = head; p != null; p = p.next) {  
            consumer.accept(p.value);  
        }  
    }  
}
```

```java
public class TestSinglyLinkedList {  
    @Test  
    public void test1() {  
        SinglyLinkedList list = new SinglyLinkedList();  
        list.addFirst(1);  
        list.addFirst(2);  
        list.addFirst(3);  
        list.addFirst(4);  
  
        list.loop2(value -> System.out.println(value));
        /*list.loop1(new Consumer<Integer>() {// 相当于  
        @Override    
        public void accept(Integer value) {        
        System.out.println(value);    
        }});*/  

  
    @Test  
    public void test2() {  
        SinglyLinkedList list = new SinglyLinkedList();  
        list.addFirst(1);  
        list.addFirst(2);  
        list.addFirst(3);  
        list.addFirst(4);  
  
        for (Integer value : list) {  
            System.out.println(value);  
        }  
    }  
}
```

#### 递归遍历

```java
public class SinglyLinkedList implements Iterable<Integer> {
    // ...
    public void loop() {
        recursion(this.head);
    }

    private void recursion(Node curr) {
        if (curr == null) {
            return;
        }
        // 前面做些事
        recursion(curr.next);
        // 后面做些事
    }
}
```



#### 尾部添加

```java
public class SinglyLinkedList {
    // ...
    private Node findLast() {
        if (this.head == null) {
            return null;
        }
        Node curr;
        for (curr = this.head; curr.next != null; ) {
            curr = curr.next;
        }
        return curr;
    }
    
    public void addLast(int value) {
        Node last = findLast();
        if (last == null) {
            addFirst(value);
            return;
        }
        last.next = new Node(value, null);
    }
}
```

* 注意，找最后一个节点，终止条件是 curr.next == null 
* 分成两个方法是为了代码清晰，而且 findLast() 之后还能复用



#### 尾部添加多个

```java
public class SinglyLinkedList {
    // ...
	public void addLast(int first, int... rest) {
        
        Node sublist = new Node(first, null);
        Node curr = sublist;
        for (int value : rest) {
            curr.next = new Node(value, null);
            curr = curr.next;
        }
        
        Node last = findLast();
        if (last == null) {
            this.head = sublist;
            return;
        }
        last.next = sublist;
    }
}
```
* 先串成一串 sublist
* 再作为一个整体添加
#### 根据索引获取

```java
public class SinglyLinkedList {
    // ...
	private Node findNode(int index) {
        int i = 0;
        for (Node curr = this.head; curr != null; curr = curr.next, i++) {
            if (index == i) {
                return curr;
            }
        }
        return null;
    }
    
    private IllegalArgumentException illegalIndex(int index) {
        return new IllegalArgumentException(String.format("index [%d] 不合法%n", index));
    }
    
    public int get(int index) {
        Node node = findNode(index);
        if (node != null) {
            return node.value;
        }
        throw illegalIndex(index);
    }
}
```

* 同样，分方法可以实现复用



#### 插入

```java
public class SinglyLinkedList {
    // ...
	public void insert(int index, int value) {
        if (index == 0) {
            addFirst(value);
            return;
        }
        Node prev = findNode(index - 1); // 找到上一个节点
        if (prev == null) { // 找不到
            throw illegalIndex(index);
        }
        prev.next = new Node(value, prev.next);
    }
}
```

* 插入包括下面的删除，都必须找到上一个节点


#### removefirst
```java
public void removeFirst() {  
    if (head == null) {  
        throw illegalIndex(0);  
    }  
    head = head.next;  
}
```
#### remove

```java
public class SinglyLinkedList {
    // ...
	public void remove(int index) {
        if (index == 0) {
            if (this.head != null) {
                this.head = this.head.next;
                return;
            } else {
                throw illegalIndex(index);
            }
        }
        Node prev = findNode(index - 1);
        Node curr;
        if (prev != null && (curr = prev.next) != null) {
            prev.next = curr.next;
        } else {
            throw illegalIndex(index);
        }
    }
}
```

* 第一个 if 块对应着 removeFirst 情况
* 最后一个 if 块对应着至少得两个节点的情况
  * 不仅仅判断上一个节点非空，还要保证当前节点非空



### 3) 单向链表（带哨兵）

观察之前单向链表的实现，发现每个方法内几乎都有判断是不是 head 这样的代码，能不能简化呢？

用一个不参与数据存储的特殊 Node 作为哨兵，它一般被称为哨兵或哑元，拥有哨兵节点的链表称为带头链表

```java
public class SinglyLinkedListSentinel {
    // ...
    private Node head = new Node(Integer.MIN_VALUE, null);
}
```

* 具体存什么值无所谓，因为不会用到它的值

加入哨兵节点后，代码会变得比较简单，先看几个工具方法

```java
public class SinglyLinkedListSentinel {
    // ...
    
    // 根据索引获取节点
    private Node findNode(int index) {
        int i = -1;
        for (Node curr = this.head; curr != null; curr = curr.next, i++) {
            if (i == index) {
                return curr;
            }
        }
        return null;
    }
    
    // 获取最后一个节点
    private Node findLast() {
        Node curr;
        for (curr = this.head; curr.next != null; ) {
            curr = curr.next;
        }
        return curr;
    }
}
```

* findNode 与之前类似，只是 i 初始值设置为 -1 对应哨兵，实际传入的 index 也是 $[-1, \infty)$
* findLast 绝不会返回 null 了，就算没有其它节点，也会返回哨兵作为最后一个节点



这样，代码简化为

```java
public class SinglyLinkedListSentinel {
    // ...
    
    public void addLast(int value) {
        Node last = findLast();
        /*
        改动前
        if (last == null) {
            this.head = new Node(value, null);
            return;
        }
        */
        last.next = new Node(value, null);
    }
    
    public void insert(int index, int value) {
        /*
        改动前
        if (index == 0) {
            this.head = new Node(value, this.head);
            return;
        }
        */
        // index 传入 0 时，返回的是哨兵
        Node prev = findNode(index - 1);
        if (prev != null) {
            prev.next = new Node(value, prev.next);
        } else {
            throw illegalIndex(index);
        }
    }
    
    public void remove(int index) {
        /*
        改动前
        if (index == 0) {
            if (this.head != null) {
                this.head = this.head.next;
                return;
            } else {
                throw illegalIndex(index);
            }
        }
        */
        // index 传入 0 时，返回的是哨兵
        Node prev = findNode(index - 1);
        Node curr;
        if (prev != null && (curr = prev.next) != null) {
            prev.next = curr.next;
        } else {
            throw illegalIndex(index);
        }
    }
    
    public void addFirst(int value) {
        /*
        改动前
        this.head = new Node(value, this.head);
        */
		this.head.next = new Node(value, this.head.next);
        // 也可以视为 insert 的特例, 即 insert(0, value);
    }
}
```

* 对于删除，前面说了【最后一个 if 块对应着至少得两个节点的情况】，现在有了哨兵，就凑足了两个节点



### 4) 双向链表（带哨兵）

```java
public class DoublyLinkedListSentinel implements Iterable<Integer> {

    private final Node head;
    private final Node tail;

    public DoublyLinkedListSentinel() {
        head = new Node(null, 666, null);
        tail = new Node(null, 888, null);
        head.next = tail;
        tail.prev = head;
    }

    private Node findNode(int index) {
        int i = -1;
        for (Node p = head; p != tail; p = p.next, i++) {
            if (i == index) {
                return p;
            }
        }
        return null;
    }

    public void addFirst(int value) {
        insert(0, value);
    }

    public void removeFirst() {
        remove(0);
    }

    public void addLast(int value) {
        Node prev = tail.prev;
        Node added = new Node(prev, value, tail);
        prev.next = added;
        tail.prev = added;
    }

    public void removeLast() {
        Node removed = tail.prev;
        if (removed == head) {
            throw illegalIndex(0);
        }
        Node prev = removed.prev;
        prev.next = tail;
        tail.prev = prev;
    }

    public void insert(int index, int value) {
        Node prev = findNode(index - 1);
        if (prev == null) {
            throw illegalIndex(index);
        }
        Node next = prev.next;
        Node inserted = new Node(prev, value, next);
        prev.next = inserted;
        next.prev = inserted;
    }

    public void remove(int index) {
        Node prev = findNode(index - 1);
        if (prev == null) {
            throw illegalIndex(index);
        }
        Node removed = prev.next;
        if (removed == tail) {
            throw illegalIndex(index);
        }
        Node next = removed.next;
        prev.next = next;
        next.prev = prev;
    }

    private IllegalArgumentException illegalIndex(int index) {
        return new IllegalArgumentException(
                String.format("index [%d] 不合法%n", index));
    }

    @Override
    public Iterator<Integer> iterator() {
        return new Iterator<Integer>() {
            Node p = head.next;

            @Override
            public boolean hasNext() {
                return p != tail;
            }

            @Override
            public Integer next() {
                int value = p.value;
                p = p.next;
                return value;
            }
        };
    }

    static class Node {
        Node prev;
        int value;
        Node next;

        public Node(Node prev, int value, Node next) {
            this.prev = prev;
            this.value = value;
            this.next = next;
        }
    }
}
```



### 5) 环形链表（带哨兵）

双向环形链表带哨兵，这时哨兵**既作为头，也作为尾**

![image-20221229144232651](./imgs/image-20221229144232651.png)

![image-20221229143756065](./imgs/image-20221229143756065.png)

![image-20221229153338425](./imgs/image-20221229153338425.png)

![image-20221229154248800](./imgs/image-20221229154248800.png)

参考实现

```java
public class DoublyLinkedListSentinel implements Iterable<Integer> {

    @Override
    public Iterator<Integer> iterator() {
        return new Iterator<>() {
            Node p = sentinel.next;

            @Override
            public boolean hasNext() {
                return p != sentinel;
            }

            @Override
            public Integer next() {
                int value = p.value;
                p = p.next;
                return value;
            }
        };
    }

    static class Node {
        Node prev;
        int value;
        Node next;

        public Node(Node prev, int value, Node next) {
            this.prev = prev;
            this.value = value;
            this.next = next;
        }
    }

    private final Node sentinel = new Node(null, -1, null); // 哨兵

    public DoublyLinkedListSentinel() {
        sentinel.next = sentinel;
        sentinel.prev = sentinel;
    }

    /**
     * 添加到第一个
     * @param value 待添加值
     */
    public void addFirst(int value) {
        Node next = sentinel.next;
        Node prev = sentinel;
        Node added = new Node(prev, value, next);
        prev.next = added;
        next.prev = added;
    }

    /**
     * 添加到最后一个
     * @param value 待添加值
     */
    public void addLast(int value) {
        Node prev = sentinel.prev;
        Node next = sentinel;
        Node added = new Node(prev, value, next);
        prev.next = added;
        next.prev = added;
    }
    
    /**
     * 删除第一个
     */
    public void removeFirst() {
        Node removed = sentinel.next;
        if (removed == sentinel) {
            throw new IllegalArgumentException("非法");
        }
        Node a = sentinel;
        Node b = removed.next;
        a.next = b;
        b.prev = a;
    }

    /**
     * 删除最后一个
     */
    public void removeLast() {
        Node removed = sentinel.prev;
        if (removed == sentinel) {
            throw new IllegalArgumentException("非法");
        }
        Node a = removed.prev;
        Node b = sentinel;
        a.next = b;
        b.prev = a;
    }

    /**
     * 根据值删除节点
     * <p>假定 value 在链表中作为 key, 有唯一性</p>
     * @param value 待删除值
     */
    public void removeByValue(int value) {
        Node removed = findNodeByValue(value);
        if (removed != null) {
            Node prev = removed.prev;
            Node next = removed.next;
            prev.next = next;
            next.prev = prev;
        }
    }

    private Node findNodeByValue(int value) {
        Node p = sentinel.next;
        while (p != sentinel) {
            if (p.value == value) {
                return p;
            }
            p = p.next;
        }
        return null;
    }
}
```

### 习题
#### 学会自己定义ListNode
```java
public class ListNode {
    // 结点的值
    int val;

    // 下一个结点
    ListNode next;

    // 节点的构造函数(无参)
    public ListNode() {
    }

    // 节点的构造函数(有一个参数)
    public ListNode(int val) {
        this.val = val;
    }

    // 节点的构造函数(有两个参数)
    public ListNode(int val, ListNode next) {
        this.val = val;
        this.next = next;
    }
}
```
#### 707 design linked list

```java
class MyLinkedList {

    class ListNode {
        int val;
        ListNode next;
        ListNode(int val) {
            this.val = val;
        }
    }

    private ListNode dummyhead; // 虚拟头节点
    private int size;           // 当前链表长度

    public MyLinkedList() {
        this.dummyhead = new ListNode(0); // dummyhead 不存值，只做哨兵节点
        this.size = 0;
    }

    // 获取第 index 个节点的值（0-based），非法返回 -1
    public int get(int index) {
        if (index < 0 || index >= size) return -1;

        ListNode cur = dummyhead.next;
        while (index > 0) {
            cur = cur.next;
            index--;
        }
        return cur.val;
    }

    // 在头部插入
    public void addAtHead(int val) {
        ListNode newnode = new ListNode(val);
        newnode.next = dummyhead.next;
        dummyhead.next = newnode;
        size++;
    }

    // 在尾部插入
    public void addAtTail(int val) {
        ListNode cur = dummyhead;
        while (cur.next != null) {
            cur = cur.next;
        }
        cur.next = new ListNode(val);
        size++;
    }

    // 在第 index 个位置插入节点（0-based）
    // 若 index == size，则相当于尾插；若 index < 0，则相当于头插
    public void addAtIndex(int index, int val) {
        if (index < 0 || index > size) return;

        ListNode cur = dummyhead;
        while (index > 0) {
            cur = cur.next;
            index--;
        }
        ListNode newnode = new ListNode(val);
        newnode.next = cur.next;
        cur.next = newnode;
        size++;
    }

    // 删除第 index 个节点
    public void deleteAtIndex(int index) {
        if (index < 0 || index >= size) return;

        ListNode cur = dummyhead;
        while (index > 0) {
            cur = cur.next;
            index--;
        }
        cur.next = cur.next.next;
        size--;
    }
}
```
#### 反转单向链表-Leetcode 206


对应力扣题目 [206. 反转链表 - 力扣（LeetCode）](https://leetcode.cn/problems/reverse-linked-list/)

```
输入：head = [1,2,3,4,5]
输出：[5,4,3,2,1]

输入：[1,2]
输出：[2,1]

输入：[]
输出：[]
```
##### ListNode
题中给的定义
```java
public class ListNode {
    int val; // 当前节点值
    ListNode next; // ❓为什么它的类型是自己 ListNode？
}
```
这是一个**链表节点**类，每个节点需要**知道“下一个节点”是谁**，所以就用同样类型的字段 `next` 来指向下一个 `ListNode`。
✅这叫**递归数据结构**,定义里包含自己的类型，这在数据结构里很常见

❓会不会造成无限递归/死循环？
不会的，因为每个字段只是**一个引用（指针）**，不是直接嵌套一个新对象。

##### 标准方法1 双指针
```java
class Solution {
    public ListNode reverseList(ListNode head) {
        ListNode cur = head;
        ListNode pre = null;
        ListNode temp = null;
        while(cur != null) {
            temp = cur.next; // 先保存下一步临时指针要走的方向
            cur.next = pre; // 反转链表，改变当前节点的方向，不影响temp里存着的下一个节点
            pre = cur; // 一定是先移动pre
            cur = temp; //再移动cur
        }
        return pre;
    }
}
```

##### 标准方法2 递归
```java
class Solution {
    public ListNode reverseList(ListNode head) {
        return reverse(head,null);
    }
    private ListNode reverse(ListNode cur, ListNode pre) {
        if (cur == null) return pre;
        ListNode temp = cur.next;
        cur.next = pre;
        return reverse(temp, cur);
    }
}
```

#### 根据值删除节点-Leetcode 203

例如

```
输入：head = [1,2,6,3,6], val = 6
输出：[1,2,3]

输入：head = [], val = 1
输出：[]

输入：head = [7,7,7,7], val = 7
输出：[]
```

##### 方法1 原链表删除元素
```java
class Solution {
    public ListNode removeElements(ListNode head, int val) {
    // 删除头节点，直接下移就行
        while (head != null && head.val == val) {
            head = head.next; 
        }

        ListNode cur = head;//定义一个指针指向head
        while (cur != null && cur.next != null) {//如果cur = null,cur.next会抛异常
            if (cur.next.val == val) {
                cur.next = cur.next.next;
            } else {
                cur = cur.next;
            }
        }
        return head;
    }
}
```
##### 方法2 使用虚拟头结点
```java
class Solution {
    public ListNode removeElements(ListNode head, int val) {
        ListNode dummyhead = new ListNode();
        dummyhead.next = head;
        ListNode cur = dummyhead;
        while (cur.next != null) {
            if (cur.next.val == val) {
               cur.next = cur.next.next;
            } else {
                cur = cur.next;
            }
        }
        return dummyhead.next;  
    }
}
```

#### leetcode142环形链表
```java
public class Solution {
    public ListNode detectCycle(ListNode head) {
        ListNode fast = head;
        ListNode slow = head;
        while (fast != null && fast.next != null) {
            fast = fast.next.next;
            slow = slow.next;

            if (fast == slow) {
                ListNode index1 = fast;
                ListNode index2 = head;

                while (index1 != index2) {
                    index1 = index1.next;
                    index2 = index2.next;
                }
                return index1;
            } 
        }

        return null; // ✅ 循环跑完了都没相遇，说明没有环
    }
}
```

#### leetcode24 swap every two adjacent nodes

思路： 
两两分组
虚拟头节点 cur指向虚拟头节点，这样才能操作指向第一个节点的指针
什么时候结束 接下来两个都为空
```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */

class Solution {
    public ListNode swapPairs(ListNode head) {
        ListNode dummyhead = new ListNode();
        dummyhead.next = head;
        ListNode cur = dummyhead;
        while (cur.next != null && cur.next.next != null) { 
        //要保证有一对节点可以交换，即当前 cur后面至少还有两个节点，而且必须cur.next.next写在后面，这样万一cur.next已经为空，cur.next.next会报空指针异常
            ListNode first = cur.next;
            ListNode second = cur.next.next;
            ListNode third = second.next;

            // swap
            cur.next = second;
            second.next = first;
            first.next = third;
            cur = first; // 继续处理下一对
        }

        return dummyhead.next;
    }
}
```

## 2.3哈希表
如果在做面试题目的时候遇到需要判断一个元素是否出现过的场景也应该第一时间想到哈希法！
### 习题
#### leetcode 349 返回两个数组的交集 hashset

```java
class Solution {
    public int[] intersection(int[] nums1, int[] nums2) {
        // 把 nums1 的所有数字放进 set1
        Set<Integer> set1 = new HashSet<>();
        Set<Integer> resultSet = new HashSet<>();
        for (int i : nums1) {
            set1.add(i);
        }
        // 如果 nums2 中的数字在 nums1 中出现过，加入结果集
        for (int j :nums2) {
            if(set1.contains(j)) {
                resultSet.add(j);
            }
        }
        // 把结果集转成 int[] 数组返回
        int[] result = new int[resultSet.size()];
        int i = 0;
        for(int num : resultSet) {
            result[i] = num;
            i++;
        }
        return result;   
    }
}
```
#### leetcode [2824. Count Pairs Whose Sum is Less than Target](https://leetcode.com/problems/count-pairs-whose-sum-is-less-than-target/)
##### 双指针法

```java
class Solution {
    public int countPairs(List<Integer> nums, int target) {
        int res = 0;
        Collections.sort(nums);
        int size = nums.size();
        int l = 0;
        int r = size - 1;
        while (l < r) {
            int sum = nums.get(l) + nums.get(r);
            if (sum < target) {
                res += r - l;
                l++;
            } else {
                r--;
            }
        }
        return res;
    }
}
```

##### 如果去重，可以使用hashset去重

```java
class Solution {
    public int countPairs(List<Integer> nums, int target) {
        Collections.sort(nums);
        Set<List<Integer>> set = new HashSet<>();
        int l = 0, r = nums.size() - 1;

        while (l < r) {
            int sum = nums.get(l) + nums.get(r);
            if (sum < target) {
                set.add(Arrays.asList(nums.get(l), nums.get(r)));
                l++;
            } else {
                r--;
            }
        }
        return set.size(); // 返回去重后的有效 pair 数量
    }
}
```

为什么选择`Set<List<Integer>>`
- `List<Integer>` 表示一个“数对”，如 `[nums[i], nums[j]]`，例如：
```java
[1, 2]
[3, 4]
```
- `Set` 可以自动去掉重复的 List，比如 `[1,2]` 和 `[1,2]` 只保留一个,
```java
[
  [1, 2],
  [3, 4]
]
```

和 Set<int[ ]> 有什么区别？
```java
Set<int[]> set = new HashSet<>();
set.add(new int[]{1, 2});
set.add(new int[]{1, 2});
System.out.println(set.size()); // ❌ 是 2，不是 1！
```
这是因为 `int[]` 没有实现 `.equals()` 和 `.hashCode()`，所以两个数组即使内容一样也不会被认为是“重复”。
而 `List<Integer>`（如 `ArrayList`）已经帮我们实现了正确的比较。
#### leetcode 350 返回两个数组的交集 hashmap
```java
class Solution {
    public int[] intersect(int[] nums1, int[] nums2) {
        Map<Integer, Integer> map = new HashMap<>();
        // 统计nums1中数字出现的次数,key是数字，value是出现次数
        for (int i : nums1) {
            if (map.containsKey(i)) {
                map.put(i, map.get(i) + 1);
            } else {
                map.put(i, 1);
            }
        }
        // ArrayList 内部可以自动扩容，而数组不行
        List<Integer> result = new ArrayList<>();
        //检测nums2,每次遇到一个在nums1中还没“用完”的元素，就放到结果里，减少一次配额
        for (int j : nums2) {
            if (map.containsKey(j) && map.get(j) > 0) {
                result.add(j);
                map.put(j, map.get(j) - 1);
            }
        }
  
        int[] resArray = new int[result.size()];
        for (int k = 0; k < resArray.length; k++) {
            resArray[k] = result.get(k);
        }

        return resArray;
    }
}
```

#### leetcode 454四数相加Ⅱ
```java
class Solution {
    public int fourSumCount(int[] nums1, int[] nums2, int[] nums3, int[] nums4) {
        int res = 0;
        Map<Integer, Integer> map = new HashMap<Integer, Integer>();
        //统计两个数组中的元素之和，同时统计出现的次数，放入map
        for (int i : nums1) {
            for (int j : nums2) {
                int sum = i + j;
                map.put(sum, map.getOrDefault(sum, 0) + 1);
            }
        }
        //统计剩余的两个元素的和，在map中找是否存在相加为0的情况，同时记录次数
        for (int i : nums3) {
            for (int j : nums4) {
                res += map.getOrDefault(0 - i - j, 0);
            }
        }
        return res;
    }
}
```

为什么最后能直接返回res?

#### leetcode[15. 3Sum](https://leetcode.com/problems/3sum/)
```java
class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();
        Arrays.sort(nums);
        int n = nums.length;
        for (int i = 0; i < n - 2 ; i++) {
          //最后两个位置留给left和right
          int x = nums[i];
          if (i > 0 && x == nums[i - 1]) continue; //对第一个数字去重
          if (x + nums[i + 1] + nums[i + 2] > 0) break;
          if (x + nums[n - 2] + nums[n - 1] < 0) continue;
          int l = i + 1;
          int r = n - 1;
          while (l < r) {
            int s = x + nums[l] + nums[r];
            if (s > 0) {
                r--;
            } else if (s < 0) {
                l++;
            } else {
                res.add(List.of(x, nums[l], nums[r]));
                while(r > l && nums[r] == nums[r - 1]) r--; // 去重r
                while(r > l && nums[l] == nums[l + 1]) l++; // 去重l
                l++;//继续找新的可能
                r--;
            }
          }
        }
        return res;
    }
}
```

1.continue和break
|`continue`|**跳过本次循环，直接进入下一次循环**|
|`break`|**彻底退出整个循环，后面的循环都不再执行**|

2.定义两遍 r > l
“外层 while”只管整个双指针循环，而现在是在 while 循环体内处理“重复值去重”**，这时 `l` 和 `r` 的值已经变动过了，**必须重新判断是否交叉（l >= r）**，否则就可能越界。
#### leetcode18 四数之和

| 项目           | 三数之和 `3Sum`                          | 四数之和 `4Sum`                                                  | 共通点说明             |
| ------------ | ------------------------------------ | ------------------------------------------------------------ | ----------------- |
| **排序**       | 需要排序                                 | 需要排序                                                         | 排序是前提，确保双指针能工作    |
| **目标和**      | 固定一个数 `x = nums[i]`，再找 `b + c == -x` | 固定两个数 `x = nums[k], y = nums[i]`，找 `c + d == target - x - y` | 多固定一个数            |
| **双指针**      | 一层 for + 一层 while(left < right)      | 两层 for + 一层 while(left < right)                              | 外部 for 固定，内部双指针推进 |
| **去重逻辑**     | 对 `i`、`left`、`right` 去重              | 对 `k`、`i`、`left`、`right` 去重                                  | 全部都需要跳过重复值        |
| **时间复杂度**    | `O(n²)`                              | `O(n³)`                                                      | 多嵌套一层循环           |
| **剪枝优化（可选）** | 提前终止不可能满足条件的分支                       | 同样可剪枝（例如：当前值 + 最小值组合 > target）                               | 排序后可做剪枝判断         |
```java
class Solution {
    public List<List<Integer>> fourSum(int[] nums, int target) {
        List<List<Integer>> res = new ArrayList<>();
        int n = nums.length;
        Arrays.sort(nums);
        for (int k = 0; k < n - 3; k++) { // 第1层：固定 nums[k]
            if (nums[k] > target && target >= 0) break;
            if (k > 0 && nums[k] == nums[k - 1]) continue;
            
            for (int i = k + 1; i < n - 2; i++) {// 第2层：固定 nums[i]
                if (nums[k] + nums[i] > target && target >= 0) break;
                if (i > k + 1 && nums[i] == nums[i - 1]) continue;
            int l = i + 1;
            int r = n - 1;
  
            while (l < r) { // 第3层：双指针夹逼
                long s = (long)nums[k] + nums[i] + nums[l] + nums[r];//特别注意，防止溢出
                if (s > target) {
                    r--;
                } else if (s < target){
                    l++;
                } else {
                    res.add(List.of(nums[k],nums[i],nums[l],nums[r]));
                    while (r > l && nums[r] == nums[r - 1]) r--;
                    while (r > l && nums[l] == nums[l + 1]) l++;
                    l++; // 下一轮组合
                    r--;
                }
            }  
        }
     }
        return res;
    }
}
```

总结：“**前两位定死，后两位夹逼，找到就去重，再推进搜索**”

#### leetcode [16. 3Sum Closest](https://leetcode.com/problems/3sum-closest/)
```java
class Solution {
    public int threeSumClosest(int[] nums, int target) {
        Arrays.sort(nums);
        int n = nums.length;
        int minDiff = Integer.MAX_VALUE;
        int closestSum = 0;

        for (int i = 0; i < n - 2; i++) {
            int x = nums[i];
            int l = i + 1;
            int r = n - 1;

            while (l < r) {
                int s = x + nums[l] + nums[r];
                int diff = Math.abs(s - target);
  

                if (diff < minDiff) {
                    minDiff = diff;
                    closestSum = s;
                }

                if (s < target) {
                    l++;
                } else if (s > target) {
                    r--;
                } else {
                    return target;
                }
            }
        }
        return closestSum;
    }

}
```

## 2.4字符串
### char和string
char 是基本类型，单个字符
string是引用类型，多个字符，**不可变**
char[]是引用类型，是数组，无论是 `char[]`、`int[]`、`String[]` 都是。char[] arr = {'a', 'b', 'c'};

| 类型                                  | 类别   | 示例     |
| ----------------------------------- | ---- | ------ |
| `int`, `char`, `boolean`            | 基本类型 | 值本身存栈上 |
| `int[]`, `char[]`, `String`, `List` | 引用类型 | 存的是堆地址 |
char和string的转化
```java
// ✅ char → String
String s1 = String.valueOf('a'); // 推荐
String s2 = Character.toString('b');

// String → char（单个字符）
char c1 = "abc".charAt(0); //取第一个字符

// ✅String → char[]（转数组）
char[] arr = "hello".toCharArray();

// ✅char[] → String （数组变string）
String s4 = new String(arr);
String s5 = String.valueOf(arr);
```

### 引用类型和基本类型的返回
1.如果我们想定义一个reverse
```java
public void reverse(char[] ch, int i, int j) {
    for (; i < j; i++, j--) {
        char temp  = ch[i];
        ch[i] = ch[j];
        ch[j] = temp;
    }
```
✔️ 因为数组是引用类型，函数体内的修改会影响原数组，所以可以 `void`，不返回也没问题。
 2. 基本类型 + `void`（就不行？❌）
**其实也可以用 `void`，只是值传递不会改变原值**：
```java
public void changeInt(int x) {
    x = 10; // 改变的是副本，对原始变量无影响
}
```
这个时候不返回 `x` 就等于什么都没发生，调用方根本拿不到新值：
```java
int a = 5;
changeInt(a); // a 仍然是 5
```
所以对于基本类型，如果你想“改变值”，**必须返回结果**，比如：
```java
public int changeInt(int x) {
    return x + 1;
}
```
3.总结
> 是否能用 `void`，**取决于你有没有需要返回的结果**，而不是参数是不是引用类型。
- 引用类型可以 `void`，因为你改的就是原数据。
- 基本类型用 `void` 会“白改”，因为是值拷贝，不会生效，所以通常会返回新值。
### 习题
#### leetcode 344 reverse string
```java
class Solution {
    public void reverseString(char[] s) {
        int n = s.length;
        int l = 0, r = n - 1;
        while(l < r) {
           char temp = s[l];
           s[l] = s[r];
           s[r] = temp;
           l++;
           r--;
        }
    }
}
```

#### leetcode 541 reverse stringⅡ
##### 分析思路：
1. string不可变，先转换为char[ ]
2. 编写reverse函数，双指针交换，反转指定区间。这里的char[]可以变，无须返回对象
3. 开始让i遍历。在遍历字符串的过程中，只要让 i += (2 * k)，i 每次移动 2 * k 就可以了，判断是否能反转 `k` 个字符，如果不足 `k`，就反转到末尾
4. 把 `char[]` 转回 `String` 返回

##### 数字的`数组的 i 到 i+k-1` 区间模版
```java
Math.min(i + k - 1, arr.length - 1) 
```
**只在最后一次** 有可能触发 `ch.length - 1`，其余时候就是 `i + k - 1`。这样就可以对每段区间做判断，避免讨论整个ch

##### 代码：
```java
class Solution {
    public String reverseStr(String s, int k) {
        char[] ch = s.toCharArray();
        for (int i = 0; i < ch.length; i += 2*k) {
            int end = Math.min(i + k - 1, ch.length - 1);
            reverse(ch, i, end);
        }
        return new String(ch);
    }

    public void reverse(char[] ch, int i, int j) {
        for (; i < j; i++, j--) {
            char temp = ch[i];
            ch[i] = ch[j];
            ch[j] = temp;
        }
    }
}
```

#### 541变种题
对于字符串 `s` 和整数 `k`，**每隔 k 个字符就反转 k 个字符**（不是反转前 k，而是 **跳过 k，再反转 k**，如此反复）
例如
```java
s = "abcdefghijk", k = 2

操作过程：
跳过 a b → 反转 c d → 跳过 e f → 反转 g h → 跳过 i j → 反转 k

结果是："abdcfehgjik"
```
从下标 `i = k` 开始，每隔 `2k` 步，反转一段长度为 `k` 的字符。
```java
for (int i = k; i < ch.length; i += 2 * k) {
    int end = Math.min(i + k - 1, ch.length - 1);
    reverse(ch, i, end);
}
```

#### leetcode[151. Reverse Words in a String](https://leetcode.com/problems/reverse-words-in-a-string/)

快慢指针
fast遍历整个数组，读取字符
slow把有效字符写入前面位置

```java
class Solution {
    public String reverseWords(String s) {
        // Step 1: Convert to char array and remove extra spaces
        char[] ch = s.toCharArray();
        int len = removeExtraSpaces(ch);// 原地修改 ch，会直接影响原来的ch数组本身

        // Step 2: Reverse the whole valid part of the array
        reverse(ch, 0, len - 1);//后面可能跟垃圾空格，表示从数组 `ch` 的下标 `0` 开始，提取 `len` 个字符，构造字符串。

        // Step 3: Reverse each word
        int start = 0;
        for (int i = 0; i <= len; i++) {
            if (i == len || ch[i] == ' ') {
                reverse(ch, start, i - 1);
                start = i + 1;
            }
        }
        return new String(ch, 0, len);
    }

    // Removes leading, trailing, and multiple spaces. Returns new valid length.
    private int removeExtraSpaces(char[] ch) {
        int n = ch.length;
        int fast = 0, slow = 0;

        // Skip leading spaces 跳过开头的空格，指向第一个有内容的字母
        while (fast < n && ch[fast] == ' ') fast++;

        while (fast < n) {
            // Copy non-space characters
            if (ch[fast] != ' ') {
                ch[slow] = ch[fast];
                slow++;
                fast++;
            } else {
                // Only keep one space between words
                ch[slow++] = ' ';// 遇到单词间的冗余空格，先写一个空格
                while (fast < n && ch[fast] == ' ') fast++;// fast再跳过所有空格，直到指向有内容的字母
            }
        }

        // Remove trailing space 删除末尾空格
        if (slow > 0 && ch[slow - 1] == ' ') slow--;//防止全空的数组，防御性编程
        return slow; // New valid length
    }

    // In-place reverse from l to r
    private void reverse(char[] ch, int l, int r) {
        while (l < r) {
            char temp = ch[l];
            ch[l++] = ch[r];
            ch[r--] = temp;
        }
    }
}
```

## 2.5KMP算法
https://github.com/youngyangyang04/leetcode-master/blob/master/problems/0028.%E5%AE%9E%E7%8E%B0strStr.md
### next数组
#### next数组原理 -- 我们要构造什么
对于模式串 `s[0...i]`，我们要找出：
```java
s[0...k-1] == s[i-k+1...i]
```
即：**前缀 == 后缀**，但不能等于整个字符串。

我们需要一个**算法**去“自动”找出这个 `k`
KMP 的构造过程用了一个关键策略：
>如果我们已经知道 `s[0...i-1]` 的最长相等前后缀是 `j`，那我们尝试直接扩展这个前缀，看看能不能让 `s[0...i]` 也匹配。

我们设：
- `i` 是当前考察的字符位置，尝试构造 `next[i]`
- `j` 是 `s[0...i-1]` 的最长相等前后缀的“末尾位置”

**我们希望通过 判断 `s[i] == s[j+1]` 来扩展之前已知的前后缀。**

合法性的关键：
如果 `s[i] == s[j + 1]`，说明：

- 原来已知的前缀 `"s[0...j]"` 和后缀 `"s[i-j...i-1]"` 是一样的；???
- 现在 `s[i] == s[j+1]`，意味着：
    - 可以把前缀和后缀各扩展一个字符；
    - 所以新的最长相等前后缀就是 `j + 1`

如果 `s[i] != s[j + 1]`
那就**回退** `j` 到上一次可能的匹配长度：
`j = next[j]; // 更短的前缀`


#### 构造next数组
**构造next数组是计算模式串s前缀表的过程。** 主要有如下三步：

1. 初始化
2. 处理前后缀不相同的情况
3. 处理前后缀相同的情况

**1.初始化：**
定义两个指针`i`和`j`，
**`j`指向前缀末尾位置 -- 最长匹配前缀长度为 `j + 1`，即末尾位置是 `j`**
**`i`指向后缀末尾位置 -- 当前正在处理的位置，用于构造 `next[i]`**
然后还要对next数组进行初始化赋值，如下：
`int j = -1 // 按统一减一写法`
`next[0] = j // 固定写法`
对于 `i = 0`，也就是只有一个字符的情况：
如`s = "a"`
**问：这个长度为 1 的字符串，有没有相等的前后缀？**
- 没有。
- 所以最长相等前后缀长度是 0 ——但我们定义的是 `j = -1`，所以就有：
- `next[0] = -1;`

**2.处理过程**
因为j初始化为-1，那么i就从1开始，进行s[i] 与 s[j+1]的比较。
Step 1: `i = 1`, `j = -1`
```java
s     =  a   b   a   b   c
index =  0   1   2   3   4
             ↑
             i
j = -1 → s[j+1] = s[0] = a

s[1] = b ≠ s[0] = a → 不匹配，无法回退
当 `j == -1` 时，`while` 条件不成立，不进入循环
→ 直接设置 next[1] = -1
更新后：
next = [-1, -1, ?, ?, ?]
```

Step 2: `i = 2`, `j = -1`
```java
s     =  a   b   a   b   c
index =  0   1   2   3   4
                 ↑
                 i
j = -1 → s[j+1] = s[0] = a

s[2] = a == s[0] ✅ 匹配
→ j++, j = 0
→ 设置 next[2] = j = 0
更新后：
next = [-1, -1, 0, ?, ?]
```

Step 3: `i = 3`, `j = 0`
当前比较：`s[3]` 和 `s[1]`
```java
s     =  a   b   a   b   c
index =  0   1   2   3   4
                     ↑
                     i
j = 0 → s[j+1] = s[1] = b

s[3] = b == s[1] ✅ 匹配
→ j++, j = 1
→ 设置 next[3] = j = 1
更新后：
next = [-1, -1, 0, 1, ?]
```

Step 4: `i = 4`, `j = 1`
当前比较：`s[4]` 和 `s[2]`
```java
s     =  a   b   a   b   c
index =  0   1   2   3   4
                         ↑
                         i
j = 1 → s[j+1] = s[2] = a

s[4] = c ≠ s[2] = a ❌ 不匹配
→ 回退 j = next[1] = -1

现在 j = -1，再比较 s[4] 和 s[0]

s[4] = c ≠ s[0] = a ❌ 不匹配
→ 设置 next[4] = -1
next = [-1, -1, 0, 1, -1]
```

代码部分：
```java
    public static int[] getNext(String s) {
        int[] next = new int[s.length()];
        next[0] = -1;
        int j = -1;

        for (int i = 1; i < s.length(); i++) {
            while (j >= 0 && s.charAt(i) != s.charAt(j + 1)) {
                j = next[j];
            }
            if (s.charAt(i) == s.charAt(j + 1)) {
                j++;
            }
            next[i] = j;
        }

        return next;
    }
```
口诀：
    固定 -1 开始  
    i 从 1 开始  
    不匹配就回退  
    匹配就 j++  
    把 j 存进 next[i]

### 用 KMP 在**文本串 `s` 中查找模式串 `t`
`i` 是当前在**文本串**中匹配到的位置（最后一个匹配字符的位置）
`j` 是当前在**模式串**中的匹配位置
我们用 KMP 在**文本串 `s` 中查找模式串 `t`**，如果匹配成功：
> `j` 会刚好指向模式串 `t` 的末尾，即 `j == t.length() - 1`

此时我们要返回**匹配的起始位置**，即：
```java
return i - t.length() + 1;
```

### 全部的代码
```java
public class KMP {
    /**
     * 构造 next 数组（前缀表）
     * next[i] 表示：t[0..i] 这一段中，最长相等前后缀的末尾位置（长度为 j+1）
     */
    public static int[] getNext(String t) {
        int[] next = new int[t.length()];
        next[0] = -1; // 固定写法：t[0] 没有前缀，设为 -1
        int j = -1;   // j 表示当前前缀的末尾位置

        for (int i = 1; i < t.length(); i++) {
            // 如果当前字符和前缀不匹配，回退 j
            while (j >= 0 && t.charAt(i) != t.charAt(j + 1)) {
                j = next[j];  // 不匹配就跳到上一个可能的前缀位置
            }

            // 如果匹配，扩展前缀长度
            if (t.charAt(i) == t.charAt(j + 1)) {
                j++;
            }

            // 记录当前 i 对应的最长前后缀长度（末尾下标）
            next[i] = j;
        }

        return next;
    }

    /**
     * KMP 主算法：在主串 s 中查找模式串 t 的第一次出现位置
     * 若找到，返回起始下标；否则返回 -1
     */
    public static int kmpSearch(String s, String t) {
        if (t.length() == 0) return 0;  // 空模式串默认返回 0

        int[] next = getNext(t);  // 构造前缀表
        int j = -1;  // 模式串指针

        for (int i = 0; i < s.length(); i++) {
            // 当前不匹配就回退 j
            while (j >= 0 && s.charAt(i) != t.charAt(j + 1)) {
                j = next[j];
            }

            // 匹配成功则 j++
            if (s.charAt(i) == t.charAt(j + 1)) {
                j++;
            }

            // 模式串匹配完毕，返回匹配起点
            if (j == t.length() - 1) {
                return i - t.length() + 1;
            }
        }

        return -1; // 没有匹配成功
    }

    // 测试样例
    public static void main(String[] args) {
        String s = "ababababc";   // 主串
        String t = "ababc";       // 模式串

        int pos = kmpSearch(s, t);

        System.out.println("模式串第一次出现的位置: " + pos);  // 应输出 4
    }
}

```

## 2.6 队列
### 概述

计算机科学中，queue 是以顺序的方式维护的一组数据集合，在一端添加数据，从另一端移除数据。习惯来说，添加的一端称为**尾**，移除的一端称为**头**，就如同生活中的排队买商品

> In computer science, a **queue** is a collection of entities that are maintained in a sequence and can be modified by  the addition of entities at one end of the sequence and the removal of  entities from the other end of the sequence

### 基础操作
```java
public interface Queue<E> {

    
     * 向队列尾插入值
     * @param value 待插入值
     * @return 插入成功返回 true, 插入失败返回 false
     
    boolean offer(E value);

    
     * 从对列头获取值, 并移除
     * @return 如果队列非空返回对头值, 否则返回 null
     
    E poll();

    
     * 从对列头获取值, 不移除
     * @return 如果队列非空返回对头值, 否则返回 null
     
    E peek();

    
     * 检查队列是否为空
     * @return 空返回 true, 否则返回 false
     
    boolean isEmpty();

    
     * 检查队列是否已满
     * @return 满返回 true, 否则返回 false
     
    boolean isFull();
}
```

### 链表实现
下面以**单向环形带哨兵**链表方式来实现队列

![image-20221230150105089.png](https://cdn.jsdelivr.net/gh/hoo01/image_auto/image-20221230150105089.png)
![image-20221230150141318.png](https://cdn.jsdelivr.net/gh/hoo01/image_auto/image-20221230150141318.png)
![image-20221230150153271.png](https://cdn.jsdelivr.net/gh/hoo01/image_auto/image-20221230150153271.png)

代码
```java
public class LinkedListQueue<E>
        implements Queue<E>, Iterable<E> {

    private static class Node<E> {
        E value;
        Node<E> next;

        public Node(E value, Node<E> next) {
            this.value = value;
            this.next = next;
        }
    }

    private Node<E> head = new Node<>(null, null);
    private Node<E> tail = head;
    private int size = 0;
    private int capacity = Integer.MAX_VALUE;

    {
        tail.next = head;
    }

    public LinkedListQueue() {
    }

    public LinkedListQueue(int capacity) {
        this.capacity = capacity;
    }

    @Override
    public boolean offer(E value) {
        if (isFull()) {
            return false;
        }
        Node<E> added = new Node<>(value, head);
        tail.next = added;
        tail = added;
        size++;
        return true;
    }

    @Override
    public E poll() {
        if (isEmpty()) {
            return null;
        }
        Node<E> first = head.next;
        head.next = first.next;
        if (first == tail) {
            tail = head;
        }
        size--;
        return first.value;
    }

    @Override
    public E peek() {
        if (isEmpty()) {
            return null;
        }
        return head.next.value;
    }

    @Override
    public boolean isEmpty() {
        return head == tail;
    }

    @Override
    public boolean isFull() {
        return size == capacity;
    }

    @Override
    public Iterator<E> iterator() {
        return new Iterator<E>() {
            Node<E> p = head.next;
            @Override
            public boolean hasNext() {
                return p != head;
            }
            @Override
            public E next() {
                E value = p.value;
                p = p.next;
                return value;
            }
        };
    }
}
```
## 2.7栈
### 概述
计算机科学中，**stack** 是一种线性的数据结构，只能在其一端添加数据和移除数据。习惯来说，这一端称之为**栈顶**，另一端不能操作数据的称之为**栈底**，就如同生活中的一摞书

### 基础操作
```java
public interface Stack<E> {
    
     * push - 向栈顶压入元素
     * @param value 待压入值
     * @return 压入成功返回 true, 否则返回 false
     
    boolean push(E value);

    
     * pop - 从栈顶弹出元素
     * @return 栈非空返回栈顶元素, 栈为空返回 null
     
    E pop();

    
     * peek - 返回栈顶元素, 不弹出
     * @return 栈非空返回栈顶元素, 栈为空返回 null
     
    E peek();

    
     * isEmpty - 判断栈是否为空
     * @return 空返回 true, 否则返回 false
    
    boolean isEmpty();

    
     * isFull - 判断栈是否已满
     * @return 满返回 true, 否则返回 false
     
    boolean isFull();
}
```

### 链表实现
```java
public class LinkedListStack<E> implements Stack<E>, Iterable<E> {

    private final int capacity;
    private int size;
    private final Node<E> head = new Node<>(null, null);

    public LinkedListStack(int capacity) {
        this.capacity = capacity;
    }

    @Override
    public boolean push(E value) {
        if (isFull()) {
            return false;
        }
        head.next = new Node<>(value, head.next);
        size++;
        return true;
    }

    @Override
    public E pop() {
        if (isEmpty()) {
            return null;
        }
        Node<E> first = head.next;
        head.next = first.next;
        size--;
        return first.value;
    }

    @Override
    public E peek() {
        if (isEmpty()) {
            return null;
        }
        return head.next.value;
    }

    @Override
    public boolean isEmpty() {
        return head.next == null;
    }

    @Override
    public boolean isFull() {
        return size == capacity;
    }

    @Override
    public Iterator<E> iterator() {
        return new Iterator<E>() {
            Node<E> p = head.next;
            @Override
            public boolean hasNext() {
                return p != null;
            }

            @Override
            public E next() {
                E value = p.value;
                p = p.next;
                return value;
            }
        };
    }

    static class Node<E> {
        E value;
        Node<E> next;

        public Node(E value, Node<E> next) {
            this.value = value;
            this.next = next;
        }
    }
}
```

### 数组实现
```java
public class ArrayStack<E> implements Stack<E>, Iterable<E>{
    private final E[] array;
    private int top = 0;

    @SuppressWarnings("all")
    public ArrayStack(int capacity) {
        this.array = (E[]) new Object[capacity];
    }

    @Override
    public boolean push(E value) {
        if (isFull()) {
            return false;
        }
        array[top++] = value;
        return true;
    }

    @Override
    public E pop() {
        if (isEmpty()) {
            return null;
        }
        return array[--top];
    }

    @Override
    public E peek() {
        if (isEmpty()) {
            return null;
        }
        return array[top-1];
    }

    @Override
    public boolean isEmpty() {
        return top == 0;
    }

    @Override
    public boolean isFull() {
        return top == array.length;
    }

    @Override
    public Iterator<E> iterator() {
        return new Iterator<E>() {
            int p = top;
            @Override
            public boolean hasNext() {
                return p > 0;
            }

            @Override
            public E next() {
                return array[--p];
            }
        };
    }
}
```