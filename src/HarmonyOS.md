# TypeScript

## 数据类型

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



## 类和继承 class和extends

- class和extends

## 抽象类 abstract

- 可以给定确定的属性和方法，也可以不给定

## 接口 interface

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

### 接口-继承接口 extends

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

### 类-实现接口 implements 

```ts
// 类按照接口主体实现结构，并可以扩展
class 类 implements 接口{
    size: string = '18'
}
```



## 泛型 广泛类型<T>

-  无法自动推断类型时 
- 通过传参方式来确定类型

### 泛型函数

```ts
function fn<T>(params: T): T{
	return params
}

fn<string>('123')
fn('123') // 可以不写，ts自动通过参数判定，但最好写上
fn<string[]>(['1'])
```



### 泛型约束

```ts
// 约束泛型的范围，T -至少- 包含 接口的结构
function fn<T extends 接口>(): Void{}

interface HasLength{
    length: number
}
function fn<T extends HasLength>(params: T){}

fn<string>('bcd')
```



### 多个泛型函数

```ts
function fn<T1, T2>(params1: T1, params2: T2){}

fn<string, number[]>('bcd')
```



### 泛型接口

```ts
interface 接口<T1, T2>{
    name: T1,
    what: T2
}
let obj: 接口<number, string[]> = {
    
}
```



### 泛型类

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



## 断言 myVariable as string

- 不确定变量的类型，用断言告诉系统

## 命名空间 namespace

- 就是模块，但不常用

## 装饰器

## 子类和父类方法

### 基础

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

### 修饰符

- readonly：不能修改（类似 java 的 final）
- private：本类-可以，子类super-不能，实例-不能
- protected：本类-可以，子类super-可以，实例-不能
- public: 默认，本类-可以，子类super-可以，实例-可以

### extends super 继承 

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



## 方法重载

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



# Harmony OS

## 封层

- 内核层
  - 内核子系统
  - 驱动子系统
  - 内核抽象层 KAL
- 系统服务层：基础能力+专有能力
- 框架层：听过多种语言的 UI 框架 和 Ability 框架
  - UI 框架
  - Ability 框架
  - 用户程序框架
- 应用层：针对框架层形成的业务逻辑开发
  - FA / PA 业务的最小单元
  - FA: Feature Ability 有 UI 界面：如-视频通话主界面
  - PA:Partical Ability 无 UI 界面：如-视频采集，美颜，夜景
- 分发平台：针对不同设备进行分发

## 设备连接方式 connect

### 交互感觉

- 碰：手机作为中心设备和其他设备碰一下
- 靠：手机距离设备在范围内
- 扫：更远距离

### 交互方式

- 首次连接
- 日常连接

### 视图

- 临时功能-小屏
- 用后即走-中屏
- 沉浸任务-全屏

## 多设备设计

- 同一元素在不同屏幕大小相同：vh
- 同一元素在不同屏幕大小控制：css媒体查询 / js / 弹性伸缩
- 同一元素在不同屏幕上的大小不易超过1.2倍

# DevEco Studio

## 运行项目方式

### 预览器-hot update

- 编辑器最右边侧边 preview

- 在组件/页面中使用 @Preview 可以预览当前组件/页面

- 双向预览：点击TT

- 多设备预览：点击下箭头->Muti-profile preview

- mock 预览

  - mock 数据

  ```ts
  // 1. 引入 mock 组件
  import { MockSetup } from '@ohos/hamock';
  // 2. 定义 mock 数据
   @MockSetup
   randomName() {
    this.species = 'primates'
   }
  // 3. 使用 mock 
  const result = this.species // in previewer, result = primates
  ```

  

  - mock 模块

  > https://developer.huawei.com/consumer/cn/training/course/slightMooc/C101717494752698457

  

  

​		

### 模拟器-no hot update

- Tools - Device Manager
- 配置虚拟机内存
- 启动模拟器
- 运行项目
  - 选择设备-点击运行-log 选择设备
- 不用就关掉，占用系统内存

### 真机-no hot update

- Harmony OS 手机
- 连续点击版本号-您正处在开发者模式
- 系统与更新-开发人员选项-USB 调试-传输文件
- DevEco Studio 选择真机-生成签名
- 点击运行-log 选择设备

## 虚拟机安装

- 登陆华为开发者账号
- +New Emulator 下载虚拟机

