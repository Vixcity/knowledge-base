## 整理的一些 Vue3 知识点

看完你就基本可以上手搞开发了，本文适合`Vue`初学者，或者`Vue2`迁移者，当然还是建议`Vue3`官网完全过一遍

不适合精通原理，源码的大佬们。 

## 先推荐两个vscode插件

### Volar

首先推荐`Volar`，使用`vscode`开发`Vue`项目的小伙伴肯定都认识`Vetur`这个神级插件，有了它可以让我们得开发如鱼得水

那么`Volar`可以理解为`Vue3`版本的`Vetur`，代码高亮，语法提示，基本上`Vetur`有的它都有

#### 特色功能

当然作为新的插件出山，肯定有它独有的功能。

##### 多个根节点编辑器不会报错

`Vue3`是允许我们有多个根节点的，但是我们如果使用Vetur就会报错，不会影响运行，但是看起来就很烦

所以当我们转向`Volar`那么就不会出现这个问题了

![多个根节点](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202110131114131.webp)

#### 编辑器分隔

即便`Vue`的组件化开发，可以将单文件的代码长度大幅缩短，但还是动辄几百行甚是上千行

那么我们切换`template`，`script`和`style`的时候就要频繁上下翻，虽然有的插件可以直接定位到`css`，但是你回不去啊！所以这个功能简直是太人性化了。

安装完`Volar`以后，打开一个`.vue`文件，看`vscode`的右上角，有这么一个图标，点一下

![图片](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202110131114070.webp)

它就会自动给你分隔成三个页面，分别对应`template`，`script`和`style`，这样就太舒服了有没有

![图片](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202110131115723.webp)

### Vue 3 Snippets

推荐的第二个插件叫做`Vue 3 Snippets`，同样的，他也有自己的`Vue2`版本

它是干什么的呢，可以看一下下面这张图，我只输入了`“v3”`，它有很多提示，我们就先选择`v3computed`，选中回车即可

![图片](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202110131116170.webp)

然后它就给自动给我们写了如下代码

![图片](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202110131116715.webp)

是不是超级省事，摸鱼的时间又增加了！

还有更多有趣的使用方式，小伙伴们自行探索吧

## 创建Vue3项目

那么正式开始学习我们的`Vue3`，先从创建项目开始。

### 使用 vue-cli 创建

输入下面的命令然后选择配置项进行安装即可，这里注意`vue-cli`的版本一定要在`4.5.0`以上

```bash
// 安装或者升级  
npm install -g @vue/cli  
//查看版本 保证 vue cli 版本在 4.5.0 以上  
vue --version  
// 创建项目  
vue create my-project  
//然后根据提示一步一步傻瓜式操作就行了  
...  
```

### 使用 Vite 创建

都说`Vue3.0`和`Vite2`更配，各种优化，各种快，但都不属于本文的内容，本文的目的我们只需要知道它特别好用，怎么用就行了。我这里是多选择了`TS`，每行都有注释，一目了然。

```bash
// 初始化viete项目  
npm init vite-app <project-name>  
// 进入项目文件夹  
cd <project-name>  
// 安装依赖  
npm install  
//启动项目  
npm run dev  
复制代码  
```

创建完以后我们先来看看入口文件`main.ts`

```js
// 引入createApp函数,创建对应的应用,产生应用的实例对象  
import { createApp } from 'vue';  
// 引入app组件(所有组件的父级组件)  
import App from './App.vue';  
// 创建app应用返回对应的实例对象,调用mount方法进行挂载  挂载到#app节点上去  
createApp(App).mount('#app');  
```

然后看看根组件`app.vue`

```html
//Vue2组件中的html模板中必须要有一对根标签,Vue3组件的html模板中可以没有根标签  
<template>  
  <img alt="Vue logo" src="./assets/logo.png">  
  <!-- 使用子级组件 -->  
  <HelloWorld msg="Welcome to Your Vue.js + TypeScript App" />  
</template>  
  
<script lang="ts">  
// 这里可以书写TS代码  
  
// defineComponent函数,目的是定义一个组件 内部可以传入一个配置对象  
import { defineComponent } from 'vue';  
//引入子级组件  
import HelloWorld from './components/HelloWorld.vue';  
  
// 暴露出去一个定义好的组件  
export default defineComponent({  
  // 当前组件的名字  
  name: 'App',  
  // 注册组件  
  components: {  
    // 注册一个子级组件  
    HelloWorld,  
  },  
});  
</script>  
```

