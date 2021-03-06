---
title: C 语言 - Linux C 编程一站式学习 chapter 03,05 - 函数
categories: 
    - C 语言
    - Linux C 编程一站式学习
tags: C 语言
---

函数

<!--more-->

## 函数

函数原型
```c
void threeline(void)
```
* 编译器要根据函数原型获取函数的参数类型、参数个数、返回值等信息，然后在调用函数处才知道怎么生成指令

函数声明
```c
void threeline(void);

//声明时可以不指定参数名
void threeline(int,char);

//可以在函数内部声明
int main(void){

    void test(void);

    test();
}

void test(void){
    printf("hahaah");
}
```
* 函数原型后面加分号
* 第二种方式：函数声明时可以只指定参数类型，不指定参数名
* 第三种方式：函数声明也可以在函数内部，则其作用范围也仅为函数内部
* 可以在函数内部声明函数，但不能在函数内部定义函数，即 C 标准不支持嵌套函数定义，但 gcc 的扩展特性允许嵌套定义

函数定义
```c
void threeline(void){}
```
* 函数原型加上函数体
* 编译器见到函数定义才生成指令

## 隐式声明

函数没有显式声明时则编译器默认隐式声明了函数，且默认返回值为 int 类型

若函数的返回值类型不是预期的 int，则警告

## 函数参数

old style C 的函数参数举例:
```c
void foo(x,y,z)
int x;
char z;
{

}
```
* 参数之间使用逗号做分隔符即是从这里继承下来的

## 最少例外原则

rule of least surprise

一个语言的语法规则设计里不应该到处是例外，而 C++ 就充满了例外，导致没人能把 C++ 的所有规则牢记于心

## 形参实参

形参相当于函数中定义的变量，调用函数传递参数的过程相当于定义形参的变量并且用实参的值来初始化

## 局部变量、全局变量

## 分配释放空间

局部变量在每次函数调用时分配存储空间，在函数返回时释放存储空间

全局变量在程序开始运行时分配存储空间，在程序结束时释放存储空间

## 调用范围

局部变量只能在函数内部调用，全局变量可任意位置调用

全局变量使用方便，在各函数内部都可以访问，但也因此导致全局变量的读写顺序不好理清，易导致问题，因此要慎用，可以通过函数传参代替就不要使用全局变量

## 初始化

全局变量定义时不初始化其值为 0，但局部变量不初始化则其值是不确定的，所以局部变量在使用前必须先初始化。

局部变量可以使用任意合法表达式初始化

全局变量初始化只能使用常量表达式，因为全局变量在程序开始运行时就要被初始化，所以保存在可执行文件里的初始化值在编译时就要计算出来，而非常量表达式需要运行时才能计算出来，所以为了简化编译器的实现，C 语言规定了全局变量只能使用常量表达式初始化。

## 语句块局部变量

语句块的局部变量通常是为了定义比函数的局部变量更局部的变量

```c
#include <stdio.h>
int main(void){

    int i = 1;
    {
        int i = 2;    
        int j = 2;
        printf("%d\n",i); //输出 2
    }
    //printf("%d\n",j);  这里不注释掉即出错，提示未声明变量 j
}
```