## 快捷键

- 代码 菜单栏 code
  - 格式化 ctrl alt l（settings 中可自定义）
  - 代码块包裹 ctrl alt t
- 国际化字符串
  - 打开文件->右上角 open editor
- 包管理
  - 导入：标红的包处单机->导入
  - 清除未使用包：文件上右键->Optimize Inports ctrl alt o
  
- 查找
  - 两次shift
  
- 代码提取-选择要抽取的部分->右键->Refactor->extract method

# ArkUI-方舟 UI

- 又名方舟开发框架
- 构建页面的最小单位是组件
- 声明式开发范式

## 组件

### 组件分类

1. 布局组件：控制布局，Row、Column
2. 基础组件：页面的元素，Text、Button

### 基础组件

#### Blank - 充满空白

#### Toggle - 切换按钮

`Toggle({type: , isOn}){Text('button')}`

#### TextInput - 文本输入

#### Progress - 进度条

#### Toast - 弹窗-消失

#### AlertDialog - 弹窗-不消失

#### ActionSheet - 弹窗-操作列表

#### 选择器弹窗

- TextPickerDialog
- DatePickerDialog
- TimePickerDialog

#### 

### 布局组件

#### Row

#### Colomn

#### ReativeContainer - 较为复杂的布局

- 使用锚点布局

```ts
 Row().width(100).height(100)
      .backgroundColor("#FF3333")
      .id("row1")

    Row().width(100).height(100)
      .backgroundColor("#FFCC00")
      .alignRules({
        left: {anchor: "row1", align: HorizontalAlign.End},
        top: {anchor: "row1", align: VerticalAlign.Top}
      })
      .id("row2")
```

- 使用 barrier

```ts
.barrier([{id: "barrier1", direction: BarrierDirection.RIGHT, referencedId:["row1", "row2"]},
        {id: "barrier2", direction: BarrierDirection.BOTTOM, referencedId:["row1", "row2"]}])
```

- 使用 chainMode

```ts
 Row().width(80).height(80)
    .backgroundColor("#FF3333")
    .alignRules({
      left: {anchor: "__container__", align: HorizontalAlign.Start},
      right: {anchor: "row2", align : HorizontalAlign.Start},
      center: {anchor: "__container__", align: VerticalAlign.Center},
      bias : {horizontal : 0}
    })
    .id("row1")
    .chainMode(Axis.Horizontal, ChainStyle.PACKED)
```



#### Badge组件

```ts
Badge({
    count: 1,
    position: BadgePosition.RightTop,
    style: {
      badgeSize: 20,
      fontSize: 18,
      badgeColor: Color.Brown
    }
    }){
Text(`ele-${ele}-index-${index}`).backgroundColor(Color.Pink).margin(10)
}
```

#### textOverflow 文本超出省略号

```ts
// 必须有maxLines才生效
.textOverflow({
	overflow: TextOverflow.Ellipsis // 省略号
	overflow: TextOverflow.Ellipsis // 溢出裁截 同none
	overflow: TextOverflow.MARQUEE // 溢出滚动，不需要maxLines
}).maxLines(5) 
```



#### Scroll 滚动

```ts
// 纵向滚动必须设置高，横向反之
// 1. 创建 Scroll 实例
scroller: Scroller = new Scroller(
// 2. 绑定
Scroll(this.scroller){
    
}.height(100)
.scrollable(ScrollDirection.Horizontal) // 滚动方向
.scrollBar(BarState.Auto) // 是否显示滚动条
.scrollBarColor()
.scrollBarWidth()
.edgeEffect(EdgeEffect.Spring) // 滚动边缘滑动效果
.onScroll((x, y)=>{
    
}) // 滑动方法

Button('click').onClick(()=>{
    // 触发
    this.scroller.scrollEdge(Edge.Top)
    const distance = this.scroller.currentOffset()
})
```

#### layoutWeight 自适应伸缩

- layoutWeight 会自动给 Coloum 添加 height('100%'), Row 添加 Width('100%')

```
Row(){
  Text('left').backgroundColor(Color.Orange)
    .layoutWeight(1)
  Text('center').backgroundColor(Color.Gray)
    .layoutWeight(2)										 	Text('right').width(60).backgroundColor(Color.Pink)
}.width('100%').backgroundColor('#fff')
```

#### justifyContent 线性布局-flex的简化

