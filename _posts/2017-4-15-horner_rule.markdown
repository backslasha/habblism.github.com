---
layout: post
title:  "霍纳算法和二进制幂"
date:   2017-4-15 23:08:17 +0800
categories: algorithms
description: new to githubPage
---


# 霍纳算法和二进制幂

标签（空格分隔）： 算法

## 霍纳法则
《 算法设计与分析基础 》第六章 6.5 节 —— 霍纳法则是一个计算多项式的值的高效算法

例：求多项式 p(x)=2x<sup>4</sup>-x<sup>3</sup>-3x<sup>2</sup>+x-5  在 x=3 时的值
<pre>仔细观察多项式的这种形式
    p(x) = 2x<sup>4</sup>-x<sup>3</sup>-3x<sup>2</sup>+x-5
         = x(2x<sup>3</sup>-x<sup>2</sup>+3x<sup>1</sup>+1)-5
         = x(x(2x<sup>2</sup>-x+3)+1)-5
         = x(x(x(2x-1)+3)+1)-5
这就是霍纳法则，也叫秦九韶算法
</pre>
- 使用一个两行的表来说明使用霍纳算法的过程
    - 第一行包含了多项式的每个系数，由次数低到高排列
    - 第二行只保留最高次数项的系数
    
<table>
    <tr>
        <th>系数</th>
        <td>-5</td>
        <td>1</td>
        <td>3</td>
        <td>-1</td>
        <td>2</td>
    </tr>
     <tr>
        <th>x = 3</th>
        <td></td>
        <td></td>
        <td></td>
        <td></td>
        <td>2</td>
    </tr>
</table>

- 从最高项的下一项开始，每一项的结果为 **x** 乘以 **上一项单元格中的结果** 加上 **本项的系数**，以此规则填充完表格的第二行:

<table>
    <tr>
        <th>系数</th>
        <td>-5</td>
        <td>1</td>
        <td>3</td>
        <td>-1</td>
        <td>2</td>
    </tr>
     <tr>
        <th>x = 3</th>
        <td>3 * 55 + （-5）= 160</td>
        <td>3 * 18 + 1 = 55</td>
        <td>3 * 5 + 3 = 18</td>
        <td>3 * 2 +（-1）= 5</td>
        <td>2</td>
    </tr>
</table>

- 填充完毕后，表格第二行中最后一项求得的数据就是多项式的结果

**伪码**
<pre>
    算法 Horner(P[0..n])
    // 用霍纳法则求一个多项式在一个给定点的值
    // 输入：一个 n 次多项式的系数数组 P[0..n](从低到高储存)，以及一个数字 x
    // 输出： 多项式在 x 处的值
    p ← P[n]
    for i ← n-1 downto 0 do
        p ← x * p + P[i]
    return p
</pre>

## 二进制幂
直接计算 a<sub>n</sub> 次方的效率是 O(n)，使用二进制幂的算法可以提高效率.

### 从左至右二进制幂(从高到低位)
###### 1. 用二进制表示整数 n，b<sub>i</sub> 为 0 和 1
<pre>
设 n = b<sub>I</sub> ... b<sub>i</sub> ... b<sub>0</sub> = b<sub>I</sub>2<sup>I</sup> + ... + b<sub>i</sub>2<sup>i</sup> + ... + b<sub>0</sub> 

则 a<sup>n</sup> = a<sup>b<sub>I</sub>2<sup>I</sup> + ... + b<sub>i</sub>2<sup>i</sup> + ... + b<sub>0</sub></sup>

记 p(2) = b<sub>I</sub>2<sup>I</sup> + ... + b<sub>i</sub>2<sup>i</sup> + ... + b<sub>0</sub>
</pre>
###### 2. 理解用霍纳法则计算 p(2) 的步骤对于 a<sup>p(2)</sup> 的意义
<table>
    <tr >
        <th>用霍纳法则计算 p(2)</th>
         <th>对 a<sup>p(2)</sup> 的意义</th>
    </tr>
     <tr>
        <td>
            <pre>
p ← 1 // 当 n>=1 时，二进制的第一个数字总是 1
for i ← I-1 downto 0 do 
    p ← 2p + b<sub>i</sub>
            </pre>
        </td>
        <td>
        <pre>
a<sup>p</sup> = a<sup>1</sup>
for i ← I-1 downto 0 do 
    a<sup>p</sup> ← a<sup>2p + b<sub>i</sub></sup>
            </pre>
        </td>
    </tr>
