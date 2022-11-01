---

title: JS 高级
tag: Javascript
keywords: Javascript
categories: Javascript

---

## 一、深入 javascript 与运行原理

### 1.V8 引擎的原理

- V8 是 C++编写的 Google 开源高性能 JavaScript 和 WebAssembly 引擎，用户 Chrome 和 nodejs 等

- 它实现 ECMAScript 和 WebAssembly，并在 Windows 7 或更高版本，macOS 10.12+和使用 x64，IA-32， ARM 或 MIPS 处理器的 Linux 系统上运行
- V8 可以独立运行，也可以嵌入到任何 C ++应用程序中

### 2.V8 引擎的架构

- parse 模块将 javascript 代码转换成 ast 抽象语法树，这是因为解释器并不直接认识 javascript 代码
- Ignition 是一个解释器，会将 ast 转换成 ByteCode 字节码
- TurboFan 是一个编译器，可以将字节码变异成 CPU 可以直接执行的机器码 ![1](./images/1.png)

### 3.V8 执行的细节

- Blink 将解析 HTML 文件时，遇到 Javascript 将源码交给 V8 引擎，Stream 获取到源码并且进行编码转换
- Scanner 会进行词法分析，词法分析会将代码转换成 tokens
- 接下来 tokens 经过 Parser 和 PreParser 模块转换成 AST 树
  - Parser 就是将 tokens 转成 AST 树结构
  - PreParser 称之为预解析，为什么需要预解析呢？
    - 这是因为并不是所有的 Javscript 代码，在一开始就会被执行。对所有的 Javascript 代码进行解析，必然会影响网页的运行效率
    - 所以 V8 引擎就实现了 Lazy Parsing（延迟解析）的方案，它的作用是将不必要的函数进行预解析，也就是只解析暂时需要的内容，而对函数的全量解析就是在函数被调用时才会进行
    - 比如我们在一个函数 outer 内部定义了另外一个函数 inner，那么 inner 函数就会进行预解析
- 生成 AST 树后，会被 Ignition 模块专程字节码（bytecode），之后的过程就是代码的执行过程

### 4.Javascript 的执行过程

1. 初始化全局对象
   - js 引擎在执行代码之前，会在堆内存中创建一个全局对象：Global Object（GO）
     - 该对象所有的作用域（scope）都可以访问
     - 里面会包含 Date、Array、String、Number、setTimeout、setInteval 等等全局属性 AA 己
2. 创建执行上下文栈
   - js 引擎内部有一个执行上下文栈（Execution Context Stack， 简称 ECS），它是用于执行代码的调用栈（执行栈）
   - 那么现在它要执行谁呢？执行的是全局的代码块：
     - 全局的代码块为了执行会创建一个全局执行上下文 Global Execution Context（GEC），GEC 中会创建一个 VO 对象（就是 GO 对象）
     - GEC 会放到 ECS 中执行
   - GEC 被放入到 ECS 里面包含两个内容：
     - 第一部分：在代码执行前，在 parser 转成 AST 的过程中，会将全局定义的变量、函数等加入到 GlobalObject 中，但是只是声明，不会赋值。这个过程也称作是作用域提升
     - 第二部分：在代码执行中，对变量赋值，或者执行其他的函数
3. 遇到函数创建函数执行上下文
   - 在执行的过程中执行到一个函数时，就会根据函数体创建一个函数执行上下文（Functional Execution Context， 简称 FEC），并且压入到 EC Stack 中
   - FEC 包含三部分内容：
     - 第一部分：在解析函数称为 AST 树结构时，会创建一个 Activation Object（AO）
       - AO 中包含形参、arguments、函数定义和指向函数对象、定义的变量
     - 第二部分：作用域链，由 VO（在函数中就是 AO 对象）和父级 VO 组成，查找时会一层层查找（函数的父级作用域 Parent Scope 和函数的定义位置有关系，和调用位置是没有关系的）
     - 第三部分：this 绑定的值

