#Shell脚本

### 简介
`Shell`脚本（Shell Script）是一种为`Shell`编写的脚本程序。而`Shell`是一个用`C`语言编写的程序，它是用户使用`Linux`的桥梁。在`Linux`中`Shell`程序又有很多种，如：
- sh - 即 Bourne Shell。sh 是 Unix 标准默认的 shell。
- bash - 即 Bourne Again Shell。bash 是 Linux 标准默认的 shell。
……

其中，bash由于易用和免费，被广泛使用。每种`Shell`程序都有其对应的`解释器`，在脚本文件第一行会告诉系统当前程序属于那种类型，如：

```c
#!/bin/bash
echo "这是bash类型的shell程序"
```

本文主要介绍bash类型的`Shell`脚本的基本使用。

### Shell脚本权限
`Shell`脚本被执行前需要可执行权限。如刚创建的`test.sh`脚本，需`chmod +x test.sh`方能执行：

```c
qincji:giteeblog mac$ ./test.sh
-bash: ./test.sh: Permission denied
qincji:giteeblog mac$ chmod +x test.sh 
qincji:giteeblog mac$ ./test.sh 
Hellow Shell
```

注：执行脚本文件的命令使用`sh`或`.`，如`sh test.sh`或`./test.sh`。

### Shell基本语法
#### 注释
- 单行注释 - 以 `#` 开头，到行尾结束。
- 多行注释 - 以 `:<<EOF` 开头，到 `EOF` 结束。

如编辑`test.s`h脚本文件

```c
#!/bin/bash     
echo "Hellow Shell"
#echo "使用#单行注释了，不能输出"
   
:<<EOF
echo "EOF里面111"
echo "EOF里面222" 
EOF  
```

执行`sh test.sh`查看输出：

```c
qincji:giteeblog mac$ sh test.sh 
Hellow Shell
```

#### echo输出
`echo`用于字符串的输出。编辑`test.sh`进行介绍：

```c
#!/bin/bash                   
echo "1-You" #1.输出普通字符串
echo "2-You \"are\"" #2.输出使用\转义
age=18
echo "3-You \"are\" ${age}" #3.使用${变量名}输出包含变量的字符串
echo "4-You \"are\" "$age"" #4.使用"$变量名"输出包含变量的字符串"
echo -e "5-YES\nNO" #5.使用 -e 开启（对\n）转义
echo "6-test" > test.txt #6.输出重定向至文件
```

执行后输出结果：

```c
qincji:giteeblog mac$ ./test.sh 
1-You
2-You "are"
3-You "are" 18
4-You "are" 18
5-YES
NO
qincji:giteeblog mac$ cat test.txt
6-test
```

#### 变量
Bash 中没有数据类型，bash 中的变量可以保存一个数字、一个字符、一个字符串等等。定义变量名的规则：
- 1.命名只能使用英文字母，数字和下划线，首个字符不能以数字开头。
- 2.中间不能有空格，可以使用下划线（_）。
- 3.不能使用标点符号。
- 4.不能使用 bash 里的关键字（可用 help 命令查看保留关键字）。

如，编辑`test.sh`如下：

```c
#!/bin/bash
#有效变量名
var="0 字符串"
VAR=1
_var=2
_var2=3
_3var=4
#以下为无效变量名
4var=5
?var=6
var*2=7
#使用表达式命令赋值，输出看看命令的结果
files=`du -sh .`
echo ${files}

#删除变量
unset var
echo ${var}
```

执行输出结果：

```c
qincji:giteeblog mac$ ./test.sh 
./test.sh: line 9: 4var=5: command not found
./test.sh: line 10: ?var=6: command not found
./test.sh: line 11: var*2=7: command not found
138M .
```

#### 字符串
使用`'`或者`"`来定义字符串，也可以不用引号。但是`'`中不能使用变量和转义符。所以记住用`"`就行了。字符串常规操作，有：
- 1.字符串拼接，格式：`'hello，'${串}''`或`"hello，${串}"`。
- 2.获取字符串长度，格式：`${#串}`。
- 3.截取字符串，格式`${串:2:3}`，其中2为起点，3为截取的个数。

编辑`test.sh`如下：

```c
#!/bin/bash
name="123456"
full1='1-hello，'${name}'' #1.单引号字符串拼接
full2="1-hello，${name}" #1.双引号字符串拼接
echo ${full1}
echo ${full2}
echo "2-长度：${#name}"  #2.获取字符串长度
echo "3-截取：${name:2:3}"  #3.截取字符串
```

执行输出结果：

```c
qincji:giteeblog mac$ ./test.sh 
1-hello，123456
1-hello，123456
2-长度：6
3-截取：345
```

