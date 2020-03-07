# `Promise` eg:

> Always remember, inside of then()‘s callback, the promise you are responding to has already resolved. The result of your callback will have no influence on this promise

## 📖 Implementation

```js
function Promise(fn) {
  var state = 'pending';
  var value;
  var deferred = null;

  function resolve(newValue) {
    // catch internal unhandled exceptions and reject
    // It’s possible an unhandled exception will happen, completely ruining everything.
    try {
      // in case of newValue is a promise object
      // it allows different promise implementations to interopt with each other
      if (newValue && typeof newValue.then === 'function') {
        newValue.then(resolve, reject);
        return;
      }
      value = newValue;
      state = 'resolved';

      if (deferred) {
        handle(deferred);
      }
    } catch (e) {
      reject(e);
    }
  }
  function reject(reason) {
    state = 'rejected';
    value = reason;

    if (deferred) {
      handle(deferred);
    }
  }

  function handle(handler) {
    if (state === 'pending') {
      deferred = handler;
      return;
    }
    // Promises/A+ spec requires that promise resolution happen asynchronously.
    // real promise libraries tend to use process.nextTick（NodeJS），for browsers maybe an asynchronous library 
    setTimeout(function() {
      var handlerCallback;

      if (state === 'resolved') {
        handlerCallback = handler.onResolved;
      } else {
        handlerCallback = handler.onRejected;
      }

      if (!handlerCallback) {
        if (state === 'resolved') {
          handler.resolve(value);
        } else {
          handler.reject(value);
        }
        return;
      }

      // make sure the callbacks given to us by the caller don’t throw unhandled exceptions
      var ret;
      try {
        ret = handlerCallback(value);
      } catch (e) {
        handler.reject(e);
        return;
      }
      handler.resolve(ret);
    }, 1);
  }

  // always return a promise object and is wasteful
  this.then = function (onResolved, onRejected) {
    // a new promise
    return new Promise(function (resolve, reject) {
      handle({
        onResolved: onResolved,
        onRejected: onRejected,
        resolve: resolve,
        reject: reject
      });
    });
  };

  fn(resolve, reject);
}
```

## 🔗 Links

- [Promises/A+ spec](https://promisesaplus.com/)
- [Promisejs.org](https://www.promisejs.org/)
- [Reference](http://www.mattgreer.org/articles/promises-in-wicked-detail/)
