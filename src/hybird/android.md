# WebView Android 适配

## 文件上传

开启文件上传，可使用 HTML5 标签 `<input type="file">` 唤出系统文件管理器或自定义文件管理器，然后选择文件。

Android 需重写 WebChromeClient onShowFileChooser 方法

```java
private ValueCallback<Uri[]> uploadMessageAboveL;
mWebView.setWebChromeClient(new WebChromeClient() {

    @Override
    public boolean onShowFileChooser(WebView webView, ValueCallback<Uri[]> filePathCallback, FileChooserParams fileChooserParams) {
        // 拍照
        openCamera();
        // 相册 文件选择模式 0单选 1多选
        openAlbum(fileChooserParams.getMode());
        uploadMessageAboveL = filePathCallback;
        return true;
    }
});

/**
 * WebView对象，在用户取消文件选择器的情况下，需给onReceiveValue传null返回值
 * 否则WebView在未收到返回值的情况下，无法进行任何操作，文件选择器会失效
 */
private void cancelUploadImage() {
    if (uploadMessageAboveL != null) {
        uploadMessageAboveL.onReceiveValue(null);
        uploadMessageAboveL = null;
    }
}

/**
 * 上传文件给h5
 *
 * @param arrayList 文件列表
 */
private void uploadImage(ArrayList<LocalMedia> arrayList) {
    if (null == uploadMessageAboveL || null == arrayList || arrayList.size() == 0) {
        return;
    }
    Uri[] results = new Uri[arrayList.size()];
    for (int i = 0; i < arrayList.size(); i++) {
        LocalMedia local = arrayList.get(i);
        String imagePath = local.getRealPath();
        Uri uri = Uri.fromFile(new File(imagePath));
        results[i] = uri;
    }
    uploadMessageAboveL.onReceiveValue(results);
    uploadMessageAboveL = null;
}
```




## 文件下载

weiView 需要设置下载监听器。
下载有两种方式：

1. 文件流的形式，需申请文件存储权限，将文件流以文件形式存储。
2. 下载链接的形式，如阿里云 oss 链接等，直接跳转到浏览器下载。

```java
webView.setDownloadListener(new DownloadListener() {
    @Override
    public void onDownloadStart(String url, String userAgent, String contentDisposition, String mimetype, long contentLength) {
        if (url.startsWith("data:")) {
            // url是文件流
            // todo 1.申请权限 Permission.WRITE_EXTERNAL_STORAGE
            // 2.存储文件
            createAndSaveFileFromBase64Url(url, activity);
            return;
        }
        // url是下载链接
        Uri uri = Uri.parse(url);
        Intent intent = new Intent(Intent.ACTION_VIEW, uri);
        activity.startActivity(intent);
    }
});

````

文件流下载

```java
/**
 * 下载base64
 *
 * @param url base64
 */
@SuppressWarnings("ResultOfMethodCallIgnored")
public static void createAndSaveFileFromBase64Url(String url, Activity activity) {
    File path = Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_DOWNLOADS);
    String filetype = url.substring(url.indexOf("/") + 1, url.indexOf(";"));
    String filename = System.currentTimeMillis() + "." + filetype;
    File file = new File(path, filename);
    try {
        if (!path.exists())
            path.mkdirs();
        if (!file.exists())
            file.createNewFile();
        String base64EncodedString = url.substring(url.indexOf(",") + 1);
        byte[] decodedBytes = Base64.decode(base64EncodedString, Base64.DEFAULT);
        OutputStream os = Files.newOutputStream(file.toPath());
        os.write(decodedBytes);
        os.close();
        // 通知系统刷新文件存储
        MediaScannerConnection.scanFile(activity,
            new String[]{file.toString()}, null,
            (path1, uri) -> {
                activity.runOnUiThread(new Runnable() {
                    @Override
                    public void run() {
                        // 图片保存成功
                    }
                });
            });
    } catch (IOException e) {
        // 图片保存失败
    }
}
```
