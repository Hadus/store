# vue2

[TOC]

## 介绍
 > https://cn.vuejs.org/

### 优点：
+ 渐进式开发框架
+ 做单页面应用程序
+ 生命式编码
+ 虚拟dom+def算法
+ 比angular小巧，有很多第三方库配合开发
+ MVVM 思想让开发只关注于业务逻辑（VM）层的开发
+ 到底用哪个框架，需要大家讨论，公司决定
+ 把angular中的指令，拆分成为指令和组件两部分
+ VUE 存在很多的问题，包括代码问题，性能问题，大项目建议使用React

### 框架与类库的区别
+ 框架：提供一整套解决方案，对项目侵入性大，更换时需要重构整个项目
+ 库：多用于解决某些特定问题，对项目侵入性小，更换方便
+ jquery一个很重要的优点是：提供操作dom的很多api，而vue不提倡操作dom，而要让开发者更注重业务逻辑层

### MVVM：
+ M：model 页面数据层，数据的增删改查，dom 监听
+ V：view 视图层
+ VM：viewModel model和view之间的调度层

### MVC：
+ M：model 页面数据层，数据的增删改查，dom 监听
+ V：view 视图层
+ C：controller 逻辑层（包括路由模块）

## VUE实现双向数据绑定以及脏值检查的原理

- 脏值检查是包含在双向数据绑定过程中的一个重要的环节
- 脏值检查会除了 双向数据绑定的 input 之外还更新其他所有绑定了该属性的 view
- 通过Object.defineProperty()来劫持各个属性的setter，getter，在数据变动时发布消息给订阅者，触发相应的监听回调
- 监听数组：Object.defineProperty()：先将数组转化为对象
- 步骤
    + 数据劫持 Object.defineProperty()：我们为每个vue属性用Object.defineProperty()实现数据劫持，为每个属性分配一个订阅者集合的管理数组dep；
    + 订阅者 v-bind（model==>view）：然后在编译的时候在该属性的数组dep中添加订阅者，v-model会添加一个订阅者，{{}}也会，v-bind也会，只要用到该属性的指令理论上都会，
    + 监听事件 v-on（view==>model）：接着为 input 会添加监听事件，修改 input 就会为该属性赋值，触发该属性的set方法，在set方法内通知订阅者数组dep，订阅者数组循环调用各订阅者的update方法更新视图。
- 实现代码

```javascript
function defineReactive (obj, key, val) {
    var dep = new Dep();
    Object.defineProperty(obj, key, {
      get: function() {
        //添加订阅者watcher到主体对象Dep
        if(Dep.target) {
          // JS的浏览器单线程特性，保证这个全局变量在同一时间内，只会有同一个监听器使用
          dep.addSub(Dep.target);
        }
        return val;
      },
      set: function (newVal) {
        if(newVal === val) return;
        val = newVal;
        console.log(val);
        // 作为发布者发出通知
        dep.notify();//通知后dep会循环调用各自的update方法更新视图
      }
    })
}
function observe(obj, vm) {
    Object.keys(obj).forEach(function(key) {
        defineReactive(vm, key, obj[key]);
    })
}

```



## 基本概念

### 使用过程

+ vue release 中包含
    - vue: runtime-only: 用在服务器上只运行 vue 项目，不认识main.js 的 template，但代码更轻
    - vue: runtime-compiler：用在开发及服务器
+ 引入vue.js会生成一个全局的Vue构造函数
+ 实例化Vue这个构造函数-并创建VM层（实例化过程）和M层（data）
+ data、computed、methods、watch的区别
    - data：定义属性
    - computed：属性调用，定义计算属性，value值为方法（返回一个属性），结果会被缓存
    - methods：定义方法，主要处理业务逻辑
    - watch：value值为对象，监控某些值的变化，
```javascript
var vm = new Vue({
    el: '#app', // 需要vue控制的元素
   	// VUE 内部会将 template 的 value 替换掉 #app
  	// template 会先被编译成 render 方法，然后形成虚拟 DOM
    // 而其他组件的 template 都已经在 build 时候被 webpack(vue-template-compiler) 处理成 render 函数了
  	// 	所以尽量使用 vue.runtime
  	template: ``, 
    render(createdElement) { // 将组件compt放在#app处(webpack中使用)
      return createdElement(compt); // 返回一个组件对象，并替换el指定的容器
    }
    data: { // 数据
        msg: 'hello-world'
    },
    methods:{// 方法：书写业务逻辑
        click1(){
            console.log(1);
        },
        click2(a){
            console.log(a);
        }
    },
    filters: {// 自定义私有过滤器

    },
    directives: {// 自定义私有指令

    },
    components: {// 自定义私有组件，或者直接注册组件

    },
    http: {// 配置http
        root: '/root',
        headers:{}
    },
    router: routerObj,// 将路由与模块绑定，用来监听hash变化触发路由
    watch: { // 监控vue实例的数据变化
        msg(newVal,oldVal){},
        name: {
          immediate: true, // 是否立即监视一次
          deep: true, // 是否深层监视
          handler(){}
        },
        '$route.path'(newVal,oldVal){}, // 可用来监控路由变化
    },
    computed: { // 计算属性
        // 1.配置计算属性，属性和data平级，使用方式相同
        // 2.当n1,n2发生改变时候触发函数刷新n3的值
        // 3.n3不管被引用几次只会触发一次函数，结果会被缓存下来
        n3() {
            console.log('触发n3计算函数');
            return parseInt(this.n1 || 0) + parseInt(this.n2 || 0);
        }
    },

    // 实例的生命周期函数：1.创建期间，2.运行期间，3.销毁期间

    /*创建阶段*/
    // 1. 创建事件和生命周期
    beforeCreate() { // 生命周期函数-1-实例创建之前执行
        // 此时data/methods中的数据还未初始化
    },
    // 2. 开始创建实例
    created() { // 生命周期函数-2-实例创建完成执行
        // 此时data/methods中的数据已经被初始化
    },
    // 3. 开始编译模板（就是el绑定的元素），包括directives，并在内存中渲染为一个虚拟DOM
    beforeMount() { // 生命周期函数-3-虚拟DOM挂载到页面之前
        // 页面的数据还是旧的，没有用虚拟DOM替换页面的dom
    },
    // 4.将虚拟Dom挂载到页面上替换原始的dom 
    mounted() { // 生命周期函数-4-虚拟DOM挂载到页面之后（实例期间的最后一个周期函数，实例被完全创建完成）
        // 操作页面上的DOM节点，最早只能在这里操作
    },

    /*运行阶段*/
    beforeUpdate() { // 生命周期函数-5-data发生改变时触发，data数据不改变则不触发
        // 数据被更新，但页面更新之前
    },
    // 1. 更新虚拟DOM，并重新挂载到页面中
    updated() { // 生命周期函数-6-data发生改变时触发
        // 页面更新完成之后
    },

    /*销毁阶段--页面被卸载*/
    beforeDestroy() { // 生命周期函数-6-实例销毁之前
        // 实例上面的所有内容均可用
    },
    destroyed() { // 生命周期函数-7-实例销毁后

    },
});
```

### 绑定数据

```html
<div id="app">
    <p>{{msg}}</p>
    <p v-text="msg">{{msg}}</p>
</div>
```
### 注意

- 不要将body实例化为vue，2.x后的版本不建议这么做



## 内置指令

### 绑定数据
- {{}}：插值表达式
- v-text="" ：不会有闪烁，不需要v-cloak
- v-html="" ：绑定数据中含有html
- v-once
- v-pre: 不解析标签，直接显示内容

### 闪烁：v-cloak 
- 添加了该属性的元素，在vue解析时候会先display:none，解析完成并绑定数据后在显示

### v-once：数据只展示第一次的值，不会改变

```javascript
<div v-once>{{mes}}</div>
```

### 绑定属性：v-bind
- v-bind:title（简写为:title）
- 绑定类名 :class
```html
    <div class="box" :class="['all',flag?'bg-red':'bg-green']">盒子</div>
    <div class="box" :class="['all',{'bg-green':true}]">盒子</div>
    <div class="box" :class="{all:true,'font-size':true}">盒子</div>
    <div class="box" :class="obj">盒子</div>
```
```javascript
new Vue({
    el: '#app',
    data: {
        flag: true,
        obj: {
            'all': true,
            'bg-green': true,
            'font-big': true
        }
    }
});
```
- 绑定行内样式 :style
```html
<div class="box all" :style="{'background-color':'red',color:'#fff'}">盒子</div>
<div class="box all" :style="obj1">盒子</div>
<div class="box all" :style="[obj1,obj2]">盒子</div>
```
```javascript
new Vue({
    el: '#app',
    data: {
        obj1: {
            'background-color': 'green',
            color: 'white'
        },
        obj2: {
            'background-color': '#ccc',
            'font-size': '40px'
        }
    }
});
```

### 监听事件：v-on

