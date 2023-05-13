[TOC]

# MARCHING ON C/C++



MinGW：Minimal GNU on Windows

gcc：用于编译C程序（推荐），也可以编译C++程序（不推荐）

g++：用于编译C++程序

gdb：用于调试C/C++程序

***ps***: 在VS Code中，通过切换 /.vscode/task.json 中task的 group.isDefault 属性控制默认编译器。



## CLang



### HelloWorld

```c
/*
stdio 表示系统文件库, 也可以声明其它的库
.h  表示头文件,因为这些文件都是放在程序各文件的开头
#include 告诉预处理器将指定头文件的内容插入到预处理器指令的相应位置
<> 表示优先引入系统标准库
也可以写成 "" 表示优先引入用户自定义库
*/
#include <stdio.h>

char* hello = "Hello\n";
char world[] = "World\n";

int main(int argc, const char* argv[])
{
    printf(hello);
    printf(world);
    printf("Hello C World!\n");

    for (int i = 0; i < argc; i++)
    {
        printf("method signature(s): [%d] %s\n", i, argv[i]);
    }
    return 0;
}
```



### 变量

```c
#include <stdbool.h>
bool b = false;

char c;
char c = 'x';
int i = 9, j, k;
float f;
double d;
```

***ps***: 未初始化的变量：带有静态存储持续时间的变量会被隐式初始化为 NULL（所有字节的值都是0），其他所有变量的初始值是未定义的。



变量的声明和定义：

- **声明**不分配存储空间，仅向程序表明变量的类型和名字。e.g. `extern int a;` 其中变量 *a* 也可以在别的文件中定义；
- **定义**会分配存储空间。e.g. `int a;` 在声明时就已经分配了存储空间。

```c
extern int x; // 声明
extern int y = 0; // 赋初始化值则是定义，定义了才会分配存储空间
int z; // 定义也是声明
```

***ps***: 变量需要定义之后才能正确使用，仅声明是不行的。在一个程序中，变量只能定义一次，却可以声明多次。

```c
/* Extern.c */
// 外部变量声明
extern int x;
extern int y;

int add()
{
    return x + y;
}
```

```c
/* Variable.c */
#include <stdio.h>

// 函数外定义变量 x 和 y
int x = 1;
int y = 2;

int add();

int main(int argc, const char* argv[])
{
    int sum = add();

    printf("%d + %d = %d\n", x, y, sum);

    return 0;
}
```



同时编译多个C文件：

- 命令行：`gcc -g Extern.c Variable.c -o exvar`
- VS Code：`task.json`

```json
"tasks": [
    {
        ...
        "command": "C:\\msys64\\mingw64\\bin\\gcc.exe",
        "args": [
            "-g",
            "Extern.c"
            "Variable.c",
            "-o",
            "exvar.exe"
        ],
        ...
    }
]
```



左值（lvalues）：指向内存位置的表达式被称为左值（lvalues）表达式。左值可以出现在赋值号的左边或右边。
右值（rvalues）：存储在内存中某些地址的数值。右值是不能对其进行赋值的表达式，也就是说，右值可以出现在赋值号的右边，但不能出现在赋值号的左边。

在一个表达式中，左值必须是变量，右值可以是变量，常量或者表达式；变量是左值，可以出现在赋值号左侧；数值型字面量是右值，不能被赋值，因此不能出现在赋值号左侧。如 `int i = 20;` 是一个有效的语句，而 `10 = 20;` 不是。

当要保存数据时，用lvalues；当要读取数据时，用rvalues。

根据表达式上下文，lvalues 在需要 rvalues 处可转换为 rvalues，但 rvalues 不能转换为 lvalues：

```c
int lr;
int ll;
ll = lr + 1; // 这里 lr 是 rvalues
```



全局变量保存在内存的全局存储区中，占用静态的存储单元；局部变量保存在栈中，只有所在函数被调用时才动态地分配存储单元。

C语言经过编译之后会将内存分为以下区域：

