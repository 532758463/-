# 徐小杨工作日报

| 作者   | 日期      |
| ------ | --------- |
| 徐小杨 | 2019.8.12 |



## 一、学习心得

### Jest
#### snapshot 
快照测试。它的作用：每当你想要确保你的UI不会有意外的改变，快照测试是非常有用的工具。

>    A typical snapshot test case for a mobile app renders a UI component, takes a snapshot, then `compares` it to a reference snapshot file stored alongside the test. The test will fail if the two snapshots do not match: either the change is unexpected, or the reference snapshot needs to be updated to the new version of the UI component.

- demo.js
```js
export const generateConfig = () =>{
    return {
        server: 'http://localhost',
        port: 8080,
        domain:'localhost',
        time: new Date()
    }
}
```
- demo.test.js
```js
import { generateConfig } from "./demo";

test("测试 generateConfig 函数", () => {
    //
  expect(generateConfig()).toMatchSnapshot({
    time: expect.any(Date)
    //保证时间每次更新的值不必相等而通过测试。
  })
});
```
`yarn test` 运行后生成快照文件。

快照直接作用于呈现的数据。

<img src="D:\XiaoYnag\Note\前端\Jest\img\untitled.png" alt="untitled"  />

#### 更新快照

##### 通过命令

指示重新生成快照

```js
jest --updateSnapshot
```

如果您想限制重新生成哪些快照测试用例，则可以传递一个额外的`--testNamePattern`标志，仅为那些与该模式匹配的测试重新记录快照。

##### 交互式快照模式

```js
//命令行
Watch Usage
 › Press a to run all tests.
 › Press f to run only failed tests.
 › Press o to only run tests related to changed files.
 › Press p to filter by a filename regex pattern.
 › Press t to filter by a test name regex pattern.
 › Press u to update failing snapshots.
 › Press i to update failing snapshots interactively.
 › Press q to quit watch mode.
```

- `a` 模式：运行所有的test测试用例。
- `i` 模式：有失败的快照时，按i进入单个的运
 行模式，再按`u`进行更新。


#### 行内snapshot（内联快照）
内联快照由[Prettier](https://prettier.io/)提供支持。要使用内联快照，您必须已`prettier`在项目中安装。
```js
npm install prettier@1.18.2 --save
yarn add prettier@1.18.2
```
运行测试用例，快照生成的内容放在了测试用例中。
```js
import { generateConfig, generateAotherConfig } from "./demo";

test("测试 generateConfig 函数", () => {
  expect(generateConfig()).toMatchInlineSnapshot(
    {
      time: expect.any(Date)
    },
    `  //生成的内容
    Object {
      "domain": "localhost",
      "port": 8080,
      "server": "http://localhost",
      "time": Any<Date>,
    }
  `
  );
});
```


## 二、问题和困惑




## 三、参考资料

- [Jest官方文档](https://jestjs.io/docs/zh-Hans/snapshot-testing)
