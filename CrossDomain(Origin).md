# 同源策略-浏览器[MDN](https://developer.mozilla.org/zh-CN/docs/Web/Security/Same-origin_policy)

## 跨域-服务端已经接收到了请求并返回了响应，浏览器对非同源请求返回的结果做了拦截

> 1. 服务端http访问控制cors(cross-origin sharing: 跨域资源共享)---Access-Control-Allow-Origin
> 2. 开发/生产(nginx)阶段proxy
> 3. jsonp(json padding)-script标签不受同源策略影响
