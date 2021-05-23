---
title: '使用Vue-cli搭建Vue项目'
date: 2018-12-10 12:20:20
tags: [JavaScript,Vue]
categories: [编程,前端,Vue]
---

# 使用Vue-cli搭建Vue项目
## 安装Vue-cli
在安装Vue-cli之前需要先安装`node`环境以及`npm`包管理工具

安装好之后可以通过以下命令查看版本
```batch
:: 查看node版本
node -v

:: 查看npm版本
npm -v
```

全局安装Vue-cli
```batch
npm install @vue/cli -g
:: 或者
yarn global add @vue/cli
```

安装后使用`vue --version`来查看版本

运行以下命令在**当前**文件夹下创建一个项目
```batch
:: 命令行创建项目
vue create <projectName>

:: 使用可视化项目创建工具
vue ui
```

创建完成后，进入到刚才创建的文件夹下，运行`npm run serve`即可启动项目

<!-- more -->

## 项目结构
```batch
<projectName>
├─node_modules
├─public
│ └─index.html
├─src
│ ├─assets
│ ├─components
│ ├─App.vue
│ └─main.js
└package.json
```

- `package.json` 用于记录项目依赖的库
- `assets` 用于存放静态资源，比如图片
- `components` 用于存放组件
- `App.vue` 根组件
- `main.js` 项目入口脚本

## 写一个Hello World
写在vue文件中
```html
<!-- 模板: html结构 -->
<!-- template中有且只能有一个根元素 -->
<template>
  <div id="app">
    <h1>{{msg}}</h1>
  </div>
</template>

<!-- 行为: 逻辑处理 -->
<script>
export default {
  name: 'app',
  data() {
    return {msg: 'Hello World'}
  }
}
</script>

<!-- 样式: 样式表 -->
<!-- 加入scope关键词代表样式仅作用于当前组件中 -->
<!-- <style scope></style> -->
<style>
#app {
  text-align: center;
  color: #2c3e50;
}
</style>
```

写在main.js中
```javascript
// 引入Vue
import Vue from 'vue'
// 引入根组件
import App from './App.vue'

// 阻止Vue在启动时生成生产提示
Vue.config.productionTip = false

// 实例化Vue对象
new Vue({
  render: function(h) { return h(App) }
}).$mount('#app')

// 也可使用以下方式
new Vue({
  el: '#app',
  template: '<App/>',
  components: { App }
})
```

## 创建并使用自己的组件
在/src/components目录下创建`.vue`文件，注册之后在`.vue`文件中可以使用`<compName/>`或`<compName></compName>`的形式来使用组件

### 全局注册组件
在`main.js`中注册组件
```javascript
// 引入组件
import compName from './components/compName'

// 全局注册组件
Vue.component("compName", compName)
```

### 局部注册组件
在要注册组件的`.vue`文件中
```html
<script>
// 引入组件
import compName from './components/compName.vue'

export default {
  name: 'app',
  data() {
    return {msg: 'Hello World'}
  },
  //局部注册组件
  components: {
    'compName': compName
  }
}
</script>
```

## 组件间的传值(props)
### 父组件向子组件传值
父组件中给子组件绑定属性值
```html
<template>
  <div id="app">
    <compName v-bind:msg="msg">
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'

export default {
  name: 'app',
  data() {
    return {
      msg: "Hello World"
    }
  },
  components: {
    HelloWorld
  }
}
</script>
```

在子组件中获取
```html
<script>
export default {
  name: 'HelloWorld',
  props: {
    msg: String,
    default: 'Def'  // 默认值
  }
}
</script>

<!-- 或者 -->

<script>
export default {
  name: 'HelloWorld',
  props: {
    msg: {
      type: String,
      required: true  //required 为true 表示这个参数必须被传入
    }
  }
}
</script>
```

### 子组件向父组件传值
子组件中注册事件
```javascript
methods:{
  foo() {
    this.$emit(ctmEvent,trsValue)
  }
}
```
`ctmEvent`即为注册的事件名称  
`trsValue`即为向父组件传递的值

父组件中绑定事件
```html
<compName v-on:ctmEvent="fun($event)" />
```
父组件中绑定事件后，当子组件触发事件后，会执行对应的函数，使用`$event` 来获取到子组件传递过来的值

定义函数来处理子组件传递过来的值，这里的`para`对应的是`$event`
```javascript
fun(para) {
  this.title = para
}
```

## 组件的生命周期(钩子函数)

```javascript
export default {
  name: 'app',
  data() { ... },
  methods: { ... },

  // 组件实例化之前
  // 数据观测(data observer)和event/watcher 事件配置之前被调用
  beforeCreate() {},

  // 组件实例化完毕, 但页面还没显示
  // 实例创建完成后立即被调用, 数据观测(data observer)和event/watcher 已配置, $el属性不可见
  created() {},

  // 组件挂载之前, 页面依然未显示, (但虚拟Dom 已经配置  (存疑))
  // 在挂载前被调用, 相关render 函数首次被调用
  beforeMount() {},

  // 组件挂载后, 此方法执行完成后, 页面显示
  // el 被新创建的vm.$el替换并挂载到实例上之后调用
  mounted() {},

  // 组件更新前调用(此时仍然可以访问到更新前的Dom)
  beforeUpdate() {},

  // 组件更新后调用(Dom 更新后调用)
  updated() {},

  // keep-alive 组件激活时调用
  activated() {},

  // keep-alive 组件停用时调用
  deactivated() {}

  // 组件销毁前
  beforeDestroy() {},

  // 组件销毁
  destroyed() {},

  // 捕获来自子组件的错误时调用
  // 可以返回false 以阻止错误继续向上传播
  errorCaptured(Error, Component, InfoString) {
    return false
  }
}
```