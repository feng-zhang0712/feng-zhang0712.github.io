CSS3 引入了许多新特性，极大地增强了网页设计和开发的能力。以下是一些主要的 CSS3 新特性及其示例：

### 1. 边框和背景（Borders and Backgrounds）

- **圆角边框（Border Radius）**

  创建圆角边框。

  ```css
  .box {
    border: 1px solid #000;
    border-radius: 10px;
  }
  ```

- **背景渐变（Background Gradient）**

  无需图像即可创建渐变背景。

  ```css
  .box {
    background: linear-gradient(to right, red, blue);
  }
  ```

### 2. 盒子模型（Box Model）

- **盒子阴影（Box Shadow）**

  添加阴影效果。

  ```css
  .box {
    box-shadow: 5px 5px 15px 5px #000;
  }
  ```

- **盒子尺寸（Box Sizing）**

  控制盒子模型的计算方式。

  ```css
  .box {
    box-sizing: border-box;
  }
  ```

### 3. 颜色（Colors）

- **RGBA 和 HSLA 颜色**

  支持透明度的颜色。

  ```css
  .box {
    background-color: rgba(255, 0, 0, 0.5); /* 半透明红色 */
    color: hsla(120, 100%, 50%, 0.7); /* 半透明绿色 */
  }
  ```

### 4. 文本效果（Text Effects）

- **文本阴影（Text Shadow）**

  添加文本阴影。

  ```css
  .text {
    text-shadow: 2px 2px 5px #000;
  }
  ```

- **多列布局（Multi-Column Layout）**

  支持多列文本布局。

  ```css
  .columns {
    column-count: 3;
    column-gap: 20px;
  }
  ```

### 5. 变换（Transforms）

- **2D 变换（2D Transforms）**

  旋转、缩放、平移和倾斜元素。

  ```css
  .box {
    transform: rotate(45deg);
  }
  ```

- **3D 变换（3D Transforms）**

  在三维空间中变换元素。

  ```css
  .box {
    transform: rotateX(45deg) rotateY(45deg);
  }
  ```

### 6. 过渡和动画（Transitions and Animations）

- **过渡（Transitions）**

  平滑地过渡元素的变化。

  ```css
  .box {
    transition: width 2s; /* 2 秒内平滑地过渡宽度变化 */
  }

  .box:hover {
    width: 300px;
  }
  ```

- **动画（Animations）**

  创建关键帧动画。

  ```css
  @keyframes example {
    from {background-color: red;}
    to {background-color: yellow;}
  }

  .box {
    animation: example 5s infinite; /* 5 秒内完成动画，并无限循环 */
  }
  ```

### 7. 布局（Layouts）

- **弹性盒子布局（Flexbox）**

  创建灵活的布局。

  ```css
  .container {
    display: flex;
    justify-content: center; /* 主轴居中 */
    align-items: center; /* 交叉轴居中 */
    height: 100vh;
  }

  .item {
    flex: 1; /* 项目将按比例分配剩余空间 */
  }
  ```

- **网格布局（Grid Layout）**

  更强大的二维布局系统。

  ```css
  .container {
    display: grid;
    grid-template-columns: repeat(3, 1fr); /* 创建三列等宽网格 */
    grid-gap: 10px; /* 设置网格间距 */
  }

  .item {
    grid-column: span 2; /* 项目跨越两列 */
  }
  ```

### 8. 媒体查询（Media Queries）

根据不同设备的特性（如屏幕宽度）应用不同的样式，是响应式设计的核心。

```css
/* 针对屏幕宽度小于600px的设备 */
@media (max-width: 600px) {
  .box {
    background-color: lightblue;
  }
}
```

### 9. 其他新特性

- **弹性单位（Flexible Units）**

  使用 `vh`、`vw`、`vmin` 和 `vmax` 单位。

  ```css
  .box {
    width: 50vw; /* 视口宽度的50% */
    height: 50vh; /* 视口高度的50% */
  }
  ```

- **自定义属性（CSS Variables）**

  定义和使用自定义属性。

  ```css
  :root {
    --main-color: #06c;
  }

  .box {
    color: var(--main-color);
  }
  ```

### 总结

以上是 CSS3 的一些主要新特性及其示例。这些新特性使得网页设计和开发变得更加灵活和强大，可以更轻松地实现复杂的布局和视觉效果。通过熟练掌握这些新特性，开发者可以创建出更加现代、响应式和动态的网页。