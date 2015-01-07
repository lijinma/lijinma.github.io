---
layout: post
title: "感受异或的神奇"
date: 2014-05-29 06:26
comments: true
categories: 
---
![异或](/images/post/xor.png)
##什么是异或？
Wikipedia的解释：
>在逻辑学中，逻辑算符异或（`exclusive or`）是对两个运算元的一种逻辑析取类型，符号为 XOR 或 EOR 或 ⊕（编程语言中常用`^`）。但与一般的逻辑或不同，异或算符的值为真仅当两个运算元中恰有一个的值为真，而另外一个的值为非真。转化为命题，就是：“两者的值不同。”或“有且仅有一个为真。”
<!--more-->
定义：
> 1 ⊕ 1 = 0

> 0 ⊕ 0 = 0

> 1 ⊕ 0 = 1

> 0 ⊕ 1 = 1

真值表：

| Y      |     B = 0 |   B = 1   |
| :-------- | :-------:| :------: |
| A = 0    |   0 |  1  
| A = 1    |   1 |  0  

表达式：

Y = A' · B + A · B'

>解释：我使用`·`作为`与`，我使用`+`作为`或`，我使用`'`作为`否`(本来应该使用`头上一横`，但是太难编辑了，就使用了`'`）；

##异或有什么特性？

根据定义我们很容易获得`异或`两个特性：

>恒等律：`X ⊕ 0 = X`
>归零律：`X ⊕ X = 0`

然后我们使用`真值表`可以证明：

（1）交换律
```
A ⊕ B = A' · B + A · B'

B ⊕ A = B' · A + B · A'
```

因为`·与`和`+或`两个操作满足交换律，所以：

A ⊕ B = B ⊕ A

（2）结合律
```
(A ⊕ B) ⊕ C

= (A' · B + A · B') ⊕ C

= (A' · B + A · B')' · C + (A' · B + A · B') · C '

= ((A' · B)' · (A · B')')· C + A' · B · C ' + A · B' · C '

= ((A + B') · (A' + B))· C + A' · B · C ' + A · B' · C '

= (A · B + A' · B') · C + A' · B · C ' + A · B' · C '

= A · B · C + A' · B' · C + A' · B · C ' + A · B' · C '

```
你可以使用同样推导方法得出（请允许我偷懒一下，数学公式敲起来不容易 +_+）：
```
A ⊕ (B ⊕ C)

= A · B · C + A' · B' · C + A' · B · C ' + A · B' · C '
```

证明过程中使用了如下几个方法（`·与` `+或` `'否`）：

`·与` `+或`交换律： 
```
A · B = B · A

A + B = B + A
```

`·与` `+或`结合律：
```
(A · B) · C = A · (B · C)

(A + B) + C = A + (B + C)　
```

`·与` `+或`分配律：
```
A · (B + C)= A · B + A · C

A + B · C = (A + B) · (A + C)
```
摩尔定理：
```
(A · B)' = A' + B'

(A + B)' = A' · B'
```
结论：

>交换律：`A ⊕ B = B ⊕ A`
>结合律：`A ⊕ (B ⊕ C) = (A ⊕ B) ⊕ C`

有了`归零率`和`结合律`，我们就可以轻松证明：

>自反：`A ⊕ B ⊕ B = A ⊕ 0 = A`

可能这些特性会很顺其自然的理解，但是如果你在解决问题的时候，你可能会忘记异或的这些特性，所以适当的应用可以让我们加深对异或的理解；
```
A ⊕ 1 = A';
A ⊕ 0 = A;
A ⊕ A = 0;
A ⊕ A' = 1;
```

##异或有什么神奇之处（应用）？

>说明：以下的的`异或`全部使用符号`^`

可能你已经被乱七八糟的公式和演算搞的有点烦了，不就是很简单的异或运算吗？还解释的那么复杂，嘿嘿，不要着急，打好了基础，你就站在了巨人的肩膀，让我们开始异或的神奇之旅吧；

###（1）快速比较两个值
先让我们来一个简单的问题；判断两个int数字a，b是否相等，你肯定会想到判断`a - b == 0`，但是如果判断`a ^ b == 0`效率将会更高，但是为什么效率高呢？就把这个给你当家庭作业吧，考虑下减法是如何实现的；
让我们看看ipv6中的比较；
```c
static inline int ipv6_addr_equal(const struct in6_addr *a1, const struct in6_addr *a2)
    {
    return (((a1->s6_addr32[0] ^ a2->s6_addr32[0]) |
        (a1->s6_addr32[1] ^ a2->s6_addr32[1]) |
        (a1->s6_addr32[2] ^ a2->s6_addr32[2]) |
        (a1->s6_addr32[3] ^ a2->s6_addr32[3])) == 0);
    }
```
###（2）在汇编语言中经常用于将变量置零：`xor a，a`；

###（3）我们可以使用`异或`来使某些特定的位翻转，因为不管是0或者是1与1做异或将得到原值的相反值；
0 ^ 1 = 1

1 ^ 1 = 0

例如：翻转`10100001`的第6位，
答案：可以将该数与`00100000`进行按位异或运算;`10100001 ^ 00100000 = 10000001`

我们给出一段常用的代码：
```
unsigned int a, b, mask = 1 << 6;
a = 0xB1; // 10100001
b = a ^ mask; /* flip the 6th bit */
```
###（4）我们使用`异或`来判断一个二进制数中1的数量是奇数还是偶数
例如：求`10100001`中1的数量是奇数还是偶数；
答案：`1 ^ 0 ^ 1 ^ 0 ^ 0 ^ 0 ^ 0 ^ 1 = 1`,结果为`1`就是奇数个1，结果为`0`就是偶数个1；
应用：这条性质可用于奇偶校验（Parity Check），比如在串口通信过程中，每个字节的数据都计算一个校验位，数据和校验位一起发送出去，这样接收方可以根据校验位粗略地判断接收到的数据是否有误

###（5）校验和恢复

校验和恢复主要利用的了异或的特性：`IF a ^ b = c THEN a ^ c = b `
应用：一个很好的应用实例是`RAID5`，使用3块磁盘（A、B、C）组成`RAID5`阵列，当用户写数据时，将数据分成两部分，分别写到磁盘A和磁盘B，`A ^ B`的结果写到磁盘C；当读取A的数据时，通过`B ^ C`可以对A的数据做校验，当A盘出错时，通过`B ^ C`也可以恢复A盘的数据。

RAID5的实现比上述的描述复杂多了，但是原理就是使用 异或，有兴趣的同学看下[RAID5](http://baike.baidu.com/view/645826.htm?fromId=969385)

###（6）经典题目：不使用其他空间，交换两个值
```
a = a ^ b;
b = a ^ b; //a ^ b ^ b = a ^ 0 = a;
a = a ^ b;
```
这个题目就不用解释了吧，太大众题目了，哈哈，但是非常好的使用的了`异或`的特性；

### （7）面试题：互换二进制数的奇偶位；
**题目**：写一个宏定义，实现的功能是将一个int型的数的奇偶位互换，例如6的2进制为`00000110`，(从右向左)第一位与第二位互换，第三位与第四位互换，其余都是0不需要交换，得到`00001001`，输出应该为9；

**思路**：我们可以把我们的问题分为三步（难道这也是分治法吗 -。-），第一步，根据原值的偶数位获取到目标值的奇数位，并把不需要的位清零；第二步，根据原值的奇数位获取到目标值的偶数位，并把不需要的位清零；第三步：把上述两个残缺的目标值合并成一个完整的目标值；

代码为：
```c++
//假设 int 占两个字节，16位；
#include<iostream>
#include<string>
using namespace std;
#define N(n) ((n<<1)&(0xAAAA))|((n>>1)&(0x5555))
void main(){
    int k = N(6);
    cout << k << endl;
}
```
**解释**：
1.为简化说明，我们以4位二进制码为例，0xAAAA 我们用 1010 代替；0x5555 我们用 0101 代替；
2.(n<<1)&(1010) 把n先左移1位，再与1010做与运算，只保留移位之后的偶数位的值，奇数位全为0，实际上是只保留了n的奇数位的值，并把它们交换到了偶数位上。比如 n = 0110 , n<<1 = 1100, (n<<1) & 1010 = 1000 ;
3.(n>>1)&(0101)
把n右移一位，再与 0101 做与运算，只保留移位之后的奇数位的值，偶数位全为0，实际是只保留n 的偶数位的值，并把它们交换到对应的奇数位上。n = 0110； n>>1 = 0011； (n>>1) & 0101 = 0001；
4.最后做或运算（相加），得到1001。

### （7）最最常出现的面试题：一个整型数组里除了N个数字之外，其他的数字都出现了两次，找出这N个数字；
比如，从`{1, 2, 3, 4, 5, 3, 2, 4, 5}`中找出单个的数字： `1`

让我们从最简单的，找一个数字开始；

**题目**：（LeetCode 中通过率最高的一道题）
Single Number: Given an array of integers, every element appears twice except for one. Find that single one.
Note:Your algorithm should have a linear runtime complexity. Could you implement it without using extra memory?
**思路**：
拿到这个题目，本能的你会使用排序（数字文字我们常常需要排序），排序后可以来判断是否数字成对出现，思路很明显，但是排序的算法上限是 O(nlogn)，不符合题目要求；

学习了强大的`异或`，我们可以轻松的使用它的特性来完成这道题目：
（1）A ^ A = 0;
（2）异或满足交换律、结合律；
所有假设有数组：`A B C B C D A`
使用异或：
```
A ^ B ^ C ^ B ^ C ^ D ^ A
= A ^ A ^ B ^ B ^ C ^ C ^ D
= 0 ^ 0 ^ 0 ^ D
= 0 ^ D
= D
```
是不是很神奇？时间复杂度为`O(n)`，当然是线性的，空间复杂度`O(1)`；

**代码**：
```c++
class Solution {
public:
    int singleNumber(int A[], int n) {  
        //特殊情况1,2  
        if(n<=0) return -1;  
        if(n==1) return A[0];  
  
        int result = 0;
        for (int i = 0; i < n; i ++) {
            result = result ^ A[i];
        }
        return result;
    }  
};
```

接下来让我们增加一些难度：

**题目**：一个整型数组里除了`两`个数字之外，其他的数字都出现了两次。请写程序找出这两个只出现一次的数字？

**思路**：
**第一步**：肯定还是像我们上面的解法一样，所有数进行`异或`，不过最终得到的结果是 a 和 b（假设 a 和 b 是落单的数字）两个值的异或结果 aXORb，没有直接得到 a 和 b 的值；

**第二步**：想办法得到 a 或者 b，假设 aXORb 为 `00001001`（F肯定不为0），根君 aXORb 的值我们发现，`值为1的位`（比如从右向左第一位）表示在此位上 a 和 b 的值不同；所以，根据这个特点，我们找出来所有第一位为1的数进行异或，得到的就是 a 或者 b；

**第三步**：aXORb = a ^ b，假设我们已经找到了 a，根据`异或`特性，我们知道，b = aXORb ^ a；这样我们就可以找出 b；所以我们只需要循环两次； 

这样我们的时间复杂度是 O(n)，空间复杂度是 O(1)
**代码**：
```c++
#include <iostream>
#include <assert.h>
using namespace std;

int getFirstOneBit(int num) //输出 num 的低位中的第一个 1 的位置  
{  
    return num & ~(num - 1);  // num 与 -num 相与找到
}

void findTwo(int *array, int length){
    int aXORb = 0;
    int firstOneBit = 0;
    int a = 0;
    int b = 0;
    for (int i = 0; i < length; i++) {
        aXORb ^= array[i];
    }
    assert(aXORb != 0); //保证题目要求，有两个single的数字
    firstOneBit = getFirstOneBit(aXORb);
    for (int i = 0; i < length; ++i) {
        if(array[i] & firstOneBit) {
            a ^= array[i];
        }
    }
    b = aXORb ^ a;
    cout << "a: " << a << endl; 
    cout << "b: " << b << endl;
}


int main()
{
    int array1[] = {2, 5, 8, 2, 5, 8, 6, 7};
    findTwo(array1, 8);
    return 0;
}


```

接下来让我们再增加一些难度：

**题目**：一个整型数组里除了`三`个数字之外，其他的数字都出现了两次。请写程序找出这两个只出现一次的数字？

**思路**：

**第一步**：肯定还是像我们上面的解法一样，所有数进行`异或`，不过最终得到的结果是 a、b 和 c（假设 a、b 和 c 是落单的数字）三个值的异或结果 aXORbXORc，没有直接得到 a、b 和 c 的值；

**第二步**：想办法得到 a、b 和 c 中的一个，让偶们把问题简化一下；

`假设一个数组中有3个不同的数字 a、b 和 c，已知 aXORbXORc = a ^ b ^ c ，求 a、b 和 c 。`

思路：
1. 根据题目 
aXORbXORc ^ a = b ^ c;
aXORbXORc ^ b = a ^ c;
aXORbXORc ^ c = a ^ b;
因为：(b ^ c) ^ (a ^ c) ^ (a ^ b) = 0;
所以：(aXORbXORc ^ a) ^ (aXORbXORc ^ b) ^ (aXORbXORc ^ c) = 0;

2. 下一步是关键：
假设 X ^ Y ^ Z = 0，则 X Y Z 三个数的低位第一位为1的位置两个相同，一个不同；
比如 X: 00001000, Y: 00000100, Z: 00001100
Y和Z的低位第一位都是00000100， X的低位第一位是00001000；
这一步可以使用倒推法证明：
已知：三个数的低位第一位为1的位置有三种情况，一种就是全相同，一种就是两个不同，一个不同，一种就是三个不同；
（1）如果是全相同，则 X ^ Y ^ Z != 0 (1 ^ 1 ^ 1 = 1)，与前提X ^ Y ^ Z = 0矛盾，不成立；
（2）如果三个不同，则 X ^ Y ^ Z != 0 (1 ^ 0 ^ 0 = 1)，与前提X ^ Y ^ Z = 0矛盾，不成立；
所以结果是：两个不同，一个不同

3. (aXORbXORc ^ a) ^ (aXORbXORc ^ b) ^ (aXORbXORc ^ c) = 0; 所以三个数(aXORbXORc ^ a)、(aXORbXORc ^ b) 和 (aXORbXORc ^ c) 的低位第一位为1的位置两个相同，一个不同；那么我们获取到这三个数的低位第一位为1的位置后，进行异或并取低位第一位为1的位置，就可以找到三个中“一个不同”的低位第一位为1的位置，假设这个值为 firstOneBit。

4. 遍历这三个数(aXORbXORc ^ a)、(aXORbXORc ^ b) 和 (aXORbXORc ^ c)，如果发现某个数异或 aXORbXORc 等于 firstOneBit，这个数就是“一个不同”的那个数；

5. 找到了一个数，剩下的两个数，我们就可以通过上面的方法找出来；

**第三步**：完成了第二步的简化题，我们回到我们的问题，我们的问题比简化的问题多了一个成对的干扰数据，我们可以使用异或要去除干扰数据（记住，我们这个题目都是用异或i去除干扰数据的）；

这样我们的时间复杂度还是 O(n)，空间复杂度是 O(1)

代码如下：
```c++
#include <iostream>
#include <assert.h>
using namespace std;

int getFirstOneBit(int num) //输出 num 的低位中的第一个 1 的位置  
{  
    return num & ~(num - 1);  // num 与 -num 相与找到
}

void findTwo(int *array, int length){
    int aXORb = 0;
    int firstOneBit = 0;
    int a = 0;
    int b = 0;
    for (int i = 0; i < length; i++) {
        aXORb ^= array[i];
    }
    assert(aXORb != 0); //保证题目要求，有两个single的数字
    firstOneBit = getFirstOneBit(aXORb);
    for (int i = 0; i < length; ++i) {
        if(array[i] & firstOneBit) {
            a ^= array[i];
        }
    }
    b = aXORb ^ a;
    cout << "a: " << a << endl; 
    cout << "b: " << b << endl;
}

int findOne(int *array, int length) {
    int aXORbXORc = 0;
    int c = 0;
    int firstOneBit = 0;
    for (int i = 0; i < length; ++i) {
        aXORbXORc ^= array[i];
    }

    for (int i = 0; i < length; ++i) {
        firstOneBit ^= getFirstOneBit(aXORbXORc ^ array[i]); //使用异或会排除掉不相干的元素
    }
    // firstOneBit = getFirstOneBit(a ^ b) ^ getFirstOneBit(a ^ c) ^ getFirstOneBit(b ^ c);

    firstOneBit = getFirstOneBit(firstOneBit); //获取到最低位下面要用

    for (int i = 0; i < length; ++i) {
        if (getFirstOneBit(aXORbXORc ^ array[i]) == firstOneBit) {
            c ^= array[i]; //使用异或会排除掉不相干的元素
        }
    }
    cout << "c: " << c << endl;
    return c;
}

int main()
{
    int array1[] = {2, 5, 8, 2, 5, 8, 6, 7, 1};
    int c = findOne(array1, 9);
    int array2[] = {2, 5, 8, 2, 5, 8, 6, 7, 1, c}; //为了更好重用函数，我重新定义了一个数组让大家理解
    findTwo(array2, 10);
    return 0;
}


```

写这篇文档参考了《离散数学与应用》课本，参考了别人多个博客，如果我参考了你的博客，但没有注明出处，请联系告知，有错误的地方，希望可以指出来，也希望大家有更多的补充，非常感谢。


参考：

[http://zh.wikipedia.org/wiki/%E9%80%BB%E8%BE%91%E5%BC%82%E6%88%96](http://zh.wikipedia.org/wiki/%E9%80%BB%E8%BE%91%E5%BC%82%E6%88%96)

[http://yjq24.blogbus.com/logs/41863963.html](http://yjq24.blogbus.com/logs/41863963.html)

[http://wzw19191.blog.163.com/blog/static/131135470200992610551971/](http://wzw19191.blog.163.com/blog/static/131135470200992610551971/)

[http://kapok.blog.51cto.com/517862/129941](http://kapok.blog.51cto.com/517862/129941)

[http://blog.csdn.net/huxian370/article/details/8024416](http://blog.csdn.net/huxian370/article/details/8024416)

[http://www.cnblogs.com/Ivony/archive/2009/07/23/1529254.html](http://www.cnblogs.com/Ivony/archive/2009/07/23/1529254.html)

[http://blog.chinaunix.net/uid-20937170-id-3407361.html](http://blog.chinaunix.net/uid-20937170-id-3407361.html)

[http://blog.csdn.net/yfkiss/article/details/11775569](http://blog.csdn.net/yfkiss/article/details/11775569)

[http://blog.sina.com.cn/s/blog_88c9ddc50101810p.html](http://blog.sina.com.cn/s/blog_88c9ddc50101810p.html)

[http://blog.csdn.net/pathuang68/article/details/7567027](http://blog.csdn.net/pathuang68/article/details/7567027)

[http://blog.csdn.net/qingen1/article/details/12656763](http://blog.csdn.net/qingen1/article/details/12656763)