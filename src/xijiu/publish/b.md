# 管理端 发布
管理端分为运营后台和商家后台

**运营后台** 主要由 fd-admin-marketing、fd-admin-product、fd-admin-system、fd-admin-trade组成。<br/>
**商家后台** 主要由 fd-seller-marketing、fd-seller-product、fd-seller-system、fd-seller-trade组成。<br/>
引用了init 工程，这 9 个应用发布流程一样。

除此之外还引用了 ui 库，是 gmall 核心库，发布方式见 [ui 发布](./ui.md)
## 运行流水线

见 [大禹发布流程](./dayu.md)

## 配置
1. 登录[运营管理后台](https://admin.exijiu.com/login/index.html)
2. 点击左侧导航栏的`系统管理-应用管理`
   ![](./assets/b1.jpg)
3. 点击设置按钮，修改分支

    - console 对应 admin 工程
    - seller_console 对应 seller 工程

   ![](./assets/b2.jpg)

   ![](./assets/b3.jpg)

4. 点击配置功能点，配置菜单。

   ![](./assets/b4.jpg)

   ![](./assets/b5.jpg)







