
## 01 shell 语法

```shell
date +"%F %H:%M:%S" # 获取当前时间
```



### 01 shell脚本格式：

```shell
# 指定该脚本是运行解析由/bin/bash来完成
#!/bin/bash   

###脚本说明信息####
#Author: Bai Shuming
#Created Time: 2018/08/2712:27
#Script Description: first shell study script
```



### 02 shell的运行

```shell
# 脚本运行需要执行权限，当我们给一个文件赋予执行权限后，该脚本就可以运行。
# 700 
chmod u+x filename

# 如果不希望赋予脚本执行权限，那么可以使用bash命令来运行未给予执行权限的脚本bash fiename
bash filename
bash -x filename  # debug 模式运行
```



### 03 shell中的特殊符号

```shell
~:                 家目录    # cd ~ 代表进入用户家目录
-:									 回到上一次的目录 # cd -
!:                 执行历史命令   # !! 执行上一条命令   # history(查看历史操作记录)  !65
+ - * \ %:         对应数学运算  加 减 乘 除 取余数  # expr 2+3  乘号需转义 \*
&:                 后台执行
*:                星号是shell中的通配符  匹配所有
?:                问号是shell中的通配符  匹配除回车以外的一个字符
;：               分号可以在shell中一行执行多个命令，命令之间用分号分割
|：               管道符 上一个命令的输出作为下一个命令的输入   cat filename | grep "abc"
\:                转义字符
' ':              单引号，脚本中字符串要用单引号引起来，但是不同于双引号的是，单引号不解释变量
" ":              双引号，脚本中出现的字符串可以用双引号引起来
```



### 04 shell中的管道运用

**|**   管道符在shell中使用是最多的，很多组合命令都需要通过组合命令来完成输出。管道符其实**就是下一个命令对上一个命令的输出做处理。**
配合**grep**来使用
`cat hello_shell.sh | grep 'h'  ` 



### 05 shell重定向

```shell
>   重定向输入  覆盖原数据  # haha > ./text.txt 
>>  重定向追加输入，在原数据的末尾添加  # # haha >> ./text.txt 
<   重定向输出      wc < text.txt   # wc < text.txt (1 1 6)  
 										wc text.txt 		# # 1 1 6 text.txt
<<  重定向追加输出  fdisk /dev/sdb  <<EOF  ....  EOF
```



### 06 shell数学运算

```shell
 expr 命令：只能做整数运算，格式比较古板，注意空格
         [root@baism ~]# expr 1 + 1
         2
         [root@baism ~]# expr 5 - 2
         3
         [root@baism ~]# expr 5 \* 2  #注意*出现应该转义，否则认为是通配符
         10
         [root@baism ~]# expr 5 / 2
         2
         [root@baism ~]# expr 5 % 2
         1
使用bc计算器处理浮点运算,scale=2代表小数点保留两位
         [root@baism ~]# echo "scale=2;3+100"|bc
         103
         [root@baism ~]# echo "scale=2;100-3"|bc
         97
         [root@baism ~]# echo "scale=2;100/3"|bc
         33.33
         [root@baism ~]# echo "scale=2;100*3"|bc
         300
双小圆括号运算，在shell中(( ))也可以用来做数学运算
         [root@baism ~]# echo $(( 100+3))
         103
         [root@baism ~]# echo $(( 100-3)) 
         97
         [root@baism ~]# echo $(( 100%3))
         1
         [root@baism ~]# echo $(( 100*3))
         300
         [root@baism ~]# echo $(( 100/3))
         33
         [root@baism ~]# echo $(( 100**3))     #开方运算
         1000000
```

### 07 退出脚本

```shell
 exit NUM 退出脚本，释放系统资源，NUM代表一个整数，代表返回值。范围 1-255
```





## 02 shell格式化输出

### 01 基本命令

```shell
# 不换行输出
echo [-n]   # echo -n "date:"; date +%F


# 转义输出
echo [-e]  # echo -e "\a\a\a\a"
```



**转义字符：**

```
\a 发出警告声;

\b 删除前一个字符;

\c 最后不加上换行符号;

\f 换行但光标仍旧停留在原来的位置;

\n 换行且光标移至行首;

\r 光标移至行首，但不换行;

\t 插入tab;

\v与\f相同;

\插入\字符;

\nnn插入nnn(八进制)所代表的ASCII字符;

–help显示帮助–version显示版本信息

```



### 02 颜色代码

`echo -e "\033[背景色;文字颜色m  字符串  \033[属性效果"`

