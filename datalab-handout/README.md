# 题解
## 1.1bitXor
```c
/* 
 * bitXor - x^y using only ~ and & 
 *   Example: bitXor(4, 5) = 1
 *   Legal ops: ~ &
 *   Max ops: 14
 *   Rating: 1
 */
int bitXor(int x, int y) {
  return ~((~x)&(~y)) & ~(x&y);
}
```
要求使用 **～** 和 **&**来实现异或运算，通过得摩根定律可得。第一题十分easy。

## 1.2tmin
```c
/* 
 * tmin - return minimum two's complement integer 
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 4
 *   Rating: 1
 */
int tmin(void) {
  
  return 1<<31;

}
```
对题意的理解，找出二进制下补码的最小数。
对于最高位代表符号位的表示，当全部位都为1的时候，整个数字为-1，而当最高为1其他都为0则是补码的最小数。

## 2.1 isTmax
```c
/*
 * isTmax - returns 1 if x is the maximum, two's complement number,
 *     and 0 otherwise 
 *   Legal ops: ! ~ & ^ | +
 *   Max ops: 10
 *   Rating: 1
 */
int isTmax(int x) {
  return (!((~(x+1))^x)) & (!!((x+1) ^ 0x0));
}
```
如果是**最大值**返回1，**其余所有情况**返回0。
开始上强度了 哭
### 如何判断最大？
对于一个有符号位整数，很容易知道当最高位为0，其余位全部为1的时候，得到最大值。
### 有什么性质可以利用？
当对最大数01111……进行+1后按位取反会得到011111……与原本一样的值。此时的 x 和 ~(x+1)进行异或运算可得=>
> x^(~(x+1)) = 0
而对于其他值得到都是1，除了**-1**这个数。
### 特殊情况 -1
x等于-1时，即11111……。当我们对他进行+1并取反，会得到11111……。
可以发现，当x=-1的时候，此时对x与~(x+1)的异或得到的也是0，而这并不是预期的答案。
### 解决方案
考虑逻辑取反**！**的作用，对于任何非零数，进行两次逻辑取反都可以得到*1*，而对于0进行两次取反仍然为*0*。
因此可以利用这个性质，与 x+1 后的值进行两次逻辑取反判断，如果为0，则排除了特殊情况。

## 2.2 allOddBits
```c
/* 
 * allOddBits - return 1 if all odd-numbered bits in word set to 1
 *   where bits are numbered from 0 (least significant) to 31 (most significant)
 *   Examples allOddBits(0xFFFFFFFD) = 0, allOddBits(0xAAAAAAAA) = 1
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 12
 *   Rating: 2
 */
int allOddBits(int x) {
  int A = 0xA;  // 四位
  int AA = A | (A << 4); // 八位
  int AAAA = AA | (AA << 8); // 十六位
  int temp = AAAA | (AAAA << 16);
  return !((x & temp) ^ temp);
}
```
题意在讲如果所有奇数位都设置为1，那么返回1，其他任何情况返回0.
### 何处下手
根据他给的范例：(0xAAAAAAAA) = 1，对于0xA的二进制表示有0b1010，可以发现A即满足，奇数位都为1，其余位都为0。
先从4位开始研究，如果对于给定任意一个四位的数，如何判断他是否满足？
### 忽略偶数位
偶数位实际上并不起作用，起到类似“罩子的作用”，可以先将x与A进行按位与运算。
> x & 0xA
得到后的结果偶数位全部为**0**，奇数位保持原本的**1**或**0**。
此时再对A进行一次异或运算，如果为0便是符合的。
> (x&0xA) ^ 0xA
### 扩展到32位
刚才所说的都是在4位的情况，扩展到32位便主要在于如何表示这个32位的A。通过位运算，先扩展到8位，再到十六位，最后再到32位。

## 2.3 negate
```c
/*    
 * negate - return -x 
 *   Example: negate(1) = -1.
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 5
 *   Rating: 2
 */
int negate(int x) {
  return 2;
}
```
求负数，按补码取反➕1，简简单单。

## 3.1 isAsciiDigit
```c
//3
/* 
 * isAsciiDigit - return 1 if 0x30 <= x <= 0x39 (ASCII codes for characters '0' to '9')
 *   Example: isAsciiDigit(0x35) = 1.
 *            isAsciiDigit(0x3a) = 0.
 *            isAsciiDigit(0x05) = 0.
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 15
 *   Rating: 3
 */
int isAsciiDigit(int x) {
  int t1 = x >> 6;
  int t2 = x >> 4;
  int t3 = (x & 0xF) - 0xA;
  return (!(!!t1)) && (!(t2 ^ 0b11)) && !!(t3 >> 31);
}
```
先将给定的两个范围转化为2进制：
0x30 == 0b00110000   0x39 == 0b00111001
一、可以观察到，对于四个字节的int类型，除去最后**六位**，前面所有的高位都是0.
二、在**后六位**中，可以发现前两个都是1，同理判断。
三、判断位于*0000*至**1001**。
### 第一次判断
先判断前26位是否全部为零，可以先将x右移6位。
如果x=0，则 !!x=0
如果x!=0，则 !!x=1
### 第二次判断
右移四位，和0b11进行异或判断。
### 第三次判断
将x减去A，因为十六进制中9下一个为A，如果得到的结果小于0那么位于这个范围内。可以根据最高位的符号位右移来判断。
如果为正，则为0，如果为负，则为1.

