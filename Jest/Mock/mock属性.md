#### 使用mock函数

- jest.fn()，创建mock函数

1. 假设测试函数forEach的内部实现，forEach函数为传入的数组中的每个元素调用一次回调函数:

```js
//forEach.js
function forEach(items, callback) {
  for (let index = 0; index < items.length; index++) {
    callback(items[index]);
  }
}
```

1. 为了测试forEach函数，使用一个 mock 函数，然后检查 mock 函数的状态来确保forEach中回调函数如期调用:

```js
const mockCallback = jest.fn(x => 42 + x);//创建mock函数
forEach([0, 1], mockCallback);//调用mock函数
 
// 此 mock 函数被调用了两次
expect(mockCallback.mock.calls.length).toBe(2);//mock函数的状态
 
// 第一次调用函数时的第一个参数是 0
expect(mockCallback.mock.calls[0][0]).toBe(0);//mock函数的状态
 
// 第二次调用函数时的第一个参数是 1
expect(mockCallback.mock.calls[1][0]).toBe(1);//mock函数的状态
 
// 第一次函数调用的返回值是 42
expect(mockCallback.mock.results[0].value).toBe(42);//mock函数的状态
```

#### .mock属性

- .calls，包含mock函数被调用时的信息
- .results，包含调用mock函数时返回值的信息
- .instances，包含mock函数实例化的信息

mock 函数都具有.mock属性，mock属性保存了关于mock函数如何被调用、调用时的返回值的信息。mock 属性可以追踪每次调用时this的值。

```js
const myMock = jest.fn();//创建mock函数
 
const a = new myMock();//new实例化
const b = {};
const bound = myMock.bind(b);//bind绑定
bound();
 
console.log(myMock.mock.instances);
// [ mockConstructor{}, {} ]
```

在测试中，mock属性成员可以用于断言mock函数如何被调用、mock函数如何被实例化或mock函数返回值是什么:

```js
// 函数只被调用一次
expect(someMockFunction.mock.calls.length).toBe(1);
 
// 函数第一次调用的第一个参数是'first arg'
expect(someMockFunction.mock.calls[0][0]).toBe('first arg');
 
// 函数第一次调用的第二个参数是'second arg'
expect(someMockFunction.mock.calls[0][1]).toBe('second arg');
 
// 函数第一次调用的返回值是'return value'
expect(someMockFunction.mock.results[0].value).toBe('return value');
 
// 函数被实例化两次
expect(someMockFunction.mock.instances.length).toBe(2);
 
// 函数被第一次实例化返回的对象中，有一个 name 属性，且被设置为了 'test’ 
expect(someMockFunction.mock.instances[0].name).toEqual('test');
```

#### mock返回值

- mockReturnValueOnce()，mock函数返回指定值一次
- mockReturnValue()，mock函数返回指定值

在测试中，mock函数可以用于将测试值注入到代码内：

```js
const myMock = jest.fn();
console.log(myMock());
// undefined
 
myMock
  .mockReturnValueOnce(10)
  .mockReturnValueOnce('x')
  .mockReturnValue(true);
 
console.log(myMock(), myMock(), myMock(), myMock());
// 10, 'x', true, true
```

在使用函数的连续传递风格（CPS）的代码中，mock函数非常有效。以CPS风格编写的代码有助于避免那些需要通过复杂的中间值，来重建mock函数在真实组件中的行为，这有利于mock函数被调用之前将值直接注入到测试中：

```js
const filterTestFn = jest.fn();
 
// 第一次调用mock函数返回true，第二次调用mock函数返回false
filterTestFn.mockReturnValueOnce(true).mockReturnValueOnce(false);
 
const result = [11, 12].filter(filterTestFn);
 
console.log(result);
// [11]
console.log(filterTestFn.mock.calls);
// [ [11,0,[11,12]],[12,1,[11,12]] ]
```

#### 模块模拟

- jest.mock('axios')
- axios.get.mockResolvedValue(resp)
- axios.get.mockImplementation(() => Promise.resolve(resp))

假设有一个class从API中获取用户。class使用axios调用API，然后返回包含所有用户的data属性:

```js
// users.js
import axios from 'axios';
 
class Users {
  static all() {//静态方法
    return axios.get('/users.json').then(resp => resp.data);//返回Promise对象
  }
}
 
export default Users;
```

在不使用真实API的情况下测试all()方法，可以使用jest.mock(…)函数来自动模拟axios模块。

一旦模拟模块之后，可以为.get提供mockResolvedValue，该值返回希望测试断言的数据。实际上，说的是期望axios.get('/users.json')返回一个伪响应。

```js
// users.test.js
import axios from 'axios';
import Users from './users';
 
jest.mock('axios');
 
test('should fetch users', () => {
  const users = [{name: 'Bob'}];
  const resp = {data: users};
  axios.get.mockResolvedValue(resp);
  // 或者，可以根据用例使用以下方法:
  // axios.get.mockImplementation(() => Promise.resolve(resp))
 
  return Users.all().then(data => expect(data).toEqual(users));//测试异步代码，Promise
});
```

#### mock实现

- jest.fn()
- mockImplementation()
- mockImplementationOnce()
- mockReturnThis()

有时，超出指定返回值的功能和全面替换模拟函数的实现是有用的 —— 这可以通过jest.fn或mock函数的mockImplementationOnce 方法完成。

```js
const myMockFn = jest.fn(cb => cb(null, true));
 
myMockFn((err, val) => console.log(val));
// true
```

如果需要定义一个mock函数，mock函数通过从另一个模块创建的模拟函数的默认实现时，使用mockImplementation方法：

```js
// foo.js
module.exports = function() {
  // some implementation;
};
 
// test.js
jest.mock('../foo'); // this happens automatically with automocking
const foo = require('../foo');
 
// foo 是一个mock函数
foo.mockImplementation(() => 42);
foo();
// 42
```

当需要重新创建复杂行为的模拟功能，这样多个函数调用产生不同的结果时，请使用 mockImplementationOnce 方法︰

```js
const myMockFn = jest.fn()
  .mockImplementationOnce(cb => cb(null, true))
  .mockImplementationOnce(cb => cb(null, false));
 
myMockFn((err, val) => console.log(val));
// true
 
myMockFn((err, val) => console.log(val));
// false
```

当mocked函数用完由mockImplementationOnce定义的实现时，它将执行使用jest.fn（如果被定义）的默认实现集：

```js
const myMockFn = jest
  .fn(() => 'default')
  .mockImplementationOnce(() => 'first call')
  .mockImplementationOnce(() => 'second call');
 
console.log(myMockFn(), myMockFn(), myMockFn(), myMockFn());
// 'first call', 'second call', 'default', 'default'
```

一般对于有链式方法（因此总是需要返回this）的情况，有一个语法糖的API以.mockReturnThis()函数的形式来简化它，它也位于所有模拟器上：

```js
const myObj = {
  myMethod: jest.fn().mockReturnThis(),
};
 
// is the same as
 
const otherObj = {
  myMethod: jest.fn(function() {
    return this;
  }),
};
```

#### mock名称

- mockName()，为mock函数提供一个名称。

```js
const myMockFn = jest.fn().mockReturnValue('default') .mockImplementation(scalar => 42 + scalar).mockName('add42');
```