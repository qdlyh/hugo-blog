# Vue2通过keep Alive设置页面TABS业务缓存


### 项目上有个需求，需要缓存已打开的页面，同时关闭 tab 的时候清除缓存。

<img src="/images/TAB缓存.png" alt="示例图片" width="800" height="50">

### 总体思路

1.在 vue 方法里`keep-alive`的`include`设置需要缓存页面的`name`可以判断页面是否已缓存（`include`可字符串可数组）这里通过数组方式进行

2.设置文件里的`name`和路由里的`name`（保持一致）

3.在进入页面之后获取该页面路由放置到`include`数组里

4.数组过滤重复进入已缓存的页面`name`

5.点击 tab 关闭标签的时候删除`include`数组缓存中的`name`

### 过程中遇到问题

#### 1.进入已缓存的页面`include`数组又重新`push`了已缓存的页面`name`导致缓存失败。

解决方法通过设置`Array.from(new Set())`过滤掉重复的数据即可

#### 2.详情页面根据 key 改变的页面没有更新数据。例如 url 为/xxx?id=7c65f58

解决方法通过设置 key `<router-view :key="$route.query.id"></router-view>`

```
    //App.vue  
      
  <keep-alive :include="$store.state.historyList.cachedHistoryNames">
      <router-view :key="$route.query.id"></router-view>
  </keep-alive>
  
  //state.js
  
  historyList: {
  historyNave: [{
  //保存详情页历史记录
  title: '首页',
    cachedName: 'home',
    href: '/home'
  }],
    cachedHistoryNames: [], //缓存页面路由
  }
 
   //main.js
 
  router.afterEach((to) => {
    //保存详情页浏览历史记录
  setTimeout(() => {
    if (to.meta.title) {
      //判断该导航是否已存在新增的url，不存在才插入url
      let path= window.location.hash.split('#')[1];
      letisHave=store.state.historyList.historyNave.every((item) => {
      if (item.href !==path) {
        return true;
      }
    })
      if (isHave) {
        store.state.historyList.historyNave.push({
          "title": to.meta.title,          //tab的名称
          "cachedName": to.name,   //缓存页面的name
          "href": path                 //点击tab转跳的地址
        });
      }
    }
    store.state.historyList.cachedHistoryNames.push(to.name) //缓存页面
      store.state.historyList.cachedHistoryNames=Array.from(new Set(store.state.historyList.cachedHistoryNames)) //删除缓存重复数据name
    });
  });
 
//顶部导航组件
 
  methods: {
        //删除缓存
    deleteHistory(item, index) {
      this.historyList.splice(index, 1); //删除历史导航
      this.cachedHistoryNames.forEach((element, i) => { //删除历史导航缓存
      if (element===item.cachedName) {
        this.cachedHistoryNames.splice(i, 1)
      }
      }
    }
  },
 
```

