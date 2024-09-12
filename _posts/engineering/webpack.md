# webpack

## 一、概述

webpack 是一个静态模块（比如各种资源文件）打包工具。webpack 会从一个或多个入口点构建依赖图(dependency graph)，然后将项目中所需的每个模块组合成一个或多个 bundles。

### 1.1 Module

Module（模块） 是 webpack 构建系统的基本单位。在 webpack 中，几乎所有的文件都被视为模块，包括 JavaScript、CSS、HTML、图片、字体等。模块是代码的最小单元，通过导入和导出实现代码的复用和分离。

模块封装了特定功能，独立于其他模块。模块之间可以相互依赖，通过 `import` 或 `require` 引入其他模块。不仅限于 JavaScript 文件，任何资源文件都可以作为模块。

### 1.2 Chunk

Chunk（代码块） 是 webpack 在打包过程中生成的中间产物。每个 Chunk 包含一组紧密相关的模块。最终，一个或多个 Chunk 会被合并成一个 Bundle。

Chunk 是 webpack 在打包过程中动态生成的。它使得代码拆分成为可能，可以根据不同的策略（如按需加载）生成多个 Chunk。Chunk 可以通过懒加载或动态导入的方式按需加载，以优化应用的加载性能。

```javascript
// src/index.js
import(/* webpackChunkName: "moduleA" */ './moduleA').then(({ greeting }) => {
  console.log(greeting);
});
```

### 1.3 Bundle

Bundle（包） 是 webpack 打包后的输出文件，它包含了应用程序的所有模块代码。Bundle 是浏览器可以执行的最终文件，通常是一个或多个 JavaScript 文件。

Bundle 是 webpack 打包的最终结果。在小型项目中，所有模块可能会被打包成一个单一的 Bundle 文件。在大型项目中，可以使用代码拆分（code splitting）将模块打包成多个 Bundle 文件，以优化加载性能。

### 1.4 webpack 中的占位符

1. **`[name]`**：表示模块的名称。通常用于多入口配置中，根据入口名称生成文件名。
2. **`[id]`**：表示模块的 ID。用于生成文件名中的模块 ID，适用于需要唯一标识模块的场景。
3. **`[hash]`**：表示编译时的哈希值。常用于生成具有唯一性的文件名，防止缓存问题。
4. **`[chunkhash]`**：表示代码块的哈希值。用于根据代码块内容生成哈希值，适用于代码分割和缓存优化。
5. **`[contenthash]`**：表示文件内容的哈希值。适用于根据文件内容生成哈希值，通常用于生成 CSS 文件名。
6. **`[ext]`**：表示文件的扩展名。用于保持文件的原始扩展名，适用于文件处理和加载。
7. **`[path]`**：表示包含文件的路径。用于生成包含文件路径的文件名，适用于文件管理和组织。
8. **`[query]`**：表示文件请求中的查询字符串。用于生成包含查询字符串的文件名，适用于动态加载和参数传递。

## 二、webpack 常用配置项

### 2.1 Entry

`entry` 指定 webpack 构建的入口。webpack 会从入口文件开始构建依赖图。在书写形式上，分为单入口、多入口和数组入口。

```javascript
// 单入口文件写法
module.exports = {
  entry: './src/index.js',
};

// 多个入口文件写法，它们将分别生成各自的打包文件
module.exports = {
  entry: {
    app: './src/app.js',
    admin: './src/admin.js',
  },
};

// 数组入口文件写法，表示多个入口文件将被打包到一个文件中
module.exports = {
  entry: ['./src/index.js', './src/vendor.js'],
};
```

### 2.2 Output

`output` 指定打包文件的输出位置和文件名。

### 2.3 Module

`module` 定义处理不同类型文件模块的规则。即如何处理除 `.js` 文件以外的其他资源，例如 CSS、图片、字体等。`module` 配置项主要包含 `rules` 和 `noParse` 两个属性。

#### (1) `module.rules`

`module.rules` 属性是 `module` 配置项的核心部分，它是一个数组，每个元素都是一个规则对象，用于定义如何使用 Loader 加载和转换不同类型的模块。`module.rules` 对象的主要属性如下。

- `test`：一个正则表达式，用于匹配文件路径。
- `exclude`：排除的文件路径，通常用于排除 `node_modules` 目录。
- `include`：包含的文件路径，通常用于指定处理哪些目录下的文件。
- `use`：指定使用的加载器（详细方式），可以是单个加载器或加载器数组。
- `loader`：指定使用的加载器（简写方式）。
- `options`：加载器的选项参数。
- `type`：指定模块的类型，例如 `json` 等。