## 3.2 conditional
```c
/* 
 * conditional - same as x ? y : z 
 *   Example: conditional(2,4,5) = 4
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 16
 *   Rating: 3
 */
int conditional(int x, int y, int z) {
  int mask = ((!!x) << 31) >> 31;
  return (mask & y) | (~mask & z);
}
```
### 转换为逻辑表达
不管给定任意x都可以通过！！来变为1或者0。
与y、z进行位判断，可以先将刚才得到的结果先左移31位，后再右移31位。
如果是0，那么右移保持逻辑右移，全部为0。如果是1，右移是算数右移，全部为1.
此时，再与y，z进行位判断。

## 3.3 isLessOrEqual
```c
/* 
 * isLessOrEqual - if x <= y  then return 1, else return 0 
 *   Example: isLessOrEqual(4,5) = 1.
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 24
 *   Rating: 3
 */
int isLessOrEqual(int x, int y) {
  // x == y
  int cond1 = !(x^y);

  int signX = x >> 31;
  int signY = y >> 31;

  // x+ y-
  int cond2 = !((!signX) & signY);
  // x- y+
  int cond3 = signX & (!signY);
  // xy同号
  int res = (x+(~y)+1) >> 31;
  return cond1 || (cond2 && (cond3 || res));
}
```
判断是否小于等于
### 判断等于
异或运算并取反来判断 !(x^y)
### x为负y为正
判断符号位，x >> 31, y >> 31, signX & !signY
### xy同号
因为不能用减号，但减号等价于 x + (~y)+1
之后再判断符号位 

## 4.1 logicalNeg
```c
//4
/* 
 * logicalNeg - implement the ! operator, using all of 
 *              the legal operators except !
 *   Examples: logicalNeg(3) = 0, logicalNeg(0) = 1
 *   Legal ops: ~ & ^ | + << >>
 *   Max ops: 12
 *   Rating: 4 
 */
int logicalNeg(int x) {
  return 2;
}
```
通过其他所有的逻辑符号来实现**！**符号
方法：可以发现除去0以外任意一个数和它的负数的符号位相或都可以取到1，而0依然位0。由此可以来实现

## 4.2 howManyBits
```c
/* howManyBits - return the minimum number of bits required to represent x in
 *             two's complement
 *  Examples: howManyBits(12) = 5
 *            howManyBits(298) = 10
 *            howManyBits(-5) = 4
 *            howManyBits(0)  = 1
 *            howManyBits(-1) = 1
 *            howManyBits(0x80000000) = 32
 *  Legal ops: ! ~ & ^ | + << >>
 *  Max ops: 90
 *  Rating: 4
 */
int howManyBits(int x) {
  // 先判断0
  int isZero = !x;
  // 对x不管正负进行统一处理 这时候负数的判断也转变为了和正数一样
  int flag = x >> 31;
  int mask = ((!!x)<<31)>>31;
  x = ((~flag) & x) | (flag & (~x));
  int bits_16,bits_8,bits_4,bits_2,bits_1,bits_0;
  bits_16 = (!((!!(x >> 16)) ^ 0b1)) << 4;
  x >>= bits_16; 
  bits_8 = (!((!!(x >> 8)) ^ 0b1)) << 3;
  x >>= bits_8; 
  bits_4 = (!((!!(x >> 4)) ^ 0b1)) << 2;
  x >>= bits_4; 
  bits_2 = (!((!!(x >> 2)) ^ 0b1)) << 1;
  x >>= bits_2; 
  bits_1 = (!((!!(x >> 1)) ^ 0b1));
  x >>= bits_1; 
  bits_0 = x;
  // 加的1 为符号位
  int res = bits_16 + bits_8 + bits_4 + bits_2 +bits_1 + bits_0 + 1;
  return isZero | (mask & res);
}
```
**写到目前个人感官最难的一题**
### 如何找正数的最小位数？
找到最高位的1的位数然后加上符号位1位。
### 如何找到负数的最小位数？
不难发现例如 1101 和 101代表的数值都是-3。所以负数是要找最高位的0加上符号位。
<hr>
由此，每次按照一半进行划分，如果在高半找到了最高位的1，那么就可以不用管低半。