- 栈（stack）：由编译器进行管理，自动分配和释放，存放函数调用过程中的各种参数、局部变量、返回值以及函数返回地址。操作方式类似于数据结构中的栈。
- 堆（heap）：用于程序动态申请分配和释放空间。C中的 `malloc` 和 `free` ，C++中的 `new` 和 `delete` 均是在堆中进行的。正常情况下，程序员申请的空间在使用结束后应该主动释放，否则要等到程序运行结束时系统才会自动回收。注意：这里的“堆”并不是数据结构中的“堆”。
- 全局（静态）存储区：分为DATA段和BSS段。DATA段（全局初始化区）存放初始化的全局变量和静态变量；BSS段（全局未初始化区）存放未初始化的全局变量和静态变量，程序运行结束时自动释放。其中BSS段在程序执行之前会被系统自动清0，所以未初始化的全局变量和静态变量在程序执行之前已经为0。
- 文字常量区：存放常量字符串。程序结束后由系统释放。
- 程序代码区：存放程序的二进制代码。

显然，C语言中的全局变量和局部变量在内存中是有区别的。全局变量包括外部变量和静态变量，均是保存在全局存储区中，占用永久性的存储单元；局部变量，即自动变量，保存在栈中，只有所在函数被调用时才由系统动态分配临时性存储单元。static局部变量仅在程序首次执行到其声明处时初始化一次，即之后的函数调用不会再进行初始化。

```c
#include <stdio.h>
#include <stdlib.h>

int f1 = 1;
int f2;

static int s1 = 2;
static int s2;

int main(int argc, const char* argv[])
{

    static int s3 = 2, s4;

    int v = 1;
    char str[8] = "hello";
    char* q = "hello";
    char* p;
    p = (char*)malloc(100);
    free(p);

    printf("程序代码区地址\t   : %p\n", &main);
    printf("文字常量区地址\t   : %p [%s]\n\n", q, q);

    printf("全局外部有初值\t f1: %p\n", &f1);
    printf("\t无初值\t f2: %p\n", &f2);
    printf("静态外部有初值\t s1: %p\n", &s1);
    printf("\t无初值\t s2: %p\n", &s2);
    printf("    内部有初值\t s3: %p\n", &s3);
    printf("\t无初值\t s4: %p\n\n", &s4);

    printf("栈区-变量地址\t  v: %p\n", &v);
    printf("\t\tstr: %p\n", str);
    printf("\t\t  q: %p\n", &q);
    printf("\t\t  p: %p\n", &p);
    printf("堆区地址-动态申请 p: %p\n", p);

    return 0;
}
```



### 常量

**整数常量** 可以是十进制、八进制或十六进制的常量。前缀指定基数：0x 或 0X 表示十六进制，0 表示八进制，不带前缀则默认表示十进制。整数常量也可以带一个后缀，后缀是 U 和 L 的组合，U 表示无符号整数（unsigned），L 表示长整数（long）。后缀可以是大写，也可以是小写，U 和 L 的顺序任意。

**浮点常量** 由整数部分、小数点、小数部分和指数部分组成。您可以使用小数形式或者指数形式来表示浮点常量。带符号的指数是用 *e* 或 *E* 引入的。当使用小数形式表示时，必须包含整数部分、小数部分，或同时包含两者。当使用指数形式表示时， 必须包含小数点、指数，或同时包含两者。

**字符常量** 由单引号 `''` 引入，可以是一个普通的字符（例如 `'x'` ）、一个转义序列（例如 `'\t'` ），或一个通用的字符（例如 `'\u02C0'` ）。而且其 ASCII 值可以强制类型转换为整数值，如 `printf("ASCII[%c]=%d", 'a', (int)'a');` 输出 *ASCII[a]=97*。

转义序列以反斜杠 `\` 起始，如 `\n`、`\t`、`\ooo`、`\xhh` 等，其中 `\ooo` 是对用三位八进制数转义表示任意字符的形象化描述，`\xhh` 中 x 是固定的，表示十六进制(hexadecimal)。e.g. `printf("%c %c %c %c", 0101, '\101', '\x41', 'A');` 均输出字符 *A*。

**字符串常量** 或字面值 由双引号 `""` 引入。C 中没有专门的字符串类型，因此双引号内的字符串会被存储到一个数组中，而这个字符串代表指向此数组起始字符的指针。



”定义常量“的方式：

- **#define** 预处理器（宏定义）
- **const** 关键字（必须在一个语句内完成声明、定义和赋值）

```c
#include <stdio.h>

#define LENGTH 10
#define WIDTH 5
#define NEWLINE '\n'