以下是一个 `module.rules` 配置项的例子。

```javascript
module.exports = {
  module: {
    rules: [
      {
        test: /\.(png|jpg|gif)$/, // 匹配图片文件
        use: [
          {
            loader: 'file-loader',
            options: {
              name: '[name].[hash].[ext]', // 保持文件名和扩展名
              outputPath: 'images/', // 输出路径
            },
          },
        ],
      },
      {
        test: /\.(woff|woff2|eot|ttf|otf)$/, // 匹配字体文件
        use: [
          {
            loader: 'file-loader',
            options: {
              name: '[name].[hash].[ext]', // 保持文件名和扩展名
              outputPath: 'fonts/', // 输出路径
            },
          },
        ],
      },
    ],
  },
};
```

Loader 本质上是一个函数，主要用于在 webpack 构建过程中，将不同类型的文件转换为模块。Loader 可以处理 JavaScript 以外的资源文件，例如 CSS、SCSS、TypeScript、图片等。它们允许你在 `import` 或 `require` 模块时预处理文件。以下是一些 webpack 中常见的 Loader。

- `babel-loader`：用于将最新的 ES 语法转换为浏览器是别的 JavaScript。
- `style-loader`：将 css 添加到样式标签 `style` 中。
- `css-loader`：允许将 css 文件通过 require 的方式引入，并返回 css 代码。
- `sass-loader:`：处理 sass 文件。
- `postcss-loader`：用 postcss 来处理 CSS。
- `file-loader`：分发文件到 output 目录并返回相对路径。
- `url-loader`：和 `file-loader` 类似，可以选择性的把图片转成 base64 格式的字符串，并打包到 js 中，比较合适对小图片的处理。
- `html-minify-loader`：压缩 HTML。

注意，大部分 Loader 在使用之前要先下载。

#### （2）`module.noParse`

`module.noParse` 用于配置哪些模块文件不需要解析。通过 `noParse`，可以跳过对大型库或文件的解析。

```javascript
module.exports = {
  module: {
    noParse: /jquery|lodash/,
  },
};
```

上面的代码，告诉 webpack 在进行代码解析式，不解析 `jquery` 和 `lodash` 库。

### 2.4 Plugins

Plugin 用于扩展 webpack 的功能。它们可以在整个编译生命周期的不同阶段执行更广泛的任务，如 **打包优化**、**环境变量注入**、**代码压缩** 等。Plugin 可以比 Loader 处理更加复杂和多样化的任务。

Plugin 是具有 `apply` 方法的 JavaScript 对象。这个 `apply` 方法在 webpack 启动时被调用，并且插件可以通过 webpack 的钩子机制（hooks）在编译过程的各个阶段插入自己的逻辑。

以下是一些 webpack 中常见的 Plugin。

- `DefinePlugin`：允许在编译时创建配置的全局对象，它是一个 webpack 内置的插件，不需要安装。
- `html-webpack-plugin`：用于简化 HTML 文件的创建、管理以及注入打包后的 JavaScript 文件。
- `mini-css-extract-plugin`：将 CSS 从 JavaScript 中提取出来，生成独立的 CSS 文件。
- `css-minimizer-webpack-plugin`
- `terser-webpack-plugin`

### 2.5 Mode

`mode` 用于设置构建模式，影响 webpack 内置的优化功能。webpack 提供了三种模式。

- `development`：用于开发环境。在这个模式下， 主要侧重于提升构建速度和开发体验。在此模式下， webpack 不会压缩代码，并且会启用 Source Map、HMR（热模块替换）和详细的日志。
- `production`：用于生产环境。在这个模式下， 主要侧重于优化代码体积和运行性能。在此模式下， webpack 会启用代码压缩和混淆、自动移除未使用的代码（Tree Shaking）和启用各种优化（如代码拆分、作用域提升）等。
- `none`：不应用任何默认优化或配置。在这个模式下，只会按照配置文件中的指示进行打包，而不进行额外的优化和处理。

### 2.6 Resolve

`resolve` 控制模块解析的行为。包括定义模块路径的查找方式、文件扩展名的解析顺序、别名等。`resolve` 的主要配置项如下。

#### （1）`resolve.alias`

