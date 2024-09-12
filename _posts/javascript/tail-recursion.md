尾递归（Tail Recursion）是指递归函数在其递归调用后直接返回结果，而不需要对返回值进行再处理。这意味着递归调用是函数执行的最后一步。尾递归优化（Tail Call Optimization, TCO）是指编译器或解释器对尾递归进行优化，使其在调用自身时不会增加调用栈的深度，从而避免了递归调用带来的栈溢出问题。

### 尾递归的特点
- **递归调用是函数的最后一步**：尾递归的特点是递归调用的结果直接作为函数的返回结果，不需要进行任何额外的计算或处理。
- **尾递归优化（TCO）**：一些现代的 JavaScript 引擎可以对尾递归进行优化，使其不会增加调用栈的深度，从而提高效率，避免栈溢出。

### 普通递归 vs 尾递归
普通递归会在每次调用时保留当前的执行状态，直到递归调用结束，这样会导致调用栈的深度随着递归层数的增加而增加。而尾递归在每次递归调用时，可以直接复用当前的调用栈，从而大大减少栈的深度。

#### 普通递归示例

```javascript
function factorial(n) {
  if (n === 1) {
    return 1;
  }
  return n * factorial(n - 1);
}

console.log(factorial(5)); // 输出: 120
```

#### 尾递归示例

```javascript
function factorial(n, acc = 1) {
  if (n === 1) {
    return acc;
  }
  return factorial(n - 1, n * acc);
}

console.log(factorial(5)); // 输出: 120
```

在上述尾递归示例中，`factorial` 函数的递归调用是函数的最后一步，因此是尾递归。使用尾递归优化可以有效减少调用栈深度。

### 应用场景
尾递归主要用于解决需要大量递归调用的场景，例如：

1. **计算阶乘**：
   上面的示例已经展示了如何使用尾递归来计算阶乘。
   
2. **斐波那契数列**：
   使用尾递归来计算斐波那契数列。

```javascript
function fibonacci(n, a = 0, b = 1) {
  if (n === 0) {
    return a;
  }
  if (n === 1) {
    return b;
  }
  return fibonacci(n - 1, b, a + b);
}

console.log(fibonacci(10)); // 输出: 55
```

3. **树的遍历**：
   遍历树结构时可以使用尾递归来避免栈溢出。

```javascript
function traverse(tree, callback, stack = []) {
  if (!tree) {
    return;
  }
  callback(tree.value);
  if (tree.left) {
    stack.push(tree.left);
  }
  if (tree.right) {
    stack.push(tree.right);
  }
  if (stack.length > 0) {
    traverse(stack.pop(), callback, stack);
  }
}

const tree = {
  value: 1,
  left: {
    value: 2,
    left: { value: 4 },
    right: { value: 5 }
  },
  right: { value: 3 }
};

traverse(tree, console.log);
```

### 注意事项
- **尾递归优化的支持**：并不是所有的 JavaScript 引擎都支持尾递归优化（TCO）。在实际应用中，需要确认目标环境是否支持该优化。
- **可读性**：虽然尾递归可以提高性能和避免栈溢出，但有时会影响代码的可读性。合理使用尾递归，确保代码的可维护性。

### 总结
尾递归是一种特殊的递归形式，递归调用是函数的最后一步。通过合理使用尾递归，可以优化递归调用的性能，减少调用栈深度，避免栈溢出问题。常见的应用场景包括计算阶乘、斐波那契数列和树的遍历等。在实际应用中，需要权衡性能和代码可读性，根据具体需求选择合适的递归方式。