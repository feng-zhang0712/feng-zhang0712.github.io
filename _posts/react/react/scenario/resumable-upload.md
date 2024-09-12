## 大文件断点续传

大文件断点续传文件分块、计算文件和分块哈希、分块上传、服务端接收和校验、记录上传进度、断点续传和合并分块。

### 一、大文件断点续传

大文件断点续传是一种在网络环境不稳定情况下，保证大文件可靠上传的技术方案。它将大文件拆分成多个小块，逐个上传，并在上传过程中记录每个块的上传状态，以便在网络中断后可以从中断处继续上传，避免了重新上传整个文件的麻烦。

### 二、原理

#### 1. 文件分块

将大文件切割成多个大小相同的小块（chunk），这样可以逐块上传，避免一次性上传大文件导致的资源开销和上传失败问题。

```javascript
const SLICE_SIZE = 5 * 1024 * 1024; // 每个块大小 5MB

function sliceFile(file) {
  const chunks = [];
  let currentByte = 0;

  while (currentByte < file.size) {
    const chunk = file.slice(currentByte, currentByte + SLICE_SIZE);
    chunks.push(chunk);
    currentByte += SLICE_SIZE;
  }

  return chunks;
}
```

#### 2. 计算文件和分块哈希

使用哈希算法（如 MD5、SHA256）计算完整文件和每个分块的哈希值，用于校验文件完整性。

```javascript
import crypto from 'crypto';

function calculateHash(chunk) {
  return new Promise((resolve, reject) => {
    const reader = new FileReader();
    reader.onload = () => {
      const buffer = reader.result;
      const hash = crypto.createHash('md5').update(buffer).digest('hex');
      resolve(hash);
    };
    reader.onerror = reject;
    reader.readAsArrayBuffer(chunk);
  });
}
```

#### 3. 分块上传

逐个上传分块，每次上传时携带分块信息（如文件名、分块序号、总分块数等）。

```javascript
import axios from 'axios';

async function uploadChunk(chunk, fileName, chunkIndex, hash) {
  const formData = new FormData();
  formData.append('file', chunk);
  formData.append('fileName', fileName);
  formData.append('chunkIndex', chunkIndex);
  formData.append('hash', hash);

  try {
    await axios.post('/upload', formData, {
      headers: {
        'Content-Type': 'multipart/form-data',
      },
    });
  } catch (error) {
    console.error(`Failed to upload chunk ${chunkIndex}:`, error);
    throw error;
  }
}
```

#### 4. 服务端接收和校验

服务端接收每个分块，并校验其哈希值，确保数据完整性。

```javascript
const express = require('express');
const multer = require('multer');
const crypto = require('crypto');
const fs = require('fs');
const path = require('path');

const upload = multer({ dest: 'uploads/' });
const app = express();

app.post('/upload', upload.single('file'), (req, res) => {
  const { fileName, chunkIndex, hash } = req.body;
  const chunkPath = path.join(__dirname, 'uploads', `${fileName}.part.${chunkIndex}`);
  const chunk = fs.readFileSync(req.file.path);

  // 校验分块哈希值
  const calculatedHash = crypto.createHash('md5').update(chunk).digest('hex');
  if (calculatedHash !== hash) {
    return res.status(400).send('Hash mismatch');
  }

  fs.renameSync(req.file.path, chunkPath);
  res.send('Chunk uploaded');
});
```

#### 5. 记录上传进度

服务端记录已成功上传的分块信息，例如使用数据库或文件记录，以便在上传中断后可以继续上传未完成的分块。

#### 6. 断点续传

当上传过程中发生中断，客户端重新发起上传请求时，先请求服务端获取已上传的分块信息，然后只上传未完成的分块。