`resolve.alias` 用于为模块路径创建别名。通过别名，可以简化模块引用的路径。

```javascript
module.exports = {
  resolve: {
    alias: {
      '@components': path.resolve(__dirname, 'src/components/'),
    },
  },
};

import Button from '@components/Button';
```

上面的代码中，我们为 `src/components/` 目录指定了一个别名 `@components`，这样在导入模块时，就可以使用 `import xxx from '@components/xxx'` 的形式，而不必每次都写前边的路径。

#### （2）`resolve.extensions`

`resolve.extensions` 定义在解析模块时可以省略的文件扩展名。默认值是 `['.js', '.json']`。

#### （3）`resolve.modules`

`resolve.modules` 指定解析模块时要搜索的目录。默认值是 ['node_modules']。

```javascript
module.exports = {
  resolve: {
    modules: [path.resolve(__dirname, 'src'), 'node_modules'],
  },
};

import Util from 'utils/util'; // 实际引入的是 'src/utils/util.js'
```

上面的代码，我们指定了 `resolve.modules` 的目录为 `src` 和 `node_modules`。这样，在导入模块时，webpack 会首先去 `src` 目录下寻找，如果找不到，就去 `node_modules` 目录下寻找。

#### （4）`resolve.fallback`

`resolve.fallback` 指定当模块在指定目录中找不到时，应该回退到哪个目录进行查找。常用于处理某些模块在特定环境下的缺失问题。

#### （5）`resolve.plugins`

`resolve.plugins` 用于配置一些自定义的解析插件。可以定义自己的解析规则或使用现成的解析插件。

```javascript
const path = require('path');

module.exports = {
  resolve: {
    alias: {
      '@components': path.resolve(__dirname, 'src/components/'),
      '@utils': path.resolve(__dirname, 'src/utils/'),
    },
    extensions: ['.js', '.jsx', '.json', '.ts', '.tsx'],
    modules: [path.resolve(__dirname, 'src'), 'node_modules'],
    fallback: {
      'path': require.resolve('path-browserify'),
      'crypto': require.resolve('crypto-browserify'),
    },
     plugins: [
      // 在此添加解析插件
    ],

    // 其他配置...
  },
};
```

### 2.7 Devtool

`devtool` 用于控制生成 source map 的方式。source map 用于将编译后的代码映射回源代码，有助于调试和错误追踪。常见的 `devtool` 配置有如下几个。

- **`cheap-module-source-map`**：类似于 cheap-source-map，对 loader 处理后的代码也生成 source map。优点是构建速度较快，适合调试经过 loader 处理的代码。缺点是调试时无法准确定位到列，适合开发环境。
- **`source-map`**：生成独立的 `.map` 文件，提供完整的 source map。优点是可以查看源文件及其位置。缺点是构建速度慢，适合生产环境。
- **`eval`**：使用 `eval` 生成每个模块的 source map。优点是构建速度最快，缺点是生成的 source map 不支持断点调试，适合快速开发和调试。
- **`inline-source-map`**：将 source map 作为 Data URL 内嵌在编译后的代码中。优点是调试体验好，构建速度较快。缺点是会增加文件体积，适合开发环境。
- **`cheap-source-map`**：生成独立的 source map 文件，但不包含列信息。优点是构建速度较快，适合开发环境。缺点是调试时无法准确定位到列，适合开发环境。
- **`eval-source-map`**：每个模块使用 eval 生成 source map，并将 source map 作为 Data URL 内嵌。优点是构建速度快，调试体验好。适合开发环境。
- **`hidden-source-map`**：生成独立的 source map 文件，但不在编译后的代码中引用。优点是保护源代码，但仍然可以使用 source map 进行调试。缺点是需要手动关联 source map 文件，适合生产环境。
- **`nosources-source-map`**：生成独立的 source map 文件，但隐藏源文件内容。优点是保护源代码内容，但提供错误信息的映射。缺点是不能查看源代码，适合生产环境。

多数情况下，在开发中主要使用 `cheap-module-source-map`；而在生产环境中，主要使用 `source-map`。

### 2.8 DevServer

`devServer` 用于配置开发服务器。即设置开发环境中的本地服务器，以便进行实时预览、热模块替换（HMR）、代理请求等操作。`devServer` 常见的配置项如下。

