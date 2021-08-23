# 
# 1.线段增长动画实现
实现一个线段和点的增长主要是依靠动画来实现
* 目标元素的长度为零
* 目标元素添加动画属性，对应的动画设置延时，执行次数为1次，100% 时的长度为最后变化的长度
> 这里需要注意的是，动画结束后，元素的状态会保持，如果初始化的时候就给一个长度，那就就会出现闪跳，故而初始动画不能有长度
* `wow.js` 的主要作用是用户滑动到页面某个部分的时候开始启用某个动画
* 在 `class` 中添加了 `wow` 属性之后，`class` 中的动画也会被 `wow.js` 控制
* 在 `class` 中也可以添加` animate.css `中的动画 ，延迟的话需要通过 ` data-wow-delay="0.8s"` 来实现


## 1.1 wow.js安装引入

[wow.js 参考文档](https://wowjs.uk/docs.html)

`"wowjs": "^1.1.3"`
```js
if (process.browser) {
    // 在这里根据环境引入wow.js
    var { WOW } = require('wowjs');
}
export default {
    mounted() {
        if (process.browser) {
            // 在页面mounted生命周期里面 根据环境实例化WOW
            new WOW({
                live: false,
                offset: 50, 
            }).init();
        }
    },
}

```
## 1.2 animate.css 配合使用
```html
 <div class="touble_main_title wow fadeInUp">您是否也在面临这些烦恼</div>
```

## 1.3 自定义增长动画
```html
<div
  :class="['line_down wow', ele.effects[2]]"
  :data-wow-delay="ele.lineTime[1]"
></div>

```
```js
export default {
    data(){
        return {
            troubleInfo: [{
                title: '想创业又缺少启动资金',
                className: 'left_1st',
                lineTime: ['0s', '0.2s', '0.4s'],
                effects: ['fadeInRight', 'showDot', 'showLineDown', 'showLineBase'],
            }]
        }
    }
}
```
```css
.line_down {
    position: absolute;
    width: 0px;
    max-width: 0px;
    content: '';
    height: 1px;
    border: none;
    bottom: 20px;
    right: 26px;
    transform: rotateZ(42deg);
    transform-origin: right top;
    background: #ff6900;
    animation: showLineDown 0.3s ease 0.9s 1 forwards;
}
@keyframes showLineDown {
  from {
    width: 0px;
    max-width: 0px;
  }
  to {
    width: 32px;
    max-width: 32px;
  }
}
```

# 2.弧度透明阴影的实现

## 2.1 源码
```less
.maker_intro_products {
    width: 100%;
    height: 828px;
    background: rgba(0, 0, 0, 0.2);
    position: relative;
    & ::before {
      position: absolute;
      display: block;
      content: '';
      left: 0;
      top: 0;
      width: 357px;
      height: 828px;
      background: radial-gradient(ellipse 60% 63% at right, transparent 73%, black 166%);
      z-index: 3;
    }
    & ::after {
      position: absolute;
      display: block;
      content: '';
      right: 0;
      top: 0;
      width: 357px;
      height: 828px;
      background: radial-gradient(ellipse 60% 63% at left, transparent 73%, black 166%);
      z-index: 3;
    }
}
```
## 2.2 属性解析

[radial-gradient 属性参考文章](https://www.w3cplus.com/css3/new-css3-radial-gradient.html)

W3C标准径向渐变语法

```text
radial-gradient([[<shape> || <size>] [at <position>]?,| at <position>,]?<color-stop>[,<color-stop>]+);	
```

* `shape`
  主要用来定义径向渐变的形状。其主要包括两个值“`circle`圆形”和“`ellipse`椭圆形”：
 
* `size`
  主要用来确定径向渐变的结束形状大小,默认值为“`farthest-corner`”

  * `closest-side`：指定径向渐变的半径长度为从圆心到离圆心最近的边；
  * `closest-corner`：指定径向渐变的半径长度为从圆心到离圆心最近的角；
  * `farthest-side`：指定径向渐变的半径长度为从圆心到离圆心最远的边；
  * `farthest-corner`：指定径向渐变的半径长度为从圆心到离圆心最远的角；
 
* `position`

  * `<length>`：用长度值指定径向渐变圆心的横坐标或纵坐标。可以为负值。
  * `<percentage>`：用百分比指定径向渐变圆心的横坐标或纵坐标。可以为负值。
  * `left`：设置左边为径向渐变圆心的横坐标值。
  * `center`：设置中间为径向渐变圆心的横坐标值或纵坐标。
  * `right`：设置右边为径向渐变圆心的横坐标值。
  * `top`：设置顶部为径向渐变圆心的纵标值。
  * `bottom`：设置底部为径向渐变圆心的纵标值。

* `color-stop`

  `transparent 73%`