- v-on:click(简写为：@)
    + 不传参数的话，绑定函数名称自动触发
    + 默认会传入 event 对象
    + 后者可以用 $event 去拿
    ```html
    <div id="app">
        <button v-on:click="click1()">不传参数</button>
        <button v-on:click="click2('传参数')">传参数</button>
        <button @click="click3">data中的数据</button>
      	<button @click="click4">data中的数据</button>
      	<button @click="click5(123, $event)">data中的数据</button>
    </div>
    ```
    ```javascript
    new Vue({
        el: '#app',
        data: {
            msg: 'data中的数据'
        },
        methods: {
            click1() {
                console.log('不传参数');
            },
            click2(a) {
                console.log(a);
            },
            click3() {
                console.log(this.msg); // 这里拿到的 event
            },
          	click4(event, msg) {
                console.log(this.msg);
            },
          	click4(msg, event) {
                console.log(this.msg);
            }
        }
    });
    ```
    
- 监控多个事件
`<button v-on="{mouseenter: onEnter,mouseleave: onLeave}">鼠标进来1</button>`
- 绑定多个事件
`<button @click="a(),b()">点我ab</button>`

- 事件修饰符：直接在事件后面使用
    + .stop：阻止自己向上冒泡/向下捕获
    + .prevent：阻止默认行为
    + .capture：元素内部子元素以捕获方式触发事件
    + .self：只触发自己绑定的事件，不触发别的元素作用在自己身上的冒泡/捕获，依然会触发自己作用于别的元素上的冒泡/捕获
    + .once：只触发一次事件，别人作用于自己的冒泡/捕获和自身事件合并只触发一次，依然会触发自己作用于别人身上的冒泡/捕获
    + .prevent.once：让其他事件处理符和自身事件都只触发一次，与顺序无关

- 内置按键修饰符
    + .113（直接加键盘对应的码值即可，为了好记，下面自定义按键修饰符给码值绑定别名）
    + .enter
    + .tab
    + .delete（捕获删除和退格）
    + .esc
    + .space
    + .up
    + .down
    + .right
    + .left

- 给按键绑定别名（就是将键盘码值绑定别名）
```html
<label for="">enter<input type="text" @keydown.enter="keydown('enter')"></label>
<label for="">113---f2<input type="text" @keydown.113="keydown('未绑定f2')"></label>
<label for="">绑定f2<input type="text" @keydown.f2="keydown('绑定f2')"></label>
```
```javascript
Vue.config.keyCodes.f2 = 113;
Vue.configkeyCodes = {
    v: 86,
    'media-play-pause': 179, // 不能用驼峰
    up: [38, 87]
};
```

### 数据双向绑定：v-model

- v-model="value" 

```javascript
<input type="text" v-model="mes">
// v-model 双向绑定相当于：绑定 model==>view: value + 监听 input,绑定 view==>model
<input type="text" v-bind:value="mes" v-on:input="mes = $event.target.value">
```



- v-model.lazy
  - 失去焦点和 enter 时候才会刷新值
- v-model.number
- v-model.trim

### 循环：v-for（必须使用key，key只接受string/number类型数据）
- 循环数组
  `<p v-for="(ele,index) in list" :key="index">ele--{{ele}}---index--{{index}}</p>`

- 循环对象
  `<p v-for="(ele,key,index) in obj" :key="index">ele--{{ele}}---key--{{key}}---index--{{index}}</p>`

- 循环数字：index从1开始
  `<p v-for="(ele,index) in 5">ele--{{ele}}---index--{{index}}</p>`

- 如果只是通过数组索引改变值，不会重新渲染数据

  `this.arr[0] = 'b'`

### 显示隐藏 v-if/v-else和/v-show
- v-if/v-else-if/v-else :较高的代码执行消耗
- v-show :较高的初始渲染消耗

## 计算属性 computed

### 基本使用

- 计算属性的方法只执行一次，然后缓存结果

```javascript
computed: { // 计算属性
        // 1.配置计算属性，属性和data平级，使用方式相同
        // 2.当n1,n2发生改变时候触发函数刷新n3的值
        // 3.n3不管被引用几次只会触发一次函数，结果会被缓存下来
        n3() {
            console.log('触发n3计算函数');
            return parseInt(this.n1 || 0) + parseInt(this.n2 || 0);
        }
    },
```

### getter && setter：就是上面的详细写法

```javascript
computed: {
  fullname: {
    set(){ // 一般不用，所以直接删除，这样就是只读属性
      
    },
    get(){
      return "勒布朗.james"
    }
  }
}
```



## 自定义过滤器

### 自定义过滤器——全局
```javascript
Vue.filter('timeFomat', timeStr => { // 第一个参数默认是进入管道的字符串
    var dateObj = new Date(timeStr);
    var y = dateObj.getFullYear();
    var m = dateObj.getMonth() + 1;
    var d = dateObj.getDate();
    return `${y}-${m}-${d}`;
});
```
```html
<h3>全局：{{time|timeFomat}}</h3>
<h3>私有：{{time|timeFomat_mine('aaa')}}</h3>
```
### 自定义过滤器-私有
```javascript
var app = new Vue({
    el: '#app',
    data: {
        time: new Date
    },
    filters: {
        timeFomat_mine(timeStr, addStr) { // 第一个参数默认是进入管道的字符串
            var dateObj = new Date(timeStr);
            var y = dateObj.getFullYear();
            var m = dateObj.getMonth() + 1;
            var d = dateObj.getDate();
            return `${y}-${m}-${d}------${addStr}`;
        }
    }
})

// 或者
app.filters =  {
    timeFomat_mine(timeStr, addStr) { // 第一个参数默认是进入管道的字符串
        var dateObj = new Date(timeStr);
        var y = dateObj.getFullYear();
        var m = dateObj.getMonth() + 1;
        var d = dateObj.getDate();
        return `${y}-${m}-${d}------${addStr}`;
    }
};
```

## 自定义指令
### 自定义指令-全局
```html
<p v-font-weight="900">color</p>
<p v-background="'pink'">color</p>
```
```javascript
Vue.directive('font-weight', {
    // 钩子函数
    bind(el, binding, vnode, oldVnode) { // 触发一次，指令绑定到元素上时，元素还未放在DOM树上渲染
        // 样式相关的操作，一般都可以在bind中执行

        // el---元素---DOM对象
        // binding: {
        //      name: 指令名称,
        //      value: 指令绑定值（计算之后）,
        //      expression: 指令绑定值的字符串形式（不计算）,
        //      oldValue: 指令绑定的前一个值，仅在update和componentUpdated中使用，无论值是否改变都可用,
        //      arg: 传给指令的参数,
        //      modifiers:包含修饰符的对象 
        // }
        // vnode: // VUE编译时产生的虚拟节点
        // oldVnode: // 上一个虚拟节点，仅在update和componentUpdated中使用

        el.style.fontWeight = binding.value;
    },
    inserted(el, ) { // 触发一次，元素插入到父节点中（父节点存在即可，不必存在在document中）
        // JS相关的操作，一般放在inserted中执行

        el.focus();
    },
    updated() { // 触发多次，vnode更新时

    },
    componentUpdated() { // 所有组件的vnode及其孩子的vnode更新时触发

    },
    ubind() { // 触发一次，指令解绑元素上时

    }
});
Vue.directive('background', (el, binding) => { // 一般只用到钩子函数bind+inserted，直接用这种简写方式
    el.style.background = binding.value;
});
```

### 自定义指令-局部
```html
<p v-font-size="24">color</p>
<input type="text" value="hhhhhhh" v-color="'red'" v-focus>
```
```javascript
var vm = new Vue({
    el: '#app',
    data: {

    },
    directives: {
        fontSize(el, binding) { // 一般只用到钩子函数bind+inserted，直接用这种简写方式
            el.style.fontSize = binding.value + 'px';
        },
        color: {
            bind(el, binding) {
                el.style.color = binding.value;
            },
            inserted(el, binding) {
                el.focus();
            }
        }
    }
})

// 或者
vm.directives = {  
    fontSize(el, binding) { // 一般只用到钩子函数bind+inserted，直接用这种简写方式
        el.style.fontSize = binding.value + 'px';
    },
    color: {
        bind(el, binding) {
            el.style.color = binding.value;
        },
        inserted(el, binding) {
            el.focus();
        }
    }};
```

## 插槽slot

### 基本使用

```javascript
<FancyButton>
  Click me! <!-- 插槽内容 -->
</FancyButton>

<button class="fancy-btn">
  <slot>默认内容</slot> <!-- 插槽出口 -->
</button>
```

### 具名插槽 v-slot: header ===>#header

```javascript
<div class="container">
  <header>
    <slot name="header"></slot>
  </header>
  <main>
    <slot></slot>
  </main>
  <footer>
    <slot name="footer"></slot>
  </footer>
</div>

<BaseLayout>
  <template #header>
    <h1>Here might be a page title</h1>
  </template>

  <template #default>
    <p>A paragraph for the main content.</p>
    <p>And another one.</p>
  </template>

  <template v-slot:footer>
    <p>Here's some contact info</p>
  </template>
</BaseLayout>
```



## 动画

