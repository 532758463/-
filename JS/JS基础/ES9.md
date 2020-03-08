# ES9

### for await of

我们知道 for…of 是同步运行的，有时候一些任务集合是异步的，那这种遍历怎么办呢？

```js
function Gen (time) {
        return new Promise(function (resolve, reject) {
          setTimeout(function () {
            resolve(time)
          }, time)
        })
      }
      
      async function test () {
        let arr = [Gen(2000), Gen(100), Gen(3000)]
        for (let item of arr) {
          console.log(Date.now(), item.then(console.log))
        }
      }
      
      test()
      // 1560090138232 Promise {<pending>}
      // 1560090138234 Promise {<pending>}
      // 1560090138235 Promise {<pending>}
      // 100
      // 2000
      // 3000
      
```

这里写了几个小任务，分别是 2000ms 、100ms、3000ms后任务结束。在上述遍历的过程中可以看到三个任务是同步启动的，然后输出上也不是按任务的执行顺序输出的，这显然不太符合我们的要求。

聪明的同学一定能想起来 await 的作用，它可以中断程序的执行直到这个 Promise 对象的状态发生改变，我们修改上面的代码：

```js
function Gen (time) {
        return new Promise(function (resolve, reject) {
          setTimeout(function () {
            resolve(time)
          }, time)
        })
      }
      
      async function test () {
        let arr = [Gen(2000), Gen(100), Gen(3000)]
        for (let item of arr) {
          console.log(Date.now(), await item.then(console.log))
        }
      }
      
      test()
      // 2000
      // 1560091834772 undefined
      // 100
      // 1560091836774 undefined
      // 3000
      // 1560091836775 undefined
      
```

从返回值看确实是按照任务的先后顺序进行的，其中原理也有说明是利用了 await 中断程序的功能。不过，在 ES9 中也可以用 for…await…of 的语法来操作：

```js
function Gen (time) {
        return new Promise(function (resolve, reject) {
          setTimeout(function () {
            resolve(time)
          }, time)
        })
      }
      
      async function test () {
        let arr = [Gen(2000), Gen(100), Gen(3000)]
        for await (let item of arr) {
          console.log(Date.now(), item)
        }
      }
      
      test()
      // 1560092345730 2000
      // 1560092345730 100
      // 1560092346336 3000
      
```

从这个结果来看和第二种写法效果差不多，但是工作原理确完全不同，重点观察下输出的时间（Chrome Console）, 第二种写法是代码块中有 await 导致等待 Promise 的状态而不再继续执行；第三种写法是整个代码块都不执行，等待 arr 当前的值（Promise状态）发生变化之后，才执行代码块的内容。

回想我们之前给数据结构自定义遍历器是同步的，如果想定义适合 for…await…of 的异步遍历器该怎么做呢？答案是 Symbol.asyncIterator。

```js
let obj = {
        count: 0,
        Gen (time) {
          return new Promise(function (resolve, reject) {
            setTimeout(function () {
              resolve({ done: false, value: time })
            }, time)
          })
        },
        [Symbol.asyncIterator] () {
          let self = this
          return {
            next () {
              self.count++
              if (self.count < 4) {
                return self.Gen(Math.random() * 1000)
              } else {
                return Promise.resolve({
                  done: true,
                  value: ''
                })
              }
            }
          }
        }
      }
      
      async function test () {
        for await (let item of obj) {
          console.log(Date.now(), item)
        }
      }
      // 1560093560200 649.3946561938179
      // 1560093560828 624.6310222512955
      // 1560093561733 901.9497480464518
      
```

### Promise.prototype.finally()

Promise.prototype.finally() 方法返回一个Promise，在promise执行结束时，无论结果是fulfilled或者是rejected，在执行then()和catch()后，都会执行finally指定的回调函数。这为指定执行完promise后，无论结果是fulfilled还是rejected都需要执行的代码提供了一种方式，避免同样的语句需要在then()和catch()中各写一次的情况。

**基本语法**

