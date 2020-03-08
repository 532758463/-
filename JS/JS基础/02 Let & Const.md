# Let & Const

知道了作用域，就很好理解 Let 和 Const 的作用了，它可以让变量声明更加完美。

### Let

#### 1. let 声明的变量拥有块级作用域

这个其实在讲作用域的时候应该就很明确了，可以看下代码：

```js
{
          let a = 1
        }
        console.log(a); //undefined
        
```

`a`变量是在代码块`{}`中使用 let 定义的，它的作用域是这个代码块内部，外部无法访问。

#### 2. let 声明的全局变量不是全局对象的属性

这就意味着，你不可以通过 window.变量名 的方式访问这些变量，而 var 声明的全局变量是 window 的属性，是可以通过 window.变量名 的方式访问的。

```js
var a = 1
        console.log(window.a); //1
        
let a = 1
        console.log(window.a); // undefined
        
```

#### 3. 用let重定义变量会抛出一个语法错误

这个很容易理解，使用 var 可以重复定义，使用 let 却不可以。

```js
var a = 1
        var a = 2
        
        console.log(a)  //2
        
```

如果是 let ，则会报错

```js
let a = 1
        let a = 2
        // VM131:1 Uncaught SyntaxError: Identifier 'a' has already been declared
        //   at <anonymous>:1:1
        
```

#### 4. let声明的变量不会进行变量提升

```js
function test () {
          console.log(a)
          var a = 1
        }
        
        test() //undefined
        
```

上述代码中，`a` 的调用在声明之前，所以它的值是 undefined，而不是 Uncaught ReferenceError。实际上因为 var 会导致变量提升，上述代码和下面的代码等同：

```js
function test () {
          var a
          console.log(a)
          a = 1
        }
        
        test() //undefined
        
```

而对于 let 而言，变量的调用是不能先于声明的，看如下代码：

```js
function test () {
          console.log(a)
          let a = 1
        }
        
        test()
        // Uncaught ReferenceError: Cannot access 'a' before initialization
        
```

在这个代码中，`a` 的调用是在声明之前，因为 let 没有发生变量提升，所有读取 a 的时候，并没有找到，而在调用之后才找到 let 对 `a` 的定义，所以按照 tc39 的定义会报错。

### Const

`const`除了具有`let`的块级作用域和不会变量提升外，还有就是它定义的是常量，在用`const`定义变量后，我们就不能修改它了，对变量的修改会抛出异常。

```js
const PI = 3.1415;
        
        console.log(PI);
        
        PI = 22;
        
        console.log(PI);
        // Uncaught TypeError: Assignment to constant variable.
        
```

这个代码块中因为对 `PI` 尝试修改，导致浏览器报错，这就说明 const 定义的变量是不能被修改的，它是只读的。聪明的同学一定会发现只读属性是不是一定要进行初始化呢？

```js
const PI
        
        PI = 3.1415
        // Uncaught SyntaxError: Missing initializer in const declaration
        
```

> [!DANGER]
> const 声明的变量必须进行初始化，不然会抛出异常 Uncaught SyntaxError: Missing initializer in const declaration。

### 练习

1. 请问下面的代码输出是什么？如何能根据 i 的顺序输出？

   ```js
   for (var i = 0; i < 3; i++) {
             setTimeout(function () {
               console.log(i);
             }, 1000);
           }
           
   ```

2. 请问下面的代码会发生什么？

   ```js
   console.log(a)
           let a = 1
           
   ```

### 阅读

1. [内存管理](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Memory_Management)