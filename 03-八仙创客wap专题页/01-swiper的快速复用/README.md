#

# 1. swiper 组成

* 引入组件 swiper swiper-slide
* 引入 swiper.css
* dom 挂载
* swiperOption 配置
* css 样式调整  
* pagination 配置 挂载 样式调整
* 掉帧问题处理
* 点击事件处理

# 1.1 引入组件 swiper swiper-slide
```js
import { Swiper, SwiperSlide } from 'vue-awesome-swiper';
import 'swiper/dist/css/swiper.css';
export default {
    components: {
        Swiper,
        SwiperSlide,
    },
}
```

# 1.2 dom 挂载

```html
 <Swiper ref="mySwiper" class="maker_banner_swiper" :options="swiperOptions">
  <swiper-slide
    v-for="(ele, idx) in topBannerInfos"
    :key="idx + 'maker_banner_slide'"
    class="maker_banner_slide"
  >
    <a href='ele.link||"javascript:void(0)"' :target="ele.link ? '_blank' : ''">
      <img :src="ele.picUrl || ''" alt="八仙创客" class="top_slide_bg" />
    </a>
  </swiper-slide>
</Swiper>
```

# 1.3 swiperOption 配置

```js
export default {
    data() {
        return {
            swiperOptions: {
                loop: true,
                direction: 'horizontal',
                effect: 'slide',
                speed: 800,
                spaceBetween: 0,
                allowTouchMove: true,
                autoplay: {
                    delay: 5000,
                    disableOnInteraction: false //用户交互之后是否禁止，false不禁止
                    // pauseOnMouseEnter:true, //鼠标移入之后是否展厅，true暂停
                    // waitForTransition: true, // 过度结束后才开始计时 true
                },
                pagination: {
                    el: '.swiper-pagination',
                    bulletClass: 'my-butllet',
                    bulletActiveClass: 'my-butllet-acitve'
                }
            }
        };
    },
}
```

# 1.4 css 样式调整

```less
  .maker_banner_swiper {
    width: 100%;
    height: 100%;
    transform: translate3d(0, 0, 0);
    overflow: hidden;
    .swiper-wrapper {
      width: 100%;
      height: 100%;
      .maker_banner_slide {
        width: 100%;
        height: 100%;
        .top_slide_bg {
          width: 100%;
          height: 100%;
        }
      }
    }
  }
```

#  1.5 pagination


```js
export default {
    data() {
        return {
            swiperOptions: {
                pagination: {
                    el: '.swiper-pagination',
                    bulletClass: 'my-butllet',
                    bulletActiveClass: 'my-butllet-acitve'
                }
            }
        };
    },
}
```
【注意】 这里的 `slot="pagination"` 很重要不要丢掉
```html
<div
  v-show="topBannerInfos.length > 1"
  class="swiper-pagination"
  slot="pagination"
></div>
```

```less
  .swiper-pagination {
    position: absolute;
    bottom: 34px;
    width: 100%;
    height: 7px;
    z-index: 1;
    /deep/.my-butllet {
      background: rgba(255, 255, 255, 0.3);
      margin: 0 4px;
      display: inline-block;
      width: 7px;
      height: 7px;
      border-radius: 7px;
    }
    /deep/.my-butllet-acitve {
      background: #fff !important;
    }
  }
```

# 1.6 掉帧问题处理

```less
 .maker_banner_swiper {
    transform: translate3d(0, 0, 0);
    overflow: hidden;
    .swiper-wrapper {
        width: 100%;
        height: 100%;
    }
}
```

# 1.7 点击事件处理

```js
export default {
    data(){
        return {
            swiperOptions:{
                on: {
                    click: (v) => {
                        switch (v.target.className) {
                            case 'slide_info_button':
                                vm.makerProductChance(); //目标执行函数
                                break;
                            default:
                                break;
                        }
                    }
                }
            }
        }
    }
}
```

# 2. 多页切换的swiper

大体上和单页切换的swiper 相同，不同的是

* slide 的宽度更小；

* swiperOptions的配置不同

```js
export default {
    data(){
        return {
            swiperOptions:{
                direction: 'horizontal',
                    slidesPerView: 'auto', //*
                    loopedSlides: 3, //*
                    spaceBetween: 17, //*
                    loop: true,//*
                    effect: 'slide',
                    speed: 300,//*
                    allowTouchMove: true,
                    autoplay: {
                    delay: 5000,
                        disableOnInteraction: false //用户交互之后是否禁止，false不禁止
                    // pauseOnMouseEnter:true, //鼠标移入之后是否展厅，true暂停
                    // waitForTransition: true, // 过度结束后才开始计时 true
                },
                pagination: {
                    el: '.swiper_product_pagination',
                        bulletClass: 'product_bullet',
                        bulletActiveClass: 'product_bullet_active'
                },
                on: {
                    click: (v) => {
                        switch (v.target.className) {
                            case 'slide_info_button':
                                vm.makerProductChance();
                                break;
                            default:
                                break;
                        }
                    }
                }
            }
        }
    }
}
```