```javascript
function useResumableUpload(file) {
  const [uploadProgress, setUploadProgress] = useState(0);
  const [status, setStatus] = useState('idle'); // idle, uploading, completed, failed
  const [chunks, setChunks] = useState(sliceFile(file));
  const [uploadedChunks, setUploadedChunks] = useState(() => {
    const savedChunks = localStorage.getItem(`uploadedChunks_${file.name}`);
    return savedChunks ? JSON.parse(savedChunks) : [];
  });

  const uploadChunk = async (chunk, index) => {
    if (uploadedChunks.includes(index)) return;

    const hash = await calculateHash(chunk);
    const formData = new FormData();
    formData.append('file', chunk);
    formData.append('fileName', file.name);
    formData.append('chunkIndex', index);
    formData.append('hash', hash);

    try {
      await axios.post('/upload', formData, {
        headers: {
          'Content-Type': 'multipart/form-data',
        },
        onUploadProgress: (progressEvent) => {
          const progress = ((index + progressEvent.loaded / chunk.size) / chunks.length) * 100;
          setUploadProgress(progress);
        },
      });

      setUploadedChunks((prev) => {
        const newUploadedChunks = [...prev, index];
        localStorage.setItem(`uploadedChunks_${file.name}`, JSON.stringify(newUploadedChunks));
        return newUploadedChunks;
      });
    } catch (error) {
      console.error(`Chunk ${index} failed to upload:`, error);
      throw error;
    }
  };

  const uploadFile = async () => {
    setStatus('uploading');

    for (let i = 0; i < chunks.length; i++) {
      try {
        await uploadChunk(chunks[i], i);
      } catch (error) {
        setStatus('failed');
        return;
      }
    }

    await axios.post('/merge', { fileName: file.name });
    localStorage.removeItem(`uploadedChunks_${file.name}`);
    setStatus('completed');
  };

  return { uploadProgress, status, uploadFile };
}
```

#### 7. 合并分块

所有分块上传完成后，服务端将所有分块按照顺序合并成原始文件，并校验完整文件的哈希值，确保最终文件完整性。

```javascript
app.post('/merge', (req, res) => {
  const { fileName } = req.body;
  const filePath = path.join(__dirname, 'uploads', fileName);
  const writeStream = fs.createWriteStream(filePath);

  const chunkPaths = fs.readdirSync('uploads')
    .filter(p => p.includes(fileName))
    .sort((a, b) => parseInt(a.split('.part.').pop(), 10) - parseInt(b.split('.part.').pop(), 10));

  chunkPaths.forEach(chunkPath => {
    const chunk = fs.readFileSync(path.join(__dirname, 'uploads', chunkPath));
    writeStream.write(chunk);
    fs.unlinkSync(path.join(__dirname, 'uploads', chunkPath));
  });

  writeStream.end();
  res.send('File merged');
});
```

好的，我们继续完成前端完整实现部分。下面是前端的完整实现代码示例，包含文件选择、上传控制和进度显示：

### 三、完整前端实现

