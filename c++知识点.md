

# C++

[TOC]

## 基本API总结

### 标准输入输出

#### c的api	

```c
int printf(const char *format, ...);           // 格式化输出到标准输出流
int scanf(const char *format, ...);            // 从标准输入流格式化读取
int putchar(int c);                            // 输出单个字符
int getchar(void);                             // 读取单个字符
int puts(const char *s);                       // 输出字符串并换行
char *gets(char *s);                           // 读取一行 (不推荐使用，有缓冲区溢出风险)
char *fgets(char *s, int size, FILE *stream);  // 安全地读取一行
```

```c
    FILE *file = fopen("example.txt", "r");
    if (file == NULL) {
        perror("Failed to open file");
        return EXIT_FAILURE;
    }

    char buffer[100];
    while (fgets(buffer, sizeof(buffer), file) != NULL) {
        int number;
        if (sscanf(buffer, "Number: %d", &number) == 1) {
            printf("Extracted number: %d\n", number);
        }
    }

    fclose(file);
```

#### cpp

std::getline

std::istringstream

```c++
    std::ifstream file("example.txt");
    if (!file.is_open()) {
        std::cerr << "Failed to open file" << std::endl;
        return 1;
    }

    std::string line;
    while (std::getline(file, line)) {
        std::istringstream iss(line);
        int number;
        // 跳过ws（空白符）
        if (iss >> std::ws && iss.peek() != EOF) {
            if (iss >> number) {
                std::cout << "Extracted number: " << number << std::endl;
            }
        }
    }

    file.close();
```

### 读写文件

#### POSIX 文件操作 API

- 面向底层，直接操作文件描述符。

- **文件打开/关闭**
  - `int open(const char *path, int flags, mode_t mode);`
    打开文件，返回文件描述符（非负整数）。常用标志：`O_RDONLY`、`O_WRONLY`、`O_RDWR`、`O_CREAT` 等。
  - `int close(int fd);`
    关闭文件描述符。
- **读写文件**
  - `ssize_t read(int fd, void *buf, size_t count);`
    从文件描述符读取数据到缓冲区。
  - `ssize_t write(int fd, const void *buf, size_t count);`
    向文件描述符写入数据。
- **文件定位**
  - `off_t lseek(int fd, off_t offset, int whence);`
    调整文件指针位置（`whence` 可为 `SEEK_SET`、`SEEK_CUR`、`SEEK_END`）。
- **文件状态**
  - `int fstat(int fd, struct stat *buf);`
    获取文件元数据（大小、权限等）。

####  **C 标准库文件操作 API**

- 面向缓冲区，操作简单，适合大多数应用场景。

- **文件打开/关闭**
  - `FILE *fopen(const char *path, const char *mode);`
    打开文件，返回文件指针（`FILE*`）。模式如 `"r"`（读）、`"w"`（写）、`"a"`（追加）等。
  - `int fclose(FILE *stream);`
    关闭文件指针。
- **读写文件**
  - `size_t fread(void *ptr, size_t size, size_t nmemb, FILE *stream);`
    从文件读取数据到缓冲区。
  - `size_t fwrite(const void *ptr, size_t size, size_t nmemb, FILE *stream);`
    向文件写入数据。
- **文件定位**
  - `int fseek(FILE *stream, long offset, int whence);`
    调整文件指针位置（`whence` 为 `SEEK_SET`、`SEEK_CUR`、`SEEK_END`）。
  - `long ftell(FILE *stream);`
    获取当前文件指针位置。
- **格式化 I/O**
  - `int fprintf(FILE *stream, const char *format, ...);`
    格式化写入文件。
  - `int fscanf(FILE *stream, const char *format, ...);`
    格式化读取文件。
- **错误处理**
  - `int ferror(FILE *stream);` 检查错误。
  - `void clearerr(FILE *stream);` 清除错误标志。

#### **C++ 文件操作 API**

C++ 标准库（`<fstream>`）通过类封装文件操作，支持面向对象编程。

- **文件流类**
  - `std::ifstream`：输入文件流（读）。
  - `std::ofstream`：输出文件流（写）。
  - `std::fstream`：输入/输出文件流。
- **基本操作**
  - `std::ifstream file("path");` 打开文件（构造函数）。
  - `file.open("path", std::ios::in | std::ios::out);` 显式打开文件。
  - `file.close();` 关闭文件。
- **读写数据**
  - `file >> variable;` 提取数据（流提取运算符）。
  - `file << data;` 插入数据（流插入运算符）。
  - `file.read(buffer, size);` 读取二进制数据。
  - `file.write(buffer, size);` 写入二进制数据。
- **状态检查**
  - `file.good()`、`file.eof()`、`file.fail()` 检查流状态。

```c++
// posix

int open(const char *path, int flags, mode_t mode);
int close(int fd);
ssize_t read(int fd, void *buf, size_t count);
ssize_t write(int fd, const void *buf, size_t count);
off_t lseek(int fd, off_t offset, int whence);
int fstat(int fd, struct stat *buf);

// c
FILE *fopen(const char *path, const char *mode);
int fclose(FILE *stream);
size_t fread(void *ptr, size_t size, size_t nmemb, FILE *stream);
size_t fwrite(const void *ptr, size_t size, size_t nmemb, FILE *stream);
int fseek(FILE *stream, long offset, int whence);

int fprintf(FILE *stream, const char *format, ...);
```



### 线程API

### 智能指针api





## 基础知识

### c代码的编译过程

预处理  -E生成.i文件 处理include 、宏展开、处理#ifdef等条件编译 

编译  -S 生成 .s文件       生成汇编代码 

汇编 -c生成.o文件   机器码

链接 

（静态库在链接时根据使用情况被复制到可执行文件中，动态库在运行时动态加载）

### 指针和引用的区别

面试简答：

**1，引⽤是⼀个别名，它是在已存在的变量上创建的**。 在**声明时被初始化**。

**在汇编层面，引用通常被实现为常量指针（const pointer）**。  因此，指向不可变，整个⽣命周期中⼀直引⽤同⼀个变量。

2，指针存放某个对象的地址，其本身就是变量，本身就有地址，可以通过解引用修改指向的地址的数据 。

3，不存在指向空值的引⽤，但是存在指向空值的指针

### 区分下面的指针

```c
int *p[10]   //指针的数组  10个int指针
int (*p)[10]   //指向int数组的 一个指针
int *p(int)   //p函数 返回值为指针
int (*p)(int)   // 函数指针 返回值和参数为int
```

### 关于const

const变量在定义时需要初始化，在汇编层面，const局部变量可能是立即数，

#### const 关键字的作⽤

==面试简答：⽤于指定变量、指针、引⽤、成员函数、成员变量时的效果==。

1，声明常量，使变量的值不能被修改。

2，声明指向常量的指针，不能通过指针修改指向的对象。

​    声明常量引⽤，不能通过引⽤修改指向的对象。

```
const int* ptr = &constantValue; // 指向常量的指针  
const int& ref = constantValue; // 常量引⽤
```

3，⽤于声明常成员函数，对this指针const，表示**该函数不会修改对象**

​    声明常成员变量。**类中的const成员变量需要在构造函数的初始化列表中指定初始值。**

#### C++指针的顶层const和底层const

**顶层const：**指的是const修饰的**变量本身是一个常量**，无法修改，指的是指针，就是 * 号的右边

**底层const：**指的是const修饰的**变量所指向的对象是一个常量**，指的是所指变量，就是 * 号的左边

举个例子

```c
int a = 10;
int* const b1 = &a; //顶层const，b1本身是一个常量
const int* b2 = &a; //底层const，b2本身可变，所指的对象是常量

const int b3 = 20; //顶层const，b3是常量不可变

const int* const b4 = &a; //前一个const为底层，后一个为顶层，b4不可变
const int& b5 = a; //用于声明引用变量，都是底层const
```

#### const和static的区别

一个是用于可变性的声明，一个是静态声明。

==解答思路：类外、类内、用于变量和函数==

<img src="./image/c++%E7%9F%A5%E8%AF%86%E7%82%B9_image/image-20241022215100387.png" alt="image-20241022215100387" style="zoom:80%;" />

##### static

面试简答：

1，声明静态全局变量，使变量只在所在文件的编译单元可见；

2，声明静态局部变量，该变量具有记忆性，存储在全局变量区，函数退出后不会消亡。

3，声明静态成员变量和成员函数，这类变量和函数不依赖类的对象，不具有this指针，通过类名::来访问。静态函数不能访问非static成员变量和函数（因为没有this指针）

不考虑类的情况：

* ==隐藏==。所有不加static的==全局变量和函数==具有全局可见性，可以在其他文件中使用，==加了之后只能在该文件所在的编译模块中使用==
* **静态局部变量在函数内定义**，始终存在，且只进行一次初始化，**具有记忆性**，其作用范围与局部变量相同，函数退出后仍然存在，但不能使用
* 未初始化的全局静态变量与局部静态变量，都存在全局未初始化区。默认初始化为0。

考虑类的情况

* ==static成员变量==：**只与类关联，不与类的对象关联**。
  
  ==定义时会在全局数据区分配空间==
  
  ==不能在类声明中初始化，必须在类定义体外部初始化==，初始化时不需要标示为static；
  
  可以被非static成员函数任意访问。
  
  ```
  class ExampleClass {
      public:
      static int staticVar; // 静态成员变量声明
  };
  // 静态成员变量定义
  int ExampleClass::staticVar = 0;
  ```

* ==static成员函数==：不具有this指针，无法访问类对象的非static成员变量和非static成员函数；不能被声明为const、虚函数和volatile；可以被非static成员函数任意访问                     

##### const

面试简答：

1，声明常量，使变量的值不能被修改。

2，声明指向常量的指针，不能通过指针修改指向的对象。

​    声明常量引⽤，不能通过引⽤修改指向的对象。

3，⽤于声明常成员函数，对this指针const，表示**该函数不会修改对象**

​    声明常成员变量。**类中的const成员变量需要在构造函数的初始化列表中指定初始值。**

​    不考虑类的情况

* const常量在定义时必须初始化，之后无法更改

* const可以修饰返回值和函数参数，const形参可以接收const和非const类型的实参，
  
  ```c
  void fun(const int& i){ //...}  //i 可以是 int 型或者 const int 型
  ```

考虑类的情况

* ==const成员变量==：不能在类定义外部初始化，==只能通过构造函数初始化列表进行初始化==，并且必须有构造函数；不
  
  同类对其const数据成员的值可以不同，所以==不能在类中声明时初始化==

* ==const成员函数==：const对象不可以调用非const成员函数；非const对象都可以调用；
  
  不可以改变非mutable（用该关键字声明的变量可以在const成员函数中被修改）数据的值

#### const和define的区别

面试简答：

1，const编译时生效，define预处理阶段生效

2，const变量可能在栈中或者全局变量区，占用内存；define定义的变量会被替换成立即数。

3，const带类型，比define更安全。

const⽤于==定义常量==；⽽define⽤于==定义宏==，⽽==宏也可以⽤于定义常量==。

都⽤于常量定义时，它们的区别有：

1. const==⽣效于编译==的阶段； define==⽣效于预处理==阶段。

2. const定义的常量，在C语⾔中是存储在内存中、需要额外的内存空间的； define定义的常量，运⾏时是直接

的操作数，并==不会存放在内存中==。

3. const定义的常量是带类型的； ==define定义的常量不带类型==。因此define定义的常量==不利于类型检查==。

#### const和constexpr

面试简答：

const可能是运行时常量，也可能是编译时常量；constexpr用于明确声明编译时常量。

- **`const`**：
  
  - 可以用于==运行时常量，也可以用于编译时常量==。它==并不保证在编译时进行计算==。
  - 适用于需要保护变量不被修改的情况，例如在类成员中声明常量。

- **`constexpr`**：
  
  - 适用于==函数、变量和表达式==，确保在==编译时能够计算其值==，所有参数也是常量表达式
    
    ```cpp
    #include <iostream>
    
    const int constVal = 5; // 运行时常量或者编译时常量 取决于编译时是否确定
    constexpr int constExprVal = 10; // 编译时常量
    
    constexpr int square(int x) { // constexpr 函数
        return x * x;
    }
    
    int main() {
        int arr1[constVal]; // 可以用 const 变量
        int arr2[square(3)]; // 必须用 constexpr 变量
    
        std::cout << "constVal: " << constVal << std::endl;
        std::cout << "constExprVal: " << constExprVal << std::endl;
        std::cout << "Size of arr2: " << sizeof(arr2) / sizeof(arr2[0]) << std::endl;
    
        return 0;
    }
    ```

### volatile

`volatile` 是 C++ 中的一个关键字，用于告诉编译器某个变量可能会在程序的控制之外被修改（例如，硬件、操作系统或其他线程）。

因此，编译器不应该对这个变量进行某些优化（如缓存它的值到寄存器中），以**确保每次访问该变量时都会从内存中读取其最新值**。

### 关于static

面试简答：

1，静态全局变量，变量只在文件的编译单元可见

2，声明静态局部变量，该变量具有记忆性，存储在全局变量区，函数退出后不会消亡。

3，声明静态成员变量和成员函数，这类变量和函数不依赖类的对象，不具有this指针，通过类名::来访问。静态函数中不能访问非static成员变量和函数（因为没有this指针）