- 线性的简化，效率高

- note: Coloum 必须有Height，Row 必须有 Width 才生效

```ts
// 主轴
justifyContent(FlexAlign.SpaceEvenly)
    Start:
    Center:
    End: 
    SpaceBetween: 两边贴边
    SpaceAround: 环绕-靠边间隙0.5
    SpaceEvenly：环绕-靠边间隙1
// 侧轴
alignItems(HorizontalAlign.Start) // 水平-Colomn
    Start:
    Center:
    End: 
alignItems(VerticalAlign.Top) // 垂直-Row
	Top:
	Center:
	Bottom:

```



#### Flex 伸缩布局

```
 Flex({
  direction: FlexDirection.Row, // 主轴方向
  justifyContent: FlexAlign.SpaceAround, // 主轴对齐方式
  alignItems：ItemsAlign.Center, // 侧轴对其方式
  wrap: FlexWrap.Wrap, // 超出换行
}){     
    Text('1').width(80).height(80).backgroundColor(Color.Pink)
    Text('1').width(80).height(80).backgroundColor(Color.Orange)
    Text('1').width(80).height(80).backgroundColor(Color.Pink)
}
```

#### Stack层级布局组件

- 定位的简化，效率高

```
Stack({
	alignContent: Alignment.Center
}){
	Item1(),
	Item2().zIndex(3),
	Item3()
}
```

#### Grid组件 很多盒子换行排列

```ts
Grid(){
  ForEach(this.list, (ele:number, index) => {
    GridItem(){
      Text()
      .backgroundColor(Color.Pink)
      .height(80).border({width: 1}).width(80)
    }
  })
}.columnsTemplate('1fr 1fr 1fr').rowsTemplate('1fr 1fr 1fr')
```



#### ForEach 循环生成

```ts
ForEach(this.list, (ele:number, index) => {
  Text(`ele-${ele}-index-${index}`)
      .heigth(60).aspectRatio(1) // 宽高比
})
```



#### List 可以实现 Grid 的一些布局, 比 Scroll 更灵活

```ts
List(){}
.listDirection(Axis.Vertical) // 主轴方向
.lanes(2, 5) // 调整 列数 和 间距
.alignListItem(ListItemAlign.Center) // 列对其方式
.scrollBar(BarState.Auto) // 滚动条
.divider({ // 行与行之间的线
    strokeWidth: 3
    color: Color.Pink
    startMargin: 10 // 线距离边缘间隙
    endMargin: 10
})
```



#### Tabs

```ts
// 自定义tabBar
@Builder
function tabBarBuilder(){
    
}

Tabs({
  barPosition: BarPosition.Start // 位置
}){
  TabContent(index: number){
    Text('首页内容')
  }.tabBar('首页')
  TabContent(){}.tabBar('推荐')
  TabContent(){}.tabBar('发现')
  TabContent(){}.tabBar(this.tabBarBuilder(3))
}.vertical(false) // 横向
.scrollable(true) // 是否可以滚动切换
.animationDuration(0)
.barMode(BarMode.Scrollable) // 导航太多时是否滚动
.onChange((index)=>{}) // 点击滑动都触发
.onTabBarClick((index)=>{}) // 点击触发
```

#### Swiper

#### WaterFlow

#### Navigation: 根组件

### 组件方法

#### vp2px 

不同设备分辨率不一样，导致像素点大小不一样

1. vh 单位控制位置，保证不同设备视觉一样
2. vp2px(100)，将 100vp 转成 px



## 类型转换

```ts
1. 字符转数字
Number(str)
parseInt(str)
parseFloat(str)

2. 数字转字符串
1 + ''
toString()
toFixed()
```

## 状态变量

```
let test_outer:number = 1

@Entry
@Component
struct Index {
  // 组件内部定义不用 let, 需要 this 访问，只会被加载到 UI 一次，后续更改无效
  test_inner: number = 11
  // 状态变量
  @State message: string = 'index';
  @State test_state: number = 21;
  build() {
    RelativeContainer() {
      Column(){
        Text('test_outer-'+ test_outer)
        Text('test_inner-'+ this.test_inner)
        Text('test_state-'+ this.test_state)
        Button('click').onClick(() => {
          test_outer++
          this.test_inner++
          this.test_state++
          AlertDialog.show({
            message: '弹框'
          })
        })
      }
      .width('100%').height('100%')
      .backgroundColor('#f0f0f0')
      .padding(10)
    }
  }
}
```

