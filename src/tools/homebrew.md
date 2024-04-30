## Homebrew

[官网](https://brew.sh/zh-cn/) [github](https://github.com/Homebrew/brew)

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

### 查看 brew 的帮助

```bash
brew help
```

### 安装软件

```bash
brew install git
```

### 卸载软件

```bash
brew uninstall git
```

### 搜索软件

```bash
brew search git
```

### 显示已经安装软件列表

```bash
brew list
```

### 更新软件，把所有的 Formula 目录更新，并且会对本机已经安装并有更新的软件用\*标明。

```bash
brew update
```

### 更新某具体软件

```bash
brew upgrade git
```

### 显示软件内容信息

```bash
brew info git
```

### 用浏览器打开

```bash
brew home
```

### 显示包依赖

```bash
brew deps
```

### 显示包的依赖树

```bash
brew deps --installed --tree
```

### 启动 web 服务器，可以通过浏览器访问 http://localhost:4567/ 来同网页来管理包

```bash
brew server
```

### 删除程序，和 upgrade 一样，单个软件删除和所有程序老版删除。

```bash
brew cleanup git
brew cleanup
```

### 查看那些已安装的程序需要更新

```bash
brew outdated
```