static 关键字主要⽤于控制变量和函数的⽣命周期、作⽤域以及访问权限。

#### 1.静态局部变量

在函数内部使⽤ static 关键字修饰的变量称为静态变量。

静态变量在程序的整个⽣命周期内存在，不会因为离开作⽤域⽽被销毁。静态变量默认初始化为零（对于基本数据类型）。

```c++
void exampleFunction() {
    static int count = 0; // 静态变量
    count++;
    cout << "Count: " << count << endl;
}
```

#### 2.静态全局变量

静态全局变量是使用 `static` 修饰的全局变量。与普通全局变量不同，==静态全局变量的作用域被限制在定义它的源文件内，不能被其他源文件引用==。

在头文件中声明和定义静态全局变量时一体化，不需要额外的 `extern` 关键字。

```c++
// static_global_variable.h
static int staticGlobalVar = 24;  // 静态全局变量的声明和定义
```

#### 3.静态成员函数

在类内部使⽤ static 关键字修饰的函数是静态函数。

==**静态函数属于类⽽不是类的实例，可以通过类名直接调⽤，⽽⽆需创建对象**==。

可以在类外或者内部定义，类外定义时要加上类名限定作用域。

**静态函数不能直接访问**⾮静态成员变量或⾮静态成员函数，因为它们==没有隐含的 this 指针==。

[==静态只能访问静态== ，不过有一些方法可以让他访问非静态成员变量]

普通成员函数必须具体作用于某个对象，而静态成员函数并不具体作用于某个对象。

==普通的成员函数一般都隐含了一个this指针==，this指针指向类的对象本身，因为普通成员函数总是具体地属于类的某个具体对象的。当函数被调用时，系统会把当前对象的起始地址赋给 this 指针。通常情况下，this是缺省的。

==与普通函数相比，静态成员函数属于类本身，而不作用于对象，因此它不具有this指针==。正因为它没有指向某一个对象，所以它无法访问属于类对象的非静态成员变量和非静态成员函数，它只能调用其余的静态成员函数和静态成员变量。从另一个角度来看，由于静态成员函数和静态成员变量在类实例化之前就已经存在可以访问，而此时非静态成员还是不存在的，因此静态成员不能访问非静态成员。

```c++
class ExampleClass {
    public:
    static void staticFunction() {
        cout << "Static function" << endl;
    }
};
ExampleClass::staticFunction();
```

#### 4.静态成员变量

在类中使⽤ static 关键字修饰的成员变量是静态成员变量。

==**所有类的对象共享同⼀个静态成员变量的副本**==。

因为静态数据成员在全局数据区分配内存，由本类的所有对象共享，所以，它不属于特定的类对象，不占用对象的内存，而是在所有对象之外开辟内存，在没有产生类对象时其作用域就可见。

因此，在没有类的实例存在时，静态成员变量就已经存在，我们就可以操作它。

静态成员变量存储在全局数据区。static 成员变量的内存空间既不是在声明类时分配，也不是在创建对象时分配，而是在初始化时分配。

==静态成员变量必须初始化，而且只能在类体外进行==。如果只声明静态成员变量而不定义，编译器会认为它是一个“未定义的引用”，导致 **链接错误（Linker Error）**

初始化时可以赋初值，也可以不赋值。如果不赋值，那么会被默认初始化，一般是 0。

静态数据成员初始化的格式为：＜数据类型＞＜类名＞::＜静态数据成员名＞=＜值＞

静态数据区的变量都有默认的初始值，而动态数据区（堆区、栈区）的变量默认是垃圾值。

```c
class ExampleClass {
    public:
    static int staticVar; // 静态成员变量声明
};
// 静态成员变量定义
int ExampleClass::staticVar = 0;
```

### 全局变量、静态全局变量、全局常量、局部变量、静态局部变量

#### 1. 全局变量

全局变量是定义在函数外部，整个程序范围内都可见的变量。

==通常将全局变量的声明放在.cpp文件中，以避免多层包含可能引起的重复定义错误。== 

==为了在其他文件中使用这些变量，可以在相应的头文件中使用 `extern` 关键字进行声明==。

```c
// global_variables.h
extern int globalVar;  // 声明全局变量  以使其他文件使用

// global_variables.cpp
int globalVar = 42;  // 声明和定义全局变量
```

#### 2.静态全局变量

静态全局变量是使用 `static` 修饰的全局变量。

与普通全局变量不同，静态全局变量的作用域被限制在定义它的源文件内，不能被其他源文件引用。

在头文件中声明和定义静态全局变量时一体化，不需要额外的 `extern` 关键字。

```c
// static_global_variable.h
static int staticGlobalVar = 24;  // 静态全局变量的声明和定义
```

#### 3. 全局常量

全局常量是使用 `const` 关键字定义的全局变量，其值在定义时被初始化，并且不能在程序运行过程中修改。在.cpp文件中定义并赋初值。

==在头文件中使用 `extern` 进行声明，便于其他文件使用。==

```c
// global_constants.h
extern const double PI;  // 声明全局常量

// global_constants.cpp
const double PI = 3.14159;  // 定义全局常量
```

#### 4.局部变量

#### 5.静态局部变量

在函数内部使⽤ static 关键字修饰的变量称为静态变量。

静态变量在程序的整个⽣命周期内存在，不会因为离开作⽤域⽽被销毁。

静态变量默认初始化为零（对于基本数据类型）。





### define 和 inline 的区别

面试简答：

1，define在编译阶段进行字符替换，inline在将内联函数编译完成后，插入调用的地方。

2，inline是典型的空间换时间，增大程序体积，但减少了函数调用的入栈、跳转、返回操作。

3，inline是对编译器的一个建议，当inline函数很庞大时，编译器可能将其视为普通函数。

##### 1、 define：

定义预编译时处理的宏，只是简单的字符串替换，⽆类型检查，不安全。

##### 2、 inline：

inline是先将内联函数编译完成，==⽣成了函数体直接插⼊被调⽤的地⽅==，==减少了压栈，跳转和返回的操作。没有普通函数调⽤时的额外开销==；

内联函数是⼀种特殊的函数，会进⾏类型检查；

对编译器的⼀种请求，编译器有可能拒绝这种请求；  内联函数仅仅是对编译器的内联建议，编译器是否觉得采取你的建议取决于函数是否符合内联的有利条件。如果函数体非常大，那么编译器将忽略函数的内联声明，而将内联函数作为普通函数处理。

#### C++中inline编译限制：

1. 不能存在任何形式的循环语句

2. 不能存在过多的条件判断语句

3. 函数体不能过于庞⼤

4. 内联函数声明必须在调⽤语句之前

##### 1、优点

通过避免函数调用所带来保存现场、变量弹栈压栈、跳转新函数、存储函数返回值、执行完返回原现场等开销，==提高了程序的运行速度==
通过将函数声明为内联，你可以把函数定义放在头文件内。编译器需要把inline函数体替换到函数调用处，所以编译器必须要知道inline函数的函数体是啥，所以要将inline函数的函数定义和函数声明一起写在头文件中，便与编译器查找替换。

##### 2、缺点

因为代码的替换扩展，==内联函数会增大可执行程序的体积==，进而导致程序变得更慢
C++内联函数的展开是中编译阶段，这就意味着如果你的内联函数发生了改动，那么就需要重新编译代码
当你把内联函数放在头文件中时，它将会使你的头文件信息变多，不过头文件的使用者不用在意这些

### define和typedef

面试简答：

1，define在预处理阶段生效，typedef在编译和运行时生效

2，define没有类型检查，只是字符替换，typedef有数据类型。

define

1. 只是==简单的字符串替换，没有类型检查==

2. 是在编译的==预处理阶段==起作⽤

3. 可以⽤来==防⽌头⽂件重复引⽤== 条件编译

4. ==不分配内存==，给出的是⽴即数，有多少次使⽤就进⾏多少次替换

typedef

1. 有==对应的数据类型==，是要进⾏判断的

2. 是在==编译、运⾏的时候起作⽤==

### 变量的声明和定义的区别

面试简答：

1，声明不分配内存空间，只是向程序表明变量的类型和名字；**定义才分配空间**。定义其实包含了声明。

2，可以用extern在多处声明，但**只能定义一次**。

### sizeof和strlen

sizeof是运算符，并不是函数，结果在编译时得到而非运行中获得；strlen是字符处理的库函数。  

```
const char* str = "name";
sizeof(str); // 取的是指针str的空间大小，是8
strlen(str); // 取的是这个字符串的长度，不包含结尾的 \0。大小是4
```

==sizeof参数可以是任何数据的类型或者数据，包括类和结构体==    ==strlen的参数只能是字符指针 且结尾是'\0'的字符串==  

### extern

定义：声明外部变量【在函数或者⽂件外部定义的全局变量】

#### extern C

面试简答：

**用于在 C++ 代码中调用 C 语言编写的函数或变量。**

- C++ 编译器会对函数名进行名称修饰，以支持函数重载等特性。而 C 语言没有名称修饰，直接使用函数名。

extern作用是**防止 C++ 编译器对函数名进行名称修饰**（name mangling），从而确保 C++ 代码能够正确链接到 C 语言编写的函数。

假设有一个 C 语言编写的库，包含以下函数：

```c++
// myclib.c
#include <stdio.h>

void c_function(int x) {
    printf("C function called with %d\n", x);
}
```

在 C++ 代码中调用该函数时，需要使用 `extern "C"`：

```c++
// main.cpp
extern "C" void c_function(int);

int main() {
    c_function(42);  // 调用 C 语言编写的函数
    return 0;
}
```

### 什么是函数指针，如何定义和使⽤场景

函数指针是指向函数的指针变量。它可以⽤于存储函数的地址，允许在运⾏时动态选择要调⽤的函数。  

```c
// 返回类型 (*指针变量名)(参数列表)
int add(int a, int b) {
    return a + b;
}
int subtract(int a, int b) {
    return a - b;    
}
int main() {
    // 定义⼀个函数指针，指向⼀个接受两个int参数、返回int的函数
    int (*operationPtr)(int, int);
    // 初始化函数指针，使其指向 add 函数
    operationPtr = &add;
    // 通过函数指针调⽤函数
    int result = operationPtr(10, 5);    
    cout << "Result: " << result << endl;
    // 将函数指针切换到 subtract 函数
    operationPtr = &subtract;
    // 再次通过函数指针调⽤函数
    result = operationPtr(10, 5);
    cout << "Result: " << result << endl;
    return 0;
}
```

使⽤场景：

1. ==回调函数==： 函数指针常⽤于实现回调机制，允许将函数的地址传递给其他函数，以便在适当的时候调⽤。

2. ==函数指针数组==： 可以使⽤函数指针数组实现类似于状态机的逻辑，根据不同的输⼊调⽤不同的函数。

3. 动态加载库： 函数指针可⽤于在运⾏时动态加载库中的函数，实现动态链接库的调⽤。

4. 多态实现： 在C++中，虚函数和函数指针结合使⽤，可以实现类似于多态的效果。

5. ==函数指针作为参数==： 可以将函数指针作为参数传递给其他函数，实现⼀种==可插拔的函数⾏为==。

6. 实现函数映射表： 在⼀些需要根据某些条件调⽤不同函数的情况下，可以使⽤函数指针来实现函数映射表。

### struct和Class的区别

面试简答：

1，struct表示一组数据，class封装了数据和操作的方法

2，struct的成员默认公有，继承默认公有继承；class的成员默认私有，继承默认私有继承。

3，struct适合简单的数据聚合；  当设计具有继承、多态的类时，使用class。

通常， struct ⽤于表示==⼀组相关的数据==，⽽ class ⽤于表示==⼀个封装了数据和操作的对象==,在实际使⽤中，可以根据具体的需求选择使⽤ struct 或 class 。

如果只是⽤来组织⼀些数据，⽽不涉及复杂的封装和继承关系， struct 可能更直观；如果==需要进⾏封装、继承等⾯向对象编程的特性，可以选择使⽤ class==。

==struct结构体中的成员默认是公有的（public）==。==class类中的成员默认是私有的（private）。==

struct 继承时默认使⽤公有继承。==class 继承时默认使⽤私有继承==。

如果结构体没有定义任何构造函数，编译器会⽣成默认的⽆参数构造函数。如果类没有定义任何构造函数，编译器也会⽣成默认的⽆参数构造函数。  

### C++强制类型转换

面试简答：

都是为了安全的转换。

1，static_cast在编译时类型检查。

2，dynamic_cast是在运行时类型检查，将基类指针进行下行转换。基类必须具有多态（虚函数实现），因为dynamic_cast使用vptr获取RTTI来进行检查。

`Derived* derivedPtr = dynamic_cast<Derived*>(basePtr);`

3，reinterpre_cast允许**强制将指针或引用**转换为其他类型的指针或引用，通常**用于需要直接操作底层内存**或实现特定功能的场景。

**不进行类型检查**        **仅适用于指针和引用**

4，const_cast 用于移除或添加const属性，适用于指针和引用。

#### static_cast

**编译时进行类型检查。 但是没有运⾏时类型检查。**

`static_cast`会检查类型是否兼容

