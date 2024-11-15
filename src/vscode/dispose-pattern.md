# 使用 Dispose Pattern 管理资源

[Dispose Pattern](https://en.wikipedia.org/wiki/Dispose_pattern) 是一种用于资源管理的设计模式, 让对象持有资源, 并通过调用方法 (这里是 `dispose()`) 来释放资源. 代码位于 `src/vs/base/common/lifecycle.ts` 中.

```ts
export interface IDisposable {
  dispose(): void;
}
```

## 其他

### 异步地初始化和销毁资源

JavaScript 不支持异步的初始化函数 (constructor). 你可能会发现, 有些资源的可用时间晚于初始化, `constructor()` 已经被调用, 但对象的功能还几乎处于不可用状态. 一个惯用法是, 让对象暴露一个名为 `ready` 的 Promise, 允许资源的使用者 (可能是类内的其他方法, 或者外部的函数) 掌握资源初始化完成的时机.

`dispose()` 也不是异步的, 但这一点往往并不重要. 即使资源释放过程是异步的, 我们通常也不需要等待资源释放完成.

下面的类给出了一个完整的样板示例:

```ts
export class AsyncResourceService {
  private ready!: Promise<MyResource>;
  private resource: MyResource | undefined = undefined;

  constructor() {
    this.initialize();
  }

  initialize(): void {
    this.ready = this.doInitialize();
  }

  private async doInitialize(): Promise<MyResource> {
    this.resource = await asyncCreateResource();

    return this.resource;
  }

  async getSomething(): Promise<Something> {
    const resource = await this.ready;

    return resource.doSomething();
  }

  dispose(): void {
    (async () => {
      if (this.resource) {
        await asyncDisposeResource(this.resource);
        this.resource = undefined;
      }
    })();
  }
}
```
