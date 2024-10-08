# 虚拟 DOM

React 的虚拟 DOM（Virtual DOM）架构是 React 框架的核心机制之一，用于提高用户界面的渲染性能和开发者体验。

## 一、概念

虚拟 DOM 是一个轻量级的 JavaScript 对象，它是 React 用来描述真实 DOM 结构的表示。虚拟 DOM 不是真实的 DOM，但它可以通过 diff 算法快速比较和更新真实 DOM。

## 二、虚拟 DOM 的架构

虚拟 DOM 的架构主要包括**React 元素**（React Elements）、**虚拟 DOM 树**（Virtual DOM Tree）、**Diffing 算法**（Diffing Algorithm）和**Reconciliation 过程**（Reconciliation Process）四个关键部分。

### 2.1 React 元素

React 元素是构建用户界面的基础组件，它是不可变的对象，用于描述界面的一部分。React 元素可以是 HTML 标签（如 `<div>`）或者自定义的 React 组件。

```javascript
const element = <h1>Hello, world!</h1>;
```

### 2.2 虚拟 DOM 树

React 元素会被转换成一个虚拟 DOM 树。虚拟 DOM 树是由 React 元素组成的树形结构，表示用户界面的当前状态。虚拟 DOM 树是一个 JavaScript 对象，每个节点包含类型、属性和子节点等信息。每次状态或属性变化时，React 会创建一个新的虚拟 DOM 树，并将其与之前的虚拟 DOM 树进行比较。

虚拟 DOM 的使用避免了直接操作真实 DOM 的高昂代价。通过在内存中进行虚拟 DOM 的比较和计算，React 能够找出最小的变化集，然后将这些变化应用到真实 DOM 中，从而提升性能。

```javascript
const virtualDOM = {
  type: 'div',
  props: {
    children: [
      {
        type: 'p',
        props: {
          children: 'Current count: 0',
        },
      },
      {
        type: 'button',
        props: {
          onClick: increment,
          children: 'Increment',
        },
      },
    ],
  },
};
```

### 2.3 Diff 算法

当界面状态发生变化时，React 使用 Diffing 算法来比较新旧虚拟 DOM 树，并计算出需要更新的部分。Diffing 算法主要依赖于以下几点：

1. **虚拟 DOM**：虚拟 DOM 是 React 的一个抽象层，它是一个轻量级的 JavaScript 对象，表示真实 DOM 的结构。每次状态或属性变化时，React 会创建一个新的虚拟 DOM 树，并将其与之前的虚拟 DOM 树进行比较。
虚拟 DOM 的使用避免了直接操作真实 DOM 的高昂代价。通过在内存中进行虚拟 DOM 的比较和计算，React 能够找出最小的变化集，然后将这些变化应用到真实 DOM 中，从而提升性能。
2. **树的分层比较**：React 的 Diff 算法基于树的分层比较，即只比较同一层级的节点，不会跨层级比较。这样可以大幅减少比较的复杂度。
分层比较使得算法的复杂度从 O(n^3) 降低到 O(n)，其中 n 是节点数。这个优化显著提升了性能，特别是在大型应用中。
3. **同类型节点比较**：当 React 比较两个同类型的节点时，会递归比较它们的属性和子节点。如果节点类型不同，React 会直接销毁旧节点并创建新节点，而不会进行进一步比较。
通过同类型节点的递归比较，React 能够高效地找出需要更新的部分，避免了不必要的节点销毁和创建。
4. **使用唯一 `key` 标识子节点**：在列表中，React 通过 key 属性来唯一标识每个子节点，从而高效地比较和更新列表。key 应该是稳定的、唯一的且可预测的。
使用 key 属性可以显著提升列表更新的性能。React 能够通过 key 快速找到对应的节点，进行最小量的 DOM 操作，而不是重新创建整个列表。
5. **最小化 DOM 操作**：React 的 Diff 算法通过最小化实际 DOM 操作来提升性能。它会尽可能复用现有的 DOM 节点，而不是销毁和重新创建节点。
最小化 DOM 操作减少了浏览器的重绘和重排，从而提升了性能。每次更新只会修改需要改变的部分，避免了不必要的开销。
6. **批量更新**：React 会将多次状态更新合并为一次批量更新，从而减少重绘和重排的次数，提升性能。
批量更新避免了频繁的 DOM 操作和状态更新，使得应用更加流畅和高效。
7. **异步渲染**：在 React 16 及以上版本中，引入了异步渲染（Concurrent Mode），使得 React 可以将渲染任务分割为多个小任务，并在空闲时段执行，从而提升用户体验。
异步渲染使得 React 可以在处理高优先级任务（如用户输入）时暂停低优先级任务，从而提升应用的响应速度和用户体验。


Diff 算法的核心原则主要是以下三点：

1. **同级比较**：只比较同一层级的节点，不跨层级比较。
2. **唯一标识符**：通过 `key` 属性标识节点，确保唯一性，以提高比较效率。
3. **递归比较**：对子节点进行递归比较，深度优先遍历。

假设有两棵 虚拟 DOM 树：

