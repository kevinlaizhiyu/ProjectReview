## 生命周期钩子

你可以通过在生命周期钩子前面加上` “on” ` 来访问组件的生命周期钩子。

下表包含如何在 `setup ()` 内部调用生命周期钩子：

<table><thead><tr><th>选项式 API</th> <th>Hook inside <code>setup</code></th></tr></thead> <tbody><tr><td><code>beforeCreate</code></td> <td>Not needed*</td></tr> <tr><td><code>created</code></td> <td>Not needed*</td></tr> <tr><td><code>beforeMount</code></td> <td><code>onBeforeMount</code></td></tr> <tr><td><code>mounted</code></td> <td><code>onMounted</code></td></tr> <tr><td><code>beforeUpdate</code></td> <td><code>onBeforeUpdate</code></td></tr> <tr><td><code>updated</code></td> <td><code>onUpdated</code></td></tr> <tr><td><code>beforeUnmount</code></td> <td><code>onBeforeUnmount</code></td></tr> <tr><td><code>unmounted</code></td> <td><code>onUnmounted</code></td></tr> <tr><td><code>errorCaptured</code></td> <td><code>onErrorCaptured</code></td></tr> <tr><td><code>renderTracked</code></td> <td><code>onRenderTracked</code></td></tr> <tr><td><code>renderTriggered</code></td> <td><code>onRenderTriggered</code></td></tr> <tr><td><code>activated</code></td> <td><code>onActivated</code></td></tr> <tr><td><code>deactivated</code></td> <td><code>onDeactivated</code></td></tr></tbody></table>

>TIP
> 因为 `setup` 是围绕 `beforeCreate` 和 `created` 生命周期钩子运行的，所以不需要显式地定义它们。换句话说，在这些钩子中编写的任何代码都应该直接在 `setup` 函数中编写。

这些函数接受一个回调函数，当钩子被组件调用时将会被执行:

```js
// MyBook.vue
export default {
    setup() {
        // mounted
        onMounted(() => {
            console.log('Component is mounted!')
        })
    }
}
```