```shell
下面是相应的字和背景颜色，可以自己来尝试找出不同颜色搭配
　　例
　　echo -e "\033[31m 红色字 \033[0m"
　　echo -e "\033[34m 黄色字 \033[0m"
　　echo -e "\033[41;33m 红底黄字 \033[0m"
　　echo -e "\033[41;37m 红底白字 \033[0m"
　　
字颜色：30—–37
　　echo -e "\033[30m 黑色字 \033[0m"
　　echo -e "\033[31m 红色字 \033[0m"
　　echo -e "\033[32m 绿色字 \033[0m"
　　echo -e "\033[33m 黄色字 \033[0m"
　　echo -e "\033[34m 蓝色字 \033[0m"
　　echo -e "\033[35m 紫色字 \033[0m"
　　echo -e "\033[36m 天蓝字 \033[0m"
　　echo -e "\033[37m 白色字 \033[0m"
　　
字背景颜色范围：40—–47
　　echo -e "\033[40;37m 黑底白字 \033[0m"
　　echo -e "\033[41;37m 红底白字 \033[0m"
　　echo -e "\033[42;37m 绿底白字 \033[0m"
　　echo -e "\033[43;37m 黄底白字 \033[0m"
　　echo -e "\033[44;37m 蓝底白字 \033[0m"
　　echo -e "\033[45;37m 紫底白字 \033[0m"
　　echo -e "\033[46;37m 天蓝底白字 \033[0m"
　　echo -e "\033[47;30m 白底黑字 \033[0m"
　　
最后面控制选项说明
　　\033[0m 关闭所有属性
　　\033[1m 设置高亮度
　　\033[4m 下划线
　　\033[5m 闪烁
　　\033[7m 反显
　　\033[8m 消隐
　　\033[30m — \33[37m 
设置前景色
　　\033[40m — \33[47m 设置背景色
　　\033[nA 光标上移n行
　　\033[nB 光标下移n行
　　\033[nC 光标右移n行
　　\033[nD 光标左移n行
　　\033[y;xH设置光标位置
　　\033[2J 清屏
　　\033[K 清除从光标到行尾的内容
　　\33[s 保存光标位置
　　\033[u 恢复光标位置
　　\033[?25l 隐藏光标
　　\033[?25h 显示光标
```



## 03 shell基本输入

read命令

**read 命令选项**

* `-p`打印信息
* `-t`限定时间
* `-s`不回显
* `-n`输入字符个数



```shell
clear
echo "CentOS Linux 7 (Core)"

echo -e "Kernel `uname -r`an `uname -m` \n"

read -p "frone_251 login:" acc
read -s -e  -p "password: " pa
echo ""
echo -e "account: $acc password:$pa"

```



## 04 shell变量



### 01 变量分类

1. 本地变量：用户私有变量，只有本用户可以使用，保存在家目录下的`.bash_profile`、`.bashrc`文件中
2. 全局变量：所有用户都可以使用，保存在`/etc/profile`、`/etc/bashrc`文件中
3. 用户自定义变量：用户自定义，比如脚本中的变量



### 02 定义变量

变量格式： `变量名=值`
**在shell编程中的变量名和等号之间不能有空格。**



### 03 读取变量内容

读取变量内容符:`$`

读取方法：`$变量名`



### 04 取消变量 unset

```shell
[root@www ~]# unset name
[root@www ~]# echo $name
```



### 05 定义全局变量 export

```shell
[root@www ~]# export name='baism'
```

**上述设置的变量其实都是一次性变量，系统重启就会丢失。**
**如果希望本地变量或者全局变量可以永久使用，可以将需要设置的变量写入变量文件中即可。**





### 06 定义永久变量

本地变量：用户私有变量，只有本用户可以使用，保存在家目录下的.bash_profile、.bashrc文件中
全局变量：所有用户都可以使用，保存在`/etc/profile`、`/etc/bashrc`文件中
注意：所有变量前面都加上` export`
重新加载配置文件：`source` 配置文件路径





## 05 数组



### 01 数组的定义

空格间隔元素
`数组名称=(元素1 元素2 元素3 ...)`



### 02 数组读出

索引从0开始
`${数组名称[索引]}`



### 03 数组赋值

**一次赋一个值**

```shell
array[0]='tom'
array[1]='jarry'
array[3]='natasha'
```

**一次赋多个值**

```shell
array=(tom jack alice)

# 将文件中的每一行作为一个元素  ``
array=(`cat /etc/passwd`)

array5=(tom jack alice "bash shell")
```



### 04 查看所有数组

```shell
# 查看所有数组
declare -a

# 展示结果
declare -a PIPESTATUS='([0]="0")'
declare -a array='([0]="a" [1]="b" [2]="c" [3]="d")'
declare -a rray5='([0]="tom" [1]="jack" [2]="alice" [3]="“bash" [4]="shell”")'
```



### 05 访问数组元素

```shell
echo ${array[0]}  # 访问数组中的第一个元素
echo ${array[@]}  或 echo ${array[*]} # 访问数组中所有元素
echo ${#array[@]} # 统计数组元素的个数
echo ${!array[@]} # 获取所有元素索引
echo ${array[@]:1} # 从下标1开始获取全部
echo ${array[@]:1:2} # 从下标1开始获取2个
```



## 06 关联数组

关联数组可以允许用户自定义数组的索引，这样使用起来更加方便、高效。



### 01 定义关联数组 

**注意**是大写A

```shell
declare -A ass_ok
```



### 02 关联数组赋值

**一次赋一个值**

