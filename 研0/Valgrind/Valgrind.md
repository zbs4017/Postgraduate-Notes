##简介
Memcheck是Valgrind的王牌，它用于C/C++程序的内存错误检测：
- 非法访问内存（堆、栈、内存段错误）
- 引用未初始化的变量
- 非法释放对内存（重复释放、释放与申请不匹配）
- 内存重叠错误
- 内存泄露
- 错误地申请内存

此外，Memcheck还可以用于内存树的分析。

## 非法访问内存

- 代码
  ```C++{.line-numbers}
  int main( void )
  {
      int *ptr = 0;
      *ptr = 0;
      return 0;
  }

  ```
- 执行
  ```shell{.line-numbers}
  $ gcc test.c -g
  $ ./a.out
  Segmentation fault (core dumped)
  ```
- 结果
  ![](2022-06-15%2008-21-02%20的屏幕截图.png)

  test.c的第4行：非法访问地址为0x0的变量。这一错误导致程序被信号SIGSEGV终止。

## 引用未初始化的变量

- 代码（一）
  ```C++{.line-numbers}
  #include <stdio.h>

  int main( void )
  {
      int x;
      printf("x = %d\n", x);
      return 0;
  }

  ```
- 调试（一）
  ```shell{.line-numbers}
  $ valgrind ./a.out
  ···
  ==2698== Conditional jump or move   depends on uninitialised value(s)
  ==2698==    at 0x4E988DA: vfprintf   (vfprintf.c:1642)
  ==2698==    by 0x4EA0F25: printf   (printf.c:33)
  ==2698==    by 0x108667: main   (test.c:6)
  ==2698== 
  ==2698== Use of uninitialised   value of size 8
  ==2698==    at 0x4E9486B:   _itoa_word (_itoa.c:179)
  ==2698==    by 0x4E97F0D: vfprintf   (vfprintf.c:1642)
  ==2698==    by 0x4EA0F25: printf   (printf.c:33)
  ==2698==    by 0x108667: main   (test.c:6)
  ···
  ```
  可以看到，`test.c`的第6行：在函数 `printf()` 中访问了一个未初始化的变量。
- 代码（二）
  ```C++{.line-numbers}
  #include <stdlib.h>

  int main( void )
  {
      int *arr = malloc(sizeof(int));
      exit(arr[0]);
      return 0;
  }

  ```
- 调试（二）
  ```shell {.line-numbers}
  ==19834== Command: ./a.out
  ==19834== 
  ==19834== Syscall param exit_group  (status) contains uninitialised   byte(s)
  ==19834==    at 0x494E146: _Exit   (_exit.c:31)
  ==19834==    by 0x48B19C1:   __run_exit_handlers (exit.c:132)
  ==19834==    by 0x48B1A5F: exit   (exit.c:139)
  ==19834==    by 0x10918F: main   (test.c:6)
  ```
  test.c的第6行：在系统调用 exit() 中访问了一个未初始化的变量。

## 非法释放对内存
- 代码（一）
  ```C++{.line-numbers}
  #include <stdlib.h>

  int main( void )
  {
      int *ptr = malloc(10);
      free(ptr);
      free(ptr);
      return 0;
  }
   
  ```
- 调试（一）
  ```shell{.line-numbers}
  $ valgrind ./a.out
  ···
  ==2715== Invalid free() / delete /   delete[] / realloc()
  ==2715==    at 0x4C30D3B: free (in   /usr/lib/valgrind/  vgpreload_memcheck-amd64-linux.so)
  ==2715==    by 0x1086B7: main   (test.c:7)
  ==2715==  Address 0x522d040 is 0   bytes inside a block of size 10   free'd
  ==2715==    at 0x4C30D3B: free (in   /usr/lib/valgrind/  vgpreload_memcheck-amd64-linux.so)
  ==2715==    by 0x1086AB: main   (test.c:6)
  ==2715==  Block was alloc'd at
  ==2715==    at 0x4C2FB0F: malloc   (in /usr/lib/valgrind/  vgpreload_memcheck-amd64-linux.so)
  ==2715==    by 0x10869B: main   (test.c:5)
  ···

  ```
  内存在 test.c 的第5行分配，第6行正确释放，而第7行发生非法释放。

- 代码（二）
  ```C++{.line-numbers}
  #include <cstdlib>

  int main(void)
  {
      int *ptr = (int*)malloc(10);
      delete ptr;
      return 0;
  }
  ```
- 调试（二）
  ```shell{.line-numbers}
  $ valgrind ./a.out
  ···
  ==2265== Mismatched free() /   delete / delete []
  ==2265==    at 0x4C3123B: operator   delete(void*) (in /usr/lib/  valgrind/  vgpreload_memcheck-amd64-linux.so)
  ==2265==    by 0x1086F0: main   (test.cpp:6)
  ==2265==  Address 0x5b7dc80 is 0   bytes inside a block of size 10   alloc'd
  ==2265==    at 0x4C2FB0F: malloc   (in /usr/lib/valgrind/  vgpreload_memcheck-amd64-linux.so)
  ==2265==    by 0x1086DB: main   (test.cpp:5)
  ···
  ```
  内存在 test.c 的第5行分配 (malloc)，第6行发生非法释放 (free)。
## 内存泄漏
- 代码
  ```shell{.line-numbers}
  #include <stdlib.h>

  int main( void )
  {
      char *ptr = malloc(100);
      ptr = malloc(50);
      free(ptr);
      return 0;
  }
  ```

- 调试
  ```shell{.line-numbers}
  $ valgrind --tool=memcheck --leak-check=full ./a.out
  ···
  ==2646== HEAP SUMMARY:
  ==2646==     in use at exit: 100   bytes in 1 blocks
  ==2646==   total heap usage: 2   allocs, 1 frees, 150 bytes   allocated
  ==2646== 
  ==2646== 100 bytes in 1 blocks are   definitely lost in loss record 1   of 1
  ==2646==    at 0x4C2FB0F: malloc   (in /usr/lib/valgrind/  vgpreload_memcheck-amd64-linux.so)
  ==2646==    by 0x10869B: main   (test.c:5)
  ==2646== 
  ==2646== LEAK SUMMARY:
  ==2646==    definitely lost: 100   bytes in 1 blocks
  ==2646==    indirectly lost: 0   bytes in 0 blocks
  ==2646==      possibly lost: 0   bytes in 0 blocks
  ==2646==    still reachable: 0   bytes in 0 blocks
  ==2646==         suppressed: 0   bytes in 0 blocks
  ···
  ```
  第5行：malloc() 申请的内存泄露了。很明显，两个 malloc() 只有一个 free() 与之对应。
  Leak Summary 各项解析：
  |类型	|解析|
  |---|---|
  |definitely lost|	确切泄露
  |indirectly lost|	间接泄露
  |possibly lost|  	可能泄露
  |still reachable|	未free，但尚可引用


以上就是`Valgrind`的一些基本用法。