# Jest中的mock

## Timer Mocks

原生timer的定时器函数（如：setTimeout,setInetrval,clearTimeout,clearInterval）并不是很方便的测试，因为这些函数需要等待相应的延时。而我们在很多时候是不想等待这个延时的，那么如何解决呢？这时候通过`Timer Mocks` ` 来解决该问题。

### 存在问题

`timer.js`

```js
export default (callback) => {
    setTimeout(() => {
        callback();
        setTimeout(()=>{
            callback(); 
        },3000)
    },3000)
}
```

`timer.test.js`

```js
import timer from "./timer";

test("测试 timer", () => {
  timer(()=> {
    expect(2).toBe(2);
  })
})
```



由于setTimeout函数是异步的函数，所以它会直接的跳过断言，运行用例成功，这显然是不行的。

这里通过回调函数的方式进行解决。

`timer.test.js`

```js
import timer from "./timer";

test("测试 timer", (done) => {
  timer(()=> {
    expect(2).toBe(2)
     done()
  })
})
```

执行结果：

![timer](D:\XiaoYnag\Note\前端\Jest\Mock\img\timer.png)

那么如何解决这种延时等待的问题呢？

### 解决方法

#### jest.useFakeTimers()

可以通过`jest.useFakeTimers()`的方式模拟定时器函数，通过mock函数可以模拟setTimeout和其他的定时器函数。

#####  jest.runAllTimers()

与`jest.useFakeTimers()`成对使用。

当需要在一个文件或一个describe块中运行多次测试，可以在每次测试前手动添加`jest.useFakeTimers();`，或者在`beforeEach`中添加。 如果不这样做的话将导致内部的定时器不被重置。

```js
import timer from "./timer";

beforeEach(()=>{
   //模拟定时器函数。
  jest.useFakeTimers();
})

test("测试 timer", () => {
  const fn = jest.fn();
  timer(fn);
  //“快进”时间使得所有的定时器回调被执行
  jest.runAllTimers();
  //确保模拟功能得到2次调用
  expect(fn).toHaveBeenCalledTimes(2);
})

```

执行结果：

![runAlltime](D:\XiaoYnag\Note\前端\Jest\Mock\img\runAlltime.png)

由以上结果可以看出通过上述的方法可以解决延时的问题。

##### jest.runOnlyPendingTimers()

与`jest.useFakeTimers()`成对使用。

在某些场景下可能还需要“循环定时器”——在定时器的callback函数中再次设置一个新定时器。 对于这种情况，如果将定时器一直运行下去那将陷入死循环，所以在此场景下不应该使用`jest.runAllTimers()` ，而是应该使用 `jest.runOnlyPendingTimers()`。

`timer.test.js`

```js
test("测试 timer", () => {
  const fn = jest.fn();
  timer(fn);
 
  //运行处于队列中的timers
  jest.runOnlyPendingTimers();
  //确保模拟功能得到调用次数
  expect(fn).toHaveBeenCalledTimes(1);
})
```

执行结果如下：

![readOnlyPendingTimers](D:\XiaoYnag\Note\前端\Jest\Mock\img\readOnlyPendingTimers.bmp)



##### advanceTimersByTime

以上两种方式都可以解决延时的问题，不过我们还可以通过快进时间的方式进行解决。这个方法是从从**22.0.0**版本的Jest开始，`runTimersToTime`被重命名为`advanceTimersByTime`。

timer.test.js

快进3000毫秒，setTimeout函数被调用一次。

```js
import timer from "./timer";

beforeEach(()=>{
  jest.useFakeTimers();
})

test("测试 timer", () => {
  const fn = jest.fn();
  timer(fn);
  //快进3000毫秒
  jest.advanceTimersByTime(3000);
  //确保模拟功能得到调用次数
  expect(fn).toHaveBeenCalledTimes(1);
})
```



快进6000毫秒，setTimeout函数被调用两次。

```js
import timer from "./timer";

beforeEach(()=>{
  jest.useFakeTimers();
})
test("测试 timer", () => {
  const fn = jest.fn();
  timer(fn);
  //快进3000毫秒
  jest.advanceTimersByTime(6000);
  //确保模拟功能得到调用次数
  expect(fn).toHaveBeenCalledTimes(2);
})
```

```js
import timer from "./timer";

beforeEach(()=>{
  jest.useFakeTimers();
})

test("测试 timer", () => {
  const fn = jest.fn();
  timer(fn);
  //期望有几个断言，可以对断言语句个数进行限制。
  // expect.assertions(1);
  // 在这个时间点，定时器的回调不应该被执行
  // expect(callback).not.toBeCalled();
  jest.advanceTimersByTime(3000);
  //确保模拟功能得到调用次数
  expect(fn).toHaveBeenCalledTimes(1);
  jest.advanceTimersByTime(3000);
  expect(fn).toHaveBeenCalledTimes(2);
})
```



# 参考资料

[Timer Mock](https://jestjs.io/docs/zh-Hans/timer-mocks)

