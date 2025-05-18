---
title: "CMake构建项目"
date: 2025-05-18
draft: true
---

# 使用Cmake构建项目

## 一、CMake构建项目

### CMake是什么？





#### 





### 1. 构建流程

1. 预处理（-E参数  宏替换）
2. 编译 gcc/msvc/clang （-S 参数）
3. 汇编（-C参数  linux生成.o文件、windows生成.obj文件）
4. 连接（将多个二进制文件连接生成一个可执行文件）

### 2. windows下使用cmake构建项目

#### 2.1 下载cmake

官网下载[Download CMake](https://cmake.org/download/)

下载msi文件即可

![image-20250506204144066](E:\Typora-md笔记\C++项目开发\项目构建\image-20250506204144066.png)

#### 2.2 创建项目

使用工具：vscode

新建一个文件夹，在其中新建一个.cpp的项目文件  以及  一个 CMakeLists.txt的文件

在CamkeList.txt中写入以下代码（将注释去掉）

```
#这一行指定了 CMake 的最低版本要求。这意味着你需要安装 CMake 3.20 或更高版本才能正确解析这个文件
cmake_minimum_required(VERSION 3.20)
#项目名称
project(Hello)
#这一行告诉 CMake 创建一个可执行文件（Hello），并指定 hello.cpp 是构建这个可执行文件所需的源文件
add_executable(Hello hello.cpp)
```

示例代码hello.cpp:

```c++
#include<iostream>

using namespace std;

int main(int argc, char const *argv[])
{
    cout<< "Hello"<< endl;
    return 0;
}
```

#### 2.3 编译

##### (1)使用默认编译器

命令行中输入：

```bash
#默认使用msvc进行编译
cmake -B build

cmake --build build
```

![image-20250506202446333](E:\Typora-md笔记\C++项目开发\项目构建\image-20250506202446333.png)

```
编译之后的项目文件目录
.\Hello\
├── CMakeLists.txt
├── hello.cpp
└── build\
```

运行程序

```
build/Debug/hello.exe
```

![image-20250506203320184](E:\Typora-md笔记\C++项目开发\项目构建\image-20250506203320184.png)

##### （2）使用"MinGW Makefiles"编译器

使用以下命令来查看你可用编译器

*在开头的是默认编译器

```
cmake --help
```

![image-20250506203426221](E:\Typora-md笔记\C++项目开发\项目构建\image-20250506203426221.png)

现在我们指定“MinGW Makefiles”编译器来编译（注意是用“”  不是**）

```
cmake -B build -G "MinGW Makefiles"
cmake --build build
```

执行命令遇到：

![image-20250506203655220](E:\Typora-md笔记\C++项目开发\项目构建\image-20250506203655220.png)

是因为之前已经使用默认编译器编译过了，已经有了一个build的文件夹，可以将之前的build文件夹重新命名一个其他名字，再进行尝试。

```
#启动！
.\build\hello.exe
```

![image-20250506203816200](E:\Typora-md笔记\C++项目开发\项目构建\image-20250506203816200.png)



### 3. Linux下使用cmake

#### 3.1. 安装cmake

##### apt安装

```bash
sudo apt-get install cmake

#查看安装版本
cmake --version

#查看编译器
cmake --help
```

![image-20250506210627123](E:\Typora-md笔记\C++项目开发\项目构建\image-20250506210627123.png)

#### 3.2 编译项目（Linux下使用默认编译器即可）

项目文件与windows下的文件一样即可

```bash
cmake -B build

cmake --build build
```

![image-20250506210830669](E:\Typora-md笔记\C++项目开发\项目构建\image-20250506210830669.png)

执行程序

```bash
build/Hello
```

![image-20250506211204551](E:\Typora-md笔记\C++项目开发\项目构建\image-20250506211204551.png)

## 二、CMake语法

### 1. message:  打印

```
cmake_grammar/
└── first.cmake
```

first.cmake:

```cmake
cmake_minimum_required(VERSION 3.20)
message("Hello")
message("--------------")
message(Hello)
message("--------------")
message("jfalk
jalsdkf")
message("--------------")
message([[asdfa
asdfef]])
message("--------------")


#获取CMAKE中的信息
#  ${}
message(${CMAKE_VERSION})
```

执行命令

```bash
camke -P first.cmake
```

![image-20250513230839584](E:\Typora-md笔记\C++项目开发\项目构建\image-20250513230839584.png)

### 2. 变量操作  set、list

#### 2.1 set

```
./../cmake_grammar/
├── first.cmake
└── set.cmake
```

set.cmake:

```cmake
cmake_minimum_required(VERSION 3.20)

#set用法
set(Var1 ASF)
message(${Var1})

set("My Var" myvar"")
message(${My\ Var})
set([[My Var]] myvar[])
message(${My\ Var})

#设置多个值
set(serval_Var1 a1 a2)
message(${serval_Var1})
set(serval_var2 b1;b2)
message(${serval_var2})

# $PATH
message($ENV{PATH})
#用于将环境变量CXX的值改为“g++”
set(ENV{CXX} "g++")
message($ENV{CXX})

#取消设置环境变量 CXX  将 CXX 的值恢复到执行本代码前的的CXX值
unset(ENV{CXX})
message($ENV{CXX})
```

执行命令：

```bash
cmake -P set.cmake
```

![image-20250513232701771](E:\Typora-md笔记\C++项目开发\项目构建\image-20250513232701771.png)

表示已经取消了CXX的值，所以此处再进行打印就会报错

![image-20250513233502956](E:\Typora-md笔记\C++项目开发\项目构建\image-20250513233502956.png)

#### 2.2 list

```
./../cmake_grammar/
├── first.cmake
└── set.cmake
└── list.cmake
```

！注意cmake中的空格

list.cmake:

```cmake
cmake_minimum_required(VERSION 3.20)

#set与list都可以创建变量
message(=========创建变量============)
set(Var_set set s1 s2 s3)
message(${Var_set})

list(APPEND Var_list list l1 l2 l3)
message(${Var_list})

#获取长度
message(=========获取长度============)
list(LENGTH Var_set len)
message(${len})

#找到Var_set中s2这个元素所在的索引  索引是从1开始的
message(=========获取索引============)
list(FIND Var_set s2 index)
message(${index})

#移除变量中的元素
message(=========移除元素============)
list(REMOVE_ITEM Var_set s2) 
message(${Var_set})

#添加元素
message(=========添加元素============)
list(APPEND Var_set a4)  # 直接尾插
message(${Var_set})
list(INSERT Var_set 3 a5) # 指定要插入的位置
message(${Var_set})

#反转、排序
message(=========反转、排序============)
list(REVERSE Var_set)
message(${Var_set})
list(SORT Var_set)
message(${Var_set})
```

执行命令：

```bash
cmake -P list.cmake
```

![image-20250513235244159](E:\Typora-md笔记\C++项目开发\项目构建\image-20250513235244159.png)

### 3. 流程控制

#### 3.1 条件流程控制（if）

```
./../cmake_grammar/
├── first.cmake
└── set.cmake
└── list.cmake
└── if.cmake
```

if.cmake:

```
cmake_minimum_required(VERSION 3.20)

set(FLAG TRUE)

if(FLAG)
    message("TRUE")
elseif(FLAG OR NOT FLAG)
    message("FLAG OR NOT FLAG")
elseif(FLAG AND NOT FLAG)
    message("FLAG AND NOT FLAG")
endif()

message("==============================")
if(1 LESS 2)
    message("1 LESS 2")
endif()

if(NOT 2 LESS 1)
    message("NOT 2 LESS 1")
endif()

message("==============================")
if(1 EQUAL "1")
    message("1 EQUAL '1'")
endif()

message("==============================")
if(NOT "a" LESS 233)
    message("a LESS 233")
endif()
```

执行命令：

```
cmake -P if.make
```

![image-20250516223621266](E:\Typora-md笔记\C++项目开发\项目构建\image-20250516223621266.png)



#### 3.2 循环流程控制（for、while）

```
./../cmake_grammar/
├── first.cmake
└── set.cmake
└── list.cmake
└── if.cmake
└── Loop.cmake
```

Loop.cmake:

```
cmake_minimum_required(VERSION 3.20)

foreach(VAR RANGE 3)
    message(${VAR})
endforeach(VAR RANGE 3)
message("===============================")

set(MY_LISTS 1 2 3)
foreach(VAR IN LISTS MY_LISTS)
    message(${VAR})    
endforeach()
message("===============================")

#项目中用的较多
set(LIST 2 3)
foreach(VAR IN LISTS LIST ITEMS a b 5)
    message(${VAR})    
endforeach()
message("===============================")

#zip
set(L1 one two three)
set(L2 1 2 3)
foreach(num IN ZIP_LISTS L1 L2)
    message("word=${num_0},num=${num_1}")    
endforeach()
```

执行命令：

```
cmake -P Loop.cmake
```

![image-20250516224617346](E:\Typora-md笔记\C++项目开发\项目构建\image-20250516224617346.png)

### 4. 函数



```
./../cmake_grammar/
├── first.cmake
└── set.cmake
└── list.cmake
└── if.cmake
└── Loop.cmake
└── func.cmake
```

func.cmake:

```
cmake_minimum_required(VERSION 3.20)

function(MyFunc FirstArg)
    #当前的函数名
    message("MyFunc Name: ${CMAKE_CURRENT_FUNCTION}")
    #传入的参数
    message("FirstArg ${FirstArg}")
    set(FirstArg "new_value")
    message("FirstArg again ${FirstArg}")
    #打印参数
    message("ARGV0 ${ARGV0}")
    message("ARGV1 ${ARGV1}")
    message("ARGV2 ${ARGV2}")
endfunction(MyFunc FirstArg)


set(FirstArg "first value")
MyFunc(${FirstArg} "second_value")   #传入参数时，要使用${} 不能直接写变量名
message("MainFunc FirstArg: ${FirstArg}")
```

执行命令：

```
cmake -P func.cmake
```

![image-20250516225748876](E:\Typora-md笔记\C++项目开发\项目构建\image-20250516225748876.png)

### 5. 作用域

新建一个项目文件夹

这只是在CMakeLists.txt中练习cmake的语法，是没有链接可执行文件的

```
./../cmake_grammar/
├──test_project
└──其他文件

./../cmake_grammar/test_project/
├──CMakeLists.txt
```

CMakeLists.txt:

```
cmake_minimum_required(VERSION 3.20)
project(scope)

function(OutFunc)
    message("-> OutFunc: ${Var}")
    set(Var 2)
    InFunc()
    message("<- OutFunc: ${Var}")
endfunction(OutFunc)

function(InFunc)
    message("->InFunc: ${Var}")
    set(Var 3)
    message("<- InFunc: ${Var}")    
endfunction(InFunc)

#相当于全局变量
set(Var 1)
message("-> Global: ${Var}")
OutFunc()
message("<- Global: ${Var}")
```

执行命令：

```
cmake -B build
```

![image-20250516232447857](E:\Typora-md笔记\C++项目开发\项目构建\image-20250516232447857.png)

### 6. 宏

注意：尽量不要写宏，只要会读

宏：作用域比函数更大，在宏内部进行修改时，外部（调用宏的作用域）的变量也会受到改变

函数：在函数内部修改变量时，不会影响到外部的变量

#### 6.1代码实现

```
~/share/C++project/cmake_learn/cmake_grammar$ tree -L 1
.
├── first.cmake
├── func.cmake
├── if.cmake
├── list.cmake
├── Loop.cmake
├── macro.cmake
├── set.cmake
└── test_project
```

macro.cmake : 

```
cmake_minimum_required(VERSION 3.20)

macro(Test myVar)
    set(myVar "new_value")   #创建了一个新的变量
    message("macro: ${myVar}")
endmacro()

set(myVar "First_value")
message("First: ${myVar}")

Test(${myVar})
message("final: ${myVar}")
```

执行命令：

```
cmake -P macro.cmake
```

![image-20250516235154733](E:\Typora-md笔记\C++项目开发\项目构建\image-20250516235154733.png)

=============================================

#### 6.2 AI代码分析

--------------------------------------------------------------

（1）定义宏 `Test`

```cmake
macro(Test myVar)
    set(myVar "new_value")   # 创建了一个新的变量
    message("macro: ${myVar}")
endmacro(Test myVar)
```
- `macro(Test myVar)` 定义了一个名为 `Test` 的宏，它接受一个参数 `myVar`。
- 在宏内部：
  - `set(myVar "new_value")` 尝试将参数 `myVar` 设置为 `"new_value"`。
  - `message("macro: ${myVar}")` 输出宏内部的变量值。

(2) 设置全局变量 `myVar`

```cmake
set(myVar "First_value")
message("First: ${myVar}")
```
- `set(myVar "First_value")` 将全局变量 `myVar` 设置为 `"First_value"`。
- `message("First: ${myVar}")` 输出当前的 `myVar` 值：
  ```
  First: First_value
  ```

(3) 调用宏 `Test`

```cmake
Test(${myVar})
```
- 调用宏 `Test`，并传递 `${myVar}` 作为参数。此时，`${myVar}` 的值是 `"First_value"`。

----------------------------------------------------------------------------------------------------------------------------

+ + 关键点：宏的参数传递和作用域

在 CMake 中，宏的参数是通过 **文本替换** 来传递的。也就是说，当你调用 `Test(${myVar})` 时，宏内部的代码会被展开为：
```cmake
set(First_value "new_value")
message("macro: ${First_value}")
```

- `set(First_value "new_value")` 尝试将变量 `First_value` 设置为 `"new_value"`，但 `First_value` 并不是一个有效的变量名，因此这行代码不会对任何变量产生实际影响。
- `message("macro: ${First_value}")` 输出宏内部的变量值，但由于 `First_value` 并没有被设置，它仍然是 `"First_value"`。

===================================输出结果===================================

因此，最终的输出是：
```
First: First_value
macro: First_value
final: new_value
```

===================================为什么 `final: new_value`？===================================

这里的关键在于 CMake 的变量作用域和宏的特性。在宏内部，`set(myVar "new_value")` 实际上修改了调用宏的作用域中的变量 `myVar`。由于宏的作用域特性，宏内部对变量的修改会直接影响调用宏的作用域。

在你的代码中，`Test(${myVar})` 调用了宏 `Test`，并将 `${myVar}` 的值 `"First_value"` 传递给宏。宏内部的 `set(myVar "new_value")` 实际上修改了全局变量 `myVar`，因为宏的作用域特性使得宏内部的变量修改会反映到调用宏的作用域中。

===================================总结===================================

- **宏的作用域特性**：宏内部对变量的修改会直接影响调用宏的作用域。
- **参数传递**：宏的参数是通过文本替换传递的，因此宏内部对参数的修改会直接影响调用宏的作用域中的变量。

===================================修改后的代码===================================

如果你希望避免这种行为，可以使用 `function` 替代 `macro`，因为 `function` 的参数是通过值传递的，不会影响外部变量。

```cmake
cmake_minimum_required(VERSION 3.20)

function(Test myVar)
    set(myVar "new_value" PARENT_SCOPE)  # 使用 PARENT_SCOPE 修改外部变量
    message("function: ${myVar}")
endfunction(Test myVar)

set(myVar "First_value")
message("First: ${myVar}")

Test(myVar)
message("final: ${myVar}")
```

这样，输出将是：
```
First: First_value
function: new_value
final: new_value
```

#### 6.3 与C++宏的区别

| 特性         | C++ 宏                       | CMake 宏                             |
| ------------ | ---------------------------- | ------------------------------------ |
| **定义方式** | `#define`                    | `macro`                              |
| **作用域**   | 全局，文件范围内有效         | 调用宏的作用域，参数通过文本替换传递 |
| **用途**     | 简化代码、条件编译、调试辅助 | 代码复用、简化 CMake 脚本            |
| **类型检查** | 没有，纯文本替换             | 有，CMake 语法检查                   |
| **调试难度** | 难，预处理后代码可能难以理解 | 较容易，CMake 提供错误信息           |

==========================================================================

## 三、构建项目的四种方式

### 3.1 直接写入源码的方式

主要关注：CMakeLists.txt

目录结构

```
# tree -L 2
.
├── animal
│   ├── dog.cpp
│   └── dog.h
├── CMakeLists.txt
└── main.cpp
```

main.cpp

```
#include <iostream>
#include "animal/dog.h"

using namespace std;

int main(int argc, char const *argv[])
{
    /* code */
    Dog wangcai;
    string bark= wangcai.barking();
    cout<<bark<<endl;
    return 0;
}
```

dog.cpp

```
#include "dog.h"

std::string Dog::barking(){
    return "fuck";
}
```

dog.h

#pragma once：确保头文件的内容在编译过程中只被包含一次。这可以避免重复定义和重复编译的问题。

```
#pragma once

#include <string>

class Dog
{
public:
    std::string barking();
};
```

**主要关注**：CMakeLists.txt:

```
cmake_minimum_required(VERSION 3.20)
project(animal CXX)  #将编译器指定为cpp
add_executable(animal main.cpp animal/dog.cpp)  #将cpp文件构建为一个名为animal的项目
```

执行命令：

```
#创建构建目录并生成构建文件（创建一个名为build的目录，并在build中创建生成文件，如Makefile等）
cmake -B build
#构建项目（根据指定文件build中的构建文件：Makefile等来编译项目并生成可执行文件）
cmake --build build 
#执行
build/animal
```

![image-20250517190247620](E:\Typora-md笔记\C++项目开发\项目构建\image-20250517190247620.png)

![](E:\Typora-md笔记\C++项目开发\项目构建\image-20250517190218964.png)

### 3.2 调用子目录中的cmake脚本

include方法可以引入子目录中.cmake后缀的配置文件



添加animal.cmake、cat.cpp、cat.h:

目录结构

```bash
├── animal
│   ├── animal.cmake
│   ├── cat.cpp
│   ├── cat.h
│   ├── dog.cpp
│   └── dog.h
├── CMakeLists.txt
└── main.cpp
```

CMakeLists.txt:

#注意要使用include引入cmake文件

```cmake
cmake_minimum_required(VERSION 3.20)
project(animal CXX)
include(animal/animal.cmake)  
add_executable(animal main.cpp ${animal_source})
```

animal.cmake

#注意！！！！

不可以是  ：   *set(animal_source cat.cpp dog.cpp)*   

其实本质：将*animal_source*这个变量设置为以下的值。 在CMakeLists.txt中将变量替换为设置的值，所以变量的值要设置为以*CMakeLists*为主体时，子文件的相对路径

```cmake
set(animal_source animal/cat.cpp animal/dog.cpp)
```

cat.h、cat.cpp中随便写一个类，定义类方法。在main.cpp中使用

```
#cat.h:
#pragma once

#include <string>

class Cat
{
public:
    std::string miaow();
};
#-----------------------------------------------
#cat.cpp:
#include "cat.h"

std::string Cat::miaow(){
    return "妙啊妙啊";
}
#-----------------------------------------------
#main.cpp:
#include <iostream>
#include "animal/dog.h"
#include "animal/cat.h"

using namespace std;

int main(int argc, char const *argv[])
{

    Dog wangcai;
    string bark= wangcai.barking();
    cout<<bark<<endl;
    Cat duoduo;
    string mao = duoduo.miaow();
    cout<<mao<<endl;
    return 0;
}
```

执行命令：

```
rm -rf build

cmake -B build 

cmake --build build
```

![image-20250517192741770](E:\Typora-md笔记\C++项目开发\项目构建\image-20250517192741770.png)

### 3.3 CMakeLists嵌套（最常见的一种方式）

CMakeLists.txt 嵌套是指在项目的主 CMakeLists.txt 文件中使用 `add_subdirectory()` 命令将子目录的 CMakeLists.txt 文件包含进来，从而构建一个**层次化**的项目结构。

```
project/
├── CMakeLists.txt          # 主 CMakeLists.txt
├── src/
│   ├── CMakeLists.txt      # 源代码模块的 CMakeLists.txt
│   └── ...                 # 源代码文件
├── lib/
│   ├── CMakeLists.txt      # 库模块的 CMakeLists.txt
│   └── ...                 # 库文件
└── ...
```

#### 几个重要的方法：

+ target_include_directories ---头文件目录的声明

+ target_link_libraries  ---  链接库文件

+ add_subdirectory --- 添加子目录

+ add_library  --- 生成库文件（默认  **STATIC library**）

#### 便利：

- **模块化项目组织**：
  - 允许将项目划分为多个子模块，每个子模块都有自己的 CMakeLists.txt 文件。这使得项目结构更清晰，便于管理和维护。
  - 例如，一个大型项目可以分为多个功能模块，每个模块独立构建，最后整合到主项目中。
- **复用构建逻辑**：
  - 子目录的 CMakeLists.txt 文件可以复用公共的构建逻辑（如编译选项、宏定义等），减少重复配置。
- **简化主 CMakeLists.txt**：
  - 主 CMakeLists.txt 文件只需关注项目的整体结构和依赖关系，具体的构建细节由子目录的 CMakeLists.txt 文件处理，使主文件更简洁。
- **支持多语言和多平台**：
  - 每个子模块可以根据需要配置不同的语言和平台特定的构建选项，增加了项目的灵活性。



#### 代码文件

目录结构

```
├── animal
│   ├── CMakeLists.txt
│   ├── cat.cpp
│   ├── cat.h
│   ├── dog.cpp
│   └── dog.h
├── CMakeLists.txt
└── main.cpp
```

CMakeLists.txt:

```
cmake_minimum_required(VERSION 3.20)
project(Animal CXX)

add_subdirectory(animal)

add_executable(Animal main.cpp)

#链接AnimalLib库
target_link_libraries(Animal PUBLIC AnimalLib)

#PROJECT_BINARY_DIR:build文件夹（构建生成文件的文件夹）     PROJECT_SOURCE_DIR:当前主目录； /animal：CMakeLists文件在/animal下
target_include_directories(Animal PUBLIC "${PROJECT_BINARY_DIR}" "${PROJECT_SOURCE_DIR}/animal")
```

animal/CMakeLists.txt:

```
#添加一个库
add_library(AnimalLib cat.cpp dog.cpp)
```

main中稍作修改：

```
可以将
#include "animal/dog.h"
#include "animal/cat.h"

修改为：
#include "dog.h"
#include "cat.h"
```

执行

```
rm -rf build

cmake -B build 

cmake --build build
```

![image-20250517202644763](E:\Typora-md笔记\C++项目开发\项目构建\image-20250517202644763.png)





### 3.4 Object Library (相当于3.3 的进一步“解耦合”)

修改

./animal/CmakeLists.txt:

```
add_library(AnimalLib OBJECT cat.cpp dog.cpp)
target_include_directories(AnimalLib PUBLIC .)
```

./CmakeLists.txt:

```
cmake_minimum_required(VERSION 3.20)
project(Animal CXX)

add_subdirectory(animal)

add_executable(Animal main.cpp)

#链接AnimalLib库
target_link_libraries(Animal PUBLIC AnimalLib)
```

执行：

```
rm -rf build

cmake -B build 

cmake --build build
```

![image-20250517202427286](E:\Typora-md笔记\C++项目开发\项目构建\image-20250517202427286.png)



### 3.5 CMakeLists嵌套 与 Object library的区别

#### 3.5.1 关键区别分析

1. **库类型不同**

   - **第一种方法**：`AnimalLib` 是一个静态库（`STATIC`），默认情况下 `add_library` 创建的是静态库。
   - **第二种方法**：`AnimalLib` 是一个对象库（`OBJECT`）。

2. **包含目录设置位置**

   - **第一种方法**：在主目录的 `CMakeLists.txt` 中设置包含目录，确保 `main.cpp` 可以找到 `animal` 子目录中的头文件。
   - **第二种方法**：在子目录的 `CMakeLists.txt` 中设置包含目录，并通过 `PUBLIC` 传播到依赖 `AnimalLib` 的目标。

3. **构建日志差异**

   - **第一种方法**：构建日志中会显示静态库的链接步骤：

     ```
     [ 60%] Linking CXX static library libAnimalLib.a
     ```

   - **第二种方法**：构建日志中不会显示静态库的链接步骤，因为对象库不会生成独立的静态库文件。

#### 3.5.2 为什么第二种方法输出少了一句

在第二种方法中，`AnimalLib` 是一个对象库，对象库不会生成独立的静态库文件，而是直接将编译后的对象文件（`.o` 文件）提供给依赖它的目标（如 `Animal` 可执行文件）。因此，构建日志中不会显示静态库的链接步骤。

#### 3.5.3 两种方法的便利之处

1. **第一种方法（静态库）**
   - **优势**：
     - 静态库可以独立于可执行文件进行构建和链接，便于复用。
     - 构建日志更详细，便于调试和验证每个阶段的构建结果。
   - **适用场景**：
     - 当需要将库作为独立组件复用时。
     - 当需要明确管理库的生成和链接步骤时。
2. **第二种方法（对象库）**
   - **优势**：
     - 对象库允许直接复用对象文件，避免重复编译，提高构建效率。
     - 构建日志更简洁，减少冗余信息。
   - **适用场景**：
     - 当需要在多个目标之间共享相同的源文件编译结果时。
     - 当希望减少构建时间和日志输出时。

#### 3.5.4 总结

- **第一种方法**：适合需要明确管理静态库的生成和链接步骤的场景，构建日志更详细。
- **第二种方法**：适合需要复用对象文件以提高构建效率的场景，构建日志更简洁。





## 四、动态库、静态库







## 五、cmake与源文件交互





## 六、条件编译

