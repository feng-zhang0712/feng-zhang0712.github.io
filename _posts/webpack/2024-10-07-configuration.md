---
layout: post
title: webpack 常用配置项
categories: blog
tags:
  - frontend
  - webpack
---

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

## 参考

- webpack 文档，[webpack](https://webpack.docschina.org/)
