# 堆和栈

“堆”（Heap）和栈（Stack）主要有两个不同的概念和应用场景：一种是指内存管理中的堆和栈，另一种是指数据结构中的堆和栈。本文只介绍内存管理中的堆和栈。

## 一、堆

### 概念

在内存管理中，堆（Heap）是动态分配内存的区域，它用于存储在运行时分配的对象和数据。堆中的内存分配和释放不遵循特定的顺序，而是由编程语言的内存管理机制（如垃圾回收器）自动管理。

### 在 JavaScript 中的应用

在 JavaScript 中，对象、数组等复杂数据结构通常分配在堆上，而基本数据类型（如数字、布尔值等）通常分配在栈上。JavaScript 的垃圾回收机制负责自动管理堆内存，程序员无需手动分配和释放内存。

```javascript
function createObject() {
  const obj = { name: "Tom" }; // 对象分配在堆上
  return obj;
}

const newObj = createObject();
```

## 二、栈

### 内存管理中的栈

在内存管理中，栈是用于存储局部变量和函数调用的内存区域。栈内存是一个连续的内存块，遵循后进先出（LIFO, Last In First Out）原则，意味着最后压入栈的变量最先被弹出。

### JavaScript 中的应用

在 JavaScript 中，栈内存用于存储函数调用、局部变量和上下文环境。每当函数调用时，JavaScript 引擎会将函数的执行上下文压入栈中，当函数返回时，执行上下文从栈中弹出。

```javascript
function foo() {
  let a = 10; // 局部变量 'a' 存储在栈上
  function bar() {
    let b = 20; // 局部变量 'b' 存储在栈上
    return a + b;
  }
  return bar();
}

foo(); // 函数调用会在调用栈上创建新的执行上下文
```

### 栈帧和调用栈

#### 栈帧

每次函数调用时，都会在栈中创建一个栈帧（stack frame），栈帧包含了函数的局部变量、参数和返回地址。当函数返回时，栈帧从栈中弹出。

#### 调用栈

调用栈（call stack）是一个栈结构，用于跟踪函数调用。每当一个新的函数调用时，会将该函数的执行上下文压入调用栈，当函数执行完毕时，执行上下文从调用栈中弹出。

### JavaScript 引擎中的栈管理

JavaScript 引擎使用调用栈来管理函数调用和执行上下文。以下是调用栈的工作流程：

1. **创建全局执行上下文**：程序启动时，JavaScript 引擎创建全局执行上下文并将其压入调用栈。
2. **函数调用**：每当函数调用时，创建该函数的执行上下文并将其压入调用栈。
3. **执行上下文栈**：执行上下文栈（Execution Context Stack）跟踪所有活动的执行上下文。
4. **函数返回**：当函数执行完毕时，将该函数的执行上下文从栈中弹出，控制权返回到调用该函数的上下文。

```javascript
function globalFunction() {
  function firstLevel() {
    function secondLevel() {
      console.log("Second level function");
    }
    secondLevel();
    console.log("First level function");
  }
  firstLevel();
  console.log("Global function");
}

globalFunction(); // 调用栈变为：globalFunction -> firstLevel -> secondLevel
```

在上述示例中，调用栈的变化如下：

1. `globalFunction` 被调用，创建其执行上下文并压入栈中。
2. `firstLevel` 被调用，创建其执行上下文并压入栈中。
3. `secondLevel` 被调用，创建其执行上下文并压入栈中。
4. `secondLevel` 执行完毕，其执行上下文从栈中弹出。
5. `firstLevel` 执行完毕，其执行上下文从栈中弹出。
6. `globalFunction` 执行完毕，其执行上下文从栈中弹出。

## 三、堆和栈的对比

- **堆（Heap）**：
  - 用于动态分配内存。
  - 内存块的生存期不固定，由程序员手动管理或通过垃圾回收机制自动管理。
  - 适用于大型对象和需要在函数调用结束后仍然存在的数据。
- **栈（Stack）**：
  - 用于函数调用时的内存分配。
  - 内存块的生存期由函数调用的生命周期决定，自动管理。
  - 适用于小型对象和局部变量。
  