const int L = 10;
const int W = 5;
const char N = '\n';

int main(int argc, const char* argv[])
{
    printf("#define area: %d%c", LENGTH * WIDTH, NEWLINE);
    printf("const area: %d%c", L * W, N);
    return 0;
}
```

***ps***: `#define` 宏定义准确地说不是定义常量，但它可以实现在字面意义上和其它定义常量相同的功能，其本质区别在于宏定义不会为宏名分配内存。而 `const` 会分配内存，但它也不是去定义一个常量，而是去改变一个变量的存储类，将此变量所占的内存变为只读。在实际使用过程中，由于宏会在每个调用处多次展开替换（多次分配内存），因此 `const` 方式（仅分配一次）更能节省内存空间。

**宏定义** 为表达式形式时要注意”边缘效应“。e.g.

```c
#include <stdio.h>

#define N 2 + 3 // 边缘效应
#define NUM (2 + 3)

int main(int argc, const char* argv[])
{
    printf("%.1f", N / (float)2); // 输出 3.5
    printf("%.1f", NUM / (float)2); // 输出 2.5
    return 0;
}
```



### 存储类

- **auto** 是所有局部变量默认的存储类，只能用在函数内部修饰局部变量，这意味着它们在函数开始时被创建，在函数结束时被销毁。。
- **register**
- **static**
- **extern**





### 运算符





### 循环





### 函数

函数在被调用之前需要声明和定义。**声明** 告诉编译器函数的名称、返回类型和参数，**定义** 提供了函数的实际主体。函数的声明和定义可以分开写，但声明必须早于调用（即出现在主函数 `main()` 之前，程序自上而下执行）。其基本形式如下：

```c
// 前置声明 (可只保留参数类型)
return_type function_name( parameter list );
// 声明定义
return_type function_name( parameter list )
{
   fuction_body
}
```

**主函数**

```c
int main(int argc, const char* argv[])
{
    return 0;
}
```

或

```c
int main()
{
    return 0;
}
```

根据能否被其它源文件调用，可以将函数分为内部函数和外部函数。**static** 用于修饰内部函数（静态函数），**extern** 用于修饰外部函数（声明函数时省略 *extern* 默认为外部函数）。另外，在其它源文件中调用外部函数时还需要再次对该函数进行外部声明。

函数传递参数：

- 值传递：将参数的实际值复制给函数形参，实参不会因为形参的操作而变化

  ```c
  #include <stdio.h>
  
  static void swap(int, int);
  
  int main(int argc, const char* argv[])
  {
      int a = 5, b = 10;
      swap(a, b);
      printf("swap[value]: a=%d b=%d", a, b); // a=5 b=10
      return 0;
  }
  
  static void swap(int x, int y)
  {
      int temp = x;
      x = y;
      y = temp;
  }
  ```

  

- 指针传递：形参为指向实参地址的指针，对形参指向的操作会引起实参本身变化（可以在不同函数间访问指针的指向而不用声明为全局变量）

  ```c
  #include <stdio.h>
  
  static void swap(int*, int*);
  
  int main(int argc, const char* argv[])
  {
      int a = 5, b = 10;
      swap(&a, &b);
      printf("swap[point]: a=%d b=%d", a, b); // a=10 b=5
      return 0;
  }
  
  static void swap(int* x, int* y)
  {
      int temp = *x;
      *x = *y;
      *y = temp;
  }
  ```

- 引用传递：见 C++

内联函数是指用 **inline** 修饰<u>定义</u>的函数（函数声明的 **inline** 关键字将被编译器忽略），编译时其函数体会直接替换至调用处（类似于宏展开），从而减少函数调用的时空开销。内联函数仅适用于一些简短函数：

- **不**能包含直接或间接递归
- **不**能包含 *while*、*switch* 等复杂结构控制语句
- **不**推荐分开声明和定义，以确保在不同翻译单元中都能正确内联

***ps***: 现代编译器对于是否采用程序的内联建议具有不同的表现，而且能够主动内联优化代码逻辑，因此需要权衡是否真正需要手动编写内联函数。

递推：

递归：

C 标准函数（库函数）*e.g.*

