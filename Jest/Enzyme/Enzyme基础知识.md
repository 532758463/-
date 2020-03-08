# Enzyme

## 浅渲染`shallow`

```js
const wrapper = shallow(<App/>)
```

可以通过debug的方式进行调试。

```js
console.log(wrapper.debug())
```

可以专门写一个属性给测试使用,这是为了保证当我们的类名进行修改后，测试用例仍然能够对该功能进行测试。

```js
<div className="container" title="dell lee" data-test="container"></div>
```

`div.test.js`

```js
expect(wrapper.find('[data-test]="container"').length).toBe(1)
```

## jest Enzyme

```js
npm install jest-enzyme --save-dev
```

jest.config.js的setupFiles里面做配置后重新启动测试命令。

配置如下：

```js
"setupFilesAfterEnv": ['./node_modules/jest-enzyme/lib/index.js'],
```

`toExist()`

```js
expect(wrapper.find('[data-test]="container"')).toExist()
```

`toHaveProp()`

```js
expect(wrapper.find('[data-test]="container"')).toHaveProp('title','dell lee')
```

单元测试使用shallow,集成测试适合使用`mount`

## mount

当对一堆组件进行测试时，使用`mount`进行测试比较合适。

```js
const wrapper = mount(<App/>)
```

### snapshot进行测试

`toMatchSnapshot()`

```js
expect(wrapper).toMatchSnapshot()
```

当对某个组件进行修改时，可以通过snapshot来进行约束，只要这个组件发生改动，测试就不会通过。确实需要j进行改动的话，按w进入交互模式按u更新快照。

## 安装

```js
npm i --save-dev enzyme enzyme-adapter-react-16
```

## 通用函数的封装

通用函数的的文件夹为`utils`:

![enzyme](D:\XiaoYnag\Note\前端\Jest\Enzyme\img\enzyme.bmp)

### Enzyme的环境配置封装

#### 针对React通过脚手架搭建的方式：

首先需要弹射出`React`默认的配置项：

```node
npm run eject
```

`yarn`的方式：

```node
yarn eject
```

运行命令，会弹射出React脚手架的默认配置项，如果重新运行命令`yarn test`会报如下的错误：

```js
[BABEL]  Cannot find module '@babel/plugin-transform-react-jsx' 
```

如何解决呢？

需要删除之前的`node_modules`文件夹，然后通过`npm install`或者`yarn`的方式重新下载就可以解决`Babel`类型的报错。

#### 封装`Enzyme`的配置，这样就不需要每个测试文件引入该配置内容。

`testSetup.js`

```js
import Enzyme from 'enzyme';
import Adapter from 'enzyme-adapter-react-16';


Enzyme.configure({ adapter: new Adapter() });
```



在`jest.config.js`中：

```js
 setupFilesAfterEnv: [
    '<rootDir>/src/utils/testSetup.js'
  ],
```

然后后面其他的文件中就不用再引入`testSetup.js`中的配置内容了。

### 封装常用的匹配

`testUtil.js`

```js
export const findTestWrapper = (wrapper,tag)=>{
    return wrapper.find(`[data-test="${tag}"]`)
}
```

`Header.js`

```js
import {findTestWrapper} from '../../../../utils/testUtils'
it('Header组件包含input框', () => {
    const wrapper = shallow(<Header/>)
    // const inputElem = wrapper.find("[data-test='input']")
    const inputElem = findTestWrapper(wrapper,'input')
    expect(inputElem.length).toBe(1);
});
```

## 资料

[eject后的问题](https://github.com/facebook/create-react-app/issues/6099)

