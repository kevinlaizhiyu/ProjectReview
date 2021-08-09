## 模板引用

尽管存在 `prop` 和事件，但有时你可能仍然需要直接访问 `JavaScript` 中的子组件。为此，可以使用 `ref` attribute 为子组件或 `HTML` 元素指定引用 `ID`。例如：

```html
<input ref="input" />
```

例如，你希望在组件挂载时，以编程的方式 `focus` 到这个 `input` 上，这可能有用

```js
const app = Vue.createApp({})

app.component('base-input', {
  template: `
    <input ref="input" />
  `,
  methods: {
    focusInput() {
      this.$refs.input.focus()
    }
  },
  mounted() {
    this.focusInput()
  }
})
```
此外，还可以向组件本身添加另一个 ref，并使用它从父组件触发 `focusInput` 事件：

```html
<base-input ref="usernameInput"></base-input>
```

```js
this.$refs.usernameInput.focusInput()
```
>! WARNING 
>` $refs `只会在组件渲染完成之后生效。这仅作为一个用于直接操作子元素的“逃生舱”——你应该避免在模板或计算属性中访问 `$refs`。

参考：[在组合式 API 中使用 template refs](https://v3.cn.vuejs.org/guide/composition-api-template-refs.html#%E6%A8%A1%E6%9D%BF%E5%BC%95%E7%94%A8)