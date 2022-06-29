g++入门教程
------

g++命令选项多如繁星，本文参考网上博客总结一篇简单易用的入门教程。

## g++简介

g++是GNU开发的C++编译器，是GCC（GNU Compiler Collection）GNU编译器套件的组成部分。另外，gcc是GNU的C编译器。

g++编译器是GCC的一部分，GCC编译工作一般分为四个步骤：  
（1）预处理（Preprocessing）。由预处理器cpp完成，将.cpp源文件预处理为.i文件。
```
    g++  -E  test.cpp  -o  test.i    //生成预处理后的.i文件
```    

（2）编译（Compilation）。由编译器cc1plus完成，将.i文件编译为.s的汇编文件。使用-S选项，只进行编译而不进行汇编，生成汇编代码。
```
    g++ -S test.i -o test.s	 //生成汇编.s文件
```   

（3）汇编（Assembly）。由汇编器as完成，将.s文件汇编成.o的二进制目标文件
```
    g++  -c  test.s  -o  test.o    //生成二进制.o文件
```   

（4）链接（Linking）。由链接器ld，将.o文件连接生成可执行程序。
```
    g++ test.o  -o  test.out  	  //生成二进制.out可执行文件
 ```   


## 命令选项


关于g++的命令选项，可以参考[GCC官方手册](https://gcc.gnu.org/onlinedocs/gcc-6.1.0/gcc.pdf)，或者使用man g++单独查看g++使用手册。  
下面列出常用的命令选项。  
1. 总体选项
   ```shell
       -E (仅作预处理，不进行编译、汇编和链   接）
       	只激活预处理,这个不生成文件,你需   要把它重定向到一个输出文件里面。   例子用法:   
       	gcc -E hello.c > pianoapan.   txt   
       	gcc -E hello.c | more   
       	慢慢看吧,一句`hello word`也要   预处理成800行的代码。     
       -S (仅编译到汇编语言，不进行汇编和链   接）  
       	只激活预处理和编译，就是指把文件   编译成为汇编代码。例子用法： 
       	gcc -S hello.c   
       	将生成.s的汇编代码，可以用文本编   辑器查看。    
       -c (编译、汇编到目标代码，不进行链   接）   
       	只激活预处理,编译,和汇编,也就是   他只把程序做成obj文件。例子用法   :   
       	gcc -c hello.c   
       	将生成.o的目标文件（object    file）。 
       -o (输出到指定的文件）
       	指定目标名称，缺省的时候，gcc/g   ++编译出来的文件是a.out。例子如   下：   
       	g++ -o hello.out hello.cpp
       	g++ -o hello.asm -S hello.   cpp   
    ```   

2. 目录选项
    ```shell
    -I[dir] （把dir加入到搜索头文件的路径列表中）
    	g++优先在当前目录查找头文件，如果没有找到，会到系统默认的头文件目录找。
    	如果使用-I指定了目录，g++会到-I指定的目录查找，查找不到，然后再到系统默认的头文件目录查找
            例子如下:
             g++ test.c -I../inc -o test	
    
    -L[dir] （把dir加入到搜索库文件的路径列表中）
    	编译的时候，指定搜索库的路径。比如你自己的库，可以用它指定目录，不然编译器将只在标准库的
    	目录找。这个dir就是目录的名称。例子如下:
            g++ -I../inc -L/home/mylib  test.c -o test -lmythread
    
    -l[library] （进行链接时搜索名为library的库）  
    	指定编译的时使用的库，例子用法   
    	g++  hello.c  -o hello -lpthread
    	使用pthread库编译连接，生成程序。  
    
    -include [file] （一般用不到，头文件都是在代码中 #include）
    	相当于在代码中使用#include，用于包含某个代码。
    	简单来说,就是编译某个文件,需要另一个文件的时候,就可以用它设定。例子用法:   
    	g++ hello.c -include /root/pianopan.h  
    -Wl,-rpath=[dir] （把dir加入到程序运行时搜索库文件的路径列表中）
              程序运行时查找自己的动态库的时候，可以用它指定运行时的动态库搜索路径。用法如下：
              g++ -o hello hello.cpp -L./lib -lmylib -Wl,-rpath=./lib
    ```
    
3. 预处理选项
   ```
    -Dmacro
    	相当于C语言中的#define macro
    -Dmacro=defn
    	定义宏，相当于C语言中的#define macro defn
    -Umacro
    	取消宏定义，相当于C语言中的#undef macro
    -undef
    	取消任何非标准宏的定义，C++标准预定义的宏仍然有效
    ```
   
4. 链接方式选项
   ```
    -static
    	此选项将禁止使用动态库。优点：程序运行不依赖于其他库。缺点：可执行文件比较大。
    -shared
    	此选项将尽量使用动态库，为默认选项。优点：生成文件比较小。缺点：运行时需要系统提供动态库。
    -Wl,-Bstatic
    	告诉链接器ld只链接静态库，如果只存在动态链接库，则链接器报错。
    -Wl,-Bdynamic
    	告诉链接器ld优先使用动态链接库，如果只存在静态链接库，则使用静态链接库。
   ``` 

5. 错误与告警选项
   ```
    -Wall（打开一些很有用的警告选项）
    	一般使用该选项，允许发出GCC能够提供的所有有用的警告。也可以用-W{warning}来标记指定的警告
    -W （打印一些额外的警告信息）
    -Werror （把警告当作错误，出现任何警告就放弃编译）
    	要求g++将所有的警告当成错误进行处理，在警告发生时中止编译过程。
    -Werror={warning}
    	将指定警告设置为错误。例如-Werror=return-type，如果函数需要返回值却没有return语句，则编译报错
    -Wshadow
            当一个局部变量遮盖住了另一个局部变量，或者全局变量时，给出警告(很有用，建议打开)
    -w
    	关闭所有警告(建议不要使用此项)
    这一类选项其实还有很多，如-Wpointer-arith、-Wcast-qual、-Waggregate-return、-Winline等，在此不一一介绍，需要用到可以查手册。
   ``` 

6. 调试选项
   ```
     -g   
    	指示编译器，在编译时，产生调试信息。
    -ggdb    
    	此选项将尽可能的生成gdb可以使用的调试信息。
    -glevel
    	请求生成调试信息，同时用level指出需要多少信息，默认的level值是2。
   ``` 

7. 优化选项
   ```
    -O0   （禁止编译器进行优化。默认为此项）
    -O1   （尝试优化编译时间和可执行文件大小）
    -O2   （更多的优化，会尝试几乎全部的优化功能，但不会进行“空间换时间”的优化方法）
    -O3   （在 -O2 的基础上再打开一些优化选项：-finline-functions， -funswitch-loops 和 -fgcse-after-reload ）
    -Os   （对生成文件大小进行优化。它会打开 -O2 开的全部选项，除了会那些增加文件大小的。）
    	编译器优化建议使用-O2。
   ``` 

（8）标准

    -ansi 
    	支持符合ANSI标准的C程序(关闭GNU C中某些不兼容ANSI C的特性)
    -std=c99   
    	指明使用标准 ISO C99 作为标准来编译程序
    -std=c++11
    	指明使用标准 C++11 作为标准来编译程序
    

9. 其他选项
   ```
    -fpic
    	编译器生成位置无关目标码（PIC，position-independent code），用于动态链接库，即Linux下的.so文件。
    	通过全局偏移表（GOT，Global Offset Table）访问所有常量地址。
    	程序启动时通过动态加载程序解析GOT条目。
    	如果链接的so文件的GOT大小超过计算机特定的最大大小，则会从链接器收到错误消息，指示-fpic不起作用。这种情况下，请使用-fPIC重新编译
    -fPIC
    	同-fpic功能一致，生成位置无关目标码，用于生成动态链接库，建议使用该选项，而非-fpic
    -v
    	显示详细的编译、汇编、连接命令
    
    -imacros file   
    	将file文件的宏,扩展到gcc/g++的输入文件,宏定义本身并不出现在输入文件中     
    -nostdinc   
    	不在系统缺省的头文件目录里面找头文件,一般和-I联合使用,明确限定头文件的位置。 
    -nostdin C++
    	不在g++指定的标准路经中搜索,但仍在其他路径中搜索,此选项在创建libg++库使用。
    -C
    	在预处理的时候,不删除注释信息,一般和-E使用,有时候分析程序，用这个很方便的。 
    -m
    	生成与具体CPU相关的程序。
    
    -m32 
    	生成32bits程序
    -m64 
    	生成64bits程序
    -M
    	生成文件依赖的信息，包含目标文件所依赖的所有源文件。你可以用gcc -M hello.c来测试一下，很简单。   
    -MM   
    	和上面的那个一样，但是它将忽略由#include造成的依赖关系。   
    -MD
    	和-M相同，但是输出将导入到.d的文件里面。
    -MMD   
    	和-MM相同，但是输出将导入到.d的文件里面。
    -x language filename   
    	设定文件所使用的语言,使后缀名无效,对以后的多个有效.
    	也就是根据约定C语言的后缀名称是.c的，而C++的后缀名是.C或者.cpp。
    	如果你很个性，决定你的C代码文件的后缀名是.pig，那你就要用这个参数
    	这个参数对他后面的文件名都起作用，除非到了下一个参数的使用。可以使用的参数有下面的这些：
    	c,objective-c,c-header,c++,cpp-output,assembler,assembler-with-cpp。   
    	看到英文，应该可以理解的。例子用法:   
    	gcc -x c hello.pig
    -x none filename
    	关掉上一个选项，也就是让gcc根据文件名后缀，自动识别文件类型，例子用法:   
    	gcc -x c hello.pig -x none hello2.c
   ``` 

