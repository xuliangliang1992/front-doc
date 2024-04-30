# iOS 发布应用

## 一、XCode 安装包上传

1. 打开 XCode，修改版本号
2. 点击菜单栏 Product -> Archive
   ![](./assets/xcode1.jpg)
3. 打包完成，点击 Distribute App
   ![](./assets/xcode2.jpg)
4. 选择 App Store Connect，点击下一步
   ![](./assets/xcode3.jpg)
5. 上传完成后等待一段时间即可在 AppStoreConnect 中看到上传的版本。
   ![](./assets/xcode4.jpg)

## 二、AppStoreConnect

1. 打开 https://developer.apple.com/account 网址，并登录开发者账号。
   ![](./assets/store1.jpg)
2. 点击 APP
   ![](./assets/store2.jpg)
3. 选择将要发布的 APP
   ![](./assets/store3.jpg)
4. 点击 testflight，可以看到刚刚上传的包，之后即可准备提交审核。
   ![](./assets/store4.jpg)
5. 点击加号创建一个新版本，和打包时的版本号一致，在构建版本里选择一个之前上传的版本
   ![](./assets/store5.jpg)
6. 如有其他信息需要修改，改完之后点存储，再点添加以供审核。