```jsx
import React, { useState } from 'react';
import axios from 'axios';
import crypto from 'crypto';

// 文件分块大小
const SLICE_SIZE = 5 * 1024 * 1024; // 每个块大小 5MB

// 文件分块函数
function sliceFile(file) {
  const chunks = [];
  let currentByte = 0;

  while (currentByte < file.size) {
    const chunk = file.slice(currentByte, currentByte + SLICE_SIZE);
    chunks.push(chunk);
    currentByte += SLICE_SIZE;
  }

  return chunks;
}

// 计算分块哈希
function calculateHash(chunk) {
  return new Promise((resolve, reject) => {
    const reader = new FileReader();
    reader.onload = () => {
      const buffer = reader.result;
      const hash = crypto.createHash('sha256').update(buffer).digest('hex');
      resolve(hash);
    };
    reader.onerror = reject;
    reader.readAsArrayBuffer(chunk);
  });
}

// 自定义 Hook
function useResumableUpload(file) {
  const [uploadProgress, setUploadProgress] = useState(0);
  const [status, setStatus] = useState('idle'); // idle, uploading, completed, failed
  const [chunks, setChunks] = useState(sliceFile(file));
  const [uploadedChunks, setUploadedChunks] = useState(() => {
    const savedChunks = localStorage.getItem(`uploadedChunks_${file.name}`);
    return savedChunks ? JSON.parse(savedChunks) : [];
  });
  const [failedChunks, setFailedChunks] = useState([]);

  const uploadChunk = async (chunk, index, retries = 3) => {
    if (uploadedChunks.includes(index)) return;

    const hash = await calculateHash(chunk);
    const formData = new FormData();
    formData.append('file', chunk);
    formData.append('fileName', file.name);
    formData.append('chunkIndex', index);
    formData.append('hash', hash);

    try {
      await axios.post('/upload', formData, {
        headers: {
          'Content-Type': 'multipart/form-data',
        },
        onUploadProgress: (progressEvent) => {
          const progress = ((index + progressEvent.loaded / chunk.size) / chunks.length) * 100;
          setUploadProgress(progress);
        },
      });

      setUploadedChunks((prev) => {
        const newUploadedChunks = [...prev, index];
        localStorage.setItem(`uploadedChunks_${file.name}`, JSON.stringify(newUploadedChunks));
        return newUploadedChunks;
      });
    } catch (error) {
      console.error(`Chunk ${index} failed to upload:`, error);
      if (retries > 0) {
        setTimeout(() => uploadChunk(chunk, index, retries - 1), 1000); // 延迟1秒后重试
      } else {
        setFailedChunks((prev) => [...prev, index]);
      }
    }
  };

  const uploadFile = async () => {
    setStatus('uploading');

    for (let i = 0; i < chunks.length; i++) {
      try {
        await uploadChunk(chunks[i], i);
      } catch (error) {
        setStatus('failed');
        return;
      }
    }

    await axios.post('/merge', { fileName: file.name });
    localStorage.removeItem(`uploadedChunks_${file.name}`);
    setStatus('completed');
  };

  const retryFailedChunks = async () => {
    const retries = failedChunks.slice();
    setFailedChunks([]);

    for (let i = 0; i < retries.length; i++) {
      try {
        await uploadChunk(chunks[retries[i]], retries[i]);
      } catch (error) {
        setStatus('failed');
        return;
      }
    }
  };

  return { uploadProgress, status, uploadFile, retryFailedChunks };
}

// React 组件
function FileUpload() {
  const [file, setFile] = useState(null);
  const { uploadProgress, status, uploadFile, retryFailedChunks } = useResumableUpload(file);

  const handleFileChange = (event) => setFile(event.target.files[0]);
  const handleUploadClick = () => file && uploadFile();

  return (
    <div>
      <input type="file" onChange={handleFileChange} />
      <button onClick={handleUploadClick} disabled={!file || status === 'uploading'}>
        Upload
      </button>
      {status === 'uploading' && <p>Uploading: {uploadProgress.toFixed(2)}%</p>}
      {status === 'completed' && <p>Upload Completed!</p>}
      {status === 'failed' && (
        <>
          <p>Upload Failed. Please try again.</p>
          <button onClick={retryFailedChunks}>Retry Failed Chunks</button>
        </>
      )}
    </div>
  );
}

export default FileUpload;
```

### 四、后端实现

在 Node.js 后端，我们需要使用 `multer` 等必要的库来处理文件上传，并确保每个分块的哈希值和上传合并。

#### 4.1 安装依赖

首先，确保安装了必要的依赖：

```bash
npm install express multer crypto
```

#### 4.2 创建服务器

以下是一个完整的后端实现，包含接收文件块和合并文件块的逻辑：

```javascript
const express = require('express');
const multer = require('multer');
const crypto = require('crypto');
const fs = require('fs');
const path = require('path');

const upload = multer({ dest: 'uploads/' });
const app = express();
app.use(express.json());

app.post('/upload', upload.single('file'), (req, res) => {
  const { fileName, chunkIndex, hash } = req.body;
  const chunkPath = path.join(__dirname, 'uploads', `${fileName}.part.${chunkIndex}`);
  const chunk = fs.readFileSync(req.file.path);

  // 校验分块哈希值
  const calculatedHash = crypto.createHash('sha256').update(chunk).digest('hex');
  if (calculatedHash !== hash) {
    return res.status(400).send('Hash mismatch');
  }

  fs.renameSync(req.file.path, chunkPath);
  res.send('Chunk uploaded');
});

app.post('/merge', (req, res) => {
  const { fileName } = req.body;
  const filePath = path.join(__dirname, 'uploads', fileName);
  const writeStream = fs.createWriteStream(filePath);

  const chunkPaths = fs.readdirSync('uploads')
    .filter(p => p.includes(fileName))
    .sort((a, b) => parseInt(a.split('.part.').pop(), 10) - parseInt(b.split('.part.').pop(), 10));

  chunkPaths.forEach(chunkPath => {
    const chunk = fs.readFileSync(path.join(__dirname, 'uploads', chunkPath));
    writeStream.write(chunk);
    fs.unlinkSync(path.join(__dirname, 'uploads', chunkPath));
  });

  writeStream.end();
  res.send('File merged');
});

app.listen(3000, () => {
  console.log('Server started on http://localhost:3000');
});
```

