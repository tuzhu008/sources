# nextTick

`nextTick` 方法使用了微任务进行包装，利用了原生的 `Promise.then` 和 `MutationObserver`。

`MutationObserver` 有更广泛的支持，但是在 iOS &gt;= 9.3.3 的 UIWebView 中，当在触摸事件处理程序中触发时，会出现严重的 bug。触发几次之后，它就完全停止工作了。因此 `nextTick` 会优先使用 `Promise`。

兼容方案：

* Promise

* MutationObserver

* setImmediate

* setTimeout

```js
/* @flow */
/* globals MutationObserver */

import { noop } from 'shared/util'
import { handleError } from './error'
import { isIE, isIOS, isNative } from './env'

export let isUsingMicroTask = false

const callbacks = []
let pending = false

function flushCallbacks () {
  pending = false
  const copies = callbacks.slice(0)
  callbacks.length = 0
  for (let i = 0; i < copies.length; i++) {
    copies[i]()
  }
}

let timerFunc

if (typeof Promise !== 'undefined' && isNative(Promise)) {
  const p = Promise.resolve()
  timerFunc = () => {
    p.then(flushCallbacks)

    // 在有问题的 uiwebview 中， Promise.then 并没有完全失效，
    // 但它会陷入一种奇怪的状态，回调被推入微任务队列，但队列没有被刷新，
    // 直到浏览器需要做一些其他的工作，例如处理一个计时器。
    // 因此，我们可以通过添加一个空计时器来“强制”刷新微任务队列。
    if (isIOS) setTimeout(noop)
  }
  isUsingMicroTask = true
} else if (!isIE && typeof MutationObserver !== 'undefined' && (
  isNative(MutationObserver) ||
  // PhantomJS and iOS 7.x
  MutationObserver.toString() === '[object MutationObserverConstructor]'
)) {
  // 在原生 Promise 不可用的地方使用 MutationObserver
  // 例如，PhantomJS, iOS7, Android 4.4
  // (#6466 MutationObserver 在 IE11 中不可靠)
  let counter = 1
  const observer = new MutationObserver(flushCallbacks)
  const textNode = document.createTextNode(String(counter))
  observer.observe(textNode, {
    characterData: true
  })
  timerFunc = () => {
    counter = (counter + 1) % 2
    textNode.data = String(counter)
  }
  isUsingMicroTask = true
} else if (typeof setImmediate !== 'undefined' && isNative(setImmediate)) {
  // 回退到 setImmediate。从技术上讲，它利用了(宏)任务队列，但它仍然是比 setTimeout 更好的选择。
  timerFunc = () => {
    setImmediate(flushCallbacks)
  }
} else {
  // 回退到 setTimeout.
  timerFunc = () => {
    setTimeout(flushCallbacks, 0)
  }
}

export function nextTick (cb?: Function, ctx?: Object) {
  let _resolve
  callbacks.push(() => {
    if (cb) {
      try {
        cb.call(ctx)
      } catch (e) {
        handleError(e, ctx, 'nextTick')
      }
    } else if (_resolve) {
      _resolve(ctx)
    }
  })
  if (!pending) {
    pending = true
    timerFunc()
  }
  // $flow-disable-line
  if (!cb && typeof Promise !== 'undefined') {
    return new Promise(resolve => {
      _resolve = resolve
    })
  }
}
```



![](/assets/nextTick.png)

