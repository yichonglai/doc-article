# SleepyFn

## 📖 Implementation

```js
function sleepyFn () {
  this.count = 0;
  this.sleep = function(delay) {
    console.log('sleep!');
    var deadline = new Date().getTime() + parseInt(delay, 10);
    while (new Date().getTime() < deadline) {}
    return this;
  }
}
sleepyFn.prototype.countUp = function() {
  console.log(++ this.count);
  return this;
}
```
