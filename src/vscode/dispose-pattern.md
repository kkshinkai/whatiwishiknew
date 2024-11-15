# 使用 Dispose Pattern 管理资源

[Dispose Pattern](https://en.wikipedia.org/wiki/Dispose_pattern) 是一种用于资源管理的设计模式, 让对象持有资源, 并通过调用方法 (这里是 `dispose()`) 来释放资源. 代码位于 `src/vs/base/common/lifecycle.ts` 中.

```ts
export interface IDisposable {
  dispose(): void;
}
```