- **`contentBase`**：指定开发服务器的根目录，即服务器将从哪个目录提供文件。
- **`https`**：是否启用 HTTPS 协议。
- **`host`**：指定开发服务器的主机名。
- **`port`**：指定开发服务器的端口号。
- **`open`**：是否自动打开浏览器。
- **`hot`**：是否启用模块热替换（HMR）。
- **`proxy`**：配置代理，将特定请求转发到其他服务器。
- **`compress`**：是否启用 gzip 压缩。
- **`before`** 和 **`after`**：在开发服务器启动之前或之后执行特定的中间件函数。

```javascript
const path = require('path');

module.exports = {
  devServer: {
    contentBase: path.join(__dirname, 'dist'), // 指定根目录
    https: false, // 使用 HTTP 协议
    host: '0.0.0.0', // 指定主机名
    port: 3000, // 指定端口号
    open: true, // 自动打开浏览器
    hot: true, // 启用模块热替换
    proxy: {
      '/api': {
        target: 'http://localhost:3000',
        changeOrigin: true,
      },
    },
    compress: true, // 启用 gzip 压缩
    before: function(app, server, compiler) {
      app.get('/some/path', function(req, res) {
        res.json({ custom: 'response' });
      });
    },
    after: function(app, server, compiler) {
      console.log('Dev server is running!');
    },

    // 其他配置项...
  },
};
```

### 2.9 Optimization

`optimization` 控制构建过程中的优化行为。以下是常见的 `optimization` 配置项。

- **`splitChunks`**：用于将代码拆分成更小的块，以实现代码的按需加载和更好的缓存策略。
- **`minimize`**：是否启用代码压缩。一般在生产环境中使用。
- **`minimizer`**：自定义代码压缩工具和压缩选项。默认使用 TerserPlugin 进行 JavaScript 代码压缩。
- **`providedExports`**：用于告诉 webpack 是否分析每个模块提供的导出内容。默认值为 true。
- **`usedExports`**：用于启用 Tree Shaking，去除未使用的导出。默认值为 true。

```javascript
const TerserPlugin = require('terser-webpack-plugin');
const path = require('path');

module.exports = {
  optimization: {
    splitChunks: {
      chunks: 'all', // 对所有类型的代码进行分离
    },
    minimize: true, // 启用代码压缩
    minimizer: [new TerserPlugin({
      terserOptions: {
        compress: {
          drop_console: true, // 移除 console 语句
        },
      },
    })],
    runtimeChunk: 'single', // 提取运行时代码到单独的 chunk
    moduleIds: 'deterministic', // 使用确定性的模块 ID
    chunkIds: 'deterministic', // 使用确定性的 chunk ID
    mangleWasmImports: true, // 压缩 WebAssembly 导入名称
    removeAvailableModules: true, // 移除可用的模块
    removeEmptyChunks: true, // 移除空的 chunk
    mergeDuplicateChunks: true, // 合并重复的 chunk
    flagIncludedChunks: true, // 标记包含的 chunk
    providedExports: true,
    usedExports: true
    sideEffects: true
    nodeEnv: 'production'
    realContentHash: true
  },
};
```

## 三、webpack 的发布-订阅模式

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

## 四、webpack 的构建流程

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

## 五、编写自定义 Loader 和 Plugin

### 5.1 自定义 Loader

编写自定义的 webpack Loader 可以让你根据项目需求进行特定的文件转换。在编写 Loader 时，有些规则和要求。

#### （1）函数签名

Loader 是一个接受文件内容（字符串或 Buffer）作为参数的函数。必须返回处理后的内容，可以是字符串或 Buffer。

#### （2）异步处理

如果 Loader 需要进行异步操作（如读取文件、网络请求等），可以使用 `this.async()` 来获取一个回调函数。调用回调函数时需要传递错误和结果。

```javascript
module.exports = function(source) {
  const callback = this.async();

  // 模拟异步操作
  setTimeout(() => {
    // 传递 null 表示没有错误，传递转换后的结果
    callback(null, source.toUpperCase());
  }, 1000);
};
```

#### （3）缓存

Loader 应该是无副作用的，并且可以缓存相同输入的结果。可以使用 `this.cacheable()` 来告诉 webpack 结果是可以缓存的。

```javascript
module.exports = function(source) {
  this.cacheable(true); // 启用缓存
  return source.toUpperCase();
};
```

#### （4）处理多个结果

如果需要返回多个结果（如附带 source map），可以返回一个包含多个元素的数组。

