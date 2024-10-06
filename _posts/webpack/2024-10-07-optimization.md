---
layout: post
title: webpack 项目优化
categories: blog
tags:
  - frontend
  - webpack
---

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

## 参考

- webpack 文档，[webpack](https://webpack.docschina.org/)