- 对象数组中的对象要同步更新，需要替换整个对象，数组用splice

## 状态管理

- PersistentStorage -永久存储
- 其他应用退出后台，数据就会清理
- 当useSharedStorage设置为true，并且storage又被赋值时，useSharedStorage的值优先级更高。

### 组件状态管理

- @State: 自己的状态
  - boolean、string、number 可以观察到变化
  - class、object可以观察到 `Object.keys()` 返回的属性
    - 自身赋值变化（更换整个对象），或第一层属性变化
    - 深层就需要更换第一层整个对象
- @Prop: 父传子
- 传递方法最好用箭头函数，保证方法中 this 指针一直指向父亲，不然儿子触发父亲的方法后，方法体中的 this 会指向儿子，拿不到父亲的属性
- `fatherClick: (index) => {this.handelClick(index)}`

```ts
// 父组件
@Component
struct Father{
	@State info: string = '父亲'
	
	build(){
		Coloum(){
			Son({
				info: this.info,
				changeInfo: (){
					this.info = newInfo
				}
			})
		}
	}
}

// 子组件
@Component
struct Sun{
	@Prop info: string = ''
	changeInfo = ()=>{} // 父亲覆盖儿子，不声明传不进来
	build(){
		Coloum(){
			Text(this.info)
		}
	}
}
```



- @Link

```ts
// 父子组件双向同步
```



- @Provide & @Consume

```ts
// 父和孙双向联动
```

- @Observed & @ObjectLink
  - Observed 深监听类创建的对象
  - 当数据变化
  - 遍历 ObjectLink 订阅的数据，并进行 UI 变化

```ts
// 深监视-对象和数组-双向绑定
// @ObjectLink 不能用在 @Entry 中

// 1. 模块定义类-只能用在类上
@Observed 
class Person{
    
}
// 2. 父组件
@state personList: Person[] = []
person: Person = {name: 1}
// 不会同步更新，因为person没有用@ObjectLink 监听
Text(person.name) 
Son({
    info: person
})

// 3. 子组件
@ObjectLink person: Person
```



### 页面状态管理 LocalStorage 

- @LocalStorageProp
- @LocalStorageLink

```ts
// 1. 定义
let storage = new LocalStorage({count: 0})
// 2. 页面引入
@Entry(storage)
// 3. 组件使用
@LocalStorageProp('count') count: number = 0
@LocalStorageLink('count') count: number = 0
```



### App 状态管理 AppStorage

- @StorageProp
- @StorageLink

```ts
// 1. 定义
Appstorage.setorcreate(count',0)
@Entry()
// 2. 组件使用
@StorageProp('count') count: number = 0
@StorageLink('count') count: number = 0

```



### PersistentStorage-永久存储

- PersistentStorage可以将指定的AppStorage中的属性保存到磁盘中
- PersistentStorage和AppStorage的属性会自动建立双向同步
- 开发者不能直接访问PersistentStorage中的属性，而只能通过AppStorage进行访问
- @storageLink

```ts
// 1. 指定 AppStorage 的 count 属性 绑定到 PersistentStorage
// 如果 AppStorage 没有此属性，会自动声明
PersistentStorage.PersistProp('count',0);
// 2. 使用
@storageLink('count') count:number =0
```



### EventHub

```ts
// 1. UIAbility 中 - 绑定方法
let context = this.context;
// 获取eventHub
let eventhub = this.context.eventHub;
// 执行订阅操作
eventhub.on('event1', this.eventFunc);
eventhub.on('event1', (data: string) => {
  // 触发事件，完成相应的业务操作
});
// 2. 页面中 - 调用方法
private context = getContext(this) as common.UIAbilityContext;

eventHubFunc(): void {
// 不带参数触发自定义“event1”事件
this.context.eventHub.emit('event1');
// 带1个参数触发自定义“event1”事件
this.context.eventHub.emit('event1', 1);
// 带2个参数触发自定义“event1”事件
this.context.eventHub.emit('event1', 2, 'test');
// 开发者可以根据实际的业务场景设计事件传递的参数
}

// 3. UIAbility 中 - 销毁方法
onDestroy(): void {
	this.context.eventHub.off('event1');
}
```

