# Vue.js的基础学习

## 一、概念

### **1.vue.js是什么**

vue.js是一套用于构建用户界面的渐进式JavaScript框架。

>渐进式代表的含义是：主张最少。                               
每个框架都不可避免会有自己的一些特点，从而会对使用者有一定的要求，这些要求就是主张，主张有强有弱，它的强势程度会影响在业务开发中的使用方式。<br/>比如说，Angular，它两个版本都是强主张的，如果你用它，必须接受以下东西：<br/>- 必须使用它的模块机制- 必须使用它的依赖注入- 必须使用它的特殊形式定义组件（这一点每个视图框架都有，难以避免）<br/><br/>所以Angular是带有比较强的排它性的，如果你的应用不是从头开始，而是要不断考虑是否跟其他东西集成，这些主张会带来一些困扰。<br/><br/>比如React，它也有一定程度的主张，它的主张主要是函数式编程的理念，比如说，你需要知道什么是副作用，什么是纯函数，如何隔离副作用。<br/>它的侵入性看似没有Angular那么强，主要因为它是软性侵入。<br/><br/>Vue可能有些方面是不如React，不如Angular，但它是渐进的，没有强主张，你可以在原有大系统的上面，把一两个组件改用它实现，当jQuery用；也可以整个用它全家桶开发，当Angular用；还可以用它的视图，搭配你自己设计的整个下层用。你可以在底层数据逻辑的地方用OO和设计模式的那套理念，也可以函数式，都可以，它只是个轻量视图而已，只做了自己该做的事，没有做不该做的事，仅此而已。<br/>渐进式的含义：没有多做职责之外的事。

