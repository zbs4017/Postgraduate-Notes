## 入门
**CMake** 是一个跨平台的、开源的构建工具。`cmake` 是 `makefile` 的上层工具，它们的目的正是为了产生可移植的makefile，并简化自己动手写makefile时的巨大工作量。

---
### 入门案例
![C++项目部署](2022-06-14%2012-44-01%20的屏幕截图.png)
如上图所示，
- src：源码工程目录
- ext：第三方依赖与头文件
- CMakeLists.txt : cmake 构建配置文件

我们在`src/main.cc`中写入原文件：
```C++{.line-numbers}
#include <iostream>

int main() {
    std::cout << "Hello, World!" << std::endl;
    return 0;
}
```
在配置文件 `CMakeLists.txt` 中编写：
```cmake{.line-numbers}
# cmake 最低版本需求
cmake_minimum_required(VERSION 3.13)

# 工程名称
project(cmake_study)

# 设置
set(CMAKE_CXX_STANDARD 11)

# 编译源码生成目标
add_executable(cmake_study src/main.cc)
```
cmake 命令便按照 CMakeLists 配置文件运行构建Makefile文件
```bash
$ mkdir build 
$ cd build/ 
$ cmake ..
```
为了不让编译产生的中间文件污染我们的工程，我们可以创建一个 `build` 目录进入执行 `cmake` 构建工具. 如果没有错误， 执行成功后会在 `build` 目录下产生 `Makefile` 文件。为了不让编译产生的中间文件污染我们的工程，我们可以创建一个 build 目录进入执行 cmake 构建工具. 如果没有错误， 执行成功后会在 build 目录下产生 Makefile 文件。

然后我们执行 `make` 命令就可以编译我们的项目了。
## cmake详解
### cmake变量
我们可以使用 `SET(set)` 来定义变量

---
**语法** ：`SET(VAR [VALUE] [CACHE TYPE DOCSTRING [FORCE]])` 
**指令功能** : 用来显式的定义变量 
**例子** : `SET (SRC_LST main.c other.c)` 
**说明**: 用变量代替值，例子中定义 `SRC_LST` 代替后面的字符串。

我们可以使用 `${NAME}` 来获取变量的名称。

**cmake常用变量**
1. 
   `CMAKE_BINARY_DIR`
   `PROJECT_BINARY_DIR`
   `<projectname>_BINARY_DIR`
   这三个变量指代的内容是一致的,如果是 in source 编译,指得就是工程顶层目录,如果是 out-of-source 编译,指的是工程编译发生的目录。`PROJECT_BINARY_DIR` 跟其他指令稍有区别,现在,你可以理解为他们是一致的。
2. 
   `CMAKE_SOURCE_DIR`
   `PROJECT_SOURCE_DIR`
   `<projectname>_SOURCE_DIR`
   这三个变量指代的内容是一致的,不论采用何种编译方式,都是工程顶层目录。
   也就是在 in source 编译时,他跟 `CMAKE_BINARY_DIR` 等变量一致。
   `PROJECT_SOURCE_DIR` 跟其他指令稍有区别,现在,你可以理解为他们是一致的。
3. `CMAKE_CURRENT_SOURCE_DIR`
   指的是当前处理的 CMakeLists.txt 所在的路径
4. `CMAKE_CURRENT_LIST_FILE`
   输出调用这个变量的 CMakeLists.txt 的完整路径
5. `CMAKE_CURRENT_LIST_LINE`
   输出这个变量所在的行
6. `PROJECT_NAME`
   返回通过 PROJECT 指令定义的项目名称。

除此之外，还要很多其他的变量。如果你可以自己想仔细体会一下，可以在CMakeLists中，利用message（）命令输出一下这些变量。

另外，这些变量不仅可以在CMakeLists中使用，同样可以在源代码.cpp中使用。

---
### 常用指令
- **ADD_DEFINITIONS**
  语法 : `ADD_DEFINITIONS(-DENABLE_DEBUG -DABC)`

  向 C/C++编译器添加 -D 定义. 如果你的代码中定义了#ifdef ENABLE_DEBUG #endif,这个代码块就会生效。
