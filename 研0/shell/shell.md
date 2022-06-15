## 简单的shell脚本

**第一个shell脚本**
```shell {.line-numbers}
#!/bin/bash
echo "Hello World !"
```
通过上面的这个简单的例子，我们来说一下一个shell脚本的怎么写。
- 第一行中，`#!`是一个约定的标记，它用于告诉系统这个脚本应该使用什么解释器来运行。
- 第二行中，`echo`用于输出

**运行shell脚本**
将上面的代码保存为`helloworld.sh`，并使用`cd`进入相应的目录：
> chmod +x ./helloworld.sh #使脚本获得权限
> ./helloworld.sh #运行脚本

注意，一定要写成`./helloworld.sh`，而不是直接`helloworld.sh`。因为他与`cd`等命令不同，直接使用`helloworld.sh`,系统会进入PATH中去查找有没有一个叫做`helloworld.sh`,只有当你的命令在PATH中时，你才可以像`cd`一样直接使用。`./helloworld.sh`就是告诉系统，在当前目录来查找命令。
**使用变量**
运行 shell 脚本中的单个命令自然有用，但这有其自身的限制。通常你会需要在 shell 命令使用其他数据来处理信息。这可以通过变量来实现。变量允许你临时性地将信息存储在 shell 脚本中，以便和脚本中的其他命令一起使用。在此之前，我们需要了解一下变量。
- 环境变量
  所有的程序，包括shell启动的程序，都能访问环境变量，有些程序需要环境变量来保证其正常运行。必要的时候shell脚本也可以定义环境变量。
  Linux系统的环境变量会在后面详细解释。 
- 用户变量
  局部变量在脚本或命令中定义，仅在当前shell实例中有效，其他shell启动的程序不能访问局部变量。
- shell变量
  shell变量是由shell程序设置的特殊变量。shell变量中有一部分是环境变量，有一部分是局部变量，这些变量保证了shell的正常运行

下面我们来写一些程序：


```shell {.line-numbers}
#!/bin/bash
echo "User info for userid: $USER" #若为单引号包裹则不会变更$USER的值
echo UID: $UID
echo HOME: $HOME
```
`$USER`、`$UID` 和`$HOME` 环境变量用来显示已登录用户的有关信息。脚本输出如下：
```shell {.line-numbers}
User info for userid: zbs
UID: 1001
HOME: /home/zbs
```

---
当然，你还可以使用${variable}形式引用的变量。变量名两侧额外的花括号通常用来帮助识别美元符后的变量名。例如：
```shell {.line-numbers}
for skill in Ada Coffe Action Java; do
    echo "I am good at ${skill}Script"
done
```
如果不给`skill`变量加花括号，写成`echo "I am good at $skillScript"`，解释器就会把`$skillScript`当成一个变量（其值为空），代码执行结果就不是我们期望的样子了。

---
在双引号中，我们使用`$`来获得变量中值，下面来看另一个例子：
> echo "The cost of the item is $15"
> The cost of the item is 5

显然这不是我们想要的。只要脚本在引号中出现美元符，它就会以为你在引用一个变量。在这个例子中，脚本会尝试显示变量$1（但并未定义），再显示数字 5。要显示美元符，你必须在它前面放置一个反斜线。
> echo "The cost of the item is \$15"
> The cost of the item is $15

---
shell脚本还允许在脚本中定义和使用自己的变量。
用户变量可以是任何由字母、数字或下划线组成的文本字符串，长度不超过20个。用户变量区分大小写，所以变量`Var1`和变量`var1`是不同的。使用等号将值赋给用户变量。在变量、等号和值之间不能出现空格。这里有一些给用户变量赋值的例子。
```shell {.line-numbers}
var1=10
var2=-57
var3=testing
var4="still more testing"
```
shell 脚本会自动决定变量值的数据类型。在脚本的整个生命周期里，shell 脚本中定义的变量会一直保持着它们的值，但在 shell 脚本结束时会被删除掉。

与系统变量类似，用户变量可通过美元符引用。变量每次被引用时，都会输出当前赋给它的值。重要的是要记住，引用一个变量值时需要使用美元符，而引用变量来对其进行赋值时则不要使用美元符。通过一个例子你就能明白我的意思。
```shell {.line-numbers}
#!/bin/bash
value1=10
value2=$value1
echo The resulting value is $value2
```
输出结果如下：
> The resulting value is 10

