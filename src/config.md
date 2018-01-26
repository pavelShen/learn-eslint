## 有两种主要的方式来配置 ESLint：

- 使用 JavaScript 注释把配置信息直接嵌入到一个代码源文件中。省略规则名则全部禁止
```javascript
/* eslint-disable no-alert, no-console */

alert('foo');
console.log('bar');

/* eslint-enable no-alert, no-console */
```

- 使用 JavaScript、JSON 或者 YAML 文件为整个目录和它的子目录指定配置信息。可以配置一个独立的 .eslintrc.* 文件，或者直接在 package.json 文件里的 eslintConfig 字段指定配置，ESLint 会查找和自动读取它们，再者，你可以在命令行运行时指定一个任意的配置文件。

```js
// .eslintrc.js
module.exports = {
  // 解析器，一般不用动，直接默认就行
  "parser": "esprima",

  // 当存在多个配置文件时候，越靠近目标文件的配置优先级最高
  // 设置root，则停止配置的向上查找
  "root": true

  // 定义了一组预定义的全局变量
  // 常用第三方库的全局变量，入jquery
  "env": {
    "browser": true,
    "commonjs": true
  },
  // 这里定义了两个全局变量：var1 和 var2。如果你想指定这些变量不应被重写（只读），你可以将它们设置为 false：
  // 注意： 要启用no-global-assign规则来禁止对只读的全局变量进行修改。
  "globals": {
    "var1": true,
    "var2": false
  }
  // 规则默认是关闭的，可以用个这个参数来开启对应的规则默认配置
  "extends": "eslint:recommended",
  // 第三方插件，插件名称可以省略 eslint-plugin- 前缀。
  // 全局安装的 ESLint 只能使用全局安装的插件。本地安装的 ESLint 不仅可以使用本地安装的插件，也可以使用全局安装的插件。
  "plugins": [
    "plugin1",
    "eslint-plugin-plugin2"
  ],
  // 规则
  // "off" 或 0 - 关闭规则
  // "warn" 或 1 - 开启规则，使用警告级别的错误：warn (不会导致程序退出)
  // "error" 或 2 - 开启规则，使用错误级别的错误：error (当被触发的时候，程序会退出)
  "rules": {
    "eqeqeq": "off",
    "curly": "error",
    "quotes": ["error", "double"]
  },
  // 对特定文件进行检查，优先级还是内部优先级较高
  "overrides": [
    {
      "files": [ "bin/*.js", "lib/*.js" ],
      "excludedFiles": "*.test.js",
      "rules": {
        "quotes": [ 2, "single" ]
      }
    }
  ]
};
```

## 支持的格式与优先级

优先级如下
1. JavaScript - 使用 .eslintrc.js 然后输出一个配置对象。
2. YAML - 使用 .eslintrc.yaml 或 .eslintrc.yml 去定义配置的结构。
3. JSON - 使用 .eslintrc.json 去定义配置的结构，ESLint 的 JSON 文件允许 JavaScript 风格的注释。
4. (不推荐) - 使用 .eslintrc，可以使 JSON 也可以是 YAML。
5. package.json - 在 package.json 里创建一个 eslintConfig属性，在那里定义你的配置。

## ignore
.eslintignore 文件告诉 ESLint 去忽略特定的文件和目录

- 以 # 开头的行被当作注释，不影响忽略模式。
- 路径是相对于 .eslintignore 的位置或当前工作目录。这也会影响通过 --ignore-pattern传递的路径。
- 忽略模式同 .gitignore 规范
- 以 ! 开头的行是否定模式，它将会重新包含一个之前被忽略的模式。
- ESLint总是忽略 `/node_modules/*` 和 `/bower_components/*`中的文件
