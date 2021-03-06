# Jest中的mock



## MocK

### jest.mock()

#### 第一种方式

通过`jest.mock()`模拟真实数据请求的方式。

`fetchData.js`

```
import axios from 'axios';
export const fetchData = (fn)=>{
    return axios.get('/').then(res => res.data)
}
```

`fetchData.test.js`

```js
import {fetchData} from './fetchData';
import Axios from 'axios'

/**
 * jest.mock()的使用
 */
jest.mock('axios')

test.only('fetchData测试',()=>{
    //通过mockResolvedValue模拟返回的数据
    Axios.get.mockResolvedValue({
        data:"(function(){return '123'})()"
    })
    return fetchData().then(data =>{
        //eval() 函数可计算某个字符串，并执行其中的的 JavaScript 代码。
        expect(eval(data)).toEqual('123')
    })
})

```

测试结果：

<img src="D:\XiaoYnag\Note\前端\Jest\Mock\img\capture_20190917.bmp" alt="capture_20190917"  />



#### 第二种方式

##### 自定义模拟的方式。

- 首先创建一个`__mocks__`文件夹。
- 在`__mocks__`下创建一个与模拟的文件名一样的文件。

效果图如下：

![__mocks__](D:\XiaoYnag\Note\前端\Jest\Mock\img\__mocks__.bmp)

`__mocks__`文件夹下的`fetchData.js`:

```js
// 在本地模拟一个fetchData假请求。
export const fetchData = (fn)=>{
    return new Promise((resolved,reject)=>{
        resolved("(function(){return '123'})()")
    })
}
```



`fetchData.test.js`

```js
//模拟的fetchData
//先去模拟fetchData再去引入
jest.mock('./fetchData')
//不会去引入真实的fetchData,而是去引入__mocks__下的fetchData
import {fetchData} from './fetchData';

test.only('fetchData测试',()=>{
    return fetchData().then(data =>{
         //eval() 函数可计算某个字符串，并执行其中的的 JavaScript 代码。
        expect(eval(data)).toEqual('123')
    })
})
```

结果如下：

![自定义模拟结果](D:\XiaoYnag\Note\前端\Jest\Mock\img\自定义模拟结果.bmp)

##### 通过jest.config.js文件开启自动模拟

还可以通过修改`jest.config.js`配置文件来自动模拟。

如何生成`jest.config.js`?

通过yarn:

```js
 yarn jest --init
```

通过npx:

```js
npx jest --init
```

通过设置`jest.config.js`文件中的`automock`为`true`可以开启自动模拟的方式。

<img src="D:\XiaoYnag\Note\前端\Jest\Mock\img\jest.config.js.bmp" alt="jest.config.js" style="zoom: 67%;" />

`fetchData.js`就可以简写成如下代码：

```js
import { fetchData } from './fetchData';

test.only('fetchData测试',()=>{
    return fetchData().then(data =>{
        expect(eval(data)).toEqual('123')
    })
})
```

当`automock`为`true`时，开启自动模拟，它会自动的模拟`__mocks__`下的`fetchData`(某个文件)，不用再在代码中通过`jest.mock('./fetchData.js')`方式模拟。如果`__mocks__`



### jest.unmock()

取消对某个文件的模拟。

`fetchData.test.js`

```js
//模拟的fetchData
//先去模拟fetchData再去引入
jest.mock('./fetchData')
//取消对fetchData的模拟
jest.unmock('./fetchData')
//不会去引入真实的fetchData,而是去引入__mocks__下的fetchData
import {fetchData} from './fetchData';

test.only('fetchData测试',()=>{
    return fetchData().then(data =>{
        expect(eval(data)).toEqual('123')
    })
})

```

上述代码的测试不会通过，因为通过jest.unmock()的方式取消了对fetchData的模拟。



## 问题

当我们希望fetchData.js中的异步函数通过自动模拟的方式进行模拟，而同步函数采用原来的方式进行模拟测试。

那么如何实现呢？

真实的`fetchData.js`文件（而非`__mocks__`下的文件）：

```js
import axios from 'axios';


export const fetchData = (fn)=>{
    return axios.get('/').then(res => res.data)
}

export const getNumber = ()=> {
    return 123;
}

```

`fetchData.test.js`

```js
//模拟的fetchData
// //先去模拟fetchData再去引入
jest.mock('./fetchData')

//不会去引入真实的fetchData,而是去引入__mocks__下的fetchData。
import { fetchData } from './fetchData';

//这里通过 jest.requireActual('./fetchData')获得的getNumber是来自真正的fetchData文件。
const { getNumber } = jest.requireActual('./fetchData')

test('fetchData测试',()=>{
    return fetchData().then(data =>{
        expect(eval(data)).toEqual('123')
    })
})

test('getNumber 测试',() => {
    expect(getNumber()).toEqual(123)
})
```



