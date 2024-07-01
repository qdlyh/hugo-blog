# Vue2通过keep Alive设置页面缓存


### 一直以来移动端的项目都有一种优化上的需求，那就是前进页面拿最新的数据，后退页面即缓存页面。

### 总体思路

1.在 vue 方法里 `keep-alive` 的 `include` 设置需要缓存页面的 name 可以判断页面是否已缓存（`include` 可字符串可数组）这里通过数组方式进行

2.设置文件里的 name 和路由里的 name（保持一致）

3.`router.js` 路由文件中设置两个值 `keepAlive` 和 `deleteCachedName`。需要缓存的设置 `keepAlive:true`，不需要的直接设置 `deleteCachedName`:需要删除的缓存 name

4.新增/删除 include 数组缓存中的 name

5.返回上一页自动滑动到上次滚动条的位置

```
 //App.vue

  <keep-alive :include="$store.state.cachedRouteNames">
     <router-view :key="$route.query.id"></router-view>
  </keep-alive>

      //router.js
  {
  path: '/home',
  name: 'home',
  meta: {
    title: '我需要缓存页面',
    keepAlive: true, //页面需要缓存
  },
    component: () =>import('@/views/home/index.vue')
  },
  {
  path: '/goHome',
  name: 'goHome',
  meta: {
    title: '删除缓存',
    deleteCachedName: 'home', //删除home页面需要缓存
  },
    component: () =>import('@/views/goHome/goHome.vue')
  }

      //state.js

  const state = {
    cachedRouteNames: [], //缓存页面路由
    cachedRouteScroll:null //缓存页面滚动条位置
  };

export default state

  //mutations.js
  const mutations = {
  /\*

  - 设置缓存
  - 添加缓存，需要配置在组件里添加 name 以及路由里写上对应组件的 name (name 格式 xx-xx (父组件 name-当前 name) 用"-"区分开来)
  - 删除缓存，需要配置（以缓从组件）上一个组件里添加"deleteCachedName:删除的组件 name" (例如 A ——> B 删除以缓存的组件 B，即在 A 的路由 meta 里添加 deleteCachedName:B)
    _/
  SetcachedRouteNames(state,{ action, route }) {
  const methods= {
    'add': () => {
    //添加缓存
    state.cachedRouteNames.push(route)
    state.cachedRouteNames=Array.from(new Set(state.cachedRouteNames)) //删除缓存重复数据 name
  },
  'delete': () => {
  //删除缓存
    state.cachedRouteNames.splice(state.cachedRouteNames.findIndex((e) => { return e === route}),1)
    }
  }
    methods[action]()
  },
    /_
    *设置缓存滚动条 set 记录滚动条位置，to 滚动到记录位置。set 和 to 调用方法是一样的
    *to 一般用在页面初始化 DOM 已加载完进行调用 this.$store.commit('SetcachedRouteScroll', { action: 'to', name: '' })
  *set一般用在页面离开进行调用 this.$store.commit('SetcachedRouteScroll', { action: 'set', name: '' })

    this.$store.commit('SetcachedRouteScroll', { action: 'to', name: '滚动条模块的name或id，需要加上.或#' })
    this.$store.commit('SetcachedRouteScroll', { action: 'to', name: '.class' })
    this.$store.commit('SetcachedRouteScroll', { action: 'to', name: '#id' })
  \*/

  SetcachedRouteScroll(state,{ action, name }) {
  const methods= {
    'set': () => {
    //记录滚动条位置
    state.cachedRouteScroll= document.querySelector(name).scrollTop;
    // console.log(state.cachedRouteScroll)
  },
  'to': () => {
    //滚动到记录位置
    document.querySelector(name).scrollTop = state.cachedRouteScroll;
    // console.log(state.cachedRouteScroll)
  }
    }
      methods[action]()
    },
  };

  export default mutations

  //main.js

  /\*

  - 页面转跳之后
  - 添加缓存，需要配置在组件里添加 name 以及路由里写上对应组件的 name (name 格式 xx-xx (父组件 name-当前 name) 用"-"区分开来)
  - 删除缓存，需要配置（以缓从组件）上一个组件里添加"deleteCachedName:删除的组件 name" (例如 A ——> B 删除以缓存的组件 B，即在 A 的路由 meta 里添加 deleteCachedName:B)
    \*/
  router.afterEach((to, from, next) => {
      if (to.meta.keepAlive) {
        //页面缓存
        store.commit('SetcachedRouteNames', {
          action: 'add',
          route: to.name
        })
      }
      if (to.meta.deleteCachedName) {
      //删除缓存
        store.commit('SetcachedRouteNames', {
          action: 'delete',
          route: to.meta.deleteCachedName
        })
      }
  });

  //组件
  activated() {
  //滑动到记录滚动条的位置
    this.$store.commit('SetcachedRouteScroll', { action: 'to', name: '#pullScrollCell' })
  },
  methods: {
    //转跳页面，记录当前滚动条
    go(item) {
        this.$store.commit('SetcachedRouteScroll', { action: 'set', name: '#pullScrollCell' })
        this.$router.push({
        path: '/details',
        query: {
            id:xxx,
            }
        })
    },
  }
```

