# axios当切换路由页面，取消正在请求的数据接口



### 应用场景：当切换路由页面，取消正在请求的接口

### 1.请求拦截器
```
// axios.js
window._axiosPromiseArr = [] // axios中设置放置要取消的对象

// 请求拦截器
axios.interceptors.request.use(config => {
  config.cancelToken = new axios.CancelToken(cancel => {
    // 存储取消请求接口对象
    window._axiosPromiseArr.push({ cancel });
  });

  return config;
}, error => {
  console.log(error);
  return Promise.reject(error);
});

```

### 2.响应拦截器
```
service.interceptors.response.use(response => {
  // 在请求完成后，从数组中移除相应的取消函数
  window._axiosPromiseArr = window._axiosPromiseArr.filter(ele => ele.cancel !== response.config.cancelToken.promise);
  return response;
}, error => {
  // 在请求错误时，也移除相应的取消函数
  window._axiosPromiseArr = window._axiosPromiseArr.filter(ele => ele.cancel !== error.config?.cancelToken?.promise);
  return Promise.reject(error);
});

```

### 3.进入路由钩子拦截时候，取消保存的_axiosPromiseArr

```
// main.js
router.beforeEach((to, from, next) => {
  // 遍历并取消所有未完成的请求
  window._axiosPromiseArr.forEach(ele => {
    if (ele.cancel) {
      ele.cancel(); // 取消请求
    }
  });

  // 清空请求数组
  window._axiosPromiseArr = []; // 清空请求的参数
  next(); // 继续路由导航
});

```