### 用户首选项 Preferences

- Key-Value键值型的数据处理能力，非关系型数据库
- 存储用户个性化设置（字体，夜间模式等）
- 将该数据缓存在内存中，当用户读取的时候，能够快速从内存中获取数据
- 需要持久化时可以使用flush接口将内存中的数据写入持久化文件中
- Preferences不适合存放过多的数据，也不支持通过配置加密

> https://developer.huawei.com/consumer/cn/training/course/slightMooc/C101717498132814493



## 循环列表

- for i
- for of
- for in
- map
- Array.from({length: 10}) // 生成长度为 10 的数组

## 关键字-装饰器

### @Extend:扩展组件(样式、事件)

```ts
// 定义
// 必须指定组件名，只有Text组件能使用
@Extend(Text)
function textFn(parms){
  .fontSize(13).fontColor('#fff').backgroundColor('#333')
}
// 使用
Text(this.message).textFn(parms)

```



### @Styles:抽取通用属性、事件

```ts
// 定义 不支持传参
// 可定义在外层-全局，也可定义在组件内层-组件使用， this
// 不指定组件名，都可以使用，但不能指定某组件独有方法，如：fontSize
@Styles function commonStyles(){
  .width(100).onClick(()=>{
      this.bgColor = Color.Green // 组件内可以this访问到私有属性
  })
}
// 使用
Text(this.message).commonStyles()
```



### @Builder:自定义(结构、样式、事件)

```
// 定义
@Builder
// 全局定义
function myCmpt(){
  Text('这是一个自定义组件').fontSize(18).fontColor(Color.Red)
}

// 组件内局部定义-就是方法
myCmpt(){
  Text('这是一个自定义组件').fontSize(18).fontColor(Color.Red)
}
// 使用
myCmpt()
```

## 自定义组件

```ts
// 定义
@Component
struct MyComponent{
    // 1.传参一：外面直接覆盖
    // 成员变量：有 = ，外部可以覆盖
    @State msg: string = ''
    data: string = ''
    sayHello = () => {} // 成员变量-函数
    cancel: () => void = () => {}// 带类型-函数
    // 成员函数：外部不可覆盖
    sayHi(){}
    
    // 传参二：传递 web 组件
    // 接受 UI 并设置默认值
    @BuliderParam ContentBuilder1: () => void = this.defaultBuilder
	@BuliderParam ContentBuilder2: () => void = this.defaultBuilder
    
    @Builder defaultBuilder(){
        Text('default')
    }
    
    // 主函数
    bulid(){
        Row(){}
    }
}

// 使用-会在 Row 外层再加一层 wrapper
@Builder useContentBuilder1(){
    Button('click')
}
HelloComponent({ // 传参数
    msg：'你好',
    // 多个 BuliderParam 时，指定
    ContentBuilder1： this.useContentBuilder1()
})({ // 传组件
    // 只有一个 BuliderParam 时直接传
    Button('click'),
})
.width().onClick(() => {}) // 加给组件
```



## font icon

```
import font from '@ohos.font'

// 在 aboutToAppear 生命周期中注册
font.registerFont({
	familyName: 'myFont',
	familySrc: fontSrc
})
```



## 路由

- main_pages.json
- or 新建page

```ts
import  router from '@ohos.router'
// 可以返回
router.pushUrl({ 
    url: 'pages/Login',
    params: {}
}, mode) // mode: Standard-一直压栈,Single-将当前栈移至前栈
router.back()
// 不能返回
router.replaceUrl('pages/login')

// 页面栈：先进后出，最多32个
// replaceUrl 删除当前换成新的-只替换不增加
// 获取页面栈长度
router.getLength()

// 清空页面栈
router.clear()
```

### 路由传参

- query
- params

```ts
// 路由参数类型接口
interface RouterParams{
    name:string
}
router.pushUrl({ 
    url: 'pages/login',
    params: {}
})

// getParams() 参数类型取决于传入方，所以需要做一个类型断言
let params =  router.getParams() as RouterParams
```



## 生命周期

- aboutToAppear: 组件将构建
- build：开始构建组件
- onPageShow： 页面触发（路由，页面进入前后台）
- Component is visiable：
- onPageHide：页面隐藏（路由，页面进入前后台）

- aboutToDisappear: 组件将销毁



- onBackPress：点击返回（return true 组织返回）

