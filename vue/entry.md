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

```
.
├── index.js
├── config.js
├── components
│   ├── index.js
│   └── keep-alive.js
├── global-api
│   ├── assets.js
│   ├── extend.js
│   ├── index.js
│   ├── mixin.js
│   └── use.js
├── instance
│   ├── events.js
│   ├── index.js
│   ├── init.js
│   ├── inject.js
│   ├── lifecycle.js
│   ├── proxy.js
│   ├── render-helpers
│   │   ├── bind-dynamic-keys.js
│   │   ├── bind-object-listeners.js
│   │   ├── bind-object-props.js
│   │   ├── check-keycodes.js
│   │   ├── index.js
│   │   ├── render-list.js
│   │   ├── render-slot.js
│   │   ├── render-static.js
│   │   ├── resolve-filter.js
│   │   ├── resolve-scoped-slots.js
│   │   └── resolve-slots.js
│   ├── render.js
│   └── state.js
├── observer
│   ├── array.js
│   ├── dep.js
│   ├── index.js
│   ├── scheduler.js
│   ├── traverse.js
│   └── watcher.js
├── util
│   ├── debug.js
│   ├── env.js
│   ├── error.js
│   ├── index.js
│   ├── lang.js
│   ├── next-tick.js
│   ├── options.js
│   ├── perf.js
│   └── props.js
└── vdom
    ├── create-component.js
    ├── create-element.js
    ├── create-functional-component.js
    ├── helpers
    │   ├── extract-props.js
    │   ├── get-first-component-child.js
    │   ├── index.js
    │   ├── is-async-placeholder.js
    │   ├── merge-hook.js
    │   ├── normalize-children.js
    │   ├── normalize-scoped-slots.js
    │   ├── resolve-async-component.js
    │   └── update-listeners.js
    ├── modules
    │   ├── directives.js
    │   ├── index.js
    │   └── ref.js
    ├── patch.js
    └── vnode.js
```

**index.js**

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

* stateMixin

* eventsMixin

* lifecycleMixin

* renderMixin

**initMixin：**