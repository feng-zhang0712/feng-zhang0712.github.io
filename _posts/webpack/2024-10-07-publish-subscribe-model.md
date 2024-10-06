---
layout: post
title: webpack 的发布-订阅模式
categories: blog
tags:
  - frontend
  - webpack
---

### 3.1 什么是发布-订阅模式？

**发布-订阅模式** 是一种设计模式，其中一个主体（发布者）维护一系列依赖其状态的观察者（订阅者）。发布者在状态变化时向观察者发送通知，而观察者可以选择性地对这些通知做出响应。

webpack 的插件系统基于发布-订阅模式。这个模式允许 webpack 在构建过程的不同阶段广播事件，插件可以订阅这些事件并在适当的时机执行特定的任务。

在 webpack 中，发布者是 webpack 本身，它在构建过程的不同阶段广播事件。订阅者是 webpack 插件，它们订阅感兴趣的事件并在这些事件触发时执行特定逻辑。

### 3.2 webpack 生命周期广播事件

webpack 在其构建生命周期的不同阶段会广播一系列事件。

- **`initialize`**：初始化阶段，webpack 构建过程开始。
- **`environment`**：设置环境变量。
- **`afterEnvironment`**：环境变量设置完成。
- **`entryOption`**：处理入口选项。
- **`afterPlugins`**：插件初始化完成。
- **`afterResolvers`**：解析器初始化完成。
- **`beforeRun`**：构建过程开始前。
- **`run`**：构建过程开始。
- **`beforeCompile`**：编译前准备阶段。
- **`compile`**：编译阶段开始。
- **`thisCompilation`**：新的 Compilation 创建前。
- **`compilation`**：新的 Compilation 创建后。
- **`make`**：从入口点开始构建模块。
- **`afterCompile`**：编译完成。
- **`emit`**：生成资源到输出目录前。
- **`afterEmit`**：生成资源到输出目录后。
- **`done`**：构建过程完成。

### 3.3 插件如何与广播事件协调工作

webpack 插件通过订阅这些事件来协调工作。插件在注册时将其逻辑挂载到特定的事件上，当事件被触发时，插件的逻辑会自动执行。

```javascript
class MyPlugin {
  apply(compiler) {
    compiler.hooks.emit.tapAsync('MyPlugin', (compilation, callback) => {
      // 插件的逻辑
      console.log('This is my plugin!');
      callback();
    });
  }
}

module.exports = MyPlugin;
```

上面代码是一个自定义的插件，其中：

- `apply` 方法用于注册插件。
- `compiler` 对象是 webpack 编译器实例，提供了绑定事件的钩子（hooks）。
- `compiler.hooks.emit` 表示 `emit` 事件的钩子。`tapAsync` 方法用于异步钩子，注册一个回调函数，该函数将在 `emit` 事件被触发时执行。

### 3.4 webpack 插件与生命周期事件的工作流程

1. 插件注册：插件通过 `apply` 方法注册到 webpack 编译器。在 `apply` 方法中，插件通过 `compiler` 对象的钩子（hooks）订阅感兴趣的事件。
2. 事件触发：webpack 在构建过程的不同阶段触发事件。当事件被触发时，所有订阅了该事件的插件都将依次执行其注册的回调函数。
3. 回调执行：插件的回调函数执行其特定的逻辑。例如，修改编译资源、生成额外的文件、优化输出等。

```javascript
const fs = require('fs');
const path = require('path');

class MyPlugin {
  constructor(options) {
    this.options = options;
  }

  apply(compiler) {
    compiler.hooks.emit.tapAsync('MyPlugin', (compilation, callback) => {
      // 生成一个额外的文件
      const content = 'This is a generated file';
      const outputPath = path.join(compiler.options.output.path, 'generated-file.txt');

      compilation.assets['generated-file.txt'] = {
        source: () => content,
        size: () => content.length
      };

      // 写入文件到输出目录
      fs.writeFile(outputPath, content, (err) => {
        if (err) throw err;
        console.log('The file has been saved!');
        callback();
      });
    });
  }
}

module.exports = MyPlugin;
```

上面的代码，是一个自定义的插件，它会在 `emit` 阶段生成一个额外的文件。

- 插件在 `emit` 阶段生成一个名为 `generated-file.txt` 的文件，并将其内容设置为 `"This is a generated file"`。
- 通过 `compilation.assets` 将生成的文件添加到 webpack 的资源管理中。
- 异步写入文件到输出目录，当写入完成后调用 `callback` 以通知 webpack 继续构建过程。

## 参考

- webpack 文档，[webpack](https://webpack.docschina.org/)
