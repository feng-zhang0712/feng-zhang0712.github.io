在用户选择图片后，通过浏览器预览待上传的图片是一种常见的需求。可以使用HTML文件输入控件和JavaScript来实现此功能。以下是一个基本的实现步骤：

### HTML 部分

创建一个文件输入控件和一个用于显示预览的`<img>`标签：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>图片预览</title>
</head>
<body>
  
  <input type="file" id="imageInput" accept="image/*">
  <br>
  <img id="imagePreview" style="display: none;" height="200" alt="Image Preview">

  <script src="script.js"></script>
</body>
</html>
```

### JavaScript 部分

使用`FileReader` API读取用户选择的文件，并将其显示在`<img>`标签中：

```javascript
// script.js

document.getElementById('imageInput').addEventListener('change', function(event) {
  const file = event.target.files[0];
  
  if (file) {
    // 检查文件类型
    if (!file.type.startsWith('image/')) {
      alert('请选择一个图片文件');
      return;
    }
    
    const reader = new FileReader();
    
    // 读取文件完成时触发
    reader.onload = function(e) {
      const img = document.getElementById('imagePreview');
      img.src = e.target.result;
      img.style.display = 'block'; // 显示预览图片
    };
    
    // 读取文件
    reader.readAsDataURL(file);
  }
});
```

### 详细步骤

1. **创建文件输入控件和预览标签**：在HTML中创建一个`<input type="file">`用于选择图片，和一个`<img>`标签用于显示预览。
2. **添加事件监听器**：在JavaScript中为文件输入控件添加`change`事件监听器，当用户选择文件时触发。
3. **获取文件对象**：从事件对象中获取用户选择的文件对象。
4. **检查文件类型**：确保用户选择的是图片文件。
5. **使用FileReader读取文件**：创建FileReader对象，并使用`readAsDataURL`方法读取文件。
6. **显示预览图片**：在FileReader的`onload`事件回调中，将读取到的文件数据（一个base64编码的URL）赋值给`<img>`标签的`src`属性，并显示图片。

### 注意事项

1. **文件类型检查**：在上传文件之前，最好检查文件类型，确保用户选择的是图像文件。
2. **浏览器兼容性**：大多数现代浏览器都支持`FileReader` API，但在开发过程中仍需考虑浏览器兼容性问题。
3. **文件大小限制**：可以根据需求添加文件大小限制，避免上传过大的文件。

### 完整示例

将上述HTML和JavaScript代码合并在一起，形成一个完整的示例：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>图片预览</title>
</head>
<body>
  
  <input type="file" id="imageInput" accept="image/*">
  <br>
  <img id="imagePreview" style="display: none;" height="200" alt="Image Preview">

  <script>
    document.getElementById('imageInput').addEventListener('change', function(event) {
      const file = event.target.files[0];
      
      if (file) {
        // 检查文件类型
        if (!file.type.startsWith('image/')) {
          alert('请选择一个图片文件');
          return;
        }
        
        const reader = new FileReader();
        
        // 读取文件完成时触发
        reader.onload = function(e) {
          const img = document.getElementById('imagePreview');
          img.src = e.target.result;
          img.style.display = 'block'; // 显示预览图片
        };
        
        // 读取文件
        reader.readAsDataURL(file);
      }
    });
  </script>
</body>
</html>
```

这个示例展示了如何在用户选择图片后，通过浏览器预览待上传的图片。你可以根据需要进一步扩展和优化这段代码，例如添加文件大小检查、支持多个文件上传等。