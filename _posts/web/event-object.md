在JavaScript中，事件对象（Event Object）包含了大量属性和方法，用于获取事件的详细信息和控制事件的行为。

### 一、事件属性

#### 1. 基本属性

- **type**：事件的类型，例如 `click`、`keydown` 等。
  ```javascript
  element.addEventListener('click', function(event) {
    console.log(event.type); // "click"
  });
  ```

- **target**：触发事件的元素。
  ```javascript
  element.addEventListener('click', function(event) {
    console.log(event.target); // 点击的元素
  });
  ```

- **currentTarget**：绑定事件处理程序的元素。
  ```javascript
  element.addEventListener('click', function(event) {
    console.log(event.currentTarget); // 绑定事件处理程序的元素
  });
  ```

- **eventPhase**：事件流的当前阶段（捕获、目标、冒泡）。
  ```javascript
  element.addEventListener('click', function(event) {
    console.log(event.eventPhase); // 1, 2, 或 3
  });
  ```

- **bubbles**：布尔值，表示事件是否冒泡。
  ```javascript
  element.addEventListener('click', function(event) {
    console.log(event.bubbles); // true
  });
  ```

- **cancelable**：布尔值，表示事件是否可以取消。
  ```javascript
  element.addEventListener('click', function(event) {
    console.log(event.cancelable); // true
  });
  ```

- **timeStamp**：事件生成的时间戳。
  ```javascript
  element.addEventListener('click', function(event) {
    console.log(event.timeStamp); // 时间戳
  });
  ```

- **defaultPrevented**：布尔值，表示是否调用了 `preventDefault()` 方法。
  ```javascript
  element.addEventListener('click', function(event) {
    console.log(event.defaultPrevented); // true 或 false
  });
  ```

#### 2. 鼠标事件属性

- **clientX** 和 **clientY**：相对于视口的鼠标位置。
  ```javascript
  element.addEventListener('click', function(event) {
    console.log(event.clientX, event.clientY); // 鼠标位置
  });
  ```

- **pageX** 和 **pageY**：相对于文档的鼠标位置。
  ```javascript
  element.addEventListener('click', function(event) {
    console.log(event.pageX, event.pageY); // 鼠标位置
  });
  ```

- **screenX** 和 **screenY**：相对于屏幕的鼠标位置。
  ```javascript
  element.addEventListener('click', function(event) {
    console.log(event.screenX, event.screenY); // 鼠标位置
  });
  ```

- **altKey**、**ctrlKey**、**shiftKey** 和 **metaKey**：布尔值，表示是否按下了对应的修饰键。
  ```javascript
  element.addEventListener('click', function(event) {
    console.log(event.altKey, event.ctrlKey, event.shiftKey, event.metaKey); // true 或 false
  });
  ```

- **button**：表示按下的鼠标按钮（0：左键，1：中键，2：右键）。
  ```javascript
  element.addEventListener('click', function(event) {
    console.log(event.button); // 0, 1, 或 2
  });
  ```

#### 3. 键盘事件属性

- **key**：按下的键名。
  ```javascript
  document.addEventListener('keydown', function(event) {
    console.log(event.key); // 按下的键名
  });
  ```

- **keyCode**：按下的键的代码（已废弃，建议使用 `key`）。
  ```javascript
  document.addEventListener('keydown', function(event) {
    console.log(event.keyCode); // 按下的键的代码
  });
  ```

- **code**：表示按下的物理键位。
  ```javascript
  document.addEventListener('keydown', function(event) {
    console.log(event.code); // 物理键位
  });
  ```

### 二、事件方法

#### 1. `preventDefault()`

取消事件的默认行为，例如防止表单提交或链接跳转。

```javascript
document.getElementById('myLink').addEventListener('click', function(event) {
  event.preventDefault();
  console.log('Link click prevented!');
});
```

#### 2. `stopPropagation()`

停止事件的传播，防止事件冒泡到父元素。

```javascript
document.getElementById('child').addEventListener('click', function(event) {
  event.stopPropagation();
  console.log('Child clicked!');
});
document.getElementById('parent').addEventListener('click', function() {
  console.log('Parent clicked!');
});
```

#### 3. `stopImmediatePropagation()`

停止事件的传播，并阻止当前元素上所有剩余事件处理程序的执行。

```javascript
document.getElementById('myButton').addEventListener('click', function(event) {
  event.stopImmediatePropagation();
  console.log('First handler');
});
document.getElementById('myButton').addEventListener('click', function() {
  console.log('Second handler');
});
```

#### 4. `initEvent()`

初始化新创建的事件对象（不常用，通常使用 `CustomEvent`）。

```javascript
const event = document.createEvent('Event');
event.initEvent('build', true, true);
document.dispatchEvent(event);
```

### 三、综合示例

以下是一个综合示例，展示如何使用上述属性和方法进行事件处理：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Event Handling in JavaScript</title>
</head>
<body>
  <div id="parent">
    <button id="myButton">Click Me</button>
  </div>
  <a href="https://example.com" id="myLink">Example Link</a>

  <script>
    document.getElementById('myButton').addEventListener('click', function(event) {
      console.log('Event type:', event.type);
      console.log('Target element:', event.target);
      console.log('Current target:', event.currentTarget);
      console.log('Mouse position:', event.clientX, event.clientY);
      
      event.stopPropagation();
    });

    document.getElementById('parent').addEventListener('click', function(event) {
      console.log('Parent element clicked!');
    });

    document.getElementById('myLink').addEventListener('click', function(event) {
      event.preventDefault();
      console.log('Link click prevented!');
    });

    document.addEventListener('keydown', function(event) {
      console.log('Key pressed:', event.key, event.keyCode);
    });
  </script>
</body>
</html>
```

### 四、总结

JavaScript中的事件对象提供了丰富的属性和方法，使我们能够获取事件的详细信息，并控制事件的行为。常用的属性包括 `type`、`target`、`currentTarget`、`clientX`、`clientY` 以及 `key` 等等。常用的方法包括 `preventDefault()`、`stopPropagation()` 和 `stopImmediatePropagation()` 等等。掌握这些属性和方法能够帮助我们更加灵活和高效地处理各种事件，提高用户交互体验。