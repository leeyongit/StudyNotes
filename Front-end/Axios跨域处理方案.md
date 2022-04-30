## Axios跨域处理方案

#### Axios跨域请求问题处理（开发环境）

1、在config文件夹下的prod和dev的js文件下添加域名

```java
dev:
module.exports = merge(prodEnv, {
  NODE_ENV: '"development"',
  API_HOST: '"/api"'
});

prod:
module.exports = {
  NODE_ENV: '"production"',
  API_HOST: '"https://xxx.xxx.com/"' //服务器域名
};
```



2、在config文件夹下的index.js下对dev环境进行配置

```cpp
proxyTable: {
      "/api": {
        target: "https://xxx.xxx.com/", //设置你调用的接口域名和端口号
        changeOrigin: true, //跨域
        pathRewrite: {
          "^/api": "/" 
     }
   }
},
```

3、在你二次封装Axios文件内设置Axios.defaults.baseURL



```undefined
Axios.defaults.baseURL = process.env.API_HOST;
```

4、正常的Axios网络请求



```jsx
 loadMore({ commit, state }) {
    request({
      url: "/event/list?loc=108288&start=" + state.skip + "&count=10",
      methods: "get",
      params: ""
    })
      .then(function(response) {
        console.log(response);
        commit({
          type: "loadMore",
          res: response.data.events
      });
    })
      .catch(function(error) {
        console.log(error);
    })
      .then(function() {
    });
 }
```

生产与开发环境都配置完成了，开发环境可直接进行跨域请求，生产环境还需要后端进行配合处理

#### 服务端：

后端服务器配置一下cros跨域即可



```undefined
access-control-allow-origin：*
```

允许所有访问