```javascript
module.exports = function(source) {
  const result = source.toUpperCase();
  const sourceMap = ''; // 示例 source map
  return [result, sourceMap];
};
```

#### （5）处理查询参数

Loader 可以通过查询参数接收额外的配置。

```javascript
module.exports = function(source) {
  const options = this.getOptions();
  if (options.enableUppercase) {
    return source.toUpperCase();
  }
  return source;
};
```

webpack 配置文件中传递查询参数：

```javascript
// webpack.config.js
module.exports = {
  module: {
    rules: [
      {
        test: /\.txt$/,
        use: {
          loader: path.resolve(__dirname, 'uppercase-loader.js'),
          options: { enableUppercase: true }
        }
      }
    ]
  }
};
```

#### （6）链式调用

Loader 可以以链式调用的方式组合使用。每个 Loader 都应该处理输入文件，并将结果传递给下一个 Loader。

#### （7）配置使用

Loader 编写完成之后，需要在 webpack 配置文件中配置 `module.rules`。它的使用方法，跟普通的 Loader 相同。

```javascript
// my-loader.js
// webpack.config.js
const path = require('path');

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist')
  },
  module: {
    rules: [
      {
        test: /\.txt$/,
        use: path.resolve(__dirname, 'my-loader.js') // 使用自定义 Loader
      }
    ]
  }
};
```

### 5.2 自定义 Plugin

编写自定义的 webpack Plugin 可以让你根据需求扩展 webpack 的功能。在编写 Plugin 时，有些规则和要求。

#### （1）类和 apply 方法

webpack Plugin 通常是一个 JavaScript 类，并拥有一个 `apply` 方法。`apply` 方法接受 `compiler` 对象作为参数，`compiler` 对象代表 webpack 的编译器实例。

```javascript
class MyPlugin {
  apply(compiler) {
    // 插件逻辑
  }
}
```

#### （2）使用钩子

webpack 使用 Tapable 库来提供钩子机制（hooks），插件可以在编译过程的各个阶段注册钩子函数。
常用的钩子有 `emit`, `done`, `compile`, `compilation` 等。

```javascript
compiler.hooks.emit.tapAsync('MyPlugin', (compilation, callback) => {
  // 插件逻辑
  callback();
});
```

- `tap`：同步钩子。
- `tapAsync`：异步钩子，使用回调。
- `tapPromise`：异步钩子，使用 Promise。

#### （3）访问 Compilation 对象

在钩子函数中，可以通过 `compilation` 对象访问编译过程中生成的资源（assets）、模块（modules）等。

```javascript
compiler.hooks.emit.tapAsync('MyPlugin', (compilation, callback) => {
  // 访问编译过程中生成的资源
  for (const filename in compilation.assets) {
    console.log('Processing:', filename);
  }
  callback();
});
```

#### （4） 配置选项

插件可以接受配置选项，通过构造函数传入并保存。

```javascript
class MyPlugin {
  constructor(options) {
    this.options = options;
  }

  apply(compiler) {
    // 使用 this.options 访问配置选项
  }
}
```

#### （5）处理异步操作

在异步钩子中，使用回调函数或返回 Promise 来处理异步操作。

```javascript
compiler.hooks.emit.tapAsync('MyPlugin', (compilation, callback) => {
  setTimeout(() => {
    // 异步操作完成后调用 callback
    callback();
  }, 1000);
});
```

#### （6）配置使用

自定义 Plugin 编写完成之后，就需要在 webpack 配置文件中加载和使用。

```javascript
// webpack.config.js
const path = require('path');
const MyPlugin = require('./MyPlugin.js');

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist')
  },
  plugins: [
    new MyPlugin({ /* 插件的配置选项 */ })
  ]
};
```

## 六、webpack 优化前端性能

### 6.1 启用代码压缩

#### （1）压缩 HTML 代码

`html-webpack-plugin` 用于简化 HTML 文件的创建、管理以及注入打包后的 JavaScript 文件。下面列出了一些 `html-webpack-plugin` 常用的配置项。

- **`title`**：页面的标题，会被插入到 `<title>` 标签中。
- **`filename`**：生成的 HTML 文件的名称，默认是 `index.html`。
- **`template`**：指定模板文件的路径。
- **`inject`**：注入选项，默认为 `true`，表示将打包的资源注入到生成的 HTML 文件中。它可以是 `true`、`false`、`'head'` 或者 `'body'`。
- **`favicon`**：指定 favicon 文件的路径。
- **`meta`**：注入页面的 `meta` 标签，可以是一个对象。
- **`hash`**：如果为 `true`，则会在所有的资源文件后面加一个唯一的 hash，防止缓存。
- **`minify`**：用于控制生成的 HTML 文件的代码压缩。这个属性接受一个对象，内含多个配置选项，分别控制不同的压缩行为。

