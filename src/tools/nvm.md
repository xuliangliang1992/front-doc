# nvm

## 安装

### 直接通过 brew 安装

```bash
brew install nvm
```

### 配置环境变量

1. 执行 `open -e ~/.bash_profile`
2. 添加`source /opt/homebrew/opt/nvm/nvm.sh`
3. 执行 `source ~/.bash_profile`

## 常用命令

1. 查看当前使用的 node 版本

    ```bash
    nvm current
    ```

2. 查看所有已经在本地安装的 node 版本

    ```bash
    nvm ls
    ```

3. 查看所有可下载的版本

    ```bash
    nvm list available
    ```

4. 安装最新版 node

    ```bash
    nvm install stable
    ```

5. 使用最新版本的 node

    ```bash
    nvm use node
    ```

6. 安装指定版本的 node

    ```bash
    nvm install node版本号（如：nvm install 14.16.0）
    ```

7. 使用指定版本的 node

    ```bash
    nvm use node版本号
    ```

8. 卸载指定版本的 node

    ```bash
    nvm uninstall node版本号
    ```

9. 设置 node 别名

    ```bash
    nvm alias node别名 node版本号
    ```

10. 取消别名

    ```bash
    nvm unalias node别名
    ```

11. 设置默认使用的 node 版本

    ```bash
    nvm alias default node版本号
    ```
