# Vue

Vue 源码解读

## 目录结构

```
.
├── compiler
├── core
├── platforms
├── server
├── sfc
└── shared
```

## Core

```js
import { initMixin } from './init'
import { stateMixin } from './state'
import { renderMixin } from './render'
import { eventsMixin } from './events'
import { lifecycleMixin } from './lifecycle'

function Vue (options) {
  this._init(options)
}

initMixin(Vue)
stateMixin(Vue)
eventsMixin(Vue)
lifecycleMixin(Vue)
renderMixin(Vue)

export default Vue
```

`index.js` 作为入口只定义了 Vue 的构造函数，它的其他能力由其他的方法来进行扩展：

* initMixin

  为 Vue 增加原型方法 `_init`

* stateMixin

  为 Vue 增加原型方法 `$set`、`$delete`、`$delete`、`$delete`、`$delete`、`$delete`、

* eventsMixin

* lifecycleMixin

* renderMixin