## 二、JS 的内存管理和闭包

### 1.认识内存管理

- 不管什么样的编程语言，在**代码的执行过程中都是需要给它分配内存**的，不同的是**某些编程语言**需要我们**自己手动**

  **的管理内存**，**某些编程语言**会可以**自动帮助我们管理内存**

- 不管以什么样的方式来管理内存，**内存的管理都会有如下的生命周期**：

  - 第一步：分配申请你需要的内存（申请）；
  - 第二步：使用分配的内存（存放一些东西，比如对象等）；
  - 第三步：不需要使用时，对其进行释放；

- **不同的编程语言对于第一步和第三步会有不同的实现：**

- 手动管理内存：比如 C、C++，包括早期的 OC，都是需要手动来管理内存的申请和释放的（malloc 和 free 函数）
- 自动管理内存：比如 Java、JavaScript、Python、Swift、Dart 等，它们有自动帮助我们管理内存；

- 我们可以知道 JavaScript 通常情况下是不需要手动来管理的。

### 2.JS 的内存管理

- javascript 会在定义变量时分配内存
  - 对于基本数据类型，会在栈空间分配内存
  - 对于复杂数据类型，会在堆空间开辟内存，并将这块空间的指针返回给值变量引用

### 3.JS 的垃圾回收

- 因为**内存的大小是有限**的，所以当**内存不再需要的时候**，我们需要**对其进行释放**，以便腾出**更多的内存空间**
- JS 有自己的垃圾回收机制（Garbage Collection，简称 GC）
- 对于那些不再使用的对象，我们都称之为是垃圾，它需要被回收，以释放更多的内存空间

### 4.常见的 GC 算法

1. 引用计数算法

   - 当一个对象有一个引用指向它时，那么这个对象的引用就+1，当一个对象的引用为 0 时，这个对象就可以被销

     毁掉

   - p 这个算法有一个很大的弊端就是会产生循环引用

2. 标记清除

   - 这个算法是设置一个根对象（root object），垃圾回收器会定期从这个根开始，找所有从根开始有引用到的对象，对

     于哪些没有引用到的对象，就认为是不可用的对象

   - 这个算法可以很好的解决循环引用的问题

   - JS 引擎比较广泛的采用的就是标记清除算法，当然类似于 V8 引擎为了进行更好的优化，它在算法的实现细节上也会结合

     一些其他的算法。

## 三、JS 中闭包的定义

- 这里先来看一下闭包的定义，分成两个：在计算机科学中和在 JavaScript 中。
- 在计算机科学中对闭包的定义（维基百科）：

  - 闭包（英语：Closure），又称**词法闭包**（Lexical Closure）或**函数闭包**（function closures）；
  - 是在支持 **头等函数** 的编程语言中，实现词法绑定的一种技术；
  - 闭包在实现上是一个结构体，它存储了一个函数和一个关联的环境（相当于一个符号查找表）；
  - 闭包跟函数最大的区别在于，当捕捉闭包的时候，它的 **自由变量** 会在补充时被确定，这样即使脱离了捕捉时的上下文，它也能照常运行；

- 闭包的概念出现于 60 年代，最早实现闭包的程序是 Scheme，那么我们就可以理解为什么 JavaScript 中有闭包：

  - 因为 JavaScript 中有大量的设计是来源于 Scheme 的；

- 我们再来看一下 MDN 对 JavaScript 闭包的解释：

  - 一个函数和对其周围状态（**lexical environment，词法环境**）的引用捆绑在一起（或者说函数被引用包围），这样的组合就是**闭包**（**closure**）；
  - 也就是说，闭包让你可以在一个内层函数中访问到其外层函数的作用域；
  - 在 JavaScript 中，每当创建一个函数，闭包就会在函数创建的同时被创建出来；