> p.finally(onFinally);

> p.finally(function() {
> // 返回状态为(resolved 或 rejected)
> });参数

**示例**

```js
let connection;
      db.open()
      .then(conn => {
          connection = conn;
          return connection.select({ name: 'Jane' });
      })
      .then(result => {
          // Process result
          // Use `connection` to make more queries
      })
      ···
      .catch(error => {
          // handle errors
      })
      .finally(() => {
          connection.close();
      });
      
```

### Object Rest Spread

前面在讲 function 的 Rest & Spread 方法，忘却的同学可以去复习下。在 ES9 新增 Object 的 Rest & Spread 方法，直接看下示例：

```js
const input = {
        a: 1,
        b: 2
      }
      
      const output = {
        ...input,
        c: 3
      }
      
      console.log(output) // {a: 1, b: 2, c: 3}
      
```

这块代码展示了 spread 语法，可以把 input 对象的数据都拓展到 output 对象，这个功能很实用。

我们再来看下 Object rest 的示例：

```js
const input = {
        a: 1,
        b: 2,
        c: 3
      }
      
      let { a, ...rest } = input
      
      console.log(a, rest) // 1 {b: 2, c: 3}
      
```

当对象 key-value 不确定的时候，把必选的 key 赋值给变量，用一个变量收敛其他可选的 key 数据，这在之前是做不到的。

### RegExp Updates

**（1）s (dotAll) flag**

正则表达式中，点（.）是一个特殊字符，代表任意的单个字符，但是有两个例外。一个是四个字节的 UTF-16 字符，这个可以用u修饰符解决；另一个是行终止符（line terminator character）。

- U+000A 换行符（\n）
- U+000D 回车符（\r）
- U+2028 行分隔符（line separator）
- U+2029 段分隔符（paragraph separator）

```js
console.log(/foo.bar/.test('foo\nbar')) // false
      console.log(/foo.bar/s.test('foo\nbar')) // true
      
```

在 ES5 中我们都是这么解决的：

```js
console.log(/foo[^]bar/.test('foo\nbar')) // true
      // or
      console.log(/foo[\s\S]bar/.test('foo\nbar')) // true
      
```

那如何判断当前正则是否使用了 dotAll 模式呢？

```js
const re = /foo.bar/s // Or, `const re = new RegExp('foo.bar', 's');`.
      console.log(re.test('foo\nbar')) // true
      console.log(re.dotAll) // true
      console.log(re.flags) // 's'
      
```

> [!TIP]
> 记住一句话就可以理解 dotAll 模式：它让 . 名副其实。

**（2）named capture groups**

我们在写正则表达式的时候，可以把一部分用()包裹起来，被包裹起来的这部分称作“分组捕获”。

```js
console.log('2019-06-07'.match(/(\d{4})-(\d{2})-(\d{2})/))
      // ["2019-06-07", "2019", "06", "07", index: 0, input: "2019-06-07", groups: undefined]
      
```

这个正则匹配很简单，按照 match 的语法，没有使用 g 标识符，所以返回值第一个数值是正则表达式的完整匹配，接下来的第二个值到第四个值是分组匹配（2019,06,07）。

此外 match 返回值还有几个属性，分别是 index、input、groups。

> [!NOTE]
>
> 1. index [匹配的结果的开始位置]
> 2. input [搜索的字符串]
> 3. groups [一个捕获组数组 或 undefined（如果没有定义命名捕获组）]

我们通过数组来获取这些捕获：

```js
let t = '2019-06-07'.match(/(\d{4})-(\d{2})-(\d{2})/)
      console.log(t[1]) // 2019
      console.log(t[2]) // 06
      console.log(t[3]) // 07
      
```

上文中重点看下 groups 的解释，这里提到了命名捕获组的概念，如果没有定义 groups 就是 undefined。很明显，我们上述的返回值就是 undefined 间接说明没有定义命名捕获分组。那什么是命名捕获分组呢？

