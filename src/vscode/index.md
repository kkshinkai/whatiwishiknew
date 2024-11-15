# 当我阅读 VS Code 源码时, 我希望我知道 ...

## 阅读 VS Code 源码

`IDisposable` 是 VS Code 中非常重要的接口, 是 [Dispose Pattern](https://en.wikipedia.org/wiki/Dispose_pattern) 的实现, 用于资源管理. 它内容简单, 而且相对独立, 因此很适合作为阅读 VS Code 源码的起点.

[VS Code 源码分析: 使用 Dispose Pattern 管理资源](./dispose-pattern.md)

Event 和 Emitter 是 VS Code 中另一个重要的模块, 用于实现事件驱动的编程模型, 在 VS Code 中被广泛使用.

TODO: VS Code 源码分析: Event 和 Emitter

VS Code 使用依赖注入来管理组件.

TODO: VS Code 源码分析: 依赖注入

## 阅读 Monaco 源码

TODO
