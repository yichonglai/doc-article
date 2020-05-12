# 设计模式

## 单例模式

> 定义：保证一个类仅有一个实例，并提供一个访问它的全局访问点。

```js
function Singleton() {
  var instance;
  return instance || instance = new Singleton();
}
// 有传统的单例模式实现，因为语言的差异性，有更适合的方法在 JavaScript 中创建单例。
function singleton(fn) {
  var result;
  return function(){
    return result || (result = fn.apply(this, arguments));
  }
}
// 如：render 函数和 bindEvent 函数都分别执行了 3 次，但 div 实际上只被绑定了一个 事件。
var bindEvent = singleton(function() { 
  document.getElementById( 'div1' ).onclick = function() {
    alert ( 'click' ); 
  }
  return true; 
});
var render = function(){ 
  console.log('开始渲染列表'); 
  bindEvent();
};
render(); 
render(); 
render();
```

## 策略模式


## 代理模式


## 发布—订阅模式


## 装饰者模式
