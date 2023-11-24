## 变量和基本类型（const限定符）



#### const对象

+ 一旦创建后值就不能再改变



#### const成员必须初始化

```c++
const int j = 42 //正确 
const int k; //错误
```





#### int类型的对象可以给const int类型的对象初始化

```c++
int i = 42;
const int ci = i;
int j = ci；
//ci的常量特征仅仅在执行改变ci的操作时才会发生作用，拷贝一个对象的值并不会改变它
```



#### const int类型的对象也可以给int类型的对象初始化

```c++
int i = 42;
const int ci = i;
int j = ci；
//ci的常量特征仅仅在执行改变ci的操作时才会发生作用，拷贝一个对象的值并不会改变它
```





#### 默认状态下，const对象仅在文件内有效

+ 当多个文件中出现了同名的const变量时，等同于在不同文件中分别定义了独立的变量
+ 如果确实有必要在文件夹共享
  + 对于const变量不管是声明还是定义都添加extern关键字，这样只需要定义一次就可以了

```c++
extern const int bufSize = fcn();
extern const int bufSize;
```





#### 常量引用（reference to cosnt）

+ 可以把引用绑定到const对象上，就像绑定到其它对象上一样



#### 不允许一个非常量引用绑定常量对象

```c++
const int ci = 1024
const int &r1 = ci; //正确，只允许常量引用绑定常量对象
r1 = 42;            //错误，不允许修改常量ci，也不能通过引用改变常量ci
int &r2 = ci;       //错误，试图让一个非常量引用指向一个常量对象
```



#### 允许一个常量引用绑定非常量的对象

```c++
int i = 42;
const int &r1 = i;
const int &r2 = 42;
```





#### 指向常量的指针（pointer to const）

+ 类似于常量引用（reference to cosnt）
+ 存放常量对象的地址，只能使用**指向常量的指针**
+ 指向常量的指针 不能用于 改变其所指对象的值

```c
const double pi = 3.14;
double *ptr = &pi; //错误
const double *cptr = &pi; //正确
```



#### 允许一个指向常量的指针指向一个非常量对象

```c
double pi = 3.14;
const double *cptr = &pi; //正确

//指向常量的指针与常量引用一样，仅仅要求不能通过该指针或引用改变对象的值，没有规定对象必须是常量
//对象的值可以通过其他途径改变
```





#### const指针

+ **const指针必须初始化**
+ 指针本身是常量：存放在指针中的地址永远不会改变，但地址对应的对象不一定不能通过指针修改

+ ptrNum可以修改Num， ptr不能修改pi

```c
int Num = 0;
int *const ptrNum = &Num;

const double pi = 3.14;
const int *const int ptr = &pi;
```





#### 顶层const 和 底层const

+ 顶层const代表该对象本身是常量

+ 底层const代表对象所指的值是常量
+ 指针类型既可以是顶层const，又可以是底层const
+ 声明引用的const都是底层const

```c
const int *const int ptr = 20;

const int &r = ci; //底层const
```



#### 执行对象的拷贝操作时，顶层const和底层const有区别

+ 顶层const没影响，不用关心拷入和拷出的对象是否是常量
+ 底层const有影响，拷入和拷出的对象必须有相同的底层const资格





#### constexpr和常量表达式

+ 常量表达式（const expression）是指**值不会改变** 并且**在编译过程就能得到计算结果**  的  表达式
    + 字面值属于常量表达式
    + 用 **常量表达式** 初始化的 **const对象** 也是常量表达式
+ 一个对象是不是常量表达式，由它的数据类型和初始值共同决定

```c
const int maxfiles = 20; //maxfile是常量表达式，因为20是字面值常量
int staffsize = 20; //staffsize不是常量表达式，数据类型只是int
const int limit = maxfiles + 1; //limit是常量表达式
const int sz = getsize();  //sz不是常量表达式，因为具体值只能等到运行后得到
```



#### constexpr类型的变量

+ 很难分析 变量的初始值 是否是常量表达式
+ 如果认定一个变量是常量表达式，就将其声明为constexpr类型
+ C++ 11标准规定，允许将变量声明为**constexpr类型**以便由编译器验证变量的初始值是否是一个常量表达式
+ constexpr类型的变量一定是常量，并且必须用常量表达式初始化

```c
constexpr int mf = 20; //20是常量表达式
constexpr limit = mf + 1; //mf + 1是常量表达式
constexpr int sz = getsize();
//当size是constexpr函数时，sz是常量表达式

//constexpr类型的函数足够简单，可以在编译的时候得到结果
//因此可以使用constexpr函数初始化constexpr变量
```



#### 指针和引用和constexpr

+ 指针和引用都能定义为constexpr，但是初始值受到严格限制
+ 一个constexpr的指针必须是nullptr或者是0，或者存储于某个固定地址中的对象
    + 函数体内的变量一般来说并非存放在固定地址中，不能用于初始化constexpr指针（引用）
    + 定义于所有函数体之外的变量其 地址不会改变，可以用于初始化constexpr指针（引用）
    + 允许函数定义有效范围超过函数本身的变量，这种变量也可以初始化constexpr指针（引用）

#### 	

#### 通过constexpr声明 定义指针，const属性仅对指针有效，对指针所指的对象无效

```c
constexpr int *q = nullptr;
//q是一个常量指针
```



#### constexpr指针既可以指向常量，也可以指向非常量

```c
int i = 0;
const int j = 0;
//i和j必须定义在函数体之外
constexpr int *p1 = &i; //p1是常量指针，指向非常量i
constexpr const int *p2 = &j; //p2是常量指针，指向常量j
```





