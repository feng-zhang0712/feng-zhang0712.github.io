# JavaScript 模块化

JavaScript 模块化是指将代码分割成独立的模块，每个模块封装特定的功能，这样可以提高代码的可维护性和重用性。JavaScript 的模块化经历了多个发展阶段，从最初的全局变量，到现在的 ES6 模块标准（ESM）。

## 一、JavaScript 模块化发展历史

### 1. 全局作用域（Global Scope）

在早期，JavaScript 代码通常直接写在 `<script>` 标签中，所有变量和函数都位于全局作用域中。这种方式容易导致命名冲突和全局污染。

### 2. 立即执行函数表达式（IIFE）

为了避免全局作用域污染，开发者开始使用立即执行函数表达式（IIFE）。IIFE 通过创建一个局部作用域，避免了变量和函数的命名冲突。

```javascript
(function() {
  var privateVariable = "I am private";
  function privateFunction() {
    console.log(privateVariable);
  }
  privateFunction();
})();
```

### 3. 模块模式（Module Pattern）

模块模式使用闭包来创建私有作用域，同时返回一个对象，暴露公共 API。这种方式模拟了模块化的概念。

```javascript
var Module = (function() {
  var privateVariable = "I am private";
  function privateFunction() {
    console.log(privateVariable);
  }
  return {
    publicMethod: function() {
      privateFunction();
    }
  };
})();

Module.publicMethod(); // 输出 "I am private"
```

### 4. CommonJS

CommonJS 是 Node.js 使用的模块化规范，广泛用于服务端 JavaScript 编程。CommonJS 使用 `require` 和 `module.exports` 来引入和导出模块。

```javascript
// 模块定义（module.js）
var privateVariable = "I am private";
function privateFunction() {
  console.log(privateVariable);
}
module.exports = {
  publicMethod: function() {
    privateFunction();
  }
};
```

```javascript
// 模块引入（main.js）
var Module = require('./module.js');
Module.publicMethod(); // 输出 "I am private"
```

### 5. AMD（Asynchronous Module Definition）

AMD 是一种用于浏览器的异步模块定义规范，主要实现是 RequireJS。AMD 允许我们异步加载模块，提高性能。

```javascript
// 模块定义（module.js）
define(['dependency'], function(dependency) {
  var privateVariable = "I am private";
  function privateFunction() {
    console.log(privateVariable);
  }
  return {
    publicMethod: function() {
      privateFunction();
    }
  };
});
```

```javascript
// 模块引入（main.js）
require(['module'], function(Module) {
  Module.publicMethod(); // 输出 "I am private"
});
```

### 6. UMD（Universal Module Definition）

UMD 是一种兼容多种模块系统的模式，可以同时兼容 CommonJS 和 AMD。UMD 模块可以在浏览器和 Node.js 中使用。

```javascript
(function (root, factory) {
  if (typeof define === 'function' && define.amd) {
    // AMD
    define(['dependency'], factory);
  } else if (typeof module === 'object' && module.exports) {
    // Node.js
    module.exports = factory(require('dependency'));
  } else {
    // Browser globals (root is window)
    root.Module = factory(root.dependency);
  }
}(this, function (dependency) {
  var privateVariable = "I am private";
  function privateFunction() {
    console.log(privateVariable);
  }
  return {
    publicMethod: function() {
      privateFunction();
    }
  };
}));
```

### 7. ES6 模块（ESM）

ES6 引入了标准化的模块系统，使用 `import` 和 `export` 关键字。这是现代 JavaScript 推荐的模块化方式。

```javascript
// 模块定义（module.js）
const privateVariable = "I am private";
function privateFunction() {
  console.log(privateVariable);
}
export function publicMethod() {
  privateFunction();
}
```

```javascript
// 模块引入（main.js）
import { publicMethod } from './module.js';
publicMethod(); // 输出 "I am private"
```

## 二、实现模块化的方式

### 1. 全局变量（不推荐）

最简单的方式是直接使用全局变量，但这容易导致命名冲突和代码维护困难。

```javascript
var myModule = {
  publicMethod: function() {
    console.log("This is a public method");
  }
};
```

### 2. IIFE（立即执行函数表达式）

使用 IIFE 创建模块，避免全局作用域污染。

```javascript
var myModule = (function() {
  var privateVariable = "I am private";
  function privateFunction() {
    console.log(privateVariable);
  }
  return {
    publicMethod: function() {
      privateFunction();
    }
  };
})();
```

### 3. CommonJS

主要用于 Node.js，使用 `require` 和 `module.exports`。

```javascript
// module.js
module.exports = {
  publicMethod: function() {
    console.log("This is a public method");
  }
};

// main.js
const myModule = require('./module');
myModule.publicMethod();
```

### 4. AMD

主要用于浏览器，使用 `define` 和 `require`。

```javascript
// module.js
define([], function() {
  return {
    publicMethod: function() {
      console.log("This is a public method");
    }
  };
});

// main.js
require(['module'], function(myModule) {
  myModule.publicMethod();
});
```

### 5. UMD

兼容 CommonJS 和 AMD，同时可以在浏览器中使用。

```javascript
(function (root, factory) {
  if (typeof define === 'function' && define.amd) {
    define(['dependency'], factory);
  } else if (typeof module === 'object' && module.exports) {
    module.exports = factory(require('dependency'));
  } else {
    root.myModule = factory(root.dependency);
  }
}(this, function (dependency) {
  return {
    publicMethod: function() {
      console.log("This is a public method");
    }
  };
}));
```

### 6. ES6 模块

使用 `import` 和 `export`，现代 JavaScript 推荐的方式。

```javascript
// module.js
export function publicMethod() {
  console.log("This is a public method");
}

// main.js
import { publicMethod } from './module.js';
publicMethod();
```
