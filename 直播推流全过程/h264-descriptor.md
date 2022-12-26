#H.264描述符

### 简述

H264/AVC文档中存在着大量元素描述符ue(v)，me(v)，se(v)，te(v)等，编码为ue(v)，me(v)，或者 se(v) 的语法元素是指数哥伦布编码，而编码为te(v)的语法元素是舍位指数哥伦布编码。这些语法元素的解析过程是由比特流当前位置比特开始读取，包括非0 比特，直至leading_bits 的数量为0。由于解码过程中需要计算出每个元素的占位(bit数量)，所以我们就不得不理解这些描述符了。

### ue(v)

无符号整数指数哥伦布码编码的语法元素，左位在先。计算公式：

​	codeNum = 2 ^leadingZeroBits^ − 1 + read_bits( leadingZeroBits )

- **codeNum：** 占的位数（bit数量）
- **leadingZeroBits：** 遇到第一个1前面0的个数；如：0010 1011，leadingZeroBits的值为2；
- **read_bits( leadingZeroBits )：** 遇到第一个1后面leadingZeroBits个组成的无符号二进制值；如：0010 1011，值为...0 1...，即01，即1；

举两个栗子：

（1）0001 1001 =>leadingZeroBits== 000. ....= 3，read_bits( 3 )==.... 100.=4，所以：codeNum=2^3^-1+4=13
（2）0000 0101  0000 0000 => leadingZeroBits=5，read_bits( 5 ) = .... ..01 000. ....=8，codeNum=2^5^-1+8=39

### se(v)

有符号整数指数哥伦布码编码的语法元素位在先。在按照上面ue(v)公式计算出codeNum后，然后使用该计算公式：

value = (−1)^k+1^ Ceil( k÷2 )

- **Ceil：** 返回大于或者等于指定表达式的最小整数，如： Ceil(1.5)= 2

如上例（1）0001 1001 => codeNum=2^3^-1+4=13，value = (-1)^13+1^Ceil(13÷2)=Ceil(6.5)=7

### me(v)

映射的指数哥伦布码编码的语法元素，左位在先。在按照上面ue(v)公式计算出codeNum后，然后查表。（在H.264-AVC-ISO_IEC_14496-10的9.1.2 章节中表9-4）

### te(v)

舍位指数哥伦布码编码语法元素，左位在先。明确取值范围在0-x；当x>1时，te(v)就是ue(v)；否则(x=1)，b = read_bits( 1 )，codeNum = !b

### 其他

- **ae(v)：** 上下文自适应算术熵编码语法元素
- **b(8)：** 任意形式的8比特字节。
- **f(n)：** n位固定模式比特串（由左至右），左位在先。
- **i(n)：** 使用n比特的有符号整数。
- **u(n)：** n位无符号整数。

### 参考

(1).[H.264-AVC-ISO_IEC_14496-10](http://103.23.20.16/srs/trunk/doc/H.264-AVC-ISO_IEC_14496-10.pdf)

(2).https://blog.csdn.net/lizhijian21/article/details/80982403