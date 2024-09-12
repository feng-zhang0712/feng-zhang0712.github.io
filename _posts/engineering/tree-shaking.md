# Tree Shaking

Tree Shaking 是一种用于优化 JavaScript 打包过程的技术，其主要目的是移除那些在项目中没有被使用的代码（即未引用的代码）。它在现代 JavaScript 应用程序中尤为重要，因为项目往往包含大量的库和模块，而其中很多代码实际上并没有被使用。通过 Tree Shaking，开发者可以显著减少打包后的文件体积，从而提升应用的加载速度和性能。

## Tree Shaking 的工作原理

Tree Shaking 依赖于 ES6 模块的静态结构特点。ES6 模块（使用 `import` 和 `export` 语法）允许工具在编译时确定哪些模块和函数是未被使用的。以下是 Tree Shaking 的基本工作流程：

1. **静态分析**：编译器分析代码中的 `import` 和 `export` 语句，构建模块依赖树。
2. **引用标记**：标记出那些实际被代码引用的模块和函数。
3. **移除未引用代码**：移除那些没有被标记为引用的代码段。

假设我们有以下两个模块：

```javascript
// math.js
export function add(a, b) {
  return a + b;
}

export function subtract(a, b) {
  return a - b;
}

// main.j
import { add } from './math.js';

console.log(add(2, 3));
```

在这个例子中，`main.js` 只使用了 `math.js` 模块中的 `add` 函数，而 `subtract` 函数没有被使用。通过 Tree Shaking，打包工具会在最终的打包结果中移除 `subtract` 函数。

## 使用 Webpack 实现 Tree Shaking

以下是如何在 Webpack 中启用 Tree Shaking 的基本步骤：

1. **确保使用 ES6 模块**：你的代码和依赖库都应使用 ES6 模块语法（即 `import` 和 `export`）。
2. **启用生产模式**：Webpack 在生产模式下会自动启用 Tree Shaking。

```javascript
// webpack.config.js
const path = require('path');

module.exports = {
  entry: './src/main.js',
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist'),
  },
  mode: 'production', // 启用生产模式
};
```

### 注意事项

1. **副作用**：在某些情况下，模块可能包含副作用（side effects）。在这种情况下，Tree Shaking 可能会错误地移除这些代码。可以通过在 `package.json` 中的 `sideEffects` 字段显式声明哪些文件有副作用。

```json
{
  "name": "your-project",
  "version": "1.0.0",
  "main": "dist/bundle.js",
  "sideEffects": [
    "*.css",
    "*.scss"
  ]
}
```

2. **与其他优化技术的结合**：Tree Shaking 通常与其他代码优化技术（如代码分割、懒加载等）结合使用，以实现最佳的性能优化。
