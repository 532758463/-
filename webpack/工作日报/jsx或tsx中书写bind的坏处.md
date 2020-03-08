### React的render中组件上使用bind和箭头函数的坏处？

A `bind` call or [arrow function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions) in a JSX prop will create a brand new function on every single render. This is bad for performance, as it may cause unnecessary re-renders if a brand new function is passed as a prop to a component that uses reference equality check on the prop to determine if it should update. 



当使用bind或者箭头函数在JSX的prop里，将会导致每次渲染时会创建一个全新的函数。这对于页面的展示是不好的。

在 render 中使用箭头函数或绑定可能会导致子组件重新渲染，即使 state 并没有改变。

避免在 render 中使用箭头函数和绑定。否则可能会打破 shouldComponentUpdate 和 PureComponent 的性能优化。

避免在JSX或tsx的组件属性上使用bind或者箭头函数的原因：

1. 每次执行 `render` 方法时都会生成一个新的匿名函数对象，这样就会对垃圾回收器造成负担 。
2.  属性中的箭头函数会影响渲染过程：当你使用了 `PureComponent`，或者自己实现了 `shouldComponentUpdate` 方法，使用对象比较的方式来决定是否要重新渲染组件，那么组件属性中的箭头函数就会让该方法永远返回真值，引起不必要的重复渲染。 

```tsx
import * as React from 'react';
import { App } from './App';
import axios from 'axios'

 interface IMainProps{
    app: App;
}
export class Main extends React.Component<IMainProps, {}>{
   
    handleCilck=async()=>{
      const a = await axios.get('http://www.dell-lee.com/react/api/demo.json')
      console.log(a)
    }
    public render(): JSX.Element{
        return (
            //可以使用一种新的，且更简短的语法来声明 Fragments,它不支持 key 或属性
            <>
                <h1 onClick={this.handleCilck}>Hello main</h1>
            </>
        );
    }
}
```





[中文资料--很好的一篇文章]( http://shzhangji.com/cnblogs/2018/09/14/is-it-necessary-to-apply-eslint-jsx-no-bind-rule/ )

[jsx-bind-github]( https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-no-bind.md )

[no-bind]( https://maarten.mulders.it/2017/07/no-bind-or-arrow-functions-in-in-jsx-props-why-how/ )

[ts]( https://segmentfault.com/a/1190000019489612 )

[<></>]( https://zh-hans.reactjs.org/docs/fragments.html#___gatsby )

