# Iterator

### Iterable

> 处理集合中的每个项是很常见的操作。JavaScript 提供了许多迭代集合的方法，从简单的for循环到map()和filter()。迭代器和生成器将迭代的概念直接带入核心语言，并提供了一种机制来自定义for…of循环的行为。

如果对 MDN 这个描述理解不是很到位的话，可以看下接下来这个小示例：

```js
let authors = {
          allAuthors: {
            fiction: [
              'Agatha Christie',
              'J. K. Rowling',
              'Dr. Seuss'
            ],
            scienceFiction: [
              'Neal Stephenson',
              'Arthur Clarke',
              'Isaac Asimov',
              'Robert Heinlein'
            ],
            fantasy: [
              'J. R. R. Tolkien',
              'J. K. Rowling',
              'Terry Pratchett'
            ]
          }
        }
        
```

这个数据结构是汇总了所有作者，每个作者按创作性质进行了分类。如果我们想获取所有作者的名单，该怎么做呢？

```js
for (let author of authors) {
          console.log(author)
        }
        
```

> [!DANGER]
> 你发现这个遍历遇到了报错：Uncaught TypeError: authors is not iterable

有的同学会说可以这样做：

```js
for (let key in authors) {
          let r = []
          for (let k in authors[key]) {
            r = r.concat(authors[key][k])
          }
          console.log(r)
          // ["Agatha Christie", "J. K. Rowling", "Dr. Seuss", "Neal Stephenson", "Arthur Clarke", "Isaac Asimov", "Robert Heinlein", "J. R. R. Tolkien", "J. K. Rowling", "Terry Pratchett"]
        }
        
```

这个做法确实可以，实际上也是手动实现的遍历加数据合并，这其实不算是遍历。这个小节就是讲述如何给这种自定义的数据结构进行遍历。

### Iterator

Iterator 就是 ES6 中用来实现自定义遍历的接口，按照上述的示例，我们来实现下这个接口：

```js
authors[Symbol.iterator] = function () {
          let allAuthors = this.allAuthors
          let keys = Reflect.ownKeys(allAuthors)
          let values = []
          return {
            next () {
              if (!values.length) {
                if (keys.length) {
                  values = allAuthors[keys[0]]
                  keys.shift()
                }
              }
              return {
                done: !values.length,
                value: values.shift()
              }
            }
          }
        }
        
```

这个代码在数据结构上部署了 Iterator 接口，我们就可以用 for…of 来遍历代码了：

```js
for (let value of authors) {
          console.log(`${value}`)
        }
        
```

从代码上我们看到了自定义遍历器的强大，但是我们怎么理解 Iterator 呢？首先，要理解几个概念：可迭代协议和迭代器协议。

**1. 迭代器协议**

| 属性 | 值                                                           | 必选 |
| ---- | ------------------------------------------------------------ | ---- |
| next | 返回一个对象的无参函数，被返回对象拥有两个属性：done 和 value | Y    |

这是两个概念：可迭代协议、迭代器协议。通俗的讲，迭代器协议要求符合以下条件：

1. 首先，它是一个对象
2. 其次，这个对象包含一个无参函数 next
3. 最后，next 返回一个对象，对象包含 done 和 value 属性。其中 done 表示遍历是否结束，value 返回当前遍历的值。

> [!DANGER]
> 如果 next 函数返回一个非对象值（比如false和undefined) 会展示一个 TypeError (“iterator.next() returned a non-object value”) 的错误

**2. 可迭代协议**

可迭代协议允许 JavaScript 对象去定义或定制它们的迭代行为, 例如（定义）在一个 for…of 结构中什么值可以被循环（得到）。一些内置类型都是内置的可迭代类型并且有默认的迭代行为, 比如 Array or Map, 另一些类型则不是 (比如Object) 。

为了变成可迭代对象， 一个对象必须实现 @@iterator 方法, 意思是这个对象（或者它原型链 prototype chain 上的某个对象）必须有一个名字是 Symbol.iterator 的属性:

| 属性              | 值                                               | 必选 |
| ----------------- | ------------------------------------------------ | ---- |
| [Symbol.iterator] | 返回一个对象的无参函数，被返回对象符合迭代器协议 | Y    |

如果让一个对象是可遍历的，就要遵守可迭代协议，该协议要求对象要部署一个以 Symbol.iterator 为 key 的键值对，而 value 就是一个无参函数，这个函数返回的对象要遵守迭代器协议。

### Generator

熟悉了 Generator 之后，发现它是天然满足可迭代协议的。上述的代码我们可以用 Generator 来实现：

```js
authors[Symbol.iterator] = function * () {
          let allAuthors = this.allAuthors
          let keys = Reflect.ownKeys(allAuthors)
          let values = []
          while (1) {
            if (!values.length) {
              if (keys.length) {
                values = allAuthors[keys[0]]
                keys.shift()
                yield values.shift()
              } else {
                return false
              }
            } else {
              yield values.shift()
            }
          }
        }
        
```

同一个场景，同一个数据结构，写法确实不同的，利用 Generator 就不再需要显示的写迭代协议了（next方法和包含 done、value 属性的返回对象）。

### 练习

1. 什么是自定义遍历，如果有复杂的数据结构会使用自定义遍历了吗？
2. 什么是迭代协议、可迭代协议？
3. Generator 和 Iterator 的关联关系理解了吗？

### 阅读

1. [A Simple Guide to ES6 Iterators in JavaScript with Examples](https://codeburst.io/a-simple-guide-to-es6-iterators-in-javascript-with-examples-189d052c3d8e)
2. [ES6 迭代器：Iterator, Iterable 和 Generator](https://harttle.land/2018/09/29/es6-iterators.html)
3. [ES6 Iterators and Generators in Practice](http://www.zsoltnagy.eu/es6-iterators-and-generators-in-practice/)
4. [ES6 Generators and Iterators: a Developer’s Guide](https://www.sitepoint.com/ecmascript-2015-generators-and-iterators/)