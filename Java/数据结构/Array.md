# Array 数组

## 简介
### 特性
数组代表一系列对象或者基本数据类型，所有相同的类型都封装到一起，采用一个统一的标识符名称。  
Java 中数组是一种引用类型,数组的定义和使用需要通过方括号 []。数组是用来存储固定大小的同类型元素。  

数组的优点:  
+ 存取速度快,java 中，数组是一种效率最高的存储和随机访问对象引用序列的方式。

数组的缺点:  
+ 事先必须知道数组的长度
+ 插入删除元素很慢
+ 空间通常是有限制的
+ 需要大块连续的内存块
+ 插入删除元素的效率很低 


### java 数组和内存
数组对象（这里可以看成一个指针）存储在栈中，数组元素存储在堆中。当 JVM 执行 new String[] 时，才会在堆中开辟相应的内存区域。数组对象 array 可以视为一个指针，指向这块内存的存储地址。

### 创建数组
> int[] array1 = new int[2]; // 指定数组维度  
> int[] array2 = new int[] { 1, 2 }; // 不指定数组维度  
> int[] array2 = { 1, 2 };

数组维度：  
创建数组时，指定的数组维度可以有多种形式：  
* 数组维度可以是整数、字符。(当指定的数组维度是字符时，Java 会将其转为整数。如字符 a 的 ASCII 码是 97。)
* 数组维度可以是整数型、字符型变量。
* 数组维度可以是计算结果为整数或字符的表达式  

综上,Java 数组的数组维度可以是常量、变量、表达式，只要转换为整数即可
``` java
public class ArrayDemo3 {
    public static void main(String[] args) {
        int length = 3;
        // 放开被注掉的代码，编译器会报错
        // int[] array = new int[4.0];
        // int[] array2 = new int["test"];
        int[] array3 = new int['a'];
        int[] array4 = new int[length];
        int[] array5 = new int[length + 2];
        int[] array6 = new int['a' + 2];
        // int[] array7 = new int[length + 2.1];
        System.out.println("array3.length = [" + array3.length + "]");
        System.out.println("array4.length = [" + array4.length + "]");
        System.out.println("array5.length = [" + array5.length + "]");
        System.out.println("array6.length = [" + array6.length + "]");
    }
}
// Output:
// array3.length = [97]
// array4.length = [3]
// array5.length = [5]
// array6.length = [99] 
```

数组维度的大小： 数组维度并非没有上限的，数组过大，可能会导致栈溢出。

## 其他
### 数组 array[++b] 与 array [b++]
* array [b++]
    > int b = 0;  
    array[b++] = 2;  

    b的值本身自增1，存储的位置是索引0

*  array[++b]
    > int b = 0;  
    array[++b] = 2;  

    b的值本身自增1，存储的位置是索引1


### 二维数组的一维表示
对于一个行数为 m，列数为 n，行列下标都从 0 开始编号的二维数组，我们可以通过下面的方式，将其中的每个元素 (i, j)(i,j) 映射到整数域内，并且它们按照行优先的顺序一一对应着 [0, mn) 中的每一个整数。形象化地来说，我们把这个二维数组「排扁」成了一个一维数组。映射关系为:
> $$ (i,j) \rightarrow i×n+j  

同样地，我们可以将整数 xx 映射回其在矩阵中的下标，即:  
> $$ 
\begin{cases}
 i=x / n \\ j=x % n
\end{cases}

```java
public int[][] matrixReshape(int[][] nums, int r, int c) {
        int m = nums.length;
        int n = nums[0].length;
        if (m * n != r * c) {
            return nums;
        }

        int[][] ans = new int[r][c];
        for (int x = 0; x < m * n; ++x) {
            ans[x / c][x % c] = nums[x / n][x % n];
        }
        return ans;
    }
```


