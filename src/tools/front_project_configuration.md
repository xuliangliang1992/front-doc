# 前端项目配置

前端开发多人协作的团队项目时应该要配置的一些规则

## commitlint

### 安装
```bash
yarn add @commitlint/cli --save-dev  
yarn add @commitlint/config-conventional --save-dev
```
初始化@commitlint/cli的配置文件，在项目根目录创建名为commitlint.config.js的文件
```json
/**
 * feature：新功能
 * update：更新某功能
 * fixbug：修补某功能的bug
 * refactor：重构某个功能
 * optimize: 优化构建工具或运行时性能
 * style：仅样式改动
 * docs：仅文档新增/改动
 * chore：构建过程或辅助工具的变动
 */
module.exports = {
  extends: ['@commitlint/config-conventional'],
  rules: {
    'type-enum': [
      2,
      'always',
      ['feature', 'update', 'fixbug', 'refactor', 'optimize', 'style', 'docs', 'chore'],
    ],
    'type-case': [0],
    'type-empty': [0],
    'scope-empty': [0],
    'scope-case': [0],
    'subject-full-stop': [0, 'never'],
    'subject-case': [0, 'never'],
    'header-max-length': [0, 'always', 72],
  },
};

```

### 验证
当我们在当前项目中执行 git commit -m '测试提交' 时将触发commit-msg事件钩子。<br/>
若校验不通过，则在终端输出错误，commit终止。

```bash
git commit -m "11"
✔ Preparing lint-staged...
✔ Running tasks for staged files...
✔ Applying modifications from tasks...
✔ Cleaning up temporary files...
⧗   input: 11
✖   subject may not be empty [subject-empty]

✖   found 1 problems, 0 warnings
ⓘ   Get help: https://github.com/conventional-changelog/commitlint/#what-is-commitlint
```

若校验通过，则committ提交成功。
```bash
git commit -m "chore: commitlint"
✔ Preparing lint-staged...
✔ Running tasks for staged files...
✔ Applying modifications from tasks...
✔ Cleaning up temporary files...
[feature/20240429 0aef082] chore: commitlint
 4 files changed, 3865 insertions(+), 3273 deletions(-)
 create mode 100644 commitlint.config.js
```
## only-allow

### 场景

多个前端开发工程师。每个人在安装依赖的时候方式不一，有人习惯用 cnpm install，别人习惯用 yarn install 或 npm install。这样的场景下，可能存在每个人所处的开发环境的依赖包不同。因此，可以试图用工具去规范团队： only-allow 。

### 使用

只需在 package.json 中加入一行代码来限制，如下含义：只允许使用 npm 来进行安装<br />
npx only-allow [method]（method 可取值：npm | yarn | pnpm）

```json
"scripts": {
    // ... 其他命令
    "preinstall": "npx only-allow npm"
 }
```

### 验证

在终端面板输入命令 yarn install ，终端会报错，并中断安装进程。

```bash
yarn install
yarn install v1.22.19
$ npx only-allow npm
Need to install the following packages:
only-allow@1.2.1
Ok to proceed? (y) y
╔════════════════════════════════════════════════════════╗
║                                                        ║
║   Use "npm install" for installation in this project   ║
║                                                        ║
╚════════════════════════════════════════════════════════╝
error Command failed with exit code 1.
info Visit https://yarnpkg.com/en/docs/cli/install for documentation about this command.
```