- **ADD_DEPENDENCIES**
  语法: `ADD_DEPENDENCIES(target-name depend-target1 depend-target2 ...)`
  定义 target 依赖的其他 target, 确保在编译本 target 之前,其他的 target 已经被构建。
- **AUX_SOURCE_DIRECTORY**
  语法 : `AUX_SOURCE_DIRECTORY(dir VARIABLE)`
  作用是发现一个目录下所有的源代码文件并将列表存储在一个变量中,这个指令临时被用来自动构建源文件列表。因为目前 cmake 还不能自动发现新添加的源文件。
  比如：
  ```bash
  AUX_SOURCE_DIRECTORY(. SRC_LIST) ADD_EXECUTABLE(main ${SRC_LIST})
  ```
  上面的代码作用是将当前目录中的所有文件存储到一个列表中，然后并把它们存储到SRC_LIST中，然后通过`ADD_EXECUTABLE`来编译代码。
- **ADD_SUBDIRECTORY**
  语法 : `ADD_SUBDIRECTORY(NAME)`
  添加一个文件夹进行编译，该文件夹下的 CMakeLists.txt 负责编译该文件夹下的源码. NAME是想对于调用`add_subdirectory`的CMakeListst.txt的相对路径．
- `include_directories`
  语法 : `include_directories([AFTER|BEFORE] [SYSTEM] dir1 [dir2 ...])`

  将给定目录添加到编译器用来搜索包含文件的目录中。相对路径被解释为相对于当前源目录。
  
  包含目录添加到 INCLUDE_DIRECTORIES 当前CMakeLists文件的目录属性。它们也被添加到INCLUDE_DIRECTORIES当前CMakeLists文件中每个目标的target属性。目标属性值是生成器使用的属性值。

  结合实际的头文件包含来说明其用法，创建的文件和目录结构及说明如下：
  > ├── CMakeLists.txt    #最外层的CMakeList.txt
  > ├── main.cpp    #源文件，包含被测试的头文件
  > ├── sub    #子目录
  > └── test.h    #测试头文件，是个空文件，被外层的main,cpp包含
  
  **场景1**：不使用`include_directories`包含子目录`sub`,直接在`main.cpp`里面包含`"test.h"`。
  
  ```cmake{.line-numbers}
  # CMakeList.txt
  cmake_minimum_required(VERSION 3.18.2)
  project(include_directories_test)
  add_executable(test main.cpp)
  ```
  ```C++{.line-numbers}
  //main.cpp
  #include "test.h"
  #include <stdio.h>
  int main(int argc, char **argv)
  {
      printf("hello, world!\n");
      return 0;
  }
  ```
  那么这个时候，会提示头文件找不到。
  **场景2**：使用`include_directories`包含子目录`sub`,并在`main.cpp`里面包含`"test.h"`。
  ```cmake {.line-numbers}
  # CMakeList.txt
  cmake_minimum_required(VERSION 3.  18.2)
  project(include_directories_test)
  include_directories(sub) #与上个场景  不同的地方在于此处
  add_executable(test main.cpp)
  ```
  ```C++{.line-numbers}
  //main.cpp
  #include "test.h"
  #include <stdio.h>
  int main(int argc, char **argv)
  {
      printf("hello, world!\n");
      return 0;
  }
  ```
  这时候会编译正常，可以正常使用。
  当然，不使用`include_directories(sub)`，在`main.cpp`中直接使用`#include "sub/test.h"`也是可以的。
- **link_libraries**
  语法 : `link_libraries([item1 [item2 [...]]] [[debug|optimized|general] <item>] ...)`
  
  将库链接到以后添加的所有目标。

- **ADD_EXECUTABLE**
  语法 : `ADD_EXECUTABLE(<name> [source1] [source2 ...])`

  利用源码文件生成目标可执行程序。
