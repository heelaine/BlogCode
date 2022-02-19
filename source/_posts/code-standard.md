---
title: 代码规范
author: helaine
date: 2022-02-17 15:51:48
tags: ["vscode", "代码规范"]
categories: 代码规范
---

# 代码规范

{% note info, 这一part主要讲的是平常代码的规范及其配置，包括eslint，stylelint和prettier。 %}

{% title h2, eslint %} 
**1.下载eslint（vscode 插件）**
`npm install eslint --save-dev`


**2.基本配置**
root: true
env：环境
extends：扩展包,会包括一些写好的规则
parserOptions: 可解析的选项
```
extends: ["plugin:vue/essential", "eslint:recommended",'@vue/airbnb'],
parserOptions: {
		parser: "babel-eslint",
}, //支持 es5 之外的语法
```
rules:校验规则

**3.rules 的选项**
'xxx 规则'：三种选项
* "off" or 0 - 关
* "warn" or 1 - 警告 
* "error" or 2 - 错误

**4.常见的 rules**
4.1 句末分号
semi:该规则有两个选项，一个字符串选项和一个对象选项。
**字符串选项**：
* "always"（默认）在语句末尾需要分号
* "never"不允许使用分号作为语句的结尾（除了消除以[、(、/、+或开头的语句的歧义-）

**对象选项**（当"always"）：
* "omitLastInOneLineBlock": true 忽略块中的最后一个分号，其中它的大括号（以及块的内容）在同一行中

**对象选项**（当"never"）：
* "beforeStatementContinuationChars": "any"[（默认）如果下一行以、(、/、+或开头，则忽略语句末尾的分号（或缺少分号）-。
* "beforeStatementContinuationChars": "always"[如果下一行以、(、/、+或开头，则在语句末尾需要分号-。
* "beforeStatementContinuationChars": "never"如果即使下一行以[, (, /,+或开头也不会造成 ASI 危险，则不允许使用分号作为语句的结尾-。
`semi:['error','never']`

{% title h2, stylelint %} 
### 1.下载相关

1.1下载 stylelint

`npm install stylelint --save-dev`
`custom-property-no-missing-var-function //如果不是最新版本会报错`

1.2下载配置规则

官方提供两套标准
`stylelint-config-recommended //包含可能报错的rule，code format的css标准`
`stylelint-config-standard //继承于recommend，一些常见的css书写标准`
`stylelint-config-standard-scss //解析scss的包,普通的stylelint只能解析css文件`
`stylelint-config-recommended-vue //解析vue的包,如果不下载这个包会报错`
**unknown word:CssSyntaxError** 识别不了vue文件
1.3下载 "stylelint-order": "^5.0.0"  配置规则 尝试校验

### 2.以scss文件为例====>校验scss文件
下载安装：
"sass": "^1.49.7",
"sass-loader": "^7.3.1",

### 3.在package.json====>编写配置文件
`"lint:style": "stylelint \"src/**/*.(css|scss|vue)\" --fix"`
匹配src文件夹下的css/scss/vue文件

* 如果出现TypeError: Class extends value undefined is not a constructor or null #5
**解决：** 
`npm install —save-dev postcss@8`
附上[原帖地址](https://github.com/stylelint-scss/stylelint-config-standard-scss/issues/5)

**弃用,下面的方法已弃用**
### 4.下载stylelint插件，保存的时候自动修复。(插件有问题弃用)
配置：setting.json
```
	"editor.codeActionsOnSave": {
        "source.fixAll": true,
    },
  "stylelint.validate": [
        "css",
        "less",
        "postcss",
        "scss",
        "vue",
        "sass"
    ],
```

### 5.忽略文件====>stylelintignore.js
* dist
* node_modules
* package.json
ps：eslint也可以配置忽略文件。


{% note warning, 如何在在commit之前触发校验规则？使用预提交！ %}
{% title h2, 预提交 %}
**通过git hook+husky+lint-staged来实现**
1.下载husky 
`npm i husky -D --registry=https://registry.npm.taobao.org`
2.配置package.json
```
//在script下面一行配置
"husky": {
  "hooks": {
    "pre-commit": "echo \"git commit trigger husky pre-commit hook\" "
  }
}  
```
**pre-commit**：可以在commit之前执行echo \"git commit trigger husky pre-commit hook\"
3.下载lint-staged
`npm i lint-staged prettier -D --registry=https://registry.npm.taobao.org`
4.配置lint-staged,在package.json中配置
```
"lint-staged": {
    "src/**/*.{js,vue}": [
      "prettier --write",
      "eslint --cache --fix"
    ],
    "src/**/*.{vue,scss}": [
      "stylelint --fix"
    ]
}
```
{% pbg yellow , 最终模版 %}
```
"husky": {
    "hooks": {
      "pre-commit": "echo 'git commit trigger husky pre-commit hook' && lint-staged"
    }
},
"lint-staged": {
	"src/**/*.{js,vue}": [
		"prettier --write",
		"eslint --cache --fix",
	],
	"src/**/*.{vue,scss}": [
      "stylelint --fix"
  ]
}
```

本文参考链接:
[链接1](https://www.jianshu.com/p/5d7e0e744a56)  
[链接2](https://neveryu.github.io/2020/06/10/husky-lint-staged/)  