要是忘了用美元符，使得 value2 的赋值行变成了这样：

`value2=value1`
那你会得到如下输出：
> The resulting value is value1

没有美元符，shell 会将变量名解释成普通的文本字符串，通常这并不是你想要的结果。在赋值的时候不要使用`$`.

---
**命令替换**
在shell脚本中，我们可以从命令的输出中来提取信息。把输出赋给变量之后，就可以随意在脚本中使用了。这个特性在处理脚本数据时尤为方便。
有两种方法可以将命令输出赋给变量：
- 反引号字符(\`)  testing=\`date`
- \$()格式   testing=$(date) 
shell会运行命令替换符号中的命令，并将其输出赋给变量`testing`。注意，赋值等号和命令替换字符之间没有空格。
下面这个例子很常见，它在脚本中通过命令替换获得当前日期并用它来生成唯一文件名,这中方法通常用来生成日志。
```shell {.line-numbers}
#!/bin/bash
today=$(date +%y%m%d)
ls /usr/bin -al > log.$today
```
today 变量被赋予格式化后的 date 命令的输出。这是提取日期信息来生成日志文件名常用的一种技术。+%y%m%d 格式告诉 date 命令将日期显示为两位数的年月日的组合。
> date +%y%m%d
> 220613

在当前目录也会出现一个新的文件，log.220613，里面的内容就是将该有`ls /usr/bin`的输出结果。
在上面的这个例子中，出现了`log.$today`，这是字符串拼接。下面我们将进入shell字符串。

## 字符串
字符串是shell编程中最常用最有用的数据类型（除了数字和字符串，也没啥其它类型好用了），字符串可以用单引号，也可以用双引号，也可以不用引号。
- 单引号
  `str='this is a string'`
  单引号字符串的限制：
  - 单引号里的任何字符都会原样输出，单引号字符串中的变量是无效的
  - 单引号字串中不能出现单独一个的单引号（对单引号使用转义符后也不行），但可成对出现，作为字符串拼接使用。
- 双引号
  双引号的优点：
    - 双引号里可以有变量
    - 双引号里可以出现转义字符
- 没有引号
  - 不被引号包围的字符串中出现变量时也会被解析，这一点和双引号" "包围的字符串一样。
  - 字符串中不能出现空格，否则空格后边的字符串会作为其他变量或者命令解析。
**获取字符串长度**
`${#string_name}`
下面为演示：
```shell {.line-numbers}
#!/bin/bash
str="uestc"
echo ${#str}
```
结果为5。

**拼接字符串**
我们在用一些其他的编程语言的时候，可以使用`+`来拼接字符串，但是在shell中，我们不需要使用任何运算符就可以拼接，直接把两个字符串放在一起就可以了。

```shell {.line-numbers}
#!/bin/bash
name="Shell"
url="http://c.biancheng.net/shell/"
str1=$name$url  #中间不能有空格
str2="$name $url"  #如果被双引号包围，那么中间可以有空格
str3=$name": "$url  #中间可以出现别的字符串
str4="$name: $url"  #这样写也可以，管道被放在命令之间，将一个命令的输出重定向到另一个命令中：管道被放在命令之间，将一个命令的输出重定向到另一个命令中：
echo $str1
echo $str2
echo $str3
echo $str4
```
结果为：
> Shellhttp://c.biancheng.net/shell/
> Shell http://c.biancheng.net/shell/
> Shell: http://c.biancheng.net/shell/
> Shell: http://c.biancheng.net/shell/

**字符串截取**
-  从字符串左边开始计数
   `${string: start :length}`
   其中，string 是要截取的字符串，start 是起始位置（从左边开始，从 0 开始计数），length 是要截取的长度（省略的话表示直到字符串的末尾）。

   例子：
  ```shell {.line-numbers}
  url="c.biancheng.net"
  echo ${url: 2: 9}
  ```
  结果为`biancheng`。
- 从右边开始计数
  `${string: 0-start :length}`
  比左边开始多了`0-`，这是固定的写法，专门用来表示从字符串右边开始计数。
  这里需要强调两点：
  - 从左边开始计数时，起始数字是 0（这符合程序员思维）；从右边开始计数时，起始数字是 1（这符合常人思维）。计数方向不同，起始数字也不同。
  - 不管从哪边开始计数，截取方向都是从左到右。
  例子：
  ```shell {.line-numbers}
  url="c.biancheng.net"
  echo ${url: 0-13: 9}
  ```
  结果为`biancheng`。从右边数，b是第 13 个字符。
  截取字符串还有一些其他的方式，比如从一个第一个指定的字母开始截取，但是时间有限，这里就不在拓展。
## 数组
数组中可以存放多个值。Bash Shell 只支持一维数组（不支持多维数组），初始化时不需要定义数组大小（与 PHP 类似）。

与大部分编程语言类似，数组元素的下标由 0 开始。

Shell 数组用括号来表示，元素用"空格"符号分割开，语法格式如下：
```shell
array_name=(value1 value2 ... valuen)
```
读取数组
读取数组元素值的一般格式是：

`${array_name[index]}`
获取数组中的所有元素
使用`@` 或 `*` 可以获取数组中的所有元素
获取数组的长度
获取数组长度的方法与获取字符串长度的方法相同
`${#my_array[*]}`
## 重定向输入输出
有些时候你想要保存某个命令的输出而不仅仅只是让它显示在显示器上。bash shell 提供了几个操作符，可以将命令的输出重定向到另一个位置（比如文件）。重定向可以用于输入，也可以用于输出，可以将文件重定向到命令输入。


**输出重定向**
最基本的重定向将命令的输出发送到一个文件中。bash shell 用大于号（>）来完成这项功能，之前显示器上出现的命令输出会被保存到指定的输出文件中。
```shell 
command1 > file1
```
上面这个命令执行command1然后将输出的内容存入file1。
比如：
> $ who > users

执行后，并没有在终端输出信息，这是因为输出已被从默认的标准输出设备（终端）重定向到指定的文件。
你可以使用 cat 命令查看文件内容,你的用户名就是出现在users中。

有时，你可能并不想覆盖文件原有内容，而是想要将新命令的输出追加到已有文件内容的后面，比如你正在创建一个记录系统上某个操作的日志文件。在这种情况下，可以用双大于号（>>）来追加数据。

**输入重定向**
和输出重定向一样，Unix 命令也可以从文件获取输入，语法为：

`command1 < file1`

这样，本来需要从键盘获取输入的命令会转移到文件读取内容。

注意：输出重定向是大于号(>)，输入重定向是小于号(<)。

**管道**
有时需要将一个命令的输出作为另一个命令的输入。这可以用重定向来实现，将命令的输出放到文件中，然后在把这个文件作为输入，只是有些笨拙。

我们用不着将命令输出重定向到文件中，可以将其直接重定向到另一个命令。这个过程叫作管道连接（piping）。管道被放在命令之间，将一个命令的输出重定向到另一个命令中：
`command1 | command2`

不要以为由管道串起的两个命令会依次执行。Linux 系统实际上会同时运行这两个命令，在系统内部将它们连接起来。在第一个命令产生输出的同时，输出会被立即送给第二个命令。数据传输不会用到任何中间文件或缓冲区。

管道最流行的用法之一是将命令产生的大量输出通过管道传送给 more 命令。这对 ls 命令来说尤为常见，ls -l 命令产生了目录中所有文件的长列表。对包含大量文件的目录来说，这个列表会相当长。通过将输出管道连接到 more 命令，可以强制输出在一屏数据显示后停下来。

## 流程控制
就像其他的编程语言一样，shell中同样包含`if`、`while`、`for`、`until`等关键字来控制整个程序。
**if结构**
if语句有以下格式：
- if
  ```shell {.line-numbers}
  if condition
  then
      command1 
      command2
      ...
      commandN 
  fi
  ```
- if else
  ```shell {.line-numbers}
  if condition
  then
      command1 
      command2
      ...
      commandN
  else
      command
  fi
  ```
- if else-if else
  ```shell{.line-numbers}
  if condition1
  then
      command1
  elif condition2 
  then 
      command2
  else
      commandN
  fi
  ```
例子：
```shell{.line-numbers}
a=10
b=20
if [ $a == $b ]
then
   echo "a 等于 b"
elif [ $a -gt $b ]
then
   echo "a 大于 b"
elif [ $a -lt $b ]
then
   echo "a 小于 b"
else
   echo "没有符合的条件"
fi
```
这种形式可能让你感到困惑。在其他编程语言中，if 语句之后的对象是一个等式，这个等式的求值结果为 TRUE 或 FALSE。但 bash shell 的 if 语句并不是这么做的。
bash shell 的 if 语句会运行 if 后面的那个命令。如果该命令的退出状态码是 0（该命令成功运行），位于 then 部分的命令就会被执行。如果该命令的退出状态码是其他值，then 部分的命令就不会被执行，bash shell 会继续执行脚本中的下一个命令。fi 语句用来表示 if-then 语句到此结束。
下面来看一个例子：
```shell{.line-numbers}
#!/bin/bash
if pwd
then
    echo "It worked"
fi
```
输出的结果为：
> /home/zbs
> It worked

shell 执行了 if 行中的 pwd 命令。由于退出状态码是 0，它就又执行了 then 部分的 echo 语句。

---
**嵌套if**
有时你需要检查脚本代码中的多种条件。对此，可以使用嵌套的 if-then 语句。
同时也可以可以继续将多个 elif 语句串起来，形成一个大的 if-then-elif 嵌套组合。每块命令都会根据命令是否会返回退出状态码 0 来执行。记住，bash shell 会依次执行 if 语句，只有第一个返回退出状态码 0 的语句中的 then 部分会被执行。
尽管使用了 elif 语句的代码看起来更清晰，但是脚本的逻辑仍然会让人犯晕。在本章稍后你会看到如何使用 case 命令代替 if-then 语句的大量嵌套。

## test命令
到目前为止，在 if 语句中看到的都是普通 shell 命令。你可能想shell是否可以想其他语言一样直接通过使用`<`，`>`这些来比较，答案是不能。但在 bash shell 中有个好用的工具可以帮你通过 if-then 语句测试其他条件。
test 命令提供了在 if-then 语句中测试不同条件的途径。如果 test 命令中列出的条件成立，test 命令就会退出并返回退出状态码 0。这样 if-then 语句就与其他编程语言中的 if-then 语句以类似的方式工作了。如果条件不成立，test 命令就会退出并返回非零的退出状态码，这使得 if-then 语句不会再被执行。
### 数值比较
使用 test 命令最常见的情形是对两个数值进行比较。如下列出了测试两个值时可用的条件参数。
- n1 -eq n2 检查 n1 是否与 n2 相等
- n1 -ge n2 检查 n1 是否大于或等于 n2
- n1 -gt n2 检查 n1 是否大于 n2
- n1 -le n2 检查 n1 是否小于或等于 n2
- n1 -lt n2 检查 n1 是否小于 n2
- n1 -ne n2 检查 n1 是否不等于 n2
数值条件测试可以用在数字和变量上。这里有个例子。
```shell{.line-numbers}
#!/bin/bash
# Using numeric test evaluations
#
value1=10
value2=11
#
if [ $value1 -gt 5 ]
then
    echo "The test value $value1 is greater than 5"
fi
#
if [ $value1 -eq $value2 ]
then
    echo "The values are equal"
else
    echo "The values are different"
fi
```
第一个条件测试测试变量 value1 的值是否大于 5。第二个条件测试测试变量 value1 的值是否和变量 value2 的值相等。两个数值条件测试的结果和预想一致。
但是涉及浮点值时，数值条件测试会有一个限制。bash shell 只能处理整数。如果你只是要通过 echo 语句来显示这个结果，那没问题。但是，在基于数字的函数中就不行了，比如数值测试条件，不能在 test 命令中使用浮点值。
### 字符串比较
条件测试还允许比较字符串值。比较字符串比较麻烦。
- str1 = str2 检查 str1 是否和 str2 相同
- str1 != str2 检查 str1 是否和 str2 不同
- str1 < str2 检查 str1 是否比 str2 小
- str1 > str2 检查 str1 是否比 str2 大
- \-n str1 检查 str1 的长度是否非 0
- \-z str1 检查 str1 的长度是否为 0

需要记住的是，在比较字符串的相等性时，比较测试会将所有的标点和大小写情况都考虑在内。

要测试一个字符串是否比另一个字符串大就是麻烦的开始。当要开始使用测试条件的大于或小于功能时，就会出现两个经常困扰 shell 程序员的问题：

- 大于号和小于号必须转义，否则 shell 会把它们当作重定向符号，把字符串值当作文件名；
- 大于和小于顺序和 sort 命令所采用的不同。

第一条在新手期非常同意踩坑，下面我们展示一个例子。
```shell{.line-numners}
#!/bin/bash
# mis-using string comparisons
#
val1=baseball
val2=football
#
if [ $val1 > $val2 ]
then
    echo "$val1 is greater than $val2"
else
    echo "$val1 is less than $val2"
fi
```
这个脚本中只用了大于号，没有出现错误，但结果是错的。脚本把大于号解释成了输出重定向。因此，它创建了一个名为 football 的文件。由于重定向的顺利完成，test 命令返回了退出状态码 0，if 语句便以为所有命令都成功结束了。要解决这个问题，就需要使用反斜杠`\>`正确转义大于号。

第二个问题更细微，除非你经常处理大小写字母，否则几乎遇不到。`sort`命令处理大写字母的方法刚好跟`test`命令相反。比如两个变量val1=Testing val2=testing，在 test 命令中，大写字母被认为是小于小写字母的。但 sort 命令恰好相反。当你将同样的字符串放进文件中并用 sort 命令排序时，小写字母会先出现。这是由各个命令使用的排序技术不同造成的。

test 命令中使用的是标准的 ASCII 顺序，根据每个字符的 ASCII 数值来决定排序结果。sort 命令使用的是系统的本地化语言设置中定义的排序顺序。对于英语，本地化设置指定了在排序顺序中小写字母出现在大写字母前。
> test 命令测试表达式使用标准的数学比较符号来表示字符串比较，而用文本代码来表示数值比较。这个细微的特性被很多程序员理解反了。如果你对数值使用了数学运算符号，shell 会将它们当成字符串值，可能无法得到正确的结果。

最后，-n 和-z 可以检查一个变量是否含有数据。如果一个变量为空字符串，或其从未被定义，那么均会被认为它的字符串长度为 0。

> 空的和未初始化的变量会对 shell 脚本测试造成灾难性的影响。如果不是很确定一个变量的内容，最好在将其用于数值或字符串比较之前先通过-n 或-z 来测试一下变量是否含有值。

### 文件比较
- \-d file 检查 file 是否存在并是一个目录
- \-e file 检查 file 是否存在（文件或目录）
- \-f file 检查 file 是否存在并是一个文件
- \-r file 检查 file 是否存在并可读
- \-s file 检查 file 是否存在并非空
- \-w file 检查 file 是否存在并可写
- \-x file 检查 file 是否存在并可执行
- \-O file 检查 file 是否存在并属当前用户所有
- \-G file 检查 file 是否存在并且默认组与当前用户相同
- file1 -nt file2 检查 file1 是否比 file2 新
- file1 -ot file2 检查 file1 是否比 file2 旧

这些测试条件使你能够在 shell 脚本中检查文件系统中的文件。它们经常出现在需要进行文件访问的脚本中。鉴于其使用广泛，建议熟练掌握。用于比较文件路径是相对你运行该脚本的目录而言的。

需要注意的是，-G 比较会检查文件的默认组，如果它匹配了用户的默认组，则测试成功。由于-G 比较只会检查默认组而非用户所属的所有组，这会叫人有点困惑。如果文件的组被改成了某个组，用户也是其中的一员，但用户并不以其为默认组，此时-G 比较会失败，因为它只比较默认组，不会去比较其他的组。

此外，在比较两个文件的新旧时，这些比较都不会先检查文件是否存在，如果你要检查的文件已经移走，就会出现问题。在你尝试使用-nt 或-ot 比较文件之前，必须先确认文件是存在的。
### 符合条件测试
if-then 语句允许你使用布尔逻辑来组合测试。有两种布尔运算符可用：

- [ condition1 ] && [ condition2 ]
- [ condition1 ] || [ condition2 ]
结合方括号测试方式和布尔逻辑组合，可以测试更多条件。
### case命令
你会经常发现自己在尝试计算一个变量的值，在一组可能的值中寻找特定值。在这种情形下，你不得不写出很长的 if-then-else 语句。

elif 语句继续 if-then 检查，为比较变量寻找特定的值。有了 case 命令，就不需要再写出所有的 elif 语句来不停地检查同一个变量的值了。case 命令会采用列表格式来检查单个变量的多个值。

case 命令会将指定的变量与不同模式进行比较。如果变量和模式是匹配的，那么 shell 会执行为该模式指定的命令。可以通过竖线操作符在一行中分隔出多个模式模式。星号会捕获所有与已知模式不匹配的值。这里有个将 if-then-else 程序转换成用 case 命令的例子。

```shell {.line-numbers}
case 值 in
模式1)
    command1
    command2
    ...
    commandN
    ;;
模式2)
    command1
    command2
    ...
    commandN
    ;;
esac
```
case 工作方式如上所示，取值后面必须为单词 in，每一模式必须以右括号结束。取值可以为变量或常数，匹配发现取值符合某一模式后，其间所有命令开始执行直至 ;;。

取值将检测匹配的每一个模式。一旦模式匹配，则执行完匹配模式相应命令后不再继续其他模式。如果无一匹配模式，使用星号 * 捕获该值，再执行后面的命令。
## 更多控制流程语句
### for语句

重复执行一系列命令在编程中很常见。通常你需要重复一组命令直至达到某个特定条件，比如处理某个目录下的所有文件、系统上的所有用户或是某个文本文件中的所有行。for 命令有几种不同的方式来读取列表中的值，下面for循环一般格式为：

```shell {.line-numbers}
for var in item1 item2 ... itemN
do
    command1
    command2
    ...
    commandN
done
```
下面我们就介绍几种for循环的用法。

#### 读取列表中的值
for 命令最基本的用法就是遍历 for 命令自身所定义的一系列值。
```shell{.line-numbers}
$ cat test1
#!/bin/bash
# basic for command
for test in Alabama Alaska Arizona Arkansas California Colorado
do
    echo The next state is $test
done
echo "The last state we visited was $test"
test=Connecticut
echo "Wait, now we're visiting $test"
$ ./test1
The next state is Alabama
The next state is Alaska
The next state is Arizona
The next state is Arkansas
The next state is California
The next state is Colorado
The last state we visited was Colorado
Wait, now we're visiting Connecticut
$
```
每次 for 命令遍历值列表，它都会将列表中的下个值赋给$test变量。$test 变量可以像 for 命令语句中的其他脚本变量一样使用。在最后一次迭代后，$test 变量的值会在 shell 脚本的剩余部分一直保持有效。它会一直保持最后一次迭代的值（除非你修改了它）。$test 变量保持了其值，也允许我们修改它的值，并在 for 命令循环之外跟其他变量一样使用

事情并不会总像你在 for 循环中看到的那么简单。有时会遇到难处理的数据。有时 for 循环的值列表中可能存在中间有空格的值，此时使用单引号或者双引号将中间存在空格的值括起来即可。有时候，有的值自身中存在单引号或双引号，这时需要用另外一种相反的引号将其括起来，或者使用反斜杠转义即可正常使用。

#### 从命令读取值

生成列表中所需值的另外一个途径就是使用命令的输出。可以用命令替换来执行任何能产生输出的命令，然后在 for 命令中使用该命令的输出。

```shell {.line-numbers}
#!/bin/bash
# reading values from a file
file="states"
for state in $(cat $file)
do
echo "Visit beautiful $state"
done
```

这个例子在命令替换中使用了 cat 命令来输出文件 states 的内容。你会注意到 states 文件中每一行有一个州，而不是通过空格分隔的。for 命令仍然以每次一行的方式遍历了 cat 命令的输出，假定每个州都是在单独的一行上。但这并没有解决数据中有空格的问题。如果你列出了一个名字中有空格的州，for 命令仍然会将每个单词当作单独的值。

#### 用通配符读取目录

我们可以用 for 命令来自动遍历目录中的文件。进行此操作时，必须在文件名或路径名中使用通配符。它会强制 shell 使用文件扩展匹配。文件扩展匹配是生成匹配指定通配符的文件名或路径名的过程。

如果不知道所有的文件名，这个特性在配合[前面所描述的文件比较](#文件比较)在处理目录中的文件时就非常好用。

```shell{.line-numbers}
#!/bin/bash
# iterate through all the files in a directory
for file in /home/rich/test/*
do
    if [ -d "$file" ]
    then
        echo "$file is a directory"
    elif [ -f "$file" ]
    then
    echo "$file is a file"
    fi
done
```
for 命令会遍历/home/rich/test/*输出的结果。该代码用 test 命令测试了每个条目（使用方括号方法），以查看它是目录（通过-d 参数）还是文件（通过-f 参数）

需要注意的是，在 Linux 中，目录名和文件名中包含空格当然是合法的。要适应这种情况，应该将$file 变量用双引号圈起来。如果不这么做，遇到含有空格的目录名或文件名时就会有错误产生。

### while循环
while 命令允许定义一个要测试的命令，然后循环执行一组命令，只要定义的测试命令返回的是退出状态码 0。它会在每次迭代的一开始测试 test 命令。在 test 命令返回非零退出状态码时，while 命令会停止执行那组命令。
while 命令的格式是：
```shell {.line-numbers}
while test command
do
    other commands
done
```

while 命令中定义的 test command 和 if-then 语句中的格式一模一样。可以使用任何普通的 bash shell 命令，或者用 test 命令进行条件测试，比如测试变量值。 while 命令的关键在于所指定的 test command 的退出状态码必须随着循环中运行的命令而改变。如果退出状态码不发生变化，while 循环就将一直不停地进行下去。 最常见的 test command 的用法是用方括号来检查循环命令中用到的 shell 变量的值。

### until循环
until 命令和 while 命令工作的方式完全相反。until 命令要求你指定一个通常返回非零退出状态码的测试命令。只有测试命令的退出状态码不为 0，bash shell 才会执行循环中列出的命令。一旦测试命令返回了退出状态码 0，循环就结束了。和 while 命令类似，你可以在 until 命令语句中放入多个测试命令。只有最后一个命令的退出状态码决定了 bash shell 是否执行已定义的 other commands。下面是使用 until 命令的一个例子。
```shell {.line-numbers}
#!/bin/bash
# using the until command
var1=100
until [ $var1 -eq 0 ]
do
    echo $var1
    var1=$[ $var1 - 25 ]
done
```
本例中会测试 var1 变量来决定 until 循环何时停止。只要该变量的值等于 0，until 命令就会停止循环。

### 跳出循环
在循环过程中，有时候需要在未达到循环结束条件时强制跳出循环，Shell使用两个命令来实现该功能：break和continue。
- break命令
  break命令允许跳出所有循环（终止执行后面的所有循环）。
- continue
  continue命令与break命令类似，只有一点差别，它不会跳出所有循环，仅仅跳出当前循环。

这些和其他语言中的功能相同，就不在解释。

## 处理输入
到目前为止，你已经看到了如何编写脚本，处理数据、变量和 Linux 系统上的文件。有时，你编写的脚本还得能够与使用者进行交互。bash shell 提供了一些不同的方法来从用户处获得数据，包括命令行参数（添加在命令后的数据）、命令行选项（可修改命令行为的单个字母）以及直接从键盘读取输入的能力。
### 命令行参数
向 shell 脚本传递数据的最基本方法是使用命令行参数。命令行参数允许在运行脚本时向命令行添加数据。
### 读取参数
bash shell 会将一些称为位置参数（positional parameter）的特殊变量分配给输入到命令行中的所有参数。这也包括 shell 所执行的脚本名称。位置参数变量是标准的数字：`$0` 是程序名，`$1` 是第一个参数，`$2` 是第二个参数，依次类推，直到第九个参数`$9`。

下面我们给出一个例子：
```shell{.line-numbers}
#!/bin/bash
echo "Shell 传递参数实例！";
echo "执行的文件名：$0";
echo "第一个参数为：$1";
echo "第二个参数为：$2";
echo "第三个参数为：$3";
```
我们输入是`./test.sh 1 2 3`，
输出是
> Shell 传递参数实例！
> 执行的文件名：./test.sh
> 第一个参数为：1
> 第二个参数为：2
> 第三个参数为：3

下面我们给出一个表格，里面是几个特殊字符，用来处理参数。由于作者现在基本没有使用过这方面的东西，只能给出一些公式类的东西，并不能像前面一样给出具体的例子。
|参数处理|说明|
|---|--|
|$#	|传递到脚本的参数个数|
|$*	|以一个单字符串显示所有向脚本传递的参数如`$*`用「"」括起来的情况、以`$1 $2 … $n`的形式输出所有参数。|
|$$	|脚本运行的当前进程ID号|
|$!	|后台运行的最后一个进程的ID号|
|$@	|与$*相同，但是使用时加引号，并在引号中返回每个参数。如"$@"用「"」括起来的情况、以`$1` `$2` … `$n` 的形式输出所有参数。|
|$-	|显示Shell使用的当前选项，与set命令功能相同。|
|$?	|显示最后命令的退出状态。0表示没有错误，其他任何值表明有错误。|

$* 与 $@ 区别：
- 相同点：都是引用所有参数。
- 不同点：只有在双引号中体现出来。假设在脚本运行时写了三
  个参数 1、2、3，，则 " * " 等价于 "1 2 3"（传递了一个参数），而 "@" 等价于 "1" "2" "3"（传递了三个参数）
## 函数

linux shell 可以用户定义函数，然后在shell脚本中可以随便调用。

shell中函数的定义格式如下：
```shell{.line-numbers}
[ function ] funname [()]  
  
{  
  
    action;  
  
    [return int;] 
  
}  
```

说明：

- 可以带function fun() 定义，也可以直接fun() 定义,不带任何参数。
- 参数返回，可以显示加：return 返回，如果不加，将以最后一条命令运行结果，作为返回值。 return后跟数值n(0-255

下面的例子定义了一个函数并进行调用：
```shell {.line-numbers}
#!/bin/bash  
demoFun(){  
    echo "这是我的第一个 shell 函数!"  
}  
echo "-----函数开始执行-----"  
demoFun  
echo "-----函数执行完毕-----"  
```
输出结果：

> -----函数开始执行-----
> 这是我的第一个 shell 函数!
> -----函数执行完毕-----

下面定义一个带有return语句的函数：
```shell {.line-numbers}
#!/bin/bash  
funWithReturn(){  
    echo "这个函数会对输入的两个数字进行相加运算..."  
    echo "输入第一个数字: "  
    read aNum  
    echo "输入第二个数字: "  
    read anotherNum  
    echo "两个数字分别为 $aNum 和 $anotherNum !"  
    return $(($aNum+$anotherNum))  
}  
funWithReturn  
echo "输入的两个数字之和为 $? !"  
```
输出类似下面：

> 这个函数会对输入的两个数字进行相加运算...
> 输入第一个数字: 
> 1
> 输入第二个数字: 
> 2
> 两个数字分别为 1 和 2 !
> 输入的两个数字之和为 3 !
> 函数返回值在调用该函数后通过 $? 来获得。

注意：所有函数在使用前必须定义。这意味着必须将函数放在脚本开始部分，直至shell解释器首次发现它时，才可以使用。调用函数仅使用其函数名即可。

函数参数

在Shell中，调用函数时可以向其传递参数。在函数体内部，通过 $n 的形式来获取参数的值，例如，$1表示第一个参数，$2表示第二个参数...

带参数的函数示例：
```shell {.line-numbers}
#!/bin/bash  
funWithParam(){  
    echo "第一个参数为 $1 !"  
    echo "第二个参数为 $2 !"  
    echo "第十个参数为 $10 !"  
    echo "第十个参数为 ${10} !"  
    echo "第十一个参数为 ${11} !"  
    echo "参数总数有 $# 个!"  
    echo "作为一个字符串输出所有参数 $\* !"  
}  
funWithParam 1 2 3 4 5 6 7 8 9 34 73  
```
输出结果：

> 第一个参数为 1 !
> 第二个参数为 2 !
> 第十个参数为 10 !
> 第十个参数为 34 !
> 第十一个参数为 73 !
> 参数总数有 11 个!
> 作为一个字符串输出所有参数 1 2 3 4 5 6 7 8 9 34 73 !

注意，$10 不能获取第十个参数，获取第十个参数需要${10}。当n>=10时，需要使用${n}来获取参数。

另外，还有几个特殊字符用来处理参数：

|参数处理|说明|
|---|---|
|$#|传递到脚本或函数的参数个数|
|$\*|以一个单字符串显示所有向脚本传递的参数|
|$$|脚本运行的当前进程ID号|
|$!|后台运行的最后一个进程的ID号|
|$@|与$\*相同，但是使用时加引号，并在引号中返回每个参数。|
|$-|显示Shell使用的当前选项，与set命令功能相同。|
|$?|显示最后命令的退出状态。0表示没有错误，其他任何值表明有错误。|