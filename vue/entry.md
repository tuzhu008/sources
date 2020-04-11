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

* `initMixin()`

  为 Vue 增加原型方法 `_init`

* `stateMixin()`

  为 Vue 增加原型属性 `$data`、`$props`

  为 Vue 增加原型方法 `$set`、`$delete`、`$watch`

* `eventsMixin()`

  为 Vue 增加原型方法 `$on`、`$once`、`$off`、`$delete`、`$emit`

* `lifecycleMixin()`

  为 Vue 增加原型方法 `_update`、`$forceUpdate`、`$destroy`

* `renderMixin()`

  为 Vue 增加原型方法 `$nextTick`、`_render`


从 Vue 的构造函数来看，`initMixin()` 方法新增的 `_init` 方法是 Vue 初始化的入口。


```js
Vue.prototype._init = function (options?: Object) {
  const vm: Component = this
  // a uid
  vm._uid = uid++

  // 用来避免被观察的标志
  vm._isVue = true
  // 合并选项
  if (options && options._isComponent) {
    // 优化内部组件实例化，因为动态选项合并非常慢，而且没有一个内部组件选项需要特殊处理。
    initInternalComponent(vm, options)
  } else {
    vm.$options = mergeOptions(
      resolveConstructorOptions(vm.constructor),
      options || {},
      vm
    )
  }
  /* istanbul ignore else */
  if (process.env.NODE_ENV !== 'production') {
    // ...
  } else {
    vm._renderProxy = vm
  }
  // expose real self
  vm._self = vm
  initLifecycle(vm)
  initEvents(vm)
  initRender(vm)
  callHook(vm, 'beforeCreate')
  initInjections(vm) // resolve injections before data/props
  initState(vm)
  initProvide(vm) // resolve provide after data/props
  callHook(vm, 'created')

  if (vm.$options.el) {
    vm.$mount(vm.$options.el)
  }
}
```

`_init` 方法对实例进行了初始化。首先它对实例的配置进行处理，并将其作为实例的 `$options` 属性。`_init` 之后的操作都依赖于此配置。


* `initLifecycle()`

  初始化实例的生命周期状态参数和一些实例属性

* `initEvents()`

  初始化实例的事件

* `initRender()`

  初始化实例的渲染器

* `callHook()`

* `initInjections()`

* `initState()`

* `initProvide()`

* `callHook()`