```c++
std::string str = "Hello";
int num = static_cast<int>(str); // 错误：不兼容的类型

class A {};
class B {};

A a;
B* bPtr = static_cast<B*>(&a); // 错误：不兼容的类型


class Base {};
class Derived : public Base {};

Base base;
Derived* derivedPtr = static_cast<Derived*>(&base); // 编译通过，但运行时未定义行为
```

进⾏上⾏转换（把派⽣类的指针或引⽤转换成基类表示）是安全的 ，**基类指针可以引用任何其派生类的对象**。

进⾏下⾏转换（把基类的指针或引⽤转换为派⽣类表示），由于没有动态类型检查，所以是不安全的。

==下行转换安全的前提是：基类指针实际指向的是子类的对象==

```c++
// 基本类型之间的转换
int intValue = 42;
float floatValue = static_cast<float>(intValue); // int 转 float

// 指针或引用的转换    
class Base {};
class Derived : public Base {};
Derived derived;
Base* basePtr = static_cast<Base*>(&derived); // 上行转换

Base* basePtr = new Derived();
Derived* derivedPtr = static_cast<Derived*>(basePtr); // 下行转换 此时安全
```

#### dynamic_cast

用于安全地进行类型转换，尤其是在**涉及多态和继承**时。

1. ==**用于指向基类的指针或引用**==

2. 进⾏**下⾏转换时， 具有类型检查**的功能，⽐static_cast更安全。

3. **基类要有虚函数**，转换后必须是类的指针、引⽤或者void*，可以交叉转换。

如果转换失败，`dynamic_cast` 返回 `nullptr`（对于指针）或抛出异常（对于引用），确保了安全性。

```c++
Base* basePtr = new Derived();
Derived* derivedPtr = dynamic_cast<Derived*>(basePtr); // 使用dynamic_cast
```

dynamic_cast需要用到RTTI

RTTI（Runtime Type Information，运行时类型信息）是 C++ 提供的一种机制，**允许程序在运行时获取类型信息**。

**RTTI 数据结构**

- **类型信息结构**：编译器为每个具有虚函数的类生成一个类型信息结构（通常是 `std::type_info` 类型的对象），该结构包含关于类的信息，如类名、基类信息等。
* 类有虚函数，则有虚表；类的对象则有虚指针vptr，虚指针指向虚表；dymamic_cast利用虚指针找虚表 ，通过虚表信息可以获取类型信息。

==`dynamic_cast` 要求基类有虚函数的原因主要与运行时类型信息（RTTI）相关==

使用 `dynamic_cast` 时**，编译器首先检查虚指针以获取对象的类型信息，确保转换的安全性。**如果对象不是所期望的类型，它会返回 `nullptr`（对于指针）或抛出 `std::bad_cast` 异常（对于引用），从而避免未定义行为。

#### reinterpret_cast

它允许**将指针或引用**转换为其他类型的指针或引用，通常**用于需要直接操作底层内存**或实现特定功能的场景。

1. 低级转换：**最强大且最危险**的转换方式，因为它可能会导致不安全的内存访问，==最好别用==。

2. **不进行类型检查**

3. **仅适用于指针和引用**

```c++
#include <iostream>

struct A {
    int x;
};

struct B {
    float y;
};

int main() {
    A a;
    B* bPtr = reinterpret_cast<B*>(&a); // 将 A* 转换为 B*

    // 此时 bPtr 指向 a 的内存，不建议直接使用
    std::cout << "Address of a: " << &a << std::endl;
    std::cout << "Address of bPtr: " << bPtr << std::endl;

    return 0;
}
```

#### const_cast

添加或移除对象的 `const` 或 `volatile` 属性

**`const_cast` 仅适用于指针和引用**

###### 可行的例子

**示例 1: 移除 `const` 属性**

```c++
#include <iostream>

void modifyValue(const int* ptr) {
    // 使用 const_cast 移除 const 属性
    int* modifiablePtr = const_cast<int*>(ptr);
    *modifiablePtr = 100; // 修改数据
}

int main() {
    int value = 10;
    const int* constPtr = &value;

    modifyValue(constPtr);
    std::cout << "Value after modification: " << value << std::endl; // 输出100
    return 0;
}
```

**示例 2: 从非常量转换为 `const`**

```c++
#include <iostream>

void displayValue(const int* ptr) {
    std::cout << "Value: " << *ptr << std::endl;
}

int main() {
    int value = 42;
    int* ptr = &value;

    // 将非常量指针转换为 const 指针
    const int* constPtr = const_cast<const int*>(ptr);
    displayValue(constPtr); // 合法

    return 0;
}
```

**示例3：与库函数交互**

满足接口的const要求

```c++
#include <iostream>

void libraryFunction(const int* ptr) {
    // 模拟库函数
    std::cout << "Library function received: " << *ptr << std::endl;
}

int main() {
    int value = 10;
    int* ptr = &value;

    libraryFunction(const_cast<const int*>(ptr)); // 使用 const_cast
    return 0;
}
```

**==注意到上面的例子都是对指针添加或移除`const`==**

###### 注意事项

如果你通过 `const_cast` 移除 `const` 属性并修改一个原本被声明为 `const` 的对象，这将导致未定义行为

```c++
const int x = 10;
int* y = const_cast<int*>(&x);
*y = 20; // 未定义行为
```

### explicit

简答：**显式声明，主要用于构造函数和转换运算符**。防止隐式类型转换，从而增强代码的安全性。

#### 1. 显式构造函数

当你使用“explicit”修饰构造函数时，C++会禁止将单个参数的构造函数用于隐式类型转换。例如：

```c
class MyClass {
public:
    explicit MyClass(int x) {
        // 构造函数逻辑
    }
};

void func(MyClass obj) {
    // 函数逻辑
}

int main() {
    MyClass a(10); // 正确
    // MyClass b = 20; // 错误，不能隐式转换
    func(a); // 正确
    // func(20); // 错误，不能隐式转换
}
```

#### 2. 显式转换运算符

`MyClass` 类包含一个显示转换运算符 `operator int()`，它允许 `MyClass` 类型的对象显式地转换为 `int` 类型。

```c++
class MyClass {
public:
    explicit operator int() const {
        return 42; // 示例转换
    }
};

int main() {
    MyClass obj;
    // int x = obj; // 错误，不能隐式转换
    int x = static_cast<int>(obj); // 正确，使用显式转换
}
```

### 隐式转换

隐式转换就是编译器自动完成的转换，不需要程序员显式操作。

#### 基本数据类型之间的转换

当进行数学运算时，编译器可能会自动将较小的类型转换为较大的类型。例如：

```c++
int a = 10;
double b = 5.5;
double result = a + b; // int a 被隐式转换为 double
```

#### 类类型之间的转换

如果一个类有单参数构造函数，C++会允许通过该构造函数进行隐式转换。例如：

```c++
class MyClass {
public:
    MyClass(int x) { /* ... */ }
};

void func(MyClass obj) {
    // 函数逻辑
}

int main() {
    func(42); // 隐式转换：42 被转换为 MyClass 对象
}
```

### enum class

面试简答：

`enum class` 是 C++11 引入的一种枚举类型，它比传统的 `enum` 提供了**更严格的类型检查和命名空间功能**。

使用 `enum class` 可以避免许多传统枚举类型的名称冲突和隐式转换问题。

**传统枚举 (enum)** 与 **强类型枚举 (enum class)** 区别

1. **名称冲突：**
   - 传统的 `enum` 会将枚举值的名称提升到全局作用域，这可能会导致名称冲突。
   - `enum class` 则不会将枚举值提升到全局作用域，它们被封装在枚举类的作用域内。
2. **隐式类型转换：**
   - 传统的 `enum` 枚举值可以隐式转换为整型或其他整数类型。
   - `enum class` 的枚举值不能隐式转换为其他类型（如整数），必须显式转换。

**基本语法**

```
enum class EnumName : UnderlyingType {
    Value1,
    Value2,
    Value3,
    //...
};
```

- **`EnumName`**：枚举的名称。
- **`UnderlyingType`**（可选）：底层类型，默认是 `int`。可以指定为 `char`、`short`、`long` 等其他整数类型，以控制存储的大小。

**示例：基础用法**

```
#include <iostream>

enum class Color {
    Red,    // 默认值为 0
    Green,  // 默认值为 1
    Blue    // 默认值为 2
};

int main() {
    Color color = Color::Red;

    if (color == Color::Red) {
        std::cout << "The color is red!" << std::endl;
    }

    // 下面这行会报错，因为 `Color::Red` 是强类型枚举，不能隐式转换为整数
    // if (color == 0) { }  

    return 0;
}
```

**输出：**

```
The color is red!
```

**解释：**

- `Color` 是一个强类型枚举，它包含三个枚举值 `Red`、`Green` 和 `Blue`。
- 你可以通过 `Color::Red` 来访问枚举值。
- 如果尝试将 `Color::Red` 与 `0` 比较，编译器会报错，因为 `enum class` 不允许隐式转换。

**指定底层类型**

你可以为 `enum class` 指定一个底层类型，它决定了枚举的存储类型大小。默认情况下，底层类型是 `int`，但你可以显式指定为其他整数类型。

```
enum class Status : unsigned int {
    Success = 0,
    Error = 1,
    Warning = 2
};
```

**强制类型转换**

由于 `enum class` 不支持隐式类型转换，你必须显式地进行类型转换才能将其转换为整数类型，反之亦然。

```
#include <iostream>

enum class Color {
    Red = 1,
    Green = 2,
    Blue = 3
};

int main() {
    Color color = Color::Red;

    // 显式转换为整数
    std::cout << "Color value: " << static_cast<int>(color) << std::endl;  // 输出 1

    // 显式转换为 Color 枚举类型
    int colorValue = 2;
    Color color2 = static_cast<Color>(colorValue);

    if (color2 == Color::Green) {
        std::cout << "The color is green!" << std::endl;
    }

    return 0;
}
```

**输出：**

```
Color value: 1
The color is green!
```

**访问枚举成员的作用域**

`enum class` 的枚举值是 **作用域限定的**，这意味着你必须使用枚举的类型名来访问枚举值，例如 `Color::Red`，而不像传统枚举那样直接访问枚举值。

```
cpp复制代码#include <iostream>

enum class Fruit {
    Apple,
    Banana,
    Orange
};

int main() {
    Fruit fruit = Fruit::Apple;

    if (fruit == Fruit::Apple) {
        std::cout << "It's an apple!" << std::endl;
    }

    return 0;
}
```

**总结与优点：**

- **作用域保护**：`enum class` 的值仅在其定义的枚举类型作用域内有效，避免了与其他枚举类型或全局命名空间的冲突。
- **强类型检查**：`enum class` 禁止隐式转换为整数或其他类型，强制要求显式转换，这有助于避免潜在的错误。
- **可以指定底层类型**：通过指定底层类型（例如 `int`、`char`、`unsigned int`），你可以控制枚举类型的存储大小和表示范围。

`enum class` 是现代 C++ 编程中推荐使用的枚举类型，它提供了比传统枚举更高的类型安全性和更好的作用域管理。

### C++的异常处理方法

##### 基本异常处理语法

try-catch 基本结构

```
try {
    // 可能抛出异常的代码
    throw 异常对象;
} catch (异常类型1 参数名) {
    // 处理异常类型1
} catch (异常类型2 参数名) {
    // 处理异常类型2
} catch (...) {
    // 处理任何类型的异常
}
```

##### C++标准异常类层次结构

C++标准库提供了一个异常类层次结构，所有标准异常都派生自`std::exception`基类：

```
std::exception
├── std::bad_alloc
├── std::bad_cast
├── std::bad_typeid
├── std::bad_exception
└── std::logic_error
    ├── std::domain_error
    ├── std::invalid_argument
    ├── std::length_error
    └── std::out_of_range
└── std::runtime_error
    ├── std::range_error
    ├── std::overflow_error
    └── std::underflow_error
```

##### 自定义异常类

```c++
#include <iostream>
#include <exception>
#include <string>

class DatabaseException : public std::exception {
private:
    std::string m_message;
    int m_errorCode;

public:
    DatabaseException(const std::string& message, int errorCode) 
        : m_message(message), m_errorCode(errorCode) {}

    // 重写what()方法
    const char* what() const noexcept override {
        return m_message.c_str();
    }

    int getErrorCode() const {
        return m_errorCode;
    }
};

void connectToDatabase() {
    // 模拟数据库连接失败
    throw DatabaseException("Failed to connect to database", 1001);
}

int main() {
    try {
        connectToDatabase();
    } catch (const DatabaseException& e) {
        std::cerr << "Database error (code " << e.getErrorCode() 
                  << "): " << e.what() << std::endl;
    } catch (const std::exception& e) {
        std::cerr << "Standard exception: " << e.what() << std::endl;
    }

    return 0;
}
```

## 面向对象

### ⾯向对象的三⼤特性

1. 继承

定义：让某种类型==对象获得另⼀个类型对象的属性和⽅法==

功能：它可以使⽤现有类的所有功能，并在⽆需重新编写原来的类的情况下对这些功能进⾏扩展

常⻅的继承有三种⽅式： 

1、实现继承： 指使⽤基类的属性和⽅法⽽⽆需额外编码的能⼒