## 网络请求

### http 模块

### axios 鸿蒙版
## 异步导入模块

如下例所示，import(modulePath)可以加载模块并返回一个promise，该promise resolve为一个包含其所有导出的模块对象。该表达式可以在代码中的任意位置调用。

```ts
let modulePath = prompt("Which module to load?");
import(modulePath).then(obj => <module object>).catch(err => <loading error>
```

如果在异步函数中，可以使用let module = await import(modulePath)。

```typescript
// say.tsexport function hi() {  console.log('Hello');} export function bye() {  console.log('Bye');}
```

那么，可以像下面这样进行动态导入：

```ts
async function test() {  
  let ns = await import('./say');  
  let hi = ns.hi;  
  let bye = ns.bye;  
  hi();  bye();
}
```

# ArkWeb

- 应用程序中加载 Web 内容，可以实现移动端混合开发（Hybrid App）

## Web()：提供网页显示能力

```ts
Web(options:{ 
	src: Resourcestr, 
	controller: WebviewController | WebController, 
	renderMode?: RenderMode, 
	incognitoMode?: boolean
})
```



## Webview()：提供web控制能力的相关接口

- 例如控制Web组件加载的内容控制Web内容后退前进，以及异步执行JavaScript脚本等能力。

## 加载网络页面

```ts
// 1.定义
webviewController: webviewController = new webview.WebviewController();

// 2.使用 Web 加载页面
Web({src: 'www.test.com', controller: this.webviewController})

// 3.使用 Webview 跳转
this.webviewController.loadUrl('www.test1.com')
```



## 加载本地页面

```ts
// 1.定义
webviewController: webviewController = new webview.WebviewController();

// 2.使用 Web 加载页面
Web({src: $rawfile('local.html'), controller: this.webviewController})

// 3.使用 Webview 跳转
this.webviewController.loadUrl($rawfile('local1.html'))
```



## 加载 html 文本

```ts
// 1.定义
webviewController: webviewController = new webview.WebviewController();

// 2.使用 Web 加载页面
Web({src: $rawfile('local.html'), controller: this.webviewController})

// 3.使用 Webview 跳转
this.webviewController.loadData(
"<html><body bgcolor=\"white\">Source:<pre>source</pre></body></html>""text/html",
"UTF-8"
)
```



# 应用程序框架 Ability Kit

- 应用内交互、应用间交互、设备流转
- 应用程序框架：一种编程框架，简化应用程序的开发过程
- 应用模型：应用程序的模型
- 应用框架是抽象，应用模型是具体实现方式

# Stage 应用模型

- 施工图纸
- 规范：程序运行流程、项目结构、文件功能、项目配置
- 根据业务的最小单元进行拆分
  - 一个 app 可以有多个 module，entry 文件夹就是一个 module
  - 一个 Module 就是一个应用程序
  - 一个 module 可以有多个 UIAbility
  - 每个 UIAbility 实例，对应一个最近任务列表的任务
  - 每个 UIAbility 都会在任务栏显示，并拥有自己的图标与名字
  -  一个UIAbility 可以有多个页面
  -  不同应用组件共享同一个 ArkTS 实例

## 编译前-工程目录

项目（应用工程）

- AppScope/
  - resources/: 资源文件
  - app.json5：app 级配置文件
- entry/：module，编译生成一个HAP包
  - src/main/
    - resources/：module 资源文件
      - base/profile：module 自定义配置
    - rawfile：直接打包进应用，不编译，无资源ID
    - ets/：ArkTs 文件
      - entryability/：entry 模块 UIAbility 的入口，指定当前UIAbility 组件默认页面
      - page/：module  的页面
    - module.json：module  级配置文件，默认的 UiAbility 组件，应用权限信息（如网络）
    - build-profile.json5：当前的 module  信息 、编译信息配置项，包括 buildOption、targets 配置等
    - hvigorfile.ts：module 编译构建任务脚本，开发者可以自定义相关任务和代码实现
  - obfuscation-rules.txt：混淆规则文件
- oh_modules：项目级三方库依赖信息。
- build-profile.json5：app 配置信息，包括签名signingConfigs、产品配置products等。
- hvigorfile.ts：app级/ module 级编译构建任务脚本。

## 编译后

***.app