###  内置`<trasition></trasition>`
- 使用样式:CSS中默认以v-开头，指定了transition 的name之后 ，CSS中以name开头
```html
<head>
    .v-enter,
    .v-leave-to {
        opacity: 0;
        transform: translateX(100px);
    }

    .v-enter-active,
    .v-leave-active {
        transition: all 1s;
    }

    .my-enter,
    .my-leave-to {
        opacity: 0;
        transform: translateY(100px);
    }

    .my-enter-active,
    .my-leave-active {
        transition: all 1s;
    }
    </style>
</head>
<body>
    <div id="app">
        <div class="box">
            <button @click="flag1=!flag1">toggle</button>
            <transition>
                <h3 v-show="flag1">这是一个h3</h3>
            </transition>
        </div>
        <hr>
        <div class="box">
            <button @click="flag2=!flag2">toggle</button>
            <transition name="my">
                <h3 v-show="flag2">这是一个h3</h3>
            </transition>
        </div>
    </div>
</body>
```
```css
/*定义离开过渡的结束状态。在离开过渡被触发之后下一帧生效 (与此同时 v-leave 被删除)，在过渡/动画完成之后移除。*/
/*v-move对于设置过渡的切换时机和过渡曲线非常有用*/
    .v-move{
        transition: all 1s;
    }

    .v-leave-to {
        position: absolute;
    }
```

- 使用类名
  `他们的优先级高于普通的类名，这对于 Vue 的过渡系统和其他第三方 CSS 动画库，如 Animate.css 结合使用十分有用。`
    + enter-class
    + enter-active-class
    + enter-to-class (2.1.8+)
    + leave-class
    + leave-active-class
    + leave-to-class (2.1.8+)
```html
<div class="box">
    <button @click="flag1=!flag1">toggle</button>
    <transition enter-active-class="slideOutRight" leave-active-class="rotateOutUpLeft" :duration="100">
        <h3 v-show="flag1" class="animated">这是一个h3</h3>
    </transition>
    <transition enter-active-class="slideOutRight" leave-active-class="rotateOutUpLeft" :duration="{enter:1000,leave:300}">
        <h3 v-show="flag1" class="animated">这是第二个h3</h3>
    </transition>
</div>
```
- 组件动画
    + name属性：指定name，类名用name-开头
    + mode属性：组件切换时候指定切换方式in-out/out-in
```html
<transition mode="out-in">
    <component :is="'login'"></component>
</transition>
```
- v-for循环出来的元素组`<transition-group></transition-group>`
    + name属性：指定name，类名用name-开头
    + appear属性：可以实现元素组第一次加载时的入场动画
    + tag属性：指定将渲染为什么元素，不指定会渲染为span
    + v-move对于设置过渡的切换时机和过渡曲线非常有用

```html
<transition-group appear tag="ul">
    <li v-for="(ele,index) in list" :key="index"></li>
</transition-group>
```

### 钩子函数--多用于设置半场动画
- 推荐对于仅使用 JavaScript 过渡的元素添加 v-bind:css="false"，Vue 会跳过 CSS 的检测。这也可以避免过渡过程中 CSS 的影响
- 当只用 JavaScript 过渡的时候，在 enter 和 leave 中必须使用 done 进行回调。否则，它们将被同步调用，过渡会立即完成。
```html
<transition
  v-on:before-enter="beforeEnter"
  v-on:enter="enter"
  v-on:after-enter="afterEnter"
  v-on:enter-cancelled="enterCancelled"

  v-on:before-leave="beforeLeave"
  v-on:leave="leave"
  v-on:after-leave="afterLeave"
  v-on:leave-cancelled="leaveCancelled"
>
  <!-- ... -->
</transition>
```
```javascript
methods: {
    /*动画的生命周期函数*/
    beforeEnter(el) { // 生命周期函数-1-动画入场前
        // 设置动画入场前的起始样式
    },
    enter(el, done) { // 生命周期函数-2-动画入场
        // 设置动画入场，样式、动画时间及方式
        el.offsetWidth; // el.offsetHeight;el.offsetTop;el.offsetLeft; 这句话没有特定的作用，但要动画必须加上

        done(); // 这里的done就是afterEnter函数的引用，不调用的话，afterEnter会出现延迟
    },
    afterEnter(el) { // 生命周期函数-3-动画入场后
        // 设置动画入场完成的样式
    },
    beforeleave(el) {

    },
    leave(el) {
        done(); // 
    },
    afterleave(el) {

    },
}
```

## 自定义组件
- 拆分vue实例代码量
- 需要什么页面小功能调用什么组件
- 组件化：前端，从UI界面角度划分，模块化：后端，从业务功能角度划分
- 注意：组件template必须有且只有一个根元素包裹

### 自定义组件-全局 
- 自定义
```html
<body>
    <div id="app">
        <h1>全局组件</h1>
        <all-hello1></all-hello1>
        <all-hello2>adf</all-hello2>
        <all-hello3></all-hello3>
        <all-hello4></all-hello4>
        <hr>
        <h1>私有组件</h1>
        <my-hello1></my-hello1>
    </div>
    <!-- 组件模板 -->
    <template id="template1">
        <div>
            <span>all-hello4</span><span>all-hello4</span>
        </div>
    </template>
</body>
```
```javascript
/*名称可以驼峰，可以非驼峰，但html中必须横杠*/
// 组件1
var allHello1 = Vue.extend({
    template: '<h3>all-hello1</h3>'
});

Vue.component('all-hello1', allHello1)

// 组件2
Vue.component('allHello2', Vue.extend({
    template: '<h3>all-hello2</h3>'
}));

// 组件3
Vue.component('all-hello3', {
    template: '<h3>all-hello3</h3>'
});

// 组件4
Vue.component('all-hello4', {
    template: '#template1'
});

new Vue({
    el: '#app',
})
```

### 自定义组件-私有
```javascript
new Vue({
    el: '#app',
    data: {},
    components: {
        myHello1: {
            template: '<h3>my-hello1</h3>'
        }
    }
})

// 或者使用render(webpack中使用)
new Vue({
    el: '#app',
    data: {},
    render(createdEle) { // 将组件compt放在#app处
            return createdEle(compt); // 返回一个组件对象，并替换el指定的容器
    }
})
```

### 参数
```javascript

new Vue({
    el: '#app',
    components: {
        myHello1: {
            template: `
                        <div>
                            <button @click="changeBGC" :style="{width:'100px',height:'50px'}">点击变颜色</button>
                            <h3 :class="[flag?'red':'green']">my-hello1</h3>
                        </div>
            `,
            data() { // data是一个闭包函数，防止相同组件指向同一个 data
                return {
                    flag: true,
                    msg: '私有组件啊'
                };
            },
            props: ['父组件传递过来的值'],// 定义接收父组件传值的变量
            methods: {
                changeBGC() {
                    this.flag = !this.flag;
                }
            }
        }
    }
})
```

### 组件切换`<component :is="'要展示的组件名称'"></component>`
```html
<component :is="whatComponenet"></component>
```

### 组件传值
- 父组件向子组件传数据，属性用 - ， JS 用驼峰
    + 1.通过v-bind将父组件的值绑定给子组件的变量
    ```html
    <div id="app">
        <hello v-bind:prop_msg="msg" :prop_title="title" prop_test="test"></hello>
    </div>
    ```
    + 2.子组件props数组中定义接收父组件传数据的变量
    ```javascript
    new Vue({
        el: '#app',
        data: {
            msg: '这是父组件msg',
            title: '父组件的title'
        },
        components: {
            hello: {
                template: '<h3>这是H3----父组件prop_msg:{{prop_msg}}---prop_title{{prop_title}}</h3>',
                data() {
                    return {
                        params_title: '子组件的title'
                    }
                },
                // 用来定义接收父组件传数据的变量，1.只读（最好不要修改），2.会覆盖子组件data中的属性值
                props: ['prop_msg', 'prop_title','prop_test'], // prop_test是字符串
              	props: {
                  params1: 'String',
                  params1: ['String', 'Number'],
                  params2: {
                    type: 'Number', // 设置参数类型
                    default: 2, // 设置默认值，数组和对象必须是 return 一个函数
                    required: true,
                    validation(value){
                      // 自定义验证规则
                      return true;
                    }
                  }
    						}
            }
        }
    })
    ```
    
- 父组件监听子组件的自定义方法，发射和监听时候都用 - 
    - 1.父组件调用子组件时候，通过 v-on  监听子组件发射的自定义方法
    ```html
    <div id="app">
      	// 父组件调用子组件时候 监听子组件发射的自定义方法
        <hello @son_func="parent_func"></hello>
    </div>
    ```
    - 2. 子组件的方法中通过 this.$emit() 向父组件发射方法方法
    ```javascript
    new Vue({
        el: '#app',
        data: {
            dataObj: null
        },
        methods: {
            parent_func(str, data_from_son) {
                data_from_son.id = 2;
                this.dataObj = data_from_son;
                console.log(this.dataObj);
                console.log('这是父组件监听的方法，监听到之后进行一些操作' + '---' + str);
            }
        },
        components: {
            hello: {
                template: '<button @click="click_btn">点击获取父组件方法，并向父组件传递子组件数据</button>',
                data() {
                    return {
                        son_obj: {
                            id: 1,
                            msg: '子组件的数据'
                        }
                    }
                },
                methods: {
                    click_btn() {
                        // 参数1：子组件的方法名
                        // 参数2-n：父组件传递过来的方法的参数
                      	// 子组件发射一个方法，同时发射参数
                        // 注意：名字不能使用驼峰，用 - 可以
                        this.$emit('son_func', '点击了子组件btn', this.son_obj)
                    }
                }
            }
        }
    })
    ```
    
    - 自组件直接把父组件的属性和方法传给孙组件
    
    ```javascript
    <son-component ...props></son-component>
    ```



