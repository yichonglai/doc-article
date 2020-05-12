# 大“数据”处理方案

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
  doBatchAppenddoBatchAppend();

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

> 1. 后台处理  
> 2. 前端处理worker
