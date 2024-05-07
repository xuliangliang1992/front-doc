# 前端项目配置

前端开发多人协作的团队项目时应该要配置的一些规则
simple-git-hooks + eslint + lint-staged + commitlint + only-allow

## simple-git-hooks

### 安装

```bash
npm i -D simple-git-hooks
```

### 配置

1. package.json 下增加 simple-git-hooks 配置

```json
"simple-git-hooks": {
  "pre-commit": "npx lint-staged",
  "commit-msg": "npx --no -- commitlint --edit ${1}"
}
```

2. 更新配置

```bash
npx simple-git-hooks
```

### 验证

1. 当我们在当前项目中执行 git commit 时将触发 pre-commit 事件钩子。若校验不通过，则在终端输出错误，commit 终止。

   ```bash
   git commit -m 'fix: eslint修复'
   ✔ Preparing lint-staged...
   ⚠ Running tasks for staged files...
     ❯ package.json — 2 files
       ❯ *.{js,mjs,cjs,jsx,ts,mts,cts,tsx,vue,json,md,html,css,scss,sass} — 1 file
         ✖ eslint --cache --fix [FAILED]
   ↓ Skipped because of errors from tasks.
   ✔ Reverting to original state because of errors...
   ✔ Cleaning up temporary files...

   ✖ eslint --cache --fix:

   /Users/xuliangliang/iwhalecloud/xijiu/gmall-fd-seller-8827/webpack.config.js
     125:28  error  Expected '!==' and instead saw '!='  eqeqeq

   ✖ 1 problem (1 error, 0 warnings)
   ```

2. 当我们在当前项目中执行 git commit -m '测试提交' 时将触发 commit-msg 事件钩子。若校验不通过，则在终端输出错误，commit 终止。

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

3. 若校验通过，则 commit 提交成功。

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

## eslint

### 安装

```bash
npm i -D @antfu/eslint-config
npm i -D @eslint/config-inspector
npm i -D eslint-plugin-format
```

### 配置

创建 eslint.config.mjs 文件

```javascript
import antfu from "@antfu/eslint-config";

export default antfu(
  {
    formatters: true,
    ignores: ["*.min.js", "**/public/lib/"],
    lessOpinionated: true,
    vue: { vueVersion: 2 }, // 仅限 Vue 2
  },
  {
    rules: {
      "brace-style": ["error", "stroustrup", { allowSingleLine: false }],
      curly: ["error", "all"],
      "no-console": "off",
      "vue/max-attributes-per-line": ["error", { singleline: 3 }],
      "vue/max-len": [
        "error",
        {
          code: 160,
          ignoreComments: true,
          ignoreHTMLAttributeValues: true,
          ignoreHTMLTextContents: true,
          ignoreRegExpLiterals: true,
          ignoreStrings: true,
          ignoreTemplateLiterals: true,
          ignoreUrls: true,
        },
      ],
      "vue/no-mutating-props": ["error", { shallowOnly: true }],
      "vue/singleline-html-element-content-newline": "off",
    },
    languageOptions: {
      globals: {
        my: "readonly",
        Vue: "readonly",
        __webpack_public_path__: "writable",
      },
    },
  }
);
```

## lint-staged

### 安装

```bash
npm i -D lint-staged
```

### 配置

package.json 下增加 lint-staged 配置

```json
"lint-staged": {
  "*.{js,mjs,cjs,jsx,ts,mts,cts,tsx,vue,json,md,html,css,scss,sass}": "eslint --cache --fix"
}
```

## commitlint

### 安装

```bash
yarn add @commitlint/cli --save-dev
yarn add @commitlint/config-conventional --save-dev
```

### 配置

初始化@commitlint/cli 的配置文件，在项目根目录创建名为 commitlint.config.mjs 的文件

```javascript
// Default Config See https://github.com/conventional-changelog/commitlint/tree/master/@commitlint/config-conventional
import config from "@commitlint/config-conventional";

const { rules } = config;

// See https://github.com/vuejs/core/blob/main/scripts/verifyCommit.js
rules["type-enum"][2].push("wip", "types", "release", "workflow", "dx");
rules["header-max-length"][2] = 200;
rules["subject-case"][0] = 0;

export default {
  extends: ["@commitlint/config-conventional"],
  rules,
};
```

## only-allow

### 场景

多个前端开发工程师。每个人在安装依赖的时候方式不一，有人习惯用 cnpm install，别人习惯用 yarn install 或 npm install。这样的场景下，可能存在每个人所处的开发环境的依赖包不同。因此，可以试图用工具去规范团队： only-allow 。

### 安装

```bash
yarn add only-allow --save-dev
```

### 使用

只需在 package.json 中加入一行代码来限制，如下含义：只允许使用 npm 来进行安装<br />
npx only-allow [method]（method 可取值：npm | yarn | pnpm）

```json
"scripts": {
    // ... 其他命令
    "preinstall": "npx only-allow npm && npm config set registry https://registry.npmmirror.com"
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

## license-checker

### 安装

```bash
npm i -D license-checker
```

## npm-deprecated-check

### 安装

```bash
npm i -D npm-deprecated-check
```
