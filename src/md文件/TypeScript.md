# 数据类型

```ts
let num: number = 1
let str: string = 'a,b'
let boo: boolean = true
let what: number | string = '1'
let gender: 'man' | 'woman' | 'secret'
```

array

```ts
let arr1: number[] = [1]
let arr2: string[] = ['a']
let arr3: (number | string)[] = [1, 'a']
// 泛型
let list: Array<number> = [1, 2, 3];
// 基于接口
interface Strudent{
    name: '', age: 1
}
let stuArr: Strudent[] = []
```

元组tuple：有长度的数组

```ts
let arr4:[string, number, boolean] = ['1',2,true]
```

枚举enum：约束变量只能在一组范围内取值 

```ts
enum Color {
  Red = 'red',
  Green = 'green',
  Blue = 'blue',
}
let myColor: Color = Color.Green;
```

void

```ts
function logMessage(): void {
  console.log("a");
}
```

Any

`let a: any = 1;`

null 和 undefined

```ts
let n:null = null; let u:null = undefined
```

Never: 永远不会发生的值的类型

```ts
function throwError(message: string): never {
  throw new Error(message);
}
```

object

```

```



# 类和继承 class和extends

- class和extends

# 抽象类 abstract

- 可以给定确定的属性和方法，也可以不给定

# 接口 interface

1. 不能给定确定的属性和方法 
2. 多次实现会取合集

```ts
// 接口-有格式的对象
interface OBJ1 {
  name: string,
  age: number
  handelRun: () => void
}
let obj1: OBJ1 = {
  name: 'a',
  age: 12,
  handelRun(){
    console.log('run--')
  }
}
```

## 接口-继承接口 extends

```ts
interface OBJ1 {
  name: string,
  age: number
  handelRun: () => void
}
// 拥有上面三个属性
interface Obj1 extends OBJ1{
	color: string
}
```

## 类-实现接口 implements 

```ts
// 类按照接口主体实现结构，并可以扩展
class 类 implements 接口{
    size: string = '18'
}
```



# 泛型 广泛类型<T>

-  无法自动推断类型时 
-  通过传参方式来确定类型

## 泛型函数

```ts
function fn<T>(params: T): T{
	return params
}

fn<string>('123')
fn('123') // 可以不写，ts自动通过参数判定，但最好写上
fn<string[]>(['1'])
```



## 泛型约束

```ts
// 约束泛型的范围，T -至少- 包含 接口的结构
function fn<T extends 接口>(): Void{}

interface HasLength{
    length: number
}
function fn<T extends HasLength>(params: T){}

fn<string>('bcd')
```



## 多个泛型函数

```ts
function fn<T1, T2>(params1: T1, params2: T2){}

fn<string, number[]>('bcd')
```



## 泛型接口

```ts
interface 接口<T1, T2>{
    name: T1,
    what: T2
}
let obj: 接口<number, string[]> = {
    
}
```



## 泛型类

```ts
class 类名<T>{
    id: T
    constructor(id: T){
        this.id = id
    }
    getId:T(){
        return this.id
    }
}
let p = new 类名<number>(10)
```



# 断言 myVariable as string

- 不确定变量的类型，用断言告诉系统

# 命名空间 namespace

- 就是模块，但不常用

# 装饰器

# 子类和父类方法

## 基础

```ts
class Father{
    // 静态属性&方法-属于类
    static version: string = '10.12'
    static getVersion(){return version}
    // 实例属性-通用
    name: string
    type: string = 'cat' // 默认值，构造函数中修改
    food?: string
    // 构造函数-个性
    construct(...params){
        this.name = params.name
    }
    // 方法
    sayHi(){
        console.log(`${name} sayHi`)
    }
}

const p: MyClass = new MyClass('Tom')
// 有food就拿到food, 没有就拿到 p
p.food?.length
p.sayHi()
Father.getVersion()
```

## 修饰符

- readonly：不能修改（类似 java 的 final）
- private：本类-可以，子类super-不能，实例-不能
- protected：本类-可以，子类super-可以，实例-不能
- public: 默认，本类-可以，子类super-可以，实例-可以

## extends super 继承 

```
class Son extends Father{
 	// 子类写了constructor会覆盖父类
	// 用 super 手动调用 父类 constructor
	constructor(name, age){
		super(name) // 调用父类构造函数
	}
	// 重写方法-覆盖
    sayHi(){
        console.log(`${name} sayHi`)
    }
    // 重写方法-不覆盖
    // readonly or private 修饰
    sayHi(tt):void{
        console.log(`${name} sayHi`)
    }
}
```



# 方法重载

- 为同一个方法写入多个同名但签名不同的方法头，方法实现紧随其后

```ts
class C {
  foo(x: number): void;            /* 第一个签名 */
  foo(x: string): void;            /* 第二个签名 */
  foo(x: number | string): void {  /* 实现签名 */
  }
}
let c = new C();
c.foo(123);     // OK，使用第一个签名
c.foo('aa'); // OK，使用第二个签名
```

# 其他

## type