```shell
#  先声明关联数组
declare -A ass_ok

ass_ok[name]='zs'
ass_ok[age]=23

# 结果
declare -A ass_ok='([name]="zs" [age]="23" )'
```



**一次赋多个值**

```shell
#  先声明关联数组
declare -A ass_ok

ass_ll=([name]='zs' [age]=23 [score]=100)

# 结果
declare -A ass_ll='([name]="zs" [age]="23" [score]="100" )'
```



### 03 查看所有关联数组

```shell
declare -A

# 结果
declare -A BASH_CMDS='()'
declare -A ass_ll='([name]="zs" [age]="23" [score]="100" )'
declare -A ass_ok='([name]="zs" [age]="23" )
```



### 04 访问数组元素

```shell
echo ${ass_ll[name]}  # 通过Key获取指定元素
echo ${ass_ll[*]} 或 echo ${ass_ll[@]}  # 获取所有value
echo ${#ass_ll[@]}  # 获取个数
echo ${!ass_ll[@]}  # 获取所有Key
```





## 07 shell中的五大运算

先采用` test 运算逻辑 ; echo $?`   的方式
等于0为真 其他全为假

帮助文档: `man test`

### 01 数学比较运算

```shell
# 运算符解释
-eq         # 等于
-gt         # 大于
-lt         # 小于
-ge         # 大于或等于
-le         # 小于或等于
-ne         # 不等于

# 比如
test 10 -gt 5; echo $?

# 浮点数的运算 乘以10 
NUM=echo "1.5*10"|bc|cut -d"." -f1
```



### 02 字符串比较运算

```shell
# 运算符解释，注意字符串一定别忘了使用引号引起来
==          # 等于    // test "a"=="b";echo $?
!=          # 不等于
-n          # 检查字符串的长度是否大于0    // test -n "a" ; echo $?
-z          # 检查字符串的长度是否为0  // test -z   ; echo $?
```



### 03 文件比较与检查

```shell
-d  检查文件是否存在且为目录
-e  检查文件是否存在
-f  检查文件是否存在且为文件
-r  检查文件是否存在且可读
-s  检查文件是否存在且不为空
-w  检查文件是否存在且可写
-x  检查文件是否存在且可执行
-O  检查文件是否存在并且被当前用户拥有
-G  检查文件是否存在并且默认组为当前用户组
file1 -nt file2  检查file1是否比file2新
file1 -ot file2  检查file1是否比file2旧
```



### 04 逻辑运算

```shell
 &&    # 逻辑与运算
 ||   # 逻辑或运算
  ！ # 逻辑非运算
```



### 05 赋值运算

```shell
  =      赋值运算符         a=10   name='baism'
```



## 08 if语法

坑：
	[] 单个方括号若使用运算符     `&& -> -a `        ` || -o	` 



### 01  单if语句

**适用范围:**只需要一步判断，条件返回真干什么或者条件返回假干什么。



**格式**

```shell

if [ condition ]; then  #condition 值为true or false
	#statements
fi
```

**翻译成汉语**

```shell
假如  条件为真; 那么
    执行statements代码块
结束
```

**例子**

```sh
read -p "请输入成绩:" score

if [ $score -gt 60 ]; then
	echo "及格"
fi
```





### 02  if else语句

**适用范围:**两步判断，条件为真干什么，条件为假干什么。



**格式**

```shell
if [ condition ]; then
    #statements1
else
    #statements2
fi
```

**翻译成汉语**

```shell
假如 条件为真; 那么
     执行statements1代码块
否则
     执行statements2代码块
结束
```

**例子**

```sh
read -p "请输入成绩:" score

if [[ $score -gt 60  ]]; then
	echo "及格"
else
	echo "不及格"
fi
```



### 03  if elif语句

**适用范围:**多于两个以上的判断结果，也就是多于一个以上的判断条件。



**格式**

```shell
if [ condition ]; then
	#statements1
elif [ condition ]; then
	#statements2
elif [ condition ]; then
	#statements3
else
	#statements4
fi

```

**翻译成汉语**

```shell
假如 条件1为真; 那么
     执行statements1代码块
假如 条件2为真; 那么
     执行statements2代码块
假如 条件3为真; 那么
     执行statements3代码块
			.......
否则
     执行statements代码块
结束
```

**例子**

```shell
read -p "请输入成绩:" score

if [ $score -ge 60 -a $score -lt 75 ]; then
	echo "及格"
elif [ $score -ge 75 -a $score -lt 85 ]; then
	echo "优秀"
elif [ $score -ge 85 -a $score -lt 100 ]; then
	echo "非常优秀"
else
	echo "不合格"
fi
```



### 04 条件是数学表达式

条件符号使用双圆括号，可以在条件中植入数学表达式

**格式**

```shell
if ((condition)); then    # condition 是数学表达式
	#statements
fi
```

**例子**

```shell
if (($score > 60)) ;   # 表达式需使用 > < = !=
	echo "及格"
fi
```





### 05 条件中使用通配符 (常用)

使用双方括号,可以在条件中使用通配符

**格式**

