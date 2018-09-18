>发现一边看书，一边写markdown，学习效率高一点，就以此来记录基础知识。 

## 快速上手
### 安装typescript
```
$ npm install -g typescript
```
### 创建typescript文件
创建hello.ts
```
function say(word) {
    return "Hello, " + word;
}

let word = "this is typescript";

document.body.innerHTML = say(word);
```
### 编译
```
tsc hello.ts
```
## 基本类型
数字、字符串、结构体、布尔值等
### 布尔值
```
let isTrue : boolean = false;
```
### 数字
所有数字都是浮点数，类型是number。支持二进制，八进制，十进制，十六进制
```
let binary: number = 0b1010;//二进制
let octal: number = 0o744;//八进制
let dec: number = 6;//十进制
let hex: number = 0xf00d;//十六进制
```
### 字符串
可以用双引号和单引号表示字符串
```
let text : string = 'hello';
text = "hello world";
```
模板字符串，反引号包围，${expr}形式嵌入
```
let age : number = 23;
let say : string = `I'll be ${age+1} years old`
```
### 数组
两种方式可以定义数组。<br/>第一种,元素类型后面接上[]
```
let list : number[] = [1,2,3]
// let list : number[] = [1,'2',3]//这样会报错
```
第二种，使用数组泛型
```
let list : Array<number> = [1,2,3]
```
### 元组
元组类型允许表示一个已知元素数量和类型的数组，各元素的类型不必相同。
```
let x: [string, number];
x = ['hello', 10];
// x = [10, 'hello']; // 这样不行
```
当访问一个越界的元素，会使用联合类型替代
```
let x: [string, number];
x = ['hello', 10];
x[2] = 1;//可以是string或number
x[3] = 'world'
console.log(x)
```
### 枚举
enum类型可以为一组数值赋予友好的名字
默认情况下，从0开始为元素编号。 你也可以手动的指定成员的数值。 
```
enum Color {Red = 1, Green, Blue}
let colorName: string = Color[2];
let colorIndex: number = Color.Green;
console.log(Color)
console.log(colorName)
console.log(colorIndex)
```
### Any
那些在编程阶段还不清楚类型的变量指定一个类型
```
let anyType:any = 123;
anyType = 'hello';
anyType = true;
let list: any[] = [1,true,'free']
```
### Void
它表示没有任何类型
```
function warining() :void{
    console.warn('bad')
}
let unsable : void = undefined;//void只能赋值undefined和null
```
### Null 和 Undefined
undefined和null两者各自有自己的类型分别叫做undefined和null,和 void相似
```
let u: undefined = undefined;
let n: null = null;
```
### Never
never类型表示的是那些永不存在的值的类型。
```
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
### 类型断言
类型断言好比其它语言里的类型转换，但是不进行特殊的数据检查和解构。 它没有运行时的影响，只是在编译阶段起作用。
尖括号语法
```
let someValue: any = "this is a string";

let strLength: number = (<string>someValue).length;
```
as语法
```
let someValue: any = "this is a string";

let strLength: number = (someValue as string).length;
```
## 变量声明
### var声明
```
var a = 10;
```
函数内部访问
```
function f() {
    var a = 10;
    return function g() {
        var b = a + 1;
        return b;
    }
}

var g = f();
g(); // returns 11;
```
作用域规则
```
function f(shouldInitialize: boolean) {
    if (shouldInitialize) {
        var x = 10;
    }

    return x;
}

f(true);  // returns '10'
f(false); // returns 'undefined'
```
 变量 x是定义在*if语句里面*，但是我们却可以在语句的外面访问它。 这是因为 var声明可以在包含它的函数，模块，命名空间或全局作用域内部任何位置被访问。
 
捕获变量怪异之处
 ```
 for (var i = 0; i < 10; i++) {
    setTimeout(function() { console.log(i); }, 100 * i);
}
//10
//10
//10
//10
//10
//10
//10
//10
//10
//10
 ```
 setTimeout执行位于for循环结束后。 for循环结束后，i的值为10。 所以当函数被调用的时候，它会打印出 10
 - 可以使用闭包解决

```
for (var i = 0; i < 10; i++) {
    (function(i) {
        setTimeout(function() { console.log(i); }, 100 * i);
    })(i);
}
//或
var _loop_1 = function (i) {
    setTimeout(function () { console.log(i); }, 100 * i);
};
for (var i = 0; i < 10; i++) {
    _loop_1(i);
}

```
### let 声明
写法与var类似，区别在于作用域
```
let hello = "Hello!";
```
块级作用域

