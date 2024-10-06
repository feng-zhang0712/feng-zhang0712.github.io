---
layout: post
title: webpack 构建流程
categories: blog
tags:
  - frontend
  - webpack
---

webpack 的构建流程，主要包括初始化、编译、打包、优化和生成输出五个阶段。

### 4.1 初始化阶段

在初始化阶段，webpack 根据配置文件和命令行参数进行初始化工作。包括以下步骤。

- **读取配置**：读取 webpack 配置文件（`webpack.config.js`）并解析配置项。
- **注册插件**：根据配置文件中的 `plugins` 项注册所有插件。
- **创建编译器对象**：初始化 `Compiler` 对象，这是 webpack 构建的核心引擎，负责整个构建过程的协调工作。
- **应用插件**：调用每个插件的 `apply` 方法，将插件挂载到 webpack 的钩子上。
- **触发环境钩子**：触发 `environment` 和 `afterEnvironment` 等初始化阶段的钩子。

```javascript
const config = require('./webpack.config.js');
const webpack = require('webpack');

const compiler = webpack(config);

compiler.run((err, stats) => {
  if (err) {
    console.error(err);
    return;
  }
  console.log(stats.toString());
});
```

初始化阶段的主要钩子如下。

- `initialize`：初始化 webpack。
- `environment`：设置环境变量。
- `afterEnvironment`：环境变量设置完成。
- `entryOption`：处理入口选项。

### 4.2 编译阶段

编译阶段主要负责将源代码转换为 webpack 的模块化表示形式。

- **确定入口点**：根据配置中的 `entry` 项确定入口模块。
- **递归解析模块**：从入口点出发，递归解析依赖的模块。每个模块都会经过相应的 Loader 处理，转换为 webpack 能够理解的模块。
- **生成模块依赖图**：构建模块与模块之间的依赖关系图。

以下是编译阶段的详细流程。

1. **创建 Compilation 对象**：`Compiler` 对象会创建一个新的 `Compilation` 对象，用来存储当前编译的状态和结果。
2. **触发 Compilation 钩子**：触发 `thisCompilation` 和 `compilation` 钩子，通知插件开始编译。
3. **构建模块**：
   - **从入口开始**：从入口模块开始，使用 `loader` 对模块进行转换。
   - **递归解析**：解析模块的依赖（如 `import` 或 `require` 语句）并递归处理依赖的模块。
   - **生成抽象语法树（AST）**：解析模块代码并生成 AST。
   - **应用 Loader**：按照配置的 `loader` 对模块进行处理。
   - **添加模块到依赖图**：将处理后的模块添加到依赖图中。

编译阶段的主要钩子如下。

- `beforeCompile`：编译开始前。
- `compile`：编译阶段开始。
- `thisCompilation`：新的 Compilation 创建前。
- `compilation`：新的 Compilation 创建后。

### 4.3 打包阶段

打包阶段负责将编译后的模块根据依赖图进行打包，生成一个或多个 Chunk。

- **生成 Chunk**：根据入口和依赖关系生成 Chunk，每个 Chunk 代表一个输出文件。
- **添加 Chunk 到 Compilation**：将生成的 Chunk 添加到 `Compilation` 对象中。

打包阶段的主要钩子如下。

- `make`：从入口点开始构建模块。
- `afterCompile`：编译完成。

### 4.4 优化阶段

在优化阶段，webpack 会对生成的 Chunk 进行优化，如代码压缩、提取公共模块等。

- **代码拆分**：提取公共模块、动态导入模块等。
- **压缩代码**：根据配置对代码进行压缩和混淆。
- **其他优化**：如 Tree Shaking、Scope Hoisting 等。

### 4.5 生成输出阶段

生成输出阶段负责将优化后的代码输出到指定的目录。

- **生成输出资源**：根据 Chunk 生成最终的输出文件（如 JavaScript、CSS、HTML 等）。
- **写入文件系统**：将生成的文件写入到输出目录（通常是 `dist` 目录）。

以下是生成输出阶段的详细流程。

1. **触发 `emit` 钩子**：在写入文件前触发 `emit` 钩子，通知插件进行最后的修改或添加额外的文件。
2. **写入文件到输出目录**：将生成的文件写入到输出目录。
3. **触发 `afterEmit` 钩子**：写入文件后触发 `afterEmit` 钩子，通知插件输出过程已完成。

生成输出阶段的主要钩子如下。

- `emit`：生成资源到输出目录前。
- `afterEmit`：生成资源到输出目录后。

## 参考

- webpack 文档，[webpack](https://webpack.docschina.org/)
