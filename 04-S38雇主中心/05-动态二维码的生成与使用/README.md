# 

# 1. 实现原理

* 使用 `qrcode` 进行实现，不过这个是 需要 `jq` 的库的支持
* `vue-qr` 是社区针对 `vue` 编写的比较全面的插件

> npm i vue-qr --save 
```js
import vueQr from 'vue-qr'
```

# 2. html

```html
<vue-qr :logoSrc="logoUrl" :text="imgLink" :size="120" class="wx-qrscanner"></vue-qr>
```
* `logoSrc` 二维码中心的链接
* `text` 需要转换的 链接地址
* `size` 生成的正方形的二维码的边长

# 3. css

```css
.wx-qrscanner{
  position: absolute;
  top: 50%;
  left:50%;
  transform: translate(-50%,-50%);
}
```
# 4. js

```js
export default {
    props:['title','itemData','status','companyName','lifeCircle','imgLink'],
    data(){
        return {
            logoUrl: require("../img/wx_scanner_logo.png")
        }
    }
}

```
