# nrm

nrm 是一个 npm 源管理器，允许你快速地在 npm 源间切换。

## 安装

```bash
npm install -g nrm
```

## 使用

1. 查看当前源

```bash
nrm current
```

2.  查看可选源

```bash
nrm ls
```

3. 切换源

```bash
nrm use <registry>
```

4. 添加源

```bash
nrm add <registry> <url>
```

5. 删除源

```bash
nrm del <registry>
```

6. 测试源

```bash
nrm test <registry>
```
