# Fiber

## 一、概念

React Fiber 是 React 16 引入的一种新的协调引擎和渲染架构。Fiber 架构旨在提高 React 的性能和灵活性。

Fiber 将渲染工作分解为多个小任务，以便在浏览器的多个帧中分批执行任务。这样可以防止长时间的单一渲染任务阻塞主线程，从而保持流畅的用户体验。

在 Fiber 架构之前，React 使用的是同步渲染模型，一旦开始渲染，就会一直进行下去，直到完成。这种方式在处理大量 DOM 更新或复杂的组件树时，会导致浏览器卡顿或掉帧。Fiber 通过将渲染工作分解并调度到多个帧中，解决了这一问题。

Fiber 架构具有如下优点。

1. **更好的用户体验**：通过时间切片和优先级调度，Fiber 可以在保证高优先级任务（如用户交互）快速响应的同时，处理低优先级任务（如数据加载），从而提高用户体验。
2. **更灵活的更新机制**：Fiber 允许对不同类型的更新进行灵活调度，可以更好地适应复杂和高频率的更新场景。
3. **可扩展性**：Fiber 架构为未来的改进和新特性（如并发模式和 Suspense）提供了基础，使得 React 可以更灵活地扩展和优化。

## 二、Fiber 的核心理念

### 2.1 时间切片（Time Slicing）

Fiber 允许将渲染工作分解为多个小任务，可以在多个帧之间进行调度，从而避免主线程长时间阻塞。

在 React Fiber 之前，React 的协调过程是同步的，即一旦开始渲染更新，React 会一直运行到完成，期间不会被打断。这种方式在处理大型更新时可能会导致主线程被阻塞，导致用户界面卡顿。
React Fiber 引入了可中断的渲染机制。它将渲染任务分割成多个小任务，允许在任务之间进行中断和恢复。这意味着 React 可以在处理长时间运行的渲染任务时，暂停当前任务，处理更高优先级的任务（如用户输入事件），然后继续之前的任务。

时间切片的实现依赖于浏览器提供的 `requestIdleCallback` 和 `requestAnimationFrame` 等 API，通过这些 API，React 可以在浏览器空闲时执行渲染任务。

```javascript
function workLoop(deadline) {
  while (deadline.timeRemaining() > 0 && work.length > 0) {
    performUnitOfWork(work.shift());
  }
  
  if (work.length > 0) {
    requestIdleCallback(workLoop);
  }
}

function performUnitOfWork(unit) {
  // 执行渲染任务
}

let work = [/* 渲染任务列表 */];

requestIdleCallback(workLoop);
```

上面代码中，`workLoop` 函数会在浏览器空闲时执行 `performUnitOfWork` 函数，从而逐步完成渲染任务。

### 2.2 优先级调度（Priority Scheduling）

传统的 React，所有更新都是同步的，没有优先级区分。无论是高优先级的用户交互还是低优先级的数据更新，都会按照相同的顺序处理。

Fiber 为不同类型的更新分配不同的优先级。例如，用户输入事件的更新具有较高优先级，而数据加载的更新可能具有较低优先级。这样可以确保高优先级的任务能够尽快完成，提高用户体验。React 使用一个优先级队列来管理不同类型的更新任务。每个任务根据其类型和紧急程度分配一个优先级。调度器会优先执行高优先级的任务，低优先级的任务会被延后执行。

React 使用一个基于时间的优先级模型，每个任务根据其优先级分配一个到期时间（expiration time）。调度器会根据任务的到期时间和当前时间来决定下一个要执行的任务。

#### （1）基于优先级的更新模型

React 将更新任务分为多个优先级级别，每个级别对应不同的场景和用户交互需求。常见的优先级级别包括：

- **Immediate**：即时优先级，通常用于立即生效的任务。
- **User-blocking**：用户阻塞优先级，通常用于用户输入事件等需要快速响应的任务。
- **Normal**：正常优先级，适用于大多数更新任务。
- **Low**：低优先级，适用于不需要立即处理的任务，如数据加载。
- **Idle**：空闲优先级，适用于后台任务或不紧急的任务。

#### （2）调度器的工作机制

React 的调度器会根据任务的优先级和到期时间来管理任务队列。调度器会优先处理高优先级任务，确保它们尽快得到执行。每个任务在创建时会分配一个到期时间（expiration time），表示任务需要在何时之前完成。优先级越高的任务，到期时间越短。调度器会根据任务的到期时间对任务队列进行排序，并优先执行即将到期的任务。这样可以确保高优先级任务能够尽快完成。

