# axios当切换路由页面，取消正在请求的接口



### 应用场景：当切换路由页面，取消正在请求的接口

### 1.请求拦截器
```
// axios.js
window._axiosPromiseArr = [] // axios中设置放置要取消的对象

axios.interceptors.request.use(config => {

  config.cancelToken = new axios.CancelToken(cancel => {

    window._axiosPromiseArr.push({ cancel })

  })

  return config

}, error => {
  return Promise.reject(error)
})
```

### 2.进入路由钩子拦截时候，取消保存的_axiosPromiseArr

```
// main.js
router.beforeEach((to, from, next) => {

  window._axiosPromiseArr.forEach((ele,index) => {

      ele.cancel() // 路由跳转之前，清空（终止）上一个页面正在请求的内容

      // 清空请求的参数 清空请求的参数

      delete window._axiosPromiseArr[index]

  })

})
```
