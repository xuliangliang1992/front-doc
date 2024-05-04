# 君品荟 C 端

## 一、开发过程

### APP 端

一开始使用一个 WebView,用于展示 H5。<br />
后发现一些问题，如：

- 页面滚动位置未记录，导致页面跳转后，页面位置丢失。<br />
- 短视频播放时，滑动不流畅，需要使用原生播放器。<br />

之后做了一部分原生改造，改为多个 webview，每个 WebView 对应一个 H5 页面。

### 小程序

一开始只有部分页面在小程序创建了页面。

- 登录。微信授权登录为小程序原生开发。其他登录相关功能还是 H5。<br />
- 几个 tab、商品、店铺、下单等主流程。

之后测试中发现一些问题，如：

- 页面刷新问题。部分页面需要每次，刷新若在 onShow()中重新加载网页，每次触发 onShow()都会增加一个 history，导致点击返回时需要多次才能离开当前页。
- 文件处理。如果该页面不是 WebView 第一层，触发 onShow 就会刷新到该 WebView 第一层，导致页面数据丢失。

所以在小程序代码为 c 端页面建了对应的页面，解决上述问题。
后续部分页面转小程序原生也方便，只需处理对应页面即可。

## 二、JSBridge 通信

### H5

公共代码在 init 工程 wap 目录下。

```JavaScript
my.isAndroid = AppUtil.isAndroid(); // 通过userAgent判断是否是安卓
my.isIOS = AppUtil.isIOS();  // 通过userAgent判断是否是IOS
//env 通过 url传入 https://mall-uat.exijiu.com/pages/shop.html?env=h5#/home
my.isAndroidAPP = my.isAndroid && my.env === "app";
my.isIOSAPP = my.isIOS && my.env === "app";
//从Android 顶部状态栏高度,默认25px,从app端获取
my.androidStatusHeight = "25px";
//顶部安全距离
my.topSafeDistance = "env(safe-area-inset-top)";
if (my.isAndroidAPP) {
  // 从Android App端传过来的状态栏距离
  const height = window.android?.getStatusHeight();
  if (height) {
    my.androidStatusHeight = height;
  }
  my.topSafeDistance = my.androidStatusHeight;
}

var rootDom = document.querySelector(":root");
//顶部安全距离
rootDom.style.setProperty("--top-safe-distance", my.topSafeDistance);
```

```TypeScript
interface MY {
  env?: 'h5' | 'app' | 'miniapp';
  jump: (path: string, params?: Record<string, any>) => void;
  goBack: (length?: number) => void;
  refreshPage: (route?: string) => void;
  isAndroid: boolean;
  isIOS: boolean;
  isAndroidAPP?: boolean;
  isIOSAPP?: boolean;
  isMiniApp?: boolean;
  /**安卓顶部状态栏高度 */
  androidStatusHeight?: string;
  /**顶部安全距离 */
  topSafeDistance: string;
}
```

#### 屏幕适配

1. 状态栏适配: window.my.topSafeDistance 或者 var(--top-safe-distance),二选一即可。
  ```JavaScript
  <template>
    <div class="container" :style="`padding-top: ${topSafeDistance};`">
  </template>

  <style lang="scss" scoped>
  .container {
    padding-top: var(--top-safe-distance);
  }
  </style>
  ```
2. 底部适配: 君品荟仅支持 iOS 12 以上，使用 env(safe-area-inset-bottom)即可。
  ```css
  .main-container {
    padding-bottom: env(safe-area-inset-bottom);
  }
  ```

关于兼容性:

- 可在 url 切换 env 更换环境调试 app 和小程序，
- 可在浏览器窗口切换尺寸，里面有 Android 和 iOS 的尺寸。注：浏览器内 Android 屏幕 topSafeDistance 是默认的 25px,实际不同手机是不一样的。

通过上述方法可调试大部分适配，若部分机型适配有问题，可按照[调试文档](../../hybird/debug/README.md)来调试。

#### 路由跳转

##### 一、my.jump()

jump 内部处理了跳转逻辑，区分 Android、iOS、微信小程序、H5。<br />
Routes 路由注册在`src/wap/util/routes.js`文件。<br />
若配置了路由中的 miniapp 则需要去小程序代码新增页面,否则小程序无法跳转。<br/>
**建议都配置并新增小程序页面，可解决切屏刷新问题。**<br/>
若未在路由注册，则跳转逻辑按 H5 处理。即:同一 WebView 内跳转。

```JavaScript
const Routes = {
  //店铺
  shophome: {
    tab: false, // 是否是 tabbar
    path: "/pages/shop.html", // 工程
    h5: "/shophome", // 路由
    miniapp: "/pages/shophome/shophome", // 小程序路径
  },
}
//my.jump('shophome',{id:1,currentTab:0})
function jump(routeName, params) {
  let route = Routes[routeName];
}
```

1. app - js 调用 openPage 方法

  ```JavaScript
  // Android
  window.android?.openPage(
    JSON.stringify({
      origin: window.location.origin,
      route: route.h5,
      path: route.path,
      source: 'jph',// 网页来源 jph:君品荟 jf:积分商城 bbs:会员论坛 后续可扩展
      params, // 路由携带参数，native 端会做拼接处理
    })
  );
  // iOS
  window.webkit.messageHandlers.openPage.postMessage({
    origin: window.location.origin,
    route: route.h5,
    path: route.path,
    source: 'jph',
    params,
  });
  ```
2. 小程序 js 调用 navigateTo 方法

  ```JavaScript
    wx.miniProgram.navigateTo({
      url: `${route.miniapp}`,
    });
  ```