- 那么我的理解和总结：
- 一个普通的函数 function，如果它可以访问外层作用于的自由变量，那么这个函数就是一个闭包；
- 从广义的角度来说：JavaScript 中的函数都是闭包；
- 从狭义的角度来说：JavaScript 中一个函数，如果访问了外层作用于的变量，那么它是一个闭包；

## 四、JS 中的 this 指向

### 1.this 的绑定规律

1. 函数在调用时，JavaScript 会默认给 this 绑定一个值；
2. this 的绑定和定义的位置（编写的位置）没有关系；
3. this 的绑定和调用方式以及调用的位置有关系；
4. this 是在运行时被绑定的；

### 2.this 的绑定规则

- 默认绑定（优先级最低）：独立函数调用
- 隐式绑定（优先级大于默认绑定）：obj.fn()
- 显示绑定（优先级大于隐式绑定）：apply、call、bind 等
- new 绑定（优先级大于显示绑定）：new Foo()

## 五、手写 call、apply 和 bind

### 1.call 函数的实现

```javascript
Function.prototype.lyCall = function (thisArg, ...args) {
  // 1.处理thisArg
  thisArg = thisArg !== undefined && thisArg !== null ? Object(thisArg) : window
  // 2.隐式绑定，调用需要修改this指向的函数
  const key = Symbol()
  thisArg[key] = this
  const result = thisArg[key](...args)
  delete thisArg[key]
  // 3.返回函数执行结果
  return result
}
```

### 2.apply 函数的实现

```javascript
Function.prototype.lyApply = function (thisArg, argArray) {
  // 1.处理thisArg
  thisArg = thisArg !== undefined && thisArg !== null ? Object(thisArg) : window
  // 2.隐式绑定，调用需要修改this指向的函数
  const key = Symbol()
  thisArg[key] = this
  argArray = argArray || []
  const result = thisArg[key](...argArray)
  delete thisArg[key]
  // 3.返回函数执行结果
  return result
}
```

### 3.bind 函数的实现

```javascript
Function.prototype.lyBind = function (thisArg, ...args) {
  return function (...bindArgs) {
    // 1.处理thisArg
    thisArg = thisArg !== undefined && thisArg !== null ? Object(thisArg) : window
    // 2.隐式绑定，调用需要修改this指向的函数
    const key = Symbol()
    thisArg[key] = this
    const result = thisArg[key]([...args, ...bindArgs])
    delete thisArg[key]
    // 3.返回函数执行结果
    return result
  }
}
```

## 六、函数科里化

### 1.函数科里化的概念

一个接收多个参数的函数，变成一个可以只接收部分参数，并且返回一个接收剩余参数的函数的函数。这个过程就叫科里化

### 2.函数科里化的实现

```javascript
function curring(fn) {
  const curriedFn = function (...args) {
    if (args.length >= fn.length) {
      // 本次函数调用传入的参数个数已经达到需要执行函数的最大参数长度，直接执行该函数
      return fn.apply(this, args)
    } else {
      // 本次函数调用传入的参数个数没有达到需要执行函数的最大参数长度，返回一个接收剩余参数的函数
      return function (...restArgs) {
        return curriedFn.apply(this, [...args, ...restArgs])
      }
    }
  }

  return curriedFn
}
```

## 七、组合函数

### 1.组合函数的概念

将多个需要执行的函数，组合到一个函数里面，函数内部会自动依次调用。这个过程就是对函数的组合，这个函数就称为组合函数

### 2.通用组合函数的实现

```javascript
function compose(...fns) {
  fns.forEach((fn) => {
    if (typeof fn !== 'function') {
      throw new Error('Expected arguments are function!')
    }
  })
  const list = fns.slice()
  return function (...args) {
    let result = list.shift().apply(this, args)
    while (list.length > 0) {
      result = list.shift().call(this, result)
    }
    return result
  }
}
```

## 八、寄生组合式继承（最终方案）

