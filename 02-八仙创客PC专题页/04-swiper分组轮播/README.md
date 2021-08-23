# 
# 1.swiper 组件应用必须项

* 组件的引用和注册
* `swiper.css` 的引入
* `swiperOption` 设置

```js
import { Swiper, SwiperSlide } from 'vue-awesome-swiper';
import 'swiper/css/swiper.css';
export default {
    data(){
        return {
            swiperOptions: {
                direction: 'horizontal',
                slidesPerView: 'auto',
                loopedSlides: 7,
                spaceBetween: 62,
                loop: true,
                navigation: {
                    nextEl: '.swiper-button-next',
                    prevEl: '.swiper-button-prev',
                },
                speed: 300,
                autoplay: {
                    delay: 3000,
                    stopOnLastSlide: false,
                    disableOnInteraction: false,
                },
                pagination: {
                    el: '.intro-pagination',
                },
                preventLinksPropagation: false,
                on: {
                    click: (v) => {
                        switch (v.target.className) {
                            case 'slide_info_button':
                                console.log(123);
                                vm.maekerIntroChance();
                                break;
                            default:
                                break;
                        }
                    },
                },
            },
        }
    }
}
```

# 2. slide 分组轮播

分组轮播主要还是要在 swiperOption 中实现

* `slidesPerView: 'auto',` 页面能够同时看到的每一组的slide的个数不做限制，自动适应；

* `loopedSlides: 7,` 循环的slide 的个数

* `spaceBetween: 62,` 间距，单位px
  
* `loop: true,` 单向无线循环

# 3. swiper 前后按钮

* 在配置项中 添加 按钮 class 名称
```js
export default {
    data(){
        return {
            swiperOptions:{
                navigation: {
                    nextEl: '.swiper-button-next',
                    prevEl: '.swiper-button-prev',
                },
            }
        }
    }
}
```
* 在 html 部分添加相应 class 的 dom 元素
```html
<div class="swiper-button-next"></div>
<div class="swiper-button-prev"></div>
```

* 根据设计稿修改相应的 css 样式
```less
.swiper-button-next,
.swiper-button-prev {
  position: absolute;
  width: 100px;
  height: 100px;
  z-index: 4;
  top: 50%;
  transform: translateY(-50%);
  right: 30px;
  left: auto;
  &::after {
    color: #fff;
  }
}
.swiper-button-prev {
  left: 30px;
  right: auto;
}
```
# 4. swiper 分页器自定义

* 在配置项中 添加 按钮 class 名称
```js
export default {
    data(){
        return {
            swiperOptions:{
                pagination: {
                    el: '.intro-pagination',
                },
            }
        }
    }
}
```
* 在 html 部分添加相应 class 的 dom 元素
```html
 <div class="intro-pagination"></div>
```

* 根据设计稿修改相应的 css 样式
```less
.intro-pagination {
  width: 200px;
  height: 40px;
  position: absolute;
  bottom: -50px;
  left: 50%;
  transform: translateX(-50%);
  display: flex;
  justify-content: center;
  align-items: center;
  /deep/.swiper-pagination-bullet {
    margin: 0 5px;
    width: 10px;
    height: 10px;
    background: rgba(255, 255, 255, 0.4);
    transition: background 0.2s linear 0s;
  }
  /deep/.swiper-pagination-bullet-active {
    background: #ff6900;
  }
}
```


# 5. swiper 内部点击甄别

`swiper` 内置了许多的事件，点击，拖动，鼠标移入移出等等，有时候点击某一个元素，由于冒泡或者捕获的出现会导致点击事件没有触发，或者误触，这种情况，就要甄别点击的元素；

不仅如此，在无线循环的时候，`swiper` 会复制首尾部分的 `slide` ，但是不会复制绑定在元素上的事件，这个时候要应用点击事件，就必须使用 `swiper` 内部的点击事件，并且甄别点击的元素，确保动作的顺利的触发。

```js
export default {
    data(){
        return {
            swiperOptions: {
                on: {
                    click: (v) => {
                        switch (v.target.className) {
                            case 'slide_info_button':
                                vm.maekerIntroChance(); //一旦匹配，就执行该方法
                                break;
                            default:
                                break;
                        }
                    },
                },
            },
        }
    }
}
```