```javascript
class Task {
  constructor(callback, priorityLevel) {
    this.callback = callback;
    this.priorityLevel = priorityLevel;
    this.expirationTime = calculateExpiration(priorityLevel);
  }
}

const IMMEDIATE_PRIORITY = 1;
const USER_BLOCKING_PRIORITY = 2;
const NORMAL_PRIORITY = 3;
const LOW_PRIORITY = 4;
const IDLE_PRIORITY = 5;

const taskQueue = [];

function scheduleTask(callback, priorityLevel) {
  const task = new Task(callback, priorityLevel);
  taskQueue.push(task);
  taskQueue.sort((a, b) => a.expirationTime - b.expirationTime);
}

function performNextTask() {
  const currentTask = taskQueue.shift();
  if (currentTask) {
    currentTask.callback();
  }
}

function calculateExpiration(priorityLevel) {
  switch (priorityLevel) {
    case IMMEDIATE_PRIORITY:
      return Date.now() + 50; // 即时优先级，50ms 内到期
    case USER_BLOCKING_PRIORITY:
      return Date.now() + 100; // 用户阻塞优先级，100ms 内到期
    case NORMAL_PRIORITY:
      return Date.now() + 500; // 正常优先级，500ms 内到期
    case LOW_PRIORITY:
      return Date.now() + 1000; // 低优先级，1000ms 内到期
    case IDLE_PRIORITY:
      return Date.now() + 5000; // 空闲优先级，5000ms 内到期
    default:
      return Date.now() + 1000;
  }
}

// 示例使用
scheduleTask(() => console.log('Immediate priority task'), IMMEDIATE_PRIORITY);
scheduleTask(() => console.log('User blocking priority task'), USER_BLOCKING_PRIORITY);
scheduleTask(() => console.log('Normal priority task'), NORMAL_PRIORITY);
scheduleTask(() => console.log('Low priority task'), LOW_PRIORITY);
scheduleTask(() => console.log('Idle priority task'), IDLE_PRIORITY);

setInterval(performNextTask, 50);
```

上面代码中，`scheduleTask` 函数会将任务添加到任务队列，并按到期时间排序。`performNextTask` 函数会执行队列中的下一个任务，高优先级任务会优先执行。

### 2.3 并发模式（Concurrent Mode）

并发模式是 React 未来的一个重要特性，它允许 React 在后台准备多个 UI 版本，并根据需要进行切换。并发模式依赖于 Fiber 架构的时间切片和优先级调度。

## 三、Fiber 架构的基本概念

### 3.1 Fiber 节点

Fiber 节点是一个 JavaScript 对象，表示组件树中的一个单元。每个 Fiber 节点保存了与其对应组件实例相关的所有信息，例如状态、属性、DOM 引用等。Fiber 节点通过链接形成树结构，类似于虚拟 DOM 树。

```javascript
const fiber = {
  type: Component,       // 对应的组件类型
  key: key,              // 元素的唯一标识
  stateNode: instance,   // 组件的实例
  child: childFiber,     // 子节点
  sibling: siblingFiber, // 兄弟节点
  return: parentFiber,   // 父节点
  // 其他属性
};
```

### 3.2 Reconciliation（协调）

Reconciliation 是 React 的核心流程之一，负责找出需要更新的部分并最小化对真实 DOM 的操作。在 Fiber 架构中，Reconciliation 流程被分为两个阶段：

- **Render 阶段**：构建新的 Fiber 树并标记需要更新的 Fiber 节点。
- **Commit 阶段**：将变化应用到真实 DOM 上。

### 3.3 Work Loop（工作循环）

在 Fiber 架构中，React 会在每一帧中执行一部分渲染工作，称为工作单元（work unit）。React 使用调度器（Scheduler）来管理这些工作单元并确保高优先级任务先执行。

## 四、Fiber 的工作流程

Fiber 的工作流程分为三个阶段：渲染阶段、提交阶段和完成阶段。

### 4.1 渲染阶段（Render Phase）

在渲染阶段，React 会根据当前状态和属性，生成新的 Fiber 树。这一阶段是可以中断和恢复的，以便处理高优先级的任务。

### 4.2 提交阶段（Commit Phase）

在 Commit 阶段，React 会一次性将所有变化应用到真实 DOM 上。这个阶段是不可中断的，以确保 DOM 状态的一致性。

### 4.3 完成阶段（Complete Phase）

在完成阶段，React 会清理和释放不再需要的资源，如旧的 Fiber 树等。
