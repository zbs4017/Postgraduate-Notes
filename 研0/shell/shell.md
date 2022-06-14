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

---
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
str4="$name: $url"  #这样写也可以，管道被放在命令之间，将一个命令的输出重定向到另一个命令中：
  管道被放在命令之间，将一个命令的输出重定向到另一个命令中：
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

## 重定向输入输出
有些时候你想要保存某个命令的输出而不仅仅只是让它显示在显示器上。bash shell 提供了几个操作符，可以将命令的输出重定向到另一个位置（比如文件）。重定向可以用于输入，也可以用于输出，可以将文件重定向到命令输入。

---
**输出重定向**
最基本的重定向将命令的输出发送到一个文件中。bash shell 用大于号（>）来完成这项功能，之前显示器上出现的命令输出会被保存到指定的输出文件中。



---
**输入重定向**


**管道**
有时需要将一个命令的输出作为另一个命令的输入。这可以用重定向来实现，只是有些笨拙。


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