</table>
###### 3.  根据 n 的二进制串计算 a<sub>n</sub> 的值
 因为 b<sub>i</sub> 的值取 1 或者 0，所以在计算 a<sup>p</sup> ← a<sup>2p + b<sub>i</sub></sup> 时，需要分两种情况：  
 
- a<sup>p</sup> = a<sup>2p + b<sub>i</sub></sup> =  (a<sup>p</sup>)<sup>2</sup> * a<sup>b<sub>i</sub></sup> = (a<sup>p</sup>)<sup>2</sup>     （当 b<sub>i</sub> = 0）  
- a<sup>p</sup> = a<sup>2p + b<sub>i</sub></sup> =  (a<sup>p</sup>)<sup>2</sup> * a<sup>b<sub>i</sub></sup> = (a<sup>p</sup>)<sup>2</sup> * a（当 b<sub>i</sub> = 1）
<br/>  
###### 4. 因此，给定 n 的二进制串，我们可以这样求得 a<sup>n</sup> 的结果，间接利用了霍纳法则
<pre>
    算法 LeftRightBinaryExponentiation(a,b(n))
    // 用从左至右二进制幂算法计算 a<sup>n</sup>
    // 输入：一个数字 a 和 它的指数 n 的二进制位列表 b(n)：b<sub>0 </sub>~ b<sub>I</sub>
    // 输出：a<sup>n</sup> 的值
    product ← a // product 代表的是 a<sup>p</sup>
    for i ← I-1 downto 0 do
        product ← product * product
        if b<sub>i</sub> == 1
            product ← product * a
    return product
</pre>
###### 5. 算法效率
本算法的每一次循环都要做一到两次乘法，记中的乘法次数为 M(n) ，
则 I-1 <= M(n) <= 2(I-1)，I 为二进制串的长度
因为 I = ⌊log<sub>2</sub>n⌋+1，所以上面的二进制幂算法的效率是对数级的，而直接计算 a 的 n 次方的蛮力法为线性级。
###从右至左二进制幂
以上的二进制幂其实叫做“从左至右的二进制幂算法”，借助了霍纳法则，实际上还有一种和霍纳法则似乎没什么关联的从右至左的二进制幂算法来计算 a<sup>n</sup> 
###### 1. 同样用二进制表示整数 n，b<sub>i</sub> 为 0 和 1
<pre>
设 n = b<sub>I</sub> ... b<sub>i</sub> ... b<sub>0</sub> = b<sub>I</sub>2<sup>I</sup> + ... + b<sub>i</sub>2<sup>i</sup> + ... + b<sub>0</sub> 

则 a<sup>n</sup> = a<sup>b<sub>I</sub>2<sup>I</sup> + ... + b<sub>i</sub>2<sup>i</sup> + ... + b<sub>0</sub></sup> = a<sup>b<sub>I</sub>2<sup>I</sup></sup> * ... * a<sup>b<sub>i</sub>2<sup>i</sup></sup>  * ... * a<sup>b<sub>0</sub></sup>
</pre>

###### 2.  根据 n 的二进制串计算 a<sub>n</sub> 的值
 因为 b<sub>i</sub> 的值取 1 或者 0，所以：  
 
- a<sup>b<sub>i</sub>2<sup>i</sup></sup> =  a<sup>2<sup>i</sup></sup>  （当 b<sub>i</sub> = 1）  
- a<sup>b<sub>i</sub>2<sup>i</sup></sup> =  1（当 b<sub>i</sub> = 0）

所以在 a<sup>b<sub>I</sub>2<sup>I</sup></sup> * ... * a<sup>b<sub>i</sub>2<sup>i</sup></sup>  * ... * a<sup>b<sub>0</sub></sup> 中，只需要考虑 b<sub>i</sub> = 1 的项
###### 3. 伪代码
<pre>
    算法 RightLeftBinaryExponentiation(a,b(n))
    // 用从右至左二进制幂算法计算 an
    // 输入：一个数字 a 和 它的指数 n 的二进制位列表 b(n)：b<sub>0</sub> ~ b<sub>I</sub>
    // 输出：an 的值
    term ← a // term 代表 a<sup>2<sup>i</sup></sup>
    if b<sub>0</sub>=1 product ← a
    else product ← 1
    for i ← 1 to I do
        term ← term * term 
        if b<sub>i</sub>=1 product ← product * term
    return product
</pre>
###### 4. 算法效率
显然从右至左二进制幂算法的效率也是对数级的。