### slot 插槽

- 基本使用

```javascript
<comp>aaa</comp>

Vue.component('comp', {
  // slot 可以有默认值，没有传就用默认值
    template: `
			<div>
				<p><p>
				<slot>default</slot>
			</div>
		`
});
```

- 具名 slot

```javascript
<comp>
  <span>instead default</span>
	<span slot="main">instead main</span>
</comp>
Vue.component('comp', {
    template: `
			<div>
				<p><p>
				<slot>default</slot>
				<slot name="main">main</slot>
			</div>
		`
});
```

### 作用域 slot（父组件调用子组件时候，使用子组件的数据）

```javascript
<comp>
  <template  v-slot="main"> // 绑定 template 对应的 slot，并赋值给 main
  	<span>main.data</span> // 就可以子组件的数据了使用了
  </template>
</comp>
Vue.component('comp', {
    template: `
			<div>
				<p><p>
				<slot>default</slot>
		// 把子组件的 list 属性绑定给 data
				<slot name="main" :data="list">main</slot> 
			</div>
		`
});
```

### 组件懒加载

```javascript
const compt = () => import('/component/Home')
```

## 常用API

### this.$emit:触发函数

`this.$emit('函数名',函数的参数...)`

### Vue.set(属性，索引，newValue)-给属性刷新监听

```javascript
Vue.set(arr, 1, 'b');
Vue.set(obj)
this.$set;
```

### Vue.delete(属性，索引，newValue)-给属性刷新监听

### Vue.nextTick( [callback, context] )延迟运行-DOM更新后执行callback

- 直接调用，2个参数（回调函数和上下文），如果没有传入参数就作为promise使用
- 在Vue生命周期的created()进行的DOM操作一定要放在this.$nextTick()的回调函数中
- 在数据变化后要执行的某个操作，而这个操作需要使用随数据改变而操作的DOM结构的时候，这个操作都应该放进this.$nextTick()的回调函数中。

```html
<div id="app">
    <div ref="msgDiv">{{msg}}</div>
    <hr>
    <div>$nextTick上面: {{msg1}}</div>
    <div>$nextTick里面: {{msg2}}</div>
    <div>$nextTick下面: {{msg3}}</div>
    <hr>
    <button @click="changeMsg"> 按钮</button>
</div>
```

```javascript
new Vue({
    el: '#app',
    data: {
        msg: 'hello-vue',
        msg1: '',
        msg2: '',
        msg3: ''
    },
    methods: {
        changeMsg() {
            console.log(1);
            this.msg = "hello-world";
            this.msg1 = this.$refs.msgDiv.innerHTML; // 这时候DOM还没更新

            this.$nextTick(() => {
                // DOM更新后才会执行的代码
                this.msg2 = this.$refs.msgDiv.innerHTML;
            });

            this.msg3 = this.$refs.msgDiv.innerHTML;
        }
    }
})
```

### this.$route:获取路由参数对象

### this.$router:获取路由导航对象

### this.$refs:获取元素，组件（父组件访问子组件）

- 不能用子组件访问父组件
- 1.元素/组件添加 ref 属性来标识

```html
<div id="app">
    <button @click="click_getDOM" ref="getDOM">点击获取元素</button>
    <br>
    <button @click="click_getCompt">点击获取组件</button><br>
    <hello ref="compt"></hello>
</div>
```

- 2.父组件方法中调用this.$refs(对象)获取用 ref 标识的元素/组件

```javascript
var hello = {
    template: '<h3>hello组件</h3>',
    data() {
        return {
            msg: '子组件的数据'
        }
    },
    methods: {
        func_son() {
            console.log('调用了子组件方法');
        }
    }
};
new Vue({
    el: '#app',
    data: {},
    methods: {
        click_getDOM() {
            console.log(this.$refs.getDOM);
        },
        click_getCompt() {
            console.log(this.$refs.compt);
            console.log(this.$refs.compt.msg);
            this.$refs.compt.func_son();
        }
    },
    components: {
        hello
    }
})
```

### this.$children-父组件访问子组件

- 不建议使用，扩展性不强，需要数组 index

### this.$parent-子组件访问父组件

- 不建议使用，组件不够解偶

### this.$root-子组件访问根组件

- 一般不用，root 中没什么数据

- # axios

# vue-router

- > https://router.vuejs.org/zh/
- 后端路由：对于普通的网站，所有超链接都是URL地址，所有URL地址都对应服务器上的资源。
- 前端路由：对于单页面应用程序，主要通过URL的hash（哈奇/锚点）来实现不同页面的切换，hash有一个特点，HTTP请求不会包含hash相关的内容

## 使用
- 1.在vue后面引入vue-router
- 2.引入后window下会有一个VueRouter的路由构造函数，且url最后上会自动加上#/
- 3.配置路由
```javascript
var obj_router = new VueRouter({
  	mode: 'history', // 默认是锚点，可以修改
    linkActiveClass: 'routerActive', // active 时候的类名
    routers: [
        path: '/login',
        component: {}// 组件对象        
    ]
})
```
- 4.将配置好的路由与Vue的实例vm进行绑定
```javascript
new Vue({
    el: '#app',
    router : obj_router
})
```
- 5.html中使用`<router-view></router-view>`占位符，用来标识路由组件展示的地方
- 6.html中来跳转路由
```html
// to: 指定路径
// tag: 指定 router-link 标签渲染成的最终标签
// replace: 会让 history 使用 replace, 不能 back 和 forword
<a href="#/home">主页/不会添加active类名</a>
<router-link to="/login">登录/默认转换为a标签</router-link>
<router-link to="/register" tag="span" replace>注册/使用tag属性处理标签</router-link>
```
- 7.被激活的路由，会给`<router-link></router-link>`元素身上加上router-link-active类名，可以通过linkActiveClass属性进行修改

- 8.js 跳转路由
  
  - this.router.push() // 可以 back 和 forward，有history
  
  ```
  this.router.push({
      name:'要跳转的路径名称', // router里定义的name
      params: { // 要传递的值
      '传递参数的名字': '传递参数的值'
  })
  this.router.push({
      name:'要跳转的路径名称', // router里定义的name
      query: { // 要传递的值
      '传递参数的名字': '传递参数的值'
  })
  ```
  
  
  
  - this.router.replace()  // 没有 history
  - this.$router.go(n)
  
- 9.路由动画，给路由加上`<transition></transition>`标签

## hash路由和history路由

1. Hash 路由

   - 优点
     1. 兼容性好
     2. 页面流畅，不用刷新整个页面

   - 缺点
     1. 地址栏不美观，有#
     2. 不利于SEO

2. history

   1. 支持pushState和replaceState API允许开发者直接更改前端路由，而不需要重新发起请求

## 配置路由
```javascript
var obj_router = new VueRouter({
    routes: [ // 这里是routes不是routers
    {
        path: '/',
        redirect: '/account', // 让根路径跳转至login路由
        name: 'root' ,// 指定当前路由的别名
        meta: {keepAlive:true} // 
    },{
        path: '/account', // path后面的路由名称以’/‘开头，表示从根路径开始
        component: {}, // 组件对象 
        children: [{
            path: 'login',// 不带’/‘，带了表示从根路由开始
            component: {},
        }],
        components: { // 路由引入多个组件时，通过name区分，默认为default
            default,
            left,
        }
    },{
            path: '*', // 遇到未配置的路由时--必须放在最下面
            redirect: '/home'
    }],
    linkActiveClass:'类名', // 修改激活路由的元素的默认active类名
}）
```

## 路由传参
- 使用查询字符串，调用this.$route.query
- 使用占位符，调用this.$route.params
```html
<router-link to="/login?id=12" tag="a">登录组件</router-link>
<router-link to="/register/13/tom">注册组件</router-link>
<router-link to="/user/13">用户组件</router-link>
<router-view></router-view>
```
```javascript
var router = new VueRouter({
    routes: [{
        path: '/login',
        component: {
            template: '<h3>登录组件---{{$route.query.id}}</h3>',
            data() {
                return {
                    msg: '啊啊啊啊'
                }
            },
            created() {
                console.log('-----登录组件-----');
                console.log(this.$route);
            }
        }
    }, {
        path: '/register/:id/:name', // :id占位符
        component: {
            template: '<h3>注册组件---id:{{$route.params.id}}---name：{{$route.params.name}}</h3>',
            created() {
                console.log('-----注册组件-----');
                console.log(this.$route);
            }
        }
    }, {
        path: '/user/:id/:name?', // :id占位符，? 表示可传可不传
        component: {
            template: '<h3>用户组件---id:{{$route.params.id}}---name：{{$route.params.name}}</h3>',
            created() {
                console.log('-----用户组件-----');
                console.log(this.$route);
            }
        }
    }]
});
new Vue({
    el: '#app',
    router
})
```