```javascript
// 寄生式继承-对象
function createObject(o) {
  function Fn() {}
  Fn.prototype = o
  return new Fn()
}

function inheritPrototype(subType, superType) {
  // subType.prototype = Object.create(superType.prototype) // 子类显式原型指向新创建的对象，新对象__proto__指向父类显示原型
  subType.prototype = createObject(superType.prototype)
  // 子类显示原型的构造函数指向自己
  Object.defineProperty(subType.prototype, 'constuctor', {
    configurable: true,
    enumerable: false,
    writable: true,
    value: subType
  })
}
// 父类
function Person(name) {
  this.name = name
}
Person.prototype.eating = function () {
  console.log('eating')
}
// 子类
function Student(sno) {
  // 继承父类属性
  Person.call(this, name)
  this.sno = sno
}
// 继承父类方法
inheritPrototype(Student, Person)
```

## 九、手写 instanceof

```javascript
function _instanceof(a, b) {
  while (a.__proto__) {
    if (a.__proto__ === b.prototype) {
      return true
    }
    a = a.__proto__
  }
  return false
}
```

## 十、手写 new 操作符

```javascript
function _new(constructor, ...args) {
  let instance = Object.create(constructor.prototype)
  const res = constuctor.apply(instance, args)
  // 构造函数有明确的返回值且返回值是Object时，直接返回构造函数的返回结果
  return res instanceof Object ? res : instance
}
```

## 十一、响应式原理实现

```javascript
// Dep类存储响应式函数（副作用）
class Dep {
  subscribers = new Set()
  addEffect() {
    activeEffect && this.subscribers.add(activeEffect)
  }
  notify() {
    this.subscribers.forEach((effect) => effect())
  }
}

// 当前需要收集的响应式函数（副作用）
let activeEffect = null
// 第一次执行响应式函数（副作用），并触发对应的追踪收集操作
const watchEffect = (fn) => {
  activeEffect = fn
  fn()
  activeEffect = null
}

// WeakMap: { obj -> Map }  Map: { obj属性 -> dep对象(存储响应式函数,并提供触发方法) }
const weakMap = new WeakMap()
// 收集响应式函数（副作用）
const track = (target, key) => {
  let targetMap = weakMap.get(target)
  if (!targetMap) {
    targetMap = new Map()
    weakMap.set(target, targetMap)
  }
  let dep = targetMap.get(key)
  if (!dep) {
    dep = new Dep()
    targetMap.set(key, dep)
  }
  dep.addEffect()
}
// 触发响应式函数（副作用）
const trigger = (target, key) => {
  const targetMap = weakMap.get(target)
  if (!targetMap) return
  const dep = targetMap.get(key)
  dep?.notify()
}

// 创建代理对象
const reactive = (o) => {
  return new Proxy(o, {
    get(target, key, receiver) {
      track(target, key)
      return Reflect.get(target, key, receiver)
    },
    set(target, key, newValue, receiver) {
      trigger(target, key)
      Reflect.set(target, key, newValue, receiver)
    }
  })
}
```

## 十二、手写简易版 promise 实现

