# URI

`encodeURIComponent` 和 `encodeURI` 是 JavaScript 中用于对 URL 进行编码的两个函数。它们的主要作用是通过编码特殊字符，使得字符串可以安全地包含在 URL 中，但它们的应用场景有所不同。

### 1. `encodeURI`

`encodeURI` 用于编码完整的 URI，即整个 URL。它主要用于确保整个 URL 在传输过程中不会被破坏，但它不编码 URL 中的保留字符，这些字符在 URL 中有特殊意义。

`encodeURI` 的编码规则

`encodeURI` 编码所有的非字母数字字符以及一些特殊字符，但保留字符这些字符：`:` `/` `?` `=` `&` `#` `@` `+` `$` `;` `,`。

```javascript
const url = "http://example.com/path name?query=hello world";
const encodedURL = encodeURI(url);
console.log(encodedURL); 
// http://example.com/path%20name?query=hello%20world
```

上面代码中，`encodeURI` 编码了空格字符为 `%20`，但保留了 `:`, `/`, `?` 等字符，因为它们在 URL 中有特殊意义。

### 2. `encodeURIComponent`

`encodeURIComponent` 用于编码 URI 的组成部分（如路径的一部分、查询参数的值等）。它会编码所有的非字母数字字符以及几乎所有的特殊字符，包括在 URL 中有特殊意义的保留字符。这确保了 URI 组件在插入到 URL 中时不会产生歧义。

`encodeURIComponent` 的编码规则

`encodeURIComponent` 编码所有的非字母数字字符以及这些特殊字符：`:` `/` `?` `=` `&` `#` `@` `+` `$` `!` `'` `(` `)` `*` `;` `,`。

```javascript
const query = "name=hello world&value=100%";
const encodedQuery = encodeURIComponent(query);
console.log(encodedQuery); 
// name%3Dhello%20world%26value%3D100%25
```

上面代码中，`encodeURIComponent` 编码了 `=`, `&`, `%` 等字符，确保这些字符在作为查询参数时不会产生歧义。

### `encodeURI` 和 `encodeURIComponent` 的对比

- **用途不同**：`encodeURI` 用于编码整个 URI，而 `encodeURIComponent` 用于编码 URI 的组成部分。
- **编码范围不同**：
  - `encodeURI` 保留了在 URL 中具有特殊意义的保留字符。
  - `encodeURIComponent` 编码几乎所有的非字母数字字符，包括保留字符。

```javascript
const url = "http://example.com/path name?query=hello world";

const encodedURI = encodeURI(url);
const encodedURIComponent = encodeURIComponent(url);

console.log(encodedURI); 
console.log(encodedURIComponent); 

// http://example.com/path%20name?query=hello%20world
// http%3A%2F%2Fexample.com%2Fpath%20name%3Fquery%3Dhello%20world
```

上面代码中，可以看到 `encodeURIComponent` 对整个 URL 进行了全面编码，包括保留字符 `:`, `/`, `?`, `=` 等，而 `encodeURI` 仅编码了不影响 URL 结构的字符。

### 编码与解码

与这两个编码函数对应的解码函数分别是 `decodeURI` 和 `decodeURIComponent`。它们的作用是将编码后的 URI 或 URI 组件还原为原始字符串。

```javascript
const encodedURL = encodeURI("http://example.com/path name?query=hello world");
const decodedURL = decodeURI(encodedURL);
console.log(decodedURL); 
// 输出: "http://example.com/path name?query=hello world"

const encodedComponent = encodeURIComponent("name=hello world&value=100%");
const decodedComponent = decodeURIComponent(encodedComponent);
console.log(decodedComponent); 
// 输出: "name=hello world&value=100%"
```

根据具体使用场景选择合适的编码函数，可以确保 URL 在传输和使用过程中保持正确性和安全性。