不同于使用 var声明的变量那样可以在包含它们的函数外访问，块作用域变量在包含它们的块或for循环之外是不能访问的。
```
function f(input: boolean) {
    let a = 100;
    if (input) {
        let b = a + 1;
        return b;
    }
    return b;
}
// Error: Cannot find name 'b'.

```
拥有块级作用域的变量的另一个特点是，它们不能在被声明之前读或写。
```
a++;
let a;
//'a' is declared here.
```
重定义及屏蔽
```
let x = 10;
let x = 20; // 错误，不能在1个作用域里多次声明`x`
```
块级作用域变量的获取<br/>

不仅是在循环里引入了一个新的变量环境，而是针对 每次迭代都会创建这样一个新作用域。 这就是我们在使用立即执行的函数表达式时做的事
```
for (let i = 0; i < 10 ; i++) {
    setTimeout(function() {console.log(i); }, 100 * i);
}
//0
//1
//2
//3
//4
//5
//6
//7
//8
//9
```
 ### const 声明
 拥有与 let相同的作用域规则，但是不能对它们重新赋值
 ```
 const age = 23;
age = 24;//Cannot assign to 'age' because it is a constant or a read-only property.
const people = {
    name:'xiaoming',
    age:18
}
//除非你使用特殊的方法去避免，实际上const变量的内部状态是可修改的
people.age = 20;//{ name: 'xiaoming', age: 20 }
console.log(people)
people = {
    name:'lily',
    age:19
}//Cannot assign to 'people' because it is a constant or a read-only property.
people.sex = 'woman'//Property 'sex' does not exist on type '{ name: string; age: number; }'.

 ```
<!-- ### 解构-->
<!-- 解构数组-->
<!-- ```-->
<!-- let arry = [1, 2];-->
<!-- let [first, second] = arry;//first 1 second 2-->
<!-- let [first, ...rest] = [1, 2, 3, 4];//first 1 rest [2,3,4]-->
<!-- ```-->
<!-- 对象解构-->
<!-- ```-->
<!-- let o = {-->
<!--    a: "foo",-->
<!--    b: 12,-->
<!--    c: "bar"-->
<!--};-->
<!--let { a, b } = o;-->
<!--console.log(a,b)//"foo" 12-->
<!-- ```-->
## 接口
TypeScript的核心原则之一是对值所具有的结构进行类型检查。 它有时被称做“鸭式辨型法”或“结构性子类型化”。（鸭式辨型来自于James Whitecomb Riley的名言："像鸭子一样走路并且嘎嘎叫的就叫鸭子。"通过制定规则来判定对象是否实现这个接口。）
```
interface LabelledValue {
  label: string;
}

function printLabel(labelledObj: LabelledValue) {
  console.log(labelledObj.label);
}

let myObj = {size: 10, label: "Size 10 Object"};
printLabel(myObj);
```
接口就好比一个名字，用来描述上面例子里的要求。
### 可选属性
接口里的属性不全都是必需的。 有些是只在某些条件下存在，或者根本不存在。
```
interface SquareConfig {
  color?: string;
  width?: number;
}

function createSquare(config: SquareConfig): {color: string; area: number} {
  let newSquare = {color: "white", area: 100};
  if (config.color) {
    newSquare.color = config.color;
  }
  if (config.width) {
    newSquare.area = config.width * config.width;
  }
  return newSquare;
}

let mySquare = createSquare({color: "black"});
```
### 只读属性
```
interface Point {
    readonly x: number;
    readonly y: number;
}
let p1: Point = { x: 10, y: 20 };
p1.x = 5; // error!
```
### 额外属性
```
interface SquareConfig {
    color?: string;
    width?: number;
    [propName: string]: any;
}
let squareOptions = { colour: "red", width: 100 };
let mySquare = createSquare(squareOptions);
```
### 函数类型
```
interface SearchFunc {
  (source: string, subString: string): boolean;
}
let mySearch: SearchFunc;
mySearch = function(source: string, subString: string) {
  let result = source.search(subString);
  return result > -1;
}
```
### 可索引的类型
```
interface StringArray {
  [index: number]: string;
}

let myArray: StringArray;
myArray = ["Bob", "Fred"];

let myStr: string = myArray[0];
```