- entry.hap
- feature.hap
- library.hap
  - Shared Library 生成 HSP 包，复用。可以避免HAR造成的多包间代码和资源的重复拷贝，从而减小应用包大小
  - Static Library 生成 HAR 包
- pack.info：DevEco 自动生成，描述 HAP 和 HSP的属性

## 应用签名

## 云端部署（应用市场）

- 校验签名
- 拆分 App 为 HAP 和 HSP
- 对 HAP 和 HSP 重签名
- 分发 HAP 和 HSP

## 用户下载

- 应用市场下载
- 安装-包管理服务
- 部署 HAP 和 HSP-生成文件系统

# UIAbility 组件

- 应用的交互方式
  - 直接点击桌面图标
  - 任务栏中切换应用
  - 其他应用唤醒
- UIAbility 是系统调度的基本单位
- 包含用户界面的应用组件，用于和用户交互
- 每个被拉起的 UIAbility 都对应一个任务栏中的任务窗
- UIAbility 启动模式
  - singleton：默认- 每次 startAbility() 都复用同一个 UIAbility 实例
  - multiton：每次 startAbility() 创建一个该类型的新 UIAbility 实例
  - specified：指定实例

- UIAbility冷启动和UIAbility热启动
  - UIAbility实例处于完全关闭状态下被启动
  - UIAbility实例已经启动并在前台运行过


## 配置启动页面

- src>main>ets>entryability：应用/服务的入口配置-配置启动页

## UIAbility 组件生命周期

- onCreate
- onForeground：前台
- onWindowStageCreate(windowStage)：界面舞台搭建时
  - windowStage.loadContent(page, ()=>{})：设置 UIAbility 要加载的页面

- onBackground：后台，释放 background 时无用资源
- onWIndowStageDestroy：界面舞台销毁时
- onDestroy：资源释放，数据保存
- 折叠屏：可直接显示两个 UIAbility
- singleton 模式时，调用startAbilit() 进入 onNewWant() 回调而不是 onCreate()

```ts
 // 设置WindowStage的事件订阅（获焦/失焦、可见/不可见）
try {
  windowStage.on('windowStageEvent', (data) => {
    let stageEventType: window.WindowStageEventType = data;
    switch (stageEventType) {
      case window.WindowStageEventType.SHOWN: // 切到前台
        console.info('windowStage foreground.');
        break;
      case window.WindowStageEventType.ACTIVE: // 获焦状态
        console.info('windowStage active.');
        break;
      case window.WindowStageEventType.INACTIVE: // 失焦状态
        console.info('windowStage inactive.');
        break;
      case window.WindowStageEventType.HIDDEN: // 切到后台
        console.info('windowStage background.');
        break;
      default:
        break;
    }
  });
} catch (exception) {
  console.error('Failed to enable the listener for window stage event changes. Cause:' + JSON.stringify(exception));
}
```



## 同 module 拉起 UIAbility 组件

- 每个 UIAbility 中都包含一个 context 属性
- context 的方法
  - startUIAbility()
  - startAbilityForResult()
  - connectUIAbility()
  - terminateSelf()
  - terminateSelfWithResult()

```ts
// 1. 获取 context 上下文
context = getContext(this) as common.UIAbilityContext
// 2. 准备 want 作为 UIAbility 的参数
let wantInfo: Want = {
    deviceID: '', // 空-本设备
    bundle Name： 'com.example.helloworld', // 应用包名
    moduleName: 'entry', // 模块名
    abilityName: 'ability', // 要拉起的UIAbility
    parameters: { // 参数
    
	}
}
// 3. 用 context 传入 want
this.context.startUIAbility(wantInfo, launchParam)
```

## 跨 module 拉起 UIAbility 组件

- 运行时候配置 多个 HUB 包，此时会编译多个模块
- 配置同上，module 名改为另一个模块



## 拉起 UIAbility 组件跳转指定页面

```ts
// 1. paramters 中传入页面信息
let want: Want = {
  deviceId: '', // deviceId为空表示本设备
  bundleName: 'com.samples.stagemodelabilityinteraction',
  moduleName: 'entry', // moduleName非必选
  abilityName: 'FuncAbility',
  parameters: { // 自定义参数传递页面信息
    router: 'funcA'
  }
```



### 冷启动