[vue渐进式框架的理解](http://www.bslxx.com/a/vue/2017/1205/1490.html)

>个人理解  
可以让开发人员选取vue中的部分功能结合自己的组件和模块进行开发，而不是强推给开发者除了需要的功能以外还附加需要理解的知识。 

### **2.为什么学vue.js**

* 特点：双向数据绑定的概念

* 优点：通过框架提供的指令，开发者只需要关心数据的业务逻辑，不再关心DOM是如何渲染数据到页面上了。

>个人理解  
以前学js、jq的时候，在函数中需要经常书写代码获取到dom对象并赋值，现在只需要用提供的“{{ }}”等指令就可以直接在页面上显示数据，简化开发。

### **3.MVC和MVVM**

* MVC是后端分层的概念，model层、view层和controller层
* MVVM是是前端视图层的概念，主要关注于视图层分离，也就是说：MVVM把前端的视图层，分为了三部分 Model（保持的是每个页面中的单独数据）, View（每个页面中HTML结构） , VM ViewModel(v和m之间的调度者)。 mvvm为了让开发更加方便，提供了数据的双向绑定。

![MVC和MVVM的关系图解](https://allenwan14353.github.io/MyPostImages/vue/MVC和MVVM的关系图解.png)

​					 **图1 MVC和MVVM的关系图解**

[vue.js 2.x 文档](https://cn.vuejs.org/)

## 二、基本结构和语法

### **1. 导入js库**

需要先导入`vue-2.4.0.js`文件

### **2. 基本结构**

在js标签中书写基本结构

```vue
var myVue = new vue({
	el   :"#app",
	data :{ },
	...
})
```

## 三、属性

### **1.`el`**
提供一个在页面上已存在的DOM元素作为vue实例挂载目标，一般都是用css选择器，也就是vue控制作用的区域。

```
el:"#app"
```

**注意：** 

* 提供的元素只能作为挂载点。不同于 Vue 1.x，所有的挂载元素会被 Vue 生成的 DOM 替换。因此不推荐挂载 root 实例到<html> 或者 <body> 上。
* 如果 render 函数和 template 属性都不存在，挂载 DOM 元素的 HTML 会被提取出来用作模板，此时，必须使用 Runtime + Compiler 构建的 Vue 库。

 [vue的选项的el疑惑]("https://segmentfault.com/q/1010000012520601/")

### **2.`data`**
存储数据，vue会监听数据的改变，只要数据以发生改变，就会自动同步到页面。所以data中的数据总是最新的。

```
data:{
	test:"helloworld",
	test2:"<font color='red'>helloworld</font>",
	test3:true,
	color:"red",
	number:10,
}
```

### **3.`methods`**
定义当前vue所有可用的方法。

```
methods:{
	myMethods: function (){
		...
	},
	myMethods2 (){
		...
	}
}
<!--两种调用方式-->
<div id="app">
    <p><button @click="myMethods()">add</button></p>
</div>
<button onclick="myVue.myMethods()">add++</button>
```

### **4.`filters`**
定义私有过滤器属性。

### **5.`directives`**
自定义私有vue指令（动作和样式）。

### **6.`components`**
定义私有组件

### **7.`watch`**
可以监听到data中指定数据的变化，然后触发watch中对应的处理函数。

#### **7.1 监听data中数据的改变**

```vue
<script>
    var vm = new Vue({
        el: '#app',
        data: {
            firstName: 'jack',
            lastName: 'chen',
            fullName: 'jack - chen'
        },
        methods: {},
        watch: {
            // 第一个参数是新数据，第二个参数是旧数据
            'firstName': function (newVal, oldVal) { 
                this.fullName = newVal + ' - ' + this.lastName;
            },
            'lastName': function (newVal, oldVal) {
                this.fullName = this.firstName + ' - ' + newVal;
            }
        }
    });
</script>
```

* **注意：**watch中对应的处理函数名称和data中要监听的数据键一致。

#### **7.2 监听路由地址的改变**

```vue
<div id="app">
    <router-link to="/login">登录</router-link>
    <router-link to="/register">注册</router-link>

    <router-view></router-view>
</div>

<script>
    var login = Vue.extend({
        template: '<h1>登录组件</h1>'
    });

    var register = Vue.extend({
        template: '<h1>注册组件</h1>'
    });

    var router = new VueRouter({
        routes: [
            { path: "/login", component: login },
            { path: "/register", component: register }
        ]
    });

    // 创建 Vue 实例，得到 ViewModel
    var vm = new Vue({
        el: '#app',
        data: {},
        methods: {},
        router: router,
        watch: {
            '$route': function (newVal, oldVal) {
                if (newVal.path === '/login') {
                    console.log('这是登录组件');
                }
            }
        }
    });
</script>
```

* 获取到路由`$route`，监听路由的改变，也可以使用`$route.path`，这代表监听路由地址的改变。

### **8.computed**
在computed中可以定义一些属性，这些属性叫做计算属性，计算属性的本质就是一个方法，不过在使用的时候把它当作属性而不是方法来使用。

```vue
<div id="app">
    <input type="text" v-model="firstName">
    <input type="text" v-model="lastName">
    <!-- 点击按钮重新为 计算属性 fullName 赋值 -->
    <input type="button" value="修改fullName" @click="changeName">
    <span>{{fullName}}</span>
</div>

<script>
    var vm = new Vue({
        el: '#app',
        data: {
            firstName: 'jack',
            lastName: 'chen'
        },
        methods: {
            changeName() {
            	this.fullName = 'TOM - chen2';
            }
        },
        computed: {
            fullName: {
            	//getter计算并返回结果
                get: function () {
                	return this.firstName + ' - ' + this.lastName;
                },
                //setter也可以通过结果值改变去修改所用到的数据，也就是在某个地方直接调用改变结果值，会执行这个方法。
                set: function (newVal) {
                    var parts = newVal.split(' - ');
                    this.firstName = parts[0];
                    this.lastName = parts[1];
                }
        	}
        }
    });
</script>
```

* **注意：**
  * 计算属性调用的时候不用当作方法去调用，而是当作普通的data中的数据去调用。
  * 只要计算属性这个方法中所用到的任何数据改变，就会重新计算属性方法的返回值。
  * 计算属性的求值结果会被缓存起来，只要使用到的数据未改变，计算属性调用的时候就是一直使用这个缓存起来的值。
  * 计算属性中只有Date.now()这个值时，不会更新，因为Date.now()不是响应式依赖：

[Vue中的computed属性](https://www.cnblogs.com/gunelark/p/8492468.html)

### **9.render**

render属性渲染组件，可以用组件把原本的el指定的容器替换掉。

```vue
<script>
	var myVue = new vue({
        el : ... ,
        data : ... ,
        //createElements是一个任意起名的方法，调用可以把指定的组件模板，渲染为html结构
        // 这里不需要操作，会直接把原本的容器替换掉
        render : function (createElements){
        	//这里return的结果，会替换页面中el指定的那个容器,不再需要写<login></login>标签
        	return createElements(login)
    	}
    })
</script>
```

* **render 渲染组件和直接使用组件的区别：**
  * render渲染的组件，会先把原本el指定的父容器删掉，再替换成createElements函数中传递的组件参数，并且只能传递一个组件。
  * 直接使用组件只是将组件放入el指定的容器中，并且可以使用多个组件。

## 四、vue的指令

### **1. `{{ }}`**

插值表达式，放在文本中，可以直接调用vue中data的键，将相应的值数据渲染到前端。

```vue
<div id="app">
    <h1>{{test}}</h1>
</div>
```

### **2.`v-cloak`**

可以解决插值表达式闪烁（页面会先显示插值表达式，然后才会调用vue的数据）的问题。

```css
<style>
    [v-cloak] {
       display: none;
    }
 </style>

<div id="app">
    <h1 v-cloak>{{test}}</h1>
</div>
```

* **原理**：在style中设置为不可见，当vue数据请求成功的时候会移除v-cloak属性，style就可见了。

### **3.`v-text`**

可以直接调用data中键，在前端获取值，没有闪烁的问题；会覆盖元素中原本的文本内容。

```vue
<div id="app">
    <h1 v-text="test">你好啊</h1>
</div>
```

### **4.`v-html`**

可以解析data中调用的键对应值的**html标签**，显示在文本中

```vue
<div id="app">
    <h1 v-html="test2">你好啊</h1>
</div>
```

### **5.`v-for`**

循环条件，在vue2的版本里，当在组件中使用 v-for 时，key 属性现在是必须绑定的，属性值只能使用number或string。

```vue
data: {
	array: [1, 2, 3, 4, 5, 6],
    list: [
          { id: 1, name: 'zs1' },
          { id: 2, name: 'zs2' },
          { id: 3, name: 'zs3' },
          { id: 4, name: 'zs4' }
        ],
    user: {
          id: 1,
          name: '托尼·屎大颗',
          gender: '男'
        },
    
},
```

**5.1 循环数组**

```vue
<p v-for="(item,i) in array">
    值：{{ item }}--索引：{{ i }}
</p>
```

**5.2 对象数组**

```vue
<p v-for="(item,i) in list" :key="item.id">
    值：{{ item.id }}--索引：{{ i }}
</p>
```

**5.3 循环对象（将对象的属性值按照键值对一样循环）**

```vue
<p v-for="(value,key,i) in user" :key="value">
    值：{{ value }}--键：{{ key }}--索引：{{ i }}
</p>
```

**5.4 循环数字**

```vue
<p v-for="count in 10">
    值：{{ value }}
</p>
```

### **6.`v-if`、`v-show`、`v-else`**

判断条件,控制标签内容的显示和隐藏。

* v-if：每次都会删除或重新创建元素。
* v-show：每次在前台只是切换了标签的display:none 样式。

* v-else：v-else相当于v-if=!变量。

一般来说，v-if 有更高的切换消耗而 v-show 有更高的初始渲染消耗。因此，如果需要频繁切换 v-show 较好，如果在运行时条件不大可能改变 v-if 较好。

```vue
<h1 v-if="test3">
    判断条件
</h1>
```

### **7.`v-bind:`、`v-model:`**

数据绑定

* v-bind：缩写为 ` :`，会自动把属性值与data中对应的键绑定，将属性值当作变量表达式来执行

* v-mode：双向数据绑定，实现表单中数据和vue中model数据的双向绑定，修改任何一方都会对另外一方进行修改。

```vue
<h1 :font="color">
    helloworld
</h1>
表单表达式：<input v-model:value="number-1"/> 
```

[Vue.js双向绑定的实现原理](http://www.cnblogs.com/kidney/p/6052935.html)

### **8.`v-on`**

缩写为@，事件绑定，属性值为定义的方法。

```vue
<a href="javascript:void(0)" @click="myMethods"> 事件绑定 </a>
<!--可以直接对data数据操作,会改变data中的值-->
<p><button @click="number=number-1"> 事件绑定 </button></p>
```

### **9.`ref`**

获取标签的document对象和组件并进行操作。

```vue
<div id="app">
    <div>
        <input type="button" value="获取元素内容" @click="getElement" />
        <!-- 使用 ref 获取元素 -->
        <h1 ref="myh1">这是一个大大的H1</h1>

        <hr>
        <!-- 使用 ref 获取子组件 -->
        <my-com ref="mycom"></my-com>
    </div>
</div>

<script>
    Vue.component('my-com', {
        template: '<h5>这是一个子组件</h5>',
        data() {
            return {
                name: '子组件'
            }
        }
    });

    // 创建 Vue 实例，得到 ViewModel
    var vm = new Vue({
        el: '#app',
        data: {},
        methods: {
            getElement() {
                // 通过 `this.$refs` 来获取标签的document对象
                console.log(this.$refs.myh1.innerText);
                // 通过 `this.$refs` 来获取组件,可以直接获取组件的data数据和methods方法
                console.log(this.$refs.mycom.name);
            }
        }
    });
</script>
```

## 五、vue中样式

### **1.使用class批量样式**

**1.1 数组**

```vue
<h1 :class="['red', 'thin'，italic]">这是一个邪恶的H1</h1>
```

但是当不使用单引号时，italic表示绑定了data中的键

```vue
data:{italic:"italic"}
```

**1.2 数组中使用三元表达式**

```vue
<h1 :class="['red', 'thin', isactive?'active':'']">这是一个邪恶的H1</h1>
```

**1.3 数组中嵌套对象**

```vue
<h1 :class="['red', 'thin', {'active': isactive}]">这是一个邪恶的H1</h1>
```

**1.4 直接使用对象**

```vue
<h1 :class="{"red":true, "italic":true, "active":true, "thin":true}">这是一个邪恶的H1</h1>
```

### **2.使用内联样式**

**2.1 直接在元素上通过 `:style` 的形式，书写样式对象**

```vue
<h1 :style="{color: 'red', 'font-size': '40px'}">这是一个善良的H1</h1>
```

**2.2 将样式对象，定义到 `data` 中，并直接引用到 `:style` 中**

在data上定义样式：

```vue
data: {
        h1StyleObj: { color: 'red', 'font-size': '40px', 'font-weight': '200' }
}
```

在元素中，通过属性绑定的形式，将样式对象应用到元素中：

```vue
<h1 :style="h1StyleObj">这是一个善良的H1</h1>
```

**2.3 在 `:style` 中通过数组，引用多个 `data` 上的样式对象**

在data上定义样式：

```vue
data: {
        h1StyleObj: { color: 'red', 'font-size': '40px', 'font-weight': '200' },
        h1StyleObj2: { fontStyle: 'italic' }
}
```

在元素中，通过属性绑定的形式，将样式对象应用到元素中：

```vue
<h1 :style="[h1StyleObj, h1StyleObj2]">这是一个善良的H1</h1>
```

## 六、vue的过渡和动画

动画能够提高用户的体验，帮助用户更好的理解页面中的功能。

### **1.第三方类库**

使用transition标签，引入animate.css第三方类库

### **2.普通标签的三种过渡方式**

**2.1 过渡类名**

```vue
//HTML结构
<div id="app">
    <input type="button" value="动起来" @click="myAnimate">
    <!-- 使用 transition 将需要过渡的元素包裹起来 -->
    <transition name="fade">
        <div v-show="isshow">动画哦</div>
    </transition>
</div>

//VM 实例
var vm = new Vue({
  el: '#app',
  data: {
    isshow: false
  },
  methods: {
    myAnimate() {
      this.isshow = !this.isshow;
    }
  }
});

//定义两组类样式
<style>
    /* 定义进入和离开时候的过渡状态 */
    .fade-enter-active,
    .fade-leave-active {
    	transition: all 0.2s ease;
    	position: absolute;
    }

    /* 定义进入过渡的开始状态 和 离开过渡的结束状态 */
    .fade-enter,
    .fade-leave-to {
        opacity: 0;
        transform: translateX(100px);
    }
</style>
```

**2.2 使用第三方 CSS 动画库**

```vue
//导入动画类库：
<link rel="stylesheet" type="text/css" href="./lib/animate.css">

//定义 transition 及属性：
<transition
	enter-active-class="fadeInRight"
    leave-active-class="fadeOutRight"
    :duration="{ enter: 500, leave: 800 }">
  	<div class="animated" v-show="isshow">动画哦</div>
</transition>
```

**2.3 使用动画钩子函数**

```vue
//定义 transition 组件以及三个钩子函数：
<div id="app">
    <input type="button" value="切换动画" @click="isshow = !isshow">
    <transition
        @before-enter="beforeEnter"
        @enter="enter"
        @after-enter="afterEnter">
        <div v-if="isshow" class="show">OK</div>
    </transition>
</div>

//定义三个 methods 钩子方法：
methods: {
	// 动画进入之前的回调
    beforeEnter(el) { 
    	el.style.transform = 'translateX(500px)';
    },
    // 动画进入完成时候的回调
    enter(el, done) { 
        el.offsetWidth;
        el.style.transform = 'translateX(0px)';
        done();
    },
    // 动画进入完成之后的回调
    afterEnter(el) { 
    	this.isshow = !this.isshow;
    }
}

//定义动画过渡时长和样式：
<style>
.show{
	transition: all 0.4s ease;
}
</style>
```

* **注意：**
  
    * 可以在transition标签中增加name属性自定义进场和出场标签类名称
    * 如果只想实现半场动画（只想进场或者出场），使用js的进场和出场钩子函数

### **3.v-for 的列表过渡**

```vue
// 定义过渡样式：
<style>
    .list-enter,
    .list-leave-to {
        opacity: 0;
        transform: translateY(10px);
    }

    .list-enter-active,
    .list-leave-active {
    	transition: all 0.3s ease;
    }
</style>

// 定义DOM结构，其中，需要使用 transition-group 组件把v-for循环的列表包裹起来：
<div id="app">
	<input type="text" v-model="txt" @keyup.enter="add">

	<transition-group tag="ul" name="list">
		<li v-for="(item, i) in list" :key="i">{{item}}</li>
	</transition-group>
</div>

// 定义 VM中的结构：
var vm = new Vue({
    el: '#app',
    data: {
        txt: '',
        list: [1, 2, 3, 4]
	},
    methods: {
        add() {
            this.list.push(this.txt);
            this.txt = '';
    	}
	}
});
```

  * **注意：**
  
    * 实现列表动画的时候被v-for渲染的需要使用transitionGroup包裹

    * 如果要为v-for循环创建的元素设置动画，必须为每一个元素设置 ：key

    * `<transition-group>` 中 `tag="..."`让`transition`标签在html界面显示成某个标签，默认为`<span>` ，`appear`让列表实现淡入

### **4.列表的排序过渡**

  * `<transition-group>` 组件还有一个特殊之处。不仅可以进入和离开动画，还可以改变定位。要使用这个新功能只需了解新增的 `v-move` 特性，它会在元素的改变定位的过程中应用。

  + `v-move` 和 `v-leave-active` 结合使用，能够让列表的过渡更加平缓柔和：

  ```vue
  <style>
      .v-move{
        transition: all 0.8s ease;
      }
      .v-leave-active{
        position: absolute;
      }
  </style>
  ```


[Vue中的动画](https://cn.vuejs.org/v2/guide/transitions.html)

## 七、事件修饰符和键盘修饰符

### **1.事件修饰符**

vue的事件修饰符可以链式编程

* **事件.stop：**可以阻止冒泡( 当一个元素接收到事件的时候 会把他接收到的事件传给自己的父级，一直到window 。注意这里传递的仅仅是事件，并不传递所绑定的事件函数，所以如果父级没有绑定事件函数，就算传递了事件也不会有什么表现，但事件确实传递了，就算子中没有事件，但是对子进行事件操作的时候还是会触发父级的相关事件。）

* **事件.prevent：**阻止默认事件，也就是每个阻止每个事件的发生。

* **事件.capture：**添加事件监听器时使用事件捕获模式（由外向里触发事件）。

* **事件.self**只当事件在该元素本身（不是子元素冒泡而来的事件）触发时触发回调。

* **事件.once：**事件函数只触发一次。

### **2.键盘修饰符**

修饰键盘敲击某个按键后触发的事件

```
<!--按下enter键后触发事件-->
<a href="#" @keyup.enter="method">
<!--按下f2键后触发事件-->
<a href="#" @keyup.113="method">
<!--自定义f2按键修饰符-->
<a href="#" @keyup.myF2="method">
<script>
    //自定义全局按键修饰符
	Vue.config.keyCodes.myF2=113    
</script>
```

[js 里面的键盘事件对应的键码](http://www.cnblogs.com/wuhua1/p/6686237.html)

## 八、过滤器

过滤器是调用函数前先处理一遍数据再渲染到前端，可以链式编程。

### **1.全局过滤器**

所有被vue实例挂载的目标都可以使用

```vue
<script>
	Vue.fliter("过滤器名称"，function(data，.......){   
        //第一个参数是固定获取管道前的参数，后面的参数是自定义传入的参数 
        //若参数中 形参=”“ 时，表示不传入这个参数时，会默认出形参为指定的值 
      return  “对data进行操作，再返回”;
	});
    var myVue = new vue({
        el:"#app",
        data:{
            name = "名字"
        }
    });
</script>
<div id="app">
    <p>
        {{name | "过滤器名称（自定义传入的参数）"}} 
    </p>
</div>
```

### **2.私有过滤器**

私有局部过滤器，只能在当前 VM 对象所控制的 View 区域进行使用。

```vue
<script>
	var myVue = new vue(
    	el:"#app",
        data:{
        	name = "名字"
        },
        //定义私有过滤器
    	filters:{
            过滤器名称 : function (data,......) {
                return “对data进行操作，再返回”;
            }
        }，
    );
</script>
```

* **注意：**当有局部和全局两个名称相同的过滤器时候，会以就近原则进行调用，即：局部过滤器优先于全局过滤器被调用！

### **3.自带过滤器**

vue有默认自带的过滤器

```vue
<script>	  
    new Vue({ el:"",data:{ msg:'abc'}})   
</script>
<p>
    //1.首字母大写: capitalize 
    {{ msg | capitalize}}  
    //2.全部大写: uppercase 
    {{ msg | uppercase  }} 
    //3.全部小写:lowercase 
    {{ msg | lowercase  }} 
</p>
```

## 九、自定义指令

除了默认设置的核心指令( v-model 和 v-show )，Vue 也允许注册自定义指令。注意，在 Vue2.0 里面，代码复用的主要形式和抽象是组件——然而，有的情况下,你仍然需要对纯 DOM 元素进行底层操作,这时候就会用到自定义指令。

### **1.全局指令**

```vue
<script>
	//自定义v-focus指令
    Vue.directive("focus",{
        inserted : function (el,.....) {
        	//表示元素插入到DOM中的时候，触发一次 
        },       
    });
</script>
<input v-focus/>
```

### **2.私有指令**

```vue
<script>
	//自定义v-focus指令
    var myVue = new vue(){
        el : ...,
        data : ...,
        directives : {
            focus : {
               inserted : function (el,.....) {
        			....
        	   }   
            }
        }    
    };
</script>

<input v-focus/>
```

### **3.指令的钩子函数**

**3.1 钩子函数：**

钩子函数是由系统消息触发的函数

* bind: 只调用一次，指令第一次绑定到元素时调用，用这个钩子函数可以定义一个在绑定时执行一次的初始化动作，但是元素还没有重新插入到dom中，和行为相关的操作不会生效。
* inserted: 只调用一次，被绑定元素插入父节点时调用（父节点存在即可调用，不必存在于 document 中）。
* update: 被绑定元素所在的模板更新时调用，而不论绑定值是否变化。通过比较更新前后的绑定值，可以忽略不必要的模板更新。
* componentUpdated: 被绑定元素所在模板完成一次更新周期时调用。
* unbind: 只调用一次， 指令与元素解绑时调用。

**3.2 钩子函数的参数 ：**

* el: 指令所绑定的元素，可以用来直接操作 DOM 。
* binding: 一个对象，包含以下属性：
  * name: 指令名，不包括 v- 前缀。
  * value: 指令的绑定值， 例如： v-my-directive=”1 + 1”, value 的值是 2。
  * oldValue: 指令绑定的前一个值，仅在 update 和 componentUpdated 钩子中可用。无论值是否改变都可用。
  * expression: 绑定值的字符串形式。 例如 v-my-directive=”1 + 1” ，expression 的值是 “1 + 1”。
  * arg: 传给指令的参数。例如 v-my-directive:foo， arg 的值是 “foo”；v-my-directive:foo.bar，arg 的值是 “foo”。
  * modifiers: 一个包含修饰符的对象。 例如： v-my-directive.foo.bar, 修饰符对象 modifiers 的值是 { foo: true, bar: true }；v-my-directive:foo.bar，修饰符对象 modifiers 的值是 { bar: true }
* vnode: Vue 编译生成的虚拟节点，查阅 VNode API 了解更多详情。
* oldVnode: 上一个虚拟节点，仅在 update 和 componentUpdated 钩子中可用。

**注意：**

* 除了 el 之外，其它参数都应该是只读的，尽量不要修改他们。如果需要在钩子之间共享数据，建议通过元素的 dataset 来进行。

* 定义的时候不需要加 v- 前缀，调用的时候需要。
* 和行为有关的操作，会在内存中保存一下，没有插入到dom中就会失效，应该使用inserted函数；和样式相关的操作，只要通过指令绑定给了元素，不管这个元素有没有被插入到页面中去，这个元素肯定有了一个内联的样式，将来元素肯定会显示到页面中，浏览器的渲染引擎必然会解析样式，应用给这个元素，所有可以使用binding钩子函数。

## 十、vue生命周期

从Vue实例创建、运行、到销毁期间，总是伴随着各种各样的事件，这些事件，统称为生命周期。

![vue的生命周期](https://allenwan14353.github.io/MyPostImages/vue/lifecycle.png)

​                                                            **图2 vue生命周期图**

### **1.创建期间的函数**

**1.1 beforeCreate：**实例刚在内存中被创建出来，此时还没有初始化好 data 和 methods 属性。

**1.2 created：**实例已经在内存中创建OK，此时 data 和 methods 已经创建，此时还没有开始编译模板，已经可以调用data和methods。

**1.3 beforeMount：**此时已经完成了模板的编译，但是还没有挂载到页面中，也就是页面中vue指令还不能获取到data和methods。

**1.4 mounted：**此时，已经将编译好的模板，挂载到了页面指定的容器中显示。

### **2.运行期间的函数：**

**2.1 beforeUpdate：**状态更新之前执行此函数， 此时 data 中的状态值是最新的，但是界面上显示的 数据还是旧的，因为此时还没有开始重新渲染DOM节点

**2.2 updated：**实例更新完毕之后调用此函数，此时 data 中的状态值 和 界面上显示的数据，都已经完成了更新，界面已经被重新渲染好了！

### **3.销毁期间的函数：**

**3.1 beforeDestroy：**实例销毁之前调用。在这一步，实例仍然完全可用。

**3.2 destroyed：**Vue 实例销毁后调用。调用后，Vue 实例指示的所有东西都会解绑定，所有的事件监听器会被移除，所有的子实例也会被销毁。

[ Vue生命周期理解 ](https://segmentfault.com/a/1190000018331135)

### **4.生命周期函数代码示例**

```vue
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>vue生命周期学习</title>
  <script src="https://cdn.bootcss.com/vue/2.4.2/vue.js"></script>
</head>
<body>
  <div id="app">
    <h1>{{message}}</h1>
  </div>
</body>
<script>
  var vm = new Vue({
    el: '#app',
    data: {
      message: 'Vue的生命周期'
    },
    beforeCreate: function() {
      console.group('------beforeCreate创建前状态------');
      console.log("%c%s", "color:red" , "el     : " + this.$el); //undefined
      console.log("%c%s", "color:red","data   : " + this.$data); //undefined 
      console.log("%c%s", "color:red","message: " + this.message) 
    },
    created: function() {
      console.group('------created创建完毕状态------');
      console.log("%c%s", "color:red","el     : " + this.$el); //undefined
      console.log("%c%s", "color:red","data   : " + this.$data); //已被初始化 
      console.log("%c%s", "color:red","message: " + this.message); //已被初始化
    },
    beforeMount: function() {
      console.group('------beforeMount挂载前状态------');
      console.log("%c%s", "color:red","el     : " + (this.$el)); //已被初始化
      console.log(this.$el);
      console.log("%c%s", "color:red","data   : " + this.$data); //已被初始化  
      console.log("%c%s", "color:red","message: " + this.message); //已被初始化  
    },
    mounted: function() {
      console.group('------mounted 挂载结束状态------');
      console.log("%c%s", "color:red","el     : " + this.$el); //已被初始化
      console.log(this.$el);    
      console.log("%c%s", "color:red","data   : " + this.$data); //已被初始化
      console.log("%c%s", "color:red","message: " + this.message); //已被初始化 
    },
    beforeUpdate: function () {
      console.group('beforeUpdate 更新前状态===============》');
      console.log("%c%s", "color:red","el     : " + this.$el);
      console.log(this.$el);   
      console.log("%c%s", "color:red","data   : " + this.$data); 
      console.log("%c%s", "color:red","message: " + this.message); 
    },
    updated: function () {
      console.group('updated 更新完成状态===============》');
      console.log("%c%s", "color:red","el     : " + this.$el);
      console.log(this.$el); 
      console.log("%c%s", "color:red","data   : " + this.$data); 
      console.log("%c%s", "color:red","message: " + this.message); 
    },
    beforeDestroy: function () {
      console.group('beforeDestroy 销毁前状态===============》');
      console.log("%c%s", "color:red","el     : " + this.$el);
      console.log(this.$el);    
      console.log("%c%s", "color:red","data   : " + this.$data); 
      console.log("%c%s", "color:red","message: " + this.message); 
    },
    destroyed: function () {
      console.group('destroyed 销毁完成状态===============》');
      console.log("%c%s", "color:red","el     : " + this.$el);
      console.log(this.$el);  
      console.log("%c%s", "color:red","data   : " + this.$data); 
      console.log("%c%s", "color:red","message: " + this.message)
    }
  })
</script>
</html>
```

[详解vue生命周期](https://segmentfault.com/a/1190000011381906?utm_source=tag-newest)

## 十一、组件

组件的出现，就是为了拆分Vue实例的代码量的，能够让我们以不同的组件，来划分不同的功能模块，将来我们需要什么样的功能，就可以去调用对应的组件即可。

### **1.自定义全局组件**

**1.1使用 Vue.extend 配合 Vue.component 方法：**

```vue
var login = Vue.extend({
	template: '<h1>登录</h1>'
});

//经过测试，下面这种写法也不会报错
/*
var login={
template: '<h1>登录</h1>',
};
*/

Vue.component('login', login);
```

**1.2 直接使用 Vue.component 方法：**

```vue
Vue.component('register', {
	template: '<h1>注册</h1>'
});		
```

**1.3 将模板字符串，定义到script标签中：**

```vue
<template id="tmpl">
	<div><a href="#">登录</a> | <a href="#">注册</a></div>
</template>
<script>
//同时，需要使用 Vue.component 来定义组件：
    Vue.component('account', {
	template: '#tmpl'
});
</script>
```

### **2.自定义私有组件**

```vue
<script>
  var app = new Vue({
    el: '#app',
    data: {
    },
  components: {
      'my-component': {
      template: "<div>这是一个局部的自定义组件，只能在当前Vue实例中使用</div>",
      }
    }
  })
</script>

//调用方法
<div id="app">
	<mycomponent></mycomponent>
    <my-component></my-component>
</div>
```

* 注意

  * 若组件命名的时候是驼峰的书写形式`myLogin1`,则标签在使用的时候需要写成
    `<my-login1></my-login1>`。
  * template中必须只有一个根元，也就是只能写成`<div><h1></h1></div>`，不能写成`<div></div><h1></h1>`。

### **3.组件中展示数据和响应事件**

```vue
Vue.component("组件名称"，{
    template:"",
	data:function(){
        return {}
    }，
    methods:{
		//这个事件是在template中标签产生的事件函数 
    }
}）
```

  * **注意：**
      * `data`属性必须是一个函数，这是因为如果像Vue实例那样，传入一个对象，由于JS中对象类型的变量实际上保存的是对象的`引用`，所以当多次引用这个组件时，会共享数据，导致一个组件中数据的改变会引起其他组件数据的改变。而使用一个返回对象的函数，每次使用组件都会创建一个新的对象，这样就不会出现共享数据的问题来了。
    * 当使用 DOM 作为模版时 (例如，将 el 选项挂载到一个已存在的元素上), 你会受到 HTML 的一些限制，因为 Vue 只有在浏览器解析和标准化 HTML 后才能获取模板内容。尤其像这些元素 `<ul>`，`<ol>`，`<table>`，`<select>` 限制了能被它包裹的元素，而一些像 `<option> `这样的元素只能出现在某些其它元素内部。所以使用受限制的元素时会导致一些问题，此时可以用限制元素的`is`属性,例如`<tr is="my-component">`，相当于用tr包裹这个组件。

### **4.组件切换**

通过切换组件实现使用不同的组件效果

```vue
<!--
使用Vue的component标签
transition的mode属性控制组件切换顺序
-->
<transition mode="in-out">  
    <component :is="'组件名称的字符串'或者data中的键">
	</component>
</transition> 
```

### **5.父子组件**

components属性相当于vue下的一个子组件，默认无法访问到父组件中的data上的数据和methods中的方法。

**5.1 传递数据**

```vue
//要先在父组件vue区域中引用子组件
<div id="app">
   <com1 v-bind:子组件自定义的属性名称="父组件data的键">	        </com1>
</div>
components:{
	myCom:{
		//引用的时候在template中就可以直接使用子组件自定义的属性名称
        template:"....",
        //其中的数据只可读
        props:['子组件自定义的属性名称',....]
        }	
}
//2.vue的localStorage数据缓存，父子组件都可以把数据放在里面，进行传递

//3.通过 `this.$refs` 来获取组件,可以直接获取组件的data数据和methods方法
```

**5.2 传递方法**

```vue
<div id="app">
   <com1 v-on:子组件自定义的属性名称="父组件方法的名称"></com1>
</div>
components:{
	myCom:{
		//template中的标签绑定方法时，使用子组件定义的方法
        template:"....",
        //其中的数据只可读
        methods:{
				方法名称(){
					//arg1:子组件自定义的属性名称;
					//arg2:若父组件的方法有形参，从第二个参数开始都可以传实参，可以用这个特性让父获取子的data
					//使用$emit调用
					this.$emit('arg1'，arg2....)  
			}
		}
    }	
}
```

* **注意：**
    * 引用的是`方法名称`，不是函数`方法名称()`，因为子组件只是引用父组件中的方法，若是函数则会获取到返回值。
    * 会先在data中查找是否有这个键，再去查是否有这个方法。


[深刻理解vue中的组件](https://segmentfault.com/a/1190000010527064)

## 十二、路由

网络原理中，路由指的是根据上一接口的数据包中的IP地址，查询路由表转发到另一个接口，它决定的是一个端到端的网络路径。
使用vue.js构建项目，vue.js本身就可以通过组合组件来组成应用程序；当引入vue-router后，我们需要处理的是将组件(components)映射到路由(routes)，然后在需要的地方进行使用渲染。

**后端路由：**对于普通的网站，所有的超链接都是URL地址，所有的URL地址都对应服务器上对应的资源，这个对应关系叫做路由。

**前端路由：**对于单页面应用程序来说，主要通过URL中的hash(#号)来实现不同页面之间的切换，同时，hash有一个特点：HTTP请求中不会包含hash相关的内容；所以，**单页面**程序中的页面跳转主要用hash实现。

[URL中的hash（井号）](http://www.cnblogs.com/joyho/articles/4430148.html)

[你真的了解前端路由吗](https://juejin.im/post/5b5ec5dd6fb9a04fc564b72d)

> 个人理解
>
> url 地址中#后面的东西都叫做hash，前端路由实现的是单页面中的不同组件的切换，可以实现不刷新页面的条件下在单页面内跳转。

### **1.基本结构和语法**

**1.1 导入 `vue-router.js` 组件类库**

**1.2 在js中书写基本结构**

```vue
<script>
    //1. 组件的模板对象
    var login = {
    	template: '<h1>登录组件</h1>'
    }
    var register = {
    	template: '<h1>注册组件</h1>'
    }
    //2. 创建路由对象
    var routerObj = new VueRouter({
        // 路由匹配规则 
        routes: [ 
        // 每个路由规则，都是一个对象，这个规则对象，身上，有两个必须的属性：`path`表示监听哪个路由链接地址；`component`表示如果路由是前面匹配到的`path`，则展示`component`属性对应的组件。
        { path: '/login', component: login },
        { path: '/register', component: register }，
        // 默认展示指定组件，但是不会将hash值放到url中，所以在url的地址是#/      
        { path: '/', component: login },
        // 默认跳转到指定组件并重定向到url地址中，url地址是#/login   
        { path: '/', redirect: '/login' },
      ],
    })
    //3. 创建 Vue 实例
    var vm = new Vue({
      el: '#app',
      data: {},
      methods: {},
      // 将路由规则对象，注册到vue实例上，用来监听URL地址的变化，然后展示对应的组件
      router: routerObj 
    });
</script>
```

* **注意**：
  * component 的属性值，必须是一个组件的模板对象， 不能是自定义组件的引用名称；
  * 路由可以监听到url的改变

### **2.使用路由标签**

```vue
<!-- <a href="#/login">登录</a> -->
<!-- <a href="#/register">注册</a> -->

<!-- 使用 router-link 组件来导航，默认渲染为一个a 标签,效果和上面定义的a标签相同 -->
<router-link to="/login">登录</router-link>
<router-link to="/register">注册</router-link>

<!-- 使用 router-view 组件来显示匹配到的组件 -->
<router-view></router-view>
```

### **3.路由标签的类名**

`<router-link>`标签在前端默认渲染为`<span>`标签，并且`class="router-link-exat-active router-link-active"`，若想修改该标签默认的类名，则需在router对象属性中配置linkActiveClass，可以对class类名进行样式定义。

```vue
var routerObj = new VueRouter({
    // 路由匹配规则 
  routes: [....],
    linkActiveClass: 'myactive'
})
//tag可以修改标签在前端显示成的标签
<router-link to="/" tag="div">....</router-link>
/*修改后在前端渲染效果
  <div class="myactive">....</div>
*/
```

### 4.路由的动画效果

```vue
<transition mode="out-in">
	<router-view></router-view>
</transition>
```

### **5.路由传参方式**

在路由规则中定义参数，可以将url地址中hash的参数传给组件使用。

**5.1 使用query方式传递参数**

不需要修改路由中的path， 通过`this.$route.params`就可以获取到参数。

```vue
//url: #/login?name=zs&age=20
<router-link to="/login?name=zs&age=12">

//在组件的属性中都可以使用
var register = Vue.extend({
	template: '<h1>注册组件 --- {{this.$route.query.name}}</h1>'
});
```

**5.2 使用params方式传递参数**

需要修改path进行匹配

```
//url: #/login/zs/20
<router-link to="/login/zs/12">

//在组件的属性中都可以使用
var register = Vue.extend({
	template: '<h1>注册组件 --- {{this.$route.params.name}}</h1>'
});

{path:"/login/:name/:age",component:login}
```

### **6.路由的嵌套**

在组件template中嵌套子路由的组件，使用路由的childrens属性

```vue
//两种书写方式

//1.在子路由path中，不带`/`
<router-link to="/account/login">
routes:[
    {path:"/accout",component:account,
		childrens:[
    		{path:"login",component:login},
    		.....
    	],
    }
]
//在前端显示的url会自动拼接为 /account/login

// 2.在子路由path中带`/`
<router-link to="/login">
routes:[
	{path:"/accout",component:account,
		childrens:[
			{path:"/login",component:login},
			.....
		]
	}
]
//带斜杠会以根路由匹配，不会拼接/accout，在前端显示的url为/login
```

### **7.命名视图实现经典布局**

实现`<frame>`标签一样的的布局

```vue
// 创建路由对象
var router = new VueRouter({
    routes: [
        {path: '/', components: {
                'default': header,
                'left': leftBox,
                'main': mainBox
            }
    	}
    ]
})

<router-view></router-view>
//实现左右布局，根据name去path中找，并且可以对name加样式
<div class="container"> 
    <router-view name="left"></router-view>
    <router-view name="main"></router-view>
</div>
```

[vue之vue router](https://segmentfault.com/a/1190000017532201)

[vue.js路由vue-router（一）——简单路由基础](https://segmentfault.com/a/1190000009350679)

[vue.js路由vue-router（二）——路由进阶](https://segmentfault.com/a/1190000009425705)

[详解Vue的钩子函数](https://www.jb51.net/article/144337.htm)

[Vue路由钩子 afterEach beforeEach区别](https://www.cnblogs.com/wuvkcyan/p/9311155.html)

## 十三、vue-resource

引入vue-resource文档，实现get,post,jsonp请求 

### **1.导入js库**

需要导入vue-resource.js

### **2.基本结构**

```vue
this.$http.get（url,{json格式的数据}，后面都是可选项....）.then(
    function(result){
    	//成功返回的函数
    },
    function(){
    	//报错返回的函数
    }
)
```

* **注意：**手动发起的post请求默认没有表单格式，所以有的服务器处理不了，需要设置可选项为` emulateJSON：true `设置提交的内容类型为普通表单数据格式。

>jsonp
>
>由于浏览器的安全性限制，不允许AJAX访问协议不同、域名不同、端口号不同的 数据接口，浏览器认为这种访问不安全；跨域问题来源于JavaScript的同源策略，即只有 协议+主机名+端口号 (如存在)相同，则允许相互访问。也就是说JavaScript只能访问和操作自己域下的资源，不能访问和操作其他域下的资源。跨域问题是针对JS和ajax的，html本身没有跨域问题，比如a标签、script标签、甚至form标签（可以直接跨域发送数据并接收数据等）
>
>可以通过动态创建script标签的形式，把script标签的src属性，指向数据接口的地址，因为script标签不存在跨域限制，这种数据获取方式，称作JSONP（注意：根据JSONP的实现原理，知晓JSONP只支持Get请求）。

### **3.基于node实现JSONP**

**3.1 客户端**

```vue
<script>
    function showInfo123(data) {
    	console.log(data)
    }
</script>
<script src="http://127.0.0.1:3000/getscript?callback=showInfo123"></script>
```

**3.2 服务器端**

```node
// 导入 http 内置模块
const http = require('http')
// 这个核心模块，能够帮我们解析 URL地址，从而拿到  pathname  query 
const urlModule = require('url')

// 创建一个 http 服务器
const server = http.createServer()
// 监听 http 服务器的 request 请求
server.on('request', function (req, res) {
  // const url = req.url
  const { pathname: url, query } = urlModule.parse(req.url, true)
  
  if (url === '/getscript') {
    // 拼接一个合法的JS脚本，这里拼接的是一个方法的调用
    // var scriptStr = 'show()'

    var data = {
      name: 'xjj',
      age: 18,
      gender: '女孩子'
    }

    var scriptStr = `${query.callback}(${JSON.stringify(data)})`
    // res.end 发送给 客户端， 客户端去把 这个 字符串，当作JS代码去解析执行
    res.end(scriptStr)
  } else {
    res.end('404')
  }
})

// 指定端口号并启动服务器监听
server.listen(3000, function () {
  console.log('server listen at http://127.0.0.1:3000')
})
```

### **4.全局配置数据接口的跟域名**

配置url的根域名，可以省略不写根域名，会自动拼接再url参数前面。

```js
Vue.http.options.root='url的根域名'

this.$http.get("后面的域名")

//拼接成 `url的根域名/后面的域名`
```

* **注意：**使用时注意请求时url参数不带斜线`/`才会拼接根域名

### **5.全局配置`emulateJSON**`

配置了全局`emulateJSON`后，请求参数中就默认加了这个，不用再写。

```js
Vue.http.options.emulateJSON=true
```

[vue前后台数据交互vue-resource文档](https://www.cnblogs.com/yuzhengbo/p/6714355.html)

[pagekit/vue-resource](https://github.com/pagekit/vue-resource)

> vue2.0之后，就不再对vue-resource更新，而是推荐使用axios。基于 Promise 的 HTTP 请求客户端，可同时在浏览器和 Node.js 中使用。
>
> [vue-resource和axios区别](https://www.cnblogs.com/chenhuichao/p/9261645.html)
>
> [Jquery ajax, Axios, Fetch区别之我见](https://segmentfault.com/a/1190000012836882)
>
> [使用fetch](https://developer.mozilla.org/zh-CN/docs/Web/API/Fetch_API/Using_Fetch)

#  Vue.js的进阶学习

## 一、npm的概念

npm 的全称是 Node Package Manager 是 JavaScript 世界的包管理工具,并且是 Node.js 平台的默认包管理工具，通过 npm 可以安装、共享、分发代码，管理项目依赖关系。

[nvm、nrm、npm 安装和使用详解](https://blog.csdn.net/qq_27626333/article/details/77857223)

[node中，npm、nvm、nrm、npx、cnpm区别和用法](https://blog.csdn.net/bamboozjy/article/details/86605659)

[如何安装 npm 并管理 npm 版本](http://caibaojian.com/npm/getting-started/installing-node.html)

[用nrm一键切换npm源](https://www.cnblogs.com/wangmeijian/p/7072053.html)

## 二、webpack的概念

### **1.webpack是什么**

webpack 是前端的一个项目构建工具，它是基于 Node.js 开发出来的一个前端工具。

### **2.为什么使用webpack**

* 传统的网页中引入的静态资源（.js，.css，.jpg等文件），导致网页加载速度慢， 因为我们要发起很多的二次请求；并且要处理错综复杂的依赖关系。

* 借助于webpack这个前端自动化构建工具，可以完美实现资源的合并、打包、压缩、混淆等诸多功能。

>Gulp和Webpack的区别：
>
>* 使用Gulp， 是基于 task 任务的；
>
>* 使用Webpack， 是基于整个项目进行构建的；

## 三、webpack的使用

### **1.webpack的安装配置**

* 安装node.js，在控制台使用`node -v`，`npm -v`，查看是否安装成功。

* 安装VS Code，安装中文插件，安装view in brower插件，安装Vetur和Vue 2 Snippets插件，点击`工具栏->查看->终端`可以使用命令提示符。

* webpack安装的两种方式：
  
  * 运行`npm i webpack -g`全局安装webpack，这样就能在全局使用webpack的命令
  
  * 初始化命令`npm init`后在项目根目录中运行`npm i webpack --save-dev`安装到项目依赖中

[宇宙最强vscode教程（基础篇）](https://segmentfault.com/a/1190000017949680)

### **2.项目的基本目录结构**

> dist --发布的产品级的内容
>
> >bundle.js --webpack打包后生成的文件

> node_modules --存储npm下载的项目依赖的js文件

> src  --源码文件
> > -css
> >  -images
> >  -js
> >  index.html
> >  main.js --项目js文件的入口

> package.json --初始化npm时生成的文件
> webpack.config.js --配置文件，通过node的模块操作，向外暴露一个配置对象

### **3.webpack打包项目**

由于 ES6的代码太高级，浏览器解析不了，所以执行到`main.js`文件下`import $ from 'jquery'`这一行会报错。

**两种解决方法：**

**3.1 使用webpack命令打包main.js文件**

```js
// 1.先运行`webpack 入口文件路径 输出文件路径`对`main.js`进行处理，webpack默认会把这种高级的语法转换为低级的浏览器能识别的语法
webpack src/js/main.js dist/bundle.js

// 2.然后在html中引用bundle.js文件
<script src = "../dist/bundle.js" ></script>
```

**3.2 使用webpack的配置文件简化打包时候的命令**

```js
// 1. 在项目根目录中新建`webpack.config.js`

// 2. 由于运行webpack命令的时候，webpack需要指定入口文件和输出文件的路径，所以，我们需要在`webpack.config.js`中配置这两个路径

// webpack.config.js文件下
// 导入处理路径的模块
var path = require('path');

// 导出一个配置对象，将来webpack在启动的时候，会默认来查找webpack.config.js，并读取这个文件中导出的配置对象，来进行打包处理
module.exports = {
	// 项目入口文件
    entry: path.resolve(__dirname, 'src/js/main.js'), 
    // 配置输出选项
    output: { 
    	// 配置输出的路径
        path: path.resolve(__dirname, 'dist'), 
        // 配置输出的文件名
        filename: 'bundle.js' 
    }
}

// 3.使用的时候只需要使用`webpack`命令
```

### **4.webpack-dev-server热部署**

由于每次重新修改代码之后，都需要手动运行webpack打包的命令，比较麻烦，所以使用webpack-dev-server来实现代码实时打包编译，当修改代码之后，会自动进行打包构建。

**两种热部署配置方式：**

**4.1 第一种：**

```
// 1. 运行`npm i webpack-dev-server -D`把这个工具安装到项目的本地开发依赖

// 2. 由于我们是在项目中本地安装的webpack-dev-server，所以无法把它当作脚本命令在powershell终端中直接运行；只有那些安装到全局（命令中带有`-g`）的工具，才能在终端中正常执行。
//在package.json文件中寻找`scripts`节点，新增指令`"dev": "webpack-dev-server"`。

// 3.运行一次指令 `npm run dev`，可以使项目是以本地服务器加端口号的形式运行，而不是以文件（view in brower）的形式。

// 4.访问webpack-dev-server启动的`http://localhost:8080/`网站，发现是一个文件夹的面板，需要点击到src目录下，才能打开我们的index首页，此时引用不到bundle.js文件，需要修改index.html中script的src属性为:`<script src="../bundle.js"></script>`

// 5.为了能在访问`http://localhost:8080/`的时候直接访问到index首页，可以使用`"dev": "webpack-dev-server --contentBase src"`指令来修改dev指令，指定启动的根目录。
```

* **注意：**
  * webpack-dev-server 这个工具，如果想要正常运行，要求在本地项目中必须安装 webpack（虽然安装了全局webpack，还是要安装本地的）。
  * webpack-dev-server 帮我们打包生成的 bundle.js 文件，并没有存放到 实际的物理磁盘上，而是直接托管到 电脑的内存中，所以我们在项目根目录中，根本找不到这个打包好的bundle.js（我们可以认为webpack-dev-server 把打包好的 文件，以一种虚拟的形式托管到了项目的根目录中，虽然我们看不到它，但是可以认为， 和 dist  src   node_modules  平级，有一个看不见的文件，叫做 bundle.js）。把bundle.js放在内存中的好处是：由于需要实时打包编译，所以放在内存中读取速度会非常快。

**4.2 第二种：**

```
// 1. 运行`npm i webpack-dev-server -D`把这个工具安装到项目的本地开发依赖

// 2.在webpack.config.js文件下

const path=require('path')
const webpack=require('webpack')

module.exports={
	entry:....,
	output:....,
	devServer:{
		open:ture.
		port:3000,
		contentBase:'src',
		hot:ture
	},
	plugins:[
		new webpack.HotModuleReplacementPlugin()
	]
}

// 3.index.html中script的src属性为:
`<script src="../bundle.js"></script>`
```

### **5.webpack-dev-server的常用命令参数**

 ```
"dev": "webpack-dev-server --open --port 3000 --contenBase src --hot"`
--open 表示运行后自动打开项目
--port 3000 在端口号3000启动
--contenBase src 启动src目录下的项目
--hot 可以扩展补丁更新，而不是更新整个js和json文件，提高加载速度；并且可以异步刷新页面。
 ```
[webpack-dev-server常用参数配置选项](https://www.9xkd.com/course/2799418208.html)

### **6.html-webpack-plugin插件配置启动页面**

由于使用`--contentBase`指令的过程比较繁琐，需要指定启动的目录，同时还需要修改index.html中script标签的src属性，所以推荐大家使用`html-webpack-plugin`插件配置启动页面.

```
// 1.运行`cnpm i html-webpack-plugin --save-dev`安装到开发依赖

// 2.修改`webpack.config.js`配置文件如下：
// 导入处理路径的模块
var path = require('path');
// 导入自动生成HTMl文件的插件
var htmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
    entry: path.resolve(__dirname, 'src/js/main.js'), // 项目入口文件
    output: { 
    // 配置输出选项
        path: path.resolve(__dirname, 'dist'), // 配置输出的路径
        filename: 'bundle.js' // 配置输出的文件名
    },
    plugins:[ 
        // 添加plugins节点配置插件            
        new htmlWebpackPlugin({
            // 根据指定的模板页面生成一个在内存中的页面
            template:path.resolve(__dirname, 'src/index.html'),//模板路径
            // 自动生成的HTML文件的名称
            filename:'index.html'            
		})
    ]
}

// 3.修改`package.json`中`script`节点中的dev指令如下：
"dev": "webpack-dev-server"

// 4.可以将硬盘上的index.html中script标签注释掉，因为`html-webpack-plugin`插件会自动把bundle.js注入到内存中的index.html页面中，系统都是默认先进入内存的index.html，若生成的内存中的html文件不是index.html这个名字，则会先进入实际的index.html页面。
```

### **7.webpack打包css文件**

webpack只可以打包js的文件，想要打包css文件需要安装第三方loader加载器

```
// 1.运行`cnpm i style-loader css-loader --save-dev`

// 2.修改`webpack.config.js`这个配置文件
// 用来配置第三方loader模块的
module: { 
		// 文件的匹配规则
        rules: [ 
        	//处理css文件的规则
        	{ test: /\.css$/, use: ['style-loader', 'css-loader'] }
        ]
}
```
* **注意：**
  * test中使用正则表达式匹配符合规则的css文件。

  * `use`表示使用哪些模块来处理`test`所匹配到的文件；`use`中相关loader模块的调用顺序是从后向前调用的。

### **8.webpack打包less文件**

```
// 1.运行`cnpm i less-loader less -D`

// 2.修改`webpack.config.js`这个配置文件：
{ test: /\.less$/, use: ['style-loader', 'css-loader', 'less-loader'] },
```
### **9.webpack打包sass文件**

```
// 1.运行`cnpm i sass-loader node-sass --save-dev`

// 2.在`webpack.config.js`中添加处理sass文件的loader模块：
{ test: /\.scss$/, use: ['style-loader', 'css-loader', 'sass-loader'] }
```
### **10.webpack处理css中的路径**

默认webpack无法处理css中文字或图片的地址，需要下载loader

```
// 1.运行`cnpm i url-loader file-loader --save-dev`

// 2.在`webpack.config.js`中添加处理url路径的loader模块：
{ test: /\.(png|jpg|gif)$/, use: 'url-loader' }

// 3.可以在url-loader后加参数`limit`指定进行base64编码的图片大小，只有小于指定字节（byte）的图片才会进行base64编码；参数name表示展现在前端的图片的名字，`[hash:8]`表示8位hash值，`[name]`表示原本图片的名字，`[ext]`表示原本图片的后缀名。
{ test: /\.(png|jpg|gif)$/, use: 'url-loader?limit=43960&name=[hash:8]-[name].[ext]' },

// 4.匹配符合规则的字体文件
{test:/\.(ttf|svg|eot)/,use:'url-loader'}
```
### **11.webpack解析.vue文件的配置**

默认webpack无法打包.vue文件，需要安装相关的loader才可以让js文件引用vue文件，并且通过render将组件渲染到页面中。

```vue
## 在webpack中配置.vue组件页面的解析

// 1. 运行`cnpm i vue -S`将vue安装为运行依赖；

// 2. 运行`cnpm i vue-loader vue-template-compiler -D`将解析转换vue的包安装为开发依赖；

// 3. 运行`cnpm i style-loader css-loader -D`将解析转换CSS的包安装为开发依赖，因为.vue文件中会写CSS样式；

// 4. 在`webpack.config.js`中，添加如下`module`规则：

module: {
    rules: [
      { test: /\.css$/, use: ['style-loader', 'css-loader'] },
      { test: /\.vue$/, use: 'vue-loader' }
    ]
  }

// 5. 创建`App.vue`组件页面：
<template>
    <div>
        <h1>这是APP组件 - {{msg}}</h1>
        <h3>我是h3</h3>
    </div>
</template>
<style>
    h1 {
    	color: red;
	}
</style>
<script>
    export default {
        data() {
            return {
                msg: 'OK'
            }
        }
    }
</script>

// 6. 创建`main.js`入口文件：

import Vue from 'vue'
// 导入App组件
import App from './components/App.vue'
var vm = new Vue({
	el: '#app',
	//这里直接使用render属性
	render: c => c(App),
	/*
	//使用私有组件的方式会在网页上报运行异常
	components :{
		App:{
			...
		}
	}
	*/
});
```

* **注意：**

  * 如果想快捷开发的话可以学习vue脚手架

  * 当vue-loader大于15以上的版本，需要使用VueLoaderPlugin伴生函数
  

```
//在webpack.config/js中
const VueLoaderPlugin = require('vue-loader/lib/plugin')

//在plugins属性中添加伴生函数
{
    plugins:[
        new VueLoaderPlugin()
    ]
}
```


[什么是Vue-CLI?](https://blog.csdn.net/weixin_40408910/article/details/81279305)

[Vue 爬坑之路（一）—— 使用 vue-cli 搭建项目](https://blog.csdn.net/WiseWrong/article/details/55212684)

[Vue2全家桶之一：vue-cli（vue脚手架）超详细教程](https://www.jianshu.com/p/32beaca25c0d)
    

## 四、babel的使用

在webpack中，默认只能处理一部分es6的语法，一些高级的语法或者es7的语法，webpack处理不了，需要第三方的loader，来帮助实现webpack将高级的语法转化为低级的语法，并会把结果交给webpack打包到bundle.js，这时候就需要通过babel将高级语法转化为低级的语法。

```js
// 1.运行`cnpm i babel-core babel-loader babel-plugin-transform-runtime --save-dev`安装babel的相关loader包，也就是转换器包

// 2.运行`cnpm i babel-preset-es2015 babel-preset-stage-0 --save-dev`安装babel转换的语法

// 3.在`webpack.config.js`中添加相关loader模块，把`node_modules`文件夹添加到排除项：
{ test: /\.js$/, use: 'babel-loader', exclude: /node_modules/ }

// 4. 在项目根目录中添加`.babelrc`文件，并修改这个配置文件如下：
{
    "presets":["es2015", "stage-0"],
    "plugins":["transform-runtime"]
}
```
* **注意：**
  * 一定要把node_modules文件夹添加到排除项，在转换的时候排除node_modules目录，让loader只转换开发者写的js文件，不要转换别人写好的js，因为这样会消耗cpu，同时使打包速度变慢。
  * `.babelrc`文件需要符合json规范：不能写注释，字符串必须要用双引号。
  * 语法插件`babel-preset-es2015`可以替换为`babel-preset-env`，它包含了所有的ES相关的语法

[babel-preset-env：你需要的唯一Babel插件](https://segmentfault.com/p/1210000008466178)

[Runtime transform 运行时编译es6](https://segmentfault.com/a/1190000009065987)

## 五、webpack中导入vue包

在webpack中使用`import Vue from 'vue'`导入的Vue构造函数使用的vue包功能不完整，只提供了runtime-vue的方式，并没有通过网页中所使用的方式，所以需要自己重新导入指定的vue包。

三种解决方法：

* 在node_modules中根据包名找到的Vue文件夹下的package.json文件，查找main属性，修改配置，使用自己想要使用的vue依赖文件。
* 修改导入路径为`import Vue from '../node_modules/vue/dist/vue.js'`
* 修改webpack.config.js文件，

```
//在main.js中
`import Vue from vue`

// 在webpack.config.js中添加属性resolve 
resolve : {
	alias : {
		//设置vue导入的包的路径
		"vue" : "vue/dist/vue.js"
	}
}
```

## 六、在.vue文件中使用组件、路由和样式

### **1.组件**

在.vue的组件中，通过 script 标签来定义组件的行为，需要使用ES6中提供的export default方式，导出一个vue实例对象。

```vue
//1.模板中可以使用export default和export中定义的数据和行为
<template>
    <div>
    	<h1>这是APP组件 - {{name}}</h1>
    </div>
</template>

var info = {
	name : "zs",
	age : 20
}

//2.export用于对外输出本模块（一个文件可以理解为一个模块）变量的接口，其他JS文件就可以通过import命令加载这个模块（文件）
export default info

export export1={name : "export1"}
export export2={name : "export2"}

//3.在main.js中导入

//导入整个组件
import test from './test.js'
//导入在.vue中导出的成员，as是为导出的成员取别名
import info,{export1 as newExport,export2} from './test.js'
console.log(test)
console.log(info)
```

* **注意：**
  * 在一个模块中，只允许export default 向外暴露一次，export可以暴露多次。
  * 可以同时使用export default和export 向外暴露成员。
  * export只可以使用`{}`（按需导出）的形式导出。

**关于import和export疑惑的解答**：

[export ，export default 和 import 区别 以及用法](https://www.cnblogs.com/xiaotanke/p/7448383.html)

[Vue中import from的来源：省略后缀与加载文件夹](https://blog.csdn.net/fyyyr/article/details/83657828)

[vue中import与@import的区别](https://blog.csdn.net/qifengle315/article/details/89231957)

### **2.路由**

**2.1 在vue组件页面中，集成vue-router路由模块**

```vue
//1. main.js中导入路由模块：
import VueRouter from 'vue-router'
//安装路由模块：
Vue.use(VueRouter);
//导入需要展示的组件:
import login from './components/account/login.vue'
import register from './components/account/register.vue'

//4. 创建路由对象，这里也可以使用子路由，不过要导入子路由组件:
var router = new VueRouter({
  routes: [
    { path: '/', redirect: '/login' },
    { path: '/login', component: login },
    { path: '/register', component: register }
  ]
});

//5. 将路由对象，挂载到 Vue 实例上:
var vm = new Vue({
	el: '#app',
	//这里使用了render函数会覆盖掉指定的容器，需要将路由的标签写在app组件中：
    render(c) {
		return c(App);
	},
	router // 将路由对象，挂载到 Vue 实例上
});

//6. 改造App.vue组件，在 template 中，添加`router-link`和`router-view`：
    <router-link to="/login">登录</router-link>
    <router-link to="/register">注册</router-link>
    <router-view></router-view>
```

**2.2 抽离路由模块**

```vue
//另建一个router.js，将main.js中的路由模块抽离出来
// 1.导入路由模块：
import VueRouter from 'vue-router'
Vue.use(VueRouter);
import login from './components/account/login.vue'
import register from './components/account/register.vue'

// 创建路由对象，这里也可以使用子路由，不过要导入子路由组件:
var router = new VueRouter({
  routes: [
    { path: '/', redirect: '/login' },
    { path: '/login', component: login },
    { path: '/register', component: register }
  ]
});

export default router

// 2.在main.js中导入
import router from "./router.js"
```

### **3.样式**

普通的style标签只支持普通的css样式，想要支持scss或less，需要设置lang；scope表示样式只在组件内生效，不是全局生效，只要style标签实在.vue组件中定义的，那么推荐使用。

```vue
//在组件文件中
//scoped是通过css样式的属性选择器实现的
<style lang="less" scoped>
...
<style>
```

# Vue.js的扩展学习

## 一、箭头函数

### **1.箭头函数语法**

箭头函数表达式的语法比函数表达式更简洁，并且没有自己的this，arguments，super 和 new.target。这些函数表达式更适用于那些本来需要匿名函数的地方，并且它们不能用作构造函数。

```
(参数1, 参数2, …, 参数N) => { 函数声明 }

//相当于：(参数1, 参数2, …, 参数N) =>{ return 表达式; }
(参数1, 参数2, …, 参数N) => 表达式（单一）

// 没有参数的函数应该写成一对圆括号。
() => {函数声明}
```

### **2.箭头函数中的this**

箭头函数不会创建自己的this,它只会从自己的作用域链的上一层继承this（这也是箭头函数最常用的用法）。

```js
function Person(){
  this.age = 0;

  setInterval(() => {
	// 这个函数里面的this是person函数中的this
    this.age++; 
  }, 1000);
}

var p = new Person();
```

[箭头函数](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Functions/Arrow_functions)

## 二、回调函数

### **1. 回调函数是什么**

回调是一个函数，它作为参数传递给另一个函数，并在其父函数完成后执行。

> 在Javascript中，函数是第一类对象，这意味着函数可以像对象一样按照第一类管理被使用。既然函数实际上是对象：它们能被“存储”在变量中，能作为函数参数被传递，能在函数中被创建，能从函数中返回。

```js
function my() {
	alert("我是回调函数!");
}
//my函数是作为一个参数被回调(调用或者执行),
$('#btn').click(my);
```

[关于js中的回调函数callback，通俗易懂](https://www.cnblogs.com/moxiaowohuwei/p/8438236.html)

> 仔细看第一篇文章的这句话，可能会让你有更深的理解：
>
> 所以回调与同步、异步并没有直接的联系，回调只是一种实现方式，既可以有同步回调，也可以有异步回调，还可以有事件处理回调和延迟函数回调，

[JS回调函数（深入篇）](https://www.jianshu.com/p/962697f602a4)

[你真的了解回调?](https://www.jianshu.com/p/ed5d5e3ebd43)

### **2. 回调地狱是什么**

多功能需要连续调用，环环相扣依赖时，函数作为参数层层嵌套，使得异步JavaScript或使用回调的JavaScript很难直观地得到正确的结果。例如下面的代码：

```js
fs.readdir(source, function (err, files) {
  if (err) {
    console.log('Error finding files: ' + err)
  } else {
    files.forEach(function (filename, fileIndex) {
      console.log(filename)
      gm(source + filename).size(function (err, values) {
        if (err) {
          console.log('Error identifying file size: ' + err)
        } else {
          console.log(filename + ' : ' + values)
          aspect = (values.width / values.height)
          widths.forEach(function (width, widthIndex) {
            height = Math.round(width / aspect)
            console.log('resizing ' + filename + 'to ' + height + 'x' + height)
            this.resize(width, height).write(dest + 'w' + width + '_' + filename, function(err) {
              if (err) console.log('Error writing file: ' + err)
            })
          }.bind(this))
        }
      })
    })
  }
})
```

回调地狱是由于糟糕的编码习惯造成的。幸运的是，编写更好的代码并不困难！ 您只需遵循三条规则：

* 保持你的代码简短
* 模块化
* 处理每一个错误

[回调地狱](https://www.jianshu.com/p/39adf6ab8ad1)

## 三、Promise的概念

### **1.Promise是什么**

Promise 是一个对象，用于表示一个异步操作的最终状态（完成或失败），以及该异步操作的结果值。

### **2.Promise的语法**

```js
new Promise( function(resolve, reject) {...} /* executor */  );
```

**executor是带有 `resolve` 和 `reject` 两个参数的函数** 。Promise构造函数执行时立即调用`executor` 函数， `resolve` 和 `reject` 两个函数作为参数传递给`executor`（**executor 函数在Promise构造函数返回所建promise实例对象前被调用**）。**`resolve` 和 `reject` 函数被调用时，分别将promise的状态改为fulfilled（完成）或rejected（失败）**。executor 内部通常会执行一些异步操作，一旦异步操作执行完毕(可能成功/失败)，要么调用resolve函数来将promise状态改成fulfilled，要么调用`reject` 函数将promise的状态改为rejected。如果在executor函数中抛出一个错误，那么该promise 状态为rejected。executor函数的返回值被忽略。

>个人理解:
>
>实例Pormise对象的时候，在Promise构造函数返回promise实例之前，执行executor(resolve, reject)匿名函数，可以在executor()函数中执行resolve()函数，将promise实例改为fulfulled状态；也可以执行reject()函数，将promise实例改为rejected状态。

### **3.Promise的状态描述**

**`Promise` 对象是一个代理对象（代理一个值），被代理的值在Promise对象创建时可能是未知的**。它允许你为异步操作的成功和失败分别绑定相应的处理方法（handlers）。 这让异步方法可以像同步方法那样返回值，但并不是立即返回最终执行结果，而是一个能代表未来出现的结果的promise对象

一个 `Promise`有以下几种状态:

- pending: 初始状态，既不是成功，也不是失败状态。
- fulfille: 意味着操作成功完成。
- rejected: 意味着操作失败。

pending 状态的 Promise 对象可能会变为fulfilled 状态并传递一个值给相应的状态处理方法，也可能变为rejected状态并传递失败信息。当其中任一种情况出现时，Promise 对象的 `then` 方法绑定的处理方法（handlers ）就会被调用（then）方法包含两个参数：onfulfilled 和 onrejected，它们都是 Function 类型。**当Promise状态为fulfilled时，调用then()的onfulfilled()匿名函数，当Promise状态为rejected时，调用then()的onrejected()匿名函数，**所以在异步操作的完成和绑定处理方法之间不存在竞争。

因为 `Promise.prototype.then` 和  `Promise.prototype.catch` 方法返回promise 对象， 所以它们可以被链式调用。

![Promise链式调用](D:\JAVA\Git-repository\MyPostImages\vue\Promise链式调用.png)

### **4.Promise的属性和方法**

**4.1 Promise.length**

length属性，其值总是为 1 (构造器参数的数目)。

**4.2 Promise.prototype**

表示Promise构造器的原型，Promise实例继承自Promise.prototype，你可以在构造器的原型对象添加属性或方法到所有Promise实例上。

**4.2.1 Promise.prototype属性**

* **Promise.prototype.constructor**

  返回被创建的实例函数.  默认为 [`Promise`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise) 函数。

**4.2.2 Promise.prototype方法**

* **Promise.prototype.catch(onRejected)**

添加一个拒绝(rejection) 回调到当前 promise, 返回一个新的promise。当这个回调函数被调用，新promise将以它的返回值来决定，否则如果当前promise 进入fulfilled状态，则以当前promise的完成结果作为新promise的完成结果。

* **Promise.prototype.then(onFulfilled, onRejected)**

  添加解决(fulfillment)和拒绝(rejection)回调到当前 promise, 返回一个新的 promise, 将以回调的返回值来决定。当一个Promise完成（fulfilled）或者失败（rejected），返回函数将被异步调用（由当前的线程循环来调度完成）。具体的返回值依据以下规则返回：

  - 如果then中的回调函数返回一个值，那么then返回的Promise将会成为接受状态，并且将返回的值作为接受状态的回调函数的参数值。
  - 如果then中的回调函数没有返回值，那么then返回的Promise将会成为接受状态，并且该接受状态的回调函数的参数值为 undefined。
  - 如果then中的回调函数抛出一个错误，那么then返回的Promise将会成为拒绝状态，并且将抛出的错误作为拒绝状态的回调函数的参数值。
  - 如果then中的回调函数返回一个已经是接受状态的Promise，那么then返回的Promise也会成为接受状态，并且将那个Promise的接受状态的回调函数的参数值作为该被返回的Promise的接受状态回调函数的参数值。
  - 如果then中的回调函数返回一个已经是拒绝状态的Promise，那么then返回的Promise也会成为拒绝状态，并且将那个Promise的拒绝状态的回调函数的参数值作为该被返回的Promise的拒绝状态回调函数的参数值。
  - 如果then中的回调函数返回一个未定状态（pending）的Promise，那么then返回Promise的状态也是未定的，并且它的终态与那个Promise的终态相同；同时，它变为终态时调用的回调函数参数与那个Promise变为终态时的回调函数的参数是相同的。
  

**注意：**如果忽略针对某个状态的回调函数参数，或者提供非函数 (nonfunction) 参数，那么 `then` 方法将会丢失关于该状态的回调函数信息，但是并不会产生错误。如果调用 `then`的 `Promise` 的状态（fulfillment 或 rejection）发生改变，但是 `then` 中并没有关于这种状态的回调函数，那么 `then` 将创建一个没有经过回调函数处理的新 `Promise` 对象，这个新 `Promise` 只是简单地接受调用这个 `then` 的原 `Promise` 的终态作为它的终态。

>个人理解：
>
>也就是说then中没有参数或者没有回调函数的时候，那么这个then相当于就会不执行了，直接执行下一个then；调用 `then`的 `Promise` 的状态（fulfillment 或 rejection）发生改变，但是`then`中没有这种状态的回调函数（onFulfilled()或onRejected()匿名函数），那么 `then` 将创建一个没有经过回调函数处理的新 `Promise` 对象，这个新 `Promise` 只是简单地接受调用这个 `then` 的原来的 `Promise` 的终态作为它的终态。

```
var p = new Promise((resolve, reject) => {
    resolve('foo')
});

// 'bar' 不是函数，会在内部被替换为 (x) => x
p.then('bar').then((value) => {
    console.log(value) // 'foo'
})
```

* **Promise.prototype.finally(onFinally)**

添加一个事件处理回调于当前promise对象，并且在原promise对象解析完毕后，返回一个新的promise对象。回调会在当前promise运行完毕后被调用，无论当前promise的状态是完成(fulfilled)还是失败(rejected)。

**4.3 Promise.all(iterable)**

这个方法返回一个新的promise对象，**该promise对象在iterable参数对象里所有的promise对象都成功的时候才会触发成功**，一旦有任何一个iterable里面的promise对象失败则立即触发该promise对象的失败。这个新的promise对象在触发**成功**状态以后，**会把一个包含iterable里所有promise返回值的数组作为成功回调的返回值**，顺序跟iterable的顺序保持一致；如果这个新的promise对象触发了**失败**状态，**它会把iterable里第一个触发失败的promise对象的错误信息作为它的失败错误信息**。Promise.all方法常被用于处理多个promise对象的状态集合。

**4.4 Promise.race(iterable)**

当iterable参数里的任意一个**子promise被成功或失败后**，父promise马上也会用**子promise的成功返回值或失败详情**作为参数**调用父promise绑定的相应句柄**，并返回该promise对象。

**4.5 Promise.reject(reason)**

返回一个状态为失败的Promise对象，并将给定的失败信息传递给对应的处理方法。

**4.6 Promise.resolve(value)**

返回一个状态是由参数value决定的。如果该value是thenable(即，带有then方法的对象)，返回的Promise对象的最终状态由then方法执行决定；否则的话(该value为空，基本类型或者不带then方法的对象),返回的Promise对象状态为fulfilled，并且将该value传递给对应的then方法。通常而言，如果你不知道一个值是否是Promise对象，使用Promise.resolve(value) 来返回一个Promise对象,这样就能将该value以Promise对象形式使用。

[Promise](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise#创建Promise)

## 四、Promise的使用

### **1.创建Promise**

Promise对象是由关键字new及其构造函数来创建的。该构造函数会把一个叫做“处理器函数”（executor function）的函数作为它的参数。这个“处理器函数”接受两个函数resolve和reject作为其参数。当异步任务顺利完成且返回结果值时，会调用 `resolve` 函数；而当异步任务失败且返回失败原因（通常是一个错误对象）时，会调用`reject` 函数。

```js
const myFirstPromise = new Promise((resolve, reject) => {
  // 做一些异步操作，最终会调用下面两者之一:
  //
  // resolve(someValue); // fulfilled
  // 或
  // reject("failure reason"); // rejected
});

myFirstPromise.then(function(successMessage){
    //successMessage的值是上面调用resolve(...)方法传入的值.
    //successMessage参数不一定非要是字符串类型，这里只是举个例子
});
```

想要某个函数拥有promise功能，只需让其返回一个promise即可。

```js
function myAsyncFunction(url) {
  return new Promise((resolve, reject) => {
    const xhr = new XMLHttpRequest();
    xhr.open("GET", url);
    xhr.onload = () => resolve(xhr.responseText);
    xhr.onerror = () => reject(xhr.statusText);
    xhr.send();
  });
};
```

### **2.Promise链式调用**

连续执行两个或者多个异步操作是一个常见的需求，在上一个操作执行成功之后，开始下一个的操作，并带着上一步操作所返回的结果。链式调用有以下约定：

* 在本轮Javascript event loop（事件循环，相当于函数同步运行）运行完成之前，回调函数是不会被调用的。
* 通过then()添加的回调函数总会被调用，即便它是在异步操作完成之后才被添加的函数。
* 通过多次调用then()，可以添加多个回调函数，它们会按照插入顺序一个接一个独立执行。

**2.1 基本写法**

```
doSomething((resolve, reject) =>{
	resolve();
})
.then(result => doSomethingElse(result))
.then(newResult => doThirdThing(newResult))
.then(finalResult => {
  console.log(`Got the final result: ${finalResult}`);
})
.catch(failureCallback);
```

* **注意：**
  * then里的参数是可选的，`catch(failureCallback)` 是 `then(null, failureCallback)` 的缩略形式。
  * 一定要有返回值，否则，callback 将无法获取上一个 Promise 的结果（原理参考Promise.prototype.then的返回值规则）。(如果使用箭头函数，`() => x` 比 `() => { return x; }` 更简洁一些，但后一种保留 `return` 的写法才支持使用多个语句。）。

**2.2 Catch 的后续链式操作**

有可能会在一个回调失败之后继续使用链式操作，即 使用一个 `catch`，这对于在链式操作中抛出一个失败之后，再次进行新的操作很有用。

```js
new Promise((resolve, reject) => {
    console.log('初始化');
    resolve();
})
.then(() => {
    throw new Error('有哪里不对了');
        
    console.log('执行「这个」”');
})
.catch(() => {
    console.log('执行「那个」');
})
.then(() => {
    console.log('执行「这个」，无论前面发生了什么');
});

//执行结果
//初始化
//执行“那个”
//执行“这个”，无论前面发生了什么
```

### **3.错误传递**

通过捕获所有的错误，甚至抛出异常和程序错误，Promise 解决了回调地狱的基本缺陷。这对于构建异步操作的基础功能而言是很有必要的。

```js
doSomething((resolve, reject) =>{
	resolve();
})
.then(result => doSomethingElse(value))
.then(newResult => doThirdThing(newResult))
.then(finalResult => console.log(`Got the final result: ${finalResult}`))
.catch(failureCallback);
```

通常，一遇到异常抛出，Promise 链就会停下来，直接调用链式中的 `catch` 处理程序来继续当前执行。这看起来和以下的同步代码的执行很相似。

```js
try {
  let result = syncDoSomething();
  let newResult = syncDoSomethingElse(result);
  let finalResult = syncDoThirdThing(newResult);
  console.log(`Got the final result: ${finalResult}`);
} catch(error) {
  failureCallback(error);
}
```

### **4.Promise拒绝事件**

当 Promise 被拒绝时，会有下文所述的两个事件之一被派发到全局作用域（通常而言，就是window；如果是在 web worker 中使用的话，就是Worker或者其他 worker-based 接口）。这**两个事件**如下所示：

- rejectionhandled：当 Promise 被拒绝、并且在 `reject` 函数处理该 rejection 之后会派发此事件。
- unhandledrejection：当 Promise 被拒绝，但没有提供 `reject` 函数来处理该 rejection 时，会派发此事件。

以上两种情况中，PromiseRejectionEvent事件都有两个属性，一个是promise属性，该属性指向被驳回的 Promise，另一个是reason属性，该属性用来说明 Promise 被驳回的原因。

因此，我们可以通过以上事件为 Promise 失败时提供补偿处理，也有利于调试 Promise 相关的问题。在每一个上下文中，该处理都是全局的，因此不管源码如何，所有的错误都会在同一个handler中被捕捉处理。

一个特别有用的例子：当你使用Node.js时，有些依赖模块可能会有未被处理的 rejected promises，这些都会在运行时打印到控制台。你可以在自己的代码中捕捉这些信息，然后添加与unhandledrejection相应的 handler 来做分析和处理，或只是为了让你的输出更整洁。举例如下：

```
//使用window事件监听器监听unhandledrejection事件
window.addEventListener("unhandledrejection", event => {
  /* 你可以在这里添加一些代码，以便检查
     event.promise 中的 promise 和
     event.reason 中的 rejection 原因 */
  //调用event的preventDefault()方法是为了告诉JavaScript引擎当 promise被拒绝时不要执行默认操作，
  默认操作一般会包含把错误打印到控制台。
  event.preventDefault();
}, false);
```

* **注意：**在忽略这些事件之前，我们应该检查所有被拒绝的 Promise，来确认这不是代码中的 bug。

### **5.Promise方法的组合**应用

Promise.resolve()和Promise.reject()是手动创建一个已经resolve或者reject的Promise快捷方法。

Promise.all()和Promise.race()是并行运行异步操作的两个组合式工具。

我们可以发起并行操作，然后等多个操作全部结束后进行下一步操作，如下：

```js
Promise.all([func1(), func2(), func3()])
.then(([result1, result2, result3]) => { /* use result1, result2 and result3 */ });
```

可以使用一些聪明的 JavaScript 写法实现时序组合：

```js
//reduce是数组的方法，
//array.reduce(function(total, currentValue, currentIndex, arr), initialValue)
[func1, func2, func3].reduce((p, f) => p.then(f), Promise.resolve())
.then(result3 => { /* use result3 */ });
```

通常，递归调用一个由异步函数组成的数组时相当于一个 Promise 链：

```
Promise.resolve().then(func1).then(func2).then(func3);
```

也可以写成可复用的函数形式，这在函数式编程中极为普遍，传入的任一函数可以是异步或同步的，它们能被保证按顺序执行：

```js
const applyAsync = (acc,val) => acc.then(val);
const composeAsync = (...funcs) => x => funcs.reduce(applyAsync, Promise.resolve(x));

const transformData = composeAsync(func1, func2, func3);
const result3 = transformData(data);
```

### **6.时序**

为了避免意外，即使是一个已经变成 resolve 状态的 Promise，传递给 `then()` 的函数也总是会被异步调用，也就是说函数执行会延时：

```js
Promise.resolve().then(() => console.log(2));
console.log(1); // 1, 2
```

传递到 `then()` 中的函数被置入了一个微任务队列，而不是立即执行，这意味着它是在 JavaScript 事件队列的所有事件运行时结束了，事件队列被清空之后，才开始执行：

```js
1.let promise = new Promise(function(resolve, reject) {
  console.log('Promise');
  resolve();
});

2.promise.then(function() {
  console.log('resolved.');
});

3.console.log('Hi!');

const wait = ms => new Promise(resolve => setTimeout(resolve, ms));

4.wait().then(() => console.log(4));
5.Promise.resolve().then(() => console.log(2)).then(() => console.log(3));
6.console.log(1); 

//执行结果：
> "Promise"
> "Hi!"
> 1
> "resolved."
> 2
> 3
> 4
```

>个人理解：
>
>通过上面的代码，我猜测的执行顺序是：
>
>js是单线程，程序顺序执行，碰见 1 的时候构造函数放入js事件队列，碰见 2 的时候会将then()中的函数放入微任务队列（等待js事件队列中的函数全部运行结束，js事件队列被清空才执行微任务队列中的函数），继续往下执行碰见 3 的时候放入js事件队列 ，碰见 4 的时候会放入微任务队列，碰见 5 的时候也放入微任务队列，碰见 6 的时候放入js事件队列。然后按照先执行完js事件队列中的事件，再执行微任务队列中的函数顺序执行（队列先进先出：1》3》6》2》4》5）。
>
>先执行 4 再执行 5 ，但是 5 的输出在 4 之前的原因是：4 使用定时器当作异步函数，异步函数会交给浏览器内核（多线程）的其他线程（Http异步线程，定时器线程等）处理，执行后不会阻塞来等待执行结果，会继续执行下一个js的函数，输出 4 的结果，等 5 的结果返回后才输出 5 的结果。  

[深入理解JavaScript运行机制](https://blog.csdn.net/Amos_liu/article/details/53560434)

[从 Event Loop 谈 JavaScript 的执行机制](https://www.jianshu.com/p/c7a0dc9fccd3)

### **7.嵌套**

简便的 Promise 链式编程最好保持扁平化，不要嵌套 Promise，因为嵌套经常会是粗心导致的。

嵌套 Promise 是一种可以限制 `catch` 语句的作用域的控制结构写法。明确来说，嵌套的 `catch`仅捕捉在其之前同时还必须是其作用域的 failureres，而捕捉不到在其链式以外或者其嵌套域以外的 error。如果使用正确，那么可以实现高精度的错误修复。

```js
doSomethingCritical()
.then(result => doSomethingOptional()
  .then(optionalResult => doSomethingExtraNice(optionalResult))
  .catch(e => {console.log(e.message)})) // 即使有异常也会忽略，继续运行;(最后会输出)
.then(() => moreCriticalStuff())
.catch(e => console.log("Critical failure: " + e.message));// 没有输出
```

注意,有些代码步骤是嵌套的，而不是一个简单的纯链式，这些语句前与后都被括号 `()` 包裹着。

这个内部的 catch 语句仅能捕获到 `doSomethingOptional()` 和 `doSomethingExtraNice()`的失败，`之后就恢复到moreCriticalStuff()` 的运行。重要提醒：如果 `doSomethingCritical()` 失败，这个错误仅会被最后的（外部）`catch` 语句捕获到。

### **8.常见错误**

在编写 Promise 链时，需要注意以下示例中展示的几个错误：

```js
// 错误示例，包含 3 个问题！

doSomething().then(function(result) {
  doSomethingElse(result) // 没有返回 Promise 以及没有必要的嵌套 Promise
  .then(newResult => doThirdThing(newResult));
}).then(() => doFourthThing());
// 最后，是没有使用 catch 终止 Promise 调用链，可能导致没有捕获的异常
```

* 第一个错误是没有正确地将事物相连接。当我们创建新 Promise 但忘记返回它时，会发生这种情况。因此，链条被打破，或者更确切地说，我们有两个独立的链条竞争（同时在执行两个异步而非一个一个的执行）。这意味着 `doFourthThing()` 不会等待 `doSomethingElse()` 或 `doThirdThing()` 完成，并且将与它们并行运行，可能是无意的。单独的链也有单独的错误处理，导致未捕获的错误。

* 第二个错误是不必要地嵌套，实现第一个错误。嵌套还限制了内部错误处理程序的范围，如果是非预期的，可能会导致未捕获的错误。其中一个变体是 [Promise 构造函数反模式](https://stackoverflow.com/questions/23803743/what-is-the-explicit-promise-construction-antipattern-and-how-do-i-avoid-it)，它结合了 Promise 构造函数的多余使用和嵌套。

* 第三个错误是忘记用 `catch` 终止链。这导致在大多数浏览器中不能终止的 Promise 链里的 rejection。

一个好的经验法则是总是返回或终止 Promise 链，并且一旦你得到一个新的 Promise，返回它。下面是修改后的平面化的代码：

```js
doSomething()
.then(function(result) {
  return doSomethingElse(result);
})
.then(newResult => doThirdThing(newResult))
.then(() => doFourthThing());
.catch(error => console.log(error));
```

上述代码的写法就是具有适当错误处理的简单明确的链式写法。

使用 [async/await](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/async_function) 可以解决以上大多数错误，使用 `async/await` 时，最常见的错误就是忘记了 `await` 关键字。

[使用 Promise](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Using_promises)

## 五、async/await

### **1.asyn/await 是什么**

在async/await之前，我们有三种方式写异步代码：嵌套回调；以Promise为主的链式回调；使用Generators。但是，这三种写起来都不够优雅，ES7做了优化改进，async/await应运而生，async/await相比较Promise 对象then 函数的嵌套，与 Generator 执行的繁琐，async/wait 可以让你轻松写出同步风格的代码同时又拥有异步机制，更加简洁，逻辑更加清晰。

### **2.async/await的目的**

async/await 的目的是简化使用多个 promise 时的同步行为，并对一组 Promises 执行某些操作。正如 Promises 类似于结构化回调，async/await 更像结合了generators和 promises。

### **2.asyn/await的特点**

* async/await更加语义化，async 是“异步”的简写，async function 用于申明一个 function 是异步的； await，可以认为是async wait的简写， 用于等待一个异步方法执行完成；

* async/await是一个用同步思维解决异步问题的方案（等结果出来之后，代码才会继续往下执行）

* 可以通过多层 async function 的同步写法代替传统的callback嵌套

### **3.async function 的语法**

```
async function name([param[, param[, ... param]]]) { statements }
```

| 参数       | 说明               |
| ---------- | ------------------ |
| name       | 函数名称           |
| param      | 要传递给函数的参数 |
| statements | 函数体语句         |

返回值：返回的 Promise 对象会运行执行 resolve() 异步函数的返回结果，或者运行拒绝(reject)——如果异步函数抛出异常的话。

![async返回值](D:\JAVA\Git-repository\MyPostImages\vue\async返回值.png)

### **4.async function 的作用**

- 自动将常规函数转换成Promise，返回值也是一个Promise对象
- 只有async函数内部的异步操作执行完，才会执行then方法指定的回调函数
- 异步函数内部可以使用await

### **5.await的语法**

异步函数可以包含 await 指令，该指令会暂停异步函数的执行，并等待Promise执行完成。若 Promise 正常处理(fulfilled)，其回调的 resolve 函数参数作为 await 表达式的值，继续执行 async function；若 Promise 处理异常(rejected)，await 表达式会把 Promise 的异常原因抛出，需要 try catch 捕捉异常。另外，如果 await 操作符后的表达式的值不是一个 Promise，则返回该值本身。然后继续执行异步函数，并返回结果。

* **注意：**
  * await 关键字只在异步函数内有效。如果你在异步函数外使用它，会抛出语法错误。
  * 当异步函数暂停时，它调用的函数会继续执行(收到异步函数返回的隐式Promise)。

### **6.async/await 和 Promise运行比较**

```js
var resolveAfter2Seconds = function() {
  console.log("starting slow promise");
  return new Promise(resolve => {
    setTimeout(function() {
      resolve("slow");
      console.log("slow promise is done");
    }, 2000);
  });
};

var resolveAfter1Second = function() {
  console.log("starting fast promise");
  return new Promise(resolve => {
    setTimeout(function() {
      resolve("fast");
      console.log("fast promise is done");
    }, 1000);
  });
};

var sequentialStart = async function() {
  console.log('==SEQUENTIAL START==');

  // 1. Execution gets here almost instantly
  const slow = await resolveAfter2Seconds();
  console.log(slow); // 2. this runs 2 seconds after 1.

  const fast = await resolveAfter1Second();
  console.log(fast); // 3. this runs 3 seconds after 1.
}
/**
> "==SEQUENTIAL START=="
> "starting slow promise"
> "slow promise is done"
> "slow"
> "starting fast promise"
> "fast promise is done"
> "fast"

执行到第一个 await，会去执行第一个 Pdomise，计时器停留 2 秒，等待第一个计时器时间结束，console.log(slow)的时候输出 resolve 的参数，然后执行第二个 Promise。
**/

var concurrentStart = async function() {
  console.log('==CONCURRENT START with await==');
  const slow = resolveAfter2Seconds(); // starts timer immediately
  const fast = resolveAfter1Second(); // starts timer immediately

  // 1. Execution gets here almost instantly
  console.log(await slow); // 2. this runs 2 seconds after 1.
  console.log(await fast); // 3. this runs 2 seconds after 1., immediately after 2., since fast is already resolved
}
/**
> "==CONCURRENT START with await=="
> "starting slow promise"
> "starting fast promise"
> "fast promise is done"
> "slow promise is done"
> "slow"
> "fast"

按照顺序调用两个Promise，输出前两句，第二个 Promise 的 resolve 函数计数器时间短，先输出，最后再顺序执行 await 输出 resolve 的参数
**/

var concurrentPromise = function() {
  console.log('==CONCURRENT START with Promise.all==');
  return Promise.all([resolveAfter2Seconds(), resolveAfter1Second()]).then((messages) => {
    console.log(messages[0]); // slow
    console.log(messages[1]); // fast
  });
}
/**
> "==CONCURRENT START with Promise.all=="
> "starting slow promise"
> "starting fast promise"
> "fast promise is done"
> "slow promise is done"
> "slow"
> "fast"

并行执行两个 Promise，输出前两句，计时器结束后输出中间两句，最后再顺序输出结果
**/

var parallel = async function() {
  console.log('==PARALLEL with await Promise.all==');
  
  // Start 2 "jobs" in parallel and wait for both of them to complete
  await Promise.all([
      (async()=>console.log(await resolveAfter2Seconds()))(),
      (async()=>console.log(await resolveAfter1Second()))()
  ]);
}
/**
> "==PARALLEL with await Promise.all=="
> "starting slow promise"
> "starting fast promise"
> "fast promise is done"
> "fast"
> "slow promise is done"
> "slow"

并发执行两个 async function 异步函数，输出前两句，第一个计时器结束后输出中间两句，第二个计时器结束后输出后两句
**/

// This function does not handle errors. See warning below!
var parallelPromise = function() {
  console.log('==PARALLEL with Promise.then==');
  resolveAfter2Seconds().then((message)=>console.log(message));
  resolveAfter1Second().then((message)=>console.log(message));
}
/**
> "==PARALLEL with Promise.then=="
> "starting slow promise"
> "starting fast promise"
> "fast promise is done"
> "fast"
> "slow promise is done"
> "slow"

按照顺序执行两个 Promise，输出前两句，Promise 中的计时器分别执行，第一个计时器结束后输出中间两句，第二个计时器结束后输出后两句
**/

sequentialStart(); // after 2 seconds, logs "slow", then after 1 more second, "fast"

// wait above to finish
setTimeout(concurrentStart, 4000); // after 2 seconds, logs "slow" and then "fast"

// wait again
setTimeout(concurrentPromise, 7000); // same as concurrentStart

// wait again
setTimeout(parallel, 10000); // truly parallel: after 1 second, logs "fast", then after 1 more second, "slow"

// wait again
setTimeout(parallelPromise, 13000); // same as parallel
```

### **7.错误处理**

在async函数里，无论是Promise reject的数据还是逻辑报错，都会被默默吞掉,所以最好把await放入try{}catch{}中，catch能够捕捉到Promise对象rejected的数据或者抛出的异常.

```js
function timeout(ms) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {reject('error')}, ms);  //reject模拟出错，返回error
  });
}
async function asyncPrint(ms) {
  try {
     console.log('start');
     await timeout(ms);  //这里返回了错误
     console.log('end');  //所以这句代码不会被执行了
  } catch(err) {
     console.log(err); //这里捕捉到错误error
  }
}

asyncPrint(1000);
```

如果不用try/catch的话，也可以像下面这样处理错误（因为async函数执行后返回一个promise）

```js
function timeout(ms) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {reject('error')}, ms);  //reject模拟出错，返回error
  });
}
async function asyncPrint(ms) {
  console.log('start');
  await timeout(ms)
  console.log('end');  //这句代码不会被执行了
}
asyncPrint(1000).catch(err => {
    console.log(err)； // 从这里捕捉到错误
});
```

如果你不想让错误中断后面代码的执行，可以提前截留住错误，像下面

```js
function timeout(ms) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
        reject('error')
    }, ms);  //reject模拟出错，返回error
  });
}
async function asyncPrint(ms) {
  console.log('start');
  await timeout(ms).catch(err => {  // 注意要用catch
	console.log(err) 
  })
  console.log('end');  //这句代码会被执行
}

asyncPrint(1000);
```

### **8.使用 async 函数重写 promise 链**

```js
function getProcessedData(url) {
  return downloadData(url) // 返回一个 promise 对象
    .catch(e => {
      return downloadFallbackData(url)  // 返回一个 promise 对象
    })
    .then(v => {
      return processDataInWorker(v); // 返回一个 promise 对象
    });
}
```

可以重写为单个`async`函数：

```js
async function getProcessedData(url) {
  let v;
  try {
    v = await downloadData(url); 
  } catch (e) {
    v = await downloadFallbackData(url);
  }
  return processDataInWorker(v);
}
```

注意，在上述示例中，`return` 语句中没有 `await` 操作符，因为 `async function` 的返回值将被隐式地传递给 `Promise.resolve`。

### **9.return await  与 return  的比较**

返回值隐式的传递给 Promise.resolve，并不意味着`return await promiseValue;和return promiseValue;`在功能上相同。

看下下面重写的上面代码，在`processDataInWorker`抛出异常时返回了null：

```
async function getProcessedData(url) {
  let v;
  try {
    v = await downloadData(url);
  } catch(e) {
    v = await downloadFallbackData(url);
  }
  try {
    return await processDataInWorker(v); // 注意 `return await` 和单独 `return` 的比较
  } catch (e) {
    return null;
  }
}
```

简单地写上`return processDataInworker(v); 将导致在 processDataInWorker(v);`出错时function返回值为 Promise 而不是`返回null。`return foo;`和`return await foo;`，有一些细微的差异:`return foo;`不管`foo`是promise还是rejects都将会直接返回`foo。相反地，`如果`foo 是一个 Promise ，`return await foo;`将等待`foo`执行(resolve)或拒绝(reject)，如果是拒绝，将会在返回前抛出异常。

> 个人理解：
>
> return 就是直接返回这个东西，return await 就是执行Promise对象后再返回，如果成功，则返回结果；如果失败就抛异常。

[async function](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/async_function)

[浅谈async/await](https://www.jianshu.com/p/1e75bd387aa0)

## 六、其他的相关扩展学习

### **1. js变量的三种定义方式**

[js声明变量的三种方式及作用域](https://www.cnblogs.com/Marydon20170307/p/7505964.html)

### **2. 开发时可能用到的**

[前后端分离下载文件](https://blog.csdn.net/ycd500756/article/details/87907525)

[RGB颜色查询](https://en.m.fontke.com/tool/rgb/)

## 参考：

[随笔分类 - VUE](https://www.cnblogs.com/leeSmall/category/1242643.html)