```shell
if [[condition]]; then    # condition 是条件表达式  可以使用 && || 运算符
	#statements
fi
```

**例子**

```shell
read -p "请输入字符:" str

if [[ $str == r* ]]; then
	echo "匹配"
fi

```





## 09 for循环



### 01 语法一

**格式**

```shell
for i in words; do
	#statements
done
```

**例子**

```shell
for i in 1 2 3 4 5 6 7; do
	echo $i
done

for i in `seq 1 9`; do
	echo $i
done
```



### 02 语法二

C语言形式的for命令

**格式**

```shell
for ((变量;条件;自增减运算  )); do
	#statements
done
```

**例子**

```shell
for((i=0; i<10; i++)); do
	echo $i
done
```



### 03  语法三

for循环使用多个变量 逗号分隔

**格式**

```shell
for((变量1,变量2; 条件1,条件2; 自增减运算)); do
	#statements
done
```



**例子**

```shell
for((i=0,k=9; i<9,k>0; i++,k--)); do
	echo -e "$i\t$k"
done
```



### 04 语法四

for 无限循环 

**格式**

```shell
for((;;)); do
	#statements
done
```

**例子**

```shell
for((;;)); do
	echo "死循环"
done
```



## 10 循环控制语句



### 01 sleep N

脚本执行到该步休眠N秒

**案例**

```shell
# 判断一个机器是否存活，能ping通就算存活
for (( ;;)); do
	ping -c1 $1 &>/dev/null   # ping一次    不输出
	if [[ $? -eq 0 ]]; then
		echo -e " `date +"%F %H:%M:%S"` : $1 is \033[32m UP \033[0m"
	else
		echo -e " `date +"%F %H:%M:%S"` : $1 is \033[31m Down \033[0m"
	fi

	sleep 5
done

```



### 02 continue

**跳过**循环中的某次循环

**案例**

```shell
# 默认循环输出1-9，但是使用continue跳过输出5
for (( i = 0; i < 10; i++ )); do
	if [[ $i -eq 5 ]]; then
		continue
	fi
	echo $i
done
```



### 03 break



**跳出**循环继续执行后续代码

**案例 01**

```shell
# 默认循环输出1-9，当输出到5的时候跳出循环
for (( i = 0; i < 10; i++ )); do
	if [[ $i -eq 5 ]]; then
		break
	fi
	echo $i
done
```



**跳出**多重循环

**案例 02**

```shell
for (( i = 0; i < 100; i++ )); do
	for (( k = 0; k < 10; k++ )); do
		if [[ $i -eq 50 ]]; then
			break 2  # 指定跳出第几个循环
		fi
		echo "$i $k"
	done
done
```



## 11 while循环

**while 和 for的取舍:**

若不知道循环的次数时采用while循环

通过条件执行循环次数 这时采用for



**格式**

```shell
while [[ condition ]]; do #注意，条件为真while才会循环，条件为假，while停止循环
	#statements
done
```



**案例 01**

```shell
while [[ "$str" != "Q" ]]; do

	read -p "输入Q退出" str

done
```



**案例 02**

```shell
# 多个条件通过 [[条件1]] 逻辑运算符 [[条件2]]  等与时q为假
while [[ "$str" != "Q" ]] && [[ "$str" != "q" ]]; do  
	read -p "输入q退出(不区分大小写)" str
done
```



## 12 until循环

和while正好相反，until是条件为假开始执行，条件为真停止执行。

**语法**

```shell
until [[ condition ]]; do #注意，条件为假until才会循环，条件为真，until停止循环
	#statements
done
```



**案例 01**

```shell
#打印10-20数字
num=10
until [[ $num -gt 20 ]]; do

	echo $num
	num=$((num+1))
	
done
```



**案例 02**

```shell
#用while和until打印1-20数字
num=1
while [[ "$num" -lt 10 ]]; do
	echo $num
	num=$((num+1))

	until [[ "$num" -lt 10 ]]; do
		echo $num
		if [[ "$num" -eq 20 ]]; then
			break
		fi
		num=$((num+1))
	done

done
```



## 13 case条件判断

根据给予的不同条件执行不同的代码块

**语法**

```shell
case 变量  in 
条件1）
           执行代码块1
;;
条件2）
           执行代码块2
;;
......
esac
# 注意：每个代码块执行完毕要以;;结尾代表结束，case结尾要以倒过来写的esac来结束。
```



**案例**

```shell
read -p "请输入数字：" N
case $N in
	1)
echo "haha"
;;
2)
echo "hehe"
;;
*)
echo "1|2"

esac
```



## 14 shell特殊变量

```shell
特殊参数
1. $*: 代表所有参数，其间隔为IFS内定参数的第一个字元
2. $@: 与*星号类同。不同之处在於不参照IFS
3. $#: 代表参数数量
4. $: 执行上一个指令的返回值
5. $-: 最近执行的foreground pipeline的选项参数
6. $$: 本身的Process ID
7. $: 执行上一个背景指令的PID
8. $_: 显示出最後一个执行的命令
9. $N: shell的第几个外传参数
```



**案例**

