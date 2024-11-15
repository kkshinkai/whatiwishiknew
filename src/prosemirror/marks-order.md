# Mark 的顺序

在 Schema 中定义 Mark (行内样式) 时, 你可以决定 Mark 的顺序. 这个顺序在一些特定场景下非常重要, 但很容易被初学者忽略.

```js
const schema = new Schema({
  nodes: { ... },
  marks: {
    highlight: { ... }, // 0
    em: { ... },        // 1
    strong: { ... },    // 2
  }
});
```

## Mark 的顺序决定渲染结果

1. **ProseMirror 行内元素的数据表示**:

    富文本 "_abc **def**_" 包含斜体和加粗两种样式, 在 Markdown 或 HTML 中, 我们经常会使用嵌套结构来表示它, 即 `_abc **def**_`. 虽然 `_abc_ _**def**_` 也能达到同样的视觉效果, 但我们一般不会这样写. 在 ProseMirror 中则恰恰相反, 这段富文本对应的数据结构如下:

    ```json
    [{"type": "text", "text": "abc ", "marks": ["em"]},
     {"type": "text", "text": "def", "marks": ["em", "strong"]}]
    ```

    "*abc **def***" 被拆分成两段表示, "*abc&nbsp;*" (em) 和 "***def***" (em, strong). 这是传统富文本 (如 Word, Google Doc) 与标记语言 (如 HTML, Markdown) 在数据表示方法上的区别, 它们的**行内样式不支持真正意义上的嵌套**.

2. **HTML 标签的渲染顺序**:

    富文本 "_**abc**_" (em, strong) 有如下两种渲染方式:

    ```html
    (1) <em><strong>abc</strong></em>
    (2) <strong><em>abc</em></strong>
    ```

    在 ProseMirror 中, Mark 的顺序越靠后, 渲染时标签就越靠内层. 在这个例子中, Schema 中 `strong` 在 `em` 之后定义, 因此 `<strong>` 标签将嵌套在 `<em>` 标签内, ProseMirror 的渲染结果是 (1).

3. **Mark 的合并**:

    富文本 "_abc **def**_" 有如下两种渲染方式.

    ```html
    (1) <em>abc <strong>def</strong></em>
    (2) <em>abc </em><em><strong>def</strong></em>
    ```

    ProseMirror 会尽可能地合并相邻的标签, 因此渲染结果是 (1). 这里必须要注意, 假设我们调换 `strong` 和 `em` 的顺序, 那么渲染结果将变成:

    ```html
    (3) <em>abc </em><strong><em>def</em></strong>
    ```

    在这种情况下, `<em>` 标签被包裹在 `<strong>` 内, 不再和另一个 `<em>` 毗邻, ProseMirror **不会**尝试调换顺序来合并它们.

## 设计 Mark 的顺序

对于简单的文字样式 (如斜体, 加粗, 字体颜色等) 来说, Mark 的顺序并不重要, 可以看到, 上一节提到的所有渲染方式都会得到完全相同的视觉效果. 但如果文字样式拥有背景, 边距, 边框, 鼠标悬停效果时, Mark 的顺序就变得至关重要了.

例如, 假设你在定义 Schema 时, 将边框置于行内代码之后, 你就可能得到一个奇丑无比的效果 -- "<span style="border: 2px solid red">abc </span><code style="background-color: gray"><span style="border: 2px solid red">def</span></code><span style="border: 2px solid red"> ghi</span>", 因为边框被嵌套在了行内代码内, 也许你期望的效果是 "<span style="border: 2px solid red">abc <code style="background-color: gray">def</code> ghi</span>".

一般来说, 表示简单文字样式的 Mark 应该放在最后 (而且它们之间的顺序并不重要), 而具有背景, 边框等效果的 Mark 则应该放在靠前的位置, 并根据实际用途决定它们之间的顺序, 尽量减少不合理的嵌套样式出现的概率.

链接的位置是另一个需要慎重的问题, 你有两种选择:

1. 将链接置于较后的位置, 只前于简单的文字样式.

    <span style="border: 2px solid red">abc <a style="color: blue; text-decoration: underline">def</a> ghi</span>

    这种方式的缺点在于, 当链接和其他样式同时出现时, 由于链接具有较低优先级, 被包裹于其他样式内部, 因此会被截断成多节, 对光标悬停效果非常不友好. 例如 "<a style="color: blue; text-decoration: underline">abc</a> <span style="border: 2px solid red"><a style="color: blue; text-decoration: underline">def</a></span> <a style="color: blue; text-decoration: underline">ghi</a>", 实际上是三个链接.

    尽管有这样的缺陷, 但很多富文本编辑器 (如 Notion) 都采取了这种方案. 因为下述的方案缺陷更严重, 而且大多数用户也没有在链接内嵌套代码等样式的需求.

2. 将链接置于较前的位置.

    <a style="color: blue; text-decoration: underline">abc <span style="border: 2px solid red">def</span> ghi</a>

    这种方案的缺点在于, 由于链接具有较高的优先级, 它可能经常会包裹其他样式. 如果其他样式有边框或圆角, 会被链接包裹而断成多节, 使得视觉效果不佳, 例如 "<span style="border: 2px solid red">abc </span><a style="color: blue; text-decoration: underline"><span style="border: 2px solid red">def</span></a><span style="border: 2px solid red"> ghi</span>".

这两种方案都有各自的合理性, 你应该根据实际需求进行选择.
