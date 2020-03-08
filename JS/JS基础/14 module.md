# module

在 ES6 之前，JS 文件之间的导入、导出是需要借助 require.js、sea.js。现在，大家可以使用 import、export 来实现原生 JavaScript 的导入、导出了。

### export

1. 导出变量或者常量

   ```js
   export const name = 'hello'
           export let addr = 'BeiJing City'
           export var list = [1, 2 , 3]
           
   ```

   或者

   ```js
   const name = 'hello'
           let addr = 'BeiJing City'
           var list = [1, 2 , 3]
           export {
           	name,
           	addr,
           	list
           }
           
   ```

2. 导出函数

   ```js
   export function say (content){
             console.log(content);
           }
           export function run (){
             console.log('run')
           }
           
   ```

   或者

   ```js
   const say = (content) => {
           	console.log(content);
           }
           let run = () => {
           	console.log('run');
           }
           export {
           	say,
           	run
           }
           
   ```

3. 导出 Object

   ```js
   export ({
            code: 0,
            message: 'success'
           })
           
   ```

   或者

   ```js
   let data = {
             code: 0,
             message: 'success'
           }
           export {
             data
           }
           
   ```

4. 导出 Class

   ```js
   class Test {
             constructor(){
               this.id = 2
             }
           }
           export {
             Test
           }
           
   ```

   或者

   ```js
   export class Test {
             constructor(){
               this.id = 2
             }
           }
           
   ```

5. 修改导出名称

   ```js
   const name = 'hello'
           let addr = 'BeiJing City'
           var list = [1, 2 , 3]
           export {
             name as cname,
             addr as caddr,
             list
           }
           
   ```

6. 设置默认导出

   ```js
   const name = 'hello'
           let addr = 'BeiJing City'
           var list = [1, 2 , 3]
           export {
             name as cname,
             addr as caddr
           }
           export default list
           
   ```

### import

1. 直接导入

   假设导出模块 A 是这样的：

   ```js
   const name = 'hello'
           let addr = 'BeiJing City'
           var list = [1, 2 , 3]
           export {
             name as cname,
             addr as caddr
           }
           export default list
           
   ```

   则导入：

   ```js
   import list, {cname, caddr} from A
           
   ```

2. 修改导入名称

   ```js
   import list, {cname as name, caddr} from A
           
   ```

3. 批量导入

   ```js
   import list, * as mod from A
           console.log(list);
           console.log(mod.cname);
           console.log(mod.caddr);
           
   ```

### 阅读

1. [Modules](https://exploringjs.com/es6/ch_modules.html)
2. [import, export, default cheatsheet](https://hackernoon.com/import-export-default-require-commandjs-javascript-nodejs-es6-vs-cheatsheet-different-tutorial-example-5a321738b50f)
3. [ECMAScript 6 modules: the final syntax](https://2ality.com/2014/09/es6-modules-final.html)

### 练习

1. 被导出的模块是否能在本模块引用？