|                          `sizeof()`                          |                 `strlen()`                  |
| :----------------------------------------------------------: | :-----------------------------------------: |
|                            运算符                            |      函数（包含于头文件*<string.h>*）       |
|             用于计算类型或变量所占用的内存字节数             |         用于计算字符串中的字符个数          |
|                     适用于任何类型的数据                     |     仅适用于以空字符 '\0' 结尾的字符串      |
|             在计算字符串长度时，包含末尾的 '\0'              |           不包含字符串末尾的 '\0'           |
| `sizeof(int) // 4（整型变量占用4个字节）` <br/> `sizeof("hello world") // 12（包含'\0'）` | `strlen("hello world") // 11（不包含'\0'）` |



| 占位符  | scanf()                                  | printf()                                                     |
| ------- | ---------------------------------------- | ------------------------------------------------------------ |
| %c      | 读入首个字符                             | 格式化输出一个字符                                           |
| %d      | 读入一个十进制整数                       | 格式化输出一个十进制整数（支持二、八、十六进制）             |
| %i      | 读入一个整数（支持十进制及八、十六进制） | 格式化输出一个十进制整数（支持二、八、十六进制）             |
| %o      | 读入一个八进制数                         | 格式化输出一个八进制数（支持二、十、十六进制）               |
| %x, %X  | 读入一个十六进制数（区分大、小写）       | 格式化输出一个十六进制数（支持二、八、十进制）               |
| %f, %lf | 读入一个浮点数                           | 格式化输出一个浮点数（默认保留六位小数，可限定 [-]<场宽>.<小数位>） |
| %e, %E  | 科学计数（支持浮点数）                   | 格式化输出一个科学计数（支持浮点型，默认保留六位小数）       |
| %g, %G  | 读入一个浮点数或科学计数                 | 格式化输出浮点数或科学计数（至多保留六位有效数字，包含整数位+小数位） |
| %s      | 读入字符串（遇空格、\n 或 \t 结束）      | 格式化输出字符串                                             |
| %p      | 指针型                                   | 输出指针内存地址（&p）                                       |
| %n      | 读入位于%n前方输入的有效或输出的字符个数 | 统计位于%n前方输出的有效字符个数                             |
| %[]     | 读入限定范围的字符集合                   | /                                                            |

```c
#include <stdio.h>

int main(int argc, const char* argv[])
{
    char input[4];
    int n;
    scanf("%[a-d]%n", input, &n); // input: "ace", 'e' 被排除，计数2
    printf("%%[a-d]=%s(%d)%n", input, n, &n); // output: %[a-d]=ac(2), 输出上一步过滤后的结果和计数值, 并重新计数当前语句字符数
    printf(" %%n=%d", n); // output: %n=12
}
```



### 数组

声明：`type arrayName[size]`

初始化

访问





### 枚举





### *指针



常量指针：本质是一个指针

指针常量：本质是一个常量



函数指针：本质是一个指针，指向一个函数

应用：回调函数



**void*** 表示未确定类型的指针，C和C++规定 *void\** 可以强转为任何其他类型的指针。



### 字符串





### 结构体





### 共同体





### 内存管理







## CPP

### HelloWorld





## APPENDIX



### ASCII

下表包含所有 128 个 ASCII 码对应的十进制 **(dec)** 、八进制 **(oct)** ，十六进制 **(hex)** 和字符 **(ch)** 的值。

