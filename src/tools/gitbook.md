# GitBook 教程

[GitBook 官网](https://www.gitbook.com)

## 安装

1. GitBook 是一个基于 Node.js 的命令行工具，下载安装 [Node.js](https://nodejs.org/en)，安装完成之后，你可以使用下面的命令来检验是否安装成功。这里我是用 nvm。

   ```bash
   nvm install v7.7.1
   Downloading and installing node v7.7.1...
   Downloading https://nodejs.org/dist/v7.7.1/node-v7.7.1-darwin-x64.tar.xz...
   ######################################################################### 100.0%
   Computing checksum with shasum -a 256
   Checksums matched!
   Now using node v7.7.1 (npm v4.1.2)
   ```

2. 切换到 7.7.1。node 版本高了会安装失败。

   ```bash
   nvm use 7.7.1
   Now using node v7.7.1 (npm v4.1.2)
   ```

3. 输入下面的命令来安装 GitBook。

   ```bash
   npm install gitbook-cli -g
   ```

4. 安装完成之后，你可以使用下面的命令来检验是否安装成功。

   ```bash
   gitbook -V
   CLI version: 2.3.2
   GitBook version: 3.2.3
   ```

## 使用

1. GitBook 准备工作做好之后，我们进入一个你要写书的目录，输入如下命令:

   ```bash
   gitbook init
   warn: no summary file in this book
   info: create README.md
   info: create SUMMARY.md
   info: initialization is finished
   ```

2. 可以看到他会创建 README.md 和 SUMMARY.md 这两个文件，README.md 应该不陌生，就是说明文档，而 SUMMARY.md 其实就是书的章节目录，其默认内容如下所示：

   ```bash
   # Summary
   * [Introduction](README.md)
   ```

### 输出成 PDF

执行 `gitbook pdf`

```bash
gitbook pdf
info: 7 plugins are installed
info: 5 explicitly listed
info: loading plugin "highlight"... OK
info: loading plugin "search"... OK
info: loading plugin "lunr"... OK
info: loading plugin "fontsettings"... OK
info: loading plugin "theme-default"... OK
info: found 5 pages
info: found 14 asset files
info: >> generation finished with success in 5.5s !
info: >> 1 file(s) generated
```

#### 若 gitbook pdf 命令失败

```bash
gitbook pdf
info: 7 plugins are installed
info: 5 explicitly listed
info: loading plugin "highlight"... OK
info: loading plugin "search"... OK
info: loading plugin "lunr"... OK
info: loading plugin "fontsettings"... OK
info: loading plugin "theme-default"... OK
info: found 5 pages
info: found 13 asset files

InstallRequiredError: "ebook-convert" is not installed.
Install it from Calibre: https://calibre-ebook.com
```

如下操作:

1.  安装[calibre](https://calibre-ebook.com)

    ```bash
    brew install --cask calibre
    ```

2.  ebook-convert -help

   ```bash
   ebook-convert -help
   使用情况: ebook-convert input_file output_file [options]

   转换不同格式的电子书。

   input_file 表示输入文件，output_file 表示输出文件。这两者作为命令行参数必须指定到最前面。

   输出电子书格式由output_file的文件扩展名推测。output_file也可以是特殊格式.EXT，其中EXT是输出文件扩展名。在这种情况下，输出文件的名称源自输入文件的名称。请注意，文件名不能以连字符开头。如果output_file不含扩展名，那么它将被视为一个文件夹，由HTML文件组成的“开放电子书”（OEB）将被写入该文件夹。
   这些文件会被视为正常文件而被输出插件所识别。

   在指定输入和输出文件后，你可以自定义特定的转换选项。根据输入和输出文件格式不同可用的转换选项也不同。如需获取针对输入和输出文件的帮助，请在命令行中输入 -h。

   对于转换系统的完整文档请查阅
   https://manual.calibre-ebook.com/conversion.html

   每当您将包含空格的参数传递给%p程序时，请将参数放在引号中。例如: "/some path/with spaces"

   选项:
   --version       显示程序版本号并退出

   -h, --help      显示此帮助信息并退出

   --list-recipes  列出内建的规则名。你可以通过如下命令创建基于内建的规则的电子书： ebook-convert "Recipe
   Name.recipe" output.epub

   创建者Kovid Goyal <kovid@kovidgoyal.net>
   ```

[ebook-convert 文档](https://manual.calibre-ebook.com/zh_CN/generated/zh_CN/ebook-convert.html)