## Composition API

接下来到了重头戏，`Vue3`的招牌特性，**Composition API**

关于`Composition API`这里有大佬做的动画演示，极力推荐

[那个忙了一夜的Vue3动画很好，就是太短了](http://mp.weixin.qq.com/s?__biz=MzAxODE2MjM1MA==&mid=2651567543&idx=1&sn=e4ff9cbf4e17f0e856afac4eb03b44f8&chksm=80256276b752eb60deea09c1d34c1c97ab60d9e9c31132979877d6cdbf0a6b78aada1ffe25c7&scene=21#wechat_redirect)

`Composition API`可以更方便的抽取共通逻辑，但是不要过于在意逻辑代码复用，以功能提取代码也是一种思路

顺便提一句，`Vue3`兼容大部分`Vue2`语法，所以在`Vue3`中书写`Vue2`语法是没有问题的（废除的除外），但是既然我们已经升级`Vue3`了，不建议混合使用，除非一些大型特殊项目需要兼容两个版本

### setup

`setup`是组合`Composition API`中的入口函数，也是第一个要使用的函数

`setup`只在初始化时执行一次，所有的`Composition API`函数都在此使用

```js
setup() {  
  console.log('我执行了') //我执行了  
},  
```

可以通过`console.log`看到`setup`是在`beforeCreate`生命周期之前执行的(只执行一次)

 ```js
beforeCreate() {  
    console.log('beforeCreate执行了');  
  },  
  setup() {  
    console.log('setup执行了');  
    return {};  
  },  
  //setup执行了  
  //beforeCreate执行了  
```

由此可以推断出`setup`执行的时候,组件对象还没有创建,组件实例对象`this`还不可用,此时`this`是`undefined`, 不能通过`this`来访问`data/computed/methods/props`

返回对象中的属性会与`data`函数返回对象的属性合并成为组件对象的属性，返回对象中的方法会与`methods`中的方法合并成功组件对象的方法，如果有重名, `setup`优先

因为在`setup`中`this`不可用，`methods`中可以访问`setup`提供的属性和方法, 但在`setup`方法中不能访问`data`和`methods`里的内容，所以还是不建议混合使用

`setup`函数如果返回对象, 对象中的 **属性** 或 **方法** , **模板** 中可以直接使用

```js
//templete  
<div>{{number}}</div>  
  
//JS  
setup() {  
  const number = 18;  
  return {  
    number,  
  };  
},  
```

**注意：**`setup`不能是一个`async`函数: 因为返回值不再是`return`的对象, 而是`promise`, 模板中就不可以使用`return`中返回对象的数据了

#### setup的参数(props,context)

**props**: 是一个对象,里面有父级组件向子级组件传递的数据,并且是在子级组件中使用`props`接收到的所有的属性

**context**：上下文对象，可以通过`es6`语法解构 `setup(props, {attrs, slots, emit})`

-   **attrs**: 获取当前组件标签上所有没有通过`props`接收的属性的对象, 相当于 `this.$attrs`
    
-   **slots**: 包含所有传入的插槽内容的对象, 相当于 `this.$slots`
    
-   **emit**: 用来分发自定义事件的函数, 相当于 `this.$emit`
    

演示`attrs`和`props`

```html
//父组件  
<template>  
  <child :msg="msg" msg2='哈哈哈' />  
</template>  
<script lang='ts'>  
import { defineComponent, ref } from 'vue';  
// 引入子组件  
import Child from './components/Child.vue';  
export default defineComponent({  
  name: 'App',  
  components: {  
    Child,  
  },  
  setup() {  
    const msg = ref('hello,vue3');  
    return {  
      msg,  
    };  
  },  
});  
</script>  
  
//子组件  
<template>  
  <h2>子组件</h2>  
  <h3>msg:{{ msg }}</h3>  
</template>  
  
<script lang='ts'>  
import { defineComponent } from 'vue';  
export default defineComponent({  
  name: 'Child',  
  props: ['msg'],  
  setup(props, {attrs, slots, emit}) {  
    console.log('props:', props);//msg: "hello,vue3"  
    console.log('attrs:', attrs);//msg2: "哈哈哈"  
    return {};  
  },  
});  
</script>  
```

演示`emit`

```html
//父组件  
<template>  
  <child @show="show" />  
</template>  
  
<script lang='ts'>  
  setup() {  
    const show = () => {  
      console.log('name:', 'hzw');  
    };  
    return {  
      show,  
    };  
  },  
</script>  
  
//子组件  
<template>  
  <button @click='emitFn'>事件分发</button>  
</template>  
<script lang='ts'>  
import { defineComponent } from 'vue';  
  
export default defineComponent({  
  name: 'Child',  
  setup(props, { emit }) {  
    const emitFn = () => {  
      emit('show');  
    };  
    return {  
      emitFn,  
    };  
  },  
});  
</script>  
```

### ref

#### 作用

定义一个响应式的数据(一般用来定义一个基本类型的响应式数据`Undefined`、`Null`、`Boolean`、`Number`和`String`)

#### 语法

```js
const xxx = ref(initValue):  
```

**注意**：`script`中操作数据需要使用`xxx.value`的形式，而模板中不需要添加`.value`

用一个例子来演示：实现一个按钮,点击可以增加数字

```html
<template>  
  <div>{{count}}</div>  
  <button @click='updateCount'>增加</button>  
</template>  
```

**在Vue2中**

```js
data() {  
  return {  
    conunt: 0,  
  };  
},  
methods: {  
  updateCount() {  
    this.conunt++;  
  },  
},  
```

**在Vue3中**

 ```js
 setup() {  
    // ref用于定义一个响应式的数据，返回的是一个Ref对象，对象中有一个value属性  
    //如果需要对数据进行操作，需要使用该Ref对象的value属性  
    const count = ref(0);  
    function updateCount() {  
      count.value++;  
    }  
    return {  
      count,  
      updateCount,  
    };  
  },  
```

在`Vue2`中我们通过`this.$refs`来获取`dom`节点，`Vue3`中我们通过`ref`来获取节点

首先需要在标签上添加`ref='xxx'`，然后再`setup`中定义一个初始值为`null`的`ref`类型,名字要和标签的`ref`属性一致

```js
const xxx = ref(null)  
```

**注意**：一定要在`setup`的`return`中返回，不然会报错。

还是用一个例子来演示:让输入框自动获取焦点

```html
<template>  
  <h2>App</h2>  
  <input type="text">---  
  <input type="text" ref="inputRef">  
</template>  
  
<script lang="ts">  
import { onMounted, ref } from 'vue'  
/*   
ref获取元素: 利用ref函数获取组件中的标签元素  
功能需求: 让输入框自动获取焦点  
*/  
export default {  
  setup() {  
    const inputRef = ref<HTMLElement|null>(null)  
  
    onMounted(() => {  
      inputRef.value && inputRef.value.focus()  
    })  
  
    return {  
      inputRef  
    }  
  },  
}  
</script>  
```

### reactive

#### 语法

```js
const proxy = reactive(obj)  
```

#### 作用

定义多个数据的响应式，接收一个普通对象然后返回该普通对象的响应式代理器对象`(Proxy)`，响应式转换是“深层的”：会影响对象内部所有嵌套的属性,所有的数据都是响应式的。

#### 代码演示

```html
<template>  
  <h3>姓名:{{user.name}}</h3>  
  <h3>年龄:{{user.age}}</h3>  
  <h3>wife:{{user.wife}}</h3>  
  <button @click="updateUser">更新</button>  
</template>  
  
 setup() {  
    const user = reactive({  
      name: 'hzw',  
      age: 18,  
      wife: {  
        name: 'xioaohong',  
        age: 18,  
        books: ['红宝书', '设计模式', '算法与数据结构'],  
      },  
    });  
    const updateUser = () => {  
      user.name = '小红';  
      user.age += 2;  
      user.wife.books[0] = '金瓶梅';  
    };  
    return {  
      user,  
      updateUser,  
    };  
  },  
```

### computed函数:

与`Vue2`中的`computed`配置功能一致，返回的是一个`ref`类型的对象

计算属性的函数中如果只传入一个回调函数 表示的是`get`操作

```js
import { computed } from 'vue';  
const user = reactive({  
  firstName: '韩',  
  lastName: '志伟',  
});  
const fullName1 = computed(() => {  
  return user.firstName + user.lastName;  
});  
return {  
  user,  
  fullName1,  
};  
```

计算属性的函数中可以传入一个对象,可以包含`set`和`get`函数,进行读取和修改的操作

```js
const fullName2 = computed({  
  get() {  
    return user.firstName + '_' + user.lastName;  
  },  
  set(val: string) {  
    const names = val.split('_');  
    user.firstName = names[0];  
    user.lastName = names[1];  
  },  
});  
return {  
  user,  
  fullName2,  
};  
```

### watch函数:

与`Vue2`中的`watch`配置功能一致,

-   参数1:要监听的数据
    
-   参数2:回调函数
    
-   参数3:配置
    

#### 作用

监视指定的一个或多个响应式数据, 一旦数据变化, 就自动执行监视回调

默认初始时不执行回调, 但可以通过配置`immediate`为`true`, 来指定初始时立即执行第一次

通过配置`deep`为`true`, 来指定深度监视

```js
import { watch, ref } from 'vue';  
const user = reactive({  
  firstName: '韩',  
  lastName: '志伟',  
});  
const fullName3 = ref('');  
watch(  
  user,  
  ({ firstName, lastName }) => {  
    fullName3.value = firstName + '_' + lastName;  
  },  
  { immediate: true, deep: true }  
);  
return {  
  user,  
  fullName3,  
};  
```

`watch`监听多个数据,使用数组

`watch`监听非响应式数据的时候需要使用回调函数的形式

```js
watch([()=>user.firstName,()=>user.lastName,fullName3],()=>{console.log('我执行了')})  
```

### watchEffect函数:

#### 作用

监视数据发生变化时执行回调，不用直接指定要监视的数据, 回调函数中使用的哪些响应式数据就监视哪些响应式数据，默认初始时就会执行第一次, 从而可以收集需要监视的数据。

```js
import { watchEffect, ref } from 'vue';  
const user = reactive({  
  firstName: '韩',  
  lastName: '志伟',  
});  
const fullName4 = ref('');  
watchEffect(() => {  
  fullName4.value = user.firstName + '_' + user.lastName;  
});  
return {  
  user,  
  fullName4,  
};  
watchEffect可以实现计算属性set方法  
watchEffect(() => {  
    const names = fullName3.value.split('_');  
    user.firstName = names[0];  
    user.lastName = names[1];  
});  
```

### 生命周期对比:

![图片](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202110131128078.webp)

微信截图_20210623104108.png

**注意**:`3.0`中的生命周期钩子要比`2.X`中相同生命周期的钩子要快

`Composition API`还新增了以下调试钩子函数：但是不怎么常用

-   onRenderTracked
    
-   onRenderTriggered
    

#### 代码演示

```js
setup() {  
  
  onBeforeMount(() => {  
    console.log('--onBeforeMount')  
  })  
  
  onMounted(() => {  
    console.log('--onMounted')  
  })  
  
  onBeforeUpdate(() => {  
    console.log('--onBeforeUpdate')  
  })  
  
  onUpdated(() => {  
    console.log('--onUpdated')  
  })  
  
  onBeforeUnmount(() => {  
    console.log('--onBeforeUnmount')  
  })  
  
  onUnmounted(() => {  
    console.log('--onUnmounted')  
  })  
}  
```

### toRefs

#### 作用

把一个响应式对象转换成普通对象，该普通对象的每个属性都是一个 `ref`

#### 应用

我们使用`reactive`创建的对象，如果想在模板中使用，就必须得使用`xxx.xxx`的形式,如果大量用到的话还是很麻烦的,但是使用`es6`解构以后,会失去响应式,那么`toRefs`的作用就体现在这,，利用`toRefs`可以将一个响应式 `reactive` 对象的所有原始属性转换为响应式的`ref`属性。当然小伙伴们可以自行开发更多应用场景。

#### 代码演示

```html
<template>  
  <div>  
    name:{{name}}  
  </div>  
</template>  
  
<script lang='ts'>  
import { defineComponent, reactive, toRefs } from 'vue';  
  
export default defineComponent({  
  name: '',  
  setup() {  
    const state = reactive({  
      name: 'hzw',  
    });  
    const state2 = toRefs(state);  
    setInterval(() => {  
      state.name += '===';  
    }, 1000);  
    return {  
      //通过toRefs返回的对象,解构出来的属性也是响应式的  
      ...state2,  
    };  
  },  
});  
</script>  
 ```

### provide 与 inject

#### 作用

实现跨层级组件(祖孙)间通信

#### 代码演示

父组件

```html
<template>  
  <h1>父组件</h1>  
  <p>当前颜色: {{color}}</p>  
  <button @click="color='red'">红</button>  
  <button @click="color='yellow'">黄</button>  
  <button @click="color='blue'">蓝</button>  
  <hr>  
  <Son />  
</template>  
<script lang="ts">  
import { provide, ref } from 'vue'  
import Son from './Son.vue'  
export default {  
  name: 'ProvideInject',  
  components: {  
    Son  
  },  
  setup() {  
    const color = ref('red')  
    provide('color', color)  
    return {  
      color  
    }  
  }  
}  
</script>  
```

子组件

```html
<template>  
  <div>  
    <h2>子组件</h2>  
    <hr>  
    <GrandSon />  
  </div>  
</template>  
  
<script lang="ts">  
import GrandSon from './GrandSon.vue'  
export default {  
  components: {  
    GrandSon  
  },  
}  
</script>  
```

孙子组件

```html
<template>  
  <h3 :style="{color}">孙子组件: {{color}}</h3>  
</template>  
  
<script lang="ts">  
import { inject } from 'vue'  
export default {  
  setup() {  
    const color = inject('color')  
    return {  
      color  
    }  
  }  
}  
</script>  
```

## 其他特性

### Teleport(瞬移)

#### 作用

`Teleport` 提供了一种干净的方法, 让组件的`html`在父组件界面外的特定标签(很可能是`body`)下插入显示 换句话说就是可以把 **子组件** 或者 **dom节点** 插入到任何你想插入到的地方去。

#### 语法

使用`to`属性 引号内使用选择器

 ```html
<teleport to="body">  
  </teleport>  
```

#### 代码演示

```html
//父组件  
  
<template>  
  <div class="father">  
    <h2>App</h2>  
    <modal-button></modal-button>  
  </div>  
</template>  
  
<script lang="ts">  
import ModalButton from './components/ModalButton.vue'  
export default {  
  setup() {  
    return {}  
  },  
  components: {  
    ModalButton,  
  },  
}  
</script>  
  
  
//子组件  
<template>  
  <div class="son">  
    <button @click="modalOpen = true">  
      点我打开对话框  
    </button>  
  
    <teleport to="body">  
      <div v-if="modalOpen"  
           class="looklook">  
        看看我出现在了哪里  
        <button @click="modalOpen = false">  
          Close  
        </button>  
      </div>  
    </teleport>  
  </div>  
</template>  
  
<script>  
import { ref } from 'vue'  
export default {  
  name: 'modal-button',  
  setup() {  
    const modalOpen = ref(false)  
    return {  
      modalOpen,  
    }  
  },  
}  
</script>  
```

可以看到在子组件中的`looklook`元素跑到了`body`下面,而之前的位置默认出现了两行注释

![图片](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202110131129811.webp)

### Suspense(不确定的)

#### 作用

它们允许我们的应用程序在等待异步组件时渲染一些后备内容，可以让我们创建一个平滑的用户体验

#### 语法

 ```html
<Suspense>  
    <template v-slot:default>  
      <!-- 异步组件 -->  
      <AsyncComp />  
    </template>  
  
    <template v-slot:fallback>  
      <!-- 后备内容 -->  
      <h1>LOADING...</h1>  
    </template>  
  </Suspense>  
```

`vue3`中引入异步组件的方式

```js
const AsyncComp = defineAsyncComponent(() => import('./AsyncComp.vue'))  
```

#### 代码演示

父组件

```
<template>  
  <Suspense>  
     <!-- v-slot:defaul可以简写成#defaul -->  
    <template v-slot:default>  
      <AsyncComp/>  
    </template>  
  
    <template v-slot:fallback>  
      <h1>LOADING...</h1>  
    </template>  
  </Suspense>  
</template>  
  
<script lang="ts">  
import { defineAsyncComponent } from 'vue'  
const AsyncComp = defineAsyncComponent(() => import('./AsyncComp.vue'))  
export default {  
  setup() {  
    return {  
    }  
  },  
  
  components: {  
    AsyncComp,  
  }  
}  
</script>  
```

子组件

```html
<template>  
  <h2>AsyncComp22</h2>  
  <p>{{msg}}</p>  
</template>  
  
<script lang="ts">  
export default {  
  name: 'AsyncComp',  
  setup () {  
     return new Promise((resolve, reject) => {  
       setTimeout(() => {  
         resolve({  
           msg: 'abc'  
         })  
       }, 2000)  
     })  
  }  
}  
</script>  
```

通过下图可以看到在异步组件加载出来之前,显示的是`fallback`中的内容

![图片](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202110131131493.gif)

### 响应式数据的判断

#### 作用

-   **isRef**: 检查一个值是否为一个 `ref` 对象
    
-   **isReactive**: 检查一个对象是否是由 `reactive` 创建的响应式代理
    
-   **isReadonly**: 检查一个对象是否是由 `readonly` 创建的只读代理
    
-   **isProxy**: 检查一个对象是否是由 `reactive` 或者 `readonly` 方法创建的代理
    

#### 代码演示

```js
setup() {  
    const state1 = ref(1);  
    console.log('isref:', isRef(state1));//isref: true  
    const state2 = reactive({});  
    console.log('isReactive:', isReactive(state2));//isReactive: true  
    const state3 = readonly({});  
    console.log('isReadonly:', isReadonly(state3));//isReadonly: true  
    const state4 = reactive({});  
    console.log('isProxy:', isProxy(state2));//isProxy: true  
    console.log('isProxy:', isProxy(state4));//isProxy: true  
    return {};  
  },  
```

### 其他不常用特性

还有很多很多不常用的新特性，我在日常开发中是没有用到的，很多都是用来做优化的，感兴趣的小伙伴们自行去官网查看，或者大佬们可以介绍一下应用场景。

-   shallowReactive
    
-   shallowRef
    
-   readonly
    
-   shallowReadonly
    
-   markRaw
    
-   customRef
    
-   ...
    

## 语法糖

虽然`Composition API`用起来已经非常方便了，但是我们还是有很烦的地方，比如

-   组件引入了还要注册
    
-   属性和方法都要在`setup`函数中返回，有的时候仅一个`return`就十几行甚至几十行
    
-   ...
    
-   不想写啊怎么办
    

好办，`Vue3`官方提供了`script setup`语法糖

只需要在`script`标签中添加`setup`，组件只需引入不用注册，属性和方法也不用返回，`setup`函数也不需要，甚至`export default`都不用写了，不仅是数据，计算属性和方法，甚至是自定义指令也可以在我们的`template`中自动获得。

但是这么过瘾的语法糖，还是稍微添加了一点点心智负担，因为没有了`setup`函数，那么`props`，`emit`，`attrs`怎么获取呢，就要介绍一下新的语法了。

`setup script`语法糖提供了三个新的`API`来供我们使用：`defineProps`、`defineEmit`和`useContext`

-   **defineProps** 用来接收父组件传来的值`props`。
    
-   **defineEmit** 用来声明触发的事件表。
    
-   **useContext** 用来获取组件上下文`context`。
    

### 代码演示

父组件

```html
<template>  
  <div>  
    <h2>我是父组件！</h2>  
    <Child msg="hello"  
           @child-click="handleClick" />  
  </div>  
</template>  
  
<script setup>  
import Child from './components/Child.vue'  
  
const handleClick = (ctx) => {  
  console.log(ctx)  
}  
</script>  
```

子组件

```html
<template>  
  <span @click="sonClick">msg: {{ props.msg }}</span>  
</template>  
  
<script setup>  
import { useContext, defineProps, defineEmit } from 'vue'  
  
const emit = defineEmit(['child-click'])  
const ctx = useContext()  
const props = defineProps({  
  msg: String,  
})  
  
const sonClick = () => {  
  emit('child-click', ctx)  
}  
</script>  
```

我们点击一下子组件![图片](https://mmbiz.qpic.cn/mmbiz_png/zPh0erYjkib3WKpiaGhyrvJfOO1gIDEcRdsEqXwEpRtrZ6Aq9l1HjKpo38prOUBictgYv6Dpqia98fRUFQes3SODDQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)可以看到`context`被打印了出来，其中的`attrs`、`emit`、`slots`、`expose`属性和方法依然可以使用。`props`也可以输出在页面上，事件也成功派发。

## 其他知识点

接下来介绍一下我使用`Vue3`过程中遇到的问题或者小技巧，不全面，想起什么就写什么吧

### script setup语法糖请注意

如果在父组件中通过`ref='xxx'`的方法来获取子组件实例，子组件使用了`script setup`语法糖,那么子组件的数据需要用expose的方式导出，否则会因为获取不到数据而报错。

#### 代码演示

父组件

```html
<template>  
  <div>  
    <h2>我是父组件！</h2>  
    <Child ref='son' />  
  </div>  
</template>  
  
<script setup>  
import Child from './components/Child.vue'  
import { ref } from 'vue'  
const son = ref(null)  
console.log('🚀🚀~ son:', son)  
</script>  
复制代码  
`

子组件先不使用语法糖

`<template>  
  <div>  
    我是子组件{{msg}}  
  </div>  
</template>  
  
<script >  
import { ref } from 'vue'  
  
export default {  
  setup() {  
    const msg = ref('hello')  
    return {  
      msg,  
    }  
  },  
}  
```

可以看到是可以获取到我们在子组件中定义的`msg`属性的

![图片](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202110131132694.webp)
现在把子组件换成`script setup`语法糖再来试一试

```html
<template>  
  <div>  
    我是子组件{{msg}}  
  </div>  
</template>  
  
<script setup>  
import { ref } from 'vue'  
const msg = ref('hello')  
</script>  
 ```

可以看到现在是获取不到子组件定义的`msg`属性的

![图片](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202110131132506.webp)

我们可以看看尤大大怎么说

![图片](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202110131132564.webp)

> 补充自评论区 **用户2150817567886** setup语法糖定义的组件默认情况下是不对外开发内部调用的，它需要用expose()函数来定义哪些数据允许对外开放，具体内容参考[1]

### Emit派发事件可以对参数进行验证

父组件

```html
<template>  
  <div>  
    <h2>我是父组件！</h2>  
    <Child @sonClick='sonClick' />  
  </div>  
</template>  
  
<script setup>  
import Child from './components/Child.vue'  
import { ref } from 'vue'  
const sonClick = (value) => {  
  console.log(value)  
}  
</script>  
```

子组件

```html 
  <template>  
  <div>  
    我是子组件{{ msg }}  
  </div>  
  <button @click="handleClick(1)">我是按钮1</button>  
  <button @click="handleClick(2)">我是按钮2</button>  
</template>  
  
<script>  
import { ref } from 'vue'  
export default {  
  name: '',  
  emits: {  
    sonClick: (value) => {  
      if (value === 1) {  
        return true  
      } else {  
        return false  
      }  
    },  
  },  
  setup(props, { emit }) {  
    const msg = ref('hello')  
    const handleClick = (value) => {  
      emit('sonClick', value)  
    }  
    return {  
      msg,  
      handleClick,  
    }  
  },  
}  
</script>  
```

我们分别点一下按钮1和按钮2，可以看到当我们点了按钮2的时候，控制台会发出警告，但是程序会继续执行，还没想到什么适合的应用场景，但是要知道这个知识点，小伙伴们可以在这搞事情

![图片](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202110131331066.webp)

### 跨组件通讯mitt.js

`Vue2`中怎么实现跨组件通讯呢,很多人第一想法就是`event bus`

但是`Vue3`移除了`$on`,`$once`,`$off`导致不能使用这个方法

但是`Vue`官方给大家推荐了`mitt.js`,它的原理就是`event bus`

#### 代码演示

先安装

```bash
npm i mitt -s  
```

然后封装一个`hook`

```js
//mitt.js  
import mitt from 'mitt'  
const emitter = mitt();  
  
export default emitter;  
```

父组件

```html
<template>  
  <div>  
    <h2>我是父组件！</h2>  
    <Child1 />  
    <Child2 />  
  
  </div>  
</template>  
  
<script setup>  
import Child1 from './components/Child1.vue'  
import Child2 from './components/Child2.vue'  
</script>  
```

子组件1

```html
<template>  
  <div>  
    我是子组件1  
    <h1>{{msg}}</h1>  
  </div>  
</template>  
  
<script>  
import { ref, onUnmounted } from 'vue'  
import emitter from '../mitt'  
export default {  
  name: '',  
  
  setup() {  
    //初始化  
    const msg = ref('hello')  
    const changeMsg = () => {  
      msg.value = 'world'  
    }  
    // 监听事件,更新数据  
    emitter.on('change-msg', changeMsg)  
    // 显式卸载  
    onUnmounted(() => {  
      emitter.off('change-msg', changeMsg)  
    })  
    return {  
      msg,  
      changeMsg,  
    }  
  },  
}  
</script>  
```

子组件2

```html
<template>  
  <div>  
    我是子组件2  
  </div>  
  <button @click='changeMsg'>点击修改msg</button>  
</template>  
  
<script>  
import { ref } from 'vue'  
import emitter from '../mitt'  
  
export default {  
  name: '',  
  
  setup() {  
    const changeMsg = () => {  
      emitter.emit('change-msg')  
    }  
    return {  
      changeMsg,  
    }  
  },  
}  
</script>  
```

演示

![图片](https://cdn.jsdelivr.net/gh/Vixcity/FigureBed/img/202110131333889.gif)

### 自定义指令

先看看`Vue2`自定义指令的钩子

-   **bind**：当指令绑定在对应元素时触发。只会触发一次。
    
-   **inserted**：当对应元素被插入到 `DOM` 的父元素时触发。
    
-   **update**：当元素更新时，这个钩子会被触发（此时元素的后代元素还没有触发更新）。
    
-   **componentUpdated**：当整个组件（包括子组件）完成更新后，这个钩子触发。
    
-   **unbind**：当指令被从元素上移除时，这个钩子会被触发。也只触发一次。
    

在 `Vue3` 中，官方为了更有助于代码的可读性和风格统一，把自定义指令的钩子名称改的更像是组件生命周期，尽管他们是两回事

-   **bind** => **beforeMount**
    
-   **inserted** => **mounted**
    
-   **beforeUpdate**: **新的钩子，会在元素自身更新前触发**
    
-   **update** => **移除**！
    
-   **componentUpdated** => **updated**
    
-   **beforeUnmount**: **新的钩子，当元素自身被卸载前触发**
    
-   **unbind** => **unmounted**
    

### 过渡动画

这个没有什么大的改动，只是修改了两个`class`名字，正是因为没有什么大的改动，导致我曾经在这里栽了大跟头，写完了怎么都不对，后来查官网才知道。

以下是直接引用 **官网的原文**

> -   **v-enter-from**：定义进入过渡的开始状态。在元素被插入之前生效，在元素被插入之后的下一帧移除。
>     
> -   **v-enter-active**：定义进入过渡生效时的状态。在整个进入过渡的阶段中应用，在元素被插入之前生效，在过渡/动画完成之后移除。这个类可以被用来定义进入过渡的过程时间，延迟和曲线函数。
>     
> -   **v-enter-to**：定义进入过渡的结束状态。在元素被插入之后下一帧生效 (与此同时 v-enter-from 被移除)，在过渡/动画完成之后移除。
>     
> -   **v-leave-from**：定义离开过渡的开始状态。在离开过渡被触发时立刻生效，下一帧被移除。
>     
> -   **v-leave-active**：定义离开过渡生效时的状态。在整个离开过渡的阶段中应用，在离开过渡被触发时立刻生效，在过渡/动画完成之后移除。这个类可以被用来定义离开过渡的过程时间，延迟和曲线函数。
>     
> -   **v-leave-to**：离开过渡的结束状态。在离开过渡被触发之后下一帧生效 (与此同时 v-leave-from 被删除)，在过渡/动画完成之后移除。
>     

特别注意的是`v-enter`改成了`v-enter-form`，`v-leave`改成了`v-leave-from`。

### 其他小知识

> `Vue3`移除了`filter`

> 获取组件实例方法`getCurrentInstance()`

这个方法可以获取到当前组件的实例，相当于`Vue2`中的`this`最后，毕竟是个人总结，难免会出现纰漏和错误，期待各路大神的补充和纠正。

原文作者的开源项目:

https://juejin.cn/post/6963945204965441550"

#### 参考资料

[1]

https://github.com/vuejs/rfcs/pull/210

this.$refs.xxx 如果在普通的DOM元素当中使用，那么指向的就是DOM元素，如果在子组件当中使用，指向的就是组件实例，不是响应式数据