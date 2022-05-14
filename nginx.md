#### 代理
```
nginx 代理给网关的时候，会丢失掉请求的host信息
proxy_set_header Host $host
```