通过配置 `html-webpack-plugin` 的 `minify` 属性来压缩 HTML 代码。以下是 `minify` 属性的常用选项。

- **`collapseWhitespace`**：压缩 HTML 中的空白。
- **`removeComments`**：移除 HTML 中的注释。
- **`removeRedundantAttributes`**：移除多余的属性（如 `<input type="text">` 中的 `type="text"`）。
- **`removeScriptTypeAttributes`**：移除 `<script>` 标签中的 `type="text/javascript"`。
- **`removeStyleLinkTypeAttributes`**：移除 `<link>` 和 `<style>` 标签中的 `type="text/css"`。
- **`useShortDoctype`**：将文档类型简化为 `<!DOCTYPE html>`。
- **`minifyCSS`**：压缩内联的 CSS。
- **`minifyJS`**：压缩内联的 JavaScript。

```javascript
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  plugins: [
    new HtmlWebpackPlugin({
      template: './src/index.html',
      minify: {
        collapseWhitespace: true,
        removeComments: true,
        removeRedundantAttributes: true,
        removeScriptTypeAttributes: true,
        removeStyleLinkTypeAttributes: true,
        useShortDoctype: true,
        minifyCSS: true,
        minifyJS: true,
      }
    })
  ]
};
```

#### （2）压缩 CSS 代码

`css-minimizer-webpack-plugin` 是一个用于压缩 CSS 代码的 Webpack 插件，它基于 cssnano 提供的优化功能，能够有效地减少 CSS 文件的体积，从而提升加载性能。

- **`test`**：匹配需要压缩的文件，默认值是 `/\.css$/i`。
- **`parallel`**：启用并行压缩以提升构建速度，默认为 `true`。
- **`minimizerOptions`**：传递给 cssnano 的配置选项。

```javascript
const CssMinimizerPlugin = require('css-minimizer-webpack-plugin');

module.exports = {
  optimization: {
    minimize: true,
    minimizer: [
      `...`,
      new CssMinimizerPlugin({
        test: /\.css$/i,
        parallel: true,
        minimizerOptions: {
          preset: [
            'default',
            {
              discardComments: { removeAll: true }, // 移除所有注释
            },
          ],
        },
      }),
    ],
  },
};
```

#### （3）压缩 JavaScript 代码

`terser-webpack-plugin` 用于压缩和优化 JavaScript 代码。它基于 Terser，一个 JavaScript 解析器、压缩器、美化器和 AST 工具包。`terser-webpack-plugin` 是 Webpack 内置的 JavaScript 压缩插件，在生产环境下默认使用，可以通过自定义配置优化其行为。

- **`test`**：匹配需要压缩的文件，默认值是 `/\.m?js(\?.*)?$/i`。
- **`parallel`**：启用并行压缩以提升构建速度，默认为 `true`。
- **`extractComments`**：提取注释到单独的文件中，默认为 `true`。
- **`terserOptions`**：传递给 Terser 的配置选项，详细配置见 Terser Options。

```javascript
const TerserPlugin = require('terser-webpack-plugin');

module.exports = {
  optimization: {
    minimize: true,
    minimizer: [
      new TerserPlugin({
        parallel: true,
        extractComments: false,
        terserOptions: {
          compress: {
            drop_console: true,
          },
          format: {
            comments: false,
          },
        },
      }),
    ],
  },
  // ...
};
```

### 6.2 图片压缩

`file-loader` 的主要作用是将文件导出到输出目录，并返回文件的 URL。它可以处理各种类型的文件，包括图片、字体、音频、视频等。以下是几个常用的 `file-loader` 配置项。

- `name`：设置文件的命名格式，可以使用占位符（如 `[name]`、`[ext]`、`[hash]` 等）。
- `outputPath`：设置文件输出目录，可以是相对于输出路径的相对路径。
- `publicPath`：设置文件的公共路径（通常用于 CDN）。
- `emitFile`：是否输出文件，默认为 `true`。