```shell
echo "脚本的名字:$0" 
echo "获取所有参数: $@"
echo "参数数量:$#"
echo "脚本的执行进程PID:$$"
echo "最后执行的命令:$_"
echo "第二个参数:$2"
```



## 15 函数

return 可有可无

**语法 01**

```shell
函数名 () {
    代码块
    return N
}
```



**语法 02**

```shell
function 函数名 {
      代码块
      return N
}
```



**调用**

```
函数名
```



**案例**

```shell
start(){
	echo "启动中........."
}

function stop {
	echo "关闭中........."
}

stop
start
start
start
```



## 16 正则表达式

```
正则表达式是一种文本模式匹配，包括普通字符（例如，a 到 z 之间的字母）和特殊字符（称为”元字符”）。它是一种字符串匹配的模式，可以用来检查一个字符串是否含有某种子串、将匹配的子串替换或者从某个字符串中取出某个条件的子串。
```

shell支持正则的常用命令: `grep`、`sed`、`awk`



### 特殊字符

定位符使用技巧：**同时锚定开头和结尾，做精确匹配**；单一锚定开头和结尾，做模糊匹配。

```shell

^   # 锚定开头   ^a  以a开头的一个字符
$   # 锚定结尾   a$  以a结尾的一个字符

# 案例
 egrep "^ac$" file  # 以a开头c结尾 (精确匹配 结果为两个字符)
 egrep "^a" file    # 以a开头 (模糊匹配)
 egrep "c$" file    # 以c结尾 (模糊匹配)

```



### 匹配符

匹配字符串

```shell

.   # 匹配除回车以外的任意(一个或多个)字符
()  # 字符串分组   例: (cc)$  cc结尾
[]  # 定义字符类，匹配括号中的一个字符
[^] # 于[] 取反
\   # 转义
|   # 或 

# 案例
egrep "^a.c$" file    # a开头 c结尾 中间任意(一个或多个)字符
egrep "(cc)$" file    # cc结尾的字符串 因为 $ 只能锚定单个字符 多个字符就需要用()来做定义

egrep "^a[a-z0-9]c$" file   # 以a开头c结尾  中间是a-z,0-9  (结果为三个字符)
egrep "^a[^a-z0-9]c$" file  # (中间于上面取反) 以a开头c结尾  中间不是是a-z,0-9  (结果也为三个字符)  

egrep "^a\*b$" file   # (转义) 以a开头b结尾 中间是*号 结果：e*f

egrep "^a.(b|c)$" file  # 以a开头  b或c结尾 (结果为三个字符)

```



### 限定符

对前面的字符或者字符串做限定说明

```shell

*  # 某个字符之后加星号表示该字符 不出现 或 出现多次
？ # 与星号相似，但略有变化，表示该字符 出现一次 或 不出现
+  # 与星号相似，表示其前面字符 出现一次 或 多次，但 必须出现一次
{n,m} # 某个字符之后出现，表示该字符最少n次，最多m次
{m} # 正好出现了m次

# 案例
egrep "^ab*c$" file  # 以a开头 c结尾  中间是 有b 或 没有b (长度不限的字符串)
> ac
> abbc
> abbbc
> abbbbbc
> abc

egrep "^ab?c$" file  # 以a开头 c结尾 中间 只出现一次b 或者 没有b 的字符串 (长度 2 或者 3)
> ac
> abc

egrep "^ab+c$" file  # 以a开头 c结尾 中间是 有b 且 至少出现一次 (长度不限的字符串)
> abbc
> abbbc
> abbbbbc
> abc

egrep "^ab{2,4}c$" file   # 以a开头 c结尾  中间是有 b>=2  b<=4 
egrep "^ab{3}c$" file   # 以a开头 c结尾 中间是有 b==3
egrep "^ab{1,}c$" file  # 以a开头 c结尾 中间是有 b<=1  b>~

```



### POSIX特殊字符

匹配的都是一个字符

```shell
[:alnum:]  # 匹配任意字母字符0-9 a-z A-Z
[:alpha:]  # 匹配任意字母，大写或小写
[:digit:]  # 数字 0-9
[:graph:]  # 非空字符( 非空格控制字符)
[:lower:]  # 小写字符a-z
[:upper:]  # 大写字符A-Z
[:cntrl:]  # 控制字符
[:print:]  # 非空字符( 包括空格)
[:punct:]  # 标点符号
[:blank:]  # 空格和TAB字符
[:xdigit:] # 16 进制数字
[:space:]  # 所有空白字符( 新行、空格、制表符)
```

**案例**