## 子路由
```html
<div id="app">
    <router-link to="/account">account</router-link>
    <router-view></router-view>
</div>
```
```javascript
var compt_account = {
    template: `
        <div>
            <h3>账户组件</h3>
            <router-link to="/account/login">登录</router-link>
            <router-link to="/account/register">注册</router-link>
            <router-view></router-view>
        </div>
    `
};

var compt_login = {
    template: '<h3>登录组件</h3>'
};

var compt_register = {
    template: '<h3>注册组件</h3>'
};

var router = new VueRouter({
    routes: [{
        path: '/account',
        component: compt_account,
        children: [{
            path: 'login', // 这里不能加/，否则从根目录开始
            component: compt_login
        }, {
            path: 'register',
            component: compt_register
        }]
    }]
});
new Vue({
    el: '#app',
    router
})
```

## 路由引入多个组件
```html
<div id="app">
    <router-view></router-view>
    <router-view name="box_left"></router-view>
    <router-view name="box_right"></router-view>
</div>
```
```javascript
var box_header = {
    template: `<h1 :style="{height:'200px',background:'pink'}">这是头部</h1>`
};

var box_left = {
    template: `<aside :class="['left']">这是left</aside>`
};

var box_right = {
    template: `<main :class="['right']">这是right</main>`
};

var router = new VueRouter({
    routes: [{
        path: '/',
        components: {
            default: box_header,
            box_left,
            box_right
        }
    }]
});

new Vue({
    el: '#app',
    router
})
```

## 组件热缓存（路由用的多）<keep-alive></keep-alive>
+ 如果需要频繁切换路由，这个时候就可以考虑用keep-alive了，来达到避免数据的重复请求的目的
+ 并保留上一个行为。
+ 本质是不让组件进入 生命周期的 destory 阶段
+ 也可以让普通组件不销毁

```vue
// 该组件的哪些子组件被缓存，两个组件名之间不能加空格
<keep-alive include="comptName,User" exclude="comptName">
    <router-view> </router-view>
</keep-alive>
```

- keep-alive 的组件，会有2个额外的生命周期
  - actived
  - deactived 

## 组件懒加载（路由懒加载用的多）

- 如果一起打包，项目会非常大，但是有的页面用不到，后者不是立即用到，所以需要懒加载

```javascript
routers: [
  {
    path: '/login',
    component: ()=>{
    	import('./component/Login'); // 用回调进行路由组件懒加载
    }
  }       
]
```

## `$routetr` && `$route`

- $router: new VueRouter 配置好的路由器
- $route: 当前活跃的路由信息

## 导航守卫

### 全局前置守卫router.beforeEach
```javascript
const router = new VueRouter({ ... })

router.beforeEach((to, from, next) => {
    // to: Route: 即将要进入的目标 路由对象
    // from: Route: 当前导航正要离开的路由
    // next: Function: 确保要调用 next 方法，否则钩子就不会被 resolved。
        // next(): 进行管道中的下一个钩子。如果全部钩子执行完了，则导航的状态就是 confirmed (确认的)。 

        // next(false): 中断当前的导航。如果浏览器的 URL 改变了 (可能是用户手动或者浏览器后退按钮)，那么 URL 地址会重置到 from 路由对应的地址。

        // next('/') 或者 next({ path: '/' }): 跳转到一个不同的地址。当前的导航被中断，然后进行一个新的导航。你可以向 next 传递任意位置对象，且允许设置诸如 replace: true、name: 'home' 之类的选项以及任何用在 router-link 的 to prop 或 router.push 中的选项。

        // next(error): (2.4.0+) 如果传入 next 的参数是一个 Error 实例，则导航会被终止且该错误会被传递给 router.onError() 注册过的回调
})
```

### 全局后置钩子router.afterEach
```javascript
router.afterEach((to, from) => {
  // ...
})
```

### 路由独享的守卫
```javascript
const router = new VueRouter({
  routes: [
    {
      path: '/foo',
      component: Foo,
      beforeEnter: (to, from, next) => {
        // ...
      }
    }
  ]
})
```

### 组件内的守卫
- 离开守卫通常用来禁止用户在还未保存修改前突然离开。该导航可以通过 next(false) 来取消。
```javascript
beforeRouteLeave (to, from , next) {
  const answer = window.confirm('Do you really want to leave? you have unsaved changes!')
  if (answer) {
    next()
  } else {
    next(false)
  }
}
```

```javascript
const Foo = {
  template: `...`,
  beforeRouteEnter (to, from, next) {
    // 在渲染该组件的对应路由被 confirm 前调用
    // 不！能！获取组件实例 `this`
    // 因为当守卫执行前，组件实例还没被创建
  },
  beforeRouteUpdate (to, from, next) {
    // 在当前路由改变，但是该组件被复用时调用
    // 举例来说，对于一个带有动态参数的路径 /foo/:id，在 /foo/1 和 /foo/2 之间跳转的时候，
    // 由于会渲染同样的 Foo 组件，因此组件实例会被复用。而这个钩子就会在这个情况下被调用。
    // 可以访问组件实例 `this`
  },
  beforeRouteLeave (to, from, next) {
    // 导航离开该组件的对应路由时调用
    // 可以访问组件实例 `this`
  }
}
```

### 完整的导航解析流程
- 1.导航被触发。
- 2.在失活的组件里调用离开守卫 beforeRouteLeave。
- 3.调用全局的 beforeEach 守卫。
- 4.在重用的组件里调用 beforeRouteUpdate 守卫 (2.2+)。
- 5.在路由配置里调用 beforeEnter。
- 6.解析异步路由组件。
- 7.在被激活的组件里调用 beforeRouteEnter。
- 8.调用全局的 beforeResolve 守卫 (2.5+)。
- 9.导航被确认。
- 10.调用全局的 afterEach 钩子。
- 11.触发 DOM 更新。
- 12.用创建好的实例调用 beforeRouteEnter 守卫中传给 next 的回调函数。

# vuex：状态管理
- 让整个应用程序共享某些状态/数据，方便整个应用中的任何组件读取/修改这些状态/数据
- 只有组件之间共享的数据才需要存在vuex中

## 使用
- 1.在vue后面引入vuex
- 2.安装vuex
`Vue.use(Vuex)`
- 3.配置vuex
```javascript
var obj_router = new Vuex.store({
    state: { // 定义放入store中的属性，形成单一状态树
        count: 0
    },
    // 定义放入store的方法
    // 每个方法第一个参数必须是state
    // 推荐通过调用方法的方式改变state中的属性
    mutations: {
        increment (state, payload) {
            state.count++
        },
    },
    // 只能获取state中的属性，不能改变，改变在mutations中
    // 实时监控所绑定的值
    getters: {
        doneTodos: state => {
            return state.todos.filter(todo => todo.done)
        }
    },
    // Action 提交的是 mutation，而不是直接变更状态
    // Action 可以包含任意异步操作
    // Action 函数接受一个与 store(或者module) 实例具有相同方法和属性的 context 对象，因此你可以调用 context.commit 提交一个 mutation，或者通过 context.state 和 context.getters 来获取 state 和 getters
  // 如果有 module 的话，context 就是当前 module 的上下文
    actions: {
        increment (context, payload) {
        	context.commit('increment')
        },
      // 可以返回一个 promise(或者直接 async/await) 在dispatch时候链式调用
      // $store.dispatch('decrement').then(fn)
      	decrement(context, payload){
         	return new Promise((resolve, reject)=>{
            // 异步操作
            resolve(111)
          });
        }
    },
    // Vuex 允许我们将 store 分割成模块（module）。
    // 每个模块拥有自己的 state、mutation、action、getter、甚至是嵌套子模块
    modules: {
      a: moduleA,
      b: moduleB
    }
})
```
- 4.将配置好的store与Vue的实例vm进行绑定
```javascript
new Vue({
    el: '#app',
    store
})
```
- 5.使用
    + state中的属性：$store.state.count
    + mutation中的方法：$store.commit('increment',params)--最多只能传2个参数
    
    ```javascript
    $store.commit({
    	type: 'increment',
    	payload: {}
    })
    ```
    
    
    
    + getters中的属性：$store.getters.doneTodos
    + action中的方法：$store.dispatch('increment',params)--最多只能传2个参数

## 项目中使用

- mutations 和 action 中的 type，可以抽为一个 types 常量文件
- 使用 map 辅助函数

```javascript
map
```

# webpack 4.x

## node工具
- nvm： node version manager node版本管理
- npm： node package manager node包管理
- nrm： node registry manager node注册地址管理，提供大陆境内镜像地址
- cnpm： 大陆装包工具--类似于npm

## 介绍
- 1.为什么使用管理工具
    + 请求静态文件多，网页加载慢，因为发起很多二次请求。
        - JS、CSS压缩合并
        - 小图片转精灵图，或base64编码（src指向base64编码后的字符串）和html第一次就一起请求过来
    + 包/文件依赖关系复杂
- 2.webpack是基于node开发的包管理工具
- 3.gulp和webpack
    + gulp：基于task任务，每个小任务都要建一个task，项目太大的话task太多，不适用于特大项目
    + webpack： 基于项目/模块