2、接⼝继承： 指仅使⽤属性和⽅法的名称、但是⼦类必须提供实现的能⼒

3、可视继承： 指⼦窗体（类）使⽤基窗体（类）的外观和实现代码的能⼒

2. 封装

定义：==数据和代码捆绑在⼀起，避免外界⼲扰和不确定性访问==;

功能：把客观事物封装成抽象的类，并且类可以把⾃⼰的数据和⽅法只让可信的类或者对象操作，对不可信的进⾏信息隐藏，例如：将公共的数据或⽅法使⽤public修饰，⽽==不希望被访问的数据或⽅法采⽤private修饰==。

3. 多态

定义：==同⼀事物表现出不同事物的能⼒==，即向不同对象发送同⼀消息，不同的对象在接收时会产⽣不同的⾏为（重载实现编译时多态，虚函数实现运⾏时多态）

功能：

==多态性是允许你将⽗对象设置成为和⼀个或更多的他的⼦对象相等的技术==，赋值之后，⽗对象就可以根据当前赋值给它的⼦对象的特性以不同的⽅式运作;

简单⼀句话：==允许将⼦类类型的指针赋值给⽗类类型的指针==。

实现多态有两种⽅式

1.覆盖（override）： 是指⼦类重新定义⽗类的虚函数的做法

2.重载（overload）： 是指允许存在多个同名函数，⽽这些函数的参数表不同（或许参数个数不同，或许参数类型不同，或许两者都不同）

### 对面向对象的理解   Object Oriented Programming

参考侯捷OOP笔记中类的关系

继承 is-a

组合 has-a ，生命周期相同，内存为包含关系，同时构造和析构；component修改时，container要重新编译

委托 component by reference，生命周期不同，通过指针指向真正的实现；编译防火墙

类的关联：考虑设计模式

### 有哪些访问修饰符

C++提供了三个访问修饰符： public 、 private 和 protected 。这些修饰符决定了类中的成员对外部代码的可⻅性和访问权限。

* public 修饰符⽤于指定类中的成员可以被类的外部代码访问。
* private 修饰符⽤于指定类中的成员只能被类的内部代码访问。
* protected 修饰符⽤于指定类中的成员可以被类的派⽣类访问。==（将成员开放给派生类，但对外部不可见）==

在==类的内部（定义类的代码内部）==，⽆论成员被声明为 public、 protected 还是 private，==都是可以互相访问的==，没有访问权限的限制。  

#### （一）访问权限

派生类可以继承基类中除了构造/析构、赋值运算符重载函数之外的成员，但是这些成员的访问属性在派生过程中也是可以调整的，三种派生方式的访问权限如下表所示：注意外部访问并不是真正的外部访问，而是在通过派生类的对象对基类成员的访问。  

![image-20241011172428753](./image/c++%E7%9F%A5%E8%AF%86%E7%82%B9_image/image-20241011172428753.png)

派生类对基类成员的访问形象有如下两种：

* 内部访问：由派生类中新增的成员函数对从基类继承来的成员的访问

* 外部访问：在派生类外部，通过派生类的对象对从基类继承来的成员的访问

#### （二）继承权限

**子类会继承父类的所有成员（包括 `public`、`protected` 和 `private`），但访问权限由继承方式和成员的原始访问权限共同决定。**

* public继承

公有继承的特点是基类的公有成员和保护成员作为派生类的成员时，都保持原有的状态，而**基类的私有成员任然是私有的，不能被这个派生类的子类所访问**

* protected继承

保护继承的特点是基类的所有公有成员和保护成员都成为派生类的保护成员，并且只能被它的派生类成员函数或友元函数访问，基类的私有成员仍然是私有的，访问规则如下表

![image-20241011172732984](./image/c++%E7%9F%A5%E8%AF%86%E7%82%B9_image/image-20241011172732984.png)

* private继承

私有继承的特点是基类的所有公有成员和保护成员都成为派生类的私有成员，并不被它的派生类的子类所访问，基类的成员只能由自己派生类访问，无法再往下继承，访问规则如下表

![image-20241011172758163](./image/c++%E7%9F%A5%E8%AF%86%E7%82%B9_image/image-20241011172758163.png)

### 多重继承  虚继承解决菱形问题

 ⼀个类可以从多个基类继承属性和⾏为  ,多重继承可能引⼊==菱形继承问题==

为了解决这些问题， C++ 提供了==虚继承==, 通过在继承声明中使⽤ virtual 关键字，可以==避免在派⽣类中⽣成多个基类的实例==。

==**虚继承的目的是让某个类做出声明，承诺愿意共享它的基类**==。其中，这个被共享的基类就称为虚基类（Virtual Base Class）。

```c++
class Animal {
    public:
    void eat() {
        std::cout << "Animal is eating." << std::endl;
    }
};

// 应改为虚继承 virtual public Animal
class Mammal : public Animal {
    public:
    void breathe() {
        std::cout << "Mammal is breathing." << std::endl;
    }
};

// 应该为虚继承 virtual public Animal
class Bird : public Animal {
    public:
    void fly() {
        std::cout << "Bird is flying." << std::endl;
    }
};
// 菱形继承，同时从 Mammal 和 Bird 继承Animal
class Bat : public Mammal, public Bird {
    public:
    void navigate() {
    // 这⾥可能会引起⼆义性，因为 Bat 继承了两个 Animal
    // navigate ⽅法中尝试调⽤ eat ⽅法，但不明确应该调⽤ Animal 的哪⼀个实现
        eat();
    }
};
```

### 多态 虚函数 虚表

C++中的多态性是通过虚函数（virtual function）和虚函数表（vtable）来实现的。具体来说，在编译时，编译器判断出多态后（父类指针指向子类对象并调用虚函数），会**生成间接调用代码，该代码在对象的内存空间起始处取出vprt，利用vptr跳转到vtabel**，然后查找函数进行调用。

多态允许==**基类类型的指针或引⽤指向派⽣类对象，从而调用派生类的函数**==。

**虚函数表：**通常存储在==程序的全局或静态内存区域==中，其中==存储了指向实际函数的指针==，是一个函数指针数组。这个表在运⾏时⽤于动态查找调⽤的函数。

虚函数的查询依赖于对象的 `vtable`虚函数表和`vptr`虚表指针

**==this指针指向对象实例，对象实例中有vptr指针==（虚指针），vptr指针指向虚表，然后查虚表找调用的函数。**

而==静态函数不依赖于类的对象==，没有this指针。

**每个类一个虚表**：

- 每个具有虚函数的类都有一个虚表，用于**存储指向其虚函数的指针**。

**指向虚函数的指针**：

- 虚表的每个条目是一个指向该类的虚函数的指针。如果某个虚函数在派生类中被重写，则虚表中对应的条目会指向派生类的实现。

**`vptr` 指针**：

- 每个==对象实例都有一个指向其类虚表的指针==，通常称为 `vptr`。当创建对象时，构造函数会初始化这个指针，使其指向相应的虚表。

```c++
class Base {
public:
    virtual void show() {
        std::cout << "Base show" << std::endl;
    }
    virtual void display() {
        std::cout << "Base display" << std::endl;
    }
};

class Derived : public Base {
public:
    void show() override {
        std::cout << "Derived show" << std::endl;
    }
};
int main() {
    Base* b = new Derived();
    b->show();    // 输出: "Derived show"
    b->display(); // 输出: "Base display"
    delete b;
    return 0;
}
```

在这个例子中，`Base` 类的虚表可能看起来像这样：

```
vtable for Base:
- Base::show() -> 指向 Base::show 实现
- Base::display() -> 指向 Base::display 实现
```

而 `Derived` 类的虚表则可能如下：

```
vtable for Derived:
- Derived::show() -> 指向 Derived::show 实现  
- Base::display() -> 指向 Base::display 实现  (因为自己没实现， base实现了)
```

- 当创建 `Derived` 类的对象时，其 `vptr` 将指向 `Derived` 的虚表。这使得当调用 `b->show()` 时，程序能够根据 `vptr` 找到并调用 `Derived` 类的 `show` 方法。

- 因为 `b` 是 `Base*` 类型，但它实际上指向 `Derived` 对象，所以调用 `show()` 会执行 `Derived` 类的实现，而调用 `display()` 会执行 `Base` 类的实现。

### C++ 的重载和重写，以及它们的区别

#### 重载 overload

一个方法的不同版本

同⼀作⽤域内  ，相同的函数名但具有==不同的参数列表或类型==

#### 重写 override

==virtual和override是为了实现多态，也就是重写父类函数==

派⽣类==重新实现基类中的虚函数==   派生类中用 ` override`表示重写。

==重写⽅法的参数列表，返回值，所抛出的异常与被重写⽅法⼀致==

1. 静态⽅法不能被重写  即静态函数不可以是虚函数

### 纯虚函数 抽象类

1. ==抽象类不能被实例化==，⽬的是为了==提供接⼝==，供派⽣类继承和实现。

2. 派⽣类必须实现抽象类中的纯虚函数，否则它们也会成为抽象类。  

3. 抽象类中可以包含普通的成员函数、数据成员和构造函数（什么都可以有，就是不能被实例化），但它==必须包含⾄少⼀个纯虚函数==。

4. 在声明中使⽤ virtual 关键字并赋予函数⼀个 === 0== 的纯虚函数。  

```c++
class Shape {
public:
    virtual void draw() = 0; // 纯虚函数
};
class Circle : public Shape {
public:
    void draw() override {
        std::cout << "Drawing a circle." << std::endl;
    }
};
class Square : public Shape {
public:
    void draw() override {
        std::cout << "Drawing a square." << std::endl;
    }
};

int main() {
    // Shape s; // 错误：不能实例化抽象类
    Shape* shape1 = new Circle();
    Shape* shape2 = new Square();

    shape1->draw(); // 输出: Drawing a circle.
    shape2->draw(); // 输出: Drawing a square.

    delete shape1;
    delete shape2;
    return 0;
}
```

### 虚函数和纯虚函数的区别

**虚函数**

1. ==有实现==： 虚函数有函数声明和实现，即在基类中可以提供默认实现。

2. 可选实现： ==派⽣类可以选择是否覆盖虚函数==。如果派⽣类没有提供实现，将使⽤基类的默认实现。

3. ==允许实例化==： 虚函数的类可以被实例化。

4. 调⽤靠对象类型决定： 在运⾏时，根据对象的实际类型来决定调⽤哪个版本的虚函数。

**纯虚函数**

1. 没有实现

2. 强制覆盖： ==派⽣类必须提供纯虚函数的具体实现==，否则它们也会成为抽象类。

3. 禁⽌实例化： 包含纯虚函数的类⽆法被实例化，只能⽤于派⽣其他类。

4. ⽤` = 0 `声明。

5. 提供==接口规范==： 通过纯虚函数，抽象类提供⼀种接⼝规范，要求派⽣类提供相关实现

### 虚析构函数

也是==为了多态==。

虚析构函数是⼀个带有 `virtual `关键字的析构函数。 

用于确保在==删除基类指针指向的派生类对象时，正确调用派生类的析构函数==。

父类函数声明为`virtual`是好习惯

#### 为什么需要虚析构函数

动态多态依赖于虚函数，之后函数为虚函数时，才会从vptr跳到虚表。

当使用基类指针指向派生类对象并删除该指针时，如果基类的析构函数不是虚函数，只有基类的析构函数会被调用，而派生类的析构函数将不会被调用。这会导致以下问题：

- **资源泄漏**：派生类可能分配了动态内存或其他资源，如果析构函数未被调用，这些资源将不会被释放。
- **未定义行为**：如果派生类的析构函数中有重要的清理代码，未调用可能导致程序出现未定义行为。

```cpp
#include <iostream>

class Base {
public:
    // 虚析构函数
    virtual ~Base() {
        std::cout << "Base destructor called." << std::endl;
    }
};

class Derived : public Base {
public:
    // 资源分配
    Derived() {
        data = new int[10]; // 动态分配内存
        std::cout << "Derived constructor called." << std::endl;
    }

    ~Derived() {
        // 释放资源
        delete[] data; 
        std::cout << "Derived destructor called." << std::endl;
    }

private:
    int* data; // 指向动态分配的内存
};

int main() {
    Base* b = new Derived(); // 基类指针指向派生类对象
    delete b; // 正确调用 Derived 和 Base 的析构函数

    return 0;
}
```

在没有虚析构函数的情况下，调用 `delete` 仅会触发基类的析构函数，而不会调用派生类的析构函数。

==这是因为 C++ 的对象销毁机制是基于静态类型的，而不是动态类型的。==

* ==运行时查找==：当基类的析构函数被声明为虚函数后，编译器在编译时并不会直接调用基类的析构函数，而是通过对象的虚表（vtable）在运行时进行动态查找。

* 当使用 `delete` 删除一个指向派生类的基类指针时，程序会按照以下步骤进行：
  
  1. **调用派生类的析构函数**：
     
     - 由于基类的析构函数是虚的，程序会首先根据对象的动态类型调用 `Derived` 类的析构函数。
     - 这确保了 `Derived` 类中的所有资源和清理逻辑会被执行
  
  2. **调用基类的析构函数**：
     
     * 然后，程序会回到基类，调用 `Base` 类的析构函数。
     
     * 这确保了 `Base` 类中的资源和清理逻辑也会被执行。

