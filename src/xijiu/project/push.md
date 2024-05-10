# 消息推送

[个推文档](https://docs.getui.com/getui/server/rest_v2/common_args/?id=doc-title-6)

## 个性化推送开关

1. h5 打开个性化服务开关时，通知到 app，app 开启推送服务

   - android

   ```javascript
   window.android?.handleEvent(
     JSON.stringify({
       event: "turnOnPush",
     })
   );
   ```

   - ios

   ```javascript
   window.webkit.messageHandlers.handleEvent.postMessage({
     event: "turnOnPush",
   });
   ```

2. h5 关闭个性化服务开关时，通知到 app，app 关闭推送服务

   - android

   ```javascript
   window.android?.handleEvent(
     JSON.stringify({
       event: "turnOffPush",
     })
   );
   ```

   - ios

   ```javascript
   window.webkit.messageHandlers.handleEvent.postMessage({
     event: "turnOffPush",
   });
   ```

## 上传 cid

在登录之后上传 cid，setParams 事件中有 token 参数时

```javascript
window.android.handleEvent(
  JSON.stringify({
    event: "setParams",
    params: {
      token,
      jfToken,
      bbsToken,
    },
  })
);
window.webkit.messageHandlers.handleEvent.postMessage({
  event: "setParams",
  params: {
    token,
    jfToken,
    bbsToken,
  },
});
```

## payload 消息参数定义

- route: 页面路由
- path: h5 应用。有/pages/shop.html 和 /pages/shop_base.html 两种
- source: 用于区分 h5 来源。有 jph 、 jf、bbs 三种，当前需求默认传 jph
- params：h5 参数。用于加载数据，一般为 h5 链接?后内容
- origin：h5 域名。用于区分环境，有下面五个

  ```text
  dev：	https://mall-dev.exijiu.com
  sit:     https://mall-sit.exijiu.com
  uat:   	 https://mall-uat.exijiu.com
  inner:	 https://mall-inner.exijiu.com
  prod:	 https://mall.exijiu.com
  ```

## 示例

1. 商品详情 https://mall-dev.exijiu.com/pages/shop.html#/detail?id=17744

   ```json
   {
     "route": "detail",
     "params": "id=17744",
     "path": "/pages/shop.html",
     "source": "jph",
     "origin": "https://mall-dev.exijiu.com"
   }
   ```

2. 店铺 https://mall-dev.exijiu.com/pages/shop.html#/shophome?id=19902&currentTab=0

   ```json
   {
     "route": "shophome",
     "params": "id=19902&currentTab=0",
     "path": "/pages/shop.html",
     "source": "jph",
     "origin": "https://mall-dev.exijiu.com"
   }
   ```

### iOS
iOS可直接使用

### android
android 需将上述参数处理后使用`"click_type":"intent"`方式

#### intent 字段 java 代码拼接

```java
 /**
 * 获取跳转url
 * 如果您手动拼装intent，
 * 如有特殊字符：比如 Scheme 链接含有#符号，
 * 建议 encode 编码处理，
 * 参数在经过Android 系统接收后，会自动进行解码处理
 * 经测试，intent 参数里面有 % 符号，客户端接收参数会乱码；
 * intent 参数里面有 # 和 ; 符号，会影响跳转，
 * encode 编码处理后正常
 *
 * @param origin      域名
 * @param path        路径
 * @param route       路由
 * @param queryString 参数
 * @param source      来源
 * @return intent
 */
public static String getIntentString(String route, String origin, String path, String queryString, String source) {
    boolean isMain = "/home".equals(route) || "/member".equals(route);
    StringBuilder url = new StringBuilder();
    url.append("intent://com.getui.push/");
    if (isMain) {
        // 首页或个人中心
        url.append("main");
    } else {
        url.append("detail");
    }
    url.append("?#Intent;");
    url.append("scheme=jphpush;")
            .append("launchFlags=0x04000000;")
            .append("package=com.exijiu.junpinhui;");
    if (isMain) {
        // 首页或个人中心
        url.append("component=com.exijiu.junpinhui%2Fcom.iwhalecloud.xijiu.pages.main.MainActivity;");
    } else {
        url.append("component=com.exijiu.junpinhui%2Fcom.iwhalecloud.xijiu.pages.other.PushActivity;");
    }
    url.append("S.gttask=;");
    if ("/home".equals(route)) {
        url.append("i.main_tab_index=0;");
    } else if ("/member".equals(route)) {
        url.append("i.main_tab_index=4;");
    } else {
        url.append("S.route=").append(route).append(";");
        url.append("S.queryString=").append(queryString).append(";");
        url.append("S.path=").append(path).append(";");
        url.append("S.source=").append(source).append(";");
        url.append("S.origin=").append(origin);
    }
    url.append("end");
    return url.toString();
}
```

#### 示例

- 首页

  ```text
  intent://com.getui.push/main?#Intent;scheme=jphpush;launchFlags=0x04000000;package=com.exijiu.junpinhui;component=com.exijiu.junpinhui%2Fcom.iwhalecloud.xijiu.pages.main.MainActivity;S.gttask=;i.main_tab_index=0;end
  ```

- 个人中心

  ```text
  intent://com.getui.push/main?#Intent;scheme=jphpush;launchFlags=0x04000000;package=com.exijiu.junpinhui;component=com.exijiu.junpinhui%2Fcom.iwhalecloud.xijiu.pages.main.MainActivity;S.gttask=;i.main_tab_index=4;end
  ```

- 其他页面

  ```text
  intent://com.getui.push/detail?#Intent;scheme=jphpush;launchFlags=0x04000000;package=com.exijiu.junpinhui;component=com.exijiu.junpinhui%2Fcom.iwhalecloud.xijiu.push.PushActivity;S.gttask=;S.route=%2Fdetail;S.queryString=id%3D17744;S.path=%2Fpages%2Fshop.html;S.source=jph;S.origin=https%3A%2F%2Fmall-dev.exijiu.com;end
  ```