## 安装
- npm install webpack -g
- npm install webpack --save-dev

## main.js 使用ES6语法
```javascript
// 使用ES6语法，浏览器兼容性不高可能报错，需要webpack打包处理
// 不加./表示在当前目录下的node_modules文件夹下面找
import $ from 'jquery';
// webpack默认只能处理JS文件，需要处理非JS文件需要loader
import './css/index.css';

$(function() {
    $('li:odd').css({ background: 'pink' });
    $('li:even').css({
        background: () => {
            return# +'lightblue';
        }
    });
});
```
## webpack.config.js webpack配置文件
```javascript
// webpack的配置文件，基于node开发的，所以是node 语法
// 依赖path模块
const path = require('path');

// 向外暴露一个配置对象
module.exports = {
    entry: path.join(__dirname, './main.js'), // 入口文件地址
    output: { // 输入文件配置
        path: path.join(__dirname, './dist'), // 输入文件目录
        filename: 'bundle.js' // 输出文件名
    },
    devServer: { // 设置webpack-dev-server（推荐在package中使用，这种比较麻烦）
        // open：默认打开浏览器
        // port：端口
        // contentBase：根目录
        // hot：热重载（不重新生成新文件，只局部更新，浏览器无刷新重载CSS，对JS无效）
        // host：指定项目运行的服务器地址
        open: true,
        port: 3000,
        contentBase: 'src',
        // 这里启用热重载还需要引入webpack模块
        // 1.hot:true
        // 2.const webpack = require('webpack'),
        // 3.配置插件 new webpack.HotModuleReplacementPlugin()
        hot: true,
        host: '192.168.1.1',
    },
    plugins: [ //配置插件--需要先require('插件')
        new webpack.HotModuleReplacementPlugin(),// 配置热重载第三步
        new htmlWebpackPlugin({ // 根据指定模板页面生成内存镜像
            template: path.join(__dirname,'index.html'),
            filename: '生成文件名称'
        }),
        // 4.x需要配置 const VueLoaderPlugin = require('vue-loader/lib/plugin');
        new VueLoaderPlugin(), 
    ],
    module: { // 配置第三方文件/模块
        rules: [ // 匹配规则，多个loader时 从右到左依次调用
            {test: /\.css$/ , use: ['style-loader','css-loader']},
            {test: /\.less$/ , use: ['style-loader','css-loader','less-loader']},
            {test: /\.scss$/ , use: ['style-loader','css-loader','sass-loader']},
            // use后面可以传参
            // limit：转换图片编码的最大大小
            // name：控制文件名【name】-保持原名，[ext]-保持原后缀[hash:8]取32位哈希的前8位
            {test: /\.(jpg|png|gif|jpeg|bmp)$/ , use: 'url-loader?limit=7624&name=[name].[ext]'},
            {test: /\.(ttf|eot|svg|woff|woff2)$/ , use: 'url-loader'},
            // babel-exclude：排除（不编译）的文件/目录
            {test:/\.js$/ , use: 'babel-loader' , exclude: /node_modules/}
        ]
    }
}
```

## package.json
```json
"devDenpendencies": {
  	"webpack": "13.0.0", // 安装 13.0.0
  	"webpack": "13.0.0", // 安装 13.x.0
  	"webpack": "13.0.0", // 安装 13.x.x
}
"script": {// 可以定义npm运行的脚本，控制台直接npm build即可
    // 设置webpack-dev-server（推荐使用这种方式，不推荐在webpack.config.js中配置，比较麻烦）
    // --open：默认打开浏览器
    // --port：端口
    // --contentBase：根目录
    // --hot：热重载（不重新生成新文件，只局部更新，浏览器无刷新重载CSS，对JS无效）
    // host：指定项目运行的服务器地址
    "build": "webpack-dev-server --open --port 3000 --contentBase src --host 192.168.1.1"
}
```

## webpack-dev-server 本地服务器
- 要求必须在项目本地安装webpack
- 内存中生成bundle.js文件

## html-webpack-plugin 插件
- 根据指定模板页面生成内存镜像
- 默认引入webpack-dev-server生成并存放在内存中的buddle.js文件
- 在plugins中设置

## loader 加载器
- css：style-loader css-loader
- less：less-loader（内部依赖less）
- sass/scss（新版后缀叫scss）: sass-loader(内部依赖node-sass，node-sass用npm一般装不下来，cnpm可以)
- url：url-loader(内部依赖 file-loader)：匹配文件和字体等url
    + 默认将图片转成base64字符串，不管大图小图
    + 大图不需要转不然字符串太长了

- babel：ES6//ES7高级语法转化为低级语法(babel-core@6版本，新版@babel/core不适用)
    + 转化工具（根据转化关系进行转化）：babel-core babel-loader babel-plugin-transform-runtime
    + 语法（高级语法到低级语法的转化关系）：babel-preset-env（之前是babel-preset-env，babel-preset-env包含所有es的语法） babel-preset-stage-0
    + 1.module的rules规则中添加babel-loader的匹配规则
    + 2.项目根目录中创建.babelrc文件
    ```json
    {
        "presets": ["env" , "stage-0"],
        "plugins": ["transform-runtime"]
    }
    ```

- vue: vue-loader(内部依赖 vue-template-compiler，webpack3.x版本)
    + 4.x需要在plugins中配置 const VueLoaderPlugin = require('vue-loader/lib/plugin');

## webpack-merge

- 将 webpack 按照 dev, build, product 配置的区别进行抽离
- 抽离出 base- config 和特殊功能模块 dev-config, build-config, product-config
- 通过 webpack-merge 合并 base-config 和特殊功能模块

```javascript
// npm install webpack-merge --save-dev
const webpackMerge = require('webpack-merge');
const baseConfig = require('./base.config.js');
module.export = webpackMerge(baseConfig, {
	// 特殊功能模块
})
```



# webpack+VUE
### 基本配置
- npm install vue -S

- main.js 导入 `import Vue from 'vue'` 此时导入的是一个runtime的阉割版

- 要使用传统网页方式开发需要引入vue.js
    + 修改导入路径
    + 修改vue/package.json中main对应的文件名
    + webpack.config.js中的resolve属性配置默认值
    ```javascript
    resolve: {// 设置vue导入时候包的路径,vue.esm.js 包含 compiler
      	extension: ['.js','.vue','.css'], // 省略扩展名
        alias: {
            	// 'vue$': 'vue/dist/vue.esm.js' // 使用哪个vueJS 版本
          		'vue$': 'vue/dist/vue.runtime.js',
              // import 可以直接拿到
              // 其他引入方式需要 ～@
              '@': resolve('src'); // 别名
        }
    }
    ```

### 组件
- 安装 vue-loader(内部依赖 vue-template-compiler)
- module的roles中配置vue-loader
- html组件（后缀.vue）
```html
<template>
    <h1>这是login组件{{123}}</h1>
</template>
<script>
    export default {
        data(){
            return {
                msg: 123
            }
        },
        methods: {}
    }
</script>
<style scoped lang="sass">
<!-- scoped表示独立作用域，style只作用在当前组件中 -->
<!-- lang表示使用哪种语言编写CSS -->
</style>
```
- 导入组件，并渲染
```javascript
import login from './login.vue'

new Vue({
    el: '#app',
    data: {},
    render(createdEle) { // 将组件compt放在#app处
            return createdEle(compt); // 返回一个组件对象，并替换el指定的容器
    },
    // 或者写成--箭头函数简化而来
    render: c=>c(login)
})
```

### 路由
- 安装 vue-router(内部依赖 vue-template-compiler) 
- main.js中导入vue-router
```javascript
import VueRouter from 'vue-router;
Vue.use(VueRouter); // 手动安装VueRouter
```
```javascript
var obj_router = new VueRouter({
    routes: [{
        path: '/',
        redirect: '/login'
    }, {
        path: '/login',
        component: {
            template: '<h3>登录组件</h3>'
        }
    },
    linkActiveClass: 'my-active'
});
new Vue({
    el: '#app',
    data: {},
    router: obj_router
})
```



# vue3

## vue3和vue2的区别

- Vue3 引入的是 createApp 工厂函数，Vue2 引入 Vue 构造函数
- Vue3 的 app 类似于 Vue2 中的 VM，但更轻
- Vue3 是组合式 companie API，Vue2 是选项是 option API
  - Vue2的数据和方法都是拆开放，Vue3处理统一业务的数据和方法放在一起


## 基本使用

