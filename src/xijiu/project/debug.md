# 调试

## 本地调试

本地采取在线上环境集成调试，在线上环境地址里传入调试参数加载本地工程代码调试。<br/>
**注：init、xi-my-v2、fe-portal 不行，需要使用 xswitch 代理线上地址。**<br/>
以 shop 本地调试为例：

启动项目，端口号取自本地服务端口

```bash
gmall-fd-shop-8838 % npm start
> fe-shop@v0.0.1 start
> webpack-dev-server --mode=development

<i> [webpack-dev-server] Project is running at:
<i> [webpack-dev-server] Loopback: http://127.0.0.1:8080/
```

- 线上页面地址：
  `https://mall-uat.exijiu.com/pages/shop.html?env=h5#/home`
- 本地调试地址：
  `https://mall-uat.exijiu.com/pages/shop.html?_debug_=8080&env=h5#/home`

## xswitch

init、xi-my-v2、fe-portal 需要使用 xswitch 代理

1. 安装 [xswitch](../../tools/xswitch.md)。
2. 配置 xswitch.json<br/>
   以 shop 为例，配置如下：
   ```json
   [
     "https://xijiu-myth-fd-uat.oss-cn-shenzhen.aliyuncs.com/fe-shop/release/20240430/js/app.js", //网络请求地址
     "http://127.0.0.1:8080/js/app.js" //本地服务地址
   ]
   ```
3. 启动 xswitch，刷新页面即可。
