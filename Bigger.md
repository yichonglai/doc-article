# Bigger

## 大批量Dom操作

### 1、一次性插入

> 减少dom操作，分片插入requestAnimationFrame

#### 📖

```js
(() => {
  const wrapper = document.getElementById('listWrapperId');
  if (!wrapper) return;

  const total = 66666;
  const batchSize = 4;
  const batchCount = Math.ceil(total / batchSize);
  let batchComplete = 0;

  const appendItems = () => {
    const fragment = document.createDocumentFragment();
    for (let i = 0; i < batchSize; i ++) {
      const node = document.createElement('tagName');
      node.innerText = batchComplete * batchSize + i + 1;
      fragment.appendChild(node);
    }

    wrapper.appendChild(fragment);

    batchComplete += 1;
    doBatchAppend();
  }

  function doBatchAppend() {
    if (batchComplete < batchCount) {
      window.requestAnimationFrame(appendItems);
    }
  }

  // 执行
  doBatchAppend();

  // 事件代理
  wrapper.addEventListener('click', (e) => {
      // do what you want
  });
})();
```

### 2、虚拟列表，监听滚动事件按需替换渲染

```js
render () {
  const { visibleList } = this.state;
  
  return (
    <div className='wrapper' onScroll={this.onScrollList}>
      <div className='virtualList' style={{ height: this.listHeight + 'px' }} />
      <div className='list' style={{ top: this.offset + 'px' }}>
      {
        visibleList.map((item) => <div className='item'>{item}</div>)
      }
      </div>
    </div>
  )
}
```

## 大量数据计算处理

> 1. 后台处理直接返回  
> 2. 前端处理worker

### Worker

> 运行者 Worker 接口是Web Workers API 的一部分，代表一个后台任务，它容易被创建并向创建者发回消息[MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/Worker)。

#### 参数

- URL：必须遵守同源策略

#### 事件

- Worker.onerror
- Worker.onmessage：postMessage()发送过来的数据在Event.data属性中
- Worker.onmessageerror

#### 方法

- Worker.postMessage()：向 Worker 线程发送消息
- Worker.terminate()：立即终止 Worker 线程

#### 示例

```js
const worker = new Worker('worker.js');
worker.postMessage(40);
worker.onmessage = e => {
  const data=e.data;
  console.log(data);
};
worker.onerror = e => {
  console.log(e.message);
};
// worker.js
function fibonacci(n) {
  return n < 2 ? n : fibonacci(n - 1) + fibonacci(n - 2);
}
this.onmessage = function(e) {
  this.postMessage(fibonacci(e.data));
};
```