### 为什么要虚析构，为什么不能虚构造

**为什么需要虚析构函数？**

1. 虚析构函数允许在运⾏时根据对象的实际类型调⽤正确的析构函数，从⽽==实现多态性==。

2. 如果基类的析构函数不是虚的，当通过基类指针删除指向派⽣类对象的对象时，只会调⽤基类的析构函数，⽽不会调⽤派⽣类的析构函数。这可能==导致派⽣类的资源未被正确释放，造成内存泄漏==。

**为什么不需要虚构造**

构造函数在对象的创建阶段被调⽤，==对象的类型在构造前中已经确定==。因此，**构造函数调⽤不涉及多态性**。

另外，对象未创建时，没有this指针，也就没有vptr（指向虚表的指针），也就没办法动态查询虚表。

### 哪些函数不能是虚函数

**构造函数**

- ==对象类型确定，不涉及多态性；没有this指针，没有vptr指向虚表==。

**静态成员函数**

* 为静态函数不依赖于类的实例，没有 `this` 指针，无法查虚表。

**友元函数**

- 友元函数不是类的成员函数，因此不能被声明为虚函数。友元函数在类外部定义，并==不属于任何类的作用域==。

**内联成员函数**

内联函数就是为了在代码中直接展开，减少函数调⽤花费的代价。

inline函数在==编译时==被展开（是==静态==的），虚函数在==运⾏时才能动态==的绑定函数（依赖于this指针，运行时查询） 。

### 静态 动态 多态

简而言之 **编译期和执行期**  

**多态就是多种状态  多种实现 从多个选一个**

静态多态和动态多态 ：**函数重载、模板**是静态多态，编译期检查；**虚函数是动态多态** ，运行期检查

**静态**（Static）

1. **定义时间**：
   - 静态特性在编译时确定，程序==运行时不会改变==。
2. **内存分配**：
   - 静态内存分配是在程序==编译时完成==的，分配的内存量在程序运行期间是固定的。
   - 例如，静态变量、全局变量和静态数组在程序加载时分配内存。
3. **作用域**：
   - 静态变量的==作用域通常是其声明的块或文件==，生命周期与程序的执行周期相同。
4. **多态性**：
   - ==静态多态性通常通过函数重载和模板实现==，在编译时决定调用哪个函数。

**动态**（Dynamic）

1. **定义时间**：
   
   - 动态特性在运行时确定，程序的状态和行为可以在运行时发生变化。

2. **内存分配**：
   
   - 动态内存分配是在程序==运行时进行==的，允许在运行时根据需要分配和释放内存。
   - 例如，使用 `new` 和 `delete`（或 `malloc` 和 `free`）动态分配的内存。

3. **作用域**：
   
   - 动态变量的==作用域可以在运行时进行管理==，通常是由指针或引用控制的，通过指针和引用跨作用域。

4. **多态性**：
   
   - ==动态多态性是通过虚函数实现的==，具体调用的函数在运行时根据对象的实际类型决定。

### 什么是构造函数和析构函数？

#### 1. 构造函数

构造函数是在创建对象时⾃动调⽤的特殊成员函数。它的==主要⽬的是初始化对象的成员变量==，为对象分配资源，执⾏必要的初始化操作。构造函数的特点包括：

* 函数名与类名相同： 构造函数的函数名必须与类名相同，且没有返回类型，包括 void。

* 可以有多个构造函数： ⼀个类可以有多个构造函数，它们可以根据参数的类型和数量不同⽽==重载==。

* 默认构造函数： 如果没有为类定义任何构造函数，编译器会⾃动⽣成⼀个默认构造函数。默认构造函数没有参数，也可能执⾏⼀些默认的初始化操作。

```c++
class MyClass {
public:
    // 默认构造函数
    MyClass() {
    // 初始化操作
    }

    // 带参数的构造函数
    MyClass(int value) {
    // 根据参数进⾏初始化操作
    }
};
```

#### 2.析构函数

析构函数是在对象⽣命周期结束时⾃动调⽤的特殊成员函数。它的==主要⽬的是释放对象占⽤的资源==、执⾏必要的清理操作。析构函数的特点包括：

* 函数名与类名相同，前⾯加上波浪号 ~ ： 析构函数的函数名为 ~ClassName ，其中 ClassName 是类名。

* 没有参数： 析构函数==没有参数，不能重载，每个类只能有⼀个析构函数==。

* 默认析构函数： 如果没有为类定义任何析构函数，编译器会⾃动⽣成⼀个默认析构函数，执⾏简单的清理操作。

```c++
class MyClass {
public:
    // 析构函数
    ~MyClass() {
    // 清理操作，释放资源
    }
};
```

### C++构造函数有⼏种，分别什么作用

1. 默认构造函数：

**==没有任何参数==的构造函数。如果⽤户没有为类定义构造函数，编译器会⾃动⽣成⼀个默认构造函数。**

默认构造函数不会对基本数据类型（如int） 初始化，因此这些==变量可能是未定义的值==。 

2. ==带参==数的构造函数  
   
   创建对象时提供初始化值。

3. ==复制构造函数==
   
   它接受一个对象的引用作为参数。

4. ==委托构造函数==

​    在⼀个构造函数中调⽤同类的另⼀个构造函数，减少代码重复。  

```c++
class MyClass {
public:
    int x, y;
    // 委托构造函数
    MyClass() : MyClass(0, 0) { // 调用带参数的构造函数
        // 其他初始化代码
    }
    MyClass(int a, int b) : x(a), y(b) {
        // 初始化代码
    }
};
```

5. ==移动==构造函数（Move Constructor）

**定义**：移动构造函数是一个构造函数，用于通过==移动资源的方式初始化对象==，而不是通过复制。它**接受一个右值引用**作为参数，适用于优化资源管理，特别是在处理动态分配的内存时。

##### 示例代码

```cpp
#include <iostream>
#include <string>
#include <utility> // for std::move

class MyClass {
public:
    int* data;

    // 参数化构造函数
    MyClass(int size) {
        data = new int[size];
        std::cout << "Parameterized Constructor called" << std::endl;
    }

    // 拷贝构造函数
    MyClass(const MyClass& other) {
        data = new int[1]; // 简化示例，实际应根据size分配
        *data = *other.data;
        std::cout << "Copy Constructor called" << std::endl;
    }
    // 赋值运算符
    MyClass& operator=(const MyClass& other) {
        if (this != &other) {  // 防止自赋值
            value = other.value;
            std::cout << "Copy Assignment (" << value << ")\n";
        }
        return *this;
    }

     //拷贝构造函数用于始化新对象（如 A a = b; 或 A a(b);）。
    //赋值运算符用于修改已有对象（如 a = b;）。

    // 移动构造函数
    MyClass(MyClass&& other) noexcept : data(other.data) {
        other.data = nullptr;
        std::cout << "Move Constructor called" << std::endl;
    }

    // 析构函数
    ~MyClass() {
        delete data;
        std::cout << "Destructor called" << std::endl;
    }
};

int main() {
    MyClass obj1(10);           // 调用参数化构造函数
    MyClass obj2 = std::move(obj1); // 调用移动构造函数
    // 注意：obj1现在处于有效但未定义的状态
    return 0;
}
```

### 友元函数和友元类

面试简答：

友元提供了**不同类的成员函数之间**、**类的成员函数和一般函数之间**进行数据共享的机制。

==通过友元，一个函数或者另一个类中的成员函数可以访问类中的私有成员和保护成员==。

友元的正确使用能提高程序的运行效率，但同时也破坏了类的封装性和数据的隐藏性，导致程序可维护性变差。

**友元函数**

友元函数是定义在类外的普通函数，不属于任何类，可以访问类的私有成员。但是需要在类的定义中声明所有可以访问它的友元函数。

```c++
#include <iostream>
using namespace std;
class A
{
 public:
    friend void set_show(int x, A &a); //该函数是友元函数的声明
 private:
    int data;
};
void set_show(int x, A &a) //友元函数定义，为了访问类A中的成员
{
    a.data = x;
    cout << a.data << endl;
}
int main(void)
{
    class A a;
    set_show(1, a);
    return 0;
}
```

一个函数可以是多个类的友元函数，但是每个类中都要声明这个函数。  

**友元类**

友元类的所有成员函数都是另一个类的友元函数，都可以访问另一个类中的隐藏信息（包括私有成员和保护成员）。

类里面要相应的进行声明。

```c++
class A
{
public:
    friend class C; //这是友元类的声明
private:
    int data;
};
class C //友元类定义，为了访问类A中的成员
{
public:
    void set_show(int x, A &a) { a.data = x; cout<<a.data<<endl;}
};
int main(void)
{
    class A a;
    class C c;
    c.set_show(1, a);
    return 0;
}
```

**使用友元类时注意：**

(1) 友元关系不能被继承。

(2) 友元关系是单向的，不具有交换性。若类B是类A的友元，类A不一定是类B的友元，要看在类中是否有相应的声明。

(3) 友元关系不具有传递性。若类B是类A的友元，类C是B的友元，类C不一定是类A的友元，同样要看类中是否有相应的申明

**相同class的各个函数互为友元**

![image-20241011201653100](./image/c++%E7%9F%A5%E8%AF%86%E7%82%B9_image/image-20241011201653100.png)

### 深拷贝和浅拷贝

主要是类的成员变量含有指针时，是否拷贝指针指向的数据。

**浅拷贝**

浅拷贝复制源对象的成员值。**如果成员是指针，则仅复制指针的值（即地址），不复制指针所指向的数据。**

复制后的对象和原对象**共享相同的动态内存。**

可能导致多个对象尝试释放同一块内存，从而引发**悬空指针或双重释放**等问题。

**深拷贝**

复制源对象的所有成员值。如果成员是指针，则不仅复制指针的值，还==会复制指针所指向的数据==。

复制后的对象和原对象**拥有各自独立的内存拷贝**。

**删除一个对象时不会影响另一个对象**，避免了悬空指针和双重释放的问题。

### 运算符重载

面试简答：

自定义对象的运算符行为，使得用户定义的类型==（类）可以像内置类型一样使用运算符==

- 重载的运算符可以是基本的算术运算符（如 `+`、`-`、`*`、`/`）以及其他如 `==`、`!=`、`[]` 等运算符。

运算符重载可以通过以下两种方式实现：

- **成员函数**：当第一个操作数是类的对象时，运算符重载通常作为类的成员函数实现。
- **友元函数**：当第一个操作数不是类的对象时，可以使用友元函数。

**重载+ 利用成员函数**

```cpp
#include <iostream>

class Complex {
public:
    double real;
    double imag;

    Complex(double r, double i) : real(r), imag(i) {}

    // 重载加法运算符
    // 第一个参数为this
    Complex operator+(const Complex& other) const {
        return Complex(real + other.real, imag + other.imag);
    }

    void display() const {
        std::cout << real << " + " << imag << "i" << std::endl;
    }
};

// 重载 + 运算符，允许与内置类型相加
Complex operator+(const Complex& c, double d) {
    return Complex(c.real + d, c.imag);
}

int main() {
    Complex c1(1.0, 2.0);
    Complex c2(3.0, 4.0);
    Complex c3 = c1 + c2; // 使用重载的 + 运算符
    Complex c4 = c1 + 5.0;   // 与内置类型相加

    c3.display(); // 输出: 4 + 6i
    return 0;
}
```

**重载<<  利用友元函数**

```cpp
#include <iostream>

class Point {
public:
    int x, y;

    Point(int xVal, int yVal) : x(xVal), y(yVal) {}

    // 友元函数重载输出运算符
    friend std::ostream& operator<<(std::ostream& os, const Point& p) {
        os << "(" << p.x << ", " << p.y << ")";
        return os;
    }
};

int main() {
    Point p(3, 4);
    std::cout << "Point: " << p << std::endl; // 输出: Point: (3, 4)
    return 0;
}
```

## 内存管理

<img src="./image/c++%E7%9F%A5%E8%AF%86%E7%82%B9_image/image-20241027232812323.png" alt="image-20241027232812323" style="zoom:67%;" />

### 堆和栈的区别

面试简答：

1，是虚拟内存的两个不同的区 堆向上 栈向下生长

2，分配方式：栈由系统自动分配，存放局部变量、形参、返回值   ；堆手动使用库函数申请（由动态内存分配器 管理空闲空间链表）

3，大小限制：栈的大小是设置好的  一般4M ； 堆空间很大 1-4G

4，空间连续性：栈连续 ；堆由于申请释放等 有很多碎片 不连续

5，效率：自动的效率高；手动分配效率低，且还要管理空闲空间，肯定慢

### 如何构造一个只能存储在栈或者堆中的类

这种控制通常通过管理构造函数、析构函数的访问权限以及重载 `new` 和 `delete` 操作符来实现。

#### 只在栈分配

要创建一个只能在栈上分配的类，需要阻止在堆上的分配。实现方法是将 `operator new` 和 `operator delete` 设为私有。

```
class StackOnly {
private:
    // 禁止使用 new 操作符
    void* operator new(size_t size);
    void operator delete(void* ptr);
}
```

#### 只在堆分配

##### 方法一

