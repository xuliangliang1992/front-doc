# WebView Android 适配

## 文件上传

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

```

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
