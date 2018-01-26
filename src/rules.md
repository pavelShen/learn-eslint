[title]eslint常见报错规则及处理方案[/title]

本文挑选了一些eslint的报错规则，说明lint报错的原因以及修改方案，让自己了解要怎么改，以及要这么改的理由。

备注：
1. ~~本文可以当成[官方规则文档](https://eslint.org/docs/rules/)的部分翻译~~突然发现了[中文文档](https://cn.eslint.org/docs/rules/no-prototype-builtins) so sad
2. 只包含了核心模块的规则，并不包括扩展库
3. 想要看全部的规则可以看[文档](https://eslint.org/docs/rules/)

<!-- TOC -->

- [可能的错误](#可能的错误)
  - [no-await-in-loop（禁止在循环中使用await）](#no-await-in-loop禁止在循环中使用await)
  - [no-compare-neg-zero (禁止比较-0)](#no-compare-neg-zero-禁止比较-0)
  - [no-cond-assign (禁止在条件语句中进行赋值操作)](#no-cond-assign-禁止在条件语句中进行赋值操作)
  - [no-control-regex (禁止在正则中使用控制字符的ASCII)](#no-control-regex-禁止在正则中使用控制字符的ascii)
  - [no-extra-boolean-cast (多余的强制转换布尔值)](#no-extra-boolean-cast-多余的强制转换布尔值)
  - [no-extra-parens (禁止多余的括号)](#no-extra-parens-禁止多余的括号)
  - [no-prototype-builtins （禁止直接使用 Object.prototypes 的内置属性）](#no-prototype-builtins-禁止直接使用-objectprototypes-的内置属性)
  - [no-sparse-arrays (禁止稀疏数组)](#no-sparse-arrays-禁止稀疏数组)
- [小结](#小结)
- [参考文档](#参考文档)

<!-- /TOC -->


## 可能的错误

### no-await-in-loop（禁止在循环中使用await）

当请求有依赖关系时，请关闭该选项
```javascript
// 每次循环都会在上一个异步操作完成之后才执行
async function foo(things) {
  const results = [];
  for (const thing of things) {
    results.push(await bar(thing));
  }
  return baz(results);
}

// 解决方案：Promise.all
async function foo(things) {
  const results = [];
  for (const thing of things) {
    results.push(bar(thing));
  }
  return baz(await Promise.all(results));
}
```



### no-compare-neg-zero (禁止比较-0)

```javascript
// why
0 === -0 // true

// 处理方案
Object.is(0,-0) //false
```

`Object.is(value1, value2)`方法判断两个值是否是相同的值。
具体细节参考[文档](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/is)



### no-cond-assign (禁止在条件语句中进行赋值操作)

```javascript
// 一不小心就会犯的错误
// eslint no-cond-assign: ["error", "always"]
if (user.jobTitle = "manager") { }
```
该规则有2个option
`except-parens` 如果赋值操作通过括号包裹则不报错
`always` 不允许在条件判断中所有的赋值操作



### no-control-regex (禁止在正则中使用控制字符的ASCII)

AlloyTeam ESLint 配置指南中的解释：
禁止在正则表达式中出现 Ctrl 键的 ASCII 表示，即禁止使用 `/\x1f/`
因为字符串中一般不会出现 Ctrl 键，所以一旦出现了，可能是一个代码错误

个人理解：
应该不单单是屏蔽了Ctrl，而是屏蔽了所有控制字符的ASCII码，防止输入错误



### no-extra-boolean-cast (多余的强制转换布尔值)

在条件表达式中，表达式的结果将被强制转为布尔值，没必要通过双重否定（!!）或布尔调用转换为布尔值。

注：不影响赋值，函数声明


### no-extra-parens (禁止多余的括号)

语法：
```javascript
/* eslint no-extra-parens: ["error", "all", { "conditionalAssign": false }] */
```

- "all" (default) 检查所有表达式
- "functions" 和all同级的option，禁止函数声明和函数表达式有多余的括号
- "conditionalAssign": 条件表达式例外
- "returnAssign": 返回声明例外
- "nestedBinaryExpressions": 表达式嵌套 例：`x = a || (b && c);`
- "ignoreJSX": "none|all|multi-line|single-line" 默认值是none，是否忽略jsx检查，针对单行多行模式
- "enforceForArrowConditionals": 箭头函数允许多余的括号包裹三元表达式


### no-prototype-builtins （禁止直接使用 Object.prototypes 的内置属性）
当创建的对象具有 Object.prototype 的内置属性时，可能会导致错误出现。

```javascript
// bad
var hasBarProperty = foo.hasOwnProperty("bar");

// good
var hasBarProperty = Object.prototype.hasOwnProperty.call(foo, "bar");
```


### no-sparse-arrays (禁止稀疏数组)
不允许数组中有empty slot

```javascript
// 长度为2 第二个逗号并没有作用
var items = [,,];

// 长度为3，两个逗号可能是输入错误
var colors = [ "red",, "blue" ];

// 尾部的逗号是ok的
var colors = [ "red", "blue", ];
```


## 小结

每次接手老项目时，看到那堆红色波浪线，简直心累，于是偷偷的，在优化老代码的同时，顺便过下文档，权当巩固基础了~

PS: 消灭这堆波浪线，真的很爽

再PS：eslint --fix虽然很爽，但是还是谨慎啊

本人能力有限，如有错误，欢迎指正。

## 参考文档

- [MDN](https://developer.mozilla.org/zh-CN/)
- [官方文档](https://eslint.org/docs/rules/)
- [中文文档](https://cn.eslint.org/docs/rules/no-prototype-builtins)
- [AlloyTeam ESLint 配置指南](http://www.alloyteam.com/2017/08/13065/)