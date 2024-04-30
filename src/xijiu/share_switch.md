# 君品荟APP分享切换小程序
## 起因
开发阶段并行多个迭代同时开发,但小程序只有一个体验版,故当时申请了两个小程序。<br />
君品荟分销小程序用于sit环境。原始id ：`gh_123c4ad0871d`<br />
君品荟小程序用于uatt环境。原始id ：`gh_cd6c35c8fbd0`<br />
```
miniprogramType:0    //0 生产 1 开发 2 体验
```

### Android 
[微信文档](https://developers.weixin.qq.com/doc/oplatform/Mobile_App/Share_and_Favorites/Android.html)<br />
gradle.properties文件中修改 `userName``miniprogramType`<br />
如<br />
```Java
dev_userName="gh_cd6c35c8fbd0"
dev_miniprogramType=2
```

### iOS 
[微信文档](https://developers.weixin.qq.com/doc/oplatform/Mobile_App/Share_and_Favorites/iOS.html)<br />
ViewController.m 文件中 `shareMiniProgram` 方法修改 `userName`<br />
```ObjectiveC
object.userName = @"gh_cd6c35c8fbd0";
object.miniProgramType = 2;
```

## 解决
小程序已开发切换环境功能,目前已不需要修改App的代码。