<!DOCTYPE html>
<html lang="zh-CN">
    <table style="text-align: left;">
        <tbody>
            <tr>
                <th> dec </th>
                <th> oct </th>
                <th> hex </th>
                <th style="text-align: left;"> ch </th>
                <td rowspan="0"> </td>
                <th> dec </th>
                <th> oct </th>
                <th> hex </th>
                <th style="text-align: left;"> ch </th>
                <td rowspan="0"> </td>
                <th> dec </th>
                <th> oct </th>
                <th> hex </th>
                <th style="text-align: left;"> ch </th>
                <td rowspan="0"> </td>
                <th> dec </th>
                <th> oct </th>
                <th> hex </th>
                <th style="text-align: left;"> ch </th>
            </tr>
            <tr>
                <td>   0 </td>
                <td>   0 </td>
                <td>  00 </td>
                <td> <b>NUL</b> (空) </td>
                <td>  32 </td>
                <td>  40 </td>
                <td>  20 </td>
                <td> (空格) </td>
                <td>  64 </td>
                <td> 100 </td>
                <td>  40 </td>
                <td> <b>@</b> </td>
                <td>  96 </td>
                <td> 140 </td>
                <td>  60 </td>
                <td> <b>`</b> </td>
            </tr>
            <tr>
                <td>   1 </td>
                <td>   1 </td>
                <td>  01 </td>
                <td> <b>SOH</b> (标题开始) </td>
                <td>  33 </td>
                <td>  41 </td>
                <td>  21 </td>
                <td> <b>!</b> </td>
                <td>  65 </td>
                <td> 101 </td>
                <td>  41 </td>
                <td> <b>A</b> </td>
                <td>  97 </td>
                <td> 141 </td>
                <td>  61 </td>
                <td> <b>a</b> </td>
            </tr>
            <tr>
                <td>   2 </td>
                <td>   2 </td>
                <td>  02 </td>
                <td> <b>STX</b> (正文开始) </td>
                <td>  34 </td>
                <td>  42 </td>
                <td>  22 </td>
                <td> <b>"</b> </td>
                <td>  66 </td>
                <td> 102 </td>
                <td>  42 </td>
                <td> <b>B</b> </td>
                <td>  98 </td>
                <td> 142 </td>
                <td>  62 </td>
                <td> <b>b</b> </td>
            </tr>
            <tr>
                <td>   3 </td>
                <td>   3 </td>
                <td>  03 </td>
                <td> <b>ETX</b> (正文结束) </td>
                <td>  35 </td>
                <td>  43 </td>
                <td>  23 </td>
                <td> <b>#</b> </td>
                <td>  67 </td>
                <td> 103 </td>
                <td>  43 </td>
                <td> <b>C</b> </td>
                <td>  99 </td>
                <td> 143 </td>
                <td>  63 </td>
                <td> <b>c</b> </td>
            </tr>
            <tr>
                <td>   4 </td>
                <td>   4 </td>
                <td>  04 </td>
                <td> <b>EOT</b> (传送结束) </td>
                <td>  36 </td>
                <td>  44 </td>
                <td>  24 </td>
                <td> <b>$</b> </td>
                <td>  68 </td>
                <td> 104 </td>
                <td>  44 </td>
                <td> <b>D</b> </td>
                <td> 100 </td>
                <td> 144 </td>
                <td>  64 </td>
                <td> <b>d</b> </td>
            </tr>
            <tr>
                <td>   5 </td>
                <td>   5 </td>
                <td>  05 </td>
                <td> <b>ENQ</b> (询问) </td>
                <td>  37 </td>
                <td>  45 </td>
                <td>  25 </td>
                <td> <b>%</b> </td>
                <td>  69 </td>
                <td> 105 </td>
                <td>  45 </td>
                <td> <b>E</b> </td>
                <td> 101 </td>
                <td> 145 </td>
                <td>  65 </td>
                <td> <b>e</b> </td>
            </tr>
            <tr>
                <td>   6 </td>
                <td>   6 </td>
                <td>  06 </td>
                <td> <b>ACK</b> (确认) </td>
                <td>  38 </td>
                <td>  46 </td>
                <td>  26 </td>
                <td> <b>&amp;</b> </td>
                <td>  70 </td>
                <td> 106 </td>
                <td>  46 </td>
                <td> <b>F</b> </td>
                <td> 102 </td>
                <td> 146 </td>
                <td>  66 </td>
                <td> <b>f</b> </td>
            </tr>
            <tr>
                <td>   7 </td>
                <td>   7 </td>
                <td>  07 </td>
                <td> <b>BEL</b> (响铃) </td>
                <td>  39 </td>
                <td>  47 </td>
                <td>  27 </td>
                <td> <b>'</b> </td>
                <td>  71 </td>
                <td> 107 </td>
                <td>  47 </td>
                <td> <b>G</b> </td>
                <td> 103 </td>
                <td> 147 </td>
                <td>  67 </td>
                <td> <b>g</b> </td>
            </tr>
            <tr>
                <td>   8 </td>
                <td>  10 </td>
                <td>  08 </td>
                <td> <b>BS</b> (退格) </td>
                <td>  40 </td>
                <td>  50 </td>
                <td>  28 </td>
                <td> <b>(</b> </td>
                <td>  72 </td>
                <td> 110 </td>
                <td>  48 </td>
                <td> <b>H</b> </td>
                <td> 104 </td>
                <td> 150 </td>
                <td>  68 </td>
                <td> <b>h</b> </td>
            </tr>
            <tr>
                <td>   9 </td>
                <td>  11 </td>
                <td>  09 </td>
                <td> <b>HT</b> (横向制表) </td>
                <td>  41 </td>
                <td>  51 </td>
                <td>  29 </td>
                <td> <b>)</b> </td>
                <td>  73 </td>
                <td> 111 </td>
                <td>  49 </td>
                <td> <b>I</b> </td>
                <td> 105 </td>
                <td> 151 </td>
                <td>  69 </td>
                <td> <b>i</b> </td>
            </tr>
            <tr>
                <td>  10 </td>
                <td>  12 </td>
                <td>  0a </td>
                <td> <b>LF</b> (换行) </td>
                <td>  42 </td>
                <td>  52 </td>
                <td>  2a </td>
                <td> <b>*</b> </td>
                <td>  74 </td>
                <td> 112 </td>
                <td>  4a </td>
                <td> <b>J</b> </td>
                <td> 106 </td>
                <td> 152 </td>
                <td>  6a </td>
                <td> <b>j</b> </td>
            </tr>
            <tr>
                <td>  11 </td>
                <td>  13 </td>
                <td>  0b </td>
                <td> <b>VT</b> (纵向制表) </td>
                <td>  43 </td>
                <td>  53 </td>
                <td>  2b </td>
                <td> <b>+</b> </td>
                <td>  75 </td>
                <td> 113 </td>
                <td>  4b </td>
                <td> <b>K</b> </td>
                <td> 107 </td>
                <td> 153 </td>
                <td>  6b </td>
                <td> <b>k</b> </td>
            </tr>
            <tr>
                <td>  12 </td>
                <td>  14 </td>
                <td>  0c </td>
                <td> <b>FF</b> (换页) </td>
                <td>  44 </td>
                <td>  54 </td>
                <td>  2c </td>
                <td> <b>,</b> </td>
                <td>  76 </td>
                <td> 114 </td>
                <td>  4c </td>
                <td> <b>L</b> </td>
                <td> 108 </td>
                <td> 154 </td>
                <td>  6c </td>
                <td> <b>l</b> </td>
            </tr>
            <tr>
                <td>  13 </td>
                <td>  15 </td>
                <td>  0d </td>
                <td> <b>CR</b> (回车) </td>
                <td>  45 </td>
                <td>  55 </td>
                <td>  2d </td>
                <td> <b>-</b> </td>
                <td>  77 </td>
                <td> 115 </td>
                <td>  4d </td>
                <td> <b>M</b> </td>
                <td> 109 </td>
                <td> 155 </td>
                <td>  6d </td>
                <td> <b>m</b> </td>
            </tr>
            <tr>
                <td>  14 </td>
                <td>  16 </td>
                <td>  0e </td>
                <td> <b>SO</b> (移出) </td>
                <td>  46 </td>
                <td>  56 </td>
                <td>  2e </td>
                <td> <b>.</b> </td>
                <td>  78 </td>
                <td> 116 </td>
                <td>  4e </td>
                <td> <b>N</b> </td>
                <td> 110 </td>
                <td> 156 </td>
                <td>  6e </td>
                <td> <b>n</b> </td>
            </tr>
            <tr>
                <td>  15 </td>
                <td>  17 </td>
                <td>  0f </td>
                <td> <b>SI</b> (移入) </td>
                <td>  47 </td>
                <td>  57 </td>
                <td>  2f </td>
                <td> <b>/</b> </td>
                <td>  79 </td>
                <td> 117 </td>
                <td>  4f </td>
                <td> <b>O</b> </td>
                <td> 111 </td>
                <td> 157 </td>
                <td>  6f </td>
                <td> <b>o</b> </td>
            </tr>
            <tr>
                <td>  16 </td>
                <td>  20 </td>
                <td>  10 </td>
                <td> <b>DLE</b> (退出数据链) </td>
                <td>  48 </td>
                <td>  60 </td>
                <td>  30 </td>
                <td> <b>0</b> </td>
                <td>  80 </td>
                <td> 120 </td>
                <td>  50 </td>
                <td> <b>P</b> </td>
                <td> 112 </td>
                <td> 160 </td>
                <td>  70 </td>
                <td> <b>p</b> </td>
            </tr>
            <tr>
                <td>  17 </td>
                <td>  21 </td>
                <td>  11 </td>
                <td> <b>DC1</b> (设备控制1) </td>
                <td>  49 </td>
                <td>  61 </td>
                <td>  31 </td>
                <td> <b>1</b> </td>
                <td>  81 </td>
                <td> 121 </td>
                <td>  51 </td>
                <td> <b>Q</b> </td>
                <td> 113 </td>
                <td> 161 </td>
                <td>  71 </td>
                <td> <b>q</b> </td>
            </tr>
            <tr>
                <td>  18 </td>
                <td>  22 </td>
                <td>  12 </td>
                <td> <b>DC2</b> (设备控制2) </td>
                <td>  50 </td>
                <td>  62 </td>
                <td>  32 </td>
                <td> <b>2</b> </td>
                <td>  82 </td>
                <td> 122 </td>
                <td>  52 </td>
                <td> <b>R</b> </td>
                <td> 114 </td>
                <td> 162 </td>
                <td>  72 </td>
                <td> <b>r</b> </td>
            </tr>
            <tr>
                <td>  19 </td>
                <td>  23 </td>
                <td>  13 </td>
                <td> <b>DC3</b> (设备控制3) </td>
                <td>  51 </td>
                <td>  63 </td>
                <td>  33 </td>
                <td> <b>3</b> </td>
                <td>  83 </td>
                <td> 123 </td>
                <td>  53 </td>
                <td> <b>S</b> </td>
                <td> 115 </td>
                <td> 163 </td>
                <td>  73 </td>
                <td> <b>s</b> </td>
            </tr>
            <tr>
                <td>  20 </td>
                <td>  24 </td>
                <td>  14 </td>
                <td> <b>DC4</b> (设备控制4) </td>
                <td>  52 </td>
                <td>  64 </td>
                <td>  34 </td>
                <td> <b>4</b> </td>
                <td>  84 </td>
                <td> 124 </td>
                <td>  54 </td>
                <td> <b>T</b> </td>
                <td> 116 </td>
                <td> 164 </td>
                <td>  74 </td>
                <td> <b>t</b> </td>
            </tr>
            <tr>
                <td>  21 </td>
                <td>  25 </td>
                <td>  15 </td>
                <td> <b>NAK</b> (反确认) </td>
                <td>  53 </td>
                <td>  65 </td>
                <td>  35 </td>
                <td> <b>5</b> </td>
                <td>  85 </td>
                <td> 125 </td>
                <td>  55 </td>
                <td> <b>U</b> </td>
                <td> 117 </td>
                <td> 165 </td>
                <td>  75 </td>
                <td> <b>u</b> </td>
            </tr>
            <tr>
                <td>  22 </td>
                <td>  26 </td>
                <td>  16 </td>
                <td> <b>SYN</b> (同步空闲) </td>
                <td>  54 </td>
                <td>  66 </td>
                <td>  36 </td>
                <td> <b>6</b> </td>
                <td>  86 </td>
                <td> 126 </td>
                <td>  56 </td>
                <td> <b>V</b> </td>
                <td> 118 </td>
                <td> 166 </td>
                <td>  76 </td>
                <td> <b>v</b> </td>
            </tr>
            <tr>
                <td>  23 </td>
                <td>  27 </td>
                <td>  17 </td>
                <td> <b>ETB</b> (传输块结束) </td>
                <td>  55 </td>
                <td>  67 </td>
                <td>  37 </td>
                <td> <b>7</b> </td>
                <td>  87 </td>
                <td> 127 </td>
                <td>  57 </td>
                <td> <b>W</b> </td>
                <td> 119 </td>
                <td> 167 </td>
                <td>  77 </td>
                <td> <b>w</b> </td>
            </tr>
            <tr>
                <td>  24 </td>
                <td>  30 </td>
                <td>  18 </td>
                <td> <b>CAN</b> (取消) </td>
                <td>  56 </td>
                <td>  70 </td>
                <td>  38 </td>
                <td> <b>8</b> </td>
                <td>  88 </td>
                <td> 130 </td>
                <td>  58 </td>
                <td> <b>X</b> </td>
                <td> 120 </td>
                <td> 170 </td>
                <td>  78 </td>
                <td> <b>x</b> </td>
            </tr>
            <tr>
                <td>  25 </td>
                <td>  31 </td>
                <td>  19 </td>
                <td> <b>EM</b> (媒介结束) </td>
                <td>  57 </td>
                <td>  71 </td>
                <td>  39 </td>
                <td> <b>9</b> </td>
                <td>  89 </td>
                <td> 131 </td>
                <td>  59 </td>
                <td> <b>Y</b> </td>
                <td> 121 </td>
                <td> 171 </td>
                <td>  79 </td>
                <td> <b>y</b> </td>
            </tr>
            <tr>
                <td>  26 </td>
                <td>  32 </td>
                <td>  1a </td>
                <td> <b>SUB</b> (替换) </td>
                <td>  58 </td>
                <td>  72 </td>
                <td>  3a </td>
                <td> <b>:</b> </td>
                <td>  90 </td>
                <td> 132 </td>
                <td>  5a </td>
                <td> <b>Z</b> </td>
                <td> 122 </td>
                <td> 172 </td>
                <td>  7a </td>
                <td> <b>z</b> </td>
            </tr>
            <tr>
                <td>  27 </td>
                <td>  33 </td>
                <td>  1b </td>
                <td> <b>ESC</b> (退出) </td>
                <td>  59 </td>
                <td>  73 </td>
                <td>  3b </td>
                <td> <b>;</b> </td>
                <td>  91 </td>
                <td> 133 </td>
                <td>  5b </td>
                <td> <b>[</b> </td>
                <td> 123 </td>
                <td> 173 </td>
                <td>  7b </td>
                <td> <b>{</b> </td>
            </tr>
            <tr>
                <td>  28 </td>
                <td>  34 </td>
                <td>  1c </td>
                <td> <b>FS</b> (文件分隔符) </td>
                <td>  60 </td>
                <td>  74 </td>
                <td>  3c </td>
                <td> <b>&lt;</b> </td>
                <td>  92 </td>
                <td> 134 </td>
                <td>  5c </td>
                <td> <b>\</b> </td>
                <td> 124 </td>
                <td> 174 </td>
                <td>  7c </td>
                <td> <b>|</b> </td>
            </tr>
            <tr>
                <td>  29 </td>
                <td>  35 </td>
                <td>  1d </td>
                <td> <b>GS</b> (组分隔符) </td>
                <td>  61 </td>
                <td>  75 </td>
                <td>  3d </td>
                <td> <b>=</b> </td>
                <td>  93 </td>
                <td> 135 </td>
                <td>  5d </td>
                <td> <b>]</b> </td>
                <td> 125 </td>
                <td> 175 </td>
                <td>  7d </td>
                <td> <b>}</b> </td>
            </tr>
            <tr>
                <td>  30 </td>
                <td>  36 </td>
                <td>  1e </td>
                <td> <b>RS</b> (记录分隔符) </td>
                <td>  62 </td>
                <td>  76 </td>
                <td>  3e </td>
                <td> <b>&gt;</b> </td>
                <td>  94 </td>
                <td> 136 </td>
                <td>  5e </td>
                <td> <b>^</b> </td>
                <td> 126 </td>
                <td> 176 </td>
                <td>  7e </td>
                <td> <b>~</b> </td>
            </tr>
            <tr>
                <td>  31 </td>
                <td>  37 </td>
                <td>  1f </td>
                <td> <b>US</b> (单元分隔符) </td>
                <td>  63 </td>
                <td>  77 </td>
                <td>  3f </td>
                <td> <b>?</b> </td>
                <td>  95 </td>
                <td> 137 </td>
                <td>  5f </td>
                <td> <b>_</b> </td>
                <td> 127 </td>
                <td> 177 </td>
                <td>  7f </td>
                <td> <b>DEL</b> (删除) </td>
            </tr>
        </tbody>
    </table>
</html>

注意：在 Unicode 中，ASCII 字符块被称作 [U+0000..U+007F 基础拉丁 (Basic Latin )](http://www.unicode.org/charts/PDF/) 。



### References

[cppreference-en]: https://en.cppreference.com/w/ "C/C++ Reference"
[cppreference-zh]: https://zh.cppreference.com/w/ "C/C++ 参考手册"