`image-webpack-loader` 是一个图片压缩加载器，可以结合 `file-loader` 使用，处理后的图片会自动进行压缩优化，从而减少文件体积，提高加载性能。以下是几个常用的 `image-webpack-loader` 配置项。

- `mozjpeg`：配置 `mozjpeg` 压缩工具的选项。
  - `progressive`：是否开启渐进式 JPEG。
  - `quality`：图像质量，范围是 0-100。
- `optipng`：配置 `optipng` 压缩工具的选项。
  - `enabled`：是否开启 `optipng` 压缩。
  - `optimizationLevel`：优化级别，范围是 0-7。
- `pngquant`：配置 `pngquant` 压缩工具的选项。
  - `quality`：图像质量范围，如 [0.65, 0.90]。
  - `speed`：压缩速度，范围是 1-11。
- `gifsicle`：配置 `gifsicle` 压缩工具的选项。
  - `interlaced`：是否开启交错模式。
- `webp`：配置 `webp` 压缩工具的选项。
  - `quality`：图像质量，范围是 0-100。

```javascript
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  module: {
    rules: [
      {
        test: /\.(png|jpe?g|gif|svg)$/i,
        use: [
          {
            loader: 'file-loader',
            options: {
              name: '[name].[hash].[ext]',
              outputPath: 'images/',
            },
          },
          {
            loader: 'image-webpack-loader',
            options: {
              mozjpeg: {
                progressive: true,
                quality: 65,
              },
              optipng: { enabled: true },
              pngquant: {
                quality: [0.65, 0.90],
                speed: 4,
              },
              gifsicle: { interlaced: false },
              webp: { quality: 75 },
            },
          },
        ],
      },
    ],
  },
  // ...
};
```

### 6.3 Tree Shaking

Tree Shaking 通过在编译时分析模块的依赖关系，移除那些没有被引用的代码，从而减少最终打包文件的体积。为了启用 Tree Shaking，需要使用 ES6 模块语法（import/export）并配置 optimization。

```javascript
module.exports = {
  optimization: {
    usedExports: true, // 启用 Tree Shaking
  },
};
```

### 6.4 代码分离（Code Splitting）

将代码分离到不同的 bundle 中，之后我们可以按需加载，或者并行加载这些文件。默认情况下，所有的 JavaScript 代码（业务代码、第三方依赖、暂时没有用到的模块）在首页全部都加载，就会影响首页的加载速度。代码分离可以分出出更小的 bundle，以及控制资源加载优先级，提供代码的加载性能。

SplitChunksPlugin 是 Webpack 提供的一个内置插件，可以用于实现代码分离（code splitting）。SplitChunksPlugin 的常用配置如下。

- **`chunks`**：可以是 `'all'`、`'async'` 或 `'initial'`。指定要分离的块类型。
- **`minSize`**：生成块的最小大小（以字节为单位），默认值为 30000。
- **`maxSize`**：生成块的最大大小（以字节为单位），超过此大小会进一步分离。
- **`minChunks`**：最少引用次数，文件被引用次数超过此值时才会被分离，默认值为 1。
- **`maxAsyncRequests`**：按需加载时的最大并行请求数，默认值为 6。
- **`maxInitialRequests`**：入口点的最大并行请求数，默认值为 4。
- **`automaticNameDelimiter`**：生成名称时的分隔符，默认值为 ~。
- **`name`**：指定分离块的名称，可以是 `true`、`false` 或者一个函数。
- **`cacheGroups`**：指定缓存组，为不同的分离规则定义不同的缓存组。

```javascript
module.exports = {
  optimization: {
    splitChunks: {
      chunks: 'all',
      minSize: 20000,
      maxSize: 70000,
      minChunks: 1,
      maxAsyncRequests: 5,
      maxInitialRequests: 3,
      automaticNameDelimiter: '~',
      cacheGroups: {
        vendors: {
          test: /[\\/]node_modules[\\/]/,
          name: 'vendors',
          chunks: 'all',
        },
        default: {
          minChunks: 2,
          priority: -20,
          reuseExistingChunk: true,
        },
      },
    },
  },
  // ...
};
```

上面代码中，我们定义了两个缓存组。`vendors` 缓存组用来将所有来自 `node_modules` 目录的模块分离到一个名为 `vendors` 的块中。`default` 缓存组用来将被至少两次引用的模块分离到一个默认块中，并且优先级较低。

## 七、提高 webpack 的构建速度

## 八、参考

- webpack 文档，[webpack](https://webpack.docschina.org/)