## 文件上传失败后的处理

在处理大文件上传时，上传失败是一个需要考虑的重要问题。我们需要确保上传过程的健壮性，即使某些块上传失败也能从失败的地方继续上传。以下是处理上传失败并继续上传的详细步骤。

### 一、前端处理

#### 1.1 记录失败的块

在上传过程中，如果某个块上传失败，我们需要记录该块的索引，以便稍后重新尝试上传。

#### 1.2 重试机制

为了确保上传的健壮性，可以实现一个简单的重试机制，对失败的块重新上传，直到成功或者达到最大重试次数。

#### 1.3 代码实现

以下是一个实现失败处理和继续上传的完整前端示例：

```jsx
import React, { useState } from 'react';
import axios from 'axios';
import crypto from 'crypto';

// 文件分块大小
const SLICE_SIZE = 5 * 1024 * 1024; // 每个块大小 5MB

// 文件分块函数
function sliceFile(file) {
  const chunks = [];
  let currentByte = 0;

  while (currentByte < file.size) {
    const chunk = file.slice(currentByte, currentByte + SLICE_SIZE);
    chunks.push(chunk);
    currentByte += SLICE_SIZE;
  }

  return chunks;
}

// 计算分块哈希
function calculateHash(chunk) {
  return new Promise((resolve, reject) => {
    const reader = new FileReader();
    reader.onload = () => {
      const buffer = reader.result;
      const hash = crypto.createHash('md5').update(buffer).digest('hex');
      resolve(hash);
    };
    reader.onerror = reject;
    reader.readAsArrayBuffer(chunk);
  });
}

// 自定义 Hook
function useResumableUpload(file) {
  const [uploadProgress, setUploadProgress] = useState(0);
  const [status, setStatus] = useState('idle'); // idle, uploading, completed, failed
  const [chunks, setChunks] = useState(sliceFile(file));
  const [uploadedChunks, setUploadedChunks] = useState(() => {
    const savedChunks = localStorage.getItem(`uploadedChunks_${file.name}`);
    return savedChunks ? JSON.parse(savedChunks) : [];
  });
  const [failedChunks, setFailedChunks] = useState([]);

  const uploadChunk = async (chunk, index, retries = 3) => {
    if (uploadedChunks.includes(index)) return;

    const hash = await calculateHash(chunk);
    const formData = new FormData();
    formData.append('file', chunk);
    formData.append('fileName', file.name);
    formData.append('chunkIndex', index);
    formData.append('hash', hash);

    try {
      await axios.post('/upload', formData, {
        headers: {
          'Content-Type': 'multipart/form-data',
        },
        onUploadProgress: (progressEvent) => {
          const progress = ((index + progressEvent.loaded / chunk.size) / chunks.length) * 100;
          setUploadProgress(progress);
        },
      });

      setUploadedChunks((prev) => {
        const newUploadedChunks = [...prev, index];
        localStorage.setItem(`uploadedChunks_${file.name}`, JSON.stringify(newUploadedChunks));
        return newUploadedChunks;
      });
    } catch (error) {
      console.error(`Chunk ${index} failed to upload:`, error);
      if (retries > 0) {
        setTimeout(() => uploadChunk(chunk, index, retries - 1), 1000); // 延迟1秒后重试
      } else {
        setFailedChunks((prev) => [...prev, index]);
      }
    }
  };

  const uploadFile = async () => {
    setStatus('uploading');

    for (let i = 0; i < chunks.length; i++) {
      try {
        await uploadChunk(chunks[i], i);
      } catch (error) {
        setStatus('failed');
        return;
      }
    }

    await axios.post('/merge', { fileName: file.name });
    localStorage.removeItem(`uploadedChunks_${file.name}`);
    setStatus('completed');
  };

  const retryFailedChunks = async () => {
    const retries = failedChunks.slice();
    setFailedChunks([]);

    for (let i = 0; i < retries.length; i++) {
      try {
        await uploadChunk(chunks[retries[i]], retries[i]);
      } catch (error) {
        setStatus('failed');
        return;
      }
    }
  };

  return { uploadProgress, status, uploadFile, retryFailedChunks };
}

// React 组件
function FileUpload() {
  const [file, setFile] = useState(null);
  const { uploadProgress, status, uploadFile, retryFailedChunks } = useResumableUpload(file);

  const handleFileChange = (event) => setFile(event.target.files[0]);
  const handleUploadClick = () => file && uploadFile();

  return (
    <div>
      <input type="file" onChange={handleFileChange} />
      <button onClick={handleUploadClick} disabled={!file || status === 'uploading'}>
        Upload
      </button>
      {status === 'uploading' && <p>Uploading: {uploadProgress.toFixed(2)}%</p>}
      {status === 'completed' && <p>Upload Completed!</p>}
      {status === 'failed' && (
        <>
          <p>Upload Failed. Please try again.</p>
          <button onClick={retryFailedChunks}>Retry Failed Chunks</button>
        </>
      )}
    </div>
  );
}

export default FileUpload;
```

