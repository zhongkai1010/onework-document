# 第五篇、TypeScript 笔记
# 一、基础类型
布尔、数字、字符串、数组、元组、枚举、Any、Void、Null 和 Undefined、Never、Object

```javascript
// 布尔
let isDone: boolean = false;
// 数字
let decLiteral: number = 6;
let hexLiteral: number = 0xf00d;
let binaryLiteral: number = 0b1010;
let octalLiteral: number = 0o744;
// 字符串
let name: string = "bob";
name = "smith";

let name: string = `Gene`;
let age: number = 37;
let sentence: string = `Hello, my name is ${ name }.

I'll be ${ age + 1 } years old next month.`;

// 数组
let list: number[] = [1, 2, 3];
let list: Array<number> = [1, 2, 3];

// 元组
// Declare a tuple type
let x: [string, number];
// Initialize it
x = ['hello', 10]; // OK
// Initialize it incorrectly
x = [10, 'hello']; // Error

// 枚举
enum Color {Red, Green, Blue}
let c: Color = Color.Green;

// Any
let notSure: any = 4;
notSure = "maybe a string instead";
notSure = false; // okay, definitely a boolean

// Void
function warnUser(): void {
    console.log("This is my warning message");
}

// Null 和 Undefined
let u: undefined = undefined;
let n: null = null;

// Never
// 返回never的函数必须存在无法达到的终点
function error(message: string): never {
    throw new Error(message);
}

// 推断的返回值类型为never
function fail() {
    return error("Something failed");
}

// 返回never的函数必须存在无法达到的终点
function infiniteLoop(): never {
    while (true) {
    }
}
```
# 二、变量声明
## （1）`let` 声明
现在你已经知道了`var`存在一些问题，这恰好说明了为什么用`let`语句来声明变量。 除了名字不同外， `let`与`var`的写法一致。

```javascript
let hello = "Hello!";
```
## （2）count 声明
```javascript
const numLivesForCat = 9;

```
它们与`let`声明相似，但是就像它的名字所表达的，它们被赋值后不能再改变。 换句话说，它们拥有与 `let`相同的作用域规则，但是不能对它们重新赋值。

## （3）对象解构
```javascript
let o = {
    a: "foo",
    b: 12,
    c: "bar"
};
let { a, b } = o;

```
这通过 `o.a` and `o.b` 创建了 `a` 和 `b` 。 注意，如果你不需要 `c` 你可以忽略它。

## （4）属性重命名
你也可以给属性以不同的名字：

```Plain Text
let { a: newName1, b: newName2 } = o;

```
这里的语法开始变得混乱。 你可以将 `a: newName1` 读做 "`a` 作为 `newName1`"。 方向是从左到右，好像你写成了以下样子：

```Plain Text
let newName1 = o.a;
let newName2 = o.b;
```
## （5）默认值
默认值可以让你在属性为 undefined 时使用缺省值：

```Plain Text
function keepWholeObject(wholeObject: { a: string, b?: number }) {
    let { a, b = 1001 } = wholeObject;
}

```
现在，即使 `b` 为 undefined ， `keepWholeObject` 函数的变量 `wholeObject` 的属性 `a` 和 `b` 都会有值。

## （5）函数声明
解构也能用于函数声明。 看以下简单的情况：

```Plain Text
type C = { a: string, b?: number }
function f({ a, b }: C): void {
    // ...
}

```
但是，通常情况下更多的是指定默认值，解构默认值有些棘手。 首先，你需要在默认值之前设置其格式。

```Plain Text
function f({ a="", b=0 } = {}): void {
    // ...
}
f();

```
> 上面的代码是一个类型推断的例子，将在本手册后文介绍。

其次，你需要知道在解构属性上给予一个默认或可选的属性用来替换主初始化列表。 要知道 `C` 的定义有一个 `b` 可选属性：

```Plain Text
function f({ a, b = 0 } = { a: "" }): void {
    // ...
}
f({ a: "yes" }); // ok, default b = 0
f(); // ok, default to {a: ""}, which then defaults b = 0
f({}); // error, 'a' is required if you supply an argument

```
要小心使用解构。 从前面的例子可以看出，就算是最简单的解构表达式也是难以理解的。 尤其当存在深层嵌套解构的时候，就算这时没有堆叠在一起的重命名，默认值和类型注解，也是令人难以理解的。 解构表达式要尽量保持小而简单。 你自己也可以直接使用解构将会生成的赋值表达式。

# 三、接口
TypeScript的核心原则之一是对值所具有的\_结构\_进行类型检查。 它有时被称做“鸭式辨型法”或“结构性子类型化”。 在TypeScript里，接口的作用就是为这些类型命名和为你的代码或第三方代码定义契约。

下面通过一个简单示例来观察接口是如何工作的：

```javascript
function printLabel(labelledObj: { label: string }) {
  console.log(labelledObj.label);
}

let myObj = { size: 10, label: "Size 10 Object" };
printLabel(myObj);
```
类型检查器会查看`printLabel`的调用。 `printLabel`有一个参数，并要求这个对象参数有一个名为`label`类型为`string`的属性。 需要注意的是，我们传入的对象参数实际上会包含很多属性，但是编译器只会检查那些必需的属性是否存在，并且其类型是否匹配。

## （1）可选属性
## （2）只读属性
## （3）函数类型
## （4）可索引的类型
## （5）类静态部分与实例部分的区别
## （6）继承接口
## （7）混合类型
## （8）接口继承类
# 参考资料
[https://www.tslang.cn/docs/home.html](https://www.tslang.cn/docs/home.html)