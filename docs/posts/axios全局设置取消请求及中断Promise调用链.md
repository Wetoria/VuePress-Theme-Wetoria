---
title: axios全局设置取消请求及中断Promise调用链
tag:
---
```
```
// 获取CancelToken
const CancelToken = axios.CancelToken;
const source = CancelToken.source();

// http request 拦截器);
axios.interceptors.request.use(
  (config) => {
    // 全局添加cancelToken
    newConfig.cancelToken = source.token;
    return newConfig;
  },
  (err) => {
    const error = err;
    return Promise.reject(error);
  },
);

// http response 拦截器
axios.interceptors.response.use((response) => {
  const code = response.data.code;
  if (code == '002') {
    // 某个请求的响应满足特定条件的情况下，取消其他正在进行的请求
    source.cancel();
  }
  return Promise.resolve(response.data);
}, (error) => {
  if (axios.isCancel(error)) { // 取消请求的情况下，终端Promise调用链
    return new Promise(() => {});
  } else {
    return Promise.reject(error);
  }
});
```