#### 数组
Bash只支持一维数组，下标从 0 开始，下标可以是整数或算术表达式，其值应大于或等于 0。数组常规操作，有：
- 1.创建数组，格式：`array=(value0 value1 value2)`或指定下标`array=([1]=value0 [1]=value1 [2]=value2)`。
- 2.访问元素，格式：获取单个：`"${array[下标]}"`；获取所有：`"${array[@]}"`。
- 3.取得个数，格式：`"${#array[@]}"`或`"${#array[*]}"`。
- 4.增加元素，格式：`array=(value3 "${array[@]}" value4)`。
- 5.删除元素，格式：`unset array[0]`。

编辑`test.sh`如下：

```c
#!/bin/bash
array=([1]=1 [0]="a" [3]="￥")  #1.创建数组
echo "2-访问：v1=${array[1]}，v2=${array[@]}" #2.访问元素
echo "3-获取：len1=${#array[@]}，len2=${#array[*]}" #3.取得个数
array=("NO" "${array[@]}" 33) #4.增加元素
echo "4-增加：${array[@]}"
unset array[0] #5.删除元素
echo "5-删除：${array[@]}"
```

执行输出结果：

```c
qincji:giteeblog mac$ ./test.sh 
2-访问：v1=1，v2=a 1 ￥
3-获取：len1=3，len2=3
4-增加：NO a 1 ￥ 33
5-删除：a 1 ￥ 33
```

#### 运算符

##### 算术运算符
- \+ - * / %（加减乘除取余），格式：`expr value0 符号 value1`，特殊符号需要转义，如`expr 3 \* 4`结果为12。
- =（赋值），格式：`value=value1`，把value1赋值给value。
- ==（比较相等），格式：`[ value1 == value2 ]`，如`[ 2 == 5 ]`，返回false。
- !=（比较不相等），格式：`[ value1 != value2 ]`，如`[ 2 != 5 ]`，返回true。
注意：`[ value1 != 'value2' ]`是带空格的，如错误写法：`[value1!='value2']`。在循环体测试中使用`[]`不起作用，需要用`(())`，如：

```c
a=1
while (($a < 10)); do
# if [ a == 2 ] #不起作用
  if ((a == 2)); then
    echo "while语句：continue"
    let "a++"
    continue
  elif ((a == 4)); then
    echo "while语句：break"
    break
  fi
  echo "while语句：$a"
  let "a++"
done
```

编辑`test.sh`如下：

```c
#!/bin/bash
a=3
b=3
o1=`expr ${a} \* ${b}`
echo "o1=${o1}"
if [ $a == $b ]
then
   echo "a 等于 b"
else
   echo "a 不等于 b"
fi
```

执行输出结果：

```c
qincji:giteeblog mac$ ./test.sh 
o1=9
a 等于 b
```

##### 关系运算符
通用格式为：`[ value1 符号 value2 ]`，用于比较结构中，同上`==`和`!=`用法一样。
- -eq 是否相等，格式：`[ value1 -eq value2 ]`，如`[ 2 -eq 5 ]`，返回false。
- -ne 是否不相等，格式：同上。
- -gt >，格式：同上。
- -lt <，格式：同上。
- -ge >=，格式：同上。
- -le <=，格式：同上。

##### 布尔运算符
- ! 非，格式：`[ ! 表达式 ]`。与表示式结果相反。。如`[ ! 1 == 1 ]`返回 false。
- -o 或 (与逻辑运算符`||`相同)，格式：`[ 表达式1 -o 表达式2 ]`。有一个表达式为 true 则返回 true。如`[ 1 == 1 -o 2 != 2 ]`返回 true。
- -a 与 (与逻辑运算符`&&`相同)，格式：`[ 表达式1 -o 表达式2 ]`。两个表达式都为 true 才返回 true。如`[ 1 == 1 -a 2 != 2 ]`返回 false。

##### 逻辑运算符
`||`和`&&`参考`布尔运算符`。

##### 字符串运算符
- = 字符串相等，格式：`[ value1 = value2 ]`，如a="a",b="b"，`[ ${a} = ${b} ]`，返回true。
- != 字符串不相等，格式：`[ value1 != value2 ]`，如a="a",b="b"，`[ ${a} != ${b} ]`，返回false。
- -z 字符串长度为0为true，格式：`[ -z value ]`，如a="a"，`[ -z ${a} ]`，返回false。
- -n 字符串长度不为0为true， 格式：`[ -n = value ]`，如a="a"，`[ -n = ${a} ]`，返回true。

##### 文件运算符
通用格式为：`[符号 文件路径 ]`，用于检查文件的属性，如何符合为true。
- -d 目录存在为true，格式：`[ -d filePath ]`，如`[ -d "./" ]`，为true。
- -f 文件存在为true，格式：`[ -f filePath ]`，如`[ -f "./test.sh" ]`，为true。
- -r 可读为true，格式：同上。
- -w 可写为true，格式：同上。
- -x 可执行 可执行为true，格式：同上。
- -s 文件不为空(有内容) true，格式：同上。
- -e 文件/目录存在，格式：同上。

