---
title: 'Vue Router的安装与使用'
date: 2019-01-25 00:10:10
tags: [JavaScript,Vue.js]
categories: [Vue.js]
---

# Vue Router 的基本用法
## 安装Vue路由
```batch
vue add @vue/router

:: 或者
npm install vue-router -S

yarn add vue-router
```

## 引入路由
写在main.js文件中
```javascript
// 引入Router
import Router from 'vue-router';

// 引入路由跳转的组件
import Home from './components/Home';

// 配置Router
const router = new VueRouter({
  // 路由模式 (hash | history | abstract(如果没有发现浏览器API, 会自动进入此模式))
  // 使用history模式可以去掉地址栏的'#'字符, 但需要后端配置支持
  // history 模式利用window.history API 来完成页面的跳转而无需重新加载页面
  mode: 'history',
  base: process.env.BASE_URL,
  routes: [
    {
      path: '/',
      name: 'home',
      component: Home
    }, {
      path: '/about',
      name: 'about',
      component: () => import('./views/About.vue')
    }
  ]
})

// 使用Router
Vue.use(Router)

new Vue({
  router: router,
  render: function(h) { return h(App) }
}).$mount('#app')
```

写在template中
```html
<template>
  <!-- 使用路由名称跳转 -->
  <router-link :to="{name:'home'}">主页</router-link>
  <router-link :to="{name:'about'}">关于</router-link>

  <!-- 直接指定跳转路径 -->
  <router-link to="/">主页</router-link>
</template>
```

<!-- more -->

也可将Router配置抽离到单独的js文件中
```javascript
export default new VueRouter({
  mode: 'history',
  base: process.env.BASE_URL,
  routes: [
    {
      // 路径
      path: '/',
      // 名称
      name: 'home',
      // 挂载的组件
      component: Home
    }, {
      path: '/about',
      name: 'about',
      // 懒加载
      component: () => import('./views/About.vue'),
      // 子路由
      children: [{
        path: 'concat',
        name: 'concat',
        component: Concat
      },{
        path: 'guide',
        name: 'guide',
        component: Guide,
        // 子路由下的子路由
        children: [
          // ...
        ]
      }],
      redirect: {name: 'concat'}
    }, {
      // 匹配所有(上面没有匹配到的)
      path: '*',
      // 重定向
      redirect: '/error/notfound'
    }
  ]
})
```

## 动态路由匹配
使用`:` 标记路径参数，当匹配到一个路由时，参数值会被设置到`this.$route.params`，以便在组件内使用。
```javascript
const router = new Router({
  routes: [{
    path: '/user/:id',
    component: User
  }]
})
```

可以设置多段路径参数

| 路由设置 | 匹配路径 | Params |
| :------ | :------ | :----- |
| /user/:id | /user/233 | {id: '233'} |
| /user/:id/name/:name | /user/233/name/ilirus | {id: '233', name: 'ilirus'}

**当使用路径参数时, 例如`/user/233`到`/user/450`, 原来的组件实例会被复用, 这导致组件的生命周期钩子不会被调用**

所以应该对`$router`监测变化(watch)，或者使用导航守卫
```javascript
watch: {
  '$route' (to, from) {
    // ...
  }
}

beforeRouteUpdate(to, from, next) {
  // ...
}
```

## 高级匹配模式
`vue-router`使用`path-to-regexp`作为路径匹配引擎，所以支持更高级的匹配模式。
```javascript
const router = new Router({
  routes: [
    // 使用?来将参数定义为可选项
    {path: '/optional/:para?'},
    {path: '/optional/(:para/)?name'}
    // 使用正则来实现仅匹配数字
    {path: '/number/:id(\\d+)'}
  ]
})
```

## 在JavaScript中进行路由跳转
### router.push()
`router.push(location, onComplete?, onAbort?)`  
此方法会向history 栈添加一个新记录, 所以当用户点击后退按钮时，会返回到之前的URL

此方法接收一个字符串路径，或者一个描述地址的对象作为参数
```javascript
// 字符串
router.push('/user')

// 对象
router.push({path: '/user'})
router.push({name: 'user'})

// 带参数
// -> /user/233
router.push({name: 'user', params: {id: '233'}})
// -> /user?id=233
router.push({name: 'user', query: {id: '233'}})

// 如果提供了path, 那么param会被忽略, query则不会
// -> /user/233
router.push({path: '/user/233'})
// -> /user
router.push({path: '/user', params: {id: '233'}})
// -> /user?id=233
router.push({path: 'user', params: {id: '233'}})
```

