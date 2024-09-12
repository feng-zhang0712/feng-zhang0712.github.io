# WebGL

## 什么是 WebGL？

WebGL (Web Graphics Library) 是一种 JavaScript API，用于在网页中渲染高性能的 2D 和 3D 图形。它基于 OpenGL ES 2.0，允许开发者直接在现代浏览器中运行复杂的图形应用程序而无需插件。因此，WebGL 是创建浏览器游戏、数据可视化、虚拟现实等的强大工具。

## WebGL 的基本概念

### 1. 上下文 (Context)

WebGL 的上下文是通过 HTML5 的 `<canvas>` 元素获得的。上下文是与绘图表面进行交互的接口。

```html
<canvas id="glCanvas" width="640" height="480"></canvas>
<script>
  const canvas = document.getElementById('glCanvas');
  const gl = canvas.getContext('webgl');
  if (!gl) {
    console.log('WebGL not supported, falling back on experimental-webgl');
    gl = canvas.getContext('experimental-webgl');
  }
  if (!gl) {
    alert('Your browser does not support WebGL');
  }
</script>
```

### 2. 着色器 (Shaders)

着色器是运行在 GPU 上的小程序，分为顶点着色器 (Vertex Shader) 和片段着色器 (Fragment Shader)。顶点着色器处理顶点数据，片段着色器处理像素数据。

```javascript
const vertexShaderSource = `
  attribute vec4 aVertexPosition;
  void main(void) {
    gl_Position = aVertexPosition;
  }
`;

const fragmentShaderSource = `
  void main(void) {
    gl_FragColor = vec4(1.0, 1.0, 1.0, 1.0);
  }
`;

function createShader(gl, type, source) {
  const shader = gl.createShader(type);
  gl.shaderSource(shader, source);
  gl.compileShader(shader);
  if (!gl.getShaderParameter(shader, gl.COMPILE_STATUS)) {
    console.error('An error occurred compiling the shaders: ' + gl.getShaderInfoLog(shader));
    gl.deleteShader(shader);
    return null;
  }
  return shader;
}

const vertexShader = createShader(gl, gl.VERTEX_SHADER, vertexShaderSource);
const fragmentShader = createShader(gl, gl.FRAGMENT_SHADER, fragmentShaderSource);
```

### 3. 着色器程序 (Shader Program)

着色器程序是将顶点着色器和片段着色器链接在一起的对象。

```javascript
function createProgram(gl, vertexShader, fragmentShader) {
  const program = gl.createProgram();
  gl.attachShader(program, vertexShader);
  gl.attachShader(program, fragmentShader);
  gl.linkProgram(program);
  if (!gl.getProgramParameter(program, gl.LINK_STATUS)) {
    console.error('Unable to initialize the shader program: ' + gl.getProgramInfoLog(program));
    return null;
  }
  return program;
}

const shaderProgram = createProgram(gl, vertexShader, fragmentShader);
```

### 4. 缓冲区 (Buffers)

缓冲区存储顶点数据、颜色数据等，并将这些数据传递给 GPU。

```javascript
const vertexBuffer = gl.createBuffer();
gl.bindBuffer(gl.ARRAY_BUFFER, vertexBuffer);
const vertices = new Float32Array([
  -0.5,  0.5,
   0.5,  0.5,
   0.0, -0.5,
]);
gl.bufferData(gl.ARRAY_BUFFER, vertices, gl.STATIC_DRAW);
```

### 5. 绘制 (Drawing)

将顶点着色器、片段着色器、缓冲区结合起来，执行绘制操作。

```javascript
gl.clearColor(0.0, 0.0, 0.0, 1.0);  // Clear the color to black
gl.clear(gl.COLOR_BUFFER_BIT);     // Clear the buffer

gl.useProgram(shaderProgram);

const vertexPosition = gl.getAttribLocation(shaderProgram, 'aVertexPosition');
gl.enableVertexAttribArray(vertexPosition);
gl.bindBuffer(gl.ARRAY_BUFFER, vertexBuffer);
gl.vertexAttribPointer(vertexPosition, 2, gl.FLOAT, false, 0, 0);

gl.drawArrays(gl.TRIANGLES, 0, 3);
```

## WebGL 的优点

1. **无需插件**：直接在浏览器中运行，无需安装额外的插件。
2. **跨平台**：支持多种操作系统和设备，只需一个现代浏览器。
3. **性能强大**：利用 GPU 进行硬件加速，适合高性能实时图形渲染。

## WebGL 的缺点

1. **复杂性**：学习曲线较陡，需要理解底层图形编程概念。
2. **浏览器兼容性**：虽然现代浏览器大多支持 WebGL，但在某些旧浏览器或设备上可能不支持。
3. **安全性**：复杂的图形编程可能会带来安全性问题，需要谨慎处理。

## WebGL 中的高级主题

### 1. 纹理 (Textures)

纹理是图像或位图，用于在几何图形上添加细节。

```javascript
const texture = gl.createTexture();
gl.bindTexture(gl.TEXTURE_2D, texture);

const image = new Image();
image.onload = function() {
  gl.bindTexture(gl.TEXTURE_2D, texture);
  gl.texImage2D(gl.TEXTURE_2D, 0, gl.RGBA, gl.RGBA, gl.UNSIGNED_BYTE, image);
  gl.generateMipmap(gl.TEXTURE_2D);
};
image.src = 'texture.png';
```

### 2. 光照 (Lighting)

通过模拟光照效果，使图形看起来更真实。

```javascript
const fragmentShaderSource = `
  precision mediump float;
  varying vec3 vLighting;
  void main(void) {
    gl_FragColor = vec4(vLighting, 1.0);
  }
`;

// 需要在顶点着色器中计算光照
```

### 3. 阴影 (Shadows)

通过阴影贴图等技术，实现物体间的阴影效果。

```javascript
// 创建阴影贴图并在渲染时应用
```

### WebGL 的工具和库

1. **Three.js**：一个高级的 3D 图形库，封装了 WebGL 的复杂细节，适合快速开发 3D 应用。
2. **Babylon.js**：一个功能强大的 3D 引擎，支持游戏开发和虚拟现实应用。
3. **ShaderToy**：一个在线平台，提供丰富的着色器示例和编辑器，适合学习和分享 WebGL 着色器。