```js
console.log('2019-06-07'.match(/(?<year>\d{4})-(?<month>\d{2})-(?<day>\d{2})/))
      // ["2019-06-07", "2019", "06", "07", index: 0, input: "2019-06-07", groups: {…}]
      
```

这段代码的返回值 groups 已经是 Object 了，具体的值是：

```js
groups: {year: "2019", month: "06", day: "07"}
      
```

这个 Object 的 key 就是正则表达式中定义的，也就是把捕获分组进行了命名。想获取这些捕获可以这样做：

```js
let t = '2019-06-07'.match(/(?<year>\d{4})-(?<month>\d{2})-(?<day>\d{2})/)
      // ["2019-06-07", "2019", "06", "07", index: 0, input: "2019-06-07", groups: {…}]
      console.log(t.groups.year) // 2019
      console.log(t.groups.month) // 06
      console.log(t.groups.day) // 07
      
```

**（3）Lookbehind Assertions**

在 ES9 之前 JavaScript 正则只支持先行断言，不支持后行断言。简单复习下先行断言的知识：

```js
let test = 'hello world'
      console.log(test.match(/hello(?=\sworld)/))
      // ["hello", index: 0, input: "hello world", groups: undefined]
      
```

这段代码要匹配后面是 world 的 hello，但是反过来就不成：

```js
let test = 'world hello'
      console.log(test.match(/hello(?=\sworld)/))
      // null
      
```

比如我们想判断前面是 world 的 hello，这个代码是实现不了的。在 ES9 就支持这个后行断言了：

```js
let test = 'world hello'
      console.log(test.match(/(?<=world\s)hello/))
      // ["hello", index: 6, input: "world hello", groups: undefined]
      
```

(?<…)是后行断言的符号，(?..)是先行断言的符号，然后结合 =(等于)、!(不等)、\1(捕获匹配)。

**（4）Unicode Property Escapes**

了解这个新的知识点，需要对文本的编码非常熟悉，不然意识不到这个功能的意义。对于文本的编码需要了解两个概念：字符编码和文件编码。字符编码包括 ASCII 和 Unicode，文件编码包括 UTF-8、GBK等。字符编码和文件编码的关系可以用一句话来概括：文件编码和字符编码没有关系，也就是说即使指定了文件编码，字符变也可以灵活选择而不受任何限制。

现在主要讲述下 Unicode 的知识点，方便快速了解 ES9 这个新特性。根据 Unicode 规范，每一个 Unicode 字符除了有唯一的码点，还具有其它属性，它们是：Unicode Property、Unicode Block、Unicode Script，下面的图粗略说明了这三者的关系。

![img](../../../../software/toolsSoft/imgs/unicode.jpg)