将**析构函数设为私有**能阻止在栈上分配对象，这涉及到 C++ 对象生命周期管理的机制RAII。

当我们在栈上创建一个对象时，编译器会自动管理该对象的生命周期：

1. 当进入对象的作用域时，构造函数被调用
2. 当离开对象的作用域时，析构函数被自动调用

如果析构函数是私有的，那么在栈上分配类对象时会编译不通过。

##### 方法二

禁用拷贝构造和赋值操作符，并**将构造函数设为私有**

通过静态成员函数，在堆上创建实例。

```
class HeapOnly2 {
private:
    // 私有构造函数
    HeapOnly2() {}

    // 禁用拷贝和赋值
    HeapOnly2(const HeapOnly2&) = delete;
    HeapOnly2& operator=(const HeapOnly2&) = delete;

public:
    ~HeapOnly2() {}

    // 静态工厂方法
    static HeapOnly2* createInstance() {
        return new HeapOnly2();
    }

    // 其他公共成员...
};
```

### 结构体内存对齐

==对齐本质是空间换时间==   满足不同平台对合法地址的要求，使数据尽量一次访问得到。因为寄存器一般是8/ 32 /64位的  

结构体内成员**按照声明顺序存储**，第一个成员地址和整个结构体地址相同。

结构体的大小计算遵循结构体的对齐规则：

1. **成员变量要==对齐到对齐数的整数倍的地址处==。** 对齐数即成员类型占用字节数。

2. **结构体的总大小为最大对齐数的整数倍**。

3. **嵌套时：外层结构体的对齐值 = 所有成员（包括嵌套结构体）的最大对齐值**

   ```c++
   struct Inner {
       char a;     // 1 字节
       int b;      // 4 字节
   };  // Inner 的对齐值 = max(1, 4) = 4，大小 = 1 + 3(padding) + 4 = 8
   
   struct Outer {
       char c;     // 1 字节
       Inner inner;// 8 字节（对齐到 4）
       double d;   // 8 字节（对齐到 8）
   };  // Outer 的对齐值 = max(1, 4, 8) = 8，大小 = 1 + 7(padding) + 8 + 8 = 24
   ```

   

<img src="./image/c++%E7%9F%A5%E8%AF%86%E7%82%B9_image/image-20241025112547035.png" alt="image-20241025112547035" style="zoom: 50%;" />

<img src="./image/c++%E7%9F%A5%E8%AF%86%E7%82%B9_image/image-20241025112646329.png" alt="image-20241025112646329" style="zoom:50%;" />

c++11以后引入两个关键字 **alignas与 alignof**。其中alignof可以计算出类型的对齐方式，alignas可以指定结构体的对齐方式

要修改编译器的默认对齐数，我们需要借助于以下预处理命令：

```c
#pragma pack(4)  //修改为4
```

### new和malloc

面试简答：

关键字 库函数；指定大小； 返回的类型； 失败的处理方式；内部实现（new、delete利用malloc、free）**

1、 new/delete是C++==关键字==，需要编译器支持。malloc/free是==库函数==，需要头文件支持；

2、 使用new操作符申请内存分配时无须指定内存块的大小，编译器会根据类型信息自行计算。而malloc则需要==显式地指出所需内存的尺寸==。

```c++
int* arr = (int*)malloc(10 * sizeof(int));
```

3、 new操作符内存分配成功时，**返回的是对象类型的指针，类型严格与对象匹配**，无须进行类型转换，故new是符合**类型安全**性的操作符。而malloc内存==分配成功则是返回void *== ，需要通过==强制类型转换==，将void*指针转换成我们需要的类型。

4、 new内存分配失败时，会抛出std::bac_alloc异常。malloc==分配内存失败时返回NULL==。

5、**实现原理：** 

new会先==调用operator new函数==，申请足够的内存（底层使用malloc实现，根据类的成员变量类型、是否有虚函数等来计算类大小）。

然后进行类型转换，将void*转换为特定类型。

之后==调用类型的构造函数==，初始化成员变量，如果类含指针变量，构造函数一般还会给指针申请内存，最后返回自定义类型指针。

delete==先调用析构函数==，如果含指针成员，析构函数会先释放指针成员的内存，

然后==调用operator delete函数释放内存==（通常底层使用free实现）。

**即除了分配内存空间之外，new和delete会调用类型的构造和析构函数，因此是类型安全的**

malloc/free是库函数，只能==动态的申请和释放内存==，无法强制要求其做自定义类型对象构造和析构工作。

### 除了malloc/free为什么需要new和delete

主要是为了服务非基本数据类型，比如**class**，这些对象**除了内存空间还需要构造和析构**，malloc是库中编译好的，不具有这个功能

### 内存泄漏 如何避免

动态分配了内存但没有及时释放； 可能会导致耗尽内存 程序崩溃

**常见的类型**

1. ==堆内存==泄漏 （Heap leak）  

malloc,realloc new等分配的内存没有释放

2. ==系统资源泄露==（Resource Leak）  
   
   系统分配的资源如 SOCKET 等没有使⽤相应的函数释放掉，导致系统资源的浪费

3. 涉及多态时，没有将==基类的析构函数定义为虚函数==  

**如何避免**

1. 及时释放内存

2. 利用智能指针 （如 `std::unique_ptr` 和 `std::shared_ptr`），它们会自动管理内存

3. RAII（Resource Acquisition Is Initialization）原则：==对象的构造函数中获取资源，并在析构函数中释放资源==

4. 避免循环引用 ：循环引用的资源无法释放

### 智能指针

参考c11新特性

### 野指针 悬挂指针

**野指针是指尚未初始化的指针**，既不指向合法的内存空间，也没有使用 NULL/nullptr 初始化指针

```
int* ptr;  // ptr 是野指针，未初始化
```

野指针从一开始就处于无效状态，而悬挂指针是指在对象生命周期结束后仍然持有无效地址的指针。

**悬空指针**是指 **指针指向的内存空间已被释放**或不再有效。

#### 悬空指针的几种情况：

1. 释放指针资源后，未重置

```c++
int* ptr = new int;
// 使⽤ ptr 操作内存
delete ptr;
// 此时 ptr 没有被置为 nullptr 或新的有效地址，成为野指针
// 避免： 
delete ptr; 
ptr = nullptr;  //重置
```

2. 超出了变量的作用范围。（与3类似）

```c++
int *p;
{
    int tmp = 10;
    p = &tmp;
}
//p 在此处成为悬空指针
```

3. 指向局部变量

```c++
int* ptr;
int* createInt() {
    int x = 10;
    return &x; // x 是局部变量，函数结束后 x 被销毁，返回的指针成为野指针
}    
ptr=createInt();
std::cout << *ptr;  // x已超出作用域，产生野指针
```

#### **如何避免**

1. 始终在声明时初始化指针
2. 释放内存后将指针置为 nullptr  
3. 避免返回局部变量的地址
4. **使用智能指针**:使用 `std::unique_ptr` 或 `std::shared_ptr` 来管理内存
5. **使用 RAII 原则**：通过构造函数和析构函数自动管理资源

### 类对象的如何存储的

##### 类对象的基本内存布局

在 C++ 中，一个类对象的内存布局主要由以下部分组成：

1. **成员变量**：直接存储在对象的内存空间中
2. **虚函数表指针**（如果有虚函数），虚指针在类对象的内存的最前面
3. **对齐填充**（为满足内存对齐要求）

##### 成员函数的存储

成员函数与对象的内存布局关系：

- **普通成员函数**：不存储在对象内部，而是在代码段中只存储一份
- **虚函数**：对象内部存储指向虚函数表的指针（通常是对象内存的前 4 或 8 字节）
- **静态成员函数**：不属于任何对象，不影响对象大小

空类通常是一个字节

### 内存泄漏检测工具

**Valgrind (Memcheck)**：Valgrind 是一个强大的动态分析工具，其中的 Memcheck 工具可以检测内存泄漏、非法内存访问等问题。它适用于 Linux 和 macOS 系统。

## STL

### 什么是STL，包含哪些组件

STL（Standard Template Library）是 C++ 标准库中的一个重要部分

⼴义上讲， STL分为3类： Algorithm（算法）、 Container（容器）和Iterator（迭代器  ）

详细的说， STL由6部分组成：**容器(Container)、算法（Algorithm）、 迭代器（Iterator）、仿函数（Function object）、适配器（Adaptor）、空间配置器（Allocator）。**  

#### 容器

**序列容器（Sequence Containers）**：vector（动态数组） deque（双端队列）  list（双向链表） array（固定大小的数组）forward_list（前向链表）

**关联容器（Associative Containers）**：set map multiset(允许元素重复) multimap(允许键重复) unordered_set unordered_map

#### 容器适配器

stack queue                             priority_queue  （**默认排序**：使用最大堆（最大优先队列））

#### 算法

sort()  reverse()

find() binary_find() find_if() 

transform() for_each()  

count() accumulate()

#### 迭代器

容器和算法的胶合剂

可以被视为指向容器中元素的“指针” 用于遍历和访问 STL 容器. 

 其实现了对operator* , operator-> , operator++,operator–等指针相关操作的重载。

#### 仿函数

STL 的仿函数（Functor）是一个**可以像函数一样被调用的类对象**。

换句话说，仿函数是一个重载了 `operator()` 的类或结构体，使得类可以接受参数。

STL 算法可以接受仿函数作为参数

下面是两个例子

```c++
// 自定义比较仿函数
class Compare {
public:
    bool operator()(int a, int b) const {
        return a > b; // 降序排序
    }
};

int main() {
    std::vector<int> vec = {5, 3, 1, 4, 2};

    // 使用 std::sort 和比较仿函数
    std::sort(vec.begin(), vec.end(), Compare());

    // 打印排序后的结果
    std::cout << "Sorted in descending order: ";
    for (int i : vec) {
        std::cout << i << " "; // 输出: 5 4 3 2 1
    }
    std::cout << std::endl;

    return 0;
}
```

```c++
// 定义仿函数  
class Multiply {
public:
    Multiply(int factor) : factor_(factor) {}

    int operator()(int input) const {
        return input * factor_;
    }

private:
    int factor_;
};

int main() {
    std::vector<int> numbers = {1, 2, 3, 4, 5};

    // 创建仿函数对象
    Multiply multiplyByThree(3);

    // 使用仿函数
    for (int num : numbers) {
        int result = multiplyByThree(num); // 调用仿函数
        std::cout << num << " * 3 = " << result << std::endl;
    }

    return 0;
}
```

#### 空间配置器

用于管理**动态内存分配和释放**的类模板  