- **ADD_LIBRARY**
  语法 : `ADD_LIBRARY(<name> [STATIC | SHARED | MODULE] [source1] [source2 ...])`

  根据源码文件生成目标库。

  `STATIC`,`SHARED` 或者 `MODULE` 可以指定要创建的库的类型。 STATIC库是链接其他目标时使用的目标文件的存档。 SHARED库是动态链接的，并在运行时加载

---
### 控制指令
- **if指令**
  ```
  if(<condition>)
   <commands> 
  elseif(<condition>) # optional block, can be repeated
   <commands> 
  else() # optional block
   <commands> 
  endif() 
  
  
  ##### 
  
  IF(var),如果变量不是:空,0,N, NO, OFF, FALSE, NOTFOUND 或<var>_NOTFOUND 时,表达式为真。 
  IF(NOT var ),与上述条件相反。 IF(var1 AND var2),当两个变量都为真是为真。 
  IF(var1 OR var2),当两个变量其中一个为真时为真。 IF(COMMAND cmd),当给定的 cmd 确实是命令并可以调用是为真。 
  IF(EXISTS dir)或者 IF(EXISTS file),当目录名或者文件名存在时为真。 
  IF(file1 IS_NEWER_THAN file2),当 file1 比 file2 新,或者 file1/file2 其中有一个不存在时为真,文件名请使用完整路径。 
  IF(IS_DIRECTORY dirname),当 dirname 是目录时,为真。 
  IF(variable MATCHES regex) IF(string MATCHES regex)
  ```

- **FOREACH 指令**
  语法:
  ```cmake {.line-numbers}
  foreach(<loop_var> <items>)
    <commands>
  endforeach()
  ```
  其中`<items>`是以分号或空格分隔的项目列表。记录foreach匹配和匹配之间的所有命令endforeach而不调用。 一旦endforeach评估，命令的记录列表中的每个项目调用一次`<items>`。在每次迭代开始时，变量loop\_var将设置为当前项的值。

- **WHILE 指令**
  语法:
  ```cmake {.line-numbers}
  while(<condition>)
  <commands>
  endwhile()
  ```
  while和匹配之间的所有命令 endwhile()被记录而不被调用。 一旦endwhile()如果被评估，则只要为`<condition>`真，就会调用记录的命令列表。

## 实战
### 多个目录多个文件

先来看整个项目的目录结构：
```
    |
    +--- main.cc
    |
    +--- math/
          |
          +--- MathFunctions.cc
          |
          +--- MathFunctions.h
```
顶层的 `CMakeLists.txt`:
```cmake{.line-numbers}
# CMake 最低版本号要求
cmake_minimum_required (VERSION 2.8)
# 项目信息
project (Demo3)
# 查找当前目录下的所有源文件
# 并将名称保存到 DIR_SRCS 变量
aux_source_directory(. DIR_SRCS)
# 添加 math 子目录
add_subdirectory(math)
# 指定生成目标 
add_executable(Demo main.cc)
# 添加链接库
target_link_libraries(Demo MathFunctions)
```

该文件添加了下面的内容: 第9行，使用命令 `add_subdirectory` 指明本项目包含一个子目录 math，这样 math 目录下的 CMakeLists.txt 文件和源代码也会被处理 。第13行，使用命令` target_link_libraries` 指明可执行文件 main 需要连接一个名为 MathFunctions 的链接库 。

在`math`中同样也有一个 CMakeLists.txt 文件。内容如下：

```cmake{.line-numbers}
# 查找当前目录下的所有源文件
# 并将名称保存到 DIR_LIB_SRCS 变量
aux_source_directory(. DIR_LIB_SRCS)
# 生成链接库
add_library (MathFunctions ${DIR_LIB_SRCS})
```

在该文件中使用命令 `add_library` 将 src 目录中的源文件编译为静态链接库。

以上就是一个案例，是作者从网上找的一个案例。
由于作者水平有限，没有接触过使用cmake编译的项目，所以只能总结这些。