```shell
# 注意[[ ]]  双中括号的意思:  第一个中括号是匹配符[] 匹配中括号中的任意一个字符，第二个[]是格式 如[:digit:]


egrep "^a[[:alnum:]]c$" file    # 以a开头c结尾  中间a-zA-Z0-9任意字符  (结果为三个字符)

egrep "^a[[:alpha:]]c$" file   # 以a开头c结尾  中间是a-zA-Z任意字符  (结果为三个字符)

egrep "^a[[:digit:]]c$" file    # 以a开头c结尾  中间是0-9任意字符  (结果为三个字符)
 
egrep "^a[[:lower:]]c$" file  # 以a开头c结尾  中间是a-z任意字符  (结果为三个字符)

egrep "^a[[:upper:]]c$" file  # 以a开头c结尾  中间是A-Z任意字符 (结果为三个字符)

egrep "^a[[:print:]]c$" file  # 以a开头c结尾  中间是非空任意字符  (结果为三个字符)

egrep "^a[[:punct:]]c$" file  # 以a开头c结尾  中间是符号字符  (结果为三个字符)

egrep "^a[[:blank:]]c$" file  # 以a开头c结尾  中间是空格或者TAB符字符  (结果为三个字符)  结果:a c

egrep "^a[[:space:]]c$" file   # 类型    (结果为三个字符)   结果: a c   

egrep "^a[[:xdigit:]]c$" file   # 以a开头c结尾  中间是十六进制字符  (结果为三个字符)

```



**案例 01**

```shell
# 匹配合法的IP地址

grep ‘^((25[0-5]|2[0-4][[:digit:]]|[01]?[[:digit:]][[:digit:]]?).){3}(25[0-5]|2[0-4][[:digit:]]|[01]?[[:digit:]][[:digit:]]?)$’ —color ip_base
```

**案例 02**

```shell
# 匹配座机电话号码
egrep “^[[:graph:]]{12}$” number |egrep “^(0[1-9][0-9][0-9]?)-[1-9][0-9]{6,7}$”
```



## 17 sed命令

sed是linux中提供的一个外部命令,它是一个行(流)编辑器

* 与文本编辑器区别：
  * 文本编辑器: 编辑对象是文件
  * 行编辑器：**编辑对象是文件中的行**



**语法**

```shell

sed [options] '{command}[flags]' [filename]


options:
		-e  多命令
		-f  文件读取命令
		-n  抑制内存输出
		-i  编辑源文件内容
		-i .bak    修改时同时创建.bak备份文件。
		-r  使用扩展的正则表达式
		!   取反 （跟在模式条件后与shell有所区别）
		
command:
		 a   在匹配后面添加
		 i   在匹配前面添加
		 p   打印
		 d   删除
		 s   查找替换
		 c   替换 列级别
		 y   替换 行级别
		
flags:
		数字    表示新文本替换的模式
		g      表示用新文本替换现有文本的全部实例
		p      表示打印原始的内容
		w filename    将替换的结果写入文件

```



### a   在匹配行 的下一行添加

```shell

a   # 在匹配后面添加   
sed 'a\追加的内容' data1

# 在第二行的下一行追加
sed '2a\append data "haha"' data1

# 在第二行到第四行后的下一行追加
sed '2,4a\append data "haha"' data1

# 字符串匹配追加 (包含 "3 the" 的追加)
sed '/3 the/a\append data "haha"' data1

```



### i   在匹配行 的上一行添加

```shell

i   # 在匹配前面插入  
sed 'i\添加的内容' data1

# 在第二行的下一行插入
sed '2i\insert data "haha"' data1

# 在第二行到第四行后的下一行插入
sed '2,4i\insert data "haha"' data1

# 字符串匹配追加 (包含 "3 the" 的插入)
sed '/3 the/i\insert data "haha"' data1

```



### s   字符替换 列级别

```shell

# 将dog 换成 cat
echo 'tom dog' | sed 's/dog/cat/'

# 将每一行的dog 换成 cat
sed 's/dog/cat/' data1

# 将第二行的dog 换成 cat
sed '2s/dog/cat/' data1

# 将第二行到第四行的dog 换成 cat
sed '2,4s/dog/cat/' data1

# 将字符串匹配的的dog 换成 cat (包含 "3 the" 的替换)
sed '/3 the/s/dog/cat/' data1

```





### y 字符串替换 行级别

**s 和 y区别:**

* y一般是行级别的替换   (替换的是每一字母对应的单个字母) 
* s一般是列级别替换 （替换的是整体,默认替换第一个）

```shell

# 列级别替换
echo "abc abc" | sed "s/abc/aaa/"
> aaa abc

# 行级别替换
echo "abc abc" | sed "y/abc/aaa/"
> aaa aaa
```



### c  修改行

```shell

# 将每一行的数据修改
sed 'c\修改的行数据' data1

# 只修改第二行
sed '2c\change data "haha"'

# 修改第二行到第四行的数据 (注意：是将二四行的数据修改成一个 "change data "haha" 包括回车)
sed '2,4c\change data "haha"' data1

# 修改字符串匹配的行 (包含 "3 the" 的替换)
sed '/3 the/c\change data "data"' data1

```



### d 文件内容删除

```shell

# 删除所有行
sed 'd' data1

# 删除第三行数据
sed '3d' data1

# 删除第三四行数据
sed '3,4d' data1

# 删除字符串匹配的行 (包含 "3 the" 的行删除)
sed '/3 the/d' data1

```



### p 文件内容的查看

