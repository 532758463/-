
| 作者 | 日期 |
| -- | -- |
| 徐小杨 | 2019.8.29 |

[TOC]


## 一、学习心得
### Typescript
#### 类
#####  readonly修饰符

```ts
class UserInfo {
    public readonly name: string
    constructor(name: string) {
        this.name = name
    }
}
const userInfo = new UserInfo('lison')
console.log(userInfo)
// userInfo.name = 'hah'  //err 不可修改，只可读
```
##### 参数属性
参数属性：就是在constructor的参数前加修饰符

public
```ts
class A {
    constructor(public name: string){
    }
}

const a1 = new A('lison')  //A { name: 'lison' }
console.log(a1)
```
private
```
class A {
    constructor(private name: string){

    }
}

const a1 = new A('lison')
console.log(a1.name) //error
```
protected
```ts
class A {
    constructor(protected name: string){

    }
}

const a1 = new A('lison')
console.log(a1.name) //error
//属性“name”受保护，只能在类“A”及其子类中访问。
```

##### 静态属性
存在于类本身上面而不是类的实例上。
```ts
class Partent {
    public static age:number = 18
    public static getAge() {
        return Partent.age
    }
    constructor(){}
}

const p =new Partent()
console.log(p.age)  //age是静态属性，实例不能访问静态属性
console.log(Partent.age) //通过自己的类名可以获取到静态属性
console.log(Partent.getAge())
```
静态属性不能通过实例访问，可以通过自身类明访问。

##### 存取器
TypeScript支持通过 `getters/setters` 来截取对对象成员的访问。 能有效的控制对对象成员的访问。

存取器要求将编译器设置为输出 ECMAScript 5 或更高.
```ts
class Info {
    public name: string
    public age: number
    private _infoStr: string
    constructor(name: string,age?:number){
        this.name = name
        this.age = age
    }
    get infoStr() {
        return `getter:${this._infoStr}`
    }
    set infoStr(value) {
        console.log(`setter:${value}`)
        this._infoStr = value
    }
}
const info = new Info('lison',18)
info.infoStr = 'lison: 18'  //set  setter: lison: 18
console.log(info.infoStr)   //get  getter:lison: 18
```

##### 抽象类
`abstract` 关键字是用于定义抽象类和在抽象类内部定义抽象方法。

抽象类中的抽象方法不包含具体实现并且必须在派生类中实现。

抽象方法必须包含 `abstract` 关键字并且可以包含访问修饰符。
```ts
//抽象类
abstract class People {
    constructor(public name: string) {
       this.name = name
    }
    public abstract printName():void
}

// const p1 = new People()  //error 不能创建一个抽象类的实例
class Man extends People {
    constructor(name:string) {
        super(name)
        this.name = name
    }
    public printName() {
        console.log(this.name)
    }
    public say(){
        console.log('say')
    }
}
let m: People  //允许创建一个对抽象类型的引用
m = new Man('lucy')
m.printName() //lucy
//m.say()  //error  类型“People”上不存在属性“say”。
```

setter、getter在抽象类中的用法
```ts
abstract class A {
    public abstract name: string
    abstract get insideName(): string
    abstract set insideName(value: string)
}

class B extends A {
    public name: string
    public insideName: string
}
```

## 二、问题和困惑
**如何获取对象里面的Symbol类型的属性?**

- Object.getOwnPropertySymbols(obj) 获得对象上的Symbol属性。
- Reflect.ownKeys(obj) 获得对象上的属性以及Symbol属性。
```ts
const s3 = Symbol('name')
const obj2 = {
    [s3]: 'ly',
    age: 18,
    sex: 'nan'
}
console.log(obj2)  //{ [Symbol(name)]: 'ly' }
obj2[s3] = 'hh'
// obj2.s3 = 'xx'   //不能通过该方式修改属性值
console.log(obj2)

for(const key in obj2){
    console.log(key)
}
console.log(Object.keys(obj2))  //[ 'age', 'sex' ]   Symbol类型的属性不能被打印出来

console.log(Object.getOwnPropertyNames(obj2)) //[ 'age', 'sex' ]

console.log(JSON.stringify(obj2))  //{"age":18,"sex":"nan"}

//如何获取对象里面的Symbol类型的属性
console.log(Object.getOwnPropertySymbols(obj2))  //[ Symbol(name) ]
console.log(Reflect.ownKeys(obj2))   //[ 'age', 'sex', Symbol(name) ]
```

**类里面的静态属性存在哪儿?**

它在constructor函数里面.
属于静态部分,因此当new时,不会实例化该部分.
```
class Animal {
    move(distance) {
        console.log(`Animal moved ${distance}`)
    }
	static n =5
}
```
结果:

```ts
Animal.prototype
{constructor: ƒ, move: ƒ}
constructor: class Animal
n: 5
arguments: (...)
caller: (...)
length: 0
name: "Animal"
prototype: {constructor: ƒ, move: ƒ}
__proto__: ƒ ()
[[FunctionLocation]]: VM2952:1
[[Scopes]]: Scopes[2]
move: ƒ move(distance)
__proto__: Object
```

a.prototype它包含两个属性:constructor和__proto__.
constructor  就是我们的构造函数a.
__proto__是什么?
每个对象都会在其内部初始化一个属性，就是__proto__，当我们访问一个对象的属性 时，如果这个对象内部不存在这个属性，那么他就会去__proto__里找这个属性，这个__proto__又会有自己的__proto__，于是就这样 一直找下去。


```js
function a(c){
    this.b = c;
    this.d =function(){
        alert(this.b);
    }
}

var obj = new a()
```

new 运算符做了那些事情
 1. var obj={}; 也就是说，初始化一个对象obj。
 2. obj.__proto__=a.prototype;
 3. a.call(obj);也就是说构造obj，也可以称之为初始化obj。


## 三、参考资料

- [Typescript中文版](https://www.tslang.cn/docs/handbook/basic-types.html)
