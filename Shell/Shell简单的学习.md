# 一、基本概念

**Shell**：命令解释器

**脚本规范**
```Bash
#！/bin/bash
# 首行的#指定解释器
# 第二行开始的#为注释

# 严格模式：
# -e（命令返回非零退出码时直接退出）
# -u（引用未定义变量退出）
# -o pipefail（管道中任意一个命令失败，整个管道退出码非零）
set -euo pipefail 
```

**执行方式**
```Bash
bash test.sh      # 使用bash执行

chmod +x test.sh  #赋予执行权限
./test.sh         # 需要先赋予执行权限

source test.sh    # 在当前Shell执行 ，变量会保留
. test.sh         # 等价于source test.sh
#区别理解：source执行相当与在当前shell中输入这几行命令 
#        使用另外两个相当与新开一个命令行执行命令（子进程）不影响父进程
```
# 二、变量

**变量的定义与使用**
```Bash
name="人类"      # 变量赋值=两边不能有空格   
$name           # $获取变量的值
${name}         # $获取变量的值，推荐加{}明确边界
readonly name   # 设置只读不能被取消，删除，修改
unset name      # 删除变量
export name     # 设置为环境变量，可以被子进程读取
local name      # 局部变量，可以限制在函数里生效
```

**特殊变量**
```Bash
$0              # 脚本名
$1~$9、${10}    # 位置参数，脚本运行时传入的参数
$#              # 参数个数
$@              # 所有参数（各自独立）
$*              # 所有参数（单个字符串）
$?              # 上条命令退出码（0 成功）
$$              # 当前进程的PID（进程编号）
$!              # 后台最后进程的PID
```
# 三、字符串与数组

**字符串**
```Bash
str="hello world"
${#str}              # 字符串的长度
${str:n:m}           # 字符串截取n~m的部分的子串
${str/$str1/$str2}   # 字符串中将第一个str1换成str2
${str//$str1/$str2}  # 字符串中将全部的str1换成str2

[ -z "$str" ]        # 判断str为空
[ -n "$str" ]        # 判断str非空
[ "$str1" = "$str2" ]# 判断str1与str2相等
```

**数组**
```Bash
arr=(a b c d)
${arr[i]}         # 第i个元素
${arr[@]}         # 全部元素
${#arr[@]}        # 数组长度
${arr[@]:n:m}     # 截取第n~m个数组
```

# 四、运算符与操作符

**算术运算符**
```Bash
a=1
b=2
((a+b))   # (( ))执行计算并判断真假
$((a+b))  # $(())获取计算结果
let "a+=2"  # let和（（ ））基本一样,可以用""把表达式括起来当成一个整体，如果表达式有空格且没加""会被当成独立的东西
# 运算符在（（ ））中和C语言一样
```

**数值比较**
```Bash
-eq      # 等于
-gt      # 大于
-ge      # 大于等于
-ne      # 不等
-lt      # 小于
-le      # 小于等于
```

**文件**
```Bash
#测试文件状态的操作符
-e       # 判断存在
-d       # 判断是否为目录
-f       # 判断是否为普通文件
-s       # 判断是否非空
-r       # 读权限
-w       # 写权限
-x       # 执行执行权限
```

**逻辑运算**
```Bash
[ $a -gt 5 ] && echo "big"    # 与
[ $a -gt 5 ] || echo "small"  # 或
[ ! -f file ]                 # 非
```

**括号**
```Bash
[ ]      # 测试，条件判断，当前进程执行[]里的命令
[[ ]]    # 增强版[]可以在里面使用&& || 等逻辑运算符
( )      # 启动一个子进程执行()里的命令，返回最后一条命令的状态码
(( ))    # 算术命令不用$就能计算，返回真假、0为假
$(( ))   # 和(())一样，返回计算后的数值
```
# 五、流程控制

**if**
```Bash
if [ "$a" -gt 10 ]; then
    echo "大于10"
elif [ "$a" -eq 10 ]; then
    echo "等于10"
else
    echo "小于10"
fi
```

**case**
```Bash
case "$1" in                                       # 判断$1是否与下面相等
    start)   echo "starting..." ;;                 # $1 == start
    stop|halt) echo "stopping..." ;;               # $1 == stop 或 $1 == halt
    *)       echo "usage: $0 {start|stop}" ;;      # *是通配符
esac
```

**for**
```Bash
for i in 1 2 3; do       # 循环头：in 后面跟着一个列表，用空格隔开, ;相当与换行
	# 循环内容            # 取i分别为列表的值
done                     # 循环结束标志

for ((i=0; i<5; i++)); do
	# 循环内容
done

for i in {1..5}; do      # {}展开1~5
	# 循环内容 
done    # 打印 1 到 5

#特殊用法
for file in *.txt; do         # file遍历当前目录下的.txt文件
    # 循环内容
done

for i in "$@"; do             # i遍历所有参数
    # 循环内容
done
```

