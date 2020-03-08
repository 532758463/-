# 徐小杨工作日报

| 作者   | 日期      |
| ------ | --------- |
| 徐小杨 | 2019.9.16 |

[TOC]

## 一、工作任务完成情况
### Anydream项目
- 代码完善与测试。

### 学习任务
- 学习MocK。

下一步：继续学习jest测试。

## 二、学习心得

### MocK

模拟回调函数以及axios请求数据.

#### mock函数作用

1. 捕获函数的调用和返回结果，以及this和调用顺序。
2. 它可以让我们自由的设置返回结果。
3. 改变内部函数的实现。

#### jest.fn()

- mock.js

```js
import axios from 'axios';

export const runCallback = (callback) => {
    callback('abc')
}
export const createObject = (classItem)=> {
    new classItem();
}
export const getData = () => {
    return axios.get('/api').then(res => res.data);
}
```

- mock.test.js

```js
import { runCallback, createObject, getData } from './demo';
import axios from 'axios';
jest.mock('axios');

test('测试 runCallback',() => {
    const func = jest.fn();  //mock函数
    // const func = jest.fn(()=>{
    //     return '456'
    // });  ==>等价于 func.mockImplementation()
    //链式调用
   //模拟返回结果。 //func.mockReturnValueOnce('Dell').mockReturnValueOnce('lee').mockReturnValueOnce('hello')
    func.mockReturnValue('Dell')
    runCallback(func);
    runCallback(func);
    runCallback(func);
    // expect(func.mock.calls[0]).toEqual(['abc']);
    expect(func.mock.calls.length).toBe(3);
    console.log(func.mock)
})
// instanceof
test('测试 createObject',() => {
    const func = jest.fn();
   createObject(func);
    //mock属性
   console.log(func.mock)
})

//only只测试当前测试用例
test.only('测试 getData', async ()=>{
    //通过mockResolvedValue方法模拟返回的数据。
    axios.get.mockResolvedValue({data: 'hello'})
    await getData().then((data)=>{
        expect(data).toBe('hello')
    })
    await getData().then((data)=>{
        expect(data).toBe('hello')
    })
})
```



#### jest.spyOn()
jest.spyOn()函数同样的也能创建一个mock函数。
1. 能够捕获函数调用的情况。
2. 可以正常执行被spyOn的函数。

实际上，jest.spyOn()函数是jest.fn()的语法糖。它创建了一个和被spyOn的函数具有相同内部代码的mock函数。

- 通过`jest.mock()`的形式。

`fetchData.js`
```js
import axios from 'axios';

export default {
  async fetchPostsList(callback) {
    return axios.get('https://jsonplaceholder.typicode.com/posts').then(res => {
      return callback(res.data);
    })
  }
}
```
`event.js`

```
import fetchData from './fetchData.js';

export default {
  async getPostList() {
    return fetchData.fetchPostsList(data => {
      console.log('fetchPostsList be called!');
    });
  }
}
```

`fetchData.test.js`

```js
import event from './event'
import fetchData from './fetchData';

//模拟fetchData.js
jest.mock('./fetchData.js')
test('mock 整个 fetch.js模块', async () => {
    //验证是否执行两个断言语句，并且表示允许执行断言语句的执行个数
    expect.assertions(2);
    //等待event.js文件中getPostList方法执行完成。
    //即：等待fetchData函数的fetchPostsList被调用成功。
    await event.getPostList();
    //toHaveBeenCalled以确保模拟功能得到调用
    //确保fetchPostsList得到调用。
    expect(fetchData.fetchPostsList).toHaveBeenCalled();
    //确保模拟功能被调用的次数。
    expect(fetchData.fetchPostsList).toHaveBeenCalledTimes(1);
});
```

![1568647821684](D:\XiaoYnag\Note\前端\Jest\Mock\img\1568647821684.png)

- jest.spyOn()

```js
import event from './event'
import fetchData from './fetchData';

test('使用jest.spyOn()监控fetch.fetchPostsList被正常调用', async() => {
    expect.assertions(2);
    const spyFn = jest.spyOn(fetchData, 'fetchPostsList');
    await event.getPostList();
    expect(spyFn).toHaveBeenCalled();
    //确保模拟功能得调用次数
    expect(spyFn).toHaveBeenCalledTimes(1);
  })
```

![1568647717234](D:\XiaoYnag\Note\前端\Jest\Mock\img\1568647717234.png)

区别：

1. 通过jest.mock(()的函数并不会执行mock函数内部的方法，而通过jest.spyOn()的函数可以执行内部的函数。

2. jest.mock()执行的时间远远小于jest.spyOn()。



## 三、收获与困惑

#### 什么情况下使用不同的mock函数？

- jest.fn()常被用来进行某些有回调函数的测试；
- jest.mock()可以mock整个模块中的方法，当某个模块已经被单元测试100%覆盖时，使用jest.mock()去mock该模块，节约测试时间和测试的冗余度；
- 当需要测试某些必须被完整执行的方法时，则可以使用jest.spyOn()。



#### 如何做一个自定义的模拟呢？

- 首先创建一个__mocks__文件
- 在__mocks__下创建一个与模拟的文件名一样的文件（以上述类的测试为例子）

util.js

```js
const Util = jest.fn(()=>{
    console.log('constructor')
})

Util.prototype.a = jest.fn(()=>{
    console.log('a')
})
Util.prototype.b = jest.fn(()=>{
    console.log('b')
})
export default Util;
```


## 参考资料
- [jest_spyOn](https://jestjs.io/docs/zh-Hans/jest-object#jestspyonobject-methodname)

- [soyOn](http://www.imooc.com/article/254755)