```shell

# 打印重复行的原因:打印了指定文件内容一次，又将读入缓存的所有数据打印了一次
# 解决：命令选项-n抑制内存输出即可

# 打印所有行
sed 'p' data1

# 打印第三行
sed '3p' data1

# 打印第二行到第四行的数据
sed '2,4p' data1

# 打印字符串匹配的行 (包含 "3 the" 的打印)
sed '/3 the/p' data1

```



###  -e 多命令

```shell

# 将brown替换为green dog替换为cat
sed -e 's/brown/green/;s/dog/cat/' data1

```



### -f 文件读取命令

```shell

# 将命令写入文件
vim abc
s/brown/green/
s/dog/cat/
s/fox/elephant/

# 调用命令文件
sed -f abc data1 

```



### -n 抑制内存输出

```shell

# 打印第三行到末行  $末行的意思
sed -n '2,$p' data1 

```



### -r 正则表达式

```shell

# 打印 "3 the" 开头的行
sed -n  -r '/^(3 the)/p' data1

```



### -i  修改源文件

以上操作都是在内存中修改的，并没有修改源文件。若要直接修改源文件加上-i 命令选项
备注：-i是一个不可逆的操作,建议使用` -i.bak`   在修改源文件的同时会先备份一个以.bak结尾的源文件

```shell
sed -i.bak 's/brown/green/' data1
```



### 标志 数字

此标志是一个非零正数，默认情况下，执行替换的时候，如果一行中有多个符合的字符串，如果没有标志位定义，那么只会替换第一个字符串，其他的就被忽略掉了，为了能精确替换，可以使用数字位做定义

```shell
# 源
# the quick brown fox jumps over the lazy dog . dog

# 替换 行中的第二处dog为cat
sed 's/dog/cat/2' data2

# 修改后
# the quick brown fox jumps over the lazy dog . cat

```



### 标志 g

选择所有

```shell
# 将data1文件中的所有dog替换为cat
sed 's/dog/cat/g' data2
```



### 标志 p

打印文本内容，类似于 p命令选项

```shell
# 替换第三行 dog 为 cat
sed  -n '3s/dog/cat/p' data2
```



### 标志 w filename

修改的内容存入filename文件

```shell
# 将修改的第三行内容存在了text文件中
sed  '3s/dog/cat/w text' data2
```



### 其他 $=

```shell
# 统计data2有多少行
sed -n '$=' data2

# 打印data2内容时加上行号
sed  '=' data2
```





## 18 awk命令

对输出数据做过滤、提取、运算的命令



### 语法

**注意:** awk 程序脚本由左花括号和右花括号定义。脚本命令必须放置在两个大括号之间。由于awk命令行假定脚是单文本字符串，所以必须将脚本包括在单引号内

```shell

# 运行优先级 
# BEGIN:   在开始处理数据流之前执行，可选项
# program: 如何处理数据流，必选项
# END:     处理完数据流后执行，可选项
awk [options] [BEGIN]{program} [END][file]


```



### 01 对列字段提取



```shell

# 字段内置变量
$0   # 表示整行文本
$1   # 表示文本行中的第一个数据字段
$2   # 表示文本行中的第二个数据字段
$NF  # 表示文本行中的最后一个数据字段

```

```shell

# 读入test把每一行数据打印出来
awk '{print $0}' test 

# 打印test第六个字段
awk '{print $6}' test

# 打印test最后一个字段
awk '{print $NF}' test

```



#### -F 字段间的分隔依据

```shell

# 按照 : 来分隔字段 打印2 3 最后 三列
awk -F ':' '{print $2,$3,$NF }' /etc/passwd 

```



#### -f file 文件读取命令

```shell

# 将命令写入文件
vi  abc 
{print $1,$3,$NF}

# 读取命令
awk -f abc test

```



#### -v 定义变量

```shell
awk -v name='zs' -v age=23 'BEGIN{print name age}'
```



### 02 对行记录提取

两种方式：1 通过行号    2 通过正则匹配

```shell

# 通过行号
awk 'NR==3{print $0}' test 

# 指定行的 第一个字段 精确匹配 字符串为3
awk '$1=="3"{print $0}' test 

```



#### 对字符串提取

通过行 和 列提取指定位置字符串

```shell

# 打印第2行 第五列
awk 'NR==2{print $5}' test 

```



#### 程序的优先级

BEGIN -> PROGRAM -> END

**BEGIN 在不需要数据源情况下可以执行**
而 PROGRAM 和 END 却必须数据源才能执行

```shell

# 不需要数据源 可以直接执行
awk 'BEGIN{print "hello world"}'

# 没有提供数据源 无法执行
awk '{print "hello world"}'
awk 'END{print "hello world"}'

```





### 03 awk高级用法



#### 01 定义数组

**方式:**

`数组名[索引]=值`

```shell
awk 'BEGIN{array[0]=100;array[1]=200;print array[0],array[1]}'
```



#### 02 运算

##### 01 赋值运算

**注意：** 最外层一定要用 单引号 阔起来， 变量赋的值  要用双引号阔起来