```javascript
import { createApp } from 'vue'
import { createPinia } from 'pinia'
import {ref, reactive, computed, watch, watchEffect, defineProps } from 'vue';
export default{
    name: 'Demo',
  props: ['name', ' age'], // 接收父组件传递的属性
  emits: ['handelClick'], // 接收父组件传递的方法，通过  context.emit 触发
  data: {
    return{
   		name: 1
  	}
  },
  // props:
  // context:
  setup(props, context){
    const name = ref('James');
    const person = reactive({
      name: 'Jocos',
      age: 18
    })
    const props = defineProps({
      minConsecutiveNumber: {
        type: String,
        required: true
      },
      teamsData: {
        type: Object,
        required: true
      },
      unFocusTeams: {
        type: String,
        required: true
      },
    });
		// computed
    const fullName = computed(()=>{
      return firstName + '-' +lastName;
    });
    // watch: 
    	// 问题1:监视 reflect 引用类型 newValue 和 oldValue 一样
    	// 问题2:监视 reflect 引用类型， deep 无效因为随时都开启deep，但监视引用类型的引用属性，deep 生效
  		// 问题3:要监视未被 ref 的值需要用方法
  		// 问题4:要监视多个未被 ref 的值需要[fn,fn,fn]
  		// 问题5:监视 const age = ref(1) 的age不能 x.value，因为监视的是结构不是值
  		// 问题6:监视 const person = ref({}) 时，需要person.value，或者开启deep
    watch(name, ()={});
    watch([name, age], (newValue, oldValue)=>{}, {
      immediate: true,
      deep: true
    });
    watch(()=>person.name, (newValue, oldValue)=>{});
    // watchEffect: 回调中所用到的变量都会触发回调，类似 computed
    watchEffect(()=>{
      const a = name;
    });
    function fn(){
      console.log(name.name);
      console.log(person.name)
    }
  // setup 返回一个对象或者返回一个渲染函数
    return {
      name,
      person,
      fn
    }
  // 返回渲染函数
  import { h } from 'vue'
  return ()=>{
    return h('h1', '111');
  }
  }
}
```



## setup: setup 只在 beforeCreate 前执行一次，因此没有 this

```javascript
<script setup>
	import { RouterLink, RouterView } from 'vue-router'
	import HelloWorld from './components/HelloWorld.vue'
</script>
<script>
	import { RouterLink, RouterView } from 'vue-router'
	import HelloWorld from './components/HelloWorld.vue'
	export default{
    setup(){
      数据
      方法
      计算属性
      监视
    }
  }
</script>
```

## script: setup

- 更少的样板内容，更简洁的代码。
- 更好的运行时性能 (其模板会被编译成同一作用域内的渲染函数，避免了渲染上下文代理对象)。
- 每次组件实例被创建的时候执行
- 不需要写setup方法，会直接暴露出去
- import 导入的也会暴露
- 但是响应式的数据需要用 ref 创建

```javascript
<script setup>
import { capitalize } from './helpers'
// 不需要引入 import { defineProps, defineEmits } from './helpers'
let person = 'aa'
// defineProps defineEmits
const props = defineProps({
  foo: String
})

const emit = defineEmits(['change', 'delete'])
</script>

<template>
  <p>{{person}}</p>
  <div>{{ capitalize('hello') }}</div>
</template>
```



## ref&&reflect

```javascript
// 将setup中的普通数据变成响应式
// ref 底层调用 reactive
let name = ref('张三');

// 响应式修改
name.value = '李四'

// 对象类型用 poxy 进行接管
let obj = ref({}) // 底层调用了 reactive
let obj = reactive({})
```



## 双向数据绑定和脏值检查

### vue2中

- Obj 使用 Object.defineProperty实现
- 数组是对数组的方法进行二次封装，一边调用数组原生方法，一边更新dom
- 新增和删除属性不能更新dom，直接通过数组下标修改数组不更新dom
- 添加 this.$set() 或者 Vue.set() 解决
- 删除 this.$delete() 或者 Vue.delete() 解决
- 数组更改下标改成使用方法

### 基本数据类型用 ref 函数，底层使用 defineProperty的getter和setter进行数据劫持（vue4.0废弃）

- 基本类型 ref 处理完的变量变成ref对象
- JS 都用通过 x.value
- 模版中不用x.value

### 引用类型用 reactive 函数，底层使用 ES6 proxy 进行数据劫持

- 可以通过索引修改数组，vue2 不行（需要调用splice方法）
- 可以新增/删除对象属性，vue2不行（需要 this.$set()/delete）

### 原理 `window.proxy` 的 getter && setter

- 用Proxy监测数据增删改查
- 然后用 Reflect 对象操作 target，ECMA 组织正在吧 Object 上的方法移植给 Reflect，是多线程的
  + Reflect.get() 
  + Reflect.set()
  + Reflect.deleteProperty()
  + 返回值为 true/false
- Reflect 返回一个 boolean，用于捕获 error，不需要 try...catch 了

```javascript
const person = {
	name: 'James',
	age: 18
};
const p = new Proxy(Person, {
	get(target, propName){
    return Reflect.get(target, propName);
  },
	set(target, propName, newValue){ // 修改和新增都掉用
    Reflect.set(target, propName, newValue);
  },
  deleteProperty(target, propName){
    return Reflect.deleteProperty(target, propName);
  }
});
p.sex = 'male';
```



## v-model

1. 用在表单组件

```vue
<input v-model="input"/>
// 相当于
<input :value="inputValue" @input="(e)=>{inputValue = e.target.value}"/>
```



2. 用在子组件

```vue
<son v-model="name"></son> // 默认绑定 modelValue
<son v-model:name="name"></son>
// 相当于
<son :name="name" @update:name="emit('name', ...value)"></son>
```

3. 修饰符

```vue
// .lazy .number .trim
// 自定义
<son v-model.myModify="input"/>

// son 组件中
const [model, modifiers] = defineModel({
// 首字母大写
  set(value) {
    if (modifiers.myModify) {
      return value.charAt(0).toUpperCase() + value.slice(1)
    }
    return value
  }
})

```



## slot

- 只能用 `v-shot:left`

## compaineAPI 对比 optionAPI 的优势

- 按照功能分离代码，而不是类型，代码更有序更优雅

## computed

```javascript
// computed
    const fullName = computed(()=>{
      return firstName + '-' +lastName;
    });
```



## watch

```javascript
// watch: 
    	// 问题1:监视 reflect 引用类型 newValue 和 oldValue 一样
    	// 问题2:监视 reflect 引用类型， deep 无效因为随时都开启deep，但监视引用类型的引用属性，deep 生效
  		// 问题3:要监视未被 ref 的值需要用方法
  		// 问题4:要监视多个未被 ref 的值需要[fn,fn,fn]
  		// 问题5:监视 const age = ref(1) 的age不能 x.value，因为监视的是结构不是值
  		// 问题6:监视 const person = ref({}) 时，需要person.value，或者开启deep
    watch(name, ()={});
    watch([name, age], (newValue, oldValue, onCleanup)=>{
        // onCleanup 用来清除每次调用的副作用
        onCleanup()
    }, {
      immediate: true,
      deep: true,
      flush: 'post'|'sync' // post 时候相当于nextTick
    });
   const unwatch = watch(()=>person.name, (newValue, oldValue)=>{});
		unwatch()
```



## watchEffect-类似 computed

```javascript
// watchEffect: 回调中所用到的变量都会触发回调，类似 computed
	// 第一次也会执行，相当于 immediate: true,
 	// watchEffect(fn, gn)
	// fn: 运行副作用的函数
	// gn:调整副作用的刷新时机或调试副作用的依赖。
    const stop = watchEffect((onCleanup)=>{
      const a = name;
        // onCleanup 用来清除每次调用的副作用
        onCleanup()
    }, {
      immediate: true,
      deep: true,
      flush: 'post'|'sync'
    });
// stop 关闭监听
stop();
```



## 生命周期变化

1. setup( props, context ){}最早执行，并且setup没有this
   1. context 的参数
      1. attr
      2. emit
      3. slot
2. compile component
3. beforeUnmount/unmounted
4. 钩子函数可以写在 setup 中，需要引入，且 beforeCreated && created 就是 setup

## hooks: 是一个函数，类似于mixin

- 包含各种生命周期和处理方法

- 一边定义成 use...

```javascript
import {reflect, onMounted} from 'vue'
export default ()=>{
  let point = reflect([1,1]);
  const handle = ()=>{
    point[0]++
  }
  onMounted(){
    point[1] = point[1]*2
  }
  return point
}
```



##  toRef(obj, props)

- `toRef(person, 'age')`
- 基本数据类型转成 ref，并和原对象保持通话关系

## toRefs(obj)

- `toRefs(person)`

# vue3 other compaine api

## h()---生成一个VNode

## render(VNode, 渲染到哪去)

## shallowReactive && shallowRef-浅层响应式

- 只把对象第一层进行响应式

## readonly && shallowReadonly

- 设置响应式数据为 readonly，不能修改，会报 warn
- `person = readonly(person)`

## toRaw() 响应式数据转成原始数据

## markRaw() 标记响应式数据为原始数据

## custom ref

- 创建一个自定义ref，并对其依赖项跟踪和更新处罚进行显示控制

```javascript
import {customRef} from 'vue';
// 实现防抖
function myRef(value, delay){
  let timer,
  return customRef((track, trigger)=>{
    return {
      get(){
        track(); // 追踪监视器
        return value;
      },
      set(newValue){
        clearTimeout(timer);
        timer = setTimeout(()=>{
          value = newValue;
          trigger(); // 触发模版重新渲染
        }, delay)
      }
    }
  })
}

let helloInput = myRef('hello', 500);
```

## provide && inject 祖孙传递数据

