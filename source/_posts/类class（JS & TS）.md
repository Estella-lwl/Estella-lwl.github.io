---
layout:
  - post
title: 类class（JS & TS）
abbrlink: ba1dc77a
author: polariis
top: true
cover: false
toc: true
mathjax: false
tags:
  - TypeScript
  - JavaScript
  - 更新中
categories: JavaScript
date: 2022-11-12 16:47:53
img:
coverImg:
password:
summary:
---



## 1. 类的定义

- 使用class关键字；
- 在严格模式下，需要给属性初始化。
- 直接初始化，或在constructor中初始化都可以。

```
class Person {
    name: string = '',  //1.直接初始化
    age: number = 0,
    // 2.也可以这样：在执行构造器时要求传进参数初始化：
    constructor(name: string, age: number) {
        this.name = name;
        this.age = age    
    }
    // 类中可以定义方法：
    eating() {
        console.log(this.name + '~');
    }
}

const p = new Person('lwl', 24);  // 这里会执行类的constructor
p.eating();   //调用类中定义的方法
```

**PS**：在TS中删去了动态属性的使用，例如👆🏻第2&3行代码如果不写，constructor里的赋值会报错，而非添加属性，这时会找不到该属性。





## 2. 类的继承 extends

- 当两个类有部分相同的属性，可以专门定义一个类抽进去。

```
class Person {
    name: string = '',
    age: number = 0
    eating() {
        console.log('eat');    
    }
}

// 继承上面类中的属性：
class Student extends Person {
    sId: number = 0
    study() {
        console.log('study');    
    }
}

class Teacher extends Person {
    title: string = '',
    teach() {
        console.log('teach');
    }
}

const stu =  new Student();
stu.name = 'lwl';
stu.age = 24;
console.log(stu.name);
console.log(stu.age);
stu.eating();    // 会发现继承后，stu也能调到eating方法。
```



### 2.1 constructor super

- super可以调用父类的构造器；
- 
- 应该是创建某个对象时就初始化，

```
class Person {
    name: string
    age: number
    
    constructor(name: string, age: number) {
        this.name = name
        this.age = age    
    }
    
    eating() {
        console.log('eat');    
    }
}

class Student extends Person {
    sId?: number
    
    // 当有一项不用传时，需要写为可选类型：
    constructor(name: string, age: number) {
        //super关键字可以调用到父类的
        super
        this.sid = sid;    
    }
    
    study() {
        console.log('study');    
    }
}

```





## 3. TS中类的使用

三个**访问修饰符**：

- public：公有。
- private：外部不能访问。
- protected，与private的区别是：在子类中可以访问到。



### 3.1 类实现接口

当不同的类中有相同的方法，可以将它们提取为接口，并利用**`implements`**关键字实现。

使用示例：https://ts.xcatliu.com/advanced/class-and-interfaces.html



### 3.3 接口继承类

PS：类继承接口未记录。

先看一个例子

- 在TS中，接口可以继承类（JS中没有这种用法）。
- 声明一个类的同时，它也将是一个类型。

```
class Point {
    x: number;
    y: number;
    constructor(x: number, y: number) {
        this.x = x;
        this.y = y;
    }
}

// 等价于上面的Point，因为Point同时可以当作一个TS类型：
interface PointInstanceType {
    x: number;
    y: number;
}

function printPoint(p: PointInstanceType) {
    console.log(p.x, p.y);
}

printPoint(new Point(1, 2));
```



------

待补充