```javascript
const PROMISE_STATUS_PENDING = 'pending'
const PROMISE_STATUS_FULFILLED = 'fulfilled'
const PROMISE_STATUS_REJECTED = 'rejected'

const execCallbackWithCatchErr = (execFn, value, resolve, reject) => {
  try {
    const result = execFn(value)
    resolve(result)
  } catch (err) {
    reject(err)
  }
}

class LyPromise {
  status = PROMISE_STATUS_PENDING
  value = undefined
  reason = undefined
  onfulfilledCallbacks = []
  onrejectedCallbacks = []
  constructor(executor) {
    const resolve = (value) => {
      if (this.status === PROMISE_STATUS_PENDING) {
        queueMicrotask(() => {
          if (this.status !== PROMISE_STATUS_PENDING) return
          this.status = PROMISE_STATUS_FULFILLED
          this.value = value
          this.onfulfilledCallbacks.forEach((callback) => {
            callback(this.value)
          })
        })
      }
    }
    const reject = (reason) => {
      if (this.status === PROMISE_STATUS_PENDING) {
        queueMicrotask(() => {
          if (this.status !== PROMISE_STATUS_PENDING) return
          this.status = PROMISE_STATUS_REJECTED
          this.reason = reason
          this.onrejectedCallbacks.forEach((callback) => {
            callback(this.reason)
          })
        })
      }
    }

    try {
      executor(resolve, reject)
    } catch (err) {
      reject(err)
    }
  }

  then(onfulfilled, onrejected) {
    return new LyPromise((resolve, reject) => {
      onfulfilled =
        onfulfilled ||
        ((res) => {
          return res
        })
      onrejected =
        onrejected ||
        ((err) => {
          throw err
        })

      switch (this.status) {
        case PROMISE_STATUS_PENDING:
          this.onfulfilledCallbacks.push(() => {
            execCallbackWithCatchErr(onfulfilled, this.value, resolve, reject)
          })
          this.onrejectedCallbacks.push(() => {
            execCallbackWithCatchErr(onrejected, this.reason, resolve, reject)
          })
          break
        case PROMISE_STATUS_FULFILLED:
          execCallbackWithCatchErr(onfulfilled, this.value, resolve, reject)
          break
        case PROMISE_STATUS_REJECTED:
          execCallbackWithCatchErr(onrejected, this.reason, resolve, reject)
          break
      }
    })
  }

  catch(onrejected) {
    return this.then(undefined, onrejected)
  }

  finally(onfinally) {
    return this.then(onfinally, onfinally)
  }

  static resolve(value) {
    return new LyPromise((resolve) => resolve(value))
  }

  static reject(reason) {
    return new LyPromise((resolve, reject) => reject(reason))
  }

  static all(promises) {
    return new Promise((resolve, reject) => {
      let values = []
      promises.forEach((promise) => {
        promise.then((res) => {
          values.push(res)
          if (values.length === promises.length) resolve(values)
        }, reject)
      })
    })
  }

  static allSettled(promises) {
    return new Promise((resolve, reject) => {
      let result = []
      promises.forEach((promise) => {
        promise.then(
          (res) => {
            result.push({
              status: PROMISE_STATUS_FULFILLED,
              value: res
            })
            if (result.length === promises.length) resolve(result)
          },
          (err) => {
            result.push({
              status: PROMISE_STATUS_REJECTED,
              reason: err
            })
            if (result.length === promises.length) resolve(result)
          }
        )
      })
    })
  }

  static race(promises) {
    return new LyPromise((resolve, reject) => {
      promises.forEach((promise) => {
        promise.then(resolve, reject)
      })
    })
  }

  static any(promises) {
    return new LyPromise((resolve, reject) => {
      let reasons = []
      promises.forEach((promise) => {
        promise.then(resolve, (err) => {
          reasons.push(err)
          if (reasons.length === promises.length) {
            reject(new AggregateError(reasons))
          }
        })
      })
    })
  }
}
```

## 十三、异步方案：Promise+Generator

```javascript
function requestData(url) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve(url)
    }, 2000)
  })
}

// 生成器
function* getData() {
  const res1 = yield requestData('coder')
  const res2 = yield requestData(res1 + '111')
  const res3 = yield requestData(res2 + '222')
  const res4 = yield requestData(res3 + '333')
  console.log(res4)
}

// 自动递归执行生成器
function co(genFn) {
  const generator = genFn()
  const exec = (res) => {
    const result = generator.next(res)
    if (result.done) {
      return result.value
    }
    result.value.then((res) => {
      exec(res)
    })
  }
  exec()
}

co(getData)
```

## 十二、手写防抖和节流

### 1.防抖

```javascript
function debounce(fn, delay, immediate = false) {
  let timer = null
  let isInvoke = false
  const _debounce = (...args) {
    if(timer) clearTimeout(timer)
    if(immediate && !isInvoke) {
      fn.apply(this, args)
      isInvoke = true
    }
    timer = setTimeout(() => {
      timer = null
      isInvoke = false
      fn.apply(this, args)
    }, delay)
  }

  _debounce.cancel = () => {
     if(timer) clearTimeout(timer)
     timer = null
     isInvoke = false
  }

  return _debounce
}
```