```shell

awk -v name='zs' 'BEGIN{print name}'

awk 'BEGIN{name="zs" ;print name}'

awk 'BEGIN{array[0]=100;print array[0]}'

```



##### 02 比较运算

```shell

# 按ascii编码顺序表 返回为真则用1 假为0
awk 'BEGIN{print "a" >= "b" }'
awk 'BEGIN{print "a" <= "b" }'


# 首先过滤 第一列 大于4的  $0打印整行文本
awk '$1>4{print $0}' data1


awk 'BEGIN{print 100 >= 1 }'
awk 'BEGIN{print 100 == 1 }'
awk 'BEGIN{print 100 <= 1 }'
awk 'BEGIN{print 100 < 1 }'
awk 'BEGIN{print 100 != 1 }'

```





##### 03 数学运算

```shell

awk 'BEGIN{print 100+3 }'
awk 'BEGIN{print 100-3 }'
awk 'BEGIN{print 100*3 }'
awk 'BEGIN{print 100/3 }'
awk 'BEGIN{print 100**3 }'
awk 'BEGIN{print 100%3 }'

awk -v 'count=0' 'BEGIN{count++; print count}'
awk -v 'count=0' 'BEGIN{count--;print count}'

```



##### 04 逻辑运算

```shell

awk 'BEGIN{print 100 >= 3 && 100 >= 10}'
awk 'BEGIN{print 100>=2 && 1>=100 }'

awk 'BEGIN{print 100>=2 || 1>=100 }'
awk 'BEGIN{print 100>=200 || 1>=100 }'

```



##### 05 匹配运算

```shell

# : 分隔  第一列以ro开头过滤  $0 打印整行文本
awk -F ':' '$1 ~"^ro" {print $0}' /etc/passwd

# ! 取反
awk -F ':' '$1 !~ "^ro" {print $0}' /etc/passwd

```



#### 03  环境变量



| 变量        | 描述                                          |
| ----------- | --------------------------------------------- |
| FIELDWIDTHS | 指定字符串按照怎么样的宽度进行展示 以空格分隔 |
| FS          | 输入字段分隔符号                              |
| OFS         | 输出字段分隔符号                              |
| RS          | 输入记录分隔符                                |
| ORS         | 输出记录分隔符号                              |

```shell

# FIELDWIDTHS  指定字符串按照怎么样的宽度进行展示 以空格分隔
# 									root: x: 0:0:root
awk 'BEGIN{FIELDWIDTHS="5 2 8"}NR==1{print $1,$2,$3}' /etc/passwd
> root: x: 0:0:root


# FS 指定数据源中字段分隔符，类似命令选项-F
awk 'BEGIN{FS=":"}NR==1{print $1,$3,$NF}' /etc/passwd
> root 0 /bin/bash

# OFS 指定输出到屏幕后字段的分隔符
awk 'BEGIN{FS=":";OFS="-"}NR==1{print $1,$3,$NF}' /etc/passwd
> root-0-/bin/bash

# RS 指定记录的分隔符
awk 'BEGIN{RS=""}{print $1,$2,$3}' num 
> 1 2 3

# ORS 输出到屏幕后记录的分隔符，默认为回车
awk 'BEGIN{RS="";ORS="*"}{print $1,$2,$3}' num 
> 1 2 3*[root@iZb*****

```



#### 04 流程控制



##### 01 if判断语句

```shell
# 单if
awk '{if($1>5)print $0}' num


# if else
awk '{if($1>5)print $1/2;else print $1*2}' num

```



##### 02 for循环语句

```shell

# 测试数据
cat num2
60 50 100
150 30 10
70 100 40

# 将每一行数据累加 打印
awk '{sum=0;for (i=1;i<4;i++){sum+=$i}print sum}' num2

# 分解
awk '{
> sum=0
> for (i=1;i<4;i++) {
>     sum+=$i
> }
> print sum
> }' num2

```



##### 03 while循环语句

```shell

# 测试数据
cat num2
60 50 100
150 30 10
70 100 40

# 每行数据累加 累积大于150停止循环 打印
awk '{sum=0;i=1;while(sum<150){sum+=$i}print sum}' num2

# 分解
awk '{
sum=0
i=1
while (sum<150)
 {
    sum+=$i
}
print sum
}' num2


```



##### 04 do…while循环语句

先执行后判断

```shell

# 每行数据累加 累积大于150停止循环 打印
awk '{sum=0;i=1;do{sum+=$i}while(sum<150);i++;print sum}' num2

# 分解
awk '{
> sum=0
> i=1
> do {
> sum+=$i
> }while (sum<150)
> i++
> print sum
> }' num2

```



##### 05 循环控制语句

```shell

# break
 awk '{
> sum=0
> i=1
> while (i<4){
>    sum+=$i
>    if (sum>150){
>       break
>    }
>    i++
> }
> print sum
> }' num2


```



#### 05 其他

```shell

# 打印行数
awk 'END{print NR}' test

# 打印test文本最后一行内容
awk 'END{print $0}' test 

# 打印test文本列数
awk 'END{print NF}' test 

```