编辑`test.sh`如下：

```c
#!/bin/bash
if [ -d "./" ]
then
   echo "1-输出为true"
else
   echo "1-输出为false"
fi
if [ -w "./test.sh" ]
then
   echo "2-输出为true"
else
   echo "2-输出为false"
fi
```

执行输出结果：

```c
qincji:giteeblog mac$ ./test.sh 
1-输出为true
2-输出为true
```

#### 流程控制
- 条件语句：
①`if..elif..else`，使用`then..fi`来控制命中的范围。单行格式：`if [ 条件1 ]; then 执行体1; elif [ 条件2 ]; then 执行体2; else 执行体2; fi`，多行格式：直接`;`去掉即可。
②`case`，在`exec..esac`范围内。格式如下

```c
exec
case 输入 in
条件1) 执行体1;;
条件2) 执行体2;;
*) 其他执行体;;
esac
```

- 循环语句：`for`、`while`和`until`。循环体在`do..done`范围内，在循环中遇到`continue`会进入下个循环，遇到`break`跳出循环。

编辑`test.sh`如下：

```c
#!/bin/bash
#1.注意单行的写法需要用 ； 把语句段落隔开
if [ "111" = "abc" ]; then
  echo "输出if-1"
elif [ "abc" = "abc" ]; then
  echo "输出elif-1"
else
  echo "输出else-1"
fi
#2.注意单行的写法需要用 ； 把语句段落隔开
if [ "111" = "abc" ]; then echo "输出if-2"; elif [ "abc2" = "abc" ]; then echo "输出elif-2"; else echo "输出else-2"; fi

#3.case语句
exec
case "4" in
"a") echo "case输出a" ;;
"b") echo "case输出b" ;;
*) echo "case输出*" ;;
esac

#4.for语句
for var in "red" 2 "yes"; do echo "for语句：${var}"; done

#5.while语句
a=1
#while [ $a -le 10 ]
while (($a < 10)); do
  # if [ a == 2 ] #不起作用
  if ((a == 2)); then
    echo "while语句：continue"
    let "a++"
    continue
  elif ((a == 4)); then
    echo "while语句：break"
    break
  fi
  echo "while语句：$a"
  let "a++"
done

#6.until语句
until (($a >= 6)); do
  echo "until语句：$a"
  let "a++"
done
```

执行输出结果：

```c
qincji:giteeblog mac$ ./test.sh 
输出elif-1
输出else-2
case输出*
for语句：red
for语句：2
for语句：yes
while语句：1
while语句：continue
while语句：3
while语句：break
until语句：4
until语句：5
```

#### 函数
编辑`test.sh`如下：

```javascript
#!/bin/bash
#function关键字可省了，fun_name随便定的函数名称。
function fun_name(){
    echo $0 #$0：文件名称
    echo $1 #$1：传递的第一个参数
    echo $2 #$2：传递的第二个参数
}
fun_name 1 "Yes"
```

执行输出结果：

```c
qincji:giteeblog mac$ ./test.sh 
./test.sh
1
Yes
```

### Shell其他关键词
#### `eval`
语法：eval cmdLine，eval会对后面的cmdLine进行两遍扫描，如果在第一遍扫面后cmdLine是一个普通命令，则执行此命令；如果cmdLine中含有变量的间接引用，则保证简介引用的语义。示例如下：
编辑`test.sh`如下：

```c
#!/bin/bash
#文件传递的长度，输出最后一个数
function test() {
    echo "\$$#"
    #这才是我们想要的结果
    eval echo "\$$#"
}
test 11 22 33 44
```

执行输出结果：

```c
qincji:giteeblog mac$ ./test.sh 
$4
44
```

#### `shift`
语法：shift number，位置参数可以用shift命令左移。比如shift 3表示原来的$4现在变成$1，原来的$5现在变成$2等等，原来的$1、$2、$3丢弃，$0不移动。不带参数的shift命令相当于shift 1。示例如下：
编辑`test.sh`如下：

```c
#!/bin/bash
#文件传递的长度，输出最后一个数
function test() {
  until [ $# -eq 0 ]; do
    echo "第一个参数为: $1 参数个数为: $#"
    shift 2
  done
}
test 11 22 33 44 55 66
```

执行输出结果：

```c
qincji:giteeblog mac$ ./test.sh 
第一个参数为: 11 参数个数为: 6
第一个参数为: 33 参数个数为: 4
第一个参数为: 55 参数个数为: 2
```


参考
- https://www.cnblogs.com/jingmoxukong/p/7867397.html