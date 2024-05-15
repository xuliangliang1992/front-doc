# 核心库

## xi-my-v2

[标品解释](https://www.yuque.com/gmall/manual/hmtvaosau322t7gv)

### 调试

xi-my-v2 无法使用本地调试。需使用[nginx](../../tools/nginx.md) 和 [xswitch](../../tools/xswitch.md)

1. 本地构建`npm run build`。
2. 启动 nginx，将 xi-my-v2 的 dist 目录映射到 nginx 目录下。
3. 添加 xswitch 配置，启动 xswitch
   ```json
   [
     "https://xijiu-myth-fd-uat.oss-cn-shenzhen.aliyuncs.com/ui/uat/my.wap.js", //ui库资源请求地址
     "http://127.0.0.1:8088/my.wap.js" //本地nginx服务地址
   ]
   ```

## xi-design

[标品解释](https://www.yuque.com/gmall/manual/hmtvaosau322t7gv)

## FAQ

### 1. eslint `vue/attribute-hyphenation` 规则引起功能异常

```js
<my-form-item
  startKey="startTime"
  endKey="endTime"
  prop="startTime"
  label="活动时间"
  :readonly="!draft"
  type="daterange"
  :value="form"
/>
// 代码保存后自动转换成 start-key="startTime" end-key="endTime"
```

**起因**<br />
xi-my-v2 中`src/my/components/formItem/daterange.js` `src/my/components/formItem/item.js`中使用此属性

**解决**<br/>
添加忽略

```js
'vue/attribute-hyphenation': ['error', 'always', {
  /**
    xi-my-v2
    src/my/components/formItem/daterange.js
    src/my/components/formItem/item.js
    中使用此属性，需要忽略
    */
  ignore: ['startKey', 'endKey'],
}],
```
