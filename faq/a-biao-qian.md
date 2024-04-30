---
description: a标签设置target="_blank"在app内点击无反应
---

# a标签

```html
<a target="_blank" href="https://www.sobot.com/">ddd</a>
```

#### &#x20;Webview中a标签点击无反应

&#x20;<mark style="color:orange;">**Android原因：**</mark>跳转链接如果添加target=\_blank会回调WebChromeClient的onCreateWindow方法, 需要进行适配

```java
@Override
public boolean onCreateWindow(WebView view, boolean isDialog, boolean isUserGesture, Message resultMsg) {      
   WebView newWebView = new WebView(view.getContext());
   newWebView.setWebViewClient(new WebViewClient() {
        @Override
        public boolean shouldOverrideUrlLoading(WebView view, WebResourceRequest request) {
            mBinding.webView.loadUrl(request.getUrl().toString());
            return true;
        }
    });
    WebView.WebViewTransport transport = (WebView.WebViewTransport) resultMsg.obj;
    transport.setWebView(newWebView);
    resultMsg.sendToTarget();
    return true;
}
```