3. H5 - router.push 方法

  ```JavaScript
    // 本应用路由使用vue-router
    $router.push(url)
    // 其他页面
    window.location.href = url;
  ```

##### 二、my.goBack()

内部处理了返回逻辑，区分 Android、iOS、微信小程序、H5。

1. app - js 调用 goBack 方法

  ```javaScript
  // 调用Android的handleEvent方法，传入goBack事件和页面步数
  window.android?.handleEvent(
    JSON.stringify({
        event: "goBack",
        params: {
        length,
      },
    })
  );
  // 调用iOS的handleEvent方法，传入goBack事件和页面步数
  window.webkit.messageHandlers.handleEvent.postMessage({
    event: "goBack",
    params: {
      length,
    },
  });
  ```

2. 小程序 js 调用 navigateBack 方法

  ```JavaScript
    // 小程序中直接使用navigateBack方法返回上一页
    wx?.miniProgram.navigateBack({
      delta: 1,
    });
  ```

##### 三、my.refreshPage()

1. app
  部分页面需要手动刷新，如：下单、加购等操作之后需要刷新购物车。<br/>
  通知 APP 调用 H5 方法刷新页面，基本无感，会比 APP 强制 reload WebView 体验更好。

  ```javaScript
  // 调用Android的handleEvent方法，传入refresh事件和route
  window.android?.handleEvent(
    JSON.stringify({
      event: "refresh",
      params: {
        route,
      },
    })
  );
  // 调用iOS的handleEvent方法，传入refresh事件和route
  window.webkit.messageHandlers.handleEvent.postMessage({
    event: "refresh",
    params: {
      route,
    },
  });
  ```

2. 小程序
  因H5 无法对小程序发送除[微信小程序](https://developers.weixin.qq.com/miniprogram/dev/component/web-view.html)支持之外的消息。<br/>
  故小程序的刷新可通过如下方法:
  - onShow()强刷加载h5。<br/>
  - wx.miniProgram.redirectTo()。<br/>


#### 导航栏
通用导航栏用`<c-toolbar title="评价晒单" />`，基于`<van-nav-bar />`二次封装，内部处理了返回和顶部适配。

#### **请务必使用上述<span style="color: red;">跳转、返回</span>方法，防止某个端兼容问题，导致页面前进后退逻辑异常。**<br/>
如有特殊处理的地方另说，比如评论列表会使用 `router.replace` 和 `wx.miniProgram.redirectTo`。<br/>

```JavaScript
tabChange(e) {
  if (my.isMiniApp) {
    wx.miniProgram.redirectTo(
      `pages/evaluate/evaluate?currentTab=${e}`,
    )
  }
  else {
    this.$router.replace({ query: { currentTab: e } })
  }
}
```

第三方页面若 APP 端无特殊处理，则是按照 H5 `window.href = url`跳转处理。

**<span style="color:green">建议</span>** 是我们建一个页面用iframe包裹第三方的页面，这样主要跳转逻辑还是君品荟的，iframe内部跳转逻辑由第三方自行处理，这样交互会方便点。如：客服页面。

有些第三方页面跳转多层之后结束，没有回调通知我们，导致需要一层层返回，体验不好。这样就可以在iframe外增加按钮处理。如：久来米页面。

  ```JavaScript
  <template>
    <div class="warp_root">
      <c-toolbar :title="title" right-text="完成签约" @right-click="onRightClick" />
      <div v-if="my.isMiniApp" :style="`height: calc(100% - ${my.topSafeDistance} - 64px - env(safe-area-inset-bottom))`">
        <iframe
          height="100%"
          width="100%"
          frameBorder="0"
          :src="path"
        />
      </div>
      <div v-else :style="`height: calc(100% - ${my.topSafeDistance} - 46px)`">
        <iframe
          height="100%"
          width="100%"
          frameBorder="0"
          :src="path"
        />
      </div>
      <van-sticky
        v-if="my.isMiniApp"
        position="bottom"
        offset="0"
        class="bottom-nav"
      >
        <van-button
          type="primary"
          color="#0FC3B9"
          block
          round
          class="button"
          @click="onRightClick"
        >
          完成签约
        </van-button>
      </van-sticky>
    </div>
  </template>
  ```

## Native端

Android基于 Java 开发，iOS基于 Objective-C 开发。
具体见[Hybird](../../hybird/README.md)。

## FAQ
1. APP分享切换小程序<br />
    **起因**<br />
    开发阶段并行多个迭代同时开发,但小程序只有一个体验版,故当时申请了两个小程序。<br />
    君品荟分销小程序用于 sit 环境。原始 id ：`gh_123c4ad0871d`<br />
    君品荟小程序用于 uat 环境。原始 id ：`gh_cd6c35c8fbd0`<br />
      ```
      miniprogramType: 0    //0 生产 1 开发 2 体验
      ```
    * [Android微信文档](https://developers.weixin.qq.com/doc/oplatform/Mobile_App/Share_and_Favorites/Android.html)<br />
    gradle.properties 文件中修改 ` userName``miniprogramType `<br />
      ```Java
      dev_userName="gh_cd6c35c8fbd0"
      dev_miniprogramType=2
      ```
    * [iOS微信文档](https://developers.weixin.qq.com/doc/oplatform/Mobile_App/Share_and_Favorites/iOS.html)<br />
    ViewController.m 文件中 `shareMiniProgram` 方法修改 `userName`<br />
      ```ObjectiveC
      object.userName = @"gh_cd6c35c8fbd0";
      object.miniProgramType = 2;
      ```
    **解决**<br/>
    小程序已开发切换环境功能,目前已不需要修改 App 的代码。