```javascript
// 父组件中
setup(){
  let person = reactive({})
  provide('person', person)
}
// 孙组件中
setup(){
  let person = inject('person')
}
```



## 判断响应式数据

- isRef
- isReactive
- isReadonly
- isProxy

## Fragment 组件：

- Vue3中不是必须要根组件，是系统自动把没有根标签的都放入Fragment标签中

## Teleport 组件：在组件内部定义，将组件的内容传给外层

- 可以直接写元素名，类名，ID

```javascript
<teleport to="body">
	<my-alert />
<teleport>
```

## defineAsyncComponent 引入异步组件

```javascript
import {defineAsyncComponent} from 'vue';
defineAsyncComponent(()=> import( './Hello.vue' ) )
```

## Suspense 组件：解决异步组件某些问题

```javascript
<Suspense>
  // 真正要展示的组件
	<templete v-slot="default">
		<my-hello />
	</templete>
	// 组件加载中显示的组件
	<templete v-slot="fallback">
		<Hello />
	</templete>
</Suspense>
```

- setup 函数可以返回一个 promise 了（估计延时加载，然后做优化）

```javascript
async setup(){
	let p = new Promise();
	return await p;
}
```



## 全局 API 转移

- Vue3 的 API 放在 app 上，Vue2 在VM上
- data 始终被生命为一个函数，防止组件复用时候形成数据干扰
- 过渡类名更改
- 移除过滤器
- 在emit中没声明就是原生事件，自定义就是自定义事件

# vite: 新构建工具

## 优势

- 开发环境中，无需打包，可快速冷启动

- 热重载

- 真正按需编译，不用等待真个编译完成

- webpack需要全部打包编译

## 使用

### 跨域

```javascript
proxy: {
  '/api': {
    // 现在自己的api路径前面加上 '/api'
    target: 'www.baidu.com', // 要替换的服务端地址
    changeOrigin: true, // 允许跨域
    rewrite: api=path.replace('') // 将自己api中所有的 '/api'替换为空
  }
}
```



# Pinia比Vuex更轻量

## 对比

- 抛弃了motation，直接使用 action + patch
- 拥抱typescript
- 不再有**可命名的模块**

## 使用

- 定义

```javascript
import { defineStore } from 'pinia'

export const useTodos = defineStore('todos', {
  state: () => ({
    /** @type {{ text: string, id: number, isFinished: boolean }[]} */
    todos: [],
    /** @type {'all' | 'finished' | 'unfinished'} */
    filter: 'all',
    // 类型将自动推断为 number
    nextId: 0,
  }),
  getters: {
    finishedTodos(state) {
      // 自动补全！ ✨
      return state.todos.filter((todo) => todo.isFinished)
    },
    unfinishedTodos(state) {
      return state.todos.filter((todo) => !todo.isFinished)
    },
    /**
     * @returns {{ text: string, id: number, isFinished: boolean }[]}
     */
    filteredTodos(state) {
      if (this.filter === 'finished') {
        // 调用其他带有自动补全的 getters ✨
        return this.finishedTodos
      } else if (this.filter === 'unfinished') {
        return this.unfinishedTodos
      }
      return this.todos
    },
  },
  actions: {
    // 接受任何数量的参数，返回一个 Promise 或不返回
    addTodo(text) {
      // 你可以直接变更该状态
      this.todos.push({ text, id: this.nextId++, isFinished: false })
    },
  },
})
```



```javascript
// 两种定义方法
export const useCounterStore = defineStore('counter', {
  state: () => ({ count: 0 }),
  getters: {
    double: (state) => state.count * 2,
  },
  actions: {
    increment() {
      this.count++
    },
  },
})

// 或者
// 	ref() 就是 state 属性
// 	computed() 就是 getters
// 	function() 就是 actions
export const useCounterStore = defineStore('counter', () => {
  const count = ref(0)
  function increment() {
    count.value++
  }

  return { count, increment }
})
```



- 使用

```javascript
<script setup>
import { useCounterStore } from '@/stores/counter'
// 可以在组件中的任意位置访问 `store` 变量 ✨
const store = useCounterStore()
</script>
```



# TypeScript

```typescript
// 定义变量
let n: number;
let num: number = 1;
let str: string = '';

let arr: Array = [];
let brr: string[]; let brr: Array<string>// 字符串数组
// 元组:固定长度的数组
let arr1: [number, number] = [1,2]

let obj: object = {age: 10}
let obj: {name: string, age?: number, [propName: string]: any}

let a: any; let d;(默认为any)-----不建议使用,用它计算会关闭其他变量的类型检测
let a: unknow-----用它计算不会关闭其他变量的类型检测

// 枚举enum：处理数据需要key--value
enum Gender{
  male: 0, female:1
}
let g:Gender = Gender.male;

// 运算符
let gender: 'male'|'female';
let a: number | string;

// 定义函数变量
function(a: number, b: string): number｜void{
  
}
let fn(a:number) => number; 
// 类型断言
let e:unknow;
e = '1';
let a = e as string-----告诉编辑器 e 就是字符串

// 抽象类--自定义obj类型，不能重复定义
type type1{

}

// 接口--自定义的obj类型，重复定义取合集
interface interface1{
  
}

// 泛型：函数返回值不确定
function fn<T>(a: T): T{} // <T>是定义，具体什么类型在执行时候才知道
fn(10) // 默认泛型T=number
fn<string>('') // 手动指定泛型T=string

```



# 其他

## node中的模块化
- 导出模块
```javascript
module.exports obj1;
exports obj2;
return obj3;
```
- 导入模块
```javascript
var obj = require('./index.js');
```

## ES6中的模块化
- 导出模块
```javascript
// 每个模块只能有一个export default，导入模块可以用任意变量接收
export default obj1; 
// 一个模块可以有多个export，导入模块只能用这里暴露的变量接收，但可以收到后修改
export obj2; 
export obj3;
export obj4;
```
- 导入模块
```javascript
// export default导出的数据可以随便起名
// export导出的数据，必须用{}接收；必须名称对应，但可以通过as增加别名；可以不全接收
import obj_my1 {obj2 , obj4 as obj_my2} from 'index.js'
```

- 数组新方法
    - forEach
    - some
    - every
    - findIndex
    - filter
    - map
    - indexof
    - splice(startIndex, numIndex, insteadValue1, insteadValue2)
  - reduce(fn(total, item, index, arr), 0)
- 字符串新方法
    - includes
    - indexof
    - padStart('填充完字符串总长度','填充物')
    - padEnd
- localStorage中只支持存放字符串
- div.domObject.getBoundingClientRect() 获取元素到屏幕的四个距离

## selenium 工具可以自动操作浏览器

1. 用于测试
2. 结合爬虫使用

## normalize.css

## 移动端300ms延迟 可以找库 解决

## 移动端开发完，可以用webpack loader自适应到其他屏幕

# 面试

## v-for &&v-if不建议放在一起

- v-for 优先级高于 v-if，所以会出现性能浪费，最好用 computed 处理完列表之后再绑定

## Vue.nextTick( [callback, context] )延迟运行-DOM更新后执行callback

- 直接调用，2个参数（回调函数和上下文），如果没有传入参数就作为promise使用
- 在Vue生命周期的created()进行的DOM操作一定要放在this.$nextTick()的回调函数中
- 在数据变化后要执行的某个操作，而这个操作需要使用随数据改变而操作的DOM结构的时候，这个操作都应该放进this.$nextTick()的回调函数中。
- this.$nextTick(()=>{}); this.$nextTick().then(); await this.$nextTick()

## 组件传值
- storage
- vuex
- 父子组件
  - props && $emit
  - 父组件 $refs 拿到子组件
  - this.$parent 拿到父组件
  - provide && inject
  - .async && update
  - $context
  - $attrs && $listeners
    - 如果父组件传入的属性，子组件未在props中声明，孙组件就可以拿到
    - <grandchild v-bind="$attrs" v-on="$listeners"></grandchild>
- 兄弟组件(vue3弃用)
  - eventBus

## vue3 和vue2的区别

1. 重写了vm，更新了虚拟dom的生命周期，优化了虚拟dom的运行时。加入了setup这个钩子函数

2. 将之前的option api升级成现在的composition api，就是组合式API，让代码看起来更简洁优雅

3. 优化了底层的diff算法

4. 使用新的es规范中的proxy重写了数据拦截和绑定的底层逻辑，解决了之前vue2中引用类型数据存在的一些情况下无法检测到的数据更新

5. 更新了一些内置方法，增加一些好用的api，teleport，fragments，provide/inject，suspense

6. teleport就是一些自由组件，可以在任意子组件中定义，并制定它要渲染的父组件位置，甚至可以是body

7. Fragments就是，vue3不需要写跟标签，vue3会将组件的所有标签直接放在一个fragment组件中

8. Suspense用来解决组件懒加载过程中的闪烁问题，懒加载的组件没有加载完成时候会展示默认组件

9. 其实在vue2的基础上更新了很多内容，和vue2不管在原理和写法上都有了重大更新，而且有很多的小的细节更新，但是兼容了vue2的写法，让我们使用者能比较平滑的过度

10. diff算法

11. watch：监听对象，监听对象的属性

12. 1. deep
    2. Immediate