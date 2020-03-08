# Jest中的mock

## MocK

Mock函数提供的以下三种特性，在我们写测试代码时十分有用：
1. 捕获函数调用情况。
2. 设置函数返回值。
3. 改变函数的内部实现。

### jest.fn()

jest.fn()是创建Mock函数最简单的方式，如果没有定义函数内部的实现，jest.fn()会返回undefined作为返回值。


```js
test('测试jest.fn()调用', () => {
  let mockFn = jest.fn();
  let result = mockFn(1, 2, 3);

  // 断言mockFn的执行后返回undefined
  expect(result).toBeUndefined();
  // 断言mockFn被调用
  expect(mockFn).toBeCalled();
  // 断言mockFn被调用了一次
  expect(mockFn).toBeCalledTimes(1);
  // 断言mockFn传入的参数为1, 2, 3
  expect(mockFn).toHaveBeenCalledWith(1, 2, 3);
})
```
`jest.fn()`所创建的Mock函数还可以**设置返回值**，**定义内部实现**或**返回Promise对象**。


### jest.mock()

模拟数据请求。
```js
//events.js

import fetch from './fetch';

export default {
  async getPostList() {
    return fetch.fetchPostsList(data => {
      console.log('fetchPostsList be called!');
      // do something
    });
  }
}

```
event.test.js

```js
mport events from '../src/events';
import fetch from '../src/fetch';

jest.mock('../src/fetch.js');

test('mock 整个 fetch.js模块', async () => {
  expect.assertions(2);
  await events.getPostList();
  expect(fetch.fetchPostsList).toHaveBeenCalled();
  expect(fetch.fetchPostsList).toHaveBeenCalledTimes(1);
});

```
在测试代码中使用了jest.mock('../src/fetch.js')去mock整个fetch.js模块。如果注释掉，就会报错，测试用例不通过。



**为什么要使用Mock函数？**

在项目中，一个模块的方法内常常会去调用另外一个模块的方法。在单元测试中，我们可能并不需要关心内部调用的方法的执行过程和结果，只想知道它是否被正确调用即可，甚至会指定该函数的返回值。此时，使用Mock函数是十分有必要。