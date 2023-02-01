---
title: CommonJS和ESModule的区别
tag: JS模块化
keywords: JS模块化
categories: 前端工程化
---

## CommonJS 和 ESModule 的区别

### 1.引入位置不同

**commonjs**

commonjs 是动态导入，可以在代码的任何地方引入

```javascript
const flag = true
if (flag) {
  const aaa = require('./aaa.js')
}
```

**esmodule**

esmodule 是静态导入，也可以理解为在编译阶段就导入，所以不能在条件语句、函数嵌套等情况下导入

```javascript
import aaa from './aaa.js'
const flag = true
if (flag) {
  // import aaa from './aaa.js' // 会报错
}
```

### 2.使用语法不同

**commonjs**

导出:

```javascript
// aaa.js
const name = 'kobe'
const obj = {
  age: 18
}
module.exports = {
  name,
  obj
}
```

导入：

```javascript
// bbb.js
const aaa = require('./aaa.js')
console.log(aaa.name) // kobe
```

### 3.模块导出导入

**commonjs**

commonjs 导出的是一个 module.exports, 导入其实就是给变量赋值(而值可以是原始类型，也可以是应用类型)

- 当 module.exports 的值是数字，字符串等原始类型时，赋值是值的拷贝，这样才会产生导出值的改变不会影响到导入值改变的现象

```javascript
  # 导出模块 a.js
  let title = 'hello hi'
  setTimeout(() => {
      title = 'hello hehe'
  }, 2000)
  module.exports = title

  # 导入模块 b.js
  const title = require('./a.js')
  setTimeout(() => {
     console.log(title)
  }, 4000)
  console.log(title)

  # 输出结果
  hello hi
  hello hi
```

- 如果 module.exports 导出的是一个对象，导出值的改变是否会影响到导入的值，这个跟导入时赋值的方式是有直接关系的

```javascript
  # 导出模块 a.js
  const obj = {
      title: 'hello hi'
  }
  setTimeout(() => {
      obj.title = 'hello hehe'
  }, 2000)
  module.exports = obj

  # 导入模块 b.js
  const obj = require('./a.js')
  setTimeout(() => {
     console.log(obj.title)
  }, 4000)
  console.log(obj.title)

  # 输出结果
  hello hi
  hello hehe

  可以看出输出的值变了，说明模块内部值的变化导致了导入对象值的变化
  这里的赋值实际上就是引用赋值，module.exports导出的对象被赋值到导入的模块，两者指向同一块内存空间
```

```javascript
  # 导出模块 a.js
  const obj = {
      title: 'hello hi'
  }
  setTimeout(() => {
      obj.title = 'hello hehe'
  }, 2000)
  module.exports = obj

  # 导入模块 b.js
  const { title } = require('./a.js')
  setTimeout(() => {
     console.log(title)
  }, 4000)
  console.log(title)

  # 输出结果
  hello hi
  hello hi

  可以看出输出的值没有发生变化，说明模块内部值的变化没有导致导入值的变化
  这里的赋值实际上就是值的拷贝，通过对象解构的方式直接给变量title赋值，所以模块内部对象值的变化不会影响到导入变量值的变化
```

**esmodule**

esModule 模块输出的是一个`值的引用`, 使用的是动态绑定，esModule 导入导出的值都指向同一个内存地址，所以导入值会跟着导出值发生变化

JS 引擎对脚本进行静态分析的时候，如果遇到模块加载命令 import，就会生成一个只读引用，等到脚本真正执行的时候，再通过这个只读引用，到被加载的模块中去取值。

esModule 导入的基本类型值在当前模块不能直接进行修改(代码会报错，但是可以通过调用模块内的方法进行修改)，也就是说基本类型的值在被导入的模块中是只读状态，对于导入引用类型的值，可以直接进行修改设置，但是不会对模块内的值产生任何影响

```javascript
# 导出模块 a.js
export let title = 'hello hi'
const obj = {
    name: 'tom'
}
export default obj
setTimeout(() => {
    title = 'hello world'
    obj.name = 'tony'
}, 2000)

# 导入模块 b.js
import obj, { title } from './a.js'
console.log(title)
console.log(obj.name)
setTimeout(() => {
    console.log(title)
    console.log(obj.name)
}, 4000)

# 输入结果
hello hi
tom
hello world
tony

可以看出输出的值发生了变化，这是因为导入和导出的值都指向同一内存地址，值变化，取值也就发生变化
```

cjs 输出的是一个对象，该对象需要在模块脚本运行完成后才能生成，而 esm 输出的是静态的，在编译时就能生成

### 4.加载运行

**commonjs**

commonjs 是在代码运行时加载，因为 commonjs 是导出的整个对象，需要在脚本运行完成后才能生成

**esmodule**

esmodule 输出的不是一个对象，而是静态分析编译时输出的接口，它的对外接口是一种静态的定义，可以理解为生成一个个引用变量，在模块读取的时候再去取
