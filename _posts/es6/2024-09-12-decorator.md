---
layout: post
title: ES6 - 装饰器
categories: blog
tags:
  - React
  - ES6
  - Decorator
---

> 本文是对[《ECMAScript 6 入门 - 装饰器》](https://es6.ruanyifeng.com/#docs/decorator)一章中知识点的摘录，请访问原文，获取更多详细信息。

## 一、简介（新语法）

装饰器（Decorator）用来增强 JavaScript 类（class）的功能，是一种用于修改类的行为的语法糖。装饰器是一种函数，写成 `@ + 函数名`，可以用来装饰四种类型的值。

- **类**
- 类的**属性**
- 类的**方法**
- 属性**存取器**（accessor）

目前，装饰器还未进入 ECMAScript 标准。

## 二、装饰器 API（新语法）

装饰器是一个函数，API 的类型描述如下（TypeScript 写法）。

```javascript
type Decorator = (value: Input, context: {
  kind: string;
  name: string | symbol;
  access: {
    get?(): unknown;
    set?(value: unknown): void;
  };
  private?: boolean;
  static?: boolean;
  addInitializer?(initializer: () => void): void;
}) => Output | void;
```

装饰器函数有两个参数。运行时，JavaScript 引擎会提供这两个参数。装饰器函数的返回值，是一个新版本的装饰对象，但也可以不返回任何值（`void`）。

- `value`：所要装饰的值，某些情况下可能是 `undefined`（装饰属性时）。
- `context`：上下文信息对象。

  - `kind`：字符串，表示装饰类型，可能的取值有 `class`、`field`、`method`、`setter`、`getter`、`accessor`。
  - `name`：被装饰的值的名称：值的名称，或者在私有元素的情况下，该元素的描述（比如可读名称）。
  - `access`：对象，包含访问这个值的方法，即存值器和取值器。
  - `static`： 布尔值，该值是否为静态元素。
  - `private`：布尔值，该值是否为私有元素。
  - `addInitializer`：函数，允许用户增加初始化逻辑。

装饰器的执行步骤如下。

1. 计算各个装饰器的值，按照从左到右，从上到下的顺序。
2. 调用方法装饰器。
3. 调用类装饰器。

## 三、类的装饰

**装饰器可以用来装饰整个类**。

```javascript
@testable
class MyTestableClass {
  // ...
}

function testable(target) {
  target.isTestable = true;
}

MyTestableClass.isTestable // true
```

上面代码中，`@testable` 就是一个装饰器。它修改了 `MyTestableClass` 这个类的行为，为它加上了静态属性 `isTestable`。`testable` 函数的参数 `target` 是 `MyTestableClass` 类本身。

装饰器是一个对类进行处理的函数。装饰器函数的第一个参数，就是所要装饰的**目标类**。装饰器的行为类似于下面这样。

```javascript
@decorator
class A {}

// 等同于
class A {}
A = decorator(A) || A;
```

**通过操作目标类的 `prototype` 对象，可以为类添加实例属性**。

```javascript
@testable
class MyTestableClass {}

function testable(target) {
  target.prototype.isTestable = true;
}

let obj = new MyTestableClass();
obj.isTestable // true
```

下面代码通过装饰器 `mixins`，把 `Foo` 对象的方法添加到了 `MyClass` 的实例上面。

```javascript
// mixins.js
export function mixins(...list) {
  return function (target) {
    Object.assign(target.prototype, ...list)
  }
}

// main.js
import { mixins } from './mixins.js'

const Foo = {
  foo() { console.log('foo') }
};

@mixins(Foo)
class MyClass {}

let obj = new MyClass();
obj.foo() // 'foo'
```

实际开发中，React 与 Redux 库结合使用时。可以使用装饰器对 `connect` 方法进行改造，改造后的写法看上去更容易理解。

```javascript
class MyReactComponent extends React.Component {}
export default connect(mapStateToProps, mapDispatchToProps)(MyReactComponent);

// 改造后
@connect(mapStateToProps, mapDispatchToProps)
export default class MyReactComponent extends React.Component {}
```

## 四、类装饰器（新语法）

类装饰器的第一个参数，是被装饰的类。第二个参数是上下文对象，如果被装饰的类是一个匿名类，`name` 属性就为 `undefined`。类装饰器的类型描述如下。

```javascript
type ClassDecorator = (value: Function, context: {
  kind: "class";
  name: string | undefined;
  addInitializer(initializer: () => void): void;
}) => Function | void;
```

**类装饰器可以返回一个新的类，取代原来的类，也可以不返回任何值**。如果返回的不是构造函数，就会报错。

```javascript
@logged 
class C {}

function logged(value, { kind, name }) {
  if (kind === "class") {
    return class extends value {
      constructor(...args) {
        super(...args);
        console.log(`constructing an instance of ${name} with arguments ${args.join(", ")}`);
      }
    }
  }
  // ...
}

new C(1);
// constructing an instance of C with arguments 1
```

## 五、属性装饰器（新语法）

属性装饰器的类型描述如下。

```javascript
type ClassFieldDecorator = (value: undefined, context: {
  kind: "field";
  name: string | symbol;
  access: { get(): unknown, set(value: unknown): void };
  static: boolean;
  private: boolean;
}) => (initialValue: unknown) => unknown | void;
```

- 属性装饰器的第一个参数是 `undefined`，即不输入值。
- 用户可以选择让装饰器返回一个初始化函数，当该属性被赋值时，这个初始化函数会自动运行，它会收到属性的初始值，然后返回一个新的初始值。属性装饰器也可以不返回任何值。除了这两种情况，返回其他类型的值都会报错。

```javascript
function logged(value, { kind, name }) {
  if (kind === "field") {
    return function (initialValue) {
      console.log(`initializing ${name} with value ${initialValue}`);
      return initialValue;
    };
  }

  // ...
}

class C {
  @logged x = 1;
}

new C();
// initializing x with value 1
```

如果不使用装饰器语法，属性装饰器的实际作用如下。

```javascript
let initializeX = logged(undefined, {
  kind: "field",
  name: "x",
  static: false,
  private: false,
}) ?? (initialValue) => initialValue;

class C {
  x = initializeX.call(this, 1);
}

```

## 六、方法的装饰

**装饰器可以装饰类的属性**。

```javascript
class Person {
  @readonly
  name() { return `${this.first} ${this.last}` }
}

function readonly(target, name, descriptor){
  // descriptor对象原来的值如下
  // {
  //   value: specifiedFunction,
  //   enumerable: false,
  //   configurable: true,
  //   writable: true
  // };
  descriptor.writable = false;
  return descriptor;
}

readonly(Person.prototype, 'name', descriptor);
// 类似于
Object.defineProperty(Person.prototype, 'name', descriptor);
```

上面代码中，装饰器 `readonly` 用来装饰类的 `name` 方法。装饰器函数 `readonly` 可以接受三个参数。

- 第一个参数是类的原型对象，上例是 `Person.prototype`，装饰器的本意是要“装饰”类的实例，但是这个时候实例还没生成，所以只能去装饰原型（这不同于类的装饰，那种情况时 `target` 参数指的是类本身）。
- 第二个参数是所要装饰的属性名。
- 第三个参数是该属性的描述对象。

**装饰器可以修改属性的描述对象**，然后被修改的描述对象再用来定义属性。下面的例子，修改属性描述对象的 `enumerable` 属性，使得该属性不可遍历。

```javascript
class Person {
  @nonenumerable
  get kidCount() { return this.children.length; }
}

function nonenumerable(target, name, descriptor) {
  descriptor.enumerable = false;
  return descriptor;
}
```

下面的 `@log` 装饰器，可以起到输出日志的作用。`@log` 装饰器的作用就是在执行原始的操作之前，执行一次 `console.log`，从而达到输出日志的目的。

```javascript
class Math {
  @log
  add(a, b) {
    return a + b;
  }
}

function log(target, name, descriptor) {
  var oldValue = descriptor.value;

  descriptor.value = function() {
    console.log(`Calling ${name} with`, arguments);
    return oldValue.apply(this, arguments);
  };

  return descriptor;
}

const math = new Math();

// passed parameters should get logged now
math.add(2, 4);
```

**装饰器有注释的作用**。比如下面的代码，我们一眼就能看出，`Person` 类是可测试的，而 `name` 方法是只读和不可枚举的。

```javascript
@testable
class Person {
  @readonly
  @nonenumerable
  name() { return `${this.first} ${this.last}` }
}
```

**如果同一个方法有多个装饰器，会先从外到内进入，然后由内向外执行**。

```javascript
function dec(id){
  console.log('evaluated', id);
  return (target, property, descriptor) => console.log('executed', id);
}

class Example {
    @dec(1)
    @dec(2)
    method(){}
}
// evaluated 1
// evaluated 2
// executed 2
// executed 1
```

上面代码中，外层装饰器 `@dec(1)` 先进入，但是内层装饰器 `@dec(2)` 先执行。

除了注释，**装饰器还能用来类型检查**。所以，对于类来说，这项功能相当有用。从长期来看，它将是 JavaScript 代码静态分析的重要工具。

## 七、方法装饰器（新语法）

**方法装饰器可以用来修改类的方法**。

```javascript
class C {
  @trace
  toString() {
    return 'C';
  }
}

// 相当于
C.prototype.toString = trace(C.prototype.toString);
```

方法装饰器使用 TypeScript 描述类型如下。

```javascript
type ClassMethodDecorator = (value: Function, context: {
  kind: "method";
  name: string | symbol;
  access: { get(): unknown };
  static: boolean;
  private: boolean;
  addInitializer(initializer: () => void): void;
}) => Function | void;
```

方法装饰器的第一个参数 `value`，就是所要装饰的方法。

**方法装饰器可以返回一个新函数，取代原来的方法，也可以不返回值，表示依然使用原来的方法**。如果返回其他类型的值，就会报错。

```javascript
function logged(value, { kind, name }) {
  if (kind === "method") {
    return function (...args) {
      console.log(`starting ${name} with arguments ${args.join(", ")}`);
      const ret = value.call(this, ...args);
      console.log(`ending ${name}`);
      return ret;
    };
  }
}

class C {
  @logged
  m(arg) {}
}

new C().m(1);
// starting m with arguments 1
// ending m
```

这里的装饰器实际上是一个语法糖，真正的操作是像下面这样，改掉原型链上面 `m()` 方法。

```javascript
class C {
  m(arg) {}
}

C.prototype.m = logged(C.prototype.m, {
  kind: "method",
  name: "m",
  static: false,
  private: false,
}) ?? C.prototype.m;
```

## 八、为什么装饰器不能用于函数？

**装饰器只能用于类和类的方法，不能用于函数，因为存在函数提升**。类不会提升，所以就没有这方面的问题。如果一定要装饰函数，可以采用高阶函数的形式直接执行。

```javascript
function doSomething(name) {
  console.log('Hello, ' + name);
}

function loggingDecorator(wrapped) {
  return function() {
    console.log('Starting');
    const result = wrapped.apply(this, arguments);
    console.log('Finished');
    return result;
  }
}

const wrapped = loggingDecorator(doSomething);
```

## 九、存取器装饰器（新语法）

存取器装饰器使用 TypeScript 描述的类型如下。

```javascript
type ClassGetterDecorator = (value: Function, context: {
  kind: "getter";
  name: string | symbol;
  access: { get(): unknown };
  static: boolean;
  private: boolean;
  addInitializer(initializer: () => void): void;
}) => Function | void;

type ClassSetterDecorator = (value: Function, context: {
  kind: "setter";
  name: string | symbol;
  access: { set(value: unknown): void };
  static: boolean;
  private: boolean;
  addInitializer(initializer: () => void): void;
}) => Function | void;
```

- 存取器装饰器的第一个参数是原始的存值器（setter）和取值器（getter）。
- 存取器装饰器的返回值如果是一个函数，就会取代原来的存取器。本质上，就像方法装饰器一样，修改发生在类的**原型对象**上。它也可以不返回任何值，继续使用原来的存取器。如果返回其他类型的值，就会报错。

上一节的 `@logged` 装饰器稍加修改，就可以用在存取装饰器。

```javascript
function logged(value, { kind, name }) {
  if (kind === "method" || kind === "getter" || kind === "setter") {
    return function (...args) {
      console.log(`starting ${name} with arguments ${args.join(", ")}`);
      const ret = value.call(this, ...args);
      console.log(`ending ${name}`);
      return ret;
    };
  }
}

class C {
  @logged
  set x(arg) {}
}

new C().x = 1
// starting x with arguments 1
// ending x
```

如果去掉语法糖，使用传统语法来写，就是改掉了类的原型链。

```javascript
class C {
  set x(arg) {}
}

let { set } = Object.getOwnPropertyDescriptor(C.prototype, "x");
set = logged(set, {
  kind: "setter",
  name: "x",
  static: false,
  private: false,
}) ?? set;

Object.defineProperty(C.prototype, "x", { set });
```

## 十、accessor 命令（新语法）

**类装饰器引入了一个新命令 `accessor`，用于属性的前缀。它是一种简写形式，相当于声明属性 `x` 是私有属性 `#x` 的存取接口**。

```javascript
class C {
  accessor x = 1;
}

// 上面的代码等同于下面的代码。
class C {
  #x = 1;

  get x() {
    return this.#x;
  }

  set x(val) {
    this.#x = val;
  }
}
```

**`accessor` 命令前面，可以加上 `static` 命令和 `private` 命令，也可以接受属性装饰器**。

```javascript
function logged(value, { kind, name }) {
  if (kind === "accessor") {
    let { get, set } = value;

    return {
      get() {
        console.log(`getting ${name}`);
        return get.call(this);
      },

      set(val) {
        console.log(`setting ${name} to ${val}`);
        return set.call(this, val);
      },

      init(initialValue) {
        console.log(`initializing ${name} with value ${initialValue}`);
        return initialValue;
      }
    };
  }

  // ...
}

class C {
  @logged accessor x = 1;
}

let c = new C();
// initializing x with value 1
c.x;
// getting x
c.x = 123;
// setting x to 123
```

上面的示例等同于使用 `@logged` 装饰器，改写 `accessor` 属性的 `getter` 和 `setter` 方法。

## 十一、addInitializer() 方法（新语法）

除了属性装饰器，其他装饰器的上下文对象还包括一个 `addInitializer()` 方法，用来完成初始化操作。它的运行时间如下。

- 类装饰器：在类被完全定义之后。
- 方法装饰器：在类构造期间运行，在属性初始化之前。
- 静态方法装饰器：在类定义期间运行，早于静态属性定义，但晚于类方法的定义。

```javascript
function customElement(name) {
  return (value, { addInitializer }) => {
    addInitializer(function() {
      customElements.define(name, this);
    });
  }
}

@customElement('my-element')
class MyElement extends HTMLElement {
  static get observedAttributes() {
    return ['some', 'attrs'];
  }
}
```

上面的代码等同于下面不使用装饰器的代码。

```javascript
class MyElement {
  static get observedAttributes() {
    return ['some', 'attrs'];
  }
}

let initializersForMyElement = [];

MyElement = customElement('my-element')(MyElement, {
  kind: "class",
  name: "MyElement",
  addInitializer(fn) {
    initializersForMyElement.push(fn);
  },
}) ?? MyElement;

for (let initializer of initializersForMyElement) {
  initializer.call(MyElement);
}
```

## 十二、core-decorators.js

[core-decorators.js](https://github.com/jayphelps/core-decorators.js) 是一个第三方模块，提供了几个常见的装饰器，通过它可以更好地理解装饰器。

- `@autobind` 装饰器使得方法中的 `this` 对象，绑定原始对象。
- `@readonly` 装饰器使得属性或方法不可写。
- `@override` 装饰器检查子类的方法，是否正确覆盖了父类的同名方法，如果不正确会报错。
- `@deprecate` 或 `@deprecated` 装饰器在控制台显示一条警告，表示该方法将废除。
- `@suppressWarnings` 装饰器抑制 `@deprecated` 装饰器导致的 `console.warn()` 调用。但是，异步代码发出的调用除外。

下面是一个使用 `@autobind` 的例子。

```javascript
import { autobind } from 'core-decorators';

class Person {
  @autobind
  getPerson() {
    return this;
  }
}

let person = new Person();
let getPerson = person.getPerson;

getPerson() === person;
// true
```

## 十三、使用装饰器实现自动发布事件

我们可以使用装饰器，使得对象的方法被调用时，自动发出一个事件。

```javascript
const postal = require("postal/lib/postal.lodash");

export default function publish(topic, channel) {
  const channelName = channel || '/';
  const msgChannel = postal.channel(channelName);
  msgChannel.subscribe(topic, v => {
    console.log('频道: ', channelName);
    console.log('事件: ', topic);
    console.log('数据: ', v);
  });

  return function(target, name, descriptor) {
    const fn = descriptor.value;

    descriptor.value = function() {
      let value = fn.apply(this, arguments);
      msgChannel.publish(topic, value);
    };
  };
}
```

上面代码定义了一个名为 `publish` 的装饰器，它通过改写 `descriptor.value`，使得原方法被调用时，会自动发出一个事件。它使用的事件“发布/订阅”库是 `Postal.js`。它的用法如下。

```javascript
// index.js
import publish from './publish';

class FooComponent {
  @publish('foo.some.message', 'component')
  someMethod() {
    return { my: 'data' };
  }
  @publish('foo.some.other')
  anotherMethod() {
    // ...
  }
}

let foo = new FooComponent();

foo.someMethod();
foo.anotherMethod();
```

以后，只要调用 `someMethod` 或者 `anotherMethod`，就会自动发出一个事件。

```bash
$ bash-node index.js
频道:  component
事件:  foo.some.message
数据:  { my: 'data' }

频道:  /
事件:  foo.some.other
数据:  undefined
```

## 十四、Mixin

**在装饰器的基础上，可以实现 `Mixin` 模式**。所谓 `Mixin` 模式，就是对象继承的一种替代方案，中文译为“**混入**”（mix in），意为**在一个对象中混入另外一个对象的方法**。

```javascript
const Foo = {
  foo() { console.log('foo') }
};

class MyClass {}
Object.assign(MyClass.prototype, Foo);

let obj = new MyClass();
obj.foo() // 'foo'
```

上面代码中，对象 `Foo` 有个 `foo` 方法，通过 `Object.assign` 方法，可以将 `foo` 方法“混入” `MyClass` 类，导致 `MyClass` 的实例 `obj` 对象都具有 `foo` 方法。这就是“混入”模式的一个简单实现。

下面，我们部署一个通用脚本 `mixins.js`，将 `Mixin` 写成一个装饰器。

```javascript
export function mixins(...list) {
  return function (target) {
    Object.assign(target.prototype, ...list);
  };
}
```

```javascript
import { mixins } from './mixins.js';

const Foo = {
  foo() { console.log('foo') }
};

@mixins(Foo)
class MyClass {}

let obj = new MyClass();
obj.foo() // "foo"
```

通过 `mixins` 这个装饰器，实现了在 `MyClass` 类上面“混入” `Foo` 对象的 `foo` 方法。上面的方法会改写 `MyClass` 类的 `prototype` 对象，如果不喜欢这一点，也可以通过类的继承实现 `Mixin`。

```javascript
class MyClass extends MyBaseClass {
  /* ... */
}
```

上面代码中，`MyClass` 继承了 `MyBaseClass`。如果我们想在 `MyClass` 里面“混入”一个 `foo` 方法，一个办法是在 `MyClass` 和 `MyBaseClass` 之间插入一个混入类，这个类具有 `foo` 方法，并且继承了 `MyBaseClass` 的所有方法，然后 `MyClass` 再继承这个类。

```javascript
let MyMixin = (superclass) => class extends superclass {
  foo() {
    console.log('foo from MyMixin');
  }
};

class MyClass extends MyMixin(MyBaseClass) {
  /* ... */
}

let c = new MyClass();
c.foo(); // "foo from MyMixin"
```

上面代码中，`MyMixin` 是一个混入类生成器，接受 `superclass` 作为参数，然后返回一个继承 `superclass` 的子类，该子类包含一个 `foo` 方法。目标类继承了这个混入类，就达到了“混入” `foo` 方法的目的。

如果需要“混入”多个方法，就生成多个混入类。

```javascript
class MyClass extends Mixin1(Mixin2(MyBaseClass)) {
  /* ... */
}
```

**这种写法的一个好处，是可以调用 `super`，因此可以避免在“混入”过程中覆盖父类的同名方法**。

```javascript
let Mixin1 = (superclass) => class extends superclass {
  foo() {
    console.log('foo from Mixin1');
    if (super.foo) super.foo();
  }
};

let Mixin2 = (superclass) => class extends superclass {
  foo() {
    console.log('foo from Mixin2');
    if (super.foo) {
      super.foo();
    };
  }
};

class S {
  foo() {
    console.log('foo from S');
  }
}

class C extends Mixin1(Mixin2(S)) {
  foo() {
    console.log('foo from C');
    super.foo();
  }
}
```

上面代码中，每次混入发生时，都调用了父类的 `super.foo` 方法，导致父类的同名方法没有被覆盖，行为被保留了下来。

```javascript
new C().foo()
// foo from C
// foo from Mixin1
// foo from Mixin2
// foo from S
```

## 十五、Trait

`Trait` 也是一种装饰器，效果与 `Mixin` 类似，但是提供更多功能，比如防止同名方法的冲突、排除混入某些方法、为混入的方法起别名等等。关于这部分的详细信息，请参考[原文](https://es6.ruanyifeng.com/#docs/decorator#Trait)。

## 十六、参考

- 阮一峰，[ECMAScript 6 入门 - 装饰器](https://es6.ruanyifeng.com/#docs/decorator)
