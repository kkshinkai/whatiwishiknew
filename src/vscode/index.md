# 当我阅读 VS Code 源码时, 我希望我知道 ...

## 阅读 VS Code 源码

**IDisposable 是我推荐的阅读起点**. `IDisposable` 是 [Dispose Pattern](https://en.wikipedia.org/wiki/Dispose_pattern) 的接口, 用于资源管理. 这个接口只有一个 `dispose()` 方法, 用于实现资源的销毁逻辑. 它内容简单, 而且相对独立, 同时也是整个 VS Code 代码库中重要的基础设施, 很适合作为阅读 VS Code 源码的入口.

[VS Code 源码分析: 使用 Dispose Pattern 管理资源](./dispose-pattern.md)

**Event 和 Emitter 是 VS Code 中一对重要的基础设施**, 用于实现事件驱动的编程模型, 它们在 VS Code 中被广泛使用. 如果你熟悉 [Qt 的信号槽机制](https://doc.qt.io/qt-6/signalsandslots.html), [RxJS 的 `Observable`](https://rxjs.dev/guide/observable), [Node.js 的 `EventEmitter`](https://nodejs.org/en/learn/asynchronous-work/the-nodejs-event-emitter), 或者 DOM 的事件监听 (`addEventListener`, `removeEventListener`), 你应该会很容易理解它们.

TODO: VS Code 源码分析: Event 和 Emitter

VS Code 使用依赖注入来管理组件.

TODO: VS Code 源码分析: 依赖注入

## 阅读 Monaco 源码

TODO