```ts
import { AbilityConstant, Want, UIAbility } from '@kit.AbilityKit';
import { hilog } from '@kit.PerformanceAnalysisKit';
import { window, UIContext } from '@kit.ArkUI';

export default class FuncAbility extends UIAbility {
  funcAbilityWant: Want | undefined = undefined;

  onCreate(want: Want, launchParam: AbilityConstant.LaunchParam): void {
    // 接收调用方UIAbility传过来的参数
    this.funcAbilityWant = want;
  }

  onWindowStageCreate(windowStage: window.WindowStage) {
    // Main window is created, set main page for this ability
    let url = 'pages/Index';
    if (this.funcAbilityWant?.parameters?.router && this.funcAbilityWant.parameters.router === 'funcA') {
      url = 'pages/Page_ColdStartUp';
    }
    windowStage.loadContent(url, (err, data) => {
      // ...
    });
  }
}
```



### 热启动

- 在onWindowStageCreate()生命周期回调中，通过调用[getUIContext()](https://developer.huawei.com/consumer/cn/doc/harmonyos-references-V5/js-apis-window-V5#getuicontext10)接口获取UI上下文实例[UIContext](https://developer.huawei.com/consumer/cn/doc/harmonyos-references-V5/js-apis-arkui-uicontext-V5)对象

```ts
import { hilog } from '@kit.PerformanceAnalysisKit';
import { Want, UIAbility } from '@kit.AbilityKit';
import { window, UIContext } from '@kit.ArkUI';

const DOMAIN_NUMBER: number = 0xFF00;
const TAG: string = '[EntryAbility]';

export default class EntryAbility extends UIAbility {
  funcAbilityWant: Want | undefined = undefined;
  uiContext: UIContext | undefined = undefined;

  // ...

  onWindowStageCreate(windowStage: window.WindowStage): void {
    // Main window is created, set main page for this ability
    hilog.info(DOMAIN_NUMBER, TAG, '%{public}s', 'Ability onWindowStageCreate');
    let url = 'pages/Index';
    if (this.funcAbilityWant?.parameters?.router && this.funcAbilityWant.parameters.router === 'funcA') {
      url = 'pages/Page_ColdStartUp';
    }

    windowStage.loadContent(url, (err, data) => {
      if (err.code) {
        return;
      }

      let windowClass: window.Window;
      windowStage.getMainWindow((err, data) => {
        if (err.code) {
          hilog.error(DOMAIN_NUMBER, TAG, `Failed to obtain the main window. Code is ${err.code}, message is ${err.message}`);
          return;
        }
        windowClass = data;
        this.uiContext = windowClass.getUIContext();
      });
      hilog.info(DOMAIN_NUMBER, TAG, 'Succeeded in loading the content. Data: %{public}s', JSON.stringify(data) ?? '');
    });
  }
}
```

- onNewWant()回调中解析调用方传递过来的want参数，通过调用UIContext中的[getRouter()](https://developer.huawei.com/consumer/cn/doc/harmonyos-references-V5/js-apis-arkui-uicontext-V5#getrouter)方法获取[Router](https://developer.huawei.com/consumer/cn/doc/harmonyos-references-V5/js-apis-arkui-uicontext-V5#router)对象，并进行指定页面的跳转

```ts
import { AbilityConstant, Want, UIAbility } from '@kit.AbilityKit';
import { hilog } from '@kit.PerformanceAnalysisKit';
import type { Router, UIContext } from '@kit.ArkUI';
import type { BusinessError } from '@kit.BasicServicesKit';

const DOMAIN_NUMBER: number = 0xFF00;
const TAG: string = '[EntryAbility]';

export default class EntryAbility extends UIAbility {
  funcAbilityWant: Want | undefined = undefined;
  uiContext: UIContext | undefined = undefined;

  onNewWant(want: Want, launchParam: AbilityConstant.LaunchParam): void {
    if (want?.parameters?.router && want.parameters.router === 'funcA') {
      let funcAUrl = 'pages/Page_HotStartUp';
      if (this.uiContext) {
        let router: Router = this.uiContext.getRouter();
        router.pushUrl({
          url: funcAUrl
        }).catch((err: BusinessError) => {
          hilog.error(DOMAIN_NUMBER, TAG, `Failed to push url. Code is ${err.code}, message is ${err.message}`);
        });
      }
    }
  }
  // ...
}
```

# 开发过程-example

> https://developer.huawei.com/consumer/cn/training/course/slightMooc/C101717497640610394