```javascript
const oldTree = {
  type: 'div',
  props: {
    id: 'container',
    children: [
      { type: 'p', props: { children: 'Hello, World!' } },
      { type: 'button', props: { onClick: increment, children: 'Click me' } },
    ],
  },
};

const newTree = {
  type: 'div',
  props: {
    id: 'container',
    children: [
      { type: 'p', props: { children: 'Hello, React!' } },
      { type: 'button', props: { onClick: increment, children: 'Click me' } },
    ],
  },
};
```

React 的 diff 算法将会：

1. 检查 `div` 节点，类型相同。
2. 比较 `div` 的属性，没有变化。
3. 递归比较 `div` 的子节点：
   - `p` 节点类型相同，比较其属性，发现 `children` 属性变化。
   - `button` 节点类型相同，比较其属性，没有变化。

Diff 算法的步骤：

1. **节点类型不同**：如果节点类型不同，则直接替换整个节点及其子节点。
2. **节点类型相同**：
   - 比较属性：生成属性的差异列表。
   - 比较子节点：递归进行子节点比较。

### 2.4 Reconciliation 过程

Reconciliation 是 React 更新真实 DOM 的过程，当虚拟 DOM 发生变化时，React 会计算出最小的更新操作，并应用到真实 DOM 上。具体步骤如下：

1. **创建新虚拟 DOM 树**：当状态或属性变化时，React 会创建新的虚拟 DOM 树。
2. **比较新旧虚拟 DOM 树**：React 使用 Diffing 算法比较新旧虚拟 DOM 树，找出变化的部分。
3. **更新真实 DOM**：根据比较结果，React 只更新发生变化的部分，避免了不必要的重绘和重排，提高了性能。

## 三、虚拟 DOM 的优点

1. **性能优化**：通过最小化直接的 DOM 操作，React 的虚拟 DOM 能显著提高渲染性能，尤其是在复杂和大型应用中。
2. **可预测性**：由于虚拟 DOM 是不可变的对象，每次状态或属性变化都会生成新的虚拟 DOM 树，这使得界面的状态更易于预测。
3. **跨平台**：虚拟 DOM 的概念不仅限于浏览器环境，React Native 就是基于虚拟 DOM 的跨平台解决方案。

## 四、虚拟 DOM 的性能

### 4.1 虚拟 DOM 如何提高性能

#### （1）避免直接操作真实 DOM

直接操作真实 DOM 是一个昂贵的操作，因为它们会触发浏览器的重排（reflow）和重绘（repaint）。每次浏览器需要更新 DOM 时，都需要重新计算布局和样式，并进行页面的重绘。这些操作的代价非常高，尤其在频繁更新的情况下。

虚拟 DOM 通过在内存中维护一个轻量级的 DOM 树副本，可以避免频繁的直接操作真实 DOM。它将多次更新合并为一次操作，从而减少了重排和重绘的次数。

#### （2）最小化 DOM 更新

虚拟 DOM 提供了一种高效的 diff 算法，可以比较新旧两棵虚拟 DOM 树的差异，并生成一个最小的更新操作列表。这些更新操作只应用于需要变更的部分，而不会重新渲染整个 DOM 树。

#### （3）批量更新

React 的批量更新机制可以将多次状态更新合并为一次更新操作，从而减少不必要的重新渲染。在 React 18 中，引入了自动批处理（Automatic Batching），进一步优化了这一过程。

#### （4）Fiber 架构

React 16 引入的 Fiber 架构支持时间切片（time-slicing）和优先级调度（priority scheduling），可以将渲染任务分解为多个小任务，并分散到多个帧中执行，从而避免主线程阻塞。这种机制特别适用于处理复杂的 UI 更新和高频率的用户交互。

### 4.2 虚拟 DOM 一定会提高性能吗？

虽然 虚拟 DOM 提供了一种高效的更新机制，但它并不适用于所有场景。在某些情况下，Virtual DOM 可能不会显著提高性能，甚至可能引入额外的开销。

#### （1）简单的静态页面

对于简单的静态页面或更新频率较低的页面，直接操作真实 DOM 的开销相对较小，Virtual DOM 带来的性能提升可能微乎其微。在这种情况下，使用 虚拟 DOM 反而可能引入额外的计算开销。

#### （2）大量复杂计算

虚拟 DOM 需要在内存中维护一棵 DOM 树，并进行 diff 运算。如果页面包含大量复杂的计算操作，Virtual DOM 的 diff 运算可能会引入额外的性能开销。在这种情况下，需要仔细评估 虚拟 DOM 的优劣。

#### （3）高频率更新

在高频率更新的场景下，例如频繁的动画更新或大量的实时数据更新，Virtual DOM 的 diff 运算可能成为瓶颈。在这种情况下，需要优化更新策略，或者结合其他技术（如 Web Workers）来分担计算任务。

#### （4）特定环境限制

在某些特定环境下，例如嵌入式设备或低性能设备，内存和处理能力有限，虚拟 DOM 的额外开销可能显著影响性能。在这些情况下，直接操作真实 DOM 可能会更加高效。