- **Unicode Property**

  它按照字符的功能对字符进行分类，一个字符只能属于一个 Unicode Property。也就是说 Property 并不关心字符所属的语言，只关心字符的功能。

  可以将Unicode property 理解为了下字符组，将小写 p 改成大写，就是该字符组的排除型字符组。想想看 `\d` 匹配 0-9 这个字符组，而`\D` 匹配 0-9 以外的字符组。

  ```js
  let input = 'abcdAeCd中国'
        console.log(input.match(/\p{L}/ug))
        // ["a", "b", "c", "d", "A", "e", "C", "d", "中", "国"]
        
  ```

  这段代码的含义是在输入中匹配所有的字符（不限语言），这里使用的是 Unicode Property：{L}，这个属性的含义是任何语言的任何字母。它有点等同于

  ```js
  let input = 'abcdAeCd中国'
        console.log(input.match(/./sg))
        
  ```

  - {Ll} [任何具有大写字母的小写字母]

  - {N} [任何语言下的数字]

    更多的 Unicode Property 请查阅 **[官网](https://www.regular-expressions.info/unicode.html)**

- **Unicode Script**

  按照字符所属的书写系统来划分字符，它一般对应某种语言。比如 \p{Script=Greek} 表示希腊语，\p{Script=Han} 表示汉语。

  ```js
  let input = `I'm chinese!我是中国人`
        console.log(input.match(/\p{Script=Han}+/u))
        // ["我是中国人", index: 12, input: "I'm chinese!我是中国人", groups: undefined]
        
  ```

  如果不适用这个新功能点，在 ES9 之前大概只能这样做：

  ```js
  let input = `I'm chinese!我是中国人`
        console.log(input.match(/[\u4e00-\u9fa5]+/))
        // ["我是中国人", index: 12, input: "I'm chinese!我是中国人", groups: undefined]
        
  ```

  虽然不同的写法看上去结果一样，然而时光飞逝，Unicode 在2017年6月发布了10.0.0版本。在这20年间，Unicode 添加了许多汉字。比如 Unicode 8.0 添加的 109 号化学元素「鿏（⿰⻐麦）」，其码点是 9FCF，不在这个正则表达式范围中。而如果我们期望程序里的`/[\u4e00-\u9fa5]/`可以与时俱进匹配最新的 Unicode 标准，显然是不现实的事情。现在只需要在 [Unicode Scripts](https://www.regular-expressions.info/unicode.html) 找到对应的名称即可，而不需要自己去计算所有对应语言字符的的 Unicode 范围。

- **Unicode Block**

  将 Unicode 字符按照编码区间进行划分，所以每一个字符都只属于一个 Unicode Block，举例说明：

  - \p{InBasic_Latin}: U+0000–U+007F
  - \p{InLatin-1_Supplement}: U+0080–U+00FF
  - \p{InLatin_Extended-A}: U+0100–U+017F
  - \p{InLatin_Extended-B}: U+0180–U+024F

  > [!WARNING]
  > 目前 JavaScript RegExp 还不支持 Unicode Block

### 思考

1. 利用 Object spread 的语法实现一个对象拷贝的方法。
2. Object rest 在实际业务中有什么应用呢？
3. 如果想后向引用分组捕获的内容该怎么做呢？
4. 如果想后向引用命名分分组捕获内容又该怎么做呢？

### 练习

1. 请把 ‘$foo %foo foo’ 字符串中前面是 $ 符号的 foo 替换成 bar。
2. 请提取 ‘$1 is worth about ¥123’ 字符中的美元数是多少。

### 阅读

1. [Asynchronous iteration](https://exploringjs.com/es2018-es2019/ch_asynchronous-iteration.html#for-await-of-and-rejections)
2. [Promise.prototype.finally()](http://2ality.com/2017/07/promise-prototype-finally.html)
3. [Upcoming regular expression features](https://developers.google.com/web/updates/2017/07/upcoming-regexp-features)
4. [JavaScript 正则表达式匹配汉字](https://zhuanlan.zhihu.com/p/33335629)

### reference

1. [当 async/await 遇上 forEach](http://objcer.com/2017/10/12/async-await-with-forEach/)
2. [asynchronous iteration](http://2ality.com/2016/10/asynchronous-iteration.html)
3. [JavaScript loops - how to handle async/await](https://lavrton.com/javascript-loops-how-to-handle-async-await-6252dd3c795/)
4. [Asynchronous iteration标准](https://exploringjs.com/es2018-es2019/ch_asynchronous-iteration.html#for-await-of-and-rejections)
5. [JavaScript: What’s new in ECMAScript 2018 (ES2018)?](https://medium.com/front-end-weekly/javascript-whats-new-in-ecmascript-2018-es2018-17ede97f36d5)
6. [Promise.prototype.finally()](http://2ality.com/2017/07/promise-prototype-finally.html)
7. [Upcoming regular expression features](https://developers.google.com/web/updates/2017/07/upcoming-regexp-features)
8. [UnicodeMatchProperty](https://tc39.es/proposal-regexp-unicode-property-escapes/#sec-runtime-semantics-unicodematchproperty-p)
9. [即将到来的正则表达式新特性](https://juejin.im/post/59683f98f265da6c4f34eec6)