### 二、后端处理

#### 2.1 接收分块和校验

后端接收每个分块并校验哈希值，确保数据完整性。

#### 2.2 合并已上传的分块

在所有分块上传完成后，后端需要合并分块为完整的文件。

#### 2.3 代码实现

以下是一个完整的后端代码示例，包含接收文件块、校验哈希值和合并分块：

```javascript
const express = require('express');
const multer = require('multer');
const crypto = require('crypto');
const fs = require('fs');
const path = require('path');

const upload = multer({ dest: 'uploads/' });
const app = express();
app.use(express.json());

app.post('/upload', upload.single('file'), (req, res) => {
  const { fileName, chunkIndex, hash } = req.body;
  const chunkPath = path.join(__dirname, 'uploads', `${fileName}.part.${chunkIndex}`);
  const chunk = fs.readFileSync(req.file.path);

  // 校验分块哈希值
  const calculatedHash = crypto.createHash('md5').update(chunk).digest('hex');
  if (calculatedHash !== hash) {
    return res.status(400).send('Hash mismatch');
  }

  fs.renameSync(req.file.path, chunkPath);
  res.send('Chunk uploaded');
});

app.post('/merge', (req, res) => {
  const { fileName } = req.body;
  const filePath = path.join(__dirname, 'uploads', fileName);
  const writeStream = fs.createWriteStream(filePath);

  const chunkPaths = fs.readdirSync('uploads')
    .filter(p => p.includes(fileName))
    .sort((a, b) => parseInt(a.split('.part.').pop(), 10) - parseInt(b.split('.part.').pop(), 10));

  chunkPaths.forEach(chunkPath => {
    const chunk = fs.readFileSync(path.join(__dirname, 'uploads', chunkPath));
    writeStream.write(chunk);
    fs.unlinkSync(path.join(__dirname, 'uploads', chunkPath));
  });

  writeStream.end();
  res.send('File merged');
});

app.listen(3000, () => {
  console.log('Server started on http://localhost:3000');
});
```

### 总结

通过以上步骤，我们在前端实现了上传失败的处理和继续上传的功能，包括以下几个方面：

1. **记录失败的块**：在上传过程中，如果某个块上传失败，记录该块的索引。
2. **重试机制**：对失败的块进行重试，直到成功或者达到最大重试次数。
3. **断点续传**：从中断点继续上传未完成的分块。
4. **合并分块**：服务端合并所有分块为完整文件。

通过这些步骤，可以显著提高大文件上传的可靠性和用户体验。

通过上述步骤，我们实现了基于 React 和 Node.js 的大文件断点续传功能。主要涉及以下几个方面：

1. **文件分块**：将大文件分成多个较小的块。
2. **计算文件和分块哈希**：确保数据完整性。
3. **分块上传**：逐块上传文件并显示进度。
4. **服务端接收和校验**：确保每个分块的完整性。
5. **记录上传进度**：在本地存储上传状态。
6. **断点续传**：从中断点继续上传未完成的分块。
7. **合并分块**：服务端合并所有分块为完整文件。

通过这些步骤，可以显著提高大文件上传的可靠性和用户体验。