`onComplete`和`onAbort` 回调函数会在导航成功完成(在所有的异步钩子被解析之后) 或者终止(导航到相同的路由、或在当前导航完成之前导航到另一个不同的路由) 的时候进行调用

### router.replace()
`router.replace(location, onComplete?, onAbort?)`  
此方法会替换掉当前的history 记录, 所以当用户点击后退按钮时，不会返回到之前的URL

其余用法同`router.push()`方法

### router.go()
`router.go(n)`  
此方法接收一个整数作为参数，意为在history 记录中前进多少步
```javascript
// 前进一步
router.go(1)
// 后退一步
router.go(-1)
// 前进十步, 如果记录不足, 则不会跳转
router.go(10)
```

## 视图命名
可以在界面中拥有多个单独命名的视图，而不是只有一个单独的出口。

如果 router-view 没有设置名字，那么默认为 default。

```html
<router-view></router-view>
<router-view name="main"></router-view>
```

```javascript
const router = new Router ({
  routes: [{
    path: '/',
    components: {
      default: Nav,
      main: Main
    }
  }]
})
```

# 导航守卫
> 参数或查询的改变并不会触发 进入/离开 的导航守卫。可以通过观察 $route 对象来应对这些变化，或使用 beforeRouteUpdate 的组件内守卫。

## 全局前置守卫
```javascript
const router = new Router({ ... })

// 全局前置守卫
// to: 即将要进入的目标路由
// from: 正要离开的路由
// next: 放行, 不调用next方法则不会进入目标路由, 根据参数的不同, 效果也不同
// next(): 进行管道中的下一个钩子, 如果全部钩子执行完了, 则导航的状态就是confirmed
// next(false): 中断当前的导航, 如果浏览器的URL改变了, 会重置到from路由对应的地址
// next('/') | next({path: '/'}): 跳转到另一个地址, 会使当前的导航被中断, 然后进行一个新的导航
// next(Error): 导航会被终止, 且会触发router.onError()
router.beforeEach((to, from, next) => {
  // ...
})
```

当一个导航触发时，全局前置守卫按照创建顺序调用。守卫是异步解析执行，此时导航在所有守卫`resolve`完之前会一直处于等待中。

## 全局后置钩子
此方法不接收`next()`函数也不会改变导航本身
```javascript
const router = new Router({ ... })

router.afterEach((to, from) => {
  // ...
})
```

## 路由独享守卫
用法与全局前置守卫相同
```javascript
const router = new Router({
  routes: [{
    path: '/home',
    component: Home,
    beforeEnter: (to, from, next) => {
      // ...
    }
  }]
})
```

## 组件内的守卫
可以在路由组件内直接定义这些路由导航守卫
```javascript
// 在渲染该组件的对应路由被 confirm 前调用
// 不能 获取组件实例 this, 因为当守卫执行前，组件实例还没被创建
beforeRouteEnter(to, from, next) {
  // ...
},

// 在当前路由改变，但是该组件被复用时调用
// 举例来说，对于一个带有动态参数的路径 /foo/:id，在 /foo/1 和 /foo/2 之间跳转的时候，
// 由于会渲染同样的 Foo 组件，因此组件实例会被复用。而这个钩子就会在这个情况下被调用。
// 可以访问组件实例 this
beforeRouteUpdate(to, from, next) {
  // ...
},

// 导航离开该组件的对应路由时调用
// 可以访问组件实例 this
// 通常用来阻止用户在还未保存修改前突然离开
// 该导航可以通过 next(false) 来取消
beforeRouteLeave(to, from, next) {
  // ...
}
```

`beforeRouteEnter` 不能访问到this, 但可以通过给`next()`传递回调来访问组件实例, 且只有`beforeRouteEnter`方法支持给`next()`函数传递回调
```javascript
beforeRouteEnter(to, from, next) {
  next(vm => {
    // ...
  })
},
```

# 过渡动效
使用`transition`组件给`router-view`添加一些过渡效果
```html
<transition name="slide">
  <router-view></router-view>
</transition>
```

也可以动态设置过渡效果
```html
<transition :name="transitionType">
  <router-view></router-view>
</transition>
```

```javascript
watch: {
  '$route' (to, from) {
    const toDepth = to.path.split('/').length
    const fromDepth = from.path.split('/').length
    this.transitionType = toDepth < fromDepth ? 'slide-right' : 'slide-left'
  }
}
```