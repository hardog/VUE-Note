
- **VUE**代表源码根目录详见: [https://github.com/vuejs/vue](https://github.com/vuejs/vue)
- nextTick源码文件位置: [VUE/src/core/util/next-tick.js](https://github.com/vuejs/vue/blob/dev/src/core/util/next-tick.js)
- 实例方法挂载位置: [VUE/src/core/global-api/index.js](https://github.com/vuejs/vue/blob/dev/src/core/global-api/index.js)
- 全局方法挂载位置: [VUE/src/core/instance/render.js](https://github.com/vuejs/vue/blob/dev/src/core/instance/render.js)


### 两个核心概念`MicroTask`&&`MacroTask`

- 在VUE < 2.4的版本中，基本都是使用MicroTask来实现nextTick的功能。`MicroTask`主要通过Promise来实现，如果浏览器不支持Promise则会回退到使用`MacroTask`实现。
- `MacroTask`主要通过`setImmediate`、`MessageChannel`、`setTimeout`实现，优先推荐使用setImmediate实现，但是该函数仅在IE浏览器可用。
- `MicroTask`往往具有更高的优先级

### 仅使用`MicroTask`或者`MacroTask`会有什么问题?

如果只使用`MicroTask`，那么在一系列按顺序执行的事件或者冒泡事件中可能触发task，造成不可预期的行为。如果只使用`MacroTask`那么当UI即将重绘时状态改变了，可能造成UI无法及时更新。

### 在`UIWebViews`中使用Promise可能存在什么问题?

`UIWebView`中，当task被推入队列中时，可能该task并不会立即执行，而是需要等待浏览器下一次有其他任务需要操作时才会去执行。此时可以通过人为执行一次`setTimeout(() => {},0)`来强制浏览器执行一次任务。

### setTimeout, setImmediate, MessageChannel, Promise

- `MicroTask`: process.nextTick, Promise, Object.observe
- `MacroTask`: setTimeout, setImmediate, setInterval, IO


### vm.$nextTick 与 Vue.nextTick 区别，即全局与局部的区别?

Vue维护一个全局task callbacks数组，保存所有nextTick回调。在Vue实例中及全局情况下调用的都是相同的函数。在task任务执行时，会将全部的callbacks回调执行完，然后数组重置。