std:alloctor一般含有两个函数allocate与deallocte，这两个函数分别调⽤operator new()与delete()，这两个函数的底层⼜分别是malloc()and free( ）

### pair容器

保存两个数据成员，⽤来⽣成特定类型的模板。使⽤： **pair<T1, T2>p;**

**内部定义：**

```
namespace std {
    template <typename T1, typename T2>
    struct pair {
        T1 first;
        T2 second;
    };
}
```

pair在**底层被定义为⼀个struct**，其所有成员默认是public，**两个成员分别是first和second**  

map的元素是pair  即 pair<const key_type， mapped_type>

### vector的实现

==动态数组 连续 插入效率O(n)  倍数扩容  reserve==

std::vector 是C++标准库中的⼀个动态数组实现，它的实现原理==基于数组==数据结构。

实现通常包含⼀个==指向数组起始位置的指针、数组的⼤⼩和容量等信息==。

* **随机访问**：通过索引直接计算地址（如`arr[i]`的地址 = 基地址 + i × 元素大小），时间复杂度 O(1)。

- **插入/删除**：需移动后续元素（平均时间复杂度 **O(n)**）。

在**尾部进⾏插⼊和删除操作时，只需调整尾部指针**，不需要移动整个数据块。

当元素数量达到当前内存块的容量时， `std::vector` 会**申请**⼀个更⼤的内存块，将元素从旧的内存块**复制**到新的内存块，并**释放**旧的内存块。

由于数组的连续内存结构，通过索引进⾏访问时可以通过指针运算实现常数时间复杂度的随机访问。

```c++
template <class T, class Allocator = std::allocator<T>>
class vector {
private:
    T* elements; // 指向数组起始位置的指针
    size_t size; // 当前元素数量
    size_t capacity; // 当前分配的内存块容量
}
```

#### **扩容机制**

- 当向 `vector` 中添加元素时，**如果当前容量不足以容纳更多的元素**，`vector` 会执行一次 **扩容**。

- **扩容时，`vector` 会申请更多的内存空间**，通常会将容量增加到 **原容量的两倍**。这种增长策略有助于减少内存重新分配的次数，从而提高性能。随着元素逐渐增多，`vector` 扩容的频率会降低，导致总体的时间复杂度接近 **O(1)**（均摊时间复杂度）。
* 扩容时，原来的内存会被销毁，并且所有的元素会被复制到新分配的内存区域。

* 如果你事先知道大致需要多少元素，可以使用 `vector::reserve()` 方法来预先分配足够的内存，从而避免频繁扩容。

```c++
std::vector<int> vec;
vec.reserve(1000); // 预分配足够的内存，避免频繁扩容
```

### list的实现

双向链表 **维护head tail指针和链表大小**

```c++
#include <iostream>

template<typename T>
class MyList {
private:
    struct Node {
        T data;
        Node* prev;
        Node* next;

        Node(const T& value) : data(value), prev(nullptr), next(nullptr) {}
    };

    Node* head;
    Node* tail;
    size_t size_;

public:
    MyList() : head(nullptr), tail(nullptr), size_(0) {}

    ~MyList() {
        clear();
    }

    void push_back(const T& value) {
        Node* newNode = new Node(value);
        if (tail) {
            tail->next = newNode;
            newNode->prev = tail;
            tail = newNode;
        } else {
            head = tail = newNode;
        }
        ++size_;
    }

    void pop_back() {
        if (tail) {
            Node* temp = tail;
            tail = tail->prev;
            if (tail) {
                tail->next = nullptr;
            } else {
                head = nullptr; // List is now empty
            }
            delete temp;
            --size_;
        }
    }

    void clear() {
        while (head) {
            pop_back();
        }
    }

    size_t size() const {
        return size_;
    }

    bool empty() const {
        return size_ == 0;
    }
};
```

### vector和list的区别

1.  **内存分配连续**/不连续  

2. 支持/不支持**随机访问**

   数组支持**随机访问**：通过索引直接计算地址（如`arr[i]`的地址 = 基地址 + i × 元素大小），时间复杂度 **O(1)**。

   链表必须**顺序访问**：必须从头节点开始逐个遍历，时间复杂度 **O(n)**。

3. **插入删除的效率**
   vector：尾部进⾏插⼊/删除操作较快，但在中间或头部进⾏插⼊/删除需移动后续元素（平均时间复杂度 **O(n)**）。

   list:  在任意位置进⾏插⼊/删除操作都是常数时间复杂度，只需修改指针（时间复杂度 **O(1)**

4. 内存：vector需要预估元素数量 分配跟大的空间；**list不需要预分配**

### push_back 和 emplace_back 的区别

都是尾部插入  

`push_back`

- **参数**：需要==传递一个已经构造好的对象==（通常是引用）。
- **构造过程**：如果传递的是一个临时对象，`push_back`会调**用拷贝构造函数**来将对象添加到容器中。

```c++
#include <vector>
#include <iostream>

struct Point {
    int x, y;
    Point(int x, int y) : x(x), y(y) {}
};

int main() {
    std::vector<Point> vec;
    Point p(1, 2);
    vec.push_back(p); // 通过拷贝构造函数
    vec.push_back(Point(3, 4)); // 先构造临时对象 再移动构造   （临时对象是右值 push_back参数是右值时，会调移动构造函数
}
```

`emplace_back`

- **参数**：==接受对象构造所需的参数==，而不需要传递一个完整的对象。
- **构造过程**：**直接在容器内部构造对象**，避免了不必要的拷贝或移动。

```c++
#include <vector>
#include <iostream>

struct Point {
    int x, y;
    Point(int x, int y) : x(x), y(y) {}
};

int main() {
    std::vector<Point> vec;
    vec.emplace_back(1, 2); // 直接在容器中构造
    vec.emplace_back(3, 4); // 直接在容器中构造
}
```

- **性能**：`emplace_back`通常**更高效**，因为它直接在容器中构造对象，避免了拷贝。
- **使用场景**：==如果你已经有一个对象，可以使用`push_back`；如果要创建一个新对象，可以使用`emplace_back`==。

在大多数情况下，推荐使用`emplace_back`，特别是在构造复杂对象时，可以提高效率和减少代码复杂度。

### deque的实现

通常是**通过分块（block）来分配空间**  块与块不连续  数组块可以动态分配和释放。

每个数组块的大小是固定的，`deque` 维护一个指向这些数组块的指针数组`map`。

<img src="./image/c++%E7%9F%A5%E8%AF%86%E7%82%B9_image/image-20241028200437977.png" alt="image-20241028200437977" style="zoom:50%;" />

要想实现迭代器的功能，必须先解决如下 2 个问题：

1. 迭代器在遍历 deque 容器时，必须能够确认各个连续空间在 map 数组中的位置；
2. 迭代器在遍历某个具体的连续空间时，必须能够判断自己是否已经处于[空间的边缘](https://zhida.zhihu.com/search?content_id=213224471&content_type=Article&match_order=1&q=空间的边缘&zhida_source=entity)位置。如果是，则一旦前进或者后退，就需要跳跃到上一个或者下一个连续空间中。

为了实现遍历 deque 容器的功能，deque 迭代器定义了如下的结构：

```text
template<class T,...>
struct __deque_iterator{
    ...
    T* cur;
    T* first;
    T* last;
    map_pointer node;//map_pointer 等价于 T**
}
```

可以看到，迭代器内部包含 4 个指针，它们各自的作用为：

- cur：指向当前正在遍历的元素；
- first：指向当前连续空间的首地址；
- last：指向当前连续空间的末尾地址；
- node：它是一个二级指针，用于指向 map 数组中存储的指向当前连续空间的指针。

<img src="./image/c++%E7%9F%A5%E8%AF%86%E7%82%B9_image/image-20241028200732886.png" alt="image-20241028200732886" style="zoom:50%;" />

### stack和queue

底层是以deque为底部架构  

```
template <class T, class Container = deque<T>>  class stack;
template <class T, class Container = deque<T>>  class queue;
```

### 哈希表

基于**哈希函数**（Hash Function）实现的 数据结构，它提供O(1)的增删改查

哈希表通常由以下两部分组成：

- **哈希数组（Hash Array）**：通常是一个固定大小的数组，用来**存储哈希值所对应的数据**，哈希数组上记录了自己键值对。
- **哈希函数（Hash Function）**：一个函数，将输入的关键字（key）映射到数组的索引。

当 **哈希冲突** 发生时，意味着不同的 **键（key）** 被哈希到相同的索引位置上。例如，假设你有一个哈希表的大小为 10，且使用哈希函数 `key % 10` 来计算索引。那么：

- 对于 `key = 25`，`25 % 10 = 5`
- 对于 `key = 35`，`35 % 10 = 5`

这两个不同的键 `25` 和 `35` 都映射到了哈希表的 **索引 5**，因此就发生了哈希冲突。

解决哈希冲突的方式有几种常见的技术，最常见的两种是 **链地址法（Chaining）** 和 **开放地址法（Open Addressing）**。

1. **链地址法（Chaining）**

在链地址法中，每个哈希表的槽（即数组的位置）都包含一个链表（或其他数据结构，如平衡树、动态数组等）。当发生冲突时，将所有具有相同哈希值的元素（即具有相同数组索引的元素）链接在一起。这种方法的优势是不会影响其他槽的使用，甚至可以解决**严重的哈希冲突**。

2. **开放地址法（Open Addressing）**

开放地址法在哈希表的某个位置发生冲突时，使用探测（probing）技术寻找其他空闲的位置。常见的探测方法包括：

- **线性探测（Linear Probing）**：逐个检查相邻位置，直到找到一个空位。
- **二次探测（Quadratic Probing）**：使用平方级数的步长进行探测。
- **双重哈希（Double Hashing）**：使用两个哈希函数进行探测。

### heap && priority_queue

heap（堆） ：

建⽴在完全⼆叉树上，分为两种，大根堆，小根堆。

在STL中做priority_queue的助⼿，即，以任何顺序将元素推⼊容器中，然后**取出时⼀定是从优先权最⾼的元素开始取**，完全⼆叉树具有这样的性质，适合做priority_queue的底层

priority_queue:

**默认排序**：使用最大堆（最大优先队列）

### map && set的区别和实现原理

都是C++的关联容器，底层都是采⽤红⿊树实现，**默认升序排列**。 **查找是 logn的复杂度**

map内部实现了⼀个红⿊树（红⿊树是⾮严格平衡的⼆叉搜索树，⽽AVL是严格平衡⼆叉搜索树），红⿊树有⾃动排序的功能，因此**map内部所有元素都是有序的**，红⿊树的每⼀个节点都代表着map的⼀个元素。

因此，对于map进⾏的查找、删除、添加等⼀系列的操作都相当于是对红⿊树进⾏的操作。

 map中的元素是按照⼆叉树（⼜名⼆叉查找树、⼆叉排序树）存储的，特点就是左⼦树上所有节点的键值都⼩于根节点的键值，右⼦树所有节点的键值都⼤于根节点的键值。**使⽤中序遍历可将键值按照从⼩到⼤遍历出来**  

### unordered_set和unordered_map

基于哈希表实现

### 迭代器失效的情况

**迭代器本质上是一种"智能指针"**，它封装了指向容器中元素的原始指针或偏移量。

当这些指针或偏移量所指向的内存位置不再有效时，迭代器就失效了。

迭代器失效的两种主要情况：

1. **地址变化**：元素在内存中的物理位置改变
2. **元素删除**：元素被删除，其占用的内存被释放

#### 顺序容器迭代器失效情况

对容器插入或删除时，因此容器重新分配内存，则会使原来定义的迭代器失效。

std::vector 和 std::string

- 插入元素时：
  - 如果容量不变（未触发重新分配），则插入点之前的迭代器有效，插入点及之后的迭代器失效
  - 如果容量变化（触发重新分配），则所有迭代器和引用都失效
- **删除元素时**：被删除元素之前的迭代器有效，被删除元素及之后的迭代器失效

```
td::vector<int> vec = {1, 2, 3, 4, 5};
auto it = vec.begin() + 2; // 指向元素 3
vec.push_back(6);  // 可能导致重新分配，使所有迭代器失效
// 此时使用 it 是不安全的

// 安全做法
it = vec.begin() + 2;  // 重新获取迭代器
```

#### 关联容器迭代器失效情况

对于std::map和std::set，使用红黑树实现：

在红黑树中，每个节点都是独立分配的内存对象，通过指针相互连接：

```
    B
   / \
  A   C
     / \
    D   E
```

这种结构中，当插入新节点时，只需要调整相关节点的指针关系，而不需要像 vector 那样移动或重新分配整个数据块。

对于unordered_map和unordered_set使用哈希表实现，插入没有触发重新哈希，迭代器就不会失效。





## c11新特性

### 智能指针

智能指针是**C++标准库中提供的一种资源管理工具**

主要用于自动管理动态分配的内存。它们封装了原始指针，并在超出作用域时自动释放内存。

**清晰的所有权**：通过智能指针，可以清晰地表达资源的所有权关系。

1. **`std::unique_ptr`**：
   * **特点**：独占所有权，不能被复制，但可以转移（移动）。

```c++
class MyClass {
public:
    MyClass() { std::cout << "MyClass constructed\n"; }
    ~MyClass() { std::cout << "MyClass destroyed\n"; }
    void sayHello() { std::cout << "Hello from MyClass!\n"; }
};

std::unique_ptr<MyClass> ptr = std::make_unique<MyClass>();
//或者是 std::unique_ptr<MyClass> ptr(new MyClass());  // C++11 方式
std::unique_ptr<MyClass> ptr2 = std::move(ptr1); // 转移所有权  
```

2. **`std::shared_ptr`**：
   * **特点**：允许多个智能指针共享同一个资源，通过引用计数来管理资源的生命周期。

```c++
std::shared_ptr<MyClass> ptr1 = std::make_shared<MyClass>();

std::shared_ptr<MyClass> ptr2 = ptr1; // 共享同一对象
```

3. **`std::weak_ptr`**：
   * **特点**：与 `std::shared_ptr` 配合使用，提供对共享资源的非拥有引用，不增加引用计数。
   * **用途**：主要用于打破循环引用。例如，在两个对象相互引用时，可以使用 `std::weak_ptr` 来引用其中一个对象，以避免内存泄漏。

```c++
std::weak_ptr<MyClass> weakPtr = ptr1; // 不增加引用计数
```

#### 循环引用示例

```c++
#include <iostream>
#include <memory>

class B; // 前向声明

class A {
public:
    std::shared_ptr<B> b_ptr;
    
    A() { std::cout << "A constructed\n"; }
    ~A() { std::cout << "A destroyed\n"; }
};

class B {
public:
// // 使用weak_ptr代替shared_ptr
    std::shared_ptr<A> a_ptr;
    
    B() { std::cout << "B constructed\n"; }
    ~B() { std::cout << "B destroyed\n"; }
};

void demonstrateCircularReference() {
    std::cout << "Creating objects...\n";
    
    {
        // 创建两个shared_ptr对象
        std::shared_ptr<A> a = std::make_shared<A>();
        std::shared_ptr<B> b = std::make_shared<B>();
        
        // 创建循环引用
        a->b_ptr = b;
        b->a_ptr = a;
        
        std::cout << "a use count: " << a.use_count() << std::endl; // 将显示2
        std::cout << "b use count: " << b.use_count() << std::endl; // 将显示2
    }
    
    std::cout << "Leaving scope...\n";
    // 这里应该销毁对象，但由于循环引用，两个对象的析构函数不会被调用
}
```



### 智能指针 引用计数如何实现的

- 每当创建一个新的指针指向对象时，引用计数加 1
- 每当一个指针不再指向对象时（如超出作用域或被重新赋值），引用计数减 1
- 当引用计数降为 0 时，表示没有指针指向该对象，此时可以安全地释放对象

`std::shared_ptr` 内部由两部分组成：

1. **指向对象的指针**：指向实际管理的资源
2. **控制块**：包含引用计数和其他管理信息

```c++
// 简化的 shared_ptr 结构示意
template <typename T>
class shared_ptr {
private:
    T* ptr;                 // 指向资源的指针
    control_block* cblock;  // 指向控制块的指针

    // 其他实现...
};
```

#### 控制块的结构

控制块通常包含：

- **强引用计数**：记录 `shared_ptr` 的数量
- **弱引用计数**：记录 `weak_ptr` 的数量
- **删除器**：自定义的资源释放函数
- **分配器**：用于控制块自身的内存管理

```c++
引用计数需要在多线程环境下安全工作，因此使用原子操作：// 简化的控制块结构示意
struct control_block {
    std::atomic<long> shared_count;    // 强引用计数
    std::atomic<long> weak_count;      // 弱引用计数
    void (*deleter)(void*);            // 删除器函数指针
    // 其他管理数据...
};
```

##### weak_ptr 解决循环引用 的机制

`shared_ptr` 和 `weak_ptr` **共享一个控制块**，但跟踪不同的计数：

1. **强引用计数**：由 `shared_ptr` 增加，决定对象何时被销毁
2. **弱引用计数**：由 `weak_ptr` 增加，决定控制块何时被销毁

```
+----------------+        +-----------------+        +-------------+
| shared_ptr     |------->| 控制块          |<-------| Object      |
+----------------+        +-----------------+        +-------------+
                          | 强引用计数: N   |
                          | 弱引用计数: M   |
                          | 删除器         |
                          +-----------------+
                                  ^
                                  |
                          +----------------+
                          | weak_ptr       |
                          +----------------+
```

当强引用计数降为 0 时，对象被销毁，但控制块保留。
当弱引用计数也降为 0 时，控制块被销毁。

#### 引用计数的具体实现

原子操作保证线程安全

引用计数需要在多线程环境下安全工作，因此使用原子操作：

```c++
#include <atomic>

struct control_block {
    std::atomic<long> shared_count;
    // 其他成员...

    void add_ref() noexcept {
        shared_count.fetch_add(1, std::memory_order_relaxed);
    }

    void release() noexcept {
        if (shared_count.fetch_sub(1, std::memory_order_acq_rel) == 1) {
            delete_resource();
            if (weak_count.fetch_sub(1, std::memory_order_acq_rel) == 1) {
                delete this;
            }
        }
    }
};
```

### 左值和右值

左值是指那些有明确内存地址的表达式，通常可以出现在赋值操作的左侧。

右值是指那些没有明确内存地址的临时表达式，通常只能出现在赋值操作的右侧。

左值可以取地址

### 右值引用   移动构造和赋值

**右值引用是 C++11 引入的一种新类型**，它允许你在程序中区分“左值”（可以取地址的表达式）和“右值”（临时对象或不再使用的对象）。右值引用通过`&&`语法表示。

**移动语义**：

- **右值引用的主要用途是实现移动语义**，可以在不进行深拷贝的情况下**转移资源**（如动态分配的内存）。可以减少不必要的复制，从而提高程序的性能
- 例如，在移动构造函数和移动赋值运算符中，可以**将右值的资源“窃取”而不是复制**，从而提高性能。

```c++
class MyClass {
public:
    MyClass(MyClass&& other) noexcept {
        // 直接窃取 other 的资源
        this->data = other.data;
        other.data = nullptr; // 将 other 的资源置为空
    }
};
```

**实现移动语义的标准库组件**：

- C++标准库中的许多组件（如`std::vector`、`std::string`等）都利用了右值引用来实现移动语义，提高了性能。

下面是一个示例 

```cpp
#include <utility> // for std::move
#include <iostream>

class MyClass {
public:
    MyClass(int data) : data(new int(data)) {}
    ~MyClass() { delete data; }

    // 移动构造函数
    MyClass(MyClass&& other) noexcept : data(other.data) {
        other.data = nullptr; // 置空
    }

    // 移动赋值运算符
    MyClass& operator=(MyClass&& other) noexcept {
        if (this != &other) {
            delete data;      // 清理当前资源
            data = other.data; // 窃取资源
            other.data = nullptr; // 置空
        }
        return *this;
    }

    void print() const {
        if (data) {
            std::cout << *data << std::endl;
        } else {
            std::cout << "No data" << std::endl;
        }
    }

private:
    int* data;
};

int main() {
    MyClass a(42);
    MyClass b(std::move(a)); // 将 a 转换为右值引用

    a.print(); // 输出: No data
    b.print(); // 输出: 42

    MyClass c(99);
    c = std::move(b); // 移动赋值

    b.print(); // 输出: No data
    c.print(); // 输出: 42

    return 0;
}
```

### std::move

- `std::move` 的作用是将一个左值强制转换为右值引用。

本身并不移动任何数据，它只是告诉编译器：“这个对象可以被移动”。

### 完美转发 std::forward

- **右值引用与模板结合使用时，可以实现完美转发**，使得函数可以根据参数的值类别（左值或右值）传递参数。
- 这通常用于泛型编程中。

```c++
#include <iostream>
#include <utility> // for std::forward

// 一个简单的打印函数
void print(const std::string& str) {
    std::cout << "Left value: " << str << std::endl;
}

void print(std::string&& str) {
    std::cout << "Right value: " << str << std::endl;
}

// 完美转发函数
/*
T&& 是一种转发引用（forwarding reference），它是模板类型推导的一种特例。以下是详细解释：

定义：当在模板参数中使用&&，而且该模板参数在函数调用时被推导为一个左值类型（例如std::string&），则该T&&会变成一个左值引用；
如果推导为右值类型，则它将是一个右值引用。
用途：转发引用使得函数能够完美转发参数，保留它们的值类别（左值或右值）
*/
template <typename T>
void forwardPrint(T&& arg) {
    print(std::forward<T>(arg));
}

int main() {
    std::string s = "Hello, World!";

    // 左值转发
    forwardPrint(s); // 输出: Left value: Hello, World!

    // 右值转发
    forwardPrint(std::string("Temporary String")); // 输出: Right value: Temporary String

    return 0;
}
```

### 可变参数

`typename...`

```cpp
#include <iostream>

// 基础情况：处理0个参数
void print() {
    std::cout << std::endl;
}

// 递归情况：处理1个或多个参数
template<typename T, typename... Args>
void print(T first, Args... args) {
    std::cout << first << " ";
    print(args...);
}

int main() {
    print(1, 2.5, "hello", 'a');
    return 0;
}
```



### nullptr 和NULL

- **`NULL`**：`NULL` 是C语言遗留下来的，在C++中仍然可以使用，但不够现代。
- **`nullptr`**：`nullptr` 是C++11引入的新特性，推荐在现代C++代码中使用。

##### **类型**

- **`NULL`**：
  
  - 在C++中，`NULL` 通常是一个宏，定义为 `0` 或 `(void*)0`。
  - 它是一个整数常量（通常是 `int` 类型），因此在某些情况下可能会导致类型不匹配的问题。例如，如果一个函数重载了指针参数和整数参数，传递 `NULL` 可能会导致调用错误的函数。
  
  ```
  #define NULL 0
  
  void foo(int);
  void foo(int*);
  
  foo(NULL);  // 可能会调用 foo(int)，而不是 foo(int*)
  ```

- **`nullptr`**：
  
  - `nullptr` 是C++11引入的关键字，它是一个特殊的空指针常量，类型是 `std::nullptr_t`。
  - 它可以**隐式转换为任何指针类型，但不能转换为整数类型**。
  - `nullptr` 是类型安全的，它只能被转换为指针类型，因此不会导致函数重载解析的歧义。
  
  ```
  nullptr;  // 类型是 std::nullptr_t
  
  void foo(int);
  void foo(int*);
  
  foo(nullptr);  // 明确调用 foo(int*)
  ```

C++ 中，指针类型在条件判断（如 `if`、`while`）中会隐式转换为布尔值：

- 如果指针是 **`nullptr`**，转换为 `false`。
- 如果指针是 **非空**，转换为 `true`。

### 类型推导

* **auto**: 会忽略顶层 const 和引用
- **decltype**: 会保留表达式的所有类型信息，包括 const、引用和值类别(左值/右值)

### enum class

`enum class` 是 C++11 引入的一种枚举类型，它比传统的 `enum` 提供了**更严格的类型检查和命名空间功能**。

使用 `enum class` 可以避免许多传统枚举类型的名称冲突和隐式转换问题。

### 范围for循环

for(xx : xx)

### 花括号初始化

采⽤==花括号来进⾏初始化==，它可以用于初始化各种类型的对象，包括数组、结构体、类对象等。

- **防止窄化转换**：允许窄化转换（如将 `double` 转换为 `int` 时丢失精度）。
  
  ```
  int x{3.14};  // 错误：窄化转换
  ```

- **统一初始化语法**：列表初始化可以用于几乎所有类型的初始化，使得代码更加一致。

#### 注意事项

- **空花括号**：空花括号 `{}` 表示值初始化，而不是默认初始化。
  
  ```
  int x{};  // x被初始化为0
  ```

- **自动推导类型**：列表初始化可以与 `auto` 关键字一起使用，自动推导类型。
  
  ```
  auto arr = {1, 2, 3};  // arr的类型是std::initializer_list<int>
  ```

#### 用法

1. **初始化基本类型**
   
   ```
   int x{10};  // 初始化整数
   double y{3.14};  // 初始化浮点数
   ```

2. **初始化数组**
   
   ```
   int arr[]{1, 2, 3, 4, 5};  // 初始化整数数组
   ```

3. **初始化结构体**
   
   ```
   struct Point {
       int x;
       int y;
   };
   
   Point p{10, 20};  // 初始化结构体
   ```

4. **初始化类对象**
   
   ```
       class MyClass {
       public:
           int a;
           double b;
       };
   
       MyClass obj{42, 3.14};  // 初始化类对象
   ```

5. **初始化标准库容器**
   
   ```
   std::vector<int> vec{1, 2, 3, 4, 5};  // 初始化vector
   std::list<std::string> lst{"hello", "world"};  // 初始化list
   ```

### lambda表达式

```c++
[capture-list](parameters) specifiers -> return-type { body }
```

| 捕获方式        | 描述                       |
| ----------- | ------------------------ |
| `[]`        | 不捕获任何外部变量                |
| `[=]`       | 以值方式捕获所有外部变量             |
| `[&]`       | 以引用方式捕获所有外部变量            |
| `[var]`     | 以值方式捕获变量 var             |
| `[&var]`    | 以引用方式捕获变量 var            |
| `[=, &var]` | 以值方式捕获所有变量，但 var 以引用方式捕获 |
| `[&, var]`  | 以引用方式捕获所有变量，但 var 以值方式捕获 |
| `[this]`    | 捕获 this 指针               |
| `[*this]`   | 捕获当前对象的副本（C++17）         |

#### 如何实现的

当编写一个 Lambda 表达式时，**编译器会将其转换为一个特殊的匿名类的实例**。这个过程可以概括为：

1. 创建一个匿名类（通常称为闭包类型）
2. **重载函数调用运算符** `operator()`
3. 根据捕获列表添加成员变量
4. 生成构造函数来初始化捕获的变量
5. 创建该**匿名类的实例**

考虑以下简单的 Lambda 表达式：

```
auto lambda = [](int x, int y) { return x + y; };
```

编译器会将其转换为类似如下的代码：

```
// Lambda 表达式
auto lambda = [](int x) { return x * x; };

// 编译器生成的等效代码
class __lambda_12345 {
public:
    auto operator()(int x) const {
        return x * x;
    }
};
```

## 模板

### 模板全特化和偏特化

模板分为类模板与函数模板，特化分为特例化（全特化）和部分特例化（偏特化）。

对模板特例化是因为对特定类型，可以利⽤某些特定知识来提⾼效率，⽽不是使⽤通⽤模板。

#### 对函数模板：

1. 模板和特例化版本应该声明在同⼀头⽂件，所有同名模板的声明应放在前⾯，接着是特例化版本。

2. ⼀个模板被称为全特化的条件： 1.必须有⼀个主模板类  2.模板类型被全部明确化。

```c++
template<typename T1, typename T2>
void fun(T1 a, T2 b)
{
    cout<<"模板函数"<<endl;
}
template<>
void fun<int , char >(int a, char b)
{
    cout<<"全特化"<<endl;
}
```

函数模板，只有全特化，偏特化的功能可以通过函数的重载完成。

#### 对类模板：

```c++
template<typename T1, typename T2>
class Test
{
public:
    Test(T1 i,T2 j):a(i),b(j){cout<<"模板类"<<endl;}
private:
    T1 a;
    T2 b;
};
template<>
class Test<int , char>
{
public:
    Test(int i, char j):a(i),b(j){cout<<"全特化"<<endl;}
private:
    int a;
    char b;
};
template <typename T2>
class Test<char, T2>
{
public:
    Test(char i, T2 j):a(i),b(j){cout<<"偏特化"<<endl;}
private:c
    char a;
    T2 b;
}
```

对主版本模板类、全特化类、偏特化类的调⽤优先级从⾼到低进⾏排序是：全特化类>偏特化类>主版本模板类。

## 经典手撕

手撕string vector

