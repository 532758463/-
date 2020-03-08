# Typescript
[英语文档](http://www.typescriptlang.org/docs/home.html)
[TOC]
## 介绍：
Typescript是由微软开发的一款开源的编程语言。

Typescript更像后端java,c#这样的面向对象语言可以让js开发大型企业项目。

Typescript是JavaScript的超集，遵循最新的ES6,ES5规范。它扩展了ES5的语法。

谷歌也在大力支持Typescript的推广，谷歌的angular2.x+就是基于Typescript语法。
最新的Vue,React集成了Typescript。

## 安装Typescript:
```js
npm install -g typescript
```

编译（将.ts文件转换成浏览器能够解析的.js文件）：

```js
tsc  hello.ts
```

## 开发工具Vscode自动编译.ts文件
- 第一步：创建tsconfig.json文件，可以输入命令生成ts.config.json文件

```js
tsc --init
```

并按下图所示修改
![01环境配置](F:/学习笔记/JS/TS/img/01环境配置.png)

- 第二步：点击菜单   任务-运行任务   点击tsc：监视-tsconfig.json  然后就可以自动生成代码了

## 开发工具Hbuider自动编译.ts文件
1. 在最上方菜单栏，点击工具-插件安装
2. 点击下方浏览Eclipse插件市场，搜索typescript插件安装
3. 安装完成后重启编辑器，点击菜单栏工具-选项  选择编译ts文件
4. 在项目上右键点击---配置---Enable TypeScript Builder  之后再保存.ts文件时会自动在当前目录下编译出对应的js文件