**while与until**
```Bash
while [ 条件 ]; do             # 当条件为真时进行循环
	 # 循环内容
done

until [ 条件 ]; do             # 当条件为假时进行循环
    # 循环内容
done
```

**关键字**
```Bash
break        # 退出,break n 可以指定跳出n层循环
continue     # 跳过本次，进行下一次循环
```

# 六、函数

```Bash
hello(){
	local name=$1
	echo "你好，$name"
}
hello "人类"

getadd(){
	echo $(($1+$2))
	echo $(($2-$1))
}
result=$(getadd 2 3)         # 打印的值会存储到变量里

```

# 七、输入输出与重定向

**标准输入输出流**

| 名称     | 文件描述符 | 默认设备 | 作用             |
| ------ | ----- | ---- | -------------- |
| stdin  | 0     | 键盘   | 程序读取数据的位置      |
| stdout | 1     | 终端   | 程序正常运行的结果输出的位置 |
| stderr | 2     | 终端   | 程序报错信息输出的位置    |

**输出重定向**：改变数据流向，把本该输出到屏幕的内容，写到文件里
```Bash
# > 覆盖写入、清空原文件，写入新内容
echo "Hello" > test.txt      # test.txt 现在只有 "Hello"
echo "World" > test.txt      # test.txt 被清空，变成 "World"
# >> 追加写入、保留原文件，在末尾添加
echo "Hello" >> test.txt
echo "World" >> test.txt     # test.txt 里面是hello （换行）world
# （> 和 >> 默认等同于 1> 和 1>>）

# 2> 覆盖写入错误日志
ls /nonexistent_dir 2> error.log 
# 2>> 追加写入错误日志
ls /nonexistent_dir 2> error.log

# &> 、&>> stdout 和 stderr 都重定向到文件

# 丢弃输出，> /dev/null
```

**输入重定向**
```Bash
# < 从文件读取读入
# 本来 grep 需要键盘输入，现在直接从 file.txt 读
grep "error" < file.txt

# <<< 把一个字符串直接输入
grep "root" <<< "user=root"   

# << 在脚本里直接嵌入多行文本作为输入
cat << EOF > config.conf 
host=localhost
port=3306
user=root
EOF
```

**管道**
```Bash
# 把前一个命令的输出直接变成后一个命令的输入
ls -l | grep ".txt" | awk '{print $9}'
```

**echo与printf**
```Bash
echo "Hello"           # 输出
echo -n "Loading..."   # 不换行输出
echo -e "a\tb\nc"      # 启用转义字符
echo 'Hello $name'     # 单引号原样输出
echo "Hello $name"     # 双引号解析变量
echo "x" > file.txt    # 写文件

printf "姓名: %s\n年龄: %d\n" "Tom" 25   # 可移植性更好
```

# 八、Linux常用命令

```Bash
ls                  # 列出目录内容 (ls -lh 详细人类可读, ls -a 显示隐藏)
cd                  # 切换目录 (cd .. 返回上级, cd - 返回上次目录)
pwd                 # 显示当前所在路径
mkdir               # 创建目录 (mkdir -p 递归创建多级)
rm                  # 删除文件或目录
cp                  # 复制文件或目录
touch               # 创建空文件或更新时间戳
find                # 搜索文件
mv                  # 移动文件或重命名

cat                 # 一次性查看整个文件内容
batcat              # 带语法高亮的cat
less / more         # 分页查看大文件
head / tail         # 查看文件头/尾
grep                # 文本搜索 (grep -i 忽略大小写, grep -v 反向过滤)
awk                 # 按列提取文本
wc                  # 统计 (wc -l 统计行数、wc -c 统计字节数 wc -w 统计单词数)
sort                # 排序
uniq                # 去重
echo                # 打印输出文本
diff                # 比较两个文件的差异

ps                  # 查看进程
top                 # 实时监控系统资源与进程状态
kill                # 终止进程
df                  # 查看磁盘空间使用情况
date                # 显示或设置日期时间
history             # 查看历史命令
crontab             # 定时任务管理

ping                # 测试网络连通性 (ping -c 限制次数)
curl                # 发送 HTTP 请求
scp                 # 跨机器复制文件
ss                  # 查看网络端口占用

chmod               # 修改文件权限 (chmod +x script.sh 加执行权限)
chown               # 修改文件所有者 
sudo                # 以管理员执行命令
```

和笔记与Class1里的一样，对Shell语法进行一个简单学习总结，将来有时间系统学习时再在此文件夹下进行系统笔记的补充。