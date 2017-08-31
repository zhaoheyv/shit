---
layout: post
title : Linux Shell
author: 赵何宇
---

> Linux下shell的学习笔记,随学随记,参考了[runoob网站](http://www.runoob.com/linux/linux-shell.html)，和[zhihu](https://www.zhihu.com/question/21281304)

#### shell 变量：
shell变量是由shell程序设置的特殊变量。shell变量中有一部分是环境变量，有一部分是局部变量，这些变量保证了shell的正常运行

|变量 	| 说明 	|
| ---- | ----------------------------------------------------------------- |
| $0 |	当前脚本的文件名 |
| $n |	传递给脚本或函数的参数。n 是一个数字，表示第几个参数。例如，第一个参数是$1，第二个参数是$2。|
| $# |	传递给脚本或函数的参数个数。|
| $* |	传递给脚本或函数的所有参数。|
| $@ |	传递给脚本或函数的所有参数。被双引号(" ")包含时，与 $* 稍有不同。|
| $? |	上个命令的退出状态，或函数的返回值。|
| $$ |	当前Shell进程ID。对于 Shell 脚本，就是这些脚本所在的进程ID。|

#### 关系运算符

 |运算符 	| 说明 | 举例 |
 | ---- | --------------------------------------------------------------- | -------- |
 | -eq 	| 检测两个数是否相等，相等返回 true。 	| [ $a -eq $b ] 返回 true。|
 | -ne 	| 检测两个数是否相等，不相等返回 true。 	| [ $a -ne $b ] 返回 true。|
 | -gt 	| 检测左边的数是否大于右边的，如果是，则返回 true。 	| [ $a -gt $b ] 返回 false。|
 | -lt 	| 检测左边的数是否小于右边的，如果是，则返回 true。 	| [ $a -lt $b ] 返回 true。|
 | -ge 	| 检测左边的数是否大等于右边的，如果是，则返回 true。 	| [ $a -ge $b ] 返回 false。|
 | -le 	| 检测左边的数是否小于等于右边的，如果是，则返回 true 	| [ $a -le $b ] 返回 true。|

#### 布尔运算符

! 非运算

-o 或运算(or)

-a 与运算(and)

#### 字符串 和 字符串运算符

1. 获取字符串长度  ${#var}
2. 查找字符串 
```
animal="dog, pig, cat, lion and so on"     
echo `expr index "$animal" cat`
```
3. = 和 != 判断俩个字符串是否相等;
4. -z 、 -n 或者直接将字符串代入 判断字符串长度是否为零（-z当字符串长度为0返回 true）

#### 文件测试运算符

#### 流程控制

```
if condition
then
    command1 
    command2
    ...
    commandN 
fi

if condition
then
    command1 
    command2
    ...
    commandN
else
    command
fi

if condition1
then
    command1
elif condition2 
then 
    command2
else
    commandN
fi

for var in item1 item2 ... itemN
do
    command1
    command2
    ...
    commandN
done

while condition
do
    command
done

until condition
do
    command
done

case 值 in
模式1)
    command1
    command2
    ...
    commandN
    ;;
模式2）
    command1
    command2
    ...
    commandN
    ;;
esac
```
