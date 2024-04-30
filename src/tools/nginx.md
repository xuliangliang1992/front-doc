# nginx

## 安装

```shell
brew install nginx
```

### 查看本地 nginx 信息

`brew info nginx`

```shell
==> nginx: stable 1.25.2 (bottled), HEAD
HTTP(S) server and reverse proxy, and IMAP/POP3 proxy server
https://nginx.org/
/opt/homebrew/Cellar/nginx/1.25.2 (26 files, 2.4MB) *
  Poured from bottle using the formulae.brew.sh API on 2023-08-22 at 10:52:10
From: https://github.com/Homebrew/homebrew-core/blob/HEAD/Formula/n/nginx.rb
License: BSD-2-Clause
==> Dependencies
Required: openssl@3 ✔, pcre2 ✔
==> Options
--HEAD
	Install HEAD version
==> Caveats
Docroot is: /opt/homebrew/var/www

The default port has been set in /opt/homebrew/etc/nginx/nginx.conf to 8080 so that
nginx can run without sudo.

nginx will load all files in /opt/homebrew/etc/nginx/servers/.

To restart nginx after an upgrade:
  brew services restart nginx
Or, if you don't want/need a background service you can just run:
  /opt/homebrew/opt/nginx/bin/nginx -g daemon\ off\;
```

## 使用

### 打开 nginx 目录

```bash
open /opt/homebrew/Cellar/nginx/1.25.2
```

### nginx 启动

```bash
sudo nginx
```

### nginx 停止

```bash
sudo nginx -s stop
```

### 快速关闭 Nginx，可能不保存相关信息，并迅速终止 web 服务。

```bash
nginx -s stop
```

### 平稳关闭 Nginx，保存相关信息，有安排的结束 web 服务。

```bash
nginx -s quit
```

### 因改变了 Nginx 相关配置，需要重新加载配置而重载。

```bash
nginx -s reload
```

### 重新打开日志文件。

```bash
nginx -s reopen
```

### 为 Nginx 指定一个配置文件，来代替缺省的。

```bash
nginx -c filename
```

### 不运行，仅仅测试配置文件。nginx 将检查配置文件的语法的正确性，并尝试打开配置文件中所引用到的文件。

```bash
nginx -t
```

### 显示 nginx 的版本。

```bash
nginx -v
```

### 显示 nginx 的版本，编译器版本和配置参数。

```bash
nginx -V
```
