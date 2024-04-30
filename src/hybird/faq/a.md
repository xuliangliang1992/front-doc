# Webview 中 a 标签点击无反应

```html
<a target="_blank" href="https://www.baidu.com/">打开百度</a>
```

## Android

**原因：** 跳转链接如果添加 `target=\_blank` 会回调 WebChromeClient 的 onCreateWindow 方法, 需要进行适配

```Java
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

## iOS

WKWebView 加载页面, 当页面使用 a 标签跳转时 跳转方式

1. 本页跳转`target="_self"`, WKWebView 可以正常跳转;
2. 新页面跳转`target="_blank"`, WKWebView 无响应, 需要进行处理, 才能跳转

```ObjectiveC
- (WKWebView *)webView:(WKWebView *)webView createWebViewWithConfiguration:(WKWebViewConfiguration *)configuration forNavigationAction:(WKNavigationAction *)navigationAction windowFeatures:(WKWindowFeatures *)windowFeatures {

    // 修复_blank的bug
    if (!navigationAction.targetFrame.isMainFrame) {
        [webView loadRequest:navigationAction.request];
    }

    return nil;
}
```