### 2.节流

```javascript
function throttle(fn, interval, options = { leading: true, trailing: true }) {
  let last = 0
  timer = null
  const { leading, trailing } = options
  const _throttle = function (...args) {
    let now = Date.now()
    if (!last && !leading) last = now
    const remainTime = interval - (now - last)
    if (remainTime <= 0) {
      fn.apply(fn, args)
      last = now
      return
    }
    if (trailing) {
      if (timer) {
        clearTimeout(timer)
        timer = null
      }
      timer = setTimeout(() => {
        fn.apply(fn, args)
        timer = null
        last = !leading ? 0 : Date.now()
      }, remainTime)
    }
  }
  _throttle.cancel = function () {
    if (timer) {
      clearTimeout(timer)
      timer = null
    }
  }
  return _throttle
}
```

## 十三、深拷贝

```javascript
function isObject(o) {
  const type = typeof o
  return o !== null && (type === 'object' || type === 'function')
}
function deepClone(originValue, weakMap = new WeakMap()) {
  // 处理数组格式
  const newObj = Array.isArray(originValue) ? [] : {}
  // 处理简单数据类型
  if (!isObject(originValue)) return originValue
  // 处理函数类型,不需要深拷贝，直接返回
  if (typeof originValue === 'function') return originValue
  // 处理Symbol类型，返回一个相同description的Symbol
  if (typeof originValue === 'symbol') return Symbol(originValue.description)
  // 处理Set类型
  if (originValue instanceof Set) return new Set([...originValue])
  // 处理Map类型
  if (originValue instanceof Map) return new Set([...originValue])

  // 解决循环引用问题
  if (weakMap.has(originValue)) {
    return weakMap.get(originValue)
  }
  weakMap.set(originValue, newObj)

  for (const key in originValue) {
    newObj[key] = deepClone(originValue[key], weakMap)
  }
  // 处理Symbol类型作为key
  const symbolKeys = Object.getOwnPropertySymbols(originValue)
  for (const sKey in symbolKeys) {
    newObj[sKey] = deepClone(originValue[sKey], weakMap)
  }

  return newObj
}
```

## 十四、事件总线

```javascript
class LyEventBus {
  constructor() {
    this.eventBus = {}
  }

  on(eventName, eventCallBack, thisArg) {
    let handlers = this.eventBus[eventName]
    if (!handlers) handlers = []
    handlers.push({
      eventCallBack,
      thisArg
    })
    this.eventBus[eventName] = handlers
  }

  emit(eventName, ...payload) {
    const handlers = this.eventBus[eventName]
    if (!handlers) return
    handlers.forEach((handler) => {
      handler.eventCallBack.apply(handler.thisArg, payload)
    })
  }

  off(eventName, eventCallBack) {
    const handlers = this.eventBus[eventName]
    if (!handlers) return
    const copyHandlers = [...handlers]
    copyHandlers.forEach((handler) => {
      if (handler.eventCallBack === eventCallBack) {
        const index = handlers.indexOf(handler)
        handlers.splice(index, 1)
      }
    })
  }
}
```

## 十五、快速排序

```javascript
function quickSort(arr, func = (a, b) => a - b) {
  if (!arr || !arr.length) return [] // 确保传入的是数组
  if (arr.length === 1) return arr // 跳出递归条件
  const pivot = arr[0] // 选取基准元素
  // 小于基准元素的元素集合
  const smallSet = arr.slice(1).filter((item) => quickSort(item, pivot) < 0)
  // 大于基准元素的元素集合
  const bigSet = arr.slice(1).filter((item) => quickSort(item, pivot) > 0)
  return quickSort(smallSet, func).concat([pivot]).concat(quickSort(bigSet, func))
}
```
