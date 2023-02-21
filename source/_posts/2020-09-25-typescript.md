---
layout: layout
title: Typescript
date: 2020-09-25 16:33:56
categories: Typescript
tags: Typescript
---
# Typescript


## interface
1. 接口的作用就是为类型命名和为你的码定义契约
2. 可选属性
```
interface SquareConfig {
  color?: string;
}
```
3. 只读属性
```
interface Point {
    readonly x: number;
}
```
4. 类类型，强制类去实现接口
```
interface ClockInterface {
    currentTime: Date;
}

class Clock implements ClockInterface {
    currentTime: Date;
    constructor() { }
}
```
5. class 可以通过 implements 实现 interface, interface 只检查类里的公共属性，静态和私有属性都不会检查

## 类
1. public修饰符，为默认值。
2. private 修饰符，不能在声明它的类的外部访问
```
class Animal {
    private name: string;
    constructor(theName: string) { this.name = theName; }
}

new Animal("Cat").name; // 错误: 'name' 是私有的
```
3. protected 修饰符，protected成员在派生类中仍然可以访问。
```

```
4. readonly修饰符, 只读属性必须在声明时或构造函数里被初始化。
5. 抽象类，抽象类做为其它派生类的基类使用。 它们一般不会直接被实例化。 不同于接口，抽象类可以包含成员的实现细节。 abstract关键字是用于定义抽象类和在抽象类内部定义抽象方法。

## ts里 type 和 interface 的区别
### 相同点：
* 都可以被 class implements 实现
* 都可以描述一个对象或者函数
* 都允许拓展（extends），可以相互 extends
```
// interface extents interface
interface Name { 
  name: string; 
}
interface User extends Name { 
  age: number; 
}

// type extends type
type Name = { 
  name: string; 
}
type User = Name & { age: number  };

// interface extends type
type Name = { 
  name: string; 
}
interface User extends Name { 
  age: number; 
}

//  type extends interface
interface Name { 
  name: string; 
}
type User = Name & { 
  age: number; 
}
```
#### 不同点
* type 可以声明基本类型别名，而interface不行
```
// 基本类型别名
type Name = string
```
* type 语句中还可以使用 typeof 获取实例的类型进行赋值
```
let div = document.createElement('div');
type B = typeof div
```
* interface 可以声明合并，而type不行
```
interface User {
  name: string
  age: number
}

interface User {
  sex: string
}

/* User 接口为 {
  name: string
  age: number
  sex: string 
}*/
```

## 泛型
1、利用泛型保证函数输入输出
```
function one<T>(a: T) : T{
    return a;
}

let a1 = one<number>(1)
```

2、泛型数组
```
// Array<T> 同 T[]
function two<T>(a: T[]) : T{
    return a[0];
}

let a1 = two<number>([1,2,3])
```

3、泛型方法
```
let three : <T>(a : T[]) => T = function (a) {
    return a[0];
}

three<number>([1,2,3])
```

4、泛型接口

```
interface IFunc<T> { 
    <T>(a: T[]): T
}

let four: IFunc<number> = function (a) {
    return a[0];
}
four([1,2,3])
four<number>([1,2,3])
```

5、泛型类
```
class Person<T, U>{
    other: T
    age: U
}

let p = new Person<string,number>()
p.other = "good men"
p.age = 12
```

6、泛型集成接口
```
interface IParams { 
    value: string
}

function test<T extends IParams>(obj: T): T['value'] {
    return obj.value
}

